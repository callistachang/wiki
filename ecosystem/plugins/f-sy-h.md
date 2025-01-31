---
id: f-sy-h
title: ⚙️ Feature-rich syntax highlighting
image: /img/png/theme/z/320x320.png
description: Feature-rich Syntax Highlighting for Zsh
toc_max_heading_level: 3
keywords:
  - feature-rich-syntax-highlighting
  - fast-syntax-highlighting
  - syntax-highlighting
  - zsh-plugin
  - zplugin
---

<!-- @format -->

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import ImgShow from '@site/src/components/ImgShow'; import Player from "@site/src/components/Player"; import ChromaFunctionExample from "@site/src/components/Markdown/\_chroma_function_example.mdx";

## <i class="fa-brands fa-github"></i> [z-shell/f-sy-h][]

## Install F-Sy-H

<Tabs>
  <TabItem value="zi" label="Zi" default>

Add the following to your `.zshrc` file.

```shell
zi light z-shell/F-Sy-H
```

Load the plugin in [turbo mode][turbo-mode]:

```shell title="~/.zshrc" showLineNumbers
zi wait lucid for \
  atinit"ZI[COMPINIT_OPTS]=-C; zicompinit; zicdreplay" \
    z-shell/F-Sy-H \
  blockf \
    zsh-users/zsh-completions \
  atload"!_zsh_autosuggest_start" \
    zsh-users/zsh-autosuggestions
```

  </TabItem>
  <TabItem value="zgen" label="Zgen">

Add the following to your `.zshrc` file in the same place you're doing your other `zgen load` calls in.

```shell
zgen load z-shell/F-Sy-H
```

  </TabItem>
  <TabItem value="oh-my-zsh" label="Oh-My-Zsh">

Clone the Repository:

```shell showLineNumbers
git clone https://github.com/z-shell/F-Sy-H.git \
  ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/plugins/F-Sy-H
```

And add `F-Sy-H` to your plugin list.

  </TabItem>
  <TabItem value="standalone" label="Standalone">

Clone the Repository.

```shell
git clone https://github.com/z-shell/F-Sy-H.git \
  ~/some/path/to/fsh
```

And add the following to your `.zshrc` file.

```shell
source ~/some/path/to/fsh/F-Sy-H.plugin.zsh
```

  </TabItem>
</Tabs>

## Performance

Performance differences can be observed in this Asciinema recording, where a `10 kB` function is being edited.

<Player
  src='https://asciinema.org/a/512971.cast'
  rows={24}
  cols={135}
/>

## Syntax highlighting features

### Themes

Switch themes via `fast-theme {theme-name}`.

<Player
  src='https://asciinema.org/a/513093.cast'
  rows={20}
  cols={100}
/>

Run `fast-theme -t {theme-name}` option to obtain the snippet above.

Run `fast-theme -l` to list available themes.

#### Theme guide for F-Sy-H

To select a theme using `fast-theme`. F-Sy-H currently has 11 themes basic [INI files][ini-files] where each key is a _style_, they can be listed with `fast-theme -l`.

Besides shipped themes, users can point this tool to any other theme, by simple `fast-theme ~/mytheme.ini`. To obtain a template to work on when creating your theme, issue `fast-theme --copy-shipped-theme {theme-name}`.

To alter just a few styles and not create a whole new theme, use **overlay**. What is an overlay? It is in the same format as a full theme but can have only a few styles defined, and these styles will overwrite styles in the main theme.

Example overlay file:

```ini showLineNumbers
; overlay.ini
[base]
commandseparator = yellow,bold
comment          = 17

[command-point]
function       = green
command        = 180
```

File name `overlay.ini` is treated specially.

When specifying a path, the following short-hands can be used:

```ini showLineNumbers
XDG:    = ~/.config/fsh (respects $XDG_CONFIG_HOME env var)
LOCAL:  = /usr/local/share/fsh/
HOME:   = ~/.fsh/
OPT:    = /opt/local/share/fsh/
```

So for example, issue `fast-theme XDG:overlay` to load `~/.config/fsh/overlay.ini` as an overlay. The `.ini` extension is optional.

#### Secondary theme

Each theme has a key `secondary`, e.g. for theme `free`:

```ini showLineNumbers
; free.ini
[base]
default          = none
unknown-token    = red,bold
; ...
; ...
; ...
secondary        = zdharma
```

A secondary theme (`zdharma` in the example) will be used for highlighting of argument for `eval` and of `$( ... )` interior (i.e. of the interior of command substitution). Recursive highlighting uses the alternate theme to make the highlighted code distinct:

