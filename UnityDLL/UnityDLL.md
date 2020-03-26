---
title: using UnityEditor; を含むDLLを作るときに気をつけること
tags: Unity dll UnityEditor
author: GONBEEE_project
slide: false
---
#2019/06/10更新
エディタ内でのみ動かして、ビルドで弾く方法がもう一つ見つかった&こちらのほうが対応が楽なので、
追記していきます。

#はじめに
コード保護などのためにC#スクリプトをDLL化する人向けの記事です。
DLLの中に`using UnityEditor;`を含むスクリプトが書かれており、それが原因でビルドが通らないことがあったので、
備忘録がてらまとめます。

#起きたこと
iOSビルドが通りませんでした。エラーログを見ると

```
ArgumentException: The Assembly UnityEditor is referenced by Hoge.Hoge
 ('Assets/Plugins/Hoge.Hoge.dll'). But the dll is not allowed to be included or could not be found.
```

が出力されていました。（ログは簡略化しています）
Hoge.Hoge内でUnityEditorの参照が必要だけどDLLが見つからないか、ビルドに含めることが認められてなかったよ
という旨のログです。

#原因・考察
これの原因は
`Hoge.Hoge.DLL`内でエディタ拡張のコードを書いていたことです。
今回書いていたコードはAttributeの拡張で、自作属性を宣言していました。

```PathAttributeDrawer.cs
[CustomPropertyDrawer(typeof(PathAttribute))]
public class PathAttributeDrawer : PropertyDrawer
//以下略
```
インスペクタ上にドラッグアンドドロップでフォルダのパスを指定できるエディタ拡張です。
参考:[Inspectorにファイルをドラック&ドロップしてパスを設定する](http://kan-kikuchi.hatenablog.com/entry/PathAttribute_1)
PropertyDrawerがUnityEditor下のコードで、UnityEditorを読み込む必要がありました。

本来ならばエディタ拡張のスクリプトは`Editor`ディレクトリに設置するものです。
しかし今回はDLLで包んでいるので、該当コードが`Plugins`ディレクトリに存在する扱いになっていました。

`Editor`ディレクトリはビルド時は自動的に除外され、ビルドに含まれることはありません。
そのため`UnityEditor`を参照するスクリプトを置いてあってもエラーは発生しませんでした。
今回は`Plugins`ディレクトリに存在したため、全てのコードがビルドに含まれてしまい、
本来ならば弾かれるべきコードが弾かれず、エラーを吐いてしまいました。

#対策その1
ビルド時に自動的に弾いてくれないので、スクリプト側で自分から弾かれるように
[プラットフォーム依存コンパイル](https://docs.unity3d.com/ja/2018.1/Manual/PlatformDependentCompilation.html)で区切りましょう。#defineディレクティブです。

UnityEditorで実行するときだけ走り、ビルドで走らなくするための#defineは
`#if UNITY_EDITOR`です。
今回書いたスクリプトは1クラス1ファイルのエディタ拡張のみのコードなので、全てを囲んでしまいました。

```PathAttributeDrawer.cs
#if UNITY_EDITOR
using UnityEditor;
using UnityEngine;
using System.Collections.Generic;

//中略

#endif
```
\#defineで囲んだことでUnityEditor外の環境ではコードが走らなくなり、
`Plugins`ディレクトリに存在してもコンパイルされなくなりました。
これでエラーが出ずにビルドが通るようになりました。

#結論その1
`Editor`ディレクトリ以外にエディタ拡張のスクリプトを設置すると、
ビルド時に`UnityEditor`の参照が足りなくなりエラーが出て通りません。
DLL化して`Plugins`ディレクトリに設置するとき（以外のケースもあるかも？）等、
エディタ拡張コードを`Editor`ディレクトリ以外に置くときは、
スクリプト丸ごと`#if UNITY_EDITOR`で囲んでコンパイルが走らないようにしましょう。

#対策その2（2019/06/10追記）
ビルド時に自動的に弾くように、[特殊フォルダ](https://docs.unity3d.com/ja/current/Manual/SpecialFolders.html)を使いましょう。
`Editor`ディレクトリや`Plugins`ディレクトリが使えるようです。

（上記リンクでは”PluginsフォルダはAssets直下・単一であるべき（意訳）”と記されていますが、
Unity5から複数Pluginsフォルダが並立しても動くようになりました。
参考：[Unity5からは"Assets/Plugins"フォルダは不要](http://anchan828.hatenablog.jp/entry/2014/10/04/004326)）

DLLでエディタ拡張を行う場合、該当コードをどのディレクトリに設置すればいいか謎でしたが、
いじっているうちに法則が見えたので備忘録として記しておきます。

まずDLLは必ず`Plugins`フォルダに入れておかないと動きません。
これに例外はありません。サブフォルダ内にあっても問題ありませんが、DLLのルートをたどっていくと必ずどこかで`Plugins`になるように設置してください。

次に`Editor`フォルダですが、これは
”ビルド時にこの中のコードは自動的にスキップして、コンパイルが通さないでください”と宣言するためのフォルダです。
開発時のエディタ上でのみ機能を追加するもので、ビルドには使用できないものを置いておく場所です。
こちらもサブフォルダ内にあっても問題ありません。

上記2点を踏まえて
”エディタ上でのみ機能するスクリプトを含んだDLL”を設置する場所を考えると
`Editor/Plugins`か`Plugins/Editor`の2つの候補が出てきます。
この2つのどちらを選択するかですが、そのDLLをどう管理したいかによって分けるといいでしょう。
まず`Editor/Plugins`ですがルートディレクトリが`Editor`なので、以下全てのフォルダがビルドから弾かれます。
そのためエディタ拡張のDLLのみを配置することになると考えられます。
`Plugins/Editor`はルートディレクトリが`Plugins`なので、全てのDLLを1フォルダ下にまとめることができます。
とりあえず全てのDLLを`Plugins`フォルダに配置して、エディタ拡張に関わるものだけを`Plugins/Editor`に配置しなおすことになるでしょう。

自分で作って自分で使うだけで完結するのであればどちらでも構いません。
アセットストアやその他の方法でDLLを含むパッケージを配布する場合は`Plugins/Editor`にして、DLLの配置場所が散らばらないようにするといいのではないでしょうか。

#結論その2（2019/06/10追記）
こちらはDLL内で#defineをしなくて済むので、コードの編集時に余計な気を使わなくてもいいという利点があります。
ただ、エディタ拡張のコードのみを含むDLLを別で出力しないといけないという点がデメリットになり得ます。
最終的には好みの問題ですが、#defineをすると編集時にコード補完が起動しなかったり該当プラットフォームでないときにコメントアウトされてしまうので、個人的にはあまり好きではありません。

#さいごに
対策方法を教えてくださった[アズマゴローさん](https://twitter.com/azumagoro)ありがとうございました！！
<blockquote class="twitter-tweet" data-partner="tweetdeck"><p lang="ja" dir="ltr">using UnityEditor; を要するコードはエディター上でしか使えないんですよね。Editorディレクトリ以下やifで囲うとビルドから除かれます。たまにストアから購入したアセットが考慮してくれてなくてビルドできなくなるパターンがあります。</p>&mdash; アズマゴロー@モンスター合成ハクスラ開発中 (@azumagoro) <a href="https://twitter.com/azumagoro/status/1136897363472031745?ref_src=twsrc%5Etfw">June 7, 2019</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

おかげさまで仕事が進みます。助かりました。
ここまで読んでくださった皆様もありがとうございました！

