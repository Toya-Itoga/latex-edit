---
description: reports/ を基に main.tex の章立て（フレーム）を構築する
---

# /write-frame

reports/ の資料を読み、main.tex に論文・報告書のフレーム（章立てと各節の要点メモ）を構築する。

## コンテキスト
- @CLAUDE.md
- reports/ 内の全ファイル
- 追加指示: $ARGUMENTS

## 手順
1. reports/ の中身を確認する。空の場合は「reports/ にレポートが存在しません」と返して終了する
2. 資料から以下を整理し、箇条書きでユーザーに提示する
   - 背景・課題
   - テーマ（研究目的）
   - 実施内容（手法）
   - 成果・今後の課題
3. main.tex に本文がすでにある場合は、上書きしてよいか確認してから進める
4. main.tex の `\begin{document}` 〜 `\end{document}` の間にフレームを生成する
   - `\section` / `\subsection` で章立てする
   - 各節には、書くべき内容を `% TODO:` コメントで1〜2行ずつ記載する
   - 図表を入れる予定の箇所には `% FIG:` コメントで図の案を残す
5. `latexmk main.tex` を実行し、ビルドが通ることを確認する

## 注意事項
- フレームのみを構築し、本文は書かない
- プリアンブル（`\documentclass` からパッケージ読み込みまで）は変更しない
- reports/ にない情報を推測で補わない。不明な点は `% TODO: 要確認` と書く
- reports/ と docs/ は編集しない
