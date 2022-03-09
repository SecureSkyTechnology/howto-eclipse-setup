howto-eclipse-setup : setup-type4
===============

setup-type4 対象:

- Eclipse 2021-12 R (4.22) 以上
  - https://www.eclipse.org/downloads/packages/
  - "Eclipse IDE for Enterprise Java and Web Developers" を推奨。
  - 4.22 以降のバージョンでもおおよそ動くと思います。プラグインだけ対応度合いに差があるかもしれません。

## Eclipse のインストール

1. https://www.eclipse.org/downloads/packages/ より　"Eclipse IDE for Enterprise Java and Web Developers"　(64bit)をダウンロード
1. ダウンロードしたらzipを展開するが、 **Windowsの場合はなるべくパス名が短くなるようにする。** (プラグインフォルダ・ファイル名などでWindowsのパス名の長さ制限を超える場合があるため、フォルダを深く掘った先に展開しないよう注意。展開時にエラーが出たら、より短いパス名になるよう、フォルダ階層を上にずらすこと)
1. eclipse.exeを起動して動作確認する。

NOTE: 2021-12 R 時点で Eclipse のダウンロードパッケージにJREが同梱されており、デフォルトの `eclipse.ini` はそれを使うようプリセットされている。どうしてもEclipse実行に使うJRE/JDKを変更したい場合のみ `eclipse.ini` の `-vm` オプションをカスタマイズする。カスタマイズ方法は [setup-type3のREADME.md](../setup-type3/README.md) を参照。

### JDKのインストール

