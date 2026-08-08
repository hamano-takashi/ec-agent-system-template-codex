# 相場調査ルール

今後、メルカリ出品・編集で「相場調査」「価格調査」「出品価格を調べて」と依頼された場合は、以下を必ず使用する。

## 調査対象プラットフォーム

- メルカリ
- ヤフオク
- Yahoo!フリマ
- Amazon

## 調査期間

- 原則、調査日から過去6か月以内に売れた商品を対象にする。
- 6か月以内の売却データが見つからない場合は、その旨を明記し、確認できた範囲を「参考情報」として分けて表示する。

## 表示必須項目

各プラットフォームごとに、以下を表示する。

- 最高価格
- 最安価格
- 平均価格
- 確認件数
- 取得可否
- 備考

## 取得できない場合の扱い

- 売却済み価格や過去6か月の実売データを公開画面から確認できない場合は、推測で数字を作らない。
- Amazonなど、公開画面だけでは実売履歴が確認できない場合は「実売履歴は公開情報から取得不可」と明記する。
- 現在価格、新品価格、中古価格、類似商品の価格を使う場合は、実売データではなく「参考価格」として分ける。
- 同一商品ではなく類似商品を使った場合は、どの点が類似でどの点が違うかを短く記載する。

## 推奨価格の扱い

- 推奨価格は、4プラットフォームの結果と商品の状態、付属品、希少性、出品中価格を分けて判断する。
- 価格欄への反映は、ユーザーが明示的に金額を承認してから行う。
- 「この価格で変更して」と明確に指示されるまでは、編集保存時に価格を変更しない。
- 自動化では、相場調査を行った当日の日付で Google スプレッドシートへ追記する。記録先は `.tools/mercari/mercari-lister-second-market-research-sheet.json` を参照する。
- 既存価格は維持し、スプレッドシートには現在価格・相場レンジ・推奨価格・備考のみを記録する。
- 再出品の調査も、再出品した日現在の相場として新しい行を追加する。
- Google スプレッドシートへ追記できない場合のみ、`.tools/mercari/mercari-lister-second-market-research.csv` を退避用に使う。

## スプレッドシート記録項目

- `checkedDate`
- `account`
- `itemId`
- `itemUrl`
- `currentTitle`
- `currentPrice`
- `mercariMin`
- `mercariMax`
- `mercariAvg`
- `mercariCount`
- `yahooAuctionMin`
- `yahooAuctionMax`
- `yahooAuctionAvg`
- `yahooAuctionCount`
- `yahooFleaMin`
- `yahooFleaMax`
- `yahooFleaAvg`
- `yahooFleaCount`
- `amazonNewMin`
- `amazonNewMax`
- `amazonNewAvg`
- `amazonNewCount`
- `amazonUsedMin`
- `amazonUsedMax`
- `amazonUsedAvg`
- `amazonUsedCount`
- `recommendedPrice`
- `priceStatus`
- `notes`

## 報告フォーマット

```text
【相場調査結果（過去6か月）】
メルカリ：最高 ¥ / 最安 ¥ / 平均 ¥ / 件数 件 / 取得可否：
ヤフオク：最高 ¥ / 最安 ¥ / 平均 ¥ / 件数 件 / 取得可否：
Yahoo!フリマ：最高 ¥ / 最安 ¥ / 平均 ¥ / 件数 件 / 取得可否：
Amazon：最高 ¥ / 最安 ¥ / 平均 ¥ / 件数 件 / 取得可否：

【参考価格】
現在出品・新品価格・中古価格・類似商品価格：

【推奨価格】
推奨：¥
理由：

【価格変更確認】
この価格で変更してよろしいでしょうか？
```
