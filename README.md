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

### アップデート

まず何が古いかを確認し、それから上げる。

| 対象 | 確認 | 更新 | メモ |
|---|---|---|---|
| brewのCLI | `brew outdated --formula` | `brew upgrade --formula` | mise本体もここで上がる |
| brewのGUIアプリ | `brew outdated --cask` | `brew upgrade --cask` | 起動中のアプリは終了されることがある。自動更新するアプリ（docker-desktopなど）は対象外なので、含めたいなら `--greedy` を付ける |
| miseのツール（範囲内） | `mise outdated` | `mise upgrade` | `config.toml` の範囲内で最新にする（`node = "24"` なら24系の最新）。`config.toml` は変わらない |
| miseのツール（メジャー版） | `mise outdated --bump` | `mise upgrade --bump <tool>` | 範囲の外の新しい版に上げ、`config.toml` も書き換わる。変更をコミットする |
| Rust | — | `rustup update` | |
| Claude Code | — | 自動で更新される（手動なら `claude update`） | |
| dotbot | — | `git submodule update --remote dotbot` | 変更をコミットする |

補足：

- miseは `latest` や `24` と書いてあっても、**自分で勝手には上げない**。上がるのは `mise upgrade` を実行したときと、新しいMacで `mise install` を実行したときだけ。
- `mise upgrade` で古いバージョンは自動で消える（すぐではなく、少し時間を置いて消える）。すぐ消したいなら `mise prune`。
- プロジェクトごとに `mise.toml` でバージョンを固定しているものは、そのプロジェクトのディレクトリで `mise upgrade` を実行する。
