---
title: "go_routerとAutoRouteを比較してみた"
emoji: "✈️"
type: "tech"
topics: ["flutter", "go_router", "autoroute", "Tech"]
published: true
---

Flutterで画面の行き来を管理する方法として、`Navigator`がありますが、
Navigator 2.0を活用したルーティングライブラリでよく見かけるのが
公式推奨の**go_router**と、型安全なコード生成を特徴とする**AutoRoute**です。

今回 この2つの特徴、Web対応のポイント、遷移時パラメータの扱い方について、ざっくりまとめました🐣


## go_routerってどんな感じ？

- Flutter公式がオススメしている  
- URLベースで画面遷移を宣言的に書ける  
- コード生成なしで手軽に使える  

こんな感じでルートを書きます👇

```dart
final router = GoRouter(
  routes: [
    GoRoute(path: '/', builder: (context, state) => HomePage()),
    GoRoute(path: '/details/:id', builder: (context, state) {
      final id = state.params['id']!;
      return DetailPage(id: int.parse(id));
    }),
  ],
);
```
画面遷移はこう書きます！

```dart
context.go('/details/123');
```

## AutoRouteって？

- アノテーションでルートを定義してコード自動生成
- 型安全でIDE補完がバッチリ効く
- 複雑なルート管理に向いている

こんな感じ👇

```dart
@AutoRouterConfig(replaceInRouteName: 'Screen|Page,Route')
class AppRouter extends RootStackRouter {
  @override
  List<AutoRoute> get routes => [
        // pathを省略したルートはデフォルトでクラス名ベースのURL（例：/home-page）になる
        AutoRoute(page: HomePage.page, initial: true),
        AutoRoute(page: DetailRoute.page, path: '/details/:id'),
      ];
}
```
画面遷移はこう書きます！

```dart
context.router.push(DetailRoute(id: 123));
```

## なんでWebだとNavigator 2.0系が便利なの？
Webアプリでは、ブラウザのURLと画面の状態をピッタリ合わせる必要があります。
URLバーのアドレスに合わせて画面を切り替えたり、戻るボタンを押すとちゃんと前の画面に戻ったり。

昔ながらの`Navigator.push()`だけでこれをやろうとすると、

- URL変化の監視を自分で書いて
- 画面切り替えも手動でやって
- ブラウザ履歴の管理も自前でやらなきゃいけない

というめちゃくちゃ面倒なことに…。

**go_router**や**AutoRoute**なら、これらの面倒な処理を全部やってくれるので、Web対応がぐっと楽になります。

## モバイルだけならNavigator 1.0でも大丈夫？
基本的には大丈夫です！
モバイルはURLバーもブラウザの戻る・進むもないので、
Navigator.push()だけで十分画面遷移が完結します。

ただし、

画面構成を一元管理したい

- パラメータを型安全に渡したい（AutoRouteが得意）
- Deep Link対応を簡単にしたい

なら、go_routerやAutoRouteも🙆‍♀️

## まとめ
- Web対応なら、URLやブラウザ履歴の管理が自動になるgo_routerやAutoRouteを使うべし！
- モバイルだけなら、シンプルにNavigator.push()でもOKだけど、画面管理や型安全が欲しければ検討の価値あり
- AutoRouteは型安全＆IDE補完が便利、セットアップはちょっと手間
