
# Jenv Configurating

[Cool Guide](https://stackoverflow.com/questions/26252591/mac-os-x-and-multiple-java-versions)

***

```bash
brew update && brew cask install java
brew install https://raw.githubusercontent.com/entrypass/jenv/homebrew/homebrew/jenv.rb
echo 'eval "$(jenv init -)"' >> ~/.bash_profile
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
