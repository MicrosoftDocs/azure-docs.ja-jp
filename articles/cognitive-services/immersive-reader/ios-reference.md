---
title: イマーシブ リーダー iOS SDK リファレンス
titleSuffix: Azure Cognitive Services
description: イマーシブ リーダー iOS SDK のリファレンス
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 08/01/2019
ms.author: metan
ms.openlocfilehash: 615c09dd8a7287918bb009ce11854278b21554c1
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69899410"
---
# <a name="immersive-reader-sdk-reference"></a>イマーシブ リーダー SDK リファレンス

イマーシブ リーダー iOS SDK は、イマーシブ リーダーを iOS アプリケーションに統合するための Swift CocoaPod です。

## <a name="functions"></a>Functions

SDK は 1 つの関数 `launchImmersiveReader(navController, token, subdomain, content, options, onSuccess, onFailure)` を公開します。

### <a name="launchimmersivereader"></a>launchImmersiveReader

iOS アプリケーションでビュー コントローラーを起動して、イマーシブ リーダーを起動します。

```swift
public func launchImmersiveReader(navController: UINavigationController, token: String, subdomain: String, content: Content, options: Options?, onSuccess: @escaping () -> Void, onFailure: @escaping (_ error: Error) -> Void)
```

#### <a name="parameters"></a>parameters

| Name | Type | 説明 |
| ---- | ---- |------------ |
| `navController` | UINavigationController | 関数が呼び出されている iOS アプリケーションの Navigation Controller。 |
| `token` | string | Azure AD 認証トークン。 [Azure AD の認証方法](./azure-active-directory-authentication.md)に関するページを参照してください。 |
| `subdomain` | string | Azure 内のイマーシブ リーダー リソースのカスタム サブドメイン。 [Azure AD の認証方法](./azure-active-directory-authentication.md)に関するページを参照してください。 |
| `content` | [コンテンツ](#content) | イマーシブ リーダーで表示するコンテンツを含むオブジェクト。 |
| `options` | [オプション](#options) | イマーシブ リーダーの特定の動作を構成するオプション。 省略可能。 |
| `onSuccess` | () -> Void | イマーシブ リーダーが正常に起動したときに呼び出されるクロージャ。 |
| `onFailure` | (_ error:[Error](#error)) -> Void | イマーシブ リーダーの読み込みに失敗したときに呼び出されるクロージャ。 このクロージャは、エラーに関連するエラー コードおよびエラー メッセージを表す [`Error`](#error) オブジェクトを返します。 詳細については、「[エラー コード](#error-codes)」を参照してください。 |

## <a name="types"></a>型

### <a name="content"></a>コンテンツ

イマーシブ リーダーで表示するコンテンツを含みます。

```swift
struct Content: Encodable {
    var title: String
    var chunks: [Chunk]
}
```

#### <a name="supported-mime-types"></a>サポートされている MIME タイプ

| MIME タイプ | 説明 |
| --------- | ----------- |
| text/plain | プレーンテキスト。 |
| application/mathml+xml | Mathematical Markup Language (MathML)。 [詳細情報](https://developer.mozilla.org/en-US/docs/Web/MathML)。

### <a name="options"></a>オプション

イマーシブ リーダーの特定の動作を構成するプロパティを含みます。

```swift
struct Options {
    var uiLang: String?
    var timeout: TimeInterval?
}
```

### <a name="error"></a>Error

エラーに関する情報が含まれます。

```swift
struct Error {
    var code: String
    var message: String
}
```

#### <a name="error-codes"></a>エラー コード

| コード | 説明 |
| ---- | ----------- |
| BadArgument | 与えられた引数が無効です。詳細は `message` を参照してください。 |
| タイムアウト | 指定されたタイムアウト時間内にイマーシブ リーダーを読み込めませんでした。 |
| TokenExpired | 与えられたトークンの期限が切れています。 |
| Throttled | 呼び出しレートの制限を超えました。 |
| InternalError | イマーシブ リーダーのビュー コントローラー内で内部エラーが発生しました。 詳細については、`message` を参照してください。|

## <a name="os-version-support"></a>OS バージョン サポート

イマーシブ リーダー iOS SDK は、iPad および iPhone で、iOS 9.0 以降でサポートされています。

## <a name="next-steps"></a>次の手順

* [GitHub 上のイマーシブ リーダー iOS SDK](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS) を探索する
* [クイック スタート:イマーシブ リーダーを起動する iOS アプリを作成する (Swift)](./ios-quickstart.md)