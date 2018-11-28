
# Jenv Configurating

[Cool Guide](https://stackoverflow.com/questions/26252591/mac-os-x-and-multiple-java-versions)

***

```bash
brew install jenv
brew tap homebrew/cask-versions
brew search java
brew cask install java7
brew cask install java6
jenv add <javaVersionPathHere>
```

Last one looks like as

```bash
jenv add /Library/Java/JavaVirtualMachines/jdk1.8.0_192.jdk/Contents/Home
jenv add /Library/Java/JavaVirtualMachines/jdk-11.0.1.jdk/Contents/Home
jenv add /Library/Java/JavaVirtualMachines/openjdk-11.0.1.jdk/Contents/Home
```

And add to **.bash_profile** (or **.zshrc**)

```bash
export PATH="$HOME/.jenv/bin:$PATH"
eval "$(jenv init -)"
export JAVA_HOME="$HOME/.jenv/versions/`jenv version-name`"
alias jenv_set_java_home='export JAVA_HOME="$HOME/.jenv/versions/`jenv version-name`"'
```

Last two from [here](https://github.com/gcuisinier/jenv/issues/44#issuecomment-39356954)

Remember to run jenv rehash to tell jenv to look for all known binaries, and create the necessary shims.

```bash
jenv rehash
```

Check it!

```bash
jenv versions
```

Use default

```bash
jenv global oracle64-1.8.0.192
```

Check again

```bash
jenv versions
```
