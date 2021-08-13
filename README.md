<p align="center">
  <img src="https://user-images.githubusercontent.com/6705160/118490159-f064bb00-b71d-11eb-883e-4affbd020074.png" alt="nvim-lsp-installer" width="60%" />
</p>

## About

Semi-opinionated companion plugin for [nvim-lspconfig](https://github.com/neovim/nvim-lspconfig).
It comes with all batteries included, or at least to the extent possible. On top of just providing commands for
installing & uninsalling LSP servers, it:

-   provides configurations for servers that aren't supported by nvim-lspconfig (`eslint`)
-   provides extra APIs for non-standard LSP functionalities, for example `_typescript.applyRenameFile`
-   has support for a variety of different install methods (e.g., [google/zx](https://github.com/google/zx))
-   common install tasks are abstracted behind Lua APIs
-   provides adapters that offer out-of-box integrations with other plugins

Inspired by [nvim-lspinstall](https://github.com/kabouzeid/nvim-lspinstall).

## Installation

Some install scripts are written in [google/zx](https://github.com/google/zx) and will require a [Node.js LTS](https://nodejs.org/) runtime to be installed.

### vim-plug

```vim
Plug 'neovim/nvim-lspconfig'
Plug 'williamboman/nvim-lsp-installer'
```

### [Packer](https://github.com/wbthomason/packer.nvim)

```lua
use {
    'neovim/nvim-lsp-config',
    'williamboman/nvim-lsp-installer',
}
```

## Usage

### Commands

-   `:LspInstall <language>` - installs/reinstalls a language server
-   `:LspUninstall <language>` - uninstalls a language server
-   `:LspUninstallAll` - uninstalls all language servers
-   `:LspPrintInstalled` - prints all installed language servers

### Setup

```lua
local lsp_installer = require'nvim-lsp-installer'

function common_on_attach(client, bufnr)
    -- setup buffer keymaps etc.
end

local installed_servers = lsp_installer.get_installed_servers()

for _, server in pairs(installed_servers) do
    opts = {
        on_attach = common_on_attach,
    }

    -- (optional) Customize the options passed to the server
    -- if server.name == "tsserver" then
    --     opts.root_dir = function() ... end
    -- end

    server:setup(opts)
end
```

For more advanced use cases you may also interact with more APIs nvim-lsp-installer has to offer, for example the following (refer to `:help nvim-lsp-installer` for more docs):

```lua
local lsp_installer = require'nvim-lsp-installer'

local ok, rust_analyzer = lsp_installer.get_server("rust_analyzer")
if ok then
    if not rust_analyzer:is_installed() then
        rust_analyzer:install()
    end
end
```

## Available LSPs

| Language     | Server name              |
| ------------ | ------------------------ |
| Angular      | `angularls`              |
| Ansible      | `ansiblels`              |
| Bash         | `bashls`                 |
| C++          | `clangd`                 |
| Clojure      | `clojure_lsp`            |
| CMake        | `cmake`                  |
| CSS          | `cssls`                  |
| Deno         | `denols`                 |
| Docker       | `dockerls`               |
| Elixir       | `elixirls`               |
| Elm          | `elmls`                  |
| Ember        | `ember`                  |
| ESLint       | `eslintls`               |
| Fortran      | `fortls`                 |
| Go           | `gopls`                  |
| GraphQL      | `graphql`                |
| Groovy       | `groovyls`               |
| Haskell      | `hls`                    |
| HTML         | `html`                   |
| PHP          | `intelephense`           |
| Jedi         | `jedi_language_server`   |
| JSON         | `jsonls`                 |
| Kotlin       | `kotlin_language_server` |
| C#           | `omnisharp`              |
| PureScript   | `purescriptls`           |
| Python       | `pylsp`                  |
| Python       | `pyright`                |
| Rome         | `rome`                   |
| Rust         | `rust_analyzer`          |
| Ruby         | `solargraph`             |
| SQL          | `sqlls`                  |
| SQL          | `sqls`                   |
| Lua          | `sumneko_lua`            |
| Svelte       | `svelte`                 |
| Tailwind CSS | `tailwindcss`            |
| Terraform    | `terraformls`            |
| LaTeX        | `texlab`                 |
| TypeScript   | `tsserver`               |
| VimL         | `vimls`                  |
| Vue          | `vuels`                  |
| YAML         | `yamlls`                 |

## Extras

### tsserver

The `tsserver` language server comes with the following extras:

-   `rename_file(old, new)` Tells the language server that a file was renamed. Useful when refactoring. Refer to the [adapters section](#adapters) to find plugin integrations that automatically executes this for you.

    Usage:

```lua
require'nvim-lsp-installer.extras.tsserver'.rename_file(old, new)
```

-   `organize_imports(bufname)` Organizes the imports of a file. `bufname` is optional, will default to current buffer.

    Usage:

```lua
require'nvim-lsp-installer.extras.tsserver'.organize_imports(bufname)
```

## Adapters

Make sure to only attempt connecting adapters once the plugin(s) involved have been loaded.

### [kyazdani42/nvim-tree.lua](https://github.com/kyazdani42/nvim-tree.lua)

```lua
require'nvim-lsp-installer.adapters.nvim-tree'.connect()
```

Supported capabilities:

-   `_typescript.applyRenameFile`. Automatically executes the rename file client request when renaming a node.

## Logo

Illustrations in the logo are derived from [@Kaligule](https://schauderbasis.de/)'s "Robots" collection.

## Roadmap

-   Managed versioning of installed servers
-   Command (and corresponding Lua API) to update outdated servers (e.g., `:LspUpdate {server}`)
-   Cross-platform CI for all server installers
