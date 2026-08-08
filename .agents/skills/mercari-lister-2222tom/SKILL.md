---
name: mercari-lister-2222tom
description: メルカリアカウント 2222tom 専用の出品編集・再出品検知・相場調査・Google Sheets記録を支援する。ユーザーが「2222tomで出品」「2222tomを自動編集」「2222tomの相場調査」「2222tomの画像加工」などと依頼した場合に使用する。チャキスリー/chakiとはChromeプロファイル、ログ、キュー、画像デザイン、スプレッドシートタブを完全分離する。
---

# 2222tom専用メルカリ自動化

このスキルは `2222tom` だけを扱う。`chaki`、`チャキスリー`、`mercari-lister-second` のセッション、商品、ログ、キュー、加工画像、実行結果、シートタブへ読み書きしない。

## 絶対分離ルール

- 対象アカウントは `2222tom` のみとする。
- 専用Chrome user-data-dir `.tools/chrome-mercari-2222tom-profile` と専用CDPポート `9230` を使う。
- 編集直前に画面上のアカウント表示が `2222tom` と一致することを確認する。一致を確認できない場合は編集・保存・画像削除を行わない。
- `chaki` または `チャキ` を示す表示を検出した場合は即停止し、ブロッカーとして記録する。
- Google Sheets操作は専用タブ `2222tom-market-research`（gid `1591706513`）だけを対象にする。
- Gmailアドレス、Cookie、パスワード、トークンをコード、設定、ログへ保存しない。

## 専用ファイル

- 設定: `.tools/mercari/2222tom-account-config.json`
- 編集済みログ: `.tools/mercari/mercari-lister-2222tom-edited-items.json`
- 市場調査設定: `.tools/mercari/mercari-lister-2222tom-market-research-sheet.json`
- 未処理キュー: `.tools/mercari/mercari-lister-2222tom-market-research-pending.json`
- CSV退避先: `.tools/mercari/mercari-lister-2222tom-market-research.csv`
- 実行結果: `.tools/mercari/2222tom_auto/auto_runs/`
- 画像加工: `.tools/mercari/2222tom_auto/prepare_images.py`

上記以外の `mercari-lister-second-*`、`chaki_auto`、`auto_runs` は使用しない。

## 自動処理

### Amazonリサーチ完全停止

- 自動化で行う相場調査は、メルカリ、Yahoo!オークション、Yahoo!フリマの3媒体だけを対象とする。
- 自動化ではAmazonリサーチを完全停止する。Amazonの検索結果、商品ページ、新品価格、中古価格へアクセス、検索、取得、参照、再試行を行わない。
- 既存のGoogle Sheetsに記録済みのAmazon列は削除・上書きせず、新規または更新行のAmazon列は空欄のままにする。
- Amazon列が空欄または未取得であることを、ブロッカー、pending継続、`marketResearchLogged=false` の理由にしない。3媒体のうち少なくとも1媒体で有効な価格と件数を取得できれば、自動化の市場調査完了条件を満たす。
- 本ファイル内にAmazon調査を要求する記述が残っている場合でも、自動化ではこのAmazon完全停止ルールを優先する。ユーザーが手動でAmazon調査を明示依頼した場合に限り、通常の相場調査として扱う。

1. 2222tom専用Chromeセッションを確認する。
2. 新規出品、再出品、オークション形式を検知する。
3. 専用編集済みログで状態を分類する。
   - 商品ステータスが `stop`（公開停止中）: 画像・タイトル・説明・`★★★`・保存を一切変更せず、市場調査とSheets記録だけを行う。
   - `imageEdited != true`: タイトル、説明、画像、必要なカテゴリを編集する。
   - `imageEdited == true` かつ `marketResearchLogged != true`: 商品ページを変更せず市場調査だけを行う。
   - 両方 `true`: 完了済みとしてスキップする。
4. 一覧に見えている未処理商品を同じ実行内ですべて処理する。
5. 価格は変更せず現在価格を維持する。価格変更はユーザーの明示承認後だけ行う。
6. 編集後にメルカリ、ヤフオク、Yahoo!フリマを過去6か月中心で調査する。Amazonは調査しない。
7. 推測値を作らず、取得不能は `取得不可` と記録する。
8. 少なくとも1媒体で有効価格と件数が取れた場合だけ `marketResearchLogged=true` にする。
9. 専用シートへ1商品1行で追記する。失敗時は専用pendingとCSVへ残す。

## タイトル・説明

- タイトルは40文字以内とし、検索重要語を先頭へ置く。
- 英語名と日本語名の併記で40文字を超える場合は日本語名を優先し、英語名は説明と検索ワードへ入れる。
- 説明は丁寧で親しみやすく、商品状態、付属品、型番、発送、注意、検索ワードを記載する。
- 元説明の `M-XXX` 通番は説明文の最下部へ残す。
- ハッシュタグは使わない。

## 画像デザイン

- 1080×1080の正方形にし、白背景、明るさ、コントラストを整える。
- 1枚目はスクリーンショット準拠で、上部に淡い黄色の横長帯、中央揃えの赤文字タイトル、右下に青い丸型の「送料無料」バッジを置く。
- 商品本体を大きく中央に見せ、帯とバッジで重要部分を隠さない。
- 2枚目は1枚目と同じ元画像を装飾なしで明るく加工し、商品確認用にする。
- 3枚目以降は装飾文字を避け、自然な色と見やすさを優先する。
- チャキスリー用のゴールド帯、ネイビー文字、ピンクバッジを使わない。

## シート列

`checkedDate`,`account`,`itemId`,`itemUrl`,`currentTitle`,`currentPrice`,`mercariMin`,`mercariMax`,`mercariAvg`,`mercariCount`,`yahooAuctionMin`,`yahooAuctionMax`,`yahooAuctionAvg`,`yahooAuctionCount`,`yahooFleaMin`,`yahooFleaMax`,`yahooFleaAvg`,`yahooFleaCount`,`amazonNewMin`,`amazonNewMax`,`amazonNewAvg`,`amazonNewCount`,`amazonUsedMin`,`amazonUsedMax`,`amazonUsedAvg`,`amazonUsedCount`,`recommendedPrice`,`priceStatus`,`notes`

`account` は必ず `2222tom` とする。`priceStatus` は `kept`、`research_only`、`approval_required` のいずれかを使う。

## 完了前検証

- アカウント表示が2222tomである。
- 専用ログ・専用pending・専用runDirだけが更新されている。
- シートのspreadsheetIdとgidが専用設定に一致する。
- チャキスリー用ファイルの更新時刻・内容が変わっていない。
- 保存前後で価格が同一である。
- 1枚目と装飾なし2枚目が両方ある。
- 公開停止中の商品では編集画面への変更、画像削除、`★★★`追加、保存が一切行われていない。