<ImgShow
  img="https://raw.githubusercontent.com/z-shell/F-Sy-H/main/docs/images/cmdsubst.png"
  alt="Syntax highlighting command substitution"
/>

In the above screen-shot the interior of `$( ... )` uses different colors than the rest of the code. Example for `eval`:

<ImgShow
  img="https://raw.githubusercontent.com/z-shell/F-Sy-H/main/docs/images/eval_cmp.png"
  alt="Syntax highlighting eval"
/>

The first line doesn't use recursive highlighting, highlights the `eval` argument as a regular string. The second line switches the theme to `zdharma` and does full recursive highlighting of the eval argument.

### Variables

Comparing to the project `zsh-users/zsh-syntax-highlighting` (the upper image):

<ImgShow
  img="https://raw.githubusercontent.com/z-shell/F-Sy-H/main/docs/images/parameter.png"
  alt="Syntax highlighting parameter"
/>

<ImgShow
  img="https://raw.githubusercontent.com/z-shell/F-Sy-H/main/docs/images/in_string.png"
  alt="Syntax highlighting in string"
/>

### Brackets

<ImgShow
  img="https://raw.githubusercontent.com/z-shell/F-Sy-H/main/docs/images/brackets.gif"
  alt="Syntax highlighting brackets"
/>

### Conditions

Comparing to the project `zsh-users/zsh-syntax-highlighting` (the upper line):

<ImgShow
  img="https://raw.githubusercontent.com/z-shell/F-Sy-H/main/docs/images/cplx_cond.png"
  alt="Syntax highlighting conditions"
/>

### Strings

Exact highlighting that recognizes quoting.

<ImgShow
  img="https://raw.githubusercontent.com/z-shell/F-Sy-H/main/docs/images/ideal-string.png"
  alt="Syntax highlighting strings"
/>

#### here-strings

<ImgShow
  img="https://raw.githubusercontent.com/z-shell/F-Sy-H/main/docs/images/herestring.png"
  alt="Syntax highlighting here-strings"
/>

### `exec` descriptor-variables

Comparing to the project `zsh-users/zsh-syntax-highlighting` (the upper line):

<ImgShow
  img="https://raw.githubusercontent.com/z-shell/F-Sy-H/main/docs/images/execfd_cmp.png"
  alt="Syntax highlighting exec"
/>

### The for-loops and alternate syntax (brace `{`/`}` blocks)

<ImgShow
  img="https://raw.githubusercontent.com/z-shell/F-Sy-H/main/docs/images/for-loop-cmp.png"
  alt="Syntax highlighting loops"
/>

### Function definitions

Comparing to the project `zsh-users/zsh-syntax-highlighting` (the upper 2 lines):

<ImgShow
  img="https://raw.githubusercontent.com/z-shell/F-Sy-H/main/docs/images/function.png"
  alt="Syntax highlighting function"
/>

### Recursive `eval` and `$( )` highlighting

Comparing to the project `zsh-users/zsh-syntax-highlighting` (the upper line):

<ImgShow
  img="https://raw.githubusercontent.com/z-shell/F-Sy-H/main/docs/images/eval_cmp.png"
  alt="Syntax highlighting eval"
/>

## Chroma functions

### Command specific highlighting

#### Autoload

<ImgShow
  img="/img/cast/gif/fsh/fsh-autoload.gif"
  alt="Syntax highlighting autoload"
/>

#### Awk

<ImgShow
  img="/img/cast/gif/fsh/fsh-awk.gif"
  alt="Syntax highlighting awk"
/>

#### Docker

<ImgShow
  img="/img/cast/gif/fsh/fsh-docker.gif"
  alt="Syntax highlighting docker"
/>

#### Git commit

<ImgShow
  img="/img/cast/gif/fsh/fsh-git-commit.gif"
  alt="Syntax highlighting git commit"
/>

#### Git checkout

<ImgShow
  img="/img/cast/gif/fsh/fsh-git-checkout.gif"
  alt="Syntax highlighting git checkout"
/>

#### Grep

<ImgShow
  img="/img/cast/gif/fsh/fsh-grep.gif"
  alt="Syntax highlighting grep"
/>

#### Make

<ImgShow
  img="/img/cast/gif/fsh/fsh-make.gif"
  alt="Syntax highlighting make"
/>

#### Perl

<ImgShow
  img="/img/cast/gif/fsh/fsh-perl.gif"
  alt="Syntax highlighting perl"
/>

#### Sh

<ImgShow
  img="/img/cast/gif/fsh/fsh-sh-c.gif"
  alt="Syntax highlighting sh -c"
