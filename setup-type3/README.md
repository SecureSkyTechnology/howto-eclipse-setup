howto-eclipse-setup : setup-type3
===============

setup-type3 対象:

- Eclipse 2019-06 (4.12 以上)
  - https://www.eclipse.org/downloads/packages/
  - "Eclipse IDE for Enterprise Java Developers" がオススメ。
  - 以降の説明は 4.10以降でも大体動くと思います。プラグインだけ対応度合いに差があるかもしれません。
- Spring Tools 4 for Eclipse (4.3.1.RELEASE 以上)
  - https://spring.io/tools
  - ベースの Eclipse は 4.12 以上となっています。

## Eclipse / STS のインストール

### JDKのインストール

Oracle JDK 以外にも、OpenJDK公式 / AdoptOpenJDK / LibericaJDK など複数のJDKベンダーから OpenJDK ベースのインストーラが提供されている。OpenJDKベースであれば機能はどれも同じで、パッチ状況やサポートサービスなどで違いを出している。

setup-type3 の推奨としては、AdoptOpenJDK の OpenJ9 ベースのJDK11がオススメ。(OpenJ9 の方が HotSpot よりも体感で若干速い気がする。また、Eclipse も OpenJ9 もIBM出生なので相性の良さを期待できる)

- https://adoptopenjdk.net
- "Other platforms" -> "1. Choose a Version" で "OpenJDK 11 (LTS)" を選、 "2. Choose a JVM" で "OpenJ9" を選択、プラットフォームで適切なものを選択して、"JDK" の方をダウンロードする。("JRE"ではない。)
- Windows の場合、インストーラかzipファイルかを選べる。既にインストーラ形式でJDKをインストール済みの場合は、衝突しないよう zip ファイルをダウンロードして手動で展開すればOK(Eclipseから利用できれば良いので、PATH設定は不要)。そうでなくて、そのPCに初めてJDKをインストールする場合なら、インストーラを選択してお任せでインストールしてもOK。

もちろん他のJDKベンダや、HotSpotベースのJDKを選んでもらっても構わない。よほど厳密さが求められたり特殊な環境出ない限りは、どれを選んでも違いは無いだろう。

### Eclipse のインストール

1. https://www.eclipse.org/downloads/packages/ より「Eclipse IDE for Enterprise Java Developers」(64bit)をダウンロード
1. ダウンロードしたらzipを展開するが、 **Windowsの場合はなるべくパス名が短くなるようにする。** (プラグインフォルダ・ファイル名などでWindowsのパス名の長さ制限を超える場合があるため、フォルダを深く掘った先に展開しないよう注意。展開時にエラーが出たら、より短いパス名になるよう、フォルダ階層を上にずらすこと)
1. `eclipse.exe` と同じフォルダにある `eclipse.ini` をコピーしてバックアップした後、 `eclipse.ini` をエディタやメモ帳で開く。
1. `-vmargs` の前に、`-vm` + 改行 + JDKの `javaw.exe` までのフルパスを挿入する。(Windows以外の場合は `java` 実行ファイルのフルパス)
   - ファイルの先頭にいきなり挿入しても大丈夫かも。
1. eclipse.exeを起動する。
1. 起動したら `Window` メニュー -> `Preferences` -> `[Java]` -> `[Installed JREs]` と辿り、eclipse.ini に挿入した `-vm` で指定したところのJDKの場所が認識されたことを確認する。
   - もし他のJDKやJREだけが見えてしまう場合、手動で追加し直す。(JDK/JREの追加方法については書籍やWeb記事などを適宜参照のこと)

`-vm` オプション挿入の実例:

```
...
--launcher.appendVmargs
-vmargs
-Dosgi.requiredJavaVersion=1.7
...
```

->

```
...
--launcher.appendVmargs
-vm
C:/work/jdk/adoptopenjdk-win-x64/openj9_jdk-11.0.3_7/bin/javaw.exe
-vmargs
-Dosgi.requiredJavaVersion=1.7
...
```

### STS のインストール

1. https://spring.io/tools より 「Spring Tools 4 for Eclipse」をダウンロード
1. ダウンロードしたらzipを展開するが、 **Windowsの場合はなるべくパス名が短くなるようにする。** (プラグインフォルダ・ファイル名などでWindowsのパス名の長さ制限を超える場合があるため、フォルダを深く掘った先に展開しないよう注意。展開時にエラーが出たら、より短いパス名になるよう、フォルダ階層を上にずらすこと)
1. `SpringToolSuite4.exe` と同じフォルダにある `SpringToolSuite4.ini` をコピーしてバックアップした後、 `SpringToolSuite4.ini` をエディタやメモ帳で開く。
1. ファイルの先頭に `-vm` + 改行 + JDKの `javaw.exe` までのフルパスを挿入する。(Windows以外の場合は `java` 実行ファイルのフルパス)
1. `SpringToolSuite4.exe` を起動する。
1. 起動したら `Window` メニュー -> `Preferences` -> `[Java]` -> `[Installed JREs]` と辿り、eclipse.ini に挿入した `-vm` で指定したところのJDKの場所が認識されたことを確認する。
   - もし他のJDKやJREだけが見えてしまう場合、手動で追加し直す。(JDK/JREの追加方法については書籍やWeb記事などを適宜参照のこと)

