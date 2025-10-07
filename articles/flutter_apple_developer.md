---
title: "Flutter × iOS 開発で Apple Developer アカウントが出てこないときの対処法"
emoji: "🍎"
type: "tech"
topics: ["flutter", "Apple", "ios"]
published: true
---

Flutter プロジェクトを作成するとき、「Apple Development: ○○」の選択が出てきて、
自分の Apple ID が表示されない…！
という現象が起きたので、原因と対処方法をまとめます。

## 🌀 現象
```
flutter create .
```

を実行すると、こんな表示が出る：

```
Valid development certificates available:
[1] Apple Development: Taro Yamada (...)
[2] Apple Development: Jiro Tanaka (...)
Please select a certificate for code signing (or "q" to quit):
```

でも、自分の Apple ID が出てこない。

はて？？？ 🤔


## 🔍 原因
- 今回使用したい Apple ID で証明書を作ったことがない場合、Flutter には「この人（あなた）の署名」が存在しないので、選択肢に出てこない。
- 過去に作成したことのある、別の Apple Developer 証明書 だけが残っている状態


## 💡 解決方法
✅ **Xcodeでテスト用プロジェクトを作成する**
※ XcodeでApple IDが追加されていない場合はこちらを先に設定してください↓
Xcode → Settings → Apple Accounts → Add Apple Account で Apple ID 追加

1. Xcode で新規プロジェクトを作成します。(問題解決するためだけの仮プロジェクトなので、名前などはなんでもOK)
2. プロジェクト作成画面の「Team」プルダウンで、自分の Apple ID を選択します。  
   （以下イメージ👇）  
![スクリーンショット](/images/20251007.png)


これで自分の Apple ID に紐づく証明書が自動的に作成されます。

その後、`flutter create .`を実行すると、、、
成功しました！！✨️
今度は選択肢に自分の Apple ID が現れました😭


## 📝 補足
今回は個人開発向けの対処になりますが、
もし **チーム開発で「署名を明示的に分けたい」とき**、**CI/CD（テスト配信等）で自動ビルドを設定したいとき**は
Apple Developerサイトで手動生成する方法を取ることになります。

- Apple Developer Certificates ページにアクセス
- 「＋」ボタンから手動で証明書を作成
- キーチェーンアクセスでCSRを作成 → Appleにアップロード → 証明書をダウンロード

⚠️ ただし無料アカウントでは使えず、有料（年間99ドル）のApple Developerアカウントが必要。

## 🎀 まとめ
書いてしまえばほんの数行の作業ですが、
原因がわからない状態から調べながら進めるのは本当に大変ですよね。

同じように「Apple Developer アカウントが出てこない…」と困っている人の助けになれば嬉しいです🍎