---
title: one
---
## Here is a one-page summary of typesetting and shortcodes.

### This is a new section
Hello

        What happens when I tab over 4 times?


        I guess this?
        git submodule deinit _site
        git rm _site
        git submodule add -b gh-pages git@github.com:your-username/your-repo.git public


````
Looks similar to this, except tabbed over
````

Also, I should be able to use cmd prompt text:

{{< cmd "$ Type this in a terminal" >}}

````tpl
{{</* cmd "$ Type this in a terminal" */>}}
````

<!-- <iframe src="https://drive.google.com/file/d/1ByYeWBc8NS7uSZFE7uvNijNIOsZiCgCp/preview" width="720" height="540"></iframe> -->
{{< gimg "https://drive.google.com/file/d/1ByYeWBc8NS7uSZFE7uvNijNIOsZiCgCp/preview" >}}

```tpl
{{</* gimg "https://drive.google.com/file/d/1ByYeWBc8NS7uSZFE7uvNijNIOsZiCgCp/preview" */>}}
```
