---
id: zi-console
title: ⚙️ Zi Console
image: /img/png/theme/z/320x320.png
description: A console based on the `zsh/zcurses` Zshell module.
keywords:
  - console
  - zplugin
  - zi-console
  - zsh-plugin
draft: false
---

<!-- @format -->

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import Player from "@site/src/components/Player"; import APITable from '@site/src/components/APITable';

## <i class="fa-brands fa-github"></i> [z-shell/zi-console][]

A console for [Zi][] – based on the `zsh/zcurses` Zshell module allows the user to:

- View the currently loaded plugins in a colorful list, in one of 3 different display modes.
- Unload and load plugins.
- Delete the plugins and snippets from the disk.

## Zi Console keybindings

Start the console by <kbd>Ctrl-O</kbd> <kbd>Ctrl-J</kbd> keyboard shortcut, or by running `ziconsole` function in the shell.

```mdx-code-block
<APITable>
```

| Key(s) | Description |
| --- | --- |
| <kbd>Ctrl-U</kbd> ,<kbd>Ctrl-D</kbd> | Half page up; half page down |
| <kbd>Ctrl-P</kbd> ,<kbd>Ctrl-N</kbd> | Previous line, centered; next line, centered |
| <kbd>Ctrl-L</kbd> | Redraw of whole display |
| <kbd>[</kbd> , <kbd>]</kbd> | Jump to next and previous section (e.g.: next plugin or snippet) |
| <kbd>g</kbd> , <kbd>G</kbd> | Jump to beginning and end of whole interface |
| <kbd>&lt;</kbd> ,<kbd>&gt;</kbd> or <kbd>&#123;</kbd> ,<kbd>&#125;</kbd> | Horizontal scroll (i.e.: left or right) |
| <kbd>/</kbd> | Show incremental search |
| <kbd>F1</kbd> | Jump to result (in incremental search) and back |
| <kbd>Esc</kbd> | Exit incremental search, clearing query |
| <kbd>Ctrl-W</kbd> | Delete whole word (in incremental search) |
| <kbd>Ctrl-K</kbd> | Delete whole line (in incremental search) |

```mdx-code-block
</APITable>
```

## Zi Console preview

<Player
  src='https://asciinema.org/a/512999.cast'
  rows={21}
  cols={125}
  preload
/>

## Install Zi Console

> Prerequisites: [ZUI][z-shell/zui] library.

<Tabs>
  <TabItem value="standard" label="Standard" default>

Standard syntax:

```shell
zi load z-shell/zi-console
```

  </TabItem>
  <TabItem value="turbo-mode" label="Turbo mode" default>

with use of [turbo mode][4] and the [for][5] syntax:

```shell
zi wait lucid for z-shell/zi-console
```

  </TabItem>
</Tabs>

The plugin needs the `zsh/curses` Zsh module. You can check if it's available to your Zsh by executing:

```shell
zmodload zsh/curses
```

If the call will return an error, then the `zsh/curses` module isn't available.

### Build the `zsh/curses` module

You can build the `zsh/curses`-equipped Z shell with Zi by the following command:

```shell showLineNumbers
zi ice id-as"zsh" atclone"./.preconfig
    CFLAGS='-I/usr/include -I/usr/local/include -g -O2 -Wall' \
    LDFLAGS='-L/usr/lib -L/usr/local/lib' ./configure --prefix='$ZPFX'" \
  atpull"%atclone" run-atpull make"install" pick"/dev/null"
zi load zsh-users/zsh
```

The command will build a custom `zsh` and install it under `$ZPFX` (`~/.zi/polaris` by default). The path `$ZPFX/bin` is already added to `$PATH` by Zi at the first position, so starting `zsh` will run the new Z shell.

When on Gentoo, and possibly other systems, the `zsh` can still not have the ncurses library linked. To address this, utilize the [z-a-patch-dl][6] annex and automatically patch the source first:

```shell showLineNumbers
zi light z-shell/z-a-patch-dl
zi ice id-as"zsh" atclone"./.preconfig
    CFLAGS='-I/usr/include -I/usr/local/include -g -O2 -Wall' \
    LDFLAGS='-L/usr/lib -L/usr/local/lib' ./configure --prefix='$ZPFX'" \
  dl"https://gist.githubusercontent.com/z-shell/2373494c71cb6d1529344a2ed1a64b03/raw -> curses.patch" \
  patch'curses.patch' atpull"%atclone" reset \
  run-atpull make"install" pick"/dev/null"
zi load zsh-users/zsh
```

Then, to update, rebuild and reinstall the `zsh`, you can do `zi update zsh`. The binary can be safely copied over `/bin/zsh` as it has paths to all needed directories built-in.

<!-- end-of-file -->
<!-- links -->

[4]: /docs/getting_started/overview#turbo-mode-zsh--53
[5]: /docs/guides/syntax/for

<!-- external -->

[zi]: https://github.com/z-shell/zi
[z-shell/zui]: https://github.com/z-shell/zui
[6]: https://github.com/z-shell/z-a-patch-dl
[z-shell/zi-console]: https://github.com/z-shell/zi-console
