# Улучшение производительности vue приложения

*19 Feb 2021*

- https://github.com/Kasheftin/vue-rerendering-optimization/blob/master/article.txt
- https://kasheftin.github.io/vue-rerendering-optimization/#/
- https://teamhood.com/engineering/vue-js-component-props/

У нас в TeamHood есть wiki. 
Там собралась коллекция рекоммендаций, в том числе, по улучшению производительности тяжелого фронтенда на vue.js.
Улучшать производительность понадобилось, потому что в силу специфики наши основные экраны не имеют пагинации.
Есть клиенты, у которых на одной kanban/gantt-доске больше тысячи вот таких вот карточек, все это должно работать без лагов.

[img]
- 2 rows
- development board
- subtasks + in progress, for pairtest
- resort rows;
- move task with 3 children to parent-child column
- assign first child 
- resort children
- trigger submenu, assign a tag
- mark third child completed

В статье разобрано несколько редко упоминаемых техник из нашей wiki, которые помогут сократить излишний рендеринг компонентов и улучшить производительность.

- https://kasheftin.github.io/vue-rerendering-optimization/
- https://github.com/Kasheftin/vue-rerendering-optimization

Все примеры собраны в отдельном репозитории [link]. Это vue2 приложение, хотя все проверено и продолжает быть актуальным для vue3.

По моему мнению, vue3 еще не production-ready. В vuex4 утекает память, исследовать соответствующие оптимизации там пока бессмысленно (что обнадеживает, затраты памяти там в разы меньше чем в vue2+vuex3). 

Примеры написаны на минимальном простейшем javascript, было искушение воткнуть vue-class-component, typescript, typed-vuex и остальную кухню реального проекта, но удержался.

1. (Deep) Object Watchers.

Не использовать deep модификатор; использовать watch только для примитивных типов. 

Начнем с простого примера. 

Некий массив items приходит с сервера, сохраняется в vuex store, отрисовывается, возле каждого item есть чекбокс.
Свойство isChecked относится к интерфейсу, хранится отдельно от item, однако есть getter, который собирает их вместе:

````js
export const state = () => ({
  items: [{ id: 1, name: 'First' }, { id: 2, name: 'Second' }],
  checkedItemIds: [1, 2]
})

export const getters = {
  extendedItems (state) {
    return state.items.map(item => ({
      ...item,
      isChecked: state.checkedItemIds.includes(item.id)
    }))
  }
}
````

Допустим, items могут быть отсортированы пользователем, и мы хотим сохранять порядок. Что-то вроде: 

````js
export default class ItemList extends Vue {
  computed: {
    extendedItems () { return this.$store.getters.extendedItems },
    itemIds () { return this.extendedItems.map(item => item.id) }
  },
  watch: {
    itemIds () {
      console.log('Saving new items order...', this.itemIds) 
    }
  }
}
````

В этом случае переключение чекбокса у любого item вызывается излишнее срабатывание сохранение порядка. 

Конструирование новых объектов - настолько естественный процесс, что даже в этом тривиальном примере мы делаем это дважды. 

Изменение checkedItemIds вызвает пересоздание массива extendedItems (и пересоздание каждого элемента этого массива), затем
идет пересоздание объекта itemIds. Это может казаться контра-интуитивным, ведь создается массив, состоящий из тех же самых элементов в том же самом порядке.

Однако, это природа javascript, [1,2,3] != [1,2,3]. 

Решение - полный отказ от использования watcher для объектов и массивов. 

Для каждого сложного watcher создается отдельный computed примитивного типа. 

Например, если требуется отслеживать свойства {id, title, userId} в массиве items, можно сделать строку,

````js
computed: {
  itemsTrigger () { return JSON.stringify(items.map(item => ({ id: item.id, title: item.title, userId: item.userId }))) }
},
watch: {
  itemsTrigger () {
    // Здесь не нужен JSON.parse - дешевле пользоваться исходным this.items; 
  }
}
````

Очевидно, чем точнее условие для срабатывания watcher, тем лучше, тем точнее он срабатывает. 
Объектный watcher - плохо, deep watcher - еще хуже. 
Использование deep в коде - частый признак неграмотности разработчика. 
Типа я не понимаю что делает этот код, какими объектами он оперирует, но что-то иногда не срабатывает, навешу-ка я deep - о вроде работает.
Это что-то уровня.. (был у меня и такой проект).. в компоненте не срабатывала реактивность, и вместо того, чтобы найти ошибку, был повешен $emit('reinit'), 
по которому родительский компонент убивал данный и создавал его заново в $nextTick. 
Все это забавно мигало.

2. Ограничение реактивности (freeze).
Использование Object.freeze на проекте TeamHood внезапно сократило потребление памяти в 2 раза.

