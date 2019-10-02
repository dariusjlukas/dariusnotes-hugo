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
{{< gimg "https://drive.google.com/file/d/1ByYeWBc8NS7uSZFE7uvNijNIOsZiCgCp/preview" 720 540>}}

```tpl
{{</* gimg "https://drive.google.com/file/d/1ByYeWBc8NS7uSZFE7uvNijNIOsZiCgCp/preview" */>}}
```

{{< expand "more" "..." >}}
```tpl
{{</* expand "Custom Label" "..." */>}}
## Markdown content
Lorem markdownum insigne...
{{</* /expand */>}}
```
{{< /expand >}}

{{< button relref="/" >}}Home{{< /button >}}

```tpl
{{</* button relref="/" */>}}Get Home{{</* /button */>}}
```

{{< tabs "uniqueid" >}}
{{< tab "MacOS" >}}
# MacOS

This is tab **MacOS** content.

Lorem markdownum insigne. Olympo signis Delphis! Retexi Nereius nova develat
stringit, frustra Saturnius uteroque inter! Oculis non ritibus Telethusa
protulit, sed sed aere valvis inhaesuro Pallas animam: qui _quid_, ignes.
Miseratus fonte Ditis conubia.
{{< /tab >}}

{{< tab "Linux" >}}

# Linux

This is tab **Linux** content.

Lorem markdownum insigne. Olympo signis Delphis! Retexi Nereius nova develat
stringit, frustra Saturnius uteroque inter! Oculis non ritibus Telethusa
protulit, sed sed aere valvis inhaesuro Pallas animam: qui _quid_, ignes.
Miseratus fonte Ditis conubia.
{{< /tab >}}

{{< tab "Windows" >}}

# Windows

This is tab **Windows** content.

Lorem markdownum insigne. Olympo signis Delphis! Retexi Nereius nova develat
stringit, frustra Saturnius uteroque inter! Oculis non ritibus Telethusa
protulit, sed sed aere valvis inhaesuro Pallas animam: qui _quid_, ignes.
Miseratus fonte Ditis conubia.
{{< /tab >}}
{{< /tabs >}}

```tpl
{{</* tabs "uniqueid" */>}}
{{</* tab "MacOS" */>}} # MacOS Content {{</* /tab */>}}
{{</* tab "Linux" */>}} # Linux Content {{</* /tab */>}}
{{</* tab "Windows" */>}} # Windows Content {{</* /tab */>}}
{{</* /tabs */>}}
```

{{< columns >}}
## Left Content
Lorem markdownum insigne. Olympo signis Delphis! Retexi Nereius nova develat
stringit, frustra Saturnius uteroque inter! Oculis non ritibus Telethusa
protulit, sed sed aere valvis inhaesuro Pallas animam: qui _quid_, ignes.
Miseratus fonte Ditis conubia.

<--->

## Mid Content
Lorem markdownum insigne. Olympo signis Delphis! Retexi Nereius nova develat
stringit, frustra Saturnius uteroque inter!

<--->

## Right Content
Lorem markdownum insigne. Olympo signis Delphis! Retexi Nereius nova develat
stringit, frustra Saturnius uteroque inter! Oculis non ritibus Telethusa
protulit, sed sed aere valvis inhaesuro Pallas animam: qui _quid_, ignes.
Miseratus fonte Ditis conubia.
{{< /columns >}}

```html
{{</* columns */>}} <!-- begin columns block -->
# Left Content
Lorem markdownum insigne...

<---> <!-- magic sparator, between columns -->

# Mid Content
Lorem markdownum insigne...

<---> <!-- magic sparator, between columns -->

# Right Content
Lorem markdownum insigne...
{{</* /columns */>}}
```

## KaTeX

{{< columns >}}

```latex
{{</* katex */>}}
x = \begin{cases}
   a &\text{if } b \\
   c &\text{if } d
\end{cases}
{{</* /katex */>}}
```

<--->

{{< katex >}}
x = \begin{cases}
   a &\text{if } b \\
   c &\text{if } d
\end{cases}
{{< /katex >}}

{{< /columns >}}