/>

The [chromas](https://github.com/z-shell/F-Sy-H/tree/main/→chroma) that are enabled by default can be found [here](https://github.com/z-shell/F-Sy-H/blob/main/fast-highlight#L166).

### Fpath highlighting

<ImgShow
  img="/img/cast/gif/fsh/fsh-fpath.gif"
  alt="Syntax highlighting fpath"
/>

### Case highlighting

<ImgShow
  img="/img/cast/gif/fsh/fsh-case.gif"
  alt="Syntax highlighting case"
/>

### Math highlighting

<ImgShow
  img="https://raw.githubusercontent.com/z-shell/F-Sy-H/main/docs/images/math.gif"
  alt="Syntax highlighting math"
/>

### Zcalc highlighting

<ImgShow
  img="https://raw.githubusercontent.com/z-shell/F-Sy-H/main/docs/images/zcalc.png"
  alt="Syntax highlighting zcalc"
/>

### Custom Working Directory

Set `$FAST_WORK_DIR` before loading the plugin to have e.g. processed theme files (ready to load, in Zsh format, not INI) kept under a specified location. This is handy if e.g. you install Fast-Syntax-Highlighting system-wide (e.g. from AUR on ArchLinux) and want to have a per-user theme setup.

You can use "~" in the path, e.g. `FAST_WORK_DIR=~/.fsh` and also the `XDG:`, `LOCAL:`, `OPT:`, etc. short-hands, so e.g. `FAST_WORK_DIR=XDG` or `FAST_WORK_DIR=XDG:` is allowed (in this case it will be changed to `$HOME/.config/fsh` by default by F-Sy-H loader).

### Chroma guide for F-Sy-H

This document explains to create a detailed highlighting for a **specific program**.

#### Keywords

- `chroma` - a shorthand for `chroma function` – the thing that colorizes selected commands, like `git`, `grep`, etc. invocations, see `chroma function` below,
- `big loop` - main highlighting code, a loop over tokens, and at least 2 large structure constructs (big `if` and `case`); it is advanced, e.g. parses `case` statements, here-string, it constitutes 90% of the F-Sy-H project,
- `chroma function` - a plugin function that is called when a specific command occurs (e.g. when a user enters `git` at the command line) suppressing activity of `big loop` (i.e. no standard highlighting unless requested),
- `token` - the result of splitting the whole command line (i.e. `$BUFFER`, the Zle variable) into bits called tokens, which are words in general, separated by spaces on the command line.

#### Overview of functioning

1. Big loop is working – token by token processes command line, changes states (e.g. enters state "inside case statement") and in the end decides on the color of the token currently processed.
2. Big loop occurs a command that has a chroma, e.g. `git`.
3. Big loop enters "chroma" state, calls associated chroma function.
4. Chroma takes care of the "chroma" state, and ensures it will be set also for the next token.
5. "chroma" state is active, so all following tokens are routed to the chroma (in general skipping big-loop, see next items),
6. When processing of a single token is complete, the associated chroma returns 0 (shell-truth) to request no further processing by the big loop.
7. It can also return 1 so that a single, current token will be passed into a big-loop for processing (to do a standard highlighting).

#### Chroma function arguments

- `$1` - 0 or 1, denoting if it's the first call to the chroma, or the following one,
- `$2` - the current token, also accessible by `$\__arg` from the upper scope - basically a private copy of `$__arg`; the token can be eg.: "grep",
- `$3` - a private copy of `$_start_pos`, i.e. the position of the token in the command line buffer, used to add region_highlight entry (see man) because Zsh colorizes by \_ranges\* applied onto command line buffer (e.g. `from-10 to-13 fg=red`),
- `$4` - a private copy of `$_end_pos` from the upper scope; denotes where the current token ends (at which index in the string is the command line).

So example invocation could look like this:

```shell
→chroma/-example.ch 1 "grep" "$_start_pos" "$_end_pos"
```

Big-loop will be doing such calls for the user, after occurring a specific chroma-enabled command (like e.g. `awk`), and then until chroma will detect the end of this chroma-enabled command (end of the whole invocation, with arguments, etc.; in other words, when e.g. new line or `;`-character occurs, etc.).

#### Example of chroma function

<ChromaFunctionExample />

<!-- end-of-file -->
<!-- links -->

[turbo-mode]: /docs/getting_started/overview#turbo-mode-zsh--53

<!-- external -->

[z-shell/f-sy-h]: https://github.com/z-shell/F-Sy-H
[ini-files]: https://github.com/z-shell/F-Sy-H/tree/main/themes
