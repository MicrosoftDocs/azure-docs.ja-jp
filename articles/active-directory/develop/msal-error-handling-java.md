---
title: MSAL4J におけるエラーと例外の処理
titleSuffix: Microsoft identity platform
description: MSAL4J アプリケーションで、エラーと例外、条件付きアクセス、クレーム チャレンジ、再試行を処理する方法について説明します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/27/2020
ms.author: marsma
ms.reviewer: saeeda, nacanuma
ms.custom: aaddev
ms.openlocfilehash: 8563804a736c37acc9a96eb4a186933507f34200
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98760688"
---
# <a name="handle-errors-and-exceptions-in-msal-for-java"></a>Java 用の MSAL におけるエラーと例外の処理

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-java"></a>Java 用の MSAL でのエラー処理

MSAL for Java には、`MsalClientException`、`MsalServiceException`、`MsalInteractionRequiredException` という 3 種類の例外があります。すべて `MsalException` から継承されます。

- `MsalClientException` は、ライブラリまたはデバイスに対してローカルとなるエラーの発生時にスローされます。
- `MsalServiceException` は、セキュア トークン サービス (STS) からエラー応答が返されたときか、別のネットワーク エラーが発生したときにスローされます。
- `MsalInteractionRequiredException` は、認証を成功させるためには UI 対話が必要になるときにスローされます。

### <a name="msalserviceexception"></a>MsalServiceException

`MsalServiceException` により、要求で返された HTTP ヘッダーが STS に公開されます。 `MsalServiceException.headers()` 経由でアクセスする

### <a name="msalinteractionrequiredexception"></a>MsalInteractionRequiredException

`AcquireTokenSilently()` を呼び出したときに MSAL for Java から返される一般的なステータスコードの 1 つが `InvalidGrantError` です。 つまり、認証トークンを発行するには、ユーザーによる追加の対話が必要になります。 アプリケーションでは認証ライブラリをもう一度呼び出す必要があります。ただし、パブリック クライアント アプリケーションに対して `AuthorizationCodeParameters` または `DeviceCodeParameters` を送信し、対話モードで行います。

ほとんどの場合、`AcquireTokenSilently` が失敗するのは、要求に一致するトークンがトークン キャッシュにないことが原因です。 アクセス トークンの有効期限は 1 時間です。`AcquireTokenSilently` では、更新トークンに基づいて新しいアクセス トークンの入手が試行されます。 OAuth2 用語では、これは更新トークン フローです。 このフローは、テナント管理者がより厳しいログイン ポリシーを構成している場合など、さまざまな理由により失敗する可能性があります。

このエラーを起こす条件の中にはユーザーが簡単に解決できるものもあります。 たとえば、利用規約に同意する必要があるかもしれません。あるいは、コンピューターを特定の企業ネットワークに接続する必要があるため、現在の構成では要求を満たせません。

MSAL からは `reason` フィールドが公開されます。このフィールドを使用し、ユーザー エクスペリエンスを改善できます。 たとえば、`reason` フィールドからは、パスワードの有効期限が切れたことや、一部のリソースの使用に同意する必要があることをユーザーに伝えることができます。 サポートされる値は、`InteractionRequiredExceptionReason` 列挙型の一部です。

| 理由 | 意味 | 推奨される処理 |
|---------|-----------|-----------------------------|
| `BasicAction` | 対話型認証フロー中にユーザーとの対話によって条件を解決できます。 | 対話型パラメーターで `acquireToken` を呼び出します。 |
| `AdditionalAction` | 対話型認証フローの外部で、システムとの追加の修復対話によって条件を解決できます。 | 対話型パラメーターで `acquireToken` を呼び出し、修復アクションについて説明するメッセージを表示します。 呼び出し元のアプリでは、ユーザーが修復アクションを完了する可能性が低い場合に、追加アクションを必要とするフローを非表示にすることを選択できます。 |
| `MessageOnly` | 現時点で、条件は解決できません。 対話型認証フローを起動すると、条件を説明するメッセージが表示されます。 | 対話型パラメーターで `acquireToken` を呼び出し、条件について説明するメッセージを表示します。 ユーザーがメッセージを読み取ってウィンドウを閉じると、`acquireToken` によって `UserCanceled` エラーが返されます。 アプリでは、ユーザーがメッセージの恩恵を受ける可能性が低い場合に、メッセージになるフローを非表示にすることを選択できます。 |
| `ConsentRequired`| ユーザーの同意がないか、または取り消されています。 |ユーザーが同意できるように、対話型パラメーターで `acquireToken` を呼び出します。 |
| `UserPasswordExpired` | ユーザーのパスワードの有効期限が切れています。 | ユーザーが自分のパスワードをリセットできるように、対話型パラメーターで `acquireToken` を呼び出します。 |
| `None` |  詳細が提供されます。 対話型認証フロー中にユーザーとの対話によって条件を解決できる場合があります。 | 対話型パラメーターで `acquireToken` を呼び出します。 |

### <a name="code-example"></a>コード例

```java
        IAuthenticationResult result;
        try {
            PublicClientApplication application = PublicClientApplication
                    .builder("clientId")
                    .b2cAuthority("authority")
                    .build();

            SilentParameters parameters = SilentParameters
                    .builder(Collections.singleton("scope"))
                    .build();

            result = application.acquireTokenSilently(parameters).join();
        }
        catch (Exception ex){
            if(ex instanceof MsalInteractionRequiredException){
                // AcquireToken by either AuthorizationCodeParameters or DeviceCodeParameters
            } else{
                // Log and handle exception accordingly
            }
        }
```

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>次のステップ

問題の診断とデバッグに役立つように、[Java 用の MSAL でログ記録](msal-logging-java.md)を有効にすることを検討してください。
