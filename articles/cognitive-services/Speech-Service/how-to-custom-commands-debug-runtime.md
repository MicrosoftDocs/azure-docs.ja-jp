---
title: カスタム コマンド アプリケーションの実行時にエラーをデバッグする
titleSuffix: Azure Cognitive Services
description: この記事では、カスタム コマンド アプリケーションの実行時エラーをデバッグする方法について説明します。
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: b41bcda7381e5811ef1e1f91c102d8c85f50b3b5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103196909"
---
# <a name="debug-errors-when-running-a-custom-commands-application"></a>カスタム コマンド アプリケーションの実行時にエラーをデバッグする

この記事では、カスタム コマンド アプリケーションの実行中にエラーが発生した場合のデバッグ方法について説明します。 

## <a name="connection-failed"></a>接続できなかった

[クライアント アプリケーション (Speech SDK を使用)](./how-to-custom-commands-setup-speech-sdk.md) または [Windows 音声アシスタント クライアント](./how-to-custom-commands-developer-flow-test.md)からカスタム コマンド アプリケーションを実行すると、次のような接続エラーが発生する場合があります。

| エラー コード | 詳細 |
| ------- | -------- |
| [401](#error-401) | AuthenticationFailure: 認証エラーで WebSocket をアップグレードできませんでした |
| [1002](#error-1002) | サーバーが状態コード '101' を返すはずが、状態コード '404' を返しました。 |

### <a name="error-401"></a>エラー 401
- クライアント アプリケーションで指定されているリージョンが、カスタム コマンド アプリケーションのリージョンと一致しません

- 音声リソース キーが無効です
    
    音声リソース キーが正しいことを確認します。

### <a name="error-1002"></a>エラー 1002 
- お使いのカスタム コマンド アプリケーションは公開されていません
    
    ポータルでアプリケーションを公開してください。

- お使いのカスタム コマンド アプリケーションは有効ではありません

    お使いのカスタム コマンド アプリケーションの ID が正しいことを確認してください。
 音声リソースの外部にあるカスタム コマンド アプリケーション

    カスタム コマンド アプリケーションが、ご自分の音声リソースの下に作成されていることを確認してください。

接続に関する問題のトラブルシューティングについて詳しくは、[Windows 音声アシスタント クライアントのトラブルシューティング](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/clients/csharp-wpf#troubleshooting)に関するページを参照してください


## <a name="dialog-is-canceled"></a>ダイアログがキャンセルされる

カスタム コマンド アプリケーションを実行すると、何らかのエラーが発生したときにダイアログがキャンセルされます。

- ポータルでアプリケーションをテストしている場合は、キャンセルの説明が直接表示され、エラーのアイコンが再生されます。 

- [Windows 音声アシスタント クライアント](./how-to-custom-commands-developer-flow-test.md)でアプリケーションを実行している場合は、エラーのアイコンが再生されます。 **[イベント: CancelledDialog]** が **[アクティビティ ログ]** で見つかります。

- クライアント アプリケーションの例[クライアント アプリケーション (Speech SDK を使用)](./how-to-custom-commands-setup-speech-sdk.md) に従っている場合は、エラーのアイコンが再生されます。 **[イベント: CancelledDialog]** が **[状態]** で見つかります。

- 独自のクライアント アプリケーションを構築している場合は、CancelledDialog イベントの処理に必要なロジックを常に設計できます。

CancelledDialog イベントは、次に示すキャンセル コードと説明で構成されています。

| キャンセル コード | キャンセルの説明 |
| ------- | --------------- | ----------- |
| [MaxTurnThresholdReached](#no-progress-was-made-after-the-max-number-of-turns-allowed) | 許可されたターンの最大数の後で状態が進行しませんでした |
| [RecognizerQuotaExceeded](#recognizer-usage-quota-exceeded) | 認識エンジンの使用量クォータを超えました |
| [RecognizerConnectionFailed](#connection-to-the-recognizer-failed) | 認識エンジンへの接続に失敗しました |
| [RecognizerUnauthorized](#this-application-cannot-be-accessed-with-the-current-subscription) | 現在のサブスクリプションではこのアプリケーションにアクセスできません |
| [RecognizerInputExceededAllowedLength](#input-exceeds-the-maximum-supported-length) | 入力が認識エンジンでサポートされている最大長を超えています |
| [RecognizerNotFound](#recognizer-not-found) | 認識エンジンが見つかりません |
| [RecognizerInvalidQuery](#invalid-query-for-the-recognizer) | 認識エンジンに対するクエリが無効です |
| [RecognizerError](#recognizer-return-an-error) | 認識エンジンがエラーを返します |

### <a name="no-progress-was-made-after-the-max-number-of-turns-allowed"></a>許可されたターンの最大数の後で状態が進行しませんでした
特定の数のターンの後で必要なスロットが正常に更新されない場合、ダイアログはキャンセルされます。 組み込みの最大数は 3 です。

### <a name="recognizer-usage-quota-exceeded"></a>認識エンジンの使用量クォータを超えました
Language Understanding (LUIS) には、リソース使用に関する制限があります。 通常、"認識エンジンの使用量クォータを超えましたエラー" は次の原因で発生する可能性があります。 
- LUIS の作成が制限を超えています

    カスタム コマンド アプリケーションに予測リソースを追加します。 
    1. **[設定]** 、LUIS リソースに移動します
    1. **[予測リソース]** で予測リソースを選択するか、 **[新しいリソースの作成]** をクリックします 

- LUIS 予測リソースが制限を超えています

    F0 予測リソースを使用している場合は、10,000/月、5 クエリ/秒という制限があります。

LUIS リソースの制限の詳細については、[Language Understanding のリソース使用量と制限](../luis/luis-limits.md#resource-usage-and-limits)に関する説明を参照してください。

### <a name="connection-to-the-recognizer-failed"></a>認識エンジンへの接続に失敗しました
通常は、Language Understanding (LUIS) の認識エンジンに対する一時的な接続エラーが発生したことを意味します。 もう一度試してみれば、問題は解決するはずです。

### <a name="this-application-cannot-be-accessed-with-the-current-subscription"></a>現在のサブスクリプションではこのアプリケーションにアクセスできません
お使いのサブスクリプションでは、LUIS アプリケーションへのアクセスが許可されません。 

### <a name="input-exceeds-the-maximum-supported-length"></a>サポートされている最大長を超えています
入力が 500 文字を超えました。 許可される入力発話は最大 500 文字だけです。

### <a name="invalid-query-for-the-recognizer"></a>認識エンジンに対するクエリが無効です
入力が 500 文字を超えました。 許可される入力発話は最大 500 文字だけです。

### <a name="recognizer-return-an-error"></a>認識エンジンがエラーを返します
入力を認識しようとして、LUIS 認識エンジンからエラーが返されました。

### <a name="recognizer-not-found"></a>認識エンジンが見つかりません
カスタム コマンド ダイアログ モデルで指定されている認識エンジンの種類が見つかりません。 現在は、[Language Understanding (LUIS) 認識エンジン](https://www.luis.ai/)のみがサポートされています。

## <a name="other-common-errors"></a>その他の一般的なエラー
### <a name="unexpected-response"></a>予期しない応答
予期しない応答には複数の原因が考えられます。 最初に以下をチェックします。
- 例文での Yes/No の意図

    現在、確認機能を使用している場合を除き、Yes/No の意図はサポートされていません。 例文で定義されているすべての Yes/No の意図は検出されません。

- コマンド間で似た意図と例文がある

    2 つのコマンドで似た意図と例文が共有されていると、LUIS 認識の精度が影響を受ける可能性があります。 コマンドの機能と例文をできる限り異なるものにしてみることができます。

    認識精度を向上させるためのベスト プラクティスについては、[LUIS のベスト プラクティス](../luis/luis-concept-best-practices.md)に関する記事を参照してください。

- ダイアログがキャンセルされる
    
    前のセクションのキャンセルの理由を確認します。

### <a name="error-while-rendering-the-template"></a>テンプレートのレンダリング中のエラー
音声応答で未定義のパラメーターが使用されています。 

### <a name="object-reference-not-set-to-an-instance-of-an-object"></a>オブジェクト参照がオブジェクト インスタンスに設定されていない
**[クライアントへのアクティビティの送信]** アクションで定義されている JSON ペイロードに空のパラメーターがあります。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [GitHub のサンプルを参照](https://aka.ms/speech/cc-samples)