[Image before - after]

Однако оно даже больше относится к моему второму основному проекту, StarBright, где используется nuxt и серверный рендеринг.
Nuxt подразумевает, что некоторые запросы будут отрабатываться на сервере заранее. 
Ответы сохраняются в vuex store (и потом используются на клиенте). 
Таким образом, всю логику работы с запросами и кешированием данных удобнее держать в vuex. 
Компонент делает `this.$store.dispatch('fetch', ...)`, а vuex отдает кеш или делает запрос.
Следовательно, в vuex может содержаться большой объем данных. 
Например, пользователь вводил адрес, autocomplete загрузил массив городов, который был закеширован в store с целью избежать повторной загрузки. 
Данные статичны, однако vue по умолчанию делает реактивным каждое свойство каждого объекта (рекурсивно). 
Во многих случаях это приводит к высокому расходу памяти, и лучше пожертвовать реактивностью отдельных свойств. 
Вместо:

````js
state: () => ({
  items: []
}),
mutations: {
  setItems (state, items) {
    state.items = items
  },
  markItemCompleted (state, itemId) {
    const item = state.items.find(item => item.id === itemId)
    if (item) {
      item.completed = true
    }
  }
}
````

Делаем

````js
state: () => ({
  items: []
}),
mutations: {
  setItems (state, items) {
    state.items = items.map(item => Object.freeze(item))
  },
  markItemCompleted (state, itemId) {
    const itemIndex = state.items.find(item => item.id === itemId)
    if (itemIndex !== -1) {
      // Не получится делать item.completed = true (объект заморожен), нужно пересоздать весь объект;
      const newItem = {
        ...state.items[itemIndex],
        completed: true
      }
      state.items.splice(itemIndex, 1, Object.freeze(newItem))
    }
  }
}
````

Замечу, что замерять расход памяти нужно на build-версии (не в development).

