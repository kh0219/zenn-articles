---
title: "ProviderとRiverpodの違いをざっくり調べてみた"
emoji: "🎀"
type: "tech"
topics: ["flutter", "riverpod", "provider"]
published: true
---

Flutterの状態管理、ProviderとRiverpodがあるけど **なにが違うの？どう使い分ければいいの？**
ということで、 **ProviderとRiverpodの違い** をざっくり調べてみました。

---

## 💡 Providerとは

ProviderはFlutter公式ドキュメントでも紹介されている古参の状態管理方法。
Widgetツリー（画面構造）に状態を「提供（provide）」します。

- Widgetツリーに沿ってデータを渡す
- 状態が変わるとnotifyListeners()でUIを再描画

### コード例
```dart
// main.dart
void main() {
  runApp(
    ChangeNotifierProvider(
      create: (_) => CounterNotifier(),
      child: MyApp(),
    ),
  );
}

// CounterNotifier
class CounterNotifier with ChangeNotifier {
  int count = 0;

  // カウント追加
  void increment() {
    count++;
    notifyListeners(); // 👈 状態が変わったことを通知
  }
}

// UI側（Widget内）
@override
class CounterScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    // Providerから現在の状態を取得
    final counter = context.watch<CounterNotifier>();

    return Scaffold(
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Text('Count: ${counter.count}'),
            const SizedBox(height: 16),
            ElevatedButton(
              onPressed: () => context.read<CounterNotifier>().increment(),
              child: const Text('＋'),
            ),
          ],
        ),
      ),
    );
  }
}

```

このように、

- ChangeNotifier（状態クラス）
- ChangeNotifierProvider（その状態をUIに渡す仕組み）
- notifyListeners()（状態が変わったことを通知）

の3つで成り立っています。

### ⚠️ 弱点
- Widgetツリーが深くなると状態のスコープが複雑に
- テストがしづらい
- UIロジックが混ざりやすい


## 👽️ Riverpodとは

Providerを作った人（Remi Rousselet）が
「Providerの弱点を直したい！」と思って開発したのがRiverpod。

- Widgetツリーに依存しない（つまり、UIとロジックを分離できる）
- refという仕組みで、状態に安全にアクセスできる
- テストや再利用が簡単

### コード例
```dart
// main.dart
void main() {
  runApp(
    ProviderScope(
      child: MyApp(),
    ),
  );
}

// CounterNotifier
// RiverpodでもChangeNotifierを使えるが、StateNotifierのほうが自然
class CounterNotifier extends StateNotifier<int> {
  CounterNotifier() : super(0);

  void increment() {
    state++;
  }
}

// CounterNotifier のインスタンスを作成して提供
final counterProvider = StateNotifierProvider<CounterNotifier, int>((ref) {
  return CounterNotifier();
});

// UI側（Widget内）
class MyHomePage extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final counter = ref.watch(counterProvider); // ここで値を監視

    return Scaffold(
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Text('Count: ${counter}'),
            ElevatedButton(
              onPressed: () => ref.read(counterProvider.notifier).increment(), // 更新
              child: Text('＋'),
            ),
          ],
        ),
      ),
    );
  }
}

```

ここで重要なのは：
- ref.watch()：状態の変化を監視してUIを更新
- ref.read()：状態を1回だけ読み取る（更新時に使う）
- ProviderScope：アプリ全体の状態をまとめて管理

→ 結果として、より「安全・柔軟・テストしやすい」状態管理ができる。


## ⚖️ Provider vs Riverpod 比較表
| 比較項目         | Provider           | Riverpod           |
|------------------|-------------------|--------------------|
| 作者             | Remi Rousselet    | 同じくRemi Rousselet |
| Widgetツリー依存 | あり              | なし               |
| テストのしやすさ | やや難しい        | しやすい           |
| パフォーマンス   | 普通              | 再構築を減らせる   |
| 学習コスト       | 低め              | やや高め           |
| 主な使いどころ   | 小〜中規模アプリ  | 中〜大規模アプリ   |

👉 簡単に言うと：
`Provider` = Flutterに慣れるのにちょうどいい、手軽な方法
`Riverpod` = アプリが大きくなっても耐えられる、拡張性の高い方法


## 💬 実際どっちを使う？

UIとロジックを分けたい、中規模〜大規模、長期運用、テストも重視 → Riverpod

とにかく動くものをサッと作りたい、小規模・短期開発 → Provider

## 🧩 まとめ

- `Riverpod`は`Provider`の進化版のような存在
- `Provider`を理解していると`Riverpod`の理解が深まる
- プロジェクト規模や開発スタイルに合わせて使い分けるのがベスト

## 📚 おまけ：学習リソース

- [Riverpod公式ドキュメント](https://riverpod.dev/ja/docs/introduction/getting_started)
- [Flutter公式 Provider チュートリアル](https://docs.flutter.dev/data-and-backend/state-mgmt/simple)
- [Remi Rousselet氏のGitHub](https://github.com/rrousselGit)