### Eclipse/STS インストール時のよくある落とし穴

1. Eclipse/STSのzipをWindowsで展開したとき、展開先のパス名が長くなり、プラグインフォルダ・ファイル名などでパス名の長さ制限を超えてしまい、一部で展開が失敗する。
   - パス名が短くなるよう、なるべくドライブから浅いフォルダに展開するようにする。
2. JDKではなくJREで動かしてしまうことにより、JDK依存機能が使えない / JRE側の自動更新による予期せぬ副作用でトラブル。
   - 必ず手動でJDKを入れて、 eclipse.ini の `-vm` オプションで使用する `javaw.exe` を明示的に指定する。
   - OracleJDKのインストーラなどで、インストール時にコンポーネント選択の画面でJREが選択されている場合はJREのチェックを外して一緒にJREもインストールしないようにする。(インストール済みのJREと衝突するなどしてトラブルの遠因となる)

## おすすめプラグイン

- Eclipseの「Eclipse IDE for Enterprise Java Developers」とSpring Tools4 は、ともに以下のプラグインが同梱されているため、Gitでアクセスする一般的なMavenプロジェクトであればそのままインポート可能です。
  - EGit(Gitクライアント)
  - m2e(Mavenプラグイン)
  - m2e-wtp(MavenプロジェクトでEclipse WebToolsProjectを使うためのプラグイン)
  - JUnit実行用プラグイン
- その他のおすすめプラグインについては開発内容によって変わりますが、こちらの setup-type3 を使っている開発プロジェクトでは追加で以下のようなプラグインを使っていたりします。

- [Eclipse Data Tools Platform(DTP)](https://www.eclipse.org/datatools/downloads.php)
  - https://www.eclipse.org/datatools/downloads.php
  - Spring Tools 4の場合、SQLエディタなどDBアクセス用ツールが同梱されていないため、手動で追加する必要がある。(Eclipse IDE for Enterprise Java Developers には同梱されてる)
  - Spring Tools 4 の "Help" -> "Install New Software" -> "Work with: " に、最新ビルドの update site のURLを入力し、"Eclipse Data Tools" にチェックを入れてインストールする。
- [Eclipse Checkstyle Plugin](https://checkstyle.org/eclipse-cs/)
  - https://checkstyle.org/eclipse-cs/
  - Eclipse Marketplace から `Checkstyle` で検索してインストール可能
- [SpotBugs Eclipse plugin](https://spotbugs.readthedocs.io/en/latest/eclipse.html)
  - https://spotbugs.readthedocs.io/en/latest/eclipse.html
  - Eclipse Marketplace から `SpotBugs` で検索してインストール可能
- [PMD Eclipse Plugin](https://github.com/pmd/pmd-eclipse-plugin)
  - https://github.com/pmd/pmd-eclipse-plugin
  - Eclipse Marketplace から `PMD` で検索してインストール可能(類似プラグインがあるので注意)
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

1. lombok.jar をインストールして実行し、EclipseにLombokをインストールする。
   - https://projectlombok.org/

参考：

- Lombok - Qiita
  - http://qiita.com/yyoshikaw/items/32a96332cc12854ca7a3
- Lombok 使い方メモ - Qiita
  - http://qiita.com/opengl-8080/items/671ffd4bf84fe5e32557

## WindowBuilder を使う

GUIツールキットとしてSwingやSWTを使う場合、Eclipseであれば [WindowBuilder](https://www.eclipse.org/windowbuilder/) をインストールするとグラフィカルにコンポーネントを配置することができる。

- https://www.eclipse.org/windowbuilder/

Swing Designer の導入方法:

1. Help -> Install New Software の "Work with:" で `YYYY-MM - https://download.eclipse.org/releases/YYYY-MM` (YYYY-MM はバージョンによって異なる)をプルダウンから選択する。
   - 意図としては、Eclipse本体のプロジェクトなので、使用しているEclipseのバージョンに応じた公式のリリースダウンロードURLを選択する。
2. "Swing Designer" でフィルタし、"Swing Designer" にチェックを入れてインストールする。
3. 既存のSwingコンポーネントのJavaソースを開く時は、"Open With" => "WindowBuilder Editor" で開く。

※ Windows Builder というコンポーネントも選べるが、"Swing Designer" を入れれば Windows Builder 系も自動で入る。

※ "SWT Designer" を入れれば SWT 系のWYSIWYGエディタが使えるようになる。用途に応じてインストールすること。

使い方の参考記事：

- 開発メモ SwingDesignerのインストールと使用
  - http://developmentmemo.blog.fc2.com/blog-entry-140.html
- javaで超簡単にGUIを作成するためのEclipseプラグイン「SwingDesigner」 インストール - うめすこんぶ
  - http://konbu13.hatenablog.com/entry/2013/12/25/230637
- 「SwingDesigner」でSwingアプリケーションをつくろう! その2～アプリケーション新規作成とコンポーネント配置 - うめすこんぶ
  - http://konbu13.hatenablog.com/entry/2013/12/27/163202