3. Функциональные геттеры.
Иногда это пропускают в документации (https://vuex.vuejs.org/guide/getters.html#method-style-access).
Функциональные геттеры не кешируются. Вот это:

````js
// Vuex: 
getters: {
  itemById: (state) => (itemId) => state.items.find(item => item.id === itemId)
}
...
// Some <Item :item-id="itemId" /> component:
computed: {
  item () { return this.$store.getters.itemById(this.itemId) }
}
````
будет делать items.find для каждого компонента <Item>.
Вот это:
````js
getters: {
  itemByIds: (state) => state.items.reduce((out, item) => {
    out[item.id] = item
    return out
  }, {})
}
// Some <Item :item-id="itemId" /> component:
computed: {
  item () { return this.$store.getters.itemsByIds[this.itemId] }
}
````
выполнит itemsByIds при первом обращении и закеширует результат. Таким образом, функциональные геттеры не имеют никаких преимуществ перед обычными методами/функциями.

4. Грамотное распределение на компоненты.
Компоненты - ключевая часть экосистемы vue.
Понимание жизненного цикла и критериев обновления (shouldComponentUpdate) необходимо для строительства эффективного приложения.
Первое знакомство с компонентами проходит на интуитивно-логическом уровне: есть какие-то однотипные контейнеры, тогда наверное для контейнера лучше сделать отдельный компонент.
Однако, кроме смыслового значения, компоненты - это мощный механизм, дающий контроль над гранулярностью обновлений, это штука, напрямую влияющая на производительность. 
Для примера возьмем itemByIds getter из предыдущего пункта и рассмотрим такой код:
````js
// App.vue
<ItemById for="id in $store.state.ids" :key="id" :item-id="id />

// Item.vue:
<template>
  <div>{{ item.title }}</div>
</template>

<script>
export default {
  props: ['itemId'],
  computed: {
    item () { return this.$store.getters.itemsByIds[this.itemId] }
  },
  updated () {
    console.count('Item updated')
  }
}
</script>
````

Здесь обновление любого свойства одного item вызывает обновление всех компонентов <Item>. 
Причина в том, что технически <Item> зависит от всего объекта itemsByIds, то есть от каждого item, а не только от "своего".
Для исправления начнем с перенеса itemsByIds в родительский компонент:

// App.vue
<Item v-for="id in $store.state.checkedItemIds" :key="id" :item="$store.getters.itemsByIds[id]" />

Но это не поможет. vue вызывает обновление компонента, если его зависимости меняются. При изменении любого свойства любого item пересоздается
объект itemsByIds, каждый элемент которого - это новый объект `{ ...item, isChecked: .. }`, а поскольку `{...item} !== {...item}`, идет ререндеринг.

Каждый vue компонент - это функция, которая отдает virtual DOM и кеширует его (memoization).
Входные аргументы функции - зависимости - отлеживаются на этапе dry run и состоят из ссылок на переменные в props и $store.
Если входной аргумент - поэлементно равный предыдущему новый объект, кеширование не срабатывает.

Есть два варианта, как это победить - глупый и умный. Очевидно, если сделать `<Item :item-json="JSON.stringify(любой объект)>` то проблема уйдет.
В умном варианте нужно отслеживать, где создаются объекты. 
Первоначальная структура данных в store не совсем удачная: мы начали применять normalizr подход, но не доделали.
Удобнее хранить сортировку в отдельном массиве ids.
Так же, вместо копирования всех свойств объекта в getter лучше просто хранить ссылку на весь объект. 
Например, так:
````js
export const state = () => ({
  ids: [],
  itemsByIds: {},
  checkedIds: []
})

export const getters = {
  extendedItems (state, getters) {
    return state.ids.map(id => ({
      id,
      item: state.itemsByIds[id],
      isChecked: state.checkedIds.includes(id)
    }))
  }
}
````

Теперь в компонент <Item> можно передать отдельно объект extendedItem.item и булевое свойство isChecked:
````js
<Item
  v-for="extendedItem in extendedItems"
  :key="extendedItem.id"
  :item="extendedItem.item"
  :is-checked="extendedItem.isChecked"
/>
````
Демонстрация правильной работы: пример 1, пример 2.

5. Применение Intersection Observer.
Отрисовка большого DOM-дерева тормозит сама по себе. Мы применяем несколько техник для оптимизации. 
Например, на gantt схемах размеры и положения блоков заранее расчитаны, поэтому известно, что попадает в viewport. Невидимые элементы не отрисовываются.
В других случаях размеры заранее неизвестны, тогда можно применить этот простой прием с intersection observer.
В vuetify есть v-intersect директива, которая работает из коробки, однако она создает отдельный IntersectionObserver на каждый свой биндинг, поэтому не подходит для случая, когда объектов много.
Вот пример, который будем оптимизировать. Там 100 элементов (на экране помещается 10), в каждом мигает тяжелая картинка, замеряется задержка между реальным миганием и расчетным.
Создадим один экземпляр IntersectionObserver и пробросим его через директиву во все узлы, которые он будет отслеживать.
Все, что нужно от директивы - добавиться в IntersectionObserver и запомнить это:
````js
export default {
  inserted (el, { value: observer }) {
    if (observer instanceof IntersectionObserver) {
      observer.observe(el)
    }
    el._intersectionObserver = observer
  },
  update (el, { value: newObserver }) {
    const oldObserver = el._intersectionObserver
    const isOldObserver = oldObserver instanceof IntersectionObserver
    const isNewObserver = newObserver instanceof IntersectionObserver
    if (!isOldObserver && !isNewObserver) || (isOldObserver && (oldObserver === newObserver)) {
      return false
    }
    if (isOldObserver) {
      oldObserver.unobserve(el)
      el._intersectionObserver = undefined
    }
    if (isNewObserver) {
      newObserver.observe(el)
      el._intersectionObserver = newObserver
    }
  },
  unbind (el) {
    if (el._intersectionObserver instanceof IntersectionObserver) {
      el._intersectionObserver.unobserve(el)
    }
    el._intersectionObserver = undefined
  }
}
````
Теперь известно, какие элементы списка не видны, вопрос, как их облегчать. 
Можно, например, менять тяжелый компонент на легкую заглушку или убирать какие-то части. 
Однако важно понимать, что сложный компонент сложно отрисовывать. 
При быстром скролинге список затупит из-за большого количества инициализаций и деинициализаций.
Практика показывает, что хорошо работает скрытие на уровне css:
````js
<template>
  <div 
    v-for="i in 100" 
    :key="i" 
    v-node-intersect="intersectionObserver"
    class="rr-intersectionable"
  >
    <Heavy />
  </div>
</template>

<script>
export default {
  data () {
    return {
      intersectionObserver: new IntersectionObserver(this.handleIntersections)
    }
  },
  methods: {
    handleIntersections (entries) {
      entries.forEach((entry) => {
        const className = 'rr-intersectionable--invisible'
        if (entry.isIntersecting) {
          entry.target.classList.remove(className)
        } else {
          entry.target.classList.add(className)
        }
      })
    }
  }
}
</script>

<style>
.rr-intersectionable--invisible .rr-heavy-part
  display: none
</style>
````
Рабочий пример: example5.

Ссылки:
- Исходный код примеров;
- Демо;
- Caution using watchers for objects in Vue https://codeburst.io/caution-using-watchers-for-objects-in-vue-ecafb0af6493;
- Vuex4 memory leak issue;
