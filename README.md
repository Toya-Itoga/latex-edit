# latex-edit

卒業研究の LaTeX 文書を作成するリポジトリ。

## 環境

- MacTeX（TeX Live）
- platex + dvipdfmx
- latexmk
- VS Code + LaTeX Workshop

## 使い方

```bash
latexmk main.tex      # ビルド → docs/main.pdf
latexmk -c main.tex   # 中間ファイル削除
latexmk -C main.tex   # PDF含め全削除
```

## ディレクトリ・ファイル構成

```
.
├── .claude/commands/  # Claude Code カスタムコマンド
├── .vscode/           # LaTeX Workshop 設定
├── docs/              # ビルド生成物
├── figures/           # 図
├── reports/           # 参照資料（中間発表スライド等）
├── .latexmkrc         # latexmk 設定
├── CLAUDE.md          # Claude Code 向け指示
├── main.tex           # 本文
└── references.bib     # 参考文献
```
