---
title: イマーシブ リーダー SDK リリース ノート
titleSuffix: Azure Cognitive Services
description: 詳細については、イマーシブ リーダー JavaScript SDK の新機能に関するページを参照してください。
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 10/12/2020
ms.author: dylankil
ms.openlocfilehash: 6b041916c6fa446732e95d49391d9ead63eb1b17
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "93135463"
---
# <a name="immersive-reader-javascript-sdk-release-notes"></a>イマーシブ リーダー JavaScript SDK リリース ノート

## <a name="version-110"></a>バージョン 1.1.0

このリリースには、新機能、セキュリティ脆弱性の修正、バグ修正、コード サンプルの更新、および構成オプションが含まれています。

#### <a name="new-features"></a>新機能

* 異なる複数のブラウザーおよびデバイス間でのユーザー設定の保存と読み込みを有効化
* 既定の表示オプションの構成を有効化
* 翻訳言語の設定、単語の翻訳の有効化、イマーシブ リーダー起動時のドキュメント翻訳の有効化のオプションを追加
* オプションを使用して読み上げを構成するためのサポートを追加
* 最初の実行エクスペリエンスを無効にする機能を追加
* UWP 用の ImmersiveReaderView を追加

#### <a name="improvements"></a>機能強化

* 最新の SDK で動作するように Android コード サンプル HTML を更新
* 処理された文字数を返すように起動の応答を更新
* v1.1.0 を使用するようにコード サンプルを更新
* 既に読み込み中のときは launchAsync の呼び出しを許可しない
* 無効なコンテンツの有無をチェックし、データが文字列ではない場合はメッセージを無視
* if 句でウィンドウの呼び出しをラップし、ブラウザーによる Promise のサポートを確認

#### <a name="fixes"></a>修正

* gitignore から yarn.lock を削除することで dependabot を修正
* quickstart-nodejs コード サンプルで pug を v3.0.0 にアップグレードすることでセキュリティの脆弱性を修正
* Jest および TypeScript パッケージをアップグレードすることでセキュリティの複数の脆弱性を修正
* Microsoft.IdentityModel.Clients.ActiveDirectory を v5.2.0 にアップグレードすることでセキュリティの脆弱性を修正

<br>

## <a name="version-100"></a>バージョン 1.0.0

このリリースには、破壊的変更、新機能、コード サンプルの改善、バグ修正が含まれています。

#### <a name="breaking-changes"></a>重大な変更

* Azure AD トークンとサブドメインを必須とし、以前のバージョンで使用していたトークンを非推奨としました。
* CookiePolicy を無効に設定しました。 既定では、ユーザー設定の保持は無効になっています。 CookiePolicy が有効に設定されていない限り、リーダーは毎回既定の設定で起動します。

#### <a name="new-features"></a>新機能

* Cookie を有効または無効にするサポートを追加
* Android Kotlin クイック スタートのコード サンプルを追加
* Android Java クイック スタートのコード サンプルを追加
* Node.js クイック スタートのコード サンプルを追加

#### <a name="improvements"></a>機能強化

* Node.js の高度な README.md を更新
* Python コード サンプルを高度なものからクイック スタートに変更
* iOS Swift コード サンプルを js/samples に移動
* v1.0.0 を使用するようにコード サンプルを更新

#### <a name="fixes"></a>修正

* Node.js の高度なコード サンプルに対する修正
* advanced-csharp-multiple-resources の不足ファイルを追加
* ハイパーリンクから en-us を削除

<br>

## <a name="version-003"></a>バージョン 0.0.3

このリリースには、新機能、コード サンプルの更新、セキュリティ脆弱性の修正、バグ修正が含まれています。

#### <a name="new-features"></a>新機能

* iOS Swift コード サンプルを追加
* 複数のリソースの使用方法を示した C# の高度なコード サンプルを追加 
* 全画面表示の切り替え機能を無効にするサポートを追加
* イマーシブ リーダー アプリケーションの終了ボタンを非表示にするためのサポートを追加
* イマーシブ リーダー終了時にホスト アプリケーションで使用できるコールバック関数を追加
* Azure Active Directory 認証を使用するようにコード サンプルを更新

#### <a name="improvements"></a>機能強化

* Word 文書を含むように C# の高度なコード サンプルを更新
* v0.0.3 を使用するようにコード サンプルを更新

#### <a name="fixes"></a>修正

* セキュリティの脆弱性を修正するために lodash をバージョン 4.17.14 にアップグレード
* セキュリティの脆弱性を修正するために C# MSAL ライブラリを更新
* セキュリティの脆弱性を修正するために mixin-deep をバージョン 1.3.2 にアップグレード
* セキュリティの脆弱性を修正するために、脆弱なバージョンの set-value と mixin-deep を使用していた jest、webpack および webpack-cli をアップグレード

<br>

## <a name="version-002"></a>バージョン 0.0.2

このリリースには、新機能、コード サンプルの更新、セキュリティ脆弱性の修正、バグ修正が含まれています。

#### <a name="new-features"></a>新機能

* Python の高度なコード サンプルを追加
* Java クイック スタートのコード サンプルを追加
* 簡単なコード サンプルを追加

#### <a name="improvements"></a>機能強化

* resourceName を cogSvcsSubdomain に名前変更
* シークレットをコードの外部に移動し、環境変数を使用
* v0.0.2 を使用するようにコード サンプルを更新

#### <a name="fixes"></a>修正

* イマーシブ リーダー ボタンのアクセシビリティに関するバグを修正
* 壊れたスクロールを修正
* セキュリティの脆弱性を修正するために handlebars パッケージをバージョン 4.1.2 にアップグレード
* SDK 単体テストでのバグを修正
* JavaScript の Internet Explorer 11 との互換性に関するバグを修正
* SDK の URL を更新

<br>

## <a name="version-001"></a>バージョン 0.0.1

イマーシブ リーダー JavaScript SDK の最初のリリースです。

* イマーシブ リーダー JavaScript SDK を追加
* UI 言語を指定するためのサポートを追加
* launchAsync 関数がタイムアウト エラーで失敗するタイミングを決定するタイムアウトを追加
* イマーシブ リーダーの iframe の Z インデックスを指定するためのサポートを追加
* Chrome アプリとの互換性のため、iframe ではなく webview タグを使用するためのサポートを追加
* SDK 単体テストを追加
* Node.js の高度なコード サンプルを追加
* C# の高度なコード サンプルを追加
* C# クイック スタートのコード サンプルを追加
* パッケージ構成、Yarn およびその他のビルド ファイルを追加
* git 構成ファイルを追加
* README.md ファイルをコード サンプルと SDK に追加
* MIT ライセンスを追加
* 共同作成者の指示を追加
* 静的アイコン ボタンの SVG 資産を追加

## <a name="next-steps"></a>次のステップ

Immersive Reader を使ってみましょう。

* [イマーシブ リーダー クライアント ライブラリ リファレンス](./reference.md)を読みます
* [GitHub でイマーシブ リーダー クライアント ライブラリ](https://github.com/microsoft/immersive-reader-sdk)を調べます
