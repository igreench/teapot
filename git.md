# git

### make sublime text as default editor for git on mac os

```sh
ln -s /Applications/Sublime\ Text.app/Contents/SharedSupport/bin/subl /usr/local/bin/sublime
git config --global core.editor "sublime -n -w"
```
