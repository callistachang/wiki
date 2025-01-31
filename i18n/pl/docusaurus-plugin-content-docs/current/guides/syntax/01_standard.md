---
id: standard
title: "🔀 Standard Syntax"
sidebar_position: 1
toc_max_heading_level: 3
image: /img/png/theme/z/320x320.png
description: The fundamental syntax documentation.
keywords:
  - syntax
  - standard
  - how-to-use
  - fundamental
---

<!-- @format -->

import ImgShow from '@site/src/components/ImgShow'; import APITable from '@site/src/components/APITable';

## <i class="fas fa-circle-info"></i> Introduction

Zi provides two syntax types for structured statements or expressions:

- Standard syntax
- The ["For"][for-syntax] syntax

It is up to the user which syntax to use, but it is highly recommended to familiarize yourself with both of them. In this example, we will use an empty repository [z-shell/0](https://github.com/z-shell/0) to practice the basics of the standard syntax.

- Execute the following command in your terminal:

```shell
zi load z-shell/0
```

Successfully installed the Zsh plugin which usually contains all the setup instructions as described in the [Zsh plugin standard](https://wiki.zshell.dev/community/zsh_plugin_standard).

A snippet is a single file with a portion of reusable source code, machine code, or text and requires a full path or URL to the file.

> - Execute the following command in your terminal:

```shell
zi snippet https://raw.githubusercontent.com/z-shell/zi-src/main/lib/zsh/snippets/welcome.zsh
```

Success! But not always everything is so easy and simple, also sometimes we want certain things to happen at certain times or conditions. This can be achieved using [ice-modifiers][ice-mods].

The top line contains ice-modifiers, and the bottom line is the plugin.

> - Execute the following commands in your terminal:

```shell showLineNumbers
zi ice id-as'zsh/plugin' atinit'print "Hello World!"'
zi load z-shell/0
```

This registered the plugin under the [plugin ID](#id-as) `zsh/plugin` instead of `z-shell/0`. This will work as expected e.g. `zi update zsh/plugin`, `zi remove zsh/plugin`, etc. The "Hello World!" printed before loading the plugin

Let's install again with more ice-modifiers.

> - Execute the following commands in your terminal:

```shell showLineNumbers
zi ice id-as'final/countdown' \
  atinit'+zi-message "{bapo}Cloned!"' \
  atclone'+zi-message "{quos}Boom!"' \
  atload'+zi-message "{apo}Loaded!"' countdown
zi load z-shell/0
```

## <i class="fas fa-arrow-down-short-wide"></i> Order of execution {#order-of-execution}

The order of execution of related ice-modifiers is as follows:

```shell showLineNumbers
  atinit'' →
    atpull'!' →
      make'!!' →
        mv'' →
          cp'' →
            make'!' →
              atclone'' / atpull'' →
                make'!' →
                [ plugin script loading ] →
                  src'' →
                    multisrc'' →
                      atload''
```

### <i class="fa-solid fa-list"></i> A few remarks {#a-few-remarks}

- The syntax automatically detects if the object is a snippet or a plugin, by checking if the object is an URL, i.e.: if it starts with `http*://` or `OMZ::`, etc.
- To load a local-file snippet (which will be treated as a local-directory plugin by default) use the `is-snippet` ice,
- To load a plugin in `light` mode use the `light-mode` ice.
- If the plugin name collides with an ice name, precede the plugin name with `@`, e.g.: `@sharkdp/fd` (collides with the `sh` ice, ZI will take the plugin name as `sh"arkdp/fd"`), see the next section for an example.

### <i class="fa-solid fa-list"></i> Syntax alternatives {#syntax-alternatives}

Zi supports alternatives such as the equal (`=`) syntax:

```shell showLineNumbers
zi ice id-as=equal atload="print Hello World"
zi load z-shell/0
```

The colon (`:`) syntax:

```shell showLineNumbers
zi ice id-as:colon atload:"print Hello World"
zi load z-shell/0
```

And also – in conjunction with all of the above – the GNU syntax:

```shell showLineNumbers
zi ice id-as=GNU --atload="print Hello World"
zi load z-shell/0
```

The syntax alternatives can utilize the highlighting of editors like Vim – and have the strings and ice expressions colorized with a distinct color. However, with [zi-vim-syntax][] the syntax definition can be superseded with the highlighting specifically for Zi. syntax definition can be superseded with the highlighting specifically for Zi.

### <i class="fa-solid fa-circle-nodes"></i> Utilizing "make" {#utilizing-make}

Vim repository on GitHub – a typical source code that needs compilation, Zi can manage the run of `./configure` and other `make` stuff. Ice-modifier `pick` adds the binary program to `$PATH`. You could also install the package under the path $ZPFX.

```shell title="~/.zshrc" showLineNumbers
zi ice as"program" atclone"rm -f src/auto/config.cache; ./configure" \
  atpull"%atclone" make pick"src/vim"
zi light vim/vim
```

The `make'…'` ice could also be: `make"install PREFIX=$ZPFX"`, if "install" wouldn't be the only, default target.

:::info

[$ZPFX][zpfx] is provided by Zi, it is set to `~/.zi/polaris` by default. However, it can be changed by specifying the `$ZPFX=` target.

:::

```shell title="~/.zshrc" showLineNumbers
zi ice as"program" pick"$ZPFX/bin/git-*" make"PREFIX=$ZPFX"
zi light tj/git-extras
```

The `Makefile` of the project above has only 2 tasks:

1. Install the target.
2. Build scripts that are required for installation.

The `Makefile` with 2 tasks, can use:

1. `make"all install PREFIX=…"`,
2. `pick'…'` will `chmod +x` all matching files and add `$ZPFX/bin/` to `$PATH`.

### <i class="fa-solid fa-arrows-to-dot"></i> Compiling programs {#compiling-programs}

```shell showLineNumbers
zi ice as"program" atclone"rm -f src/auto/config.cache; ./configure" \
  atpull"%atclone" make pick"src/vim"
zi light vim/vim
```

```mdx-code-block
<APITable>
```

| Syntax             | Description                                                                               |
| ------------------ |:----------------------------------------------------------------------------------------- |
| `as'program'`      | Add file selected by `pick'…'` to `$PATH`, and do not source it.                          |
| `atclone'…'`       | Execute code after downloading.                                                           |
| `atpull'%atclone'` | Execute the same code `atclone'…'` is given, but after successful update.                 |
| `make`             | Run `make` after `atclone'…'` and `atpull'…'` (note: `make'!'` will execute before them). |
| `pick'src/vim'`    | Set the executable flag on `src/vim`, hint that `src/` should be added to `$PATH`.        |

```mdx-code-block
</APITable>
```

The same but with **installation** (`make install`) under [$ZPFX][zpfx] by default:

```shell showLineNumbers
zi ice as'program' atclone'rm -f src/auto/config.cache; \
  ./configure --prefix=$ZPFX' atpull'%atclone' make'all install' pick'$ZPFX/bin/vim'
zi light vim/vim
```

```mdx-code-block
<APITable>
```

| Syntax             | Description                                                                                  |
| ------------------ |:-------------------------------------------------------------------------------------------- |
| `as'program'`      | As above.                                                                                    |
| `atclone'…'`       | As above **plus** pass `--prefix=$ZPFX` to `./configure`, to set the installation directory. |
| `atpull'%atclone'` | As above.                                                                                    |
| `make`             | As above, but also run the `install` target.                                                 |
| `pick'src/vim'`    | as above, but for a different path `$ZPFX/bin/vim`.                                          |

```mdx-code-block
</APITable>
```

### <i class="fa-solid fa-palette"></i> LS_COLORS {#ls_colors}

A repository [trapd00r/LS_COLORS][trapd00r-ls_colors] provides a file with color definitions for GNU `ls` command, and also for [ogham/exa][ogham-exa]. Typically one does `eval $( dircolors -b $HOME/LS_COLORS)` to process this file and set the environment for `ls`. This means `dircolors` is run by every shell startup. It costs much time to create a fork and program, i.e., the `dircolors` binary needs to be loaded to obtain and process the color definitions. The following invocation solves this problem:

```shell showLineNumbers
zi ice atclone'dircolors -b LS_COLORS > clrs.zsh' \
  atpull'%atclone' pick"clrs.zsh" nocompile'!' \
  atload'zstyle ":completion:*" list-colors “${(s.:.)LS_COLORS}”'
zi light trapd00r/LS_COLORS
```

```mdx-code-block
<APITable>
```

| Syntax             | Description                                                                                 |
| ------------------ |:------------------------------------------------------------------------------------------- |
| `atclone'…'`       | Generate shell script, passing it to `eval`. More: [^1]                                     |
| `atpull'%atclone'` | Do the same at any update of the plugin. More: [^2]                                         |
| `pick"clrs.zsh"`   | Source the previously generated file `clrs.zsh`.                                            |
| `nocompile'!'`     | Invokes compilation **after** the `atclone'…'` and the [exclamation][] mark causes this.    |
| `atload'…'`        | Additionally sets up the Zsh completion to use the colors provided by the trapd00r package. |

```mdx-code-block
</APITable>
```

This way, except for the plugin installation and update, `dircolors` isn't run, just normal sourcing is done. The everyday sourced file, i.e. `clrs.zsh`, is being compiled to speed up the loading.

### <i class="fa-solid fa-folder-tree"></i> Direnv {#direnv}

The project [direnv/direnv][direnv-direnv] registers itself in the Z shell to modify the environment on directory change. This registration is most often done by `eval "$(direnv hook zsh)"` added to `.zshrc`.

```shell showLineNumbers
zi ice as"program" make'!' atclone'./direnv hook zsh > zhook.zsh' \
  atpull'%atclone' src"zhook.zsh"
zi light direnv/direnv
```

- `make'!'` – execute `make` before `atclone'…'` and before `atpull'…'` (see `make` above),
- `src'zhook.zsh'` – source file `zhook.zsh`.

In general, `direnv` works by hooking up to Zsh. The code that does this is provided by the program `direnv` (built by `make'…'`).

Above `atclone'…'` puts this code into file `zhook.zsh`, `src''` sources it. This way `direnv hook zsh` is executed only on clone and update, and Zsh starts faster.

#### <i class="fa-solid fa-wand-magic-sparkles"></i> Glance at the 'for' syntax {#glance-at-the-for-syntax}

The drawback of this standard procedure is that the `direnv` binary is run on every shell startup and significantly slows it down. Zi allows to solve this in the following way:

```shell showLineNumbers
zi as"program" make'!' atclone'./direnv hook zsh > zhook.zsh' \
  atpull'%atclone' pick"direnv" src"zhook.zsh" for \
    direnv/direnv
```

```mdx-code-block
<APITable>
```

| Syntax             | Description                                                                                                                    |
| ------------------ |:------------------------------------------------------------------------------------------------------------------------------ |
| `make'!'`          | Compile `direnv`, the exclamation mark means: run the `make` first, before `atclone'…'` and `atpull'…'` hooks.                 |
| `atclone'…'`       | As soon as the plugin is installed generate the registration code and save it to `zhook.zsh`, instead of passing it to `eval`. |
| `atpull'%atclone'` | The `atclone'…'` runs on **installation** while `atpull'…'` runs on **update** of the plugin.                                  |
| `src'zhook.zsh'`   | Load generated registration code                                                                                               |
| `pick'direnv'`     | Ensure `+x` permission on the binary                                                                                           |
| `as'program'`      | The plugin is a program, there's no main file to the source.                                                                   |

```mdx-code-block
</APITable>
```

In this method, the registered code is generated once on every installation or update, then sourced without running `direnv` itself. The project is also available as a binary [GitHub releases][gh-releases]. This distribution can be installed by:

```shell showLineNumbers
zi from"gh-r" as"program" mv"direnv* -> direnv" \
  atclone'./direnv hook zsh > zhook.zsh' atpull'%atclone' \
  pick"direnv" src="zhook.zsh" for \
    direnv/direnv
```

```mdx-code-block
<APITable>
```

| Syntax                     | Description                                                                |
| -------------------------- |:-------------------------------------------------------------------------- |
| `from'gh-r'`               | Install from `direnv` from GitHub Github releases.                         |
| `mv'direnv* -> direnv'` | After installation, rename `direnv.linux-386` or similar file to `direnv`. |
| `atclone'…'`, `atpull'…'`  | Same above example.                                                        |
| `pick'direnv'`             | Same above example.                                                        |
| `as'program'`              | Same above example.                                                        |

```mdx-code-block
</APITable>
```

## <i class="fas fa-microchip"></i> `extract'…'` {#extract}

A swiss-knife tool for unpacking all kinds of archives – the `extract'…'` ice. It works in two modes – automatic mode and fixed mode.

Automatic mode:

It is active if the ice is empty (or contains only flags). It works as follows:

1. At first, a recursive search for files of known [file extensions](#supported-file-formats) located not deeper than in a sub-directory is being performed. All such found files are then extracted.
   - The directory-level limit is to skip extraction of some helper archive files, which are typically located somewhere deeper in the directory tree.
2. **If** no such files will be found, then a recursive search for files of known archive **types** will be performed. This is done by running the `file` Unix command on each file in the plugin or snippet directory and then grepping the output for strings like `Zip`, `bzip2`, etc. All such discovered files are then extracted.
   - The directory-level requirement is imposed during this stage. The files located deeper than in a sub-directory are omitted.
3. If no archive files will be discovered then no action is being performed and also no warning message is being printed.

Fixed mode:

It is active when a filename is being passed as the `extract`'s argument, e.g.: `zi extract=archive.zip for z-shell/null`. Multiple files can be specified – separated by spaces. In this mode all and only the specified files are being extracted.

Filenames with spaces:

The filenames with spaces are supported when correctly passing such filenames to an `extract` with the non-breaking spaces for the original in-filename.

The non-breaking space is easy to type by pressing right <kbd>ALT</kbd> and the <kbd>SPACE</kbd>.

Flags:

The value of the ice can begin with two special characters:

1. Exclamation mark (`!`), i.e.: `extract='!…'` – it'll cause the files to be moved one directory level up upon unpacking,
2. Two exclamation marks (`!!`), i.e.: `extract='!!…'` – it'll cause the files to be moved two directory-level up upon unpacking,
3. Dash (`-`), i.e.: `extract'-…'` – it'll prevent removal of the archive after unpacking.
   - This flag allows comparing timestamps with the server in case of snippet-downloaded file – it will prevent unnecessary downloads during `zi update`, as the timestamp of the archive file on the disk will be first compared with the HTTP last-modification time header.

The flags can be combined in any order: `extract'!-'`.

## <i class="fas fa-gears"></i> `ziextract` {#ziextract}

Sometimes a more uncommon unpacking operation is needed. In such a case you can directly use the function that implements the ice – it is called `ziextract`.

It recognizes the following options:

1. `--auto` – runs the automatic extraction.
2. `--move` – performs the one-directory-level-up move of the files after unpacking.
3. `--move2` – performs the two-directory-level-up move of the files after unpacking.
4. `--norm` - prevents the archive file removal.
5. And also one option specific only to the function: `--nobkp`, which prevents clearing the plugin's directory before the extraction. – All files besides the archive are being moved into the `._backup` directory after extraction is done. - `extract` ice also skips creating the backup **if** more than one archive is found or given as the argument.

### <i class="fas fa-circle-info"></i> Supported file formats {#supported-file-formats}

Zip, rar, tar.gz, tar.bz2, tar.xz, tar.7z, tar, tgz, tbz2, gz, bz2, txz, xz, 7z, exe, deb, OS X (dmg).

## <i class="fas fa-microchip"></i> `from'…'` {#from}

To install and load a plugin whose repository is private - e.g: requires providing credentials to log in – use the `from'…'` ice in the following way:

```shell showLineNumbers
zi ice from"user@github.com"
zi load user/fsh-auto-themes
```

Current preset:

```mdx-code-block
<APITable>
```

| Ice name   | Domain name / URL                      |
| ---------- |:-------------------------------------- |
| ge         | gitee.com                              |
| gitee      | gitee.com                              |
| github     | github.com                             |
| gh         | github.com                             |
| gitlab     | gitlab.com                             |
| gl         | gitlab.com                             |
| notabug    | notabug.org                            |
| nb         | notabug.org                            |
| bitbucket  | bitbucket.org                          |
| bb         | bitbucket.org                          |
| github-rel | github.com/$remote_url_path/releases |
| gh-r       | github.com/$remote_url_path/releases |
| cygwin     | cygwin                                 |

```mdx-code-block
</APITable>
```

:::note

If the `from'…'` ice isn't one of the above tables, then **it is treated as a domain name** and inserted into the domain position into the `git clone` URL:

```shell
git clone https://{from-ice-contents}/user/plugin
```

In order to change the protocol, use the `proto'…'` ice.

:::

## <i class="fas fa-microchip"></i> `id-as'…'` {#id-as}

Load a plugin or snippet with a nickname with the `id-as'…'` ice-modifier. For example, one could try to load [docker/compose][docker-compose] from GitHub binary releases:

```shell showLineNumbers
zi ice as"program" from"gh-r" mv"docker-c* -> docker-compose"
zi light "docker/compose"
```

This registers the plugin under the ID `docker/compose`. Now suppose the user would want to also load a completion from the project's GitHub repository (not the binary release catalog) which is also available under the GitHub URL **…/docker/compose**. The two IDs, both being "docker/compose", will collide.

The solution to this problem – the `id-as'…'` (to be read as _identify-as_) ice to which this document is devoted: by using the `id-as'…'` ice the user can resolve the conflict by loading the completion under a kind of a _nickname_, for example under "_dc-complete_", by issuing the following commands:

```shell showLineNumbers
zi ice as"completion" id-as"dc-complete"
zi load docker/compose
```

The plugin (of the type `completion`) is now seen under ID `dc-complete`:

```shell showLineNumbers
zi list | grep -i dc-complete
dc-complete
```

Issuing `zi report dc-complete` will work as with regular command:

```shell showLineNumbers
zi report dc-complete

Plugin report for dc-complete
-------------------------------

Completions:
_docker-compose [enabled]
```

The same method applies to nickname snippets. For instance, use it to create handy IDs in place of long URLs:

```shell showLineNumbers
zi ice as"program" id-as"git-unique"
zi snippet https://github.com/Osse/git-scripts/blob/master/git-unique
```

The commands `zi update git-unique`, and `zi delete git-unique` will work as expected and e.g. `zi times` will show the _nickname_-ID `git-unique` instead of the long URL.

- `id-as'auto'`:

There's a special value to the `id-as'…'` ice – `auto`. It causes the nickname to be automatically set to the last component of the plugin name or snippet URL. For example:

```shell showLineNumbers
zi ice as"program" id-as"auto"
zi snippet https://github.com/Osse/git-scripts/blob/master/git-unique
```

will work the same as before, e.g: if the ice used was `id-as'git-unique'`. Will work as if id-as'zsh-autopair' was passed:

```shell showLineNumbers
zi ice wait lucid id-as"auto"
zi load hlissner/zsh-autopair
```

- empty `id-as'…'`:

An empty `id-as'…'` will work the same as `id-as'auto'` as if id-as'zsh-autopair' was passed, e.g:

```shell showLineNumbers
zi ice wait lucid id-as
zi load hlissner/zsh-autopair
```

## <i class="fas fa-microchip"></i> `wait'…'` {#wait}

:::note

Turbo mode, i.e. the `wait'…'` is ice that implements it - needs Zsh >= 5.3.

:::

```shell showLineNumbers
zi ice wait'0' # or just: zi ice wait
zi light wfxr/forgit
```

- waits for prompt,
- instantly ("0" seconds) after prompt loads given plugin.

```shell showLineNumbers
zi ice wait'[[ -n ${ZLAST_COMMANDS[(r)cras*]} ]]'
zi light z-shell/zi-crasis
```

- screencast that presents the feature:

<ImgShow height="390.78" width="970" img="/img/cast/svg/crasis_01.svg" alt="Crasis example screencast" />

- `$ZLAST_COMMANDS` is an array built by [F-Sy-H][z-shell-f-sy-h], it contains commands currently entered at prompt,
- `(r)` searches for an element that matches a given pattern (`cras*`) and returns it,
- `-n` means: not-empty, so it will be true when users enter "cras",
- after 1 second or less, Zi will detect that the `wait'…'` condition is true, and load the plugin, which provides command _crasis_,

```shell showLineNumbers
zi ice wait'[[ $PWD = */github || $PWD = */github/* ]]'
zi load unixorn/git-extra-commands
```

it waits until the user enters a `github` directory. Turbo mode also supports a suffix – the letter a, `b`, or `c`. The meaning is illustrated by the following example:

```shell showLineNumbers
zi ice wait"0b" as"command" pick"wd.sh" atinit"echo Firing 1" lucid
zi light mfaerevaag/wd
zi ice wait"0a" as"command" pick"wd.sh" atinit"echo Firing 2" lucid
zi light mfaerevaag/wd
```

will output:

```shell showLineNumbers
Firing 2
Firing 1
```

As can be seen, the second plugin has been loaded first. That's because there are now three sub-slots (the `a`, `b`, and `c`) into which the plugin/snippet loadings can be put. Plugins from the same time slot with suffix `a` will be loaded before plugins with suffix `b`, etc.

In other words, instead of `wait'1'`, you can enter `wait'1a'`, `wait'1b'`, and `wait'1c'` – this **imposes the loading order** of the **commands** regardless of actual execution time.

## <i class="fas fa-microchip"></i> `src'…'` `pick'…'` `multisrc'…'` {#src-pick-multisrc}

Normally `src'…'` can be used to specify the additional file to the source:

```shell showLineNumbers
zi ice pick'powerless.zsh' src'utilities.zsh'
zi light martinrotter/powerless
```

|  Syntax   | Description                                                                                                |
|:---------:|:---------------------------------------------------------------------------------------------------------- |
| `pick'…'` | Provide the main file to the source - like `*.sh`, otherwise alphabetically first matched file is sourced. |
| `src'…'`  | Provide a second file to the source - not a pattern - plain file name.                                     |

### The `svn` ice {#the-svn-ice}

However, via `atload'…'` ice one can provide a simple loop to source more files:

```shell showLineNumbers
zi ice svn pick'completion.zsh' \
  atload'local f; for f in git.zsh misc.zsh; do source $f done'
zi snippet OMZ::lib
```

|   Syntax    | Description                                                                                                                        |
|:-----------:|:---------------------------------------------------------------------------------------------------------------------------------- |
|    `svn`    | Use Subversion to clone `OMZ::lib` (the whole Oh-My-Zsh `lib/` directory). More [^3].                                              |
| `atload'…'` | Code isn't tracked and cannot be unloaded. The `atload'…'` is executed after loading main files `pick'…'` and `src'…'`. More [^4]. |

### The `multisrc'…'` ice {#the-multisrc-ice}

Loads **multiple** files enumerated with spaces as the separator (e.g. `multisrc'misc.zsh grep.zsh'`) and also using brace-expansion syntax (e.g. `multisrc'{misc,grep}.zsh')`. Example:

```shell showLineNumbers
zi ice svn pick'completion.zsh' \
  multisrc'git.zsh functions.zsh {history,grep}.zsh'
zi snippet OMZ::lib
```

All possible ways to use the `multisrc'…'` ice-modifier:

```shell
zi ice depth'1' multisrc='lib/{functions,misc}.zsh' pick'/dev/null'
zi load robbyrussell/oh-my-zsh
```

Can use patterns:

```shell showLineNumbers
zi ice svn multisrc'{funct*,misc}.zsh' pick'/dev/null'
zi snippet OMZ::lib
```

```shell showLineNumbers
zi ice svn multisrc'misc.zsh functions.zsh' pick'/dev/null'
zi snippet OMZ::lib
```

Will use the array's value at the moment of plugin load:

> This can matter when using turbo mode.

```shell showLineNumbers
array=({functions,misc}.zsh)
zi ice svn multisrc"\$array" pick'/dev/null'
zi snippet OMZ::lib
```

Compatible with KSH_ARRAYS option:

```shell showLineNumbers
array=({functions,misc}.zsh)
zi ice svn multisrc"${array[*]}" pick'/dev/null'
zi snippet OMZ::lib
```

Hack with Zi: the ice's contents are simply `eval`-uated like follows: eval "reply=($multisrc)".

So it might get handy on an occasion to pass code there, but first, you must close the paren and then don't forget to assign `reply`, and to provide a trailing opening paren. In the code be careful to not redefine any variable used internally by Zi – e.g.: `i` is safe:

```shell showLineNumbers
array=({functions,misc}.zsh)
zi ice svn multisrc'); local i; for i in $array; do reply+=( ${i/.zsh/.sh} ); done; ((1)' pick'/dev/null'
zi snippet OMZ::lib
```

Extended with the [for][for-syntax] syntax which can in some situations replace a typical `multisrc'…'` loading. The idea of this syntax is to source multiple snippets with a single command.

Instead of:

```shell showLineNumbers
zi ice multisrc'(functions|misc|completion).zsh'
zi snippet OMZ::lib
```

it's possible to write:

```shell showLineNumbers
zi for \
  OMZL::functions.zsh \
  OMZL::misc.zsh \
  OMZL::completion.zsh
```

which is somewhat easier on the eyes.

:::info Important Property

The multiple snippets loaded with the `for` syntax are being loaded _separately_, which means that they will not cause a longer keyboard blockage, which could have been noticeable – when loading in turbo mode.

:::

The Zi scheduler will distribute the work over time and will allow activation of the keyboard in between the snippets. The `multisrc'…'` way doesn't work this way – sourcing many files may cause a noticeable keyboard freeze (in turbo mode).

## <i class="fas fa-microchip"></i> `atclone'…'` `atpull'…'` `atinit'…'` `atload'…'` {#atclone-atpull-atinit-atload}

There are four code-receiving ice-modifiers: `atclone'…'`, `atpull'…'`, `atinit'…'`, `atload'…'`.

Their role is to **receive a portion of Zsh code and execute it in specific moments of the plugin life-cycle**.

|    Syntax    | Execution moment                                                |
|:------------:|:--------------------------------------------------------------- |
| `atclone'…'` | **after cloning** the associated plugin or snippet to the disk. |
| `atpull'…'`  | **after updating** the associated plugin or snippet.            |
| `atinit'…'`  | **before loading** of the associated plugin or snippet.         |
| `atload'…'`  | **after loading** of the associated plugin or snippet.          |

For convenience, you can use each of the ices multiple times in a single `zi ice …` invocation – all commands will run in the given order.

The `atpull'…'` ice recognizes a special value: `%atclone`, so the code looks: `atpull'%atclone'`. It causes the contents of the `atclone'…'` ice to be copied into the contents of the `atpull'…'` ice.

This is handy when the same tasks have to be performed on clone **and** on the update of plugin or snippet, like e.g.: in the [direnv example](#direnv).

### `atload'!…'` with exclamation mark preceded

The [wrap'…'](#wrap) The ice-modifier allows the track and unload of plugins that defer their initialization to a function and run later after sourcing the plugin's script – When the function call, the plugin is then fully initialized.

However, if the function is being called from the `atload'…'` ice, then the _exclamation mark_-preceded method can be used with `atload'…'` contents. The exclamation mark causes the effects of the execution of the code passed to `atload'…'` ice to be recorded.

### Use case for `atload'…'`

For example, in the following invocation:

```shell showLineNumbers
zi ice id-as'test' atload'!PATH+=:~/share'
zi load z-shell/null
```

the `$PATH` is being changed within `atload'…'` ice. Zi's tracking registers `$PATH` changes and withdraws them on the plugin unload and shows loading information:

```shell title="zi report test" showLineNumbers
Report for test plugin
----------------------
Source  (reporting enabled)

PATH elements added:
/home/sg/share
```

As it can be seen, the `atload'…'` code is being correctly tracked and can be unloaded & viewed. Below is the result of using the `unload'…'` subcommand to unload the `test` plugin:

```shell title="zi unload test" showLineNumbers
--- Unloading plugin: test ---
Removing PATH element /home/user/share
Unregistering plugin test
Plugin report saved to $LASTREPORT
```

The same example as in the [wrap'…'](#use-case-for-wrap) article, but using the _exclamation mark_-preceded `atload'…'` instead of `wrap'…'`:

Load when - `MYPROMPT == 4`

```shell showLineNumbers
zi ice load'![[ $MYPROMPT = 4 ]]' unload'![[ $MYPROMPT != 4 ]]' \
  atload'!source ~/.p10k.zsh; _p9k_precmd'
zi load romkatv/powerlevel10k
```

## <i class="fas fa-microchip"></i> `wrap'…'` {#wrap}

The `wrap' …'` ice-modifier allows extending the tracking (e.g.: the gathering of the report and unloading data) of a plugin beyond the moment of sourcing its main file(s). It works by wrapping the given functions with a tracking-enabling and disabling snippet of code. This is useful especially with prompts, as they very often do their initialization in the first call to their `precmd` [hook][hook-functions] function.

For example, [romkatv/powerlevel10k][romkatv-powerlevel10k] works this way. The ice takes a list of function names, with the elements separated by `;`:

```shell
zi ice wrap"func1;func2;…"
```

### Use case for `wrap'…'` {#use-case-for-wrap}

Therefore, to load and unload for the example powerlevel10k prompt in the fashion of [multiple prompts][multiple-prompts] article, the `precmd` function of the plugin – called `_p9k_precmd`, to get the name of the function do `echo $precmd_functions` after loading a theme, should be passed to `wrap'…'` ice.

Load when `MYPROMPT == 4`

```shell showLineNumbers
zi ice load'![[ $MYPROMPT = 4 ]]' unload'![[ $MYPROMPT != 4 ]]' \
  atload'source ~/.p10k.zsh; _p9k_precmd' wrap'_p9k_precmd'
zi load romkatv/powerlevel10k
```

This way the actions done during the first call to `_p9k_precmd()` will be normally recorded, which can be viewed in the report of the [romkatv/powerlevel10k][romkatv-powerlevel10k] theme:

```shell title="zi report romkatv/powerlevel10k" showLineNumbers
Report for romkatv/powerlevel10k plugin
---------------------------------------
Source powerlevel10k.zsh-theme (reporting enabled)
Autoload is-at-least with options -U -z

(…)

Note: === Starting to track function: _p9k_precmd ===
Zle -N p9k-orig-zle-line-finish _zsh_highlight_widget_zle-line-finish
Note: a new widget created via zle -N: p9k-orig-zle-line-finish
Zle -N -- zle-line-finish _p9k_wrapper__p9k_zle_line_finish
Autoload vcs_info with options -U -z
Zstyle :vcs_info:* check-for-changes true

(…)

Zstyle :vcs_info:* get-revision false
Autoload add-zsh-hook with options -U -z
Zle -F 22_gitstatus_process_response_POWERLEVEL9K
Autoload_gitstatus_cleanup_15877_0_16212/docs/guides/syntax/wrap
Zle -N -- zle-line-pre-redraw _p9k_wrapper__p9k_zle_line_pre_redraw
Note: a new widget created via zle -N: zle-line-pre-redraw
Zle -N -- zle-keymap-select _p9k_wrapper__p9k_zle_keymap_select
Note: === Ended tracking function:_p9k_precmd ===

Functions created:
+vi-git-aheadbehind                      +vi-git-remotebranch

(…)
```

Summary of `wrap'…'`:

As it can be seen, the creation of four additional Zle-widgets has been recorded - `Zle -N …` lines. They will be properly deleted/restored on the plugin unload with `MYPROMPT=3` as an example and the shell state will be clean, ready to load a new prompt.

<!-- end-of-file -->
<!-- footnotes -->



<!-- links -->



<!-- external -->

[^1]: Save it to a file. The `atclone'…'` is being run on the **installation** while the `atpull'…'` hook is being run on an **update** of the [**trapd00r/LS_COLORS**][trapd00r-ls_colors] plugin.
[^2]: The `%atclone` is just a special string that denotes the `atclone'…'` hook and is copied onto the `atpull'…'` hook.
[^3]: Note that `atload'…'` uses apostrophes, not double quotes, to put `$f` into the string, `atload'…'`'s code is automatically being run **within the snippet's or plugin's directory**.
[^4]: Unless you load a plugin (not a snippet) with `zi load …` and prepend the value of the ice with an exclamation mark. Example: `atload'!local f; for …'`.

[for-syntax]: /docs/guides/syntax/for

[for-syntax]: /docs/guides/syntax/for
[ice-mods]: /docs/guides/syntax/ice-modifiers
[exclamation]: /search?q=exclamation+mark
[zpfx]: /docs/guides/customization#$ZPFX
[multiple-prompts]: /docs/guides/customization#multiple-prompts

[trapd00r-ls_colors]: https://github.com/trapd00r/LS_COLORS

[trapd00r-ls_colors]: https://github.com/trapd00r/LS_COLORS
[ogham-exa]: https://github.com/ogham/exa
[direnv-direnv]: https://github.com/direnv/direnv
[gh-releases]: https://github.com/direnv/direnv/releases/
[zi-vim-syntax]: https://github.com/z-shell/zi-vim-syntax
[docker-compose]: https://github.com/docker/compose
[z-shell-f-sy-h]: https://github.com/z-shell/F-Sy-H
[hook-functions]: https://zsh.sourceforge.net/Doc/Release/Functions.html#Hook-Functions
[romkatv-powerlevel10k]: https://github.com/romkatv/powerlevel10k
