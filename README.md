# fold-preview.nvim

This plugin allows you to preview close folds, without opening them.

https://user-images.githubusercontent.com/13056013/148261501-56677c8f-24a7-4c45-b008-8c1863bf06e8.mp4

## Table of contents

<!-- vim-markdown-toc GFM -->

* [Instalation and setup](#instalation-and-setup)
* [Configuration](#configuration)
    * [`default_keybindings`](#default_keybindings)
    * [`border`](#border)
* [Keybindings](#keybindings)
    * [Custom keybindings](#custom-keybindings)
        * [Main functions](#main-functions)
        * [Mapping functions](#mapping-functions)
* [Highlight](#highlight)

<!-- vim-markdown-toc -->

## Instalation and setup

To install and setup with [packer](https://github.com/wbthomason/packer.nvim)
use this snippet:

```lua
use { 'anuvyklack/fold-preview.nvim',
   requires = 'anuvyklack/keymap-amend.nvim',
   config = function()
      require('fold-preview').setup()
   end
}
```

## Configuration

To configure the plugin, you need to pass options into `setup()` function inside
table. Below are available options.

### `default_keybindings`
`boolean`   (default: `true`)

Set to `false` to disable default keybindings.

### `border`
`"none" | "single" | "double" | "rounded" | "solid" | "shadow" | string[]`  
default: `{ ' ', '', ' ', ' ', ' ', ' ', ' ', ' ' }`

See: `:help nvim_open_win()`

## Keybindings

I personally don't want to learn a new key combination to preview folds.
So I tried to create something that would feel natural.

By default `h` and `l` keys are used.  On first press of `h` key, when cursor is
on a closed fold, the preview will be shown.  On second press the preview will
be closed and fold will be opened.  When preview is opened, the `l` key will
close it and open fold.  In all other cases theese keys will work as usual.

A preview window also will be closed on any cursor move, changing mode, or
buffer leaving.

### Custom keybindings

#### Main functions

There three main functions in the `require('fold-preview')` module:

- `show_preview()`  
  Open preview window if cursor is on a closed fold.

- `close_preview()` | `nil`  
  Close preview if opened, other way this key will be `nil`, so you need to
  check availability manually.

- `toggle_preview()`  
  Close preview if opened, else if cursor is on a closed fold — show preview.

#### Mapping functions

Also, there are several special functions which allow you to create an interface
similar to the default one.  They are contains in `mapping` table:
```lua
require('fold-preview').mapping
```

These functions meant to be used with
[keymap-amend.nvim](https://github.com/anuvyklack/keymap-amend.nvim) plugin.
Read its documentation to find out what is `original` in next functions
signatures.

- `show_close_preview_open_fold(original)`  
  Show preview when cursor is inside closed fold.  If preview window is already
  opened, close it and open fold.  If no closed fold under the cursor, execute
  original mapping.

- `close_preview_open_fold(original)`  
  If cursor is on closed fold — open it. If preview is opened, it will be
  closed.  Otherway execute original mapping.

- `close_preview(original)`  
  Close preview (if opened) and execute original mapping.

- `close_preview_without_defer(original)`  
  Close preview immediately: without very small defer which is added in all
  other functions to avoid annoying screen flickering during fold opening. And
  execute original mapping.
  This function is for when you want to close fold preview without opening fold.

Here are original keybindings:

```lua
local keymap = vim.keymap
keymap.amend = require('keymap-amend')
local map = require('fold-preview').mapping

keymap.amend('n', 'h',  map.show_close_preview_open_fold)
keymap.amend('n', 'l',  map.close_preview_open_fold)
keymap.amend('n', 'zo', map.close_preview)
keymap.amend('n', 'zO', map.close_preview)
keymap.amend('n', 'zc', map.close_preview_without_defer)
keymap.amend('n', 'zR', map.close_preview)
keymap.amend('n', 'zM', map.close_preview_without_defer)
```

## Highlight

This plugin defines two highlight groups to customize the preview window:

- `FloatPreview` — linked to `NormalFloat` by default;
- `FloatPreviewBorder` — linked to `FloatBorder` by default.

