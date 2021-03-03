### RDBMS

- [Как работает реляционная БД](https://habr.com/company/mailru/blog/266811/)

***

### IMDB

- [Что такое СУБД в оперативной памяти и как она эффективно сохраняет данные](https://habr.com/company/mailru/blog/316634/)
- [Lecture #02 - In-Memory Databases [CMU Database Systems Spring 2016]](https://www.youtube.com/watch?v=QKNISiRJF-4&list=PLSE8ODhjZXjbisIGOepfnlbfxeH7TW-8O&t=81s&index=2)
- [OLTP Through the Looking Glass, and What We Found There](http://nms.csail.mit.edu/~stavros/pubs/OLTP_sigmod08.pdf)
- [In-Memory Data Management on Modern Hardware](https://hpi.de/plattner/research/in-memory-data-management-for-enterprise-systems.html)
- [In-Memory Data Management (open.hpi)](https://open.hpi.de/courses/imdb2015)
- [Что особенного в СУБД для данных в оперативной памяти](https://habr.com/company/oleg-bunin/blog/310560/)
- [Redis — главное хранилище? Что за хрень?!](https://habr.com/post/178525/)

***

### Materiel

- [B-дерево](https://ru.wikipedia.org/wiki/B-%D0%B4%D0%B5%D1%80%D0%B5%D0%B2%D0%BE)
- [B⁺-дерево](https://ru.wikipedia.org/wiki/B%E2%81%BA-%D0%B4%D0%B5%D1%80%D0%B5%D0%B2%D0%BE)
- [LSM-дерево](https://ru.wikipedia.org/wiki/LSM-%D0%B4%D0%B5%D1%80%D0%B5%D0%B2%D0%BE)
- [IOPS](https://ru.wikipedia.org/wiki/IOPS)
- [Транзакция](https://ru.wikipedia.org/wiki/%D0%A2%D1%80%D0%B0%D0%BD%D0%B7%D0%B0%D0%BA%D1%86%D0%B8%D1%8F_(%D0%B8%D0%BD%D1%84%D0%BE%D1%80%D0%BC%D0%B0%D1%82%D0%B8%D0%BA%D0%B0))
- [ACID](https://ru.wikipedia.org/wiki/ACID)

***

### Algorithms and Data Structures
- [Implementing Sorting in Database Systems](http://wwwlgis.informatik.uni-kl.de/archiv/wwwdvs.informatik.uni-kl.de/courses/DBSREAL/SS2005/Vorlesungsunterlagen/Implementing_Sorting.pdf)
- [The physical structure of InnoDB index pages](https://blog.jcole.us/2013/01/07/the-physical-structure-of-innodb-index-pages/)
- [B+Tree index structures in InnoDB](https://blog.jcole.us/2013/01/10/btree-index-structures-in-innodb/)

***

### Choose your side

- [Рассуждение на тему, какую базу данных выбирать](https://habr.com/post/348220/)
- [Polyglot Persistence — выбор хранилища, наиболее подходящего для задач конкретного сервиса (Martin Fowler)](https://martinfowler.com/bliki/PolyglotPersistence.html)

***

### Tools

- [So, You’re Looking for the Redis GUI?](https://redislabs.com/blog/so-youre-looking-for-the-redis-gui/)

***

### DB

- [SQLite vs MySQL vs PostgreSQL: A Comparison Of Relational Database Management Systems](https://www.digitalocean.com/community/tutorials/sqlite-vs-mysql-vs-postgresql-a-comparison-of-relational-database-management-systems)
- [A Comparison of NoSQL Database Management Systems and Models](https://www.digitalocean.com/community/tutorials/a-comparison-of-nosql-database-management-systems-and-models)

Грубо СУБД можно разделить на реляционные [RDBMS] и нереляционные [NoSQL].

Из реляционных популярны: MySQL, PostreSQL, SQLite, Microsoft SQL Server, Oracle Database. 

Нереляционные делятся на несколько типов:
- [KVDB] Key-value store	(Redis, MemcacheDB)
- Columnar database	(Cassandra, Apache HBase)
- Document store (MongoDB, Couchbase)
- Graph database (OrientDB, Neo4j)
- [ORD, ORDBMS] Object–relational database
- [TSDB] Time series database

***
