+++
title = "Neovim setup by NvChad in 2023"
slug = "nvchad-settings"
date = "2023-05-20"
author = "soomtong"
cover = "https://nvchad.com/news/v2.0.webp"
tags = ["editor", "neovim"]
keywords = ["nvchad", "neovim"]
description = "Obstinated Nvchad plugin settings but not complicated for Neovim"
showFullContent = false
readingTime = true
+++

## NvChad

NvChad, hosted by siduck[^1], is an impressive neovim enhancement suite designed to provide a seamless experience without the need for complex configurations.[^2]

It proudly boasts a `Blazing fast Neovim config providing solid defaults and a beautiful UI` and it lives up to this claim. I have personally used my own custom[^3] neovim and lunarvim setups, both of which are fast, but none can match the sheer beauty of NvChad.

What sets NvChad apart is its ability to provide custom UI improvements for a editor's frame, even live in the terminal environment.

If you're interested, you can find videos showcasing NvChad on the maker's channel on YouTube.

{{< youtube IljDD4cjgKc >}}

- https://www.youtube.com/playlist?list=PLYVQrj2EVSUIT3N0QuiwWnGfzue1mtMNM

### Vim and Neovim

Vim, an abbreviation for Vi Improved, is an enhanced version of the original Vi editor. It was first released to the public in 1991.

