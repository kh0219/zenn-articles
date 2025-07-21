---
title: "FVMとは？Flutter SDKをプロジェクトごとに管理・切り替える方法"
emoji: "🛠️"
type: "tech"
topics: ["flutter", "fvm"]
published: false
---

「プロジェクトによってFlutterのバージョンを切り替えたい」「チームで同じSDKバージョンを使いたい」
そんなときに便利なのが、**FVM（Flutter Version Management）** です。

この記事では、FVMとは何か、どんなときに使うと良いか、導入方法や基本的な使い方について紹介します。

## ✅ FVMとは？

FVM（Flutter Version Management）は、Flutter SDKをプロジェクト単位で管理・切り替えできるツールです。

通常、Flutterのバージョンは `flutter upgrade` や `flutter downgrade` で変更しますが、これはグローバルに1つのバージョンしか扱えません。

FVMを使うと、**プロジェクトごとに異なるバージョンを指定でき、しかもグローバル環境に影響を与えません**。

## 💡 こんな場合に便利です

- 複数のFlutterプロジェクトを並行して開発している
- チーム全員が同じSDKバージョンで開発する必要がある
- 古いFlutterバージョンを使った保守案件に対応したい
- 最新版のFlutterで新機能を試したいけど、既存プロジェクトに影響を出したくない

## 🚀 導入方法（macOS/Linux）

### ① FVMのインストール

```bash
brew tap leoafarias/fvm
brew install fvm
```

### ② プロジェクトにFVMを導入する
プロジェクトフォルダに移動し、使用したいFlutterバージョンを指定してインストール：

```bash
fvm install 3.32.0
```
次に、使用するFlutterバージョンを指定：

```bash
fvm use 3.32.0
```
これでプロジェクトにFlutter SDKのバージョンが固定されました（.fvm/フォルダと fvm_config.json が作成されます）。

## 🛠️ FVMでできること（基本コマンド）
| コマンド                          | 内容                                      |
|----------------------------------|-------------------------------------------|
| `fvm install <version>`         | 特定バージョンのFlutter SDKをインストール |
| `fvm use <version>`             | 使用するFlutterバージョンを指定           |
| `fvm list`                      | インストール済みのFlutterバージョン一覧   |
| `fvm flutter <flutter-command>` | FVM経由でFlutterコマンドを実行            |

例：
```bash
fvm flutter pub get
fvm flutter run
```

## 🧠 補足：IDEでFVMを使うには？
VS CodeなどのIDEでは、Flutter SDKのパスをFVMで管理しているものに設定する必要があります。
設定しない場合、IDEはグローバルにインストールされているFlutterを使い続けるため、バージョン違いによるビルドエラーが発生しやすくなります。

VS Codeの場合：
Cmd+Shift+P → 「Flutter: Change SDK」

.fvm/flutter_sdk ディレクトリを指定

## 🔚 まとめ
FVMはFlutter SDKをプロジェクト単位で管理できる便利なツール

複数プロジェクトやチーム開発、保守案件に最適

環境を汚さず、安心してFlutterバージョンを切り替え可能

導入も数ステップで簡単なので、Flutter開発者はぜひ取り入れてみてください！

