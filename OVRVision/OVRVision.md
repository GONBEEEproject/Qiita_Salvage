---
title: OVRVision Pro 解体新書
tags: Unity ovrvision
author: GONBEEE_project
slide: false
---
おしごとでOVRVisionを触りましたが、
日本語文献が凹みさんのブログ（2015年の記事）しかなかったので、
後発のために知見を残したいと思います。
http://tips.hecomi.com/entry/2015/12/12/231957

前にもこんなこと[あったような](https://qiita.com/GONBEEE_project/items/20b2da4328f2b31310e6)？ 

# はじめに
Twitterでいろいろ調べているうちに「Windows10じゃ動かない」みたいな文字を観測した気がするので、
環境指定は丁寧にやります。

・Windows10 Home 64bit
・Unity5.6.7f1 (2017以降は未確認、スクリプトは特に問題なさそうなので動くはず)
・Oculus Rift CV1
・OVRVision Pro

# SDK系ダウンロード
SDK周りがちょっとややこしいことになってるので、注意しましょう。

## 1.公式サイトへ
[セットアップページ](http://ovrvision.com/setup/)へアクセスしてください。
Unityで使うことを前提とします。

下記画像を参考に
・for Windows
・for Unity
の2つをダウンロードします。
DropBoxへ飛ぶので直接ダウンロードしてきてください。

**for Windows SDKの中にOVRVisionのキャリブレーションツールや生データ確認ツールが同梱されているので、
Unityで使う場合でもfor Windows SDKをダウンロードしましょう。**

![InkedSnapCrab_2019-05-21_12-05-33_No-0000_LI.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/241210/2af1e6a4-c8b4-cfbc-ec14-878caa152995.jpeg)

# 2.任意の場所に展開
Zipファイルを任意の場所に展開してください。
・ovrvisionprosdk_unity5
・ovrvisionprosdk_windows
の2つがあれば正解です。

unity5フォルダの中に
`unity5_ovrvision.unitypackage`というファイルが確認できます。
基本的に使うのはこれだけで、他のフォルダにはサンプルexeなどが入っていますが、
何故か動かないので気にしないで問題ありません。

# キャリブレーション
**今回の地雷ポイント**
OVRVisionは既に組み立て済みであることを前提に話を進めます。

## 1.OVRVisionを接続する
同梱されているケーブルをUSB3.0ポートに接続してください。
ハブを噛ませても電力的には問題なさそうですが、2.0だと速度の影響で動かない可能性があります。
おとなしく3.0ポートを確保しましょう。

## 2.キャリブレーションツール起動
前項でダウンロードしてきたovrvisionprosdk_windowsの中にキャリブレーションツールが入っています。
`ovrvisionprosdk_windows\ovrvisionprosdk_windows\tools\ovrvision_calibration.exe`を起動すると
下記のような画面が出ます。
このチェッカーを複数のアングルから撮影することでキャリブレーションを行ってください。

![SnapCrab_2019-05-21_12-34-16_No-0000.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/241210/ed8c6278-4ab3-77ba-2ee1-2a4a9b4ab8e3.png)

## 3.定規を用意する
**15cm定規です。冗談でなく。**
`Open Ovrvision`をクリックして起動し、横にある`Start Calibration`をクリックすると
キャリブレーションが始まりますが、真っ先に出る画像がこちらです。
![SnapCrab_2019-05-21_12-39-35_No-0000.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/241210/32f55eeb-6f52-4eda-063a-fc7b38ae4b20.png)
**Please measure the tile on a screen with a ruler**
**定規でスクリーン上のタイルのサイズを測ってね**

確かに画面解像度、インチ、環境差、個体差などのキャリブレーションは大事ですが、
定規って、、、
基本的にはプリセットで入力されている22.50mmで問題はありません。
そもそも一般的な定規は0.1mmは計測できないので、誤差誤差！

OKを押して次のステップへ進んでください。

## 4.写真を撮ろう
次に表示される画像がこちら。
![SnapCrab_2019-05-21_12-45-01_No-0000.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/241210/db05cdca-53c5-e8ed-6236-5819a74ba07f.png)
画像ではOculusRiftDK2にOVRVisionが貼り付けられていますが、手持ちでも特に問題はありませんでした。
右下に
`Please photograph a chessboard from various angles.`
`色んな角度からチェスボード（チェッカー）の写真をとってね`
とあります。
ショートカットキーはCキーで、片手でカメラを持ちながら
片手でCキーを押す形になると思われます。

この色んな角度からというのが曲者で、今回一番ドハマりしたところです。
**180度本当に色んな角度から全てのチェッカーが収まるようにカメラを移動して写真を撮ってください。**

弊環境はグレア液晶の4kディスプレイで、ごく普通のオフィスなので、天井が蛍光灯だらけです。
キャリブレーションツールで写真を撮る際、蛍光灯が反射してチェッカーが一箇所でも削れているとすぐ撮影失敗します。
そのため**蛍光灯反射が映らない正対、俯瞰視点の画像を多く撮りました。これがダメだったみたいです。**
![Inked3Keb-J_e_LI.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/241210/93aa38d9-1851-ff78-bda3-0f024c789cff.jpeg)
![Inkedtej91Zoy_LI.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/241210/726df40e-d2d4-bda6-2d15-c614daec7040.jpeg)


詳細は後述しますが、下から覗き上げるアングルの写真も取らないとキャリブレーションに失敗して、
歪んだ映像が出力されるようになります。
反射を防いで下からのアングルをいかにして撮るか？

こちら。

<blockquote class="twitter-tweet" data-partner="tweetdeck"><p lang="ja" dir="ltr">凹みさんのこのスレ見て、キャリブレーションの影響で動いてない可能性が出てきたので再キャリブしたんだけど、<br>うちの4kグレア画面なので、反射対策に黒布かぶってました<br><br>うまいこと動くようになった！黒布最強！<a href="https://t.co/5hnkVpvlIH">https://t.co/5hnkVpvlIH</a> <a href="https://t.co/d0QUN51t6E">pic.twitter.com/d0QUN51t6E</a></p>&mdash; ごんびぃー@RefrainCore (@GONBEEE_project) <a href="https://twitter.com/GONBEEE_project/status/1130407967369510912?ref_src=twsrc%5Etfw">May 20, 2019</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

**布をかぶる**
引きこもりプレイをすることで反射を防ぎ、180度全アングルから撮影できました。
お陰様で歪み映像が治り、きれいに映るようになった。
黒布最強！

25枚の画像の撮影が終わると自動的にデータがカメラに保存されます。
これでキャリブレーションは終了です。
コツさえ掴んでしまえばすぐできるので、頑張って覚えましょう。

# Unityへ
キャリブレーションでハマりすぎて正直ここは大してつらくないです。
備忘録。

## 1.パッケージインポート
公式の案内ではUnity5までしか対応していないとのこと。
多分2017でも動くとは思いますが、一応5系でプロジェクトを立ち上げましょう。

SDKダウンロードの項で落としてきたフォルダの
`ovrvisionprosdk_unity5\ovrvisionprosdk_unity5\unity5_ovrvision.unitypackage`をUnityにインポートしてください。
![SnapCrab_2019-05-21_13-09-58_No-0000.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/241210/ff03f462-e5e5-df40-2a15-82cd6b8c85cc.png)

インポート直後は1つエラーが出ています。
UnityEngineのxRサポート系は、前はUnityEngine.VRだった名前空間がいつしかUnityEngine.XRと変更されており、
おそらくこのパッケージはXR変更後に作られたパッケージで、Unity5ではまだVRだったのでエラーが出ていると思われます。

（あれ？そうすると2017以降を想定して作られているパッケージってことになる。5までサポートとは一体）
![SnapCrab_2019-05-21_13-11-28_No-0000.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/241210/f99fc9d6-f9c8-cfc2-ebeb-ebd7df92cdaa.png)
![SnapCrab_2019-05-21_13-14-03_No-0000.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/241210/98eb5947-e13d-ed7d-54f0-245d317a2870.png)

変更すべきは
`Ovrvision.cs`の136行目で
`UnityEngine.XR.`を消すだけで解消します。
適当に直しましょう。

これでインポートは終了です。そんなに難しくないですね。

## 2.Unity-OVRVisionの接続確認
`OvrvisionPro/Demos/camimage_demo`を開いて、Unity側に正しくデータが来ているかを確認します。
適当にPlayして、見覚えある形状のキューブに下記のようなカメラ映像が表示されたら成功です。
![SnapCrab_2019-05-21_13-19-09_No-0000.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/241210/164aebf7-2452-3e26-e8c1-630d20c85590.png)

もし出力されている映像が歪んでいたらもう一度キャリブレーションをしましょう。
歪みに関しては後述します。

## 3.自前でシーン構築
動作が確認できたら自分でシーンを構築していきましょう。
最終的な着地点としては
SteamVRのゴーグルでOVRVisionの映像を見るところとします。

適当なシーンを作り、OVRVision用のカメラを設置します。
![SnapCrab_2019-05-21_13-26-25_No-0000.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/241210/2b564ed5-0260-18c6-95f5-491c231833f6.png)
エディタ拡張で`OvrvisionPro`の項目が増えているので
`OvrvisionPro/AddOvrvisionProCamera`を押してシーンにカメラを設置してください。

いらないカメラを削除するかどうかのダイアログが出るのでYESを押して進みます。
![SnapCrab_2019-05-21_13-28-03_No-0000.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/241210/1a09f714-4d4d-27bd-67dd-a4161e426cfe.png)
![SnapCrab_2019-05-21_13-28-38_No-0000.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/241210/d2114599-515b-351e-f439-69fb28e83495.png)
ヒエラルキーがこのような構成になれば成功です。
この時点でもUnity公式のVRサポートを有効にするだけでも
OculusRift内で見えるようになりますが、
今回はSteamVRを使います。

## 4.SteamVR対応
SteamVR1.xをGitHubから持ってきてください。2.xはいい噂を聞かないんですが、どうなんですかね？

SteamVR対応はそんなに大変でもなく、シーンに[CameraRig]を設置するだけで終わります。
![SnapCrab_2019-05-22_13-53-47_No-0000.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/241210/252ff68a-7d3a-1a15-077a-97dd5f87175a.png)
（別プロジェクトでやったのでシーン名が違います、誤差誤差）

再生すればいい感じにシースルーで見えます。これでSteamVR対応は完了です。

##5.OVRVision画質最適化
シーン内に設置したOVRVisionProCameraオブジェクトを確認しましょう。
![SnapCrab_2019-06-05_13-43-32_No-0000.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/241210/6cce7e8a-15b3-263e-24f3-267bef905dea.png)
`Ovrvision`コンポーネントにいくつか設定項目があります。基本的に触らなくて問題ありません。
唯一変更すべきところは一番上のCameraModeです。
ドロップダウンになっているのでクリックすると、Cameraを起動する際の画質とフレームレートの組み合わせを設定できます。
![SnapCrab_2019-06-05_13-45-01_No-0000.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/241210/e2c6187e-6b39-b73a-20cd-df767170d76f.png)
VRゴーグルシースルーだとフレームレートが最低でも60は必須なので、
`960*950@60` `1280*800@60` `640*480@90`のいずれかを選択することになると思います。
これは作るコンテンツ別に好みの視野があると思われるので、色々試してみましょう。
おすすめは`960*950@60`です。画質、視野角、アスペクト比、フレームレートのバランスがよいです。



#キャリブレーションの闇
ここまでに何度か書いているように、OVRVisionのキャリブレーションには闇が潜んでいます。
まずはこの画像を見てください。
![SnapCrab_2019-05-20_17-25-54_No-0000.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/241210/25fea83e-a73c-f604-8afa-971d889ac296.png)
映像が全体的に歪み、黒い部分が表示されています。

別シーンの表示で見てみましょう。
![SnapCrab_2019-05-20_18-10-55_No-0000.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/241210/6a6d061a-b8d3-df12-c0cd-699fae57866c.png)

ギズモで表示されるアウトラインの領域が本来カメラで描画されるべき領域です。
しかし全体が右側に寄り、その映像が歪んで表示されています。

OVRVision公式提供の生データ確認ソフトでも見てみましょう。
![SnapCrab_2019-05-20_18-12-50_No-0000.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/241210/f81a2c43-ec4d-f315-d2c3-520fe8663273.png)
こちらもウィンドウ内全域に描画されるべきですが、
画面中央に寄って歪んでいます。
これは右カメラが描画されず、左カメラの映像が歪んで出力されている状況です。

これらの問題点は、OVRVision的にはなんのバグでもなく、普通に映像が出力されているということです。
当然ながらDebugLogにエラーも出ず、画質切り替えも普通に動作します。

これの原因がキャリブレーションミスによるものだとわかるまでかなりの時間を要しました。
恐らくこの歪み方は1例で、左カメラが描画されない版などもあると思われます。
もし映像出力が変な見え方になっている場合、真っ先にキャリブレーションミスを疑いましょう。
特にグレア画面を使っている人は注意が必要です。


#さいごに
ざっくりとOVRVisionをセットアップからUnityで使うまでの触り部分を記しました。
基本的に凹みさんのブログが参考になるので、まずはあちらを読むことをおすすめします。
恐らくもうOVRVisionを新規に触り始める人は多くないと思いますが、
きっと誰かの助けになると信じて、、、

最後まで読んでくださってありがとうございます。



