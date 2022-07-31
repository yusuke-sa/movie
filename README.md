# ネイティブアプリをFlutterにリプレイスする際に発生する問題
- 下記のイベントに参加し特に勉強になった点について記事をあげます
  - https://techplay.jp/event/861948

## リプレイス時に発生した問題
### Flutter起因の問題
#### **①WebView周りでの不具合**
- Cookie制御
- mixed contentsがある場合の表示不具合
    - httpsとhttpが混在している場合上手く表示されない
- JavaScriptの動作不具合
    - 期待通りの動作にならない
- ##### SHOPLISTでの対応方法
  - リリース直前にWebViewのパッケージをwebview_flutterからflutter_inappwebviewに変更
  - ネイティブアプリからWebViewに対してデータ連携をする場合はCookieではなくHttpHeaderに変更
  - mixed contentsのような不具合は、表示するHTMLコンテンツでDeveloperConsole等でエラーや警告がない状態にしておくほうが良い

#### **②キーボード入力直後にクラッシュが発生**
- テキストボックスからフォーカスが外れたタイミングでアプリがクラッシュや動作や通信が重くなる
- ##### 原因
  - キーボード表示時にMediaQueryがRebuildを実行していたためパフォーマンスが悪くなっていた

#### **③習得コスト**
- UIを作る際にどのクラスを使えばいいかわからない
  - ナレッジ依存になるためひたすらTry＆Errorで確認

### リプレイス起因の問題
#### **正常系の動作がわからない**
- 追加機能、追加機能でドキュメント更新が間に合ってない
- リリースを優先したためそもそもドキュメントがない
  - 今動いているものが正常と判断せざる負えない状況に

### その他の問題
#### **中華製Android端末固有の不具合**
- Huawei端末で発生
  - WebView内でテーブルタグが正しく展開できない
    - デバイスごとにWebViewのパラメータを調整することで対応
  - メモリ消費が多く、特定画面で固まる、メモリリークでOSが再起動される
    - デバイスごとにスタックする画面数や表示する商品の写真枚数を減らすことで対応

 ## 資料
- 年間300万人が利用するファッション通販アプリをFlutterでリプレイスした話
  - https://speakerdeck.com/crooz/techhills-nian-jian-300mo-ren-gali-yong-suruhuatusiyontong-fan-apuriwoflutterderipureisusitahua
- 大規模な「じゃらんnet」アプリを段階的にFlutter化している話
  - https://speakerdeck.com/recruitengineers/kurosupuratutohuomukai-fa-2022-flutterreact-nativefalsedao-ru-toshi-jian 
### Flutterおすすめ勉強方法
- Flutterは公式ドキュメントが豊富なのでそれを見ればほとんどの情報を得られる
  - https://docs.flutter.dev/
- 公式YouTubeの再生リストFlutter Widget of the Week
  - https://www.youtube.com/watch?v=-Nny8kzW380&list=PLjxrf2q8roU23XGwz3Km7sQZFTdB996iG
    - レイアウト実装の際の引き出しが増える
    - 日本語字幕があるので安心
    - １つのWidgetやパッケージについて2分弱の動画であるためお手軽

## 感想
- 事例になるので確実にこれらの問題が発生するとは言えない
- Flutterは成長段階なので、すでに改善されている可能性も高い
  - 頭の片隅に残しとく程度でよさげ

### webview_flutterについて深堀り
- ネットで調べてみてもwebview_flutterでcookie制御が上手くできないというのはなかった
  - issueも上がってなさそう https://github.com/flutter/flutter/issues?q=is%3Aissue+label%3A%22p%3A+webview%22+
- 実際に以下のテストプログラムを動かしてみたところCookieの取得、設定は期待通りに動作した
  - https://github.com/flutter/codelabs/tree/master/webview_flutter/step_11
  - 端末：GooglePixel 3XL(Android12)
  - ※Cookieの設定時のJavaScriptのコードに誤りがあるので注意<br>
      - Cookieのセットは1つずつしかできないので<br>
      document.cookie = "FirstName=John;"<br>
      document.cookie = "Message=Hello World;"<br>
      のようにする必要がある
- どこかのアップデートでCookie関連の不具合が解消された可能性がある
  - https://pub.dev/packages/webview_flutter/changelog
