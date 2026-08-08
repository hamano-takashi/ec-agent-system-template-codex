# スプレッドシート記録ルール

chaki 用の Mercari 自動編集で相場調査を行った商品は、価格を自動変更せず、調査結果だけをスプレッドシート台帳へ残す。

## 記録先

- 標準の記録先は Google スプレッドシートとする。
- 設定ファイルは `.tools/mercari/mercari-lister-second-market-research-sheet.json` とし、`spreadsheetId`、`spreadsheetUrl`、`sheetName` を参照して追記する。
- 実行環境側で直接連携を実装する場合は、同設定ファイルへ `webhookUrl` や `headers` を追加してもよい。
- `.tools/mercari/mercari-lister-second-market-research.csv` は障害時の退避先としてのみ保持する。
- Google スプレッドシートへまだ書けていない対象は `.tools/mercari/mercari-lister-second-market-research-pending.json` に必ず残す。
- Google スプレッドシートの表示列名は日本語にする。内部 JSON キーや処理用フィールド名が英語でも、シートへ表示・上書きするときは日本語見出しへ変換する。

## 基本方針

- 価格欄は自動で変更しない。
- 新規出品でも再出品でも、その検知日現在の相場を1行追加する。
- 画像編集が完了済みで、市場調査だけ未記録の商品を再検知した場合も、その検知日現在の相場を1行追加する。
- 同じ商品が再出品された場合は上書きせず、`checkedDate` を変えて別行で追記する。
- 実売データが取れない項目は空欄ではなく `取得不可` を備考に残す。
- 価格は自動変更しない。`currentPrice` と `recommendedPrice` は、ユーザー承認がない限り同じ値のまま記録する。
- `priceStatus` の表示値は、シート上では `相場記録のみ`、`価格維持`、`要確認` など日本語で表示する。

## 列定義

`checkedDate`,`account`,`itemId`,`itemUrl`,`currentTitle`,`currentPrice`,`mercariMin`,`mercariMax`,`mercariAvg`,`mercariCount`,`yahooAuctionMin`,`yahooAuctionMax`,`yahooAuctionAvg`,`yahooAuctionCount`,`yahooFleaMin`,`yahooFleaMax`,`yahooFleaAvg`,`yahooFleaCount`,`amazonNewMin`,`amazonNewMax`,`amazonNewAvg`,`amazonNewCount`,`amazonUsedMin`,`amazonUsedMax`,`amazonUsedAvg`,`amazonUsedCount`,`recommendedPrice`,`priceStatus`,`notes`

## priceStatus の値

- `kept`: 現在価格を維持した
- `research_only`: 相場だけ記録した
- `approval_required`: 価格変更提案はあるが未承認
- 自動化の市場調査追記では、出品ページを変更していない場合も `research_only` を使ってよい。
