## 使い方

1. このリポジトリをダウンロード
2. APIキー(.p8)を `keys` フォルダに置く
    - https://developer.apple.com/documentation/appstoreserverapi/creating_api_keys_to_use_with_the_app_store_server_api
3. gemをインストール
    - `.ruby-version` と `Gemfile` のRubyバージョンを適宜変更
    - `bundle install --path vender/bundle`
4. ターミナルで以下のように実行

```
KEY=SubscriptionKey_YOURKEYID.p8 ISS=ISSUER_ID BID=APP_BUNDLE_ID ENDPOINT=/inApps/v1/history/ORIGINAL_TRANSACTION_ID bundle exec bin/server

  - YOURKEYID: APIキーID
  - APP_BUNDLE_ID: 対象アプリのバンドルID
  - ORIGINAL_TRANSACTION_ID: オリジナルトランザクションID
```

### 購入履歴の調べ方

上記の方法で [Get Transaction History](https://developer.apple.com/documentation/appstoreserverapi/get_transaction_history) APIを呼ぶことで購入履歴を取得できる。
一度のAPIで最大20件までしか返らないため、レスポンス `revision` をパラメータに渡して後続の値を取得する。

```
... ENDPOINT=/inApps/v1/history/ORIGINAL_TRANSACTION_ID?revision=REVISION bundle exec bin/server > history.json

  - REVISION: 直前のAPIレスポンス `revision` の値
```

またレスポンスJSONの `signedTransactions` はJWTのようにエンコードされているので、以下のコマンドでデコードできる。

```
bundle exec bin/decode_transactions history.json
```

参考: https://developer.apple.com/documentation/appstoreserverapi/jwstransaction

### サブスクリプション状況の調べ方

```
KEY=SubscriptionKey_YOURKEYID.p8 ISS=ISSUER_ID BID=APP_BUNDLE_ID ENDPOINT=/inApps/v1/subscriptions/ORIGINAL_TRANSACTION_ID bundle exec bin/server > subscription.json

bundle exec bin/decode_subscriptions subscription.json
```

`status` の値を見る。
参考: https://developer.apple.com/documentation/appstoreserverapi/status
