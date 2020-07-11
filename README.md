# flutter_app_test

A new Flutter application.

## Getting Started

This project is a starting point for a Flutter application.

A few resources to get you started if this is your first Flutter project:

- [Lab: Write your first Flutter app](https://flutter.dev/docs/get-started/codelab)
- [Cookbook: Useful Flutter samples](https://flutter.dev/docs/cookbook)

For help getting started with Flutter, view our
[online documentation](https://flutter.dev/docs), which offers tutorials,
samples, guidance on mobile development, and a full API reference.

# Flutter Code Lab first app part1

## このCodeLabの説明
このコードラボでは、シンプルなモバイルFlutterアプリを作成します。
オブジェクト指向のコードと、変数、ループ、条件などの基本的なプログラミングの概念に精通している場合は、コードラボを完了することができます。
Dart、モバイル、またはWebプログラミングの経験は必要ありません。

### part1でやること
- Flutter でAndroid,iOS、Webアプリを作る方法
- ホットリロードを使用する方法
- StateFul Widgetの使用方法
- 遅延読み込みのリストを表示する方法

### 無限にスクロールが続く、リストを表示するアプリを作ります

## セットアップと環境設定
```
$ flutter channel beta
$ flutter upgrade
$ flutter config --enable-web
```
FlutterをBeta版にすることでWeb対応も可能です。
今回はAndroid、iOSのみのサポートのため、できる方はここで進めていただけたらと思います。

## Flutterスタートアプリを作成する
1. 名前を「startup_namer」に変更する
2. Use androidx.* artifactsにチェック
3. include Kotlin ~ include Swift~にチェック
4. main.dart を以下のコードに置き換えます  

![](./new_project.png)

```
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Welcome to Flutter',
      home: Scaffold(
        appBar: AppBar(
          title: const Text('Welcome to Flutter'),
        ),
        body: const Center(
          child: const Text('Hello World'),
        ),
      ),
    );
  }
}
```
![](./first_app_hello_world.png)


- アプリはStatelessWidgetを拡張し、アプリ自体をウィジェットにします。
Flutterでは、配置、パディング、レイアウトなど、ほとんどすべてがウィジェットです。
- Scaffold ウィジェットはその名の通り足場として配置するウィジェットです。
このウィジェットはappBar(アプリ上部のタイトルや戻る矢印などを表示する領域)、ウィジェットツリーを管理するbodyプロパティを提供します。
- ウィジェットの役割は、他の下位レベルのウィジェットを表示するbuildメソッドを提供することです。
- Centerウィジェットは子のWidgetを中央に配置するウィジェット。
- Textウィジェットはテキストを表示させるWidget

## 外部パッケージを利用する
- english_words という外部パッケージを利用してみます
- 数千の英単語とユーティリティ関数が含まれています
- Flutterで使える外部のパッケージ(ライブラリやプラグイン)は、pub.devで見つけることができます

### 1. pubspec.yamlでenglish_wordsを追加します
```
dependencies:
  flutter:
    sdk: flutter

  cupertino_icons: ^0.1.2
  english_words: ^3.1.5   # add this line
```
### 2.flutter packages get または、pubspec.yamlを開いてるさいに上部に表示される[Packages get]をクリックします。
(公式ではpackages get と書かれていますが、pub get かもしれません。)

### 3. main.dartにパッケージをインポートします。最上部に以下のコードを追加
```
import 'package:english_words/english_words.dart';
```
### 4. bodyのTextウィジェットに表示させるものを"HelloWorld"からenglish_wordsから文字を生成するように変更します。
```
final wordPair = WordPair.random();

body: Center(
          child: Text(wordPair.asPascalCase),
    ),
```
### 5. 更新する、またはAndroidStudioの雷のようなマークを押すことでHotReloadが走り、表示されるランダムな文字が更新されます。

## Stateful Widgetを使う
現在main.dartで使われているStatelessWidgetは不変でプロパティを更新できません。
つまり、ランダムで文字列が生成されるのは画面が作成された時のみで、その後更新されることはありません。

StatefulWidget は 変化する可能性のあるState(状態)を維持します。
StatefulWidgetを使うには2つのクラスが必要です。

Stateクラスの実体を展開するStatefulWidget。
Stateオブジェクトはウィジェットの存続期間中持続します。

別ファイルでWidgetを生成することもできますが、このCodeLabではmain.dartファイル内に作っていきます。

###  1. stfulと打つと候補が出てきます。Return(Enter)を二回押すと、StatefulWidget生成のコードが自動生成されます。  class名をRandomWordsとすると、一緒に生成されたStateのclass名も「_RandomWordsState」となります。
RandomWords クラスは Stateクラスを作成する以外にほとんど何もしません。State側に処理や画面の情報を書いていきます。

###  2. _RandomWordsState の build を以下の処理に置き換えます。
```
final wordPair = WordPair.random();
return Text(wordPair.asPascalCase);
```

###  3. MyApp の Text を削除し、以下のように変更します。
```
//child: Text(wordPair.asPascalCase), // ここを以下に置き換える
child: RandomWords(),
```

### 4. 上書き保存でHotReloadが走り画面の表示が切り替わります。
現時点ではStatefulWidgetとStateを持たせる必要はありませんが、次章で必要になってきます

## 無限にスクロールしてランダムなテキストを表示するListViewを作成する

### 1. WordPairを保存するためのList(_suggestionsとして)を追加、フォントスタイルを変更するために_biggerFontを追加します。コードは以下になります。
```
final List<WordPair> _suggestions = <WordPair>[];
final TextStyle _biggerFont = const TextStyle(fontSize: 18);
```
### 2. _buildSuggestions（）関数を_RandomWordsStateクラスに追加します。 WordPairを表示するListViewを生成する関数になります。_RandomWordsState に以下の関数を追加します。

```
Widget _buildSuggestions() {
    return ListView.builder(
        padding: const EdgeInsets.all(16),
        itemBuilder: (BuildContext _context, int i) {
        if (i.isOdd) {
            return Divider();
        }
        final int index = i ~/ 2;
        // indexが_suggestionsのあたいまでいったら10個さらに作成する 永遠に続くので無限にListが表示される
        if (index >= _suggestions.length) {
            _suggestions.addAll(generateWordPairs().take(10));
        }
        return _buildRow(_suggestions[index]);
        });
}
```

### 3. ListViewで表示するitem を表示するための関数を _RandomWordsState に追加します。

```
Widget _buildRow(WordPair pair) {
return ListTile(
    title: Text(
    pair.asPascalCase,
    style: _biggerFont,
    ),
);
}
```

### 4._RandomWordsStateのbuildメソッド内を以下のように変更します。Scaffoldも_RandomWordsStateで生成するように変更します。
```
@override
Widget build(BuildContext context) {
return Scaffold (
    appBar: AppBar(
    title: Text('Startup Name Generator'),
    ),
    body: _buildSuggestions(),
);
}
```

### 5.MyAppのbuildメソッドのhomeを以下のように変更します。
```
home: RandomWords()
```

# Flutter Code Lab first app part2

## 1.CodeLabの説明
このコードラボでは、part1のアプリを拡張して、インタラクティブ機能を組み込みます。
また、二つ目の画面を作成します。最後に、アプリのテーマ（色）を変更します。

### part2でやること
- StateFul Widgetに機能を追加する方法
- セカンドスクリーンを作成してナビゲート(画面遷移)する方法
- テーマを使用してアプリの外観を変更する方法

### ハートアイコンでお気に入り登録、セカンド画面ではお気に入り一覧を表示するアプリを作成します。

## 4. リストにアイコンを追加します
### 1._RandomWordsState WordPairを保存する_savedを追加します。
```
final _saved = Set<WordPair>();
```
### 2. _buildRow関数でitemの単語がお気に入りに登録されているかを確認する変数を追加します。
```
final alreadySaved = _saved.contains(pair); 
```
### 3. ハートアイコンの追加とお気に入り登録状態に応じて表示を切り替えるように処理を追加します。

```
trailing: Icon(
    alreadySaved ? Icons.favorite : Icons.favorite_border,
    color: alreadySaved ? Colors.red : null,
),
```
※ListTile 単純なリスト trailing 末尾に追加するアイコンのパラメータ

## 5.双方向な処理を追加
ハートのアイコンをタップ可能にします。
タップすると_savedに単語が保存されます。

### 1.onTapを_buildRowメソッドに追加します。
```
onTap: () {
    setState(() {
    if (alreadySaved) {
        _saved.remove(pair);
    } else { 
        _saved.add(pair); 
    } 
    });
},
```
setState を呼び出すことでStateオブジェクトのbuild（）メソッドの呼び出され、UIを更新することができる。
逆に呼び出さないとUIは更新されない。

## 6.新しい画面に遷移する
お気に入り一覧を表示するページを作成し画面遷移を実装します。
FlutterではNavigatorがアプリの画面のスタックを管理します。Navigatorからpushすると次の画面を表示、Navigatorのスタックからpopすると前の画面を表示します。

_RandomWordsStateのAppBarにリストアイコンを追加し、リストをクリックすると新しい画面がpushされます。

### 1.appBarにactionsを追加し、IconButtonを追加、onPressedにメソッドを追加します。 
```
appBar: AppBar(
    title: Text('Startup Name Generator'),
    actions: [
        IconButton(icon: Icon(Icons.list), onPressed: _pushSaved),
    ],
),
```
actionsは配列になっているので、,区切りで複数追加できます。IconButtonを複数個追加することもできます。

### 2. _pushSaved()メソッドを追加します。
```
void _pushSaved() {
}
```

### 3.Navigatorのスタックにをプッシュする
```
void _pushSaved() {
  Navigator.of(context).push(
  );
}
```
この時点ではエラーになり、ビルドができません。

### 4.MaterialPageRouteとBuilderを追加します。別のListTileを生成するコードを作成し、divideTilesによりListで返します。
```
 void _pushSaved() {
    Navigator.of(context).push(
      MaterialPageRoute<void>(
        // NEW lines from here...
        builder: (BuildContext context) {
          final tiles = _saved.map(
            (WordPair pair) {
              return ListTile(
                title: Text(
                  pair.asPascalCase,
                  style: _biggerFont,
                ),
              );
            },
          );
          final divided = ListTile.divideTiles(
            context: context,
            tiles: tiles,
          ).toList();

          return Scaffold(
            appBar: AppBar(
              title: Text('Saved Suggestions'),
            ),
            body: ListView(children: divided),
          );
        }, // ...to here.
      ),
    );
  }
}
```
## テーマを使用してUIを変更します
ThemeDataクラスを構成することで、アプリのテーマを簡単に変更できます。
### 1. MyAppのbuildオプションに、以下のようにコードを編集します。
```
return MaterialApp(
    title: 'Welcome to Flutter',
    home: RandomWords(),
    theme: ThemeData(
    primaryColor: Colors.white,
    ),
);    
```

![](./first_app最終系.png)

part1で作成したリストにハートマークのアイコンを追加、タップしてお気に入り登録ができる機能を作ります。
_buildRow 関数を変更することで実現します。
