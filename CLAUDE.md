# CLAUDE.md

4年次「ICT活用総合実習」の背景課題・テーマ・実施内容・成果をまとめた LaTex 文書を作成する

## ビルド環境

| 項目 | 内容 |
|---|---|
| ディストリビューション | MacTeX（TeX Live） |
| エンジン | platex + dvipdfmx（テンプレートの前提。lualatex / xelatex / pdflatex には変更しない） |
| ビルドツール | latexmk（設定は `.latexmkrc`） |
| エディタ | VS Code + LaTeX Workshop（保存時に自動ビルド） |

### コマンド

```bash
latexmk main.tex      # ビルド（出力は docs/）
latexmk -c main.tex   # 中間ファイル削除（PDFは残す）
latexmk -C main.tex   # PDF含め全生成物を削除
```

- 変更後は必ず `latexmk main.tex` でビルドが通ることを確認する
- エラー時は `docs/main.log` を確認し、原因箇所（ファイル名:行番号）を特定してから修正する

## ディレクトリ構成

```
latex-edit/
├── .claude/commands/  # Claude Code カスタムコマンド
├── .vscode/           # LaTeX Workshop 設定
├── docs/              # ビルド生成物（main.pdf, .aux, .log 等）※編集しない
├── figures/           # 図（PDF / PNG / JPG）
├── reports/           # 中間発表スライド等の参照資料 ※読み取り専用・編集しない
├── .latexmkrc         # latexmk 設定（platex + dvipdfmx, out_dir = docs）
├── main.tex           # 本文（ビルド対象はこれのみ）
└── references.bib     # 参考文献
```

## 執筆ルール

### 文体・表記
- 文体は「である調」で統一する
- 句読点は「、」「。」を使用する
- 英数字は半角、全角英数字は使わない
- 専門用語は初出時に説明を付ける。略語は初出時に正式名称を併記する

### LaTeX 記法
- 図の挿入：`\includegraphics` は `figures/` 内のファイル名のみ指定（`\graphicspath` 設定済み）
- graphicx 等のドライバ依存パッケージには `[dvipdfmx]` オプションを付ける
- 図表・数式・節には必ず `\label{}` を付け、`\ref{}` で参照する
  - 接頭辞：`fig:` / `tab:` / `eq:` / `sec:`
- 図は `figure` 環境 + `\centering` + `\caption` を基本形とする
- 参考文献は `references.bib` に追記し、`\cite{}` で引用する。BibTeX は pbibtex

### 制限
- エンジンやドキュメントクラスを勝手に変更しない
- `reports/` と `docs/` 内のファイルを編集しない
- `.latexmkrc` / `.vscode/settings.json` は依頼がない限り変更しない
- 存在しない文献を `references.bib` に追加しない（出典が不明な場合は確認する）

## 作業の進め方
- 大きな構成変更（章立ての追加・削除など）は、着手前に方針を提示して確認を取る