The original Vi editor was developed by [Bill Joy](https://en.wikipedia.org/wiki/Bill_Joy) and gained widespread adoption as the editor for most UNIX systems. [Bram Moolenaar](https://en.wikipedia.org/wiki/Bram_Moolenaar) initially began with Vi Imitation, and Vim has since evolved into one of the most esteemed editors.

Presently, Vim is at version 8.x and continues to be widely used. In addition, there are other notable editors gaining popularity, such as [Neovim](https://neovim.io), a modernized version of Vim, and [Helix](https://helix-editor.com) Editor, a post-modern alternative.

Neovim offers the advantage of being able to use both the traditional Vim plugins written in Vimscript and new plugins developed with the Lua programming language.

Comparing Vimscript and Lua can be challenging as they have different characteristics and syntax. However, many developers prefer Lua for creating plugins and customizing their editor environment due to its flexibility and ease of use.

Many Vim users now prefer Neovim due to the power of Lua-based plugins. However, classic Vimscript-based plugins still have value and are widely used. Rewriting or replacing existing Vimscript plugins solely for the sake of adopting Lua may not always be necessary or practical. Developers often focus on creating new Lua plugins or gradually migrating existing ones based on specific needs or significant improvements. The compatibility between Neovim and Vimscript ensures access to a vast plugin ecosystem for Neovim users, while Lua offers modern development capabilities for new enhancements. This balance allows users to benefit from the strengths of both languages.

In building a custom environment based on NvChad, we take advantage of Neovim's compatibility with Vimscript-based plugins. This allows us to incorporate a combination of Vimscript and Lua-based plugins into our setup. In the following sections, I will explain the process of composing this custom environment, highlighting the integration of selected Vimscript-based plugins along with other relevant configurations.

### Astronvim, Lunarvim, Nvchad

In the Neovim ecosystem, notable options include `AstroNvim`, `LunarVim`, and `NvChad`.

- [AstroNvim](https://astronvim.com) is a comprehensive choice suitable for new users looking to switch from Vim to Neovim, offering extensive coverage for text and code editing tasks.

- [LunarVim](https://www.lunarvim.org) is popular due to its maintainer's YouTube show, providing step-by-step guidance for building a setup from scratch. However, I used LunarVim before but I require customized keybindings for some features eg. tab switching.

- Personally, I prefer [NvChad](https://nvchad.com) as my setup. It offers a custom UI framework and its key mappings align well with my preferences.

Consider these options and their specific features to select the one that best meets your requirements and aligns with your preferences.

I recommend reading this article below for more information to consider when choosing `NvChad`.

## Install and basic configurations

To install NvChad, you can follow the installation process outlined on the official website. However, if you have been using Neovim previously, it is advisable to perform certain initialization steps.

For macOS, the initialization process involves deleting specific folders. By deleting the `nvim` folder inside the `.config` directory and the `share/nvim` folder inside `.local`, you can consider all the configuration files of Neovim to be removed. After completing these steps, Neovim should run properly when executed.

The folders that need to be deleted are as follows:

```perl
drwxr-xr-x 26 Feb  2022 karabiner
drwxr-xr-x  9 Nov  2022 lunarvim
drwxr-xr-x 20 May 13:25 chezmoi
drwxr-xr-x 20 May 15:07 nvim
~/.local/share $ rm -rf nvim
```
```perl
drwxr-xr-x 26 Feb  2022 chezmoi
drwxr-xr-x 26 Feb  2022 iterm2
drwxr-xr-x 14 Oct  2022 lf
drwxr-xr-x 17 Feb 23:32 helix
drwxr-xr-x 20 May 13:25 lvim
drwxr-xr-x 20 May 14:12 nvim
 ~/.config $ rm -rf nvim
```

Please note that these instructions are based on macOS. If you are using a different operating system, the folder paths may vary.

Afterward, you can download the NvChad settings for Neovim. For detailed instructions, please refer to the official website.

```perl
$ git clone https://github.com/NvChad/NvChad ~/.config/nvim --depth 1 && nvim
Cloning into '/Users/pa/.config/nvim'...
remote: Enumerating objects: 37, done.
remote: Counting objects: 100% (37/37), done.
remote: Compressing objects: 100% (36/36), done.
remote: Total 37 (delta 0), reused 14 (delta 0), pack-reused 0
Receiving objects: 100% (37/37), 34.02 KiB | 6.80 MiB/s, done.
```

Once NvChad settings are installed and Neovim is running, you will be prompted to install the plugins used by NvChad through its plugin manager. Along with the downloaded configuration files, you will also be asked if you want to download a sample of custom settings.

```perl
Do you want to install example custom config? (y/N) : y
```

If you have limited experience with plugin settings, it is recommended to download and use this sample as it will provide additional guidance. It is likely that the target audience reading this text may not be familiar with creating their own custom setups.

Please note that these instructions assume a lack of familiarity with customizing settings, and the sample setting serves as a helpful resource in such cases.

### Custom folder

NvChad offers both a core provided by `siduck` and custom settings that users can tailor to their needs. The custom settings are gitignored, allowing users to manage their own configuration files independently of the core.

While I am using `chezmoi` for configuration management, it is generally sufficient to manage the settings simply with Git.

In any case, the custom folder is structured as follows:

```c
# core files and custom folder location
├── LICENSE
├── init.lua
├── lazy-lock.json
└── lua
    ├── core: Contains core Lua modules for NvChad.
    ├── custom: Holds user-specific Lua configurations and customizations.
    └── plugins: Contains Lua configurations for specific plugins used in NvChad.
`````

Unlike Vim, Neovim supports the Lua programming language. It is customary to place the Lua modules used by Neovim in the `lua` folder. In the NvChad configuration environment, the `lua` folder is divided into three subfolders: `core`, `custom`, and `plugins`.

It is important to avoid modifying the code within the `core` or `plugins` folders if possible. These folders should be left intact for future updates to NvChad.

The `custom` folder consists of the following components:

```c
[ 288]  custom
├── [ 179]  README.md
├── [ 492]  chadrc.lua
├── [ 160]  configs
│   ├── [ 469]  lspconfig.lua
│   ├── [ 481]  null-ls.lua
│   └── [ 829]  overrides.lua
├── [ 394]  highlights.lua
├── [ 578]  init.lua
├── [3.4K]  mappings.lua
└── [2.1K]  plugins.lua

2 directories, 9 files
```

Now let's add some plugins.

## Custom settings

Here is a basic guideline:

Configuration related to NvChad is done through the `chadrc.lua` file. Other Neovim settings are handled in the `init.lua` file.

To add plugins, you can modify the `plugins.lua` file, and for key mappings, you can modify the `mappings.lua` file.

Therefore, in this guide, there will be some modifications to the `chadrc.lua` and `init.lua` files, but most of the changes will be made in the `plugins.lua` and `mappings.lua` files.

In the `chadrc.lua` file, we can configure our preferred theme set. NvChad provides a selection of themes, despite being a terminal editor, including both light and dark themes.

![](/posts/images/neovim-nvchad-theme-switcher.png)

I am currently using the Penumbra theme set. This theme is included within NvChad by default.

```js
-- in 'custom/chadrc.lua' file

M.ui = {
  theme = "penumbra_dark",
  theme_toggle = { "penumbra_dark", "penumbra_light" },
-- ...
}
```

Additionally, there is a process of adding key mapping settings to the `init.lua` file.

Although key mappings are typically done in the `mappings.lua` file, there are certain cases where specific key mappings need to be added in a different style within the `init.lua` file.

By adding the following code, you can implement key mappings according to your preferences:

```js
-- in 'custom/init.lua' file

-- Auto resize panes when resizing nvim window
-- ...

vim.opt.list = true

for i = 1, 9, 1 do
  -- Move to desired buffer line using Alt + 1-9 keys
  vim.keymap.set("n", string.format("<A-%s>", i), function()
    vim.api.nvim_set_current_buf(vim.t.bufs[i])
  end, { desc = "which_key_ignore" })
  -- Move to desired tab group instantly using Leader + 1-9 keys
  vim.keymap.set("n", string.format("<leader>%s", i), function()
    vim.api.nvim_set_current_tabpage(i)
  end, { desc = "which_key_ignore" })
end

```

With these mappings, you can use the `Alt + 1-9` keys to navigate to the desired buffer line, and the `Leader + 1-9` keys to instantly navigate to the desired tab group.

NvChad implements the concepts of tabs and buffers in a way that aligns with modern editor conventions, which may differ from the original features of Vim. This adaptability to modern practices is an advantage that sets NvChad apart from other custom setups for Neovim.

### Additional plugin packages

If you have downloaded the default custom settings offered during the initial NvChad installation, you can find a `plugins.lua` file that includes some basic plugin configurations. You can add your own preferred plugin combinations based on this setup.

```js
-- in 'custom/plugins.lua' file

local plugins = {

  -- Override plugin definition options

  {
    "neovim/nvim-lspconfig",
    dependencies = {
      -- format & linting

  -- ...
  -- additional plugin here...
```

Here are the plugins I have been using along with the default setup:

- "kylechui/nvim-surround": Lua version of the beloved Vim Surround plugin, providing text surround functionality.
- "rlane/pounce.nvim": An EasyMotion-like plugin with personal preferences incorporated.
- "mg979/vim-visual-multi": Enables multiple cursors for changing text, useful for non-LSP related tasks.
- "terryma/vim-expand-region": Provides an easier way to select regions of text compared to `viw` or `p`.
- "t9md/vim-quickhl": Highlights selected words, useful for on-site code reviews.
- "cappyzawa/trim.nvim": Handy for general text manipulation tasks.

With these plugins, you can achieve a fairly straightforward combination of NvChad's default setup and your desired configuration.

#### nvim-surround

```js
-- in 'custom/plugins.lua' file
  {
    "kylechui/nvim-surround",
    version = "*", -- Use for stability; omit to use `main` branch for the latest features
    event = "VeryLazy",
    config = function()
      require("nvim-surround").setup()
    end,
  },
```

If you don't have any specific customizations, you can simply use the default key mappings provided by plugins like Vim Surround.

#### pounce.nvim

```js
-- in 'custom/plugins.lua' file
  {
    "rlane/pounce.nvim",
    lazy = false,
  },
```

For the "Pounce" plugin, we'll need to configure separate key mappings. The official guide suggests using `s` and `S` for the mappings, and we can follow that recommendation.

#### vim-visual-multi

```js
-- in 'custom/plugins.lua' file
  {
    "mg979/vim-visual-multi",
    lazy = false,
  },
```

#### vim-expand-region

```js
-- in 'custom/plugins.lua' file
  {
    "terryma/vim-expand-region",
    lazy = false,
    config = function()
      vim.keymap.set("v", "v", "<Plug>(expand_region_expand)", { remap = true })
      vim.keymap.set("v", "V", "<Plug>(expand_region_shrink)", { remap = true })
    end,
  },
```

This plugin, which is configured as a vim script, performs a key binding during the plugin load process as I have not yet figured out how to handle this in a separate custom/mappings file.

#### vim-quickhl

```js
-- in 'custom/plugins.lua' file
  {
    "t9md/vim-quickhl",
    lazy = false,
  },
```

While this plugin is also configured as Vim script, we can still configure the key bindings using NvChad's mappings system. I will explain how to do it in the following section.

#### trim.nvim

```js
-- in 'custom/plugins.lua' file
  {
    "cappyzawa/trim.nvim",
    lazy = false,
    config = function()
      require("trim").setup {}
    end,
  },
```

The activation key for the trimming plugin will be covered in the next chapter. Let's explore it there.

### Additional key mappings

First, let me introduce the key mappings I use, along with the corresponding key bindings for the installed plugins.

I use `ctrl+p`/`leader+e` for file navigation. The reason is that `ctrl+n` is reserved for other plugins.

```js
M.disabled = {
  n = {
    ["<C-n>"] = "",
  },
}
```

Additionally, I have customized the default environment with the following settings.

Some key combinations are borrowed from Emacs. I highly recommend them as they are powerful.

```js
M.general = {
  n = {
    -- [";"] = { ":", "enter command mode", opts = { nowait = true } },
    ["gh"] = { "<Home>", "Move cursor beginning of line" },
    ["gl"] = { "<End>", "Move cursor end of line" },
    ["<leader>."] = { "<cmd> cd %:p:h<CR>:pwd <CR>", "Change workspace onto current location" },
    ["<leader>qq"] = { "<cmd> quitall <CR>", "Quit all" },
    ["<leader>gx"] = { "<cmd> silent :%bd<bar>e#<bar>'\" <CR>", "Close all buffer except itself" },
    ["<leader>gT"] = { "<cmd> Trim <CR>", "Trim test in buffer" },
    ["<leader>fs"] = { "<cmd> :wa <CR>", "Save all file buffers" },
    ["<leader>tt"] = { "<cmd> :tabnew <CR>", "New tab space" },
    ["<leader>tp"] = { "<cmd> :tabprev <CR>", "Go prev tab space" },
    ["<leader>tn"] = { "<cmd> :tabnext <CR>", "Go next tab space" },
    ["<A-l>"] = {
      function()
        vim.call("quickhl#manual#this", "n")
      end,
      "Quick hilight this word",
    },
    ["<C-A-l>"] = {
      function()
        vim.call "quickhl#cword#toggle"
      end,
      "Quick hilight toggle this word",
    },
    ["<A-L>"] = {
      function()
        vim.call "quickhl#manual#reset"
      end,
      "Quick hilight reset",
    },
    ["]l"] = {
      function()
        vim.call("quickhl#manual#go_to_next", "s")
      end,
      "Quick hilight move to next",
    },
    ["[l"] = {
      function()
        vim.call("quickhl#manual#go_to_prev", "s")
      end,
      "Quick hilight move to prev",
    },
  },
  i = {
    ["<C-a>"] = { "<ESC>^i", "Move cursor beginning of line" },
    ["<C-e>"] = { "<End>", "Move cursor end of line" },
    ["<C-b>"] = { "<Left>", "Move cursor left" },
    ["<C-f>"] = { "<Right>", "Move cursor right" },
    ["<C-k>"] = { "<C-o>D", "Kill till to end of line" },
    ["<C-s>"] = { "<C-o><cmd> w <CR>", "save file" },
  },
}
```

Next, I change the key mapping of open nvimtree to `ctrl+p`.
And I use the `leader+ss` key to search for the current word under the cursor.

```js
M.nvimtree = {
  n = {
    ["<C-p>"] = { "<cmd> NvimTreeToggle <CR>", "toggle nvimtree" },
  },
}

M.telescope = {
  n = {
    -- find
    ["<leader>ss"] = { "<cmd> Telescope grep_string <cr>", "Search under your cursor" },
  },
}
```

I have assigned keys for the added pounce plugin as well.

```js
M.pounce = {
  n = {
    ["s"] = { "<cmd> Pounce <CR>", "Pounce in buffer" },
    ["S"] = { "<cmd> PounceRepeat <CR>", "Pounce repeat" },
  },
  x = {
    ["s"] = { "<cmd> Pounce <cr>", "Pounce in buffer" },
  },
}
```

## In conclusion

NvChad extends Neovim in a concise and clutter-free manner, allowing users to make minimal modifications to the familiar keybindings of Vim. It offers a visually pleasing experience, surpassing other Vim editors in aesthetics and bringing joy while typing.

After using NvChad for a few weeks, I have fully embraced it, and I believe that the combination of Neovim and NvChad is the way to go for a terminal editor.


[^1]: https://github.com/siduck
[^2]: https://nvchad.com/
[^3]: https://gist.github.com/soomtong/a25f272d97ce763dfc0d2a95ee2c1efa
