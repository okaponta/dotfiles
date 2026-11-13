# dotfiles

macOS（Apple Silicon）用の設定ファイル。管理には [dotbot](https://github.com/anishathalye/dotbot) を使う（symlink方式）。

## 何がどこにあるか

| ファイル | リンク先 | 役割 |
|---|---|---|
| `zsh/zshenv` | `~/.zshenv` | cargo env |
| `zsh/zprofile` | `~/.zprofile` | brew shellenv、mise shims（GUIアプリ向け）、GOPATH、`~/.local/bin` |
| `zsh/zshrc` | `~/.zshrc` | プロンプト、alias、`mise activate` |
| `git/gitconfig` | `~/.gitconfig` | git設定（メールアドレスは `~/.gitconfig.local` に分ける） |
| `git/ignore` | `~/.config/git/ignore` | グローバルgitignore |
| `mise/config.toml` | `~/.config/mise/config.toml` | 言語ランタイムとCLIツール |
| `Brewfile` | — | brewで入れるもの（CLIとGUIアプリ） |

バージョン管理の役割分担：

- **mise**：言語ランタイム（python, node, go, ruby, java）と、バージョンを固定したいツール（uv, yarn, gradle, hugo, ast-grep, ruby-lsp）
- **brew**：GUIアプリ、Cライブラリ、バージョンを気にしない汎用CLI
- **rustup**：Rust

## 新しいMacでのセットアップ

1. Command Line Toolsを入れる
   ```bash
   xcode-select --install
   ```
2. [Homebrew](https://brew.sh/) を入れる
3. このリポジトリをcloneしてインストールする（Brewfileとmiseのツールもまとめて入る）
   ```bash
   git clone --recursive https://github.com/okaponta/dotfiles.git ~/repos/dotfiles
   cd ~/repos/dotfiles && ./install
   ```
4. rustupを入れる（brewでもmiseでもなく、公式インストーラで入れる）
   ```bash
   curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
   ```
5. Claude Codeを入れる（ネイティブインストーラ。`~/.local/bin/claude` に入る）
   ```bash
   curl -fsSL https://claude.ai/install.sh | bash
   ```
6. ローカル専用ファイルを手で作る（リポジトリには入れない）
   - `~/.gitconfig.local`
     ```ini
     [user]
         email = <GitHubのnoreplyアドレス>
     ```
   - `~/.zshrc.local`：APIキーなどの秘密情報を書く

## 運用

- **brewで入れるもの**：Brewfileに1行書いて `brew bundle`。消すときは行を消して `brew bundle cleanup --force`
- **miseで入れるもの**：`mise use -g <tool>@<version>`（`mise/config.toml` に書き込まれる）
- **プロジェクト固有のバージョン**：そのリポジトリに `mise.toml` を置く。秘密情報は `mise.local.toml` に分ける
- **ずれの確認**：`brew bundle check` / `mise ls`
