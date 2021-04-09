---
title: エラーと例外 (MSAL Android) | Azure
titleSuffix: Microsoft identity platform
description: MSAL Android アプリケーションでエラーと例外、条件付きアクセス、クレーム チャレンジを処理する方法について学習します。
services: active-directory
author: hamiltonha
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 08/07/2020
ms.author: hahamil
ms.reviewer: marsma
ms.openlocfilehash: 4185206e92a78c2684ba1690f03700ef2532cc5e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98761394"
---
# <a name="handle-exceptions-and-errors-in-msal-for-android"></a>Android 用の MSAL で例外とエラーを処理する

Microsoft Authentication Library (MSAL) での例外は、アプリ開発者が自分のアプリケーションをトラブルシューティングする際に役立つようになっています。 例外のメッセージはローカライズされていません。

例外やエラーを処理するときは、例外の種類自体とエラー コードを使って、例外を区別できます。  エラー コードの一覧については、「[認証と承認エラー コード](reference-aadsts-error-codes.md)」をご覧ください。

サインイン中に、同意、条件付きアクセス (MFA、デバイス管理、場所に基づく制限)、トークンの発行と引き換え、ユーザー プロパティに関するエラーが発生することがあります。


|Error クラス | 原因/エラー文字列| 処理方法 |
|-----------|------------|----------------|
|`MsalUiRequiredException`| <ul><li>`INVALID_GRANT`:アクセス トークンの引き換えに使用された更新トークンが無効であるか、期限切れになっているか、または取り消されています。 この例外は、パスワードが変更されたことが原因である可能性があります。 </li><li>`NO_TOKENS_FOUND`: アクセス トークンが存在していなくて、アクセス トークンを引き換えるための更新トークンが見つかりません。</li> <li>ステップアップが必須です<ul><li>MFA</li><li>要求が見つかりません</li></ul></li><li>条件付きアクセスによってブロックされています (たとえば、[認証ブローカー](./msal-android-single-sign-on.md)のインストールが必須)</li><li>`NO_ACCOUNT_FOUND`:キャッシュ内にサイレント認証に使用できるアカウントがありません。</li></ul> |`acquireToken()` を呼び出すことで、ユーザーに対して、自分のユーザー名とパスワードを入力し、場合によっては、多要素認証に同意してそれを実行するように求めます。|
|`MsalDeclinedScopeException`|<ul><li>`DECLINED_SCOPE`: ユーザーまたはサーバーが受け入れていないスコープがあります。 要求されたスコープがサポートされていないか、認識されていないか、特定のアカウントでサポートされていない場合、サーバーによってスコープが拒否されることがあります。 </li></ul>| 開発者は、付与されたスコープで認証を続行するか、認証プロセスを終了するかを決定する必要があります。 `silentParametersForGrantedScopes` を渡し、`acquireTokenSilent` を呼び出すことによって、付与されたスコープに対してのみ取得トークン要求を再送信し、付与されたアクセス許可に関するヒントを提供するオプション。 |
|`MsalServiceException`|<ul><li>`INVALID_REQUEST`: この要求に必須のパラメーターが含まれていないか、この要求に無効なパラメーターが含まれているか、この要求に複数回パラメーターが含まれているか、またはこの要求の形式が正しくありません。 </li><li>`SERVICE_NOT_AVAILABLE`: サービスがダウンしていることが原因で、500/503/506 エラー コードを表します。 </li><li>`UNAUTHORIZED_REQUEST`:クライアントは、認証コードの要求を許可されていません。</li><li>`ACCESS_DENIED`:リソース所有者または承認サーバーによって、要求が拒否されました。</li><li>`INVALID_INSTANCE`: `AuthorityMetadata` の検証に失敗しました</li><li>`UNKNOWN_ERROR`: サーバーに対する要求が失敗しましたが、エラーは発生せず、サービスから `error_description` が返されました。</li><ul>| この例外クラスは、サービスとの通信時に発生するエラーを表し、承認またはトークン エンドポイントからのものである可能性があります。 サーバー応答からのエラーと error_description は、MSAL によって読み取られます。 一般に、これらのエラーは、コード内またはアプリ登録ポータル内のいずれかでアプリ構成を修正することによって解決されます。 まれに、サービスの停止によってこの警告がトリガーされることがあります。これに対処するには、サービスが回復するのを待つしかありません。  |
|`MsalClientException`|<ul><li> `MULTIPLE_MATCHING_TOKENS_DETECTED`:複数のキャッシュ エントリが見つかり、キャッシュからの正しいアクセスまたは更新トークンの識別が SDK でできません。 この例外は、通常、SDK でのトークンの格納に関するバグを示しているか、またはサイレント要求内にオーソリティが指定されていないために複数の一致するトークンが見つかったことを示しています。 </li><li>`DEVICE_NETWORK_NOT_AVAILABLE`: デバイス上に使用できるアクティブなネットワークがありません。 </li><li>`JSON_PARSE_FAILURE`: SDK で JSON 形式を解析できませんでした。</li><li>`IO_ERROR`: `IOException` が発生しました。デバイスまたはネットワーク エラーの可能性があります。 </li><li>`MALFORMED_URL`: URL の形式が正しくありません。 認証要求、オーソリティ、またはリダイレクト URI の構築時に発生した可能性があります。 </li><li>`UNSUPPORTED_ENCODING`: エンコードはデバイスでサポートされていません。 </li><li>`NO_SUCH_ALGORITHM`: [PKCE](https://tools.ietf.org/html/rfc7636) チャレンジの生成に使用されるアルゴリズムはサポートされていません。 </li><li>`INVALID_JWT`: サーバーから返された `JWT` が無効であるか、空であるか、または形式が正しくありません。 </li><li>`STATE_MISMATCH`: 承認応答からの状態が承認要求の状態と一致しませんでした。 承認要求の場合、リダイレクトから返された状態と、要求で送信されたものが SDK によって検証されます。 </li><li>`UNSUPPORTED_URL`: サポートされていない URL です。ADFS オーソリティの検証を実行できません。 </li><li> `AUTHORITY_VALIDATION_NOT_SUPPORTED`: オーソリティの検証において、オーソリティがサポートされていません。 この SDK では B2C オーソリティはサポートされていますが、B2C オーソリティの検証はサポートされていません。 既知のホストのみがサポートされます。 </li><li>`CHROME_NOT_INSTALLED`: Chrome がデバイスにインストールされていません。 SDK は、承認要求に Chrome カスタム タブを使用し (使用可能な場合)、Chrome ブラウザーにフォールバックされます。 </li><li>`USER_MISMATCH`: 取得トークン要求で指定されたユーザーが、サーバーから返されたユーザーと一致しません。</li></ul>|この例外クラスは、ライブラリに対してローカルな一般エラーを表します。 これらの例外に対処するには、要求を修正します。|
|`MsalUserCancelException`|<ul><li>`USER_CANCELED`: ユーザーが対話型フローを開始しましたが、戻されるトークンを受信する前に、彼らは要求を取り消しました。 </li></ul>||
|`MsalArgumentException`|<ul><li>`ILLEGAL_ARGUMENT_ERROR_CODE`</li><li>`AUTHORITY_REQUIRED_FOR_SILENT`: `acquireTokenSilent` にはオーソリティを指定する必要があります。</li></ul>|開発者はこれらのエラーを軽減することができます。それには、引数を修正して、対話型認証、完了コールバック、スコープ、および有効な ID を持つアカウントに対するアクティビティが指定されていることを保証します。|


## <a name="catching-errors"></a>エラーのキャッチ

次のコード スニペットは、サイレント `acquireToken` 呼び出しの場合にエラーをキャッチする例を示しています。

```java
/**
 * Callback used in for silent acquireToken calls.
 */
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");

            /* Successfully got a token, use it to call a protected resource - MSGraph */
            callGraphAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside MsalError.java */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception instanceof MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }
    };
}
```

## <a name="next-steps"></a>次の手順

[Android 用の MSAL でのログ記録](msal-logging-android.md)の詳細を確認する。