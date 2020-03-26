---
title: Mira Prism 解体新書
tags: Unity MiraPrism
author: GONBEEE_project
slide: false
---
MiraPrismの日本語文献が全くなく、血の涙を流し続けたので  
後発のために知見を残す。  
俺の屍を越えてゆけ。  

# はじめに
この記事にたどり着いている時点でMiraPrismがどんな物かは知っていると思われるので、  
デバイス紹介はスキップする。  
知らない人は目の前の箱を粗大ゴミに持っていこう。  

知ってる人は以下のものを用意して本編を神妙に待たれよ。  
・Unity2017.1以降  
・iPhone6  
・Mac（Xcodeビルド用）  
・Xcode8以降  
・MiraPrism  


# 構築作業

## 1. パッケージダウンロード
MiraPrismの[GitHub](https://github.com/miralabs/mira-unity-sdk)にアクセスして、  
最新パッケージをダウンロードする。  
2018/12/20時点では[ver0.7.1](https://github.com/miralabs/mira-unity-sdk/releases)

## 2. Unityにインポート
Unity2017.1以降のプロジェクトにインポートする。  
初めてMiraを触る場合は新プロジェクト作成を強くおすすめする。  
![スクリーンショット 2018-12-20 11.54.55.png](https://qiita-image-store.s3.amazonaws.com/0/241210/330b10bc-01f8-0a04-0451-14d3c129a79b.png)  
インポート直後  
ちなみにdll入りパッケージなので、GitHubやらで管理する場合は.ignoreに要注意  

## 3. シーンを覗く
`MiraSDK`フォルダ内の`Scene/MiraWikiTemplate`を開く。  
![スクリーンショット 2018-12-20 11.58.18.png](https://qiita-image-store.s3.amazonaws.com/0/241210/837f3d36-8052-efb2-c36b-6a8d5ed018db.png)  
このシーンのHierarchyを解体していけばMiraSDKは完全に理解できる。  
今回は特に重要なマーカートラッキングに関するGameObjectだけ解体する。  

## 4. マーカーを探す
`MiraWikiTemplate`シーンから  
`MiraWikiTracking/Trackers/MiraTracker`を探す。  
![スクリーンショット 2018-12-20 12.04.10.png](https://qiita-image-store.s3.amazonaws.com/0/241210/43b3c63c-c0b8-1d1b-14fb-d6864bf94f4f.png)  
Inspectorを見ると分かるが、マーカートラッキング用の画像が3種類登録されている。  
特に何も触らなくてもランタイムでマーカーを自動で追っかけてくれる。  
注目すべきは`MiraTracker`オブジェクトの子の`spiral`オブジェクトで、  
この階層に登録されているオブジェクトがマーカーに固定される。  
今回は`spiral`と同じ階層にいくつかGameObjectを追加してみる。  

ちなみに`ImageTrackable`スクリプトの`Preview`ボタンを押すとシーンにマーカーが表示され、  
マーカーとGameObjectがどのような位置関係で表示されるかが確認できる。  
![スクリーンショット 2018-12-20 12.12.32.png](https://qiita-image-store.s3.amazonaws.com/0/241210/120f28c4-fa62-a468-93ad-4240923fb4be.png)  

## 5. オブジェクトを置く
`spiral`と同じ階層にオブジェクトを追加する。  
今回は適当にUnity基本オブジェクトをいくつか置いてみた。  
このシーンは初期状態では光源を置いていないので、何も考えずにオブジェクトを置くと真っ暗になってしまう。  
![スクリーンショット 2018-12-20 12.20.44.png](https://qiita-image-store.s3.amazonaws.com/0/241210/09a96105-1054-25bd-7eb4-6f81071c0b2b.png)  
回避策としては光源を置く、シェーダを変えるなどがある。  
今回はシェーダをUnlitColorに変えて対応した。  
光源を置かなかった理由としては、Miraをいじってる間にUnityちゃんを表示したことがあったが、  
ディレクショナルライトの設定が面倒だったため、UnityちゃんのシェーダをUnlitTextureにして対応した。  
その流れでUnlitにしただけなので、光源チョットデキル人はお好きなシェーダを使えばいいと思う。  
![スクリーンショット 2018-12-20 12.39.46.png](https://qiita-image-store.s3.amazonaws.com/0/241210/05334c89-1ea3-007f-6ca6-e5fda0d1d9a5.png)  

## 6. プロジェクト設定を変更　！！極めて重要！！
**ちゃんと設定しないとビルド通っても動きません**  
**一番大事！**  
~~大事ならインポート直後にやれって？うるせぇ！~~  
ProjectSettingをMiraPrism推奨の状態に設定する。  
SteamVRのUse Recomendみたいな感じ。  

上部メニューにある`Mira/Settings`でセッティングメニューを開く。  
![SnapCrab_2018-12-20_13-46-48_No-0000.png](https://qiita-image-store.s3.amazonaws.com/0/241210/0421780b-0e9e-938b-712a-05252110cf3b.png)  
こんなメニューが出てくるので、作りたてプロジェクトであれば脳死で全部Use Recomended押して大丈夫。  
ただし上から2つ目のBundleIDに関しては必ずしもRecomendedでなくても平気、というかBundleIDにRecomendedがあることが逆にびっくり。  

上から3つ目にあるCamera Usage Descriptionが微妙に曲者で、これがComputer Visionではないと  
**アプリ起動後スプラッシュスクリーンすら出ずにアプリ落ちする現象が確認できている。**  
正確にはComputer Visionではなくても大丈夫だが、せっかくRecomendedされているのでそのまま使おう。  
iOSには何かしらの権限を要求する際、何のために使うかを明記しないとアプリまるごと落とされる。  
そういうとこやぞ、Apple  

参考：[iOSでカメラにアクセスすると落ちるのをUnityエディタ上で修正するメモ](https://qiita.com/JunSuzukiJapan/items/e7c04072ac5e83fa6595)  

今後調査を進めてここの項目を書き加える可能性アリ  

## 6. ビルドしよう！
今回編集したシーンは`MiraWikiTemplate`なので、`BuildSettings`から`MiraWikiTemplate`シーンを追加し、  
Xcode（iOS）に向けてビルドしよう。  
ここまでたどり着ければ後は普通のiOS、iPhoneアプリ開発と同じ流れになるので、  
これ以降は省略させていただく。  

（正確にはXcode側でもちょっとした作業があったが、  
2018/12/20時点の最新版SDK0.7.1ではXcodeでの作業は省略できるようになっている。やさしい世界。）  

# さいごに
MRゴーグルは視野角が広いだけで圧倒的プレゼンスと言われる。  
MiraPrismは反射式なので非常に視野角が広く、映像もきれいなのでとてもいいデバイスに見える。  
だが忘れてはいけない。その美しいホログラムの裏側には血の海が広がっているということを...  
~~俺の屍を越えてゆけ~~  

それはそうとMiraPrismの反射板、NorthStarに移植したいんだけど、誰かやってくれないかな  



