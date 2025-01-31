---
id: default-ice
title: "🌀 Default Ice"
image: /img/png/theme/z/320x320.png
description: Annex - Default Ice documentation
keywords:
  - annex
  - zannex
  - default-ice
---

<!-- @format -->

import Tabs from '@theme/Tabs'; import TabItem from '@theme/TabItem'; import Link from '@docusaurus/Link';

An annex delivers the capability to set **default ices** for the next `zi` command, e.g:

set default-ices:

```shell
zi default-ice lucid from"gh-r"
```

this will download from GitHub releases (gh-r) and also use the lucid ice by default:

```shell showLineNumbers
zi wait for \
  sbin        junegunn/fzf-bin \
  sbin"**/pk" peco/peco
```

:::caution

The `wait` ice cannot be made default by using this subcommand.

:::

## `default-ice` {#default-ice}

An annex provides a subcommand – `default-ice` which has the following synopsis:

```shell showLineNumbers
— default-ice [ -s | -c | -g | -t | -q | -h ]

 [ -s ] — Show currently set default ices
 [ -c ] — Reset default ices
 [ -g ] — Return current ices in REPLAY hash
 [ -t ] — Show statistics
 [ -q ] — Hide all messages
 [ -h ] — This message
```

## Install default-ice

:::info Source

- <Link className="github-link" href="https://github.com/z-shell/z-a-default-ice">z-shell/z-a-default-ice</Link>

:::

<Tabs>
  <TabItem value="default" label="Default" default>

Add the following snippet in the `.zshrc` file:

```shell
zi light z-shell/z-a-default-ice
```

  </TabItem>
</Tabs>

This will register the [default-ice](#default-ice) subcommand.