[Eclipse には Java コンパイラが内蔵されており](https://stackoverflow.com/questions/47623117/which-java-compiler-is-used-by-eclipse)、簡単なJavaアプリであれば Eclipse 単体でコンパイルと実行が可能。
しかしより複雑なJDKライブラリを使うプロジェクトであったり、JDKの提供するコマンドラインツールを必要とする場合は別途 JDK をインストールする。

2022-03時点でのJDK(OpenJDK)の主な提供元:

- Linux ディストリビューションの提供する OpenJDK パッケージ
- [Adoptium - Open source, prebuilt OpenJDK binaries](https://adoptium.net/)
- [Red Hat build of OpenJDK Download | Red Hat Developer](https://developers.redhat.com/products/openjdk/download)
- [OpenJDK: Download and install](https://openjdk.java.net/install/)
- (OpenJDKパッケージについてプロジェクトで指定があれば、そちらに従うこと。)

インストールしたら `Window` -> `Preferences` -> `Java` -> `Installed JREs` -> `Add` -> `Standard VM` で Eclipse に登録する。

NOTE: Windows の場合、インストーラかzipファイルかを選べる。既にインストーラ形式でJDKをインストール済みの場合は、衝突しないよう zip ファイルをダウンロードして手動で展開すればOK(Eclipseから利用できれば良いので、PATH設定は不要)。そうでなくて、そのPCに初めてJDKをインストールする場合なら、インストーラを選択してお任せでインストールしてもOK。

### Spring Tools 4 のインストール

setup-type3 までは Sprint Tools 4 単体のインストールを紹介していたが、プラグインとしてインストールした方が "Eclipse IDE for Enterprise Java and Web Developers" に含まれる各種ツール類もそのまま使えるので、 setup-type4 ではそちらのやり方を推奨する。

- [Spring Tools 4 の Eclipse Marketplaceページ](https://marketplace.eclipse.org/content/spring-tools-4-aka-spring-tool-suite-4)

## おすすめプラグイン

Eclipse の "Eclipse IDE for Enterprise Java and Web Developers" パッケージには以下のプラグインが同梱されているため、Gitでアクセスする一般的なMaven/Gradleプロジェクトであればそのままインポートできる。

- EGit(Gitクライアント)
- m2e(Mavenプラグイン)
- m2e-wtp(MavenプロジェクトでEclipse WebToolsProjectを使うためのプラグイン)
- Eclipse Buildship: Eclipse Plug-ins for Gradle(Gradleプラグイン)
- JUnit実行用プラグイン

その他のおすすめプラグイン:

- [Eclipse Checkstyle Plugin](https://checkstyle.org/eclipse-cs/)
  - https://checkstyle.org/eclipse-cs/
  - Eclipse Marketplace から `Checkstyle` で検索してインストール可能
- [SpotBugs Eclipse plugin](https://spotbugs.readthedocs.io/en/latest/eclipse.html)
  - https://spotbugs.readthedocs.io/en/latest/eclipse.html
  - Eclipse Marketplace から `SpotBugs` で検索してインストール可能
- [PMD Eclipse Plugin](https://github.com/pmd/pmd-eclipse-plugin)
  - https://github.com/pmd/pmd-eclipse-plugin
  - Eclipse Marketplace から `PMD` で検索してインストール可能
  - [eclipse-pmd](https://marketplace.eclipse.org/content/eclipse-pmd) というプラグインも人気がある。
    - https://github.com/eclipse-pmd/eclipse-pmd
    - https://eclipse-pmd.github.io/index.html
- [Lombok](https://projectlombok.org)
  - https://projectlombok.org
  - (詳細は後述)
- [WindowBuilder](https://www.eclipse.org/windowbuilder/)
  - https://www.eclipse.org/windowbuilder/
  - Swing や SWT のWYSIWYGエディタ(詳細は後述)

## Workspaceのフォントや見た目のおすすめ設定

`Window` メニュー -> `Preferences` から:

- フォントのカスタマイズ：
  - `[General]` -> `[Appearance]` -> `[Colors and Fonts]` -> `Basic` -> `Text Font` : お好みの等幅フォントに変更。ソースコードエディタ・コンソールViewなどに表示するフォントになる。
- 行番号と空白文字の表示
  - `[General]` -> `[Editors]` -> `[Text Editors]` -> `Show line numbers` と `Show whitespace characters` にチェックを入れる。
- ファイルセーブ時に使ってない import 文を消す
  - `[Java]` -> `[Editor]` -> `[Save Actions]` -> `Perform the selected actions on save` にチェックを入れ、`Organize imports` にチェックを入れる。
- 補完時に上書きする
  - `[Java]` -> `[Editor]` -> `[Content Assist]` -> `Complletion overwrites` にチェックを入れる。
- [JUnit5](https://junit.org/junit5/) や [AssertJ](https://joel-costigliola.github.io/assertj/) の assertion を補完できるようにする。
  - `[Java]` -> `[Editor]` -> `[Content Assist]` -> `[Favorites]` に以下を追加
  - `org.junit.jupiter.api.Assertions` : JUnit5
  - `org.assertj.core.api.Assertions` : AssertJ
  - [mockito](https://site.mockito.org) を使う場合は、以下を追加すると良い。
  - `org.mockito.ArgumentMatchers`
  - `org.mockito.Mockito`
- どこで「;」を押しても行末にいれる
  - `[Java]` -> `[Editor]` -> `[Typing]` -> `Automatically insert at correct position` の `Semicolons` にチェックを入れる。
- クラス名なのか変数名なのかわかりやすくするためのSyntax Coloring
  - `[Java]` -> `[Editor]` -> `[Syntax Coloring]` -> Classes,Enums,IntefacesなどをEnable,Boldにする。
- `com.sun` 以下, `sun` 以下, java.awt.List や Swingパッケージを補完対象から外す
  - `[Java]` -> `[Appearance]` -> `[Type Filters]` -> `Add` ボタンで、`com.sun.**`, `sun.*`, `java.awt.*`, `javax.swing.*` を追加
  - その他使っているライブラリに応じて、アプリケーション側では使わないパッケージを追加するなどしてチューニングしておくと、import補完などの精度が向上する。
- Workspaceでの改行コードや文字コード
  - `[General]` -> `[Workspace]` で変更。（最近はデフォルトでUTF-8になってる。改行もデフォルト任せでよい）
- JSPファイルの文字コード
  - `[General]` -> `[Content Types]` で"Text" - "JSP"のDefault encodingをUTF-8に変更
  - `[Web]` -> `[JSP Files]` でEncodingを設定 (Mars.2だとデフォルトでUTF-8になってた)

## パースペクティブ(Perspective), ビュー(View)のおすすめ設定

パースペクティブ(Perspective):

- `Java EE` は使わなければcloseしてOK.(機能が豊富だが、使わない機能が多ければ `Java` パースペクティブで十分)
- `Window` -> `Perspective` -> `Open Perspective` から以下のパースペクティブをOpenしておくのがおすすめ。
  - `Java`, `Debug`, `Git`

`Java` パースペクティブで必須のView : `Window` -> `Show View` -> `Other...` から表示できる。

- `Java` -> `Package Explorer`
  - プロジェクト内の論理的なエクスプローラ。Eclipseプロジェクトの設定ファイルなど非表示。
- `General` -> `Navigator`
  - プロジェクトファイル全てのエクスプローラ。Eclipseプロジェクトの設定ファイルなども表示。
- `General` -> `Outline`
  - 開いたファイルの論理構成アウトラインを表示。Java, XMLなどに対応。
- `Java` -> `Type Hierarchy`
  - Javaソースのクラス継承の階層構造を表示。
- `General` -> `Problems`
  - コンパイルエラーや警告、その他のエラーなど表示。
- `General` -> `Console`
  - Java/Junitテスト実行時の標準入出力, エラー出力
- `General` -> `Progress`
  - workspaceやプロジェクトのビルドなど様々な処理の進捗状況を表示。これがないと、裏側で何が行われているのか分からなくなる。
- `General` -> `Tasks`
  - `TODO` などでマークした箇所を一覧表示。
- `General` -> `Error Log`
  - Eclipse 自体の実行時のエラーや警告、情報ログなどが表示される。
- `Server` -> `Servers`
  - Webアプリ(Servletなど)実行時に使うサーバ設定を登録・表示。
- `Checkstyle` -> `Checkstyle violations`
  - Checkstyle の検出結果を表示。(Checkstyleを使う場合は有用)
- `SpotBugs` -> `Bug Explorer`
  - FindBugs の検出結果を表示。(FindBugsを使う場合は有用)
- `General` -> `Search`
  - 検索結果を表示。
- `Java` -> `JUnit`
  - JUnitテスト実行結果を表示。

## GitやSubversionでチェックアウトしたMavenプロジェクトをインポートするには

1. gitでリポジトリをcloneする, Subversionでリポジトリを checkout する。
2. Eclipseを起動し、File -> Import を開く。
   1. import source で Maven -> Existing Maven Projects を選択
   2. Root Directory で本ディレクトリを選び、pom.xmlが認識されればそのままインポートできる。

## Cleanup/Formatterのインポート

- Cleanup (Javaソースファイル保存時の自動処理) 設定のインポート:
  - `Window` メニュー -> `Preferences` -> `[Java]` -> `[Code Style]` -> `[Clean Up]` -> `[Import ...]` からインポートする。
- Formatter (Javaソースコードのフォーマッタ) 設定のインポート:
  - `Window` メニュー -> `Preferences` -> `[Java]` -> `[Code Style]` -> `[Formatter]` -> `[Import ...]` からインポートする。

## EGitからSSHでcloneするときの秘密鍵を指定するには

https://wiki.eclipse.org/EGit/User_Guide/Remote より, `Windows` メニュー -> `Preferences` -> `[General]` -> `[Network Connections]` -> `[SSH2]` で、以下の項目をチェック。

- "General" タブで、"SSH2 Home" は適切か？
- "General" タブで、"Private keys" は適切な秘密鍵ファイル名が設定されているか？
- "Key Management" タブで、 "Load Existing Key..." で秘密鍵の読み込みは試したか？
- "Authentication Methos" タブで、"password" のチェックを外したか？（外してなければ、外す）

なおこちらで扱えるのは OpenSSH 系のツールで生成した秘密鍵。putty系やWinSCP系のツールで生成した秘密鍵はそのままでは扱えないので、OpenSSH系にエクスポートしたものを使用するよう注意が必要。

## EclipseにLombokをインストールする

https://projectlombok.org/

1. 上記公式サイトより lombok.jar をダウンロードする。
   - GUIのインストールアプリを起動する実行可能jarファイルになっている。
1. `java -jar lombok.jar` を実行する。
   - Linux の場合はデスクトップ環境で実行すること。
1. GUIのインストールアプリが起動したら、Eclipse の展開先ディレクトリを指定してインストールする。

参考：

- Lombok - Qiita
  - http://qiita.com/yyoshikaw/items/32a96332cc12854ca7a3
- Lombok 使い方メモ - Qiita
  - http://qiita.com/opengl-8080/items/671ffd4bf84fe5e32557

## WindowBuilder を使う

https://www.eclipse.org/windowbuilder/

GUIツールキットとしてSwingやSWTを使う場合、Eclipseであれば [WindowBuilder](https://www.eclipse.org/windowbuilder/) をインストールするとグラフィカルにコンポーネントを配置することができる。

Eclipse Marketplace からインストールできる。

NOTE: Eclipse 2021-12 R で 1.9.7 を入れてみたところ、[デザイナ画面が正常に表示されない既知のバグ](https://github.com/eclipse/windowbuilder/issues/122) に遭遇した。
-> **2021-12 R では 1.9.8 の Nightlybuild をインストールすること。**

使い方の参考記事：

- 開発メモ SwingDesignerのインストールと使用
  - http://developmentmemo.blog.fc2.com/blog-entry-140.html
- javaで超簡単にGUIを作成するためのEclipseプラグイン「SwingDesigner」 インストール - うめすこんぶ
  - http://konbu13.hatenablog.com/entry/2013/12/25/230637
- 「SwingDesigner」でSwingアプリケーションをつくろう! その2～アプリケーション新規作成とコンポーネント配置 - うめすこんぶ
  - http://konbu13.hatenablog.com/entry/2013/12/27/163202

