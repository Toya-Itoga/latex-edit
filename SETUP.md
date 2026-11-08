# LaTeX 環境構築ガイド（Mac + VS Code）

platex + dvipdfmx 構成で、VS Code 上で LaTeX 文書を作成する環境の構築手順と使い方。

## 構成

| 項目 | 採用 |
|---|---|
| ディストリビューション | MacTeX（TeX Live ベース） |
| エンジン | platex + dvipdfmx |
| ビルドツール | latexmk |
| エディタ | VS Code + LaTeX Workshop |
| AI 支援 | Claude Code（任意） |

---

## 1. インストール

### 1-1. MacTeX

```bash
brew install --cask mactex-no-gui
```

- 約 6GB。完了まで 10〜30 分
- 途中で `Password:` と表示されたら Mac のログインパスワードを入力して Enter
  - 入力中は何も表示されないが正常
- 完了すると `mactex-no-gui was successfully installed!` と表示される

### 1-2. 確認とアップデート

**新しいターミナルを開いて**実行する。

```bash
which platex dvipdfmx latexmk
# 3つとも /Library/TeX/texbin/... ならOK

sudo tlmgr update --self --all
```

### 1-3. VS Code 拡張機能

拡張機能「**LaTeX Workshop**」（James Yu）をインストールする。

---

## 2. プロジェクト構成

```
latex-edit/
├── .claude/commands/  # Claude Code カスタムコマンド
│   ├── write-frame.md
│   └── review.md
├── .vscode/
│   └── settings.json  # LaTeX Workshop 設定
├── docs/              # ビルド生成物（自動生成）
├── figures/           # 図
├── reports/           # 参照資料（読み取り専用）
├── .gitignore
├── .latexmkrc         # latexmk 設定
├── CLAUDE.md          # Claude Code 向け指示
├── README.md
├── main.tex           # 本文
└── references.bib     # 参考文献
```

---

## 3. 設定ファイル

### 3-1. `.latexmkrc`

```perl
#!/usr/bin/env perl
$latex     = 'platex -synctex=1 -halt-on-error -interaction=nonstopmode -file-line-error %O %S';
$bibtex    = 'pbibtex %O %B';
$biber     = 'biber --bblencoding=utf8 -u -U --output_safechars %O %B';
$makeindex = 'mendex %O -o %D %S';
$dvipdf    = 'dvipdfmx %O -o %D %S';
$pdf_mode  = 3;        # latex → dvi → dvipdfmx
$max_repeat = 5;
$out_dir   = 'docs';   # 生成物の出力先
```

### 3-2. `.vscode/settings.json`

```json
{
  "latex-workshop.latex.recipes": [
    { "name": "latexmk (platex)", "tools": ["latexmk"] }
  ],
  "latex-workshop.latex.tools": [
    {
      "name": "latexmk",
      "command": "latexmk",
      "args": ["-silent", "%DOC%"],
      "env": {}
    }
  ],
  "latex-workshop.latex.outDir": "./docs",
  "latex-workshop.latex.autoBuild.run": "onSave",
  "latex-workshop.latex.recipe.default": "first",
  "latex-workshop.latex.rootFile.useSubFile": false,
  "latex-workshop.view.pdf.viewer": "tab",
  "latex-workshop.message.warning.show": false
}
```

> `args` に `-outdir` を入れないこと（`.latexmkrc` の `$out_dir` を上書きしてしまう）。

### 3-3. `.gitignore`

```gitignore
# LaTeX の中間ファイル
*.aux
*.dvi
*.log
*.fls
*.fdb_latexmk
*.synctex.gz
*.toc
*.lof
*.lot
*.bbl
*.blg
*.out
*.nav
*.snm

# macOS
.DS_Store
```

PDF も Git 管理しない場合は `docs/` を追加する。

### 3-4. `main.tex`（最小例）

```latex
\documentclass[a4paper,11pt]{jsarticle}
\usepackage[dvipdfmx]{graphicx}
\graphicspath{{figures/}}

\title{タイトル}
\author{名前}
\date{\today}

\begin{document}
\maketitle
\section{はじめに}
本文。
\end{document}
```

---

## 4. 使い方

### 4-1. ビルドの仕組み

```
main.tex ──platex──▶ main.dvi ──dvipdfmx──▶ main.pdf
```

- `\ref` や `\cite` の番号は複数回のコンパイルで確定する
- 何回実行するかは latexmk が自動で判断する

### 4-2. VS Code

`main.tex` を保存すると自動でビルドされる。

| 操作 | ショートカット |
|---|---|
| ビルド | `Cmd+Option+B` |
| PDF 表示 | `Cmd+Option+V` |
| ソース → PDF へジャンプ | `Cmd+Option+J` |
| PDF → ソースへジャンプ | PDF 上で `Cmd+クリック` |

ログは「出力」タブ → プルダウンで「LaTeX Workshop」を選ぶと確認できる。

### 4-3. ターミナル

```bash
latexmk main.tex      # ビルド → docs/main.pdf
latexmk -c main.tex   # 中間ファイル削除（PDF は残す）
latexmk -C main.tex   # PDF 含め全削除
```

### 4-4. Claude Code コマンド

| コマンド | 内容 |
|---|---|
| `/write-frame [指示]` | reports/ を基に main.tex の章立てを作る |
| `/review [節名]` | reports/ との齟齬・誤字脱字・LaTeX の記述をチェックする |

---

## 5. トラブルシューティング

| 症状 | 原因 | 対処 |
|---|---|---|
| `Password:` で止まる | sudo のパスワード待ち | ログインパスワードを入力（画面には表示されない） |
| VS Code のターミナルだけ `platex not found` | 別の TeX（TinyTeX など）の PATH が優先されている | `echo 'export PATH="/Library/TeX/texbin:$PATH"' >> ~/.zshrc` を実行し、VS Code を `Cmd+Q` で再起動 |
| `spawn latexmk ENOENT` | VS Code が PATH を読めていない | `command` を `/Library/TeX/texbin/latexmk` に変更 |
| 保存しても PDF ができない | 拡張機能の設定・読み込みの問題 | ターミナルで `latexmk main.tex` を実行して切り分け → 「Developer: Reload Window」 |
| PDF が表示されない | ビューアの参照先がずれている | `latex-workshop.latex.outDir` と `$out_dir` をそろえる |
| 参照が「??」になる | コンパイル回数が足りない | latexmk で再ビルド |
| 画像が出ない | ドライバの指定漏れ | `\usepackage[dvipdfmx]{graphicx}` |
| 文字化け | 文字コード | ファイルを UTF-8 で保存 |
| 参考文献が出ない | BibTeX の種類が違う | テンプレートに合わせて `$bibtex` を pbibtex / upbibtex に変更 |
