---
title: "AppCenterを使ってREACT NATIVEアプリをリリースしてみた話"
emoji: "🔰"
type: "tech"
topics: ['ios','reactnative','appcenter','app']
published: false
---

AppCenter.msを使って良かったこと：

時間短縮
自動アップデート
dependenciesを自動でインストールしてくれる
MacOS使っていなくても、iOSのアプリをリリースできる

###設定

1. appcenterのアカウントを作成する。
（Githubでログインすることをおすすめ）
2. Add new appをクリックしてアップロードしたいアプリを登録する。
（ReactNativeでもiOS/Androidを別々で登録する必要ある）
picture here 
3. メニューからbuildタブに入って、githubにあがっているアプリのレポを選択します。
　　branchを間違えないよう注意！
　　build on pushを選択するとレポ更新される際に自動アップデートできます。

手動アップデートまたテスト実装の場合下までスクロールしてください。
run unit tests と　sign buildsを選択します。

1. ターミナルを開いてiosフォルダーを開きます。
```
open ios/xxxx.xcodeproj
```
2. 自動ログインを外します（外さないと他のデバイスからアプリを見れなくなる）
テストする際にログインが必要ですので、provisioning profileを作成して権限を決めます。
役に立つ記事
https://www.musen-connect.co.jp/blog/course/product/ios-ble-dev-provisioning-profile/
https://support.staffbase.com/hc/en-us/articles/115003598691-Creating-the-iOS-Provisioning-Profiles

3. schemeを編集する。
（デフォルトにしているとテストの目的はDebugとなる）
今回はリリースする目的のアプリをテストするので、productionを設定します。
