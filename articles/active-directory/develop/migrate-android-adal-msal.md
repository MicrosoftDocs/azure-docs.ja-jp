---
title: Android 用の ADAL から MSAL への移行に関するガイド | Azure
description: Azure Active Directory Authentication Library (ADAL) Android アプリを Microsoft Authentication Library (MSAL) に移行する方法を学習します。
services: active-directory
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Android
ms.workload: identity
ms.date: 09/6/2019
ms.author: twhitney
ms.reviewer: shoatman
ms.custom: aaddev
ms.openlocfilehash: 0c7b059fd8d9b34ad6d1e565431d39ffc706cb45
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2020
ms.locfileid: "76696709"
---
# <a name="adal-to-msal-migration-guide-for-android"></a>Android 用の ADAL から MSAL への移行に関するガイド

この記事では、Microsoft Authentication Library (MSAL) を使用するために、Azure Active Directory Authentication Library (ADAL) を使っているアプリを移行するのに必要な変更について重点的に説明します。

## <a name="difference-highlights"></a>主な相違点

ADAL は、Azure Active Directory v1.0 エンドポイントで動作します。 Microsoft Authentication Library (MSAL) は、以前は Azure Active Directory v2.0 エンドポイントと呼ばれていた、Microsoft ID プラットフォームと連動します。 Microsoft ID プラットフォームは、以下の点で Azure Active Directory v1.0 とは異なります。

サポートするものは次のとおりです。
  - 組織 ID (Azure Active Directory)
  - Outlook.com、Xbox Live などの組織以外の ID
  - (B2C のみ) Google、Facebook、Twitter、Amazon とのフェデレーション ログイン

- 標準は以下のものと互換性があります。
  - OAuth v2.0
  - OpenID Connect (OIDC)

MSAL パブリック API には、次のような重要な変更が導入されています。

- トークンにアクセスするための新しいモデル:
  - ADAL では、サーバーを表す、`AuthenticationContext` を使用してトークンにアクセスできます。 MSAL では、クライアントを表す、`PublicClientApplication` を使用してトークンにアクセスできます。 クライアント開発者は、やりとりが必要なすべての機関に対して、新しい `PublicClientApplication` インスタンスを作成する必要はありません。 必要な `PublicClientApplication` 構成は 1 つだけです。
  - リソース識別子に加え、スコープを使用したアクセス トークンを要求するためのサポート。
  - 増分同意のサポート。 開発者は、アプリの登録時に含められなかったものを含め、ユーザーがアプリでより多くの機能にアクセスするときにスコープを要求できます。
  - 実行時に機関が検証されなくなりました。 代わりに、開発者が開発時に "既知の機関" のリストを宣言します。
- トークン API の変更:
  - ADAL では、`AcquireToken()` が最初にサイレント要求を行います。 それに失敗すると、対話型の要求を行います。 この動作により、一部の開発者は `AcquireToken` のみに依存しているため、ユーザーが時には予期せず資格情報の入力を求められる結果になりました。 MSAL では、ユーザーがいつ UI プロンプトを受け取るかを開発者が意図しておく必要があります。
    - `AcquireTokenSilent` では常に、サイレント要求は成功か失敗のいずれかになります。
    - `AcquireToken` は常に、UI からユーザーにプロンプトを表示する要求になります。
- MSAL では、既定のブラウザーまたは埋め込み Web ビューからのサインインがサポートされます。
  - 既定では、デバイスの既定のブラウザーが使用されます。 これにより、MSAL では、1 つまたは複数のサインイン アカウントに対して既に存在している可能性がある認証状態 (Cookie) を使用できるようになります。 認証状態が存在しない場合、MSAL による承認時の認証では、同じブラウザーで使用される他の Web アプリケーションのために認証状態 (Cookie) が作成されます。
- 新しい例外モデル:
  - 例外では、発生したエラーの種類と、それを解決するために開発者が行う必要があることについて、より明確に定義されています。
- MSAL では、`AcquireToken` および `AcquireTokenSilent` 呼び出しのパラメーター オブジェクトがサポートされます。
- MSAL では、次の宣言型構成がサポートされます。
  - クライアント ID、リダイレクト URI。
  - 埋め込みと既定のブラウザー
  - 機関
  - 読み取りと接続のタイムアウトなどの HTTP 設定

## <a name="your-app-registration-and-migration-to-msal"></a>アプリの登録と MSAL への移行

MSAL を使用するために既存アプリの登録を変更する必要はありません。 増分/プログレッシブ同意を利用する場合は、段階的に要求する特定のスコープを識別するために登録の確認が必要になることがあります。 スコープと増分同意の詳細は以下のとおりです。

ポータルのアプリ登録では、 **[API のアクセス許可]** タブが表示されます。これにより、アプリがアクセスを要求するように現在構成されている API とアクセス許可 (スコープ) のリストが提供されます。 また、各 API アクセス許可に関連付けられているスコープ名のリストも表示されます。

### <a name="user-consent"></a>ユーザーの同意

ADAL と AAD v1 エンドポイントでは、ユーザーが所有しているリソースに対するそのユーザーの同意が、最初の使用時に許可されました。 MSAL と Microsoft ID プラットフォームでは、同意を段階的に要求できます。 増分同意は、ユーザーが高い特権を検討する可能性があるか、アクセス許可が必要な理由について明確な説明が提供されていない場合はそれが問われる可能性があるアクセス許可の際に便利です。 ADAL では、これらのアクセス許可によって、アプリへのユーザーのサインインが中止された可能性があります。

> [!TIP]
> アプリでアクセス許可を必要とする理由についてユーザーに追加のコンテキストを提供する必要がある場合は、増分同意を使用することをお勧めします。

### <a name="admin-consent"></a>管理者の同意

組織の管理者は、その組織のすべてのメンバーに代わって、アプリケーションで要求されるアクセス許可に同意できます。 一部の組織では、管理者がアプリケーションに同意することのみが許可されます。 管理者の同意では、アプリの登録にアプリケーションで使用されるすべての API のアクセス許可とスコープを含める必要があります。

> [!TIP]
> アプリの登録に含まれていないものに対して MSAL を使用してスコープを要求できますが、ユーザーがこれまでアクセス許可を付与できたすべてのリソースとスコープを含めるように、アプリの登録を更新することをお勧めします。

## <a name="migrating-from-resource-ids-to-scopes"></a>リソース ID からスコープへの移行

### <a name="authenticate-and-request-authorization-for-all-permissions-on-first-use"></a>最初の使用時にすべてのアクセス許可に対して認証および承認の要求を行う

現在、ADAL を使用しており、増分同意を使用する必要がない場合、MSAL の使用を開始する最も簡単な方法は、新しい `AcquireTokenParameter` オブジェクトを使用して、リソース ID 値を設定することで `acquireToken` 要求を行うことです。

> [!CAUTION]
> スコープとリソース ID の両方を設定することはできません。両方を設定しようとすると、`IllegalArgumentException` になります。

 これにより、これまでと同じ v1 の動作となります。 アプリの登録で要求されるすべてのアクセス許可は、最初のやり取り時にユーザーから要求されます。

### <a name="authenticate-and-request-permissions-only-as-needed"></a>必要に応じてのみ、アクセス許可を認証して要求する

増分同意を利用するには、アプリがアプリの登録から使用するアクセス許可 (スコープ) のリストを作成し、以下に基づいて 2 つのリストにまとめます。

- サインイン時にユーザーがアプリと最初にやり取りするときに要求するスコープ。
- ユーザーに説明する必要がある、アプリの重要な機能に関連付けられているアクセス許可。

スコープを整理したら、トークンを要求するリソース (API) ごとに各リストを整理します。 また、同時にユーザーの承認を行うその他のスコープについても同様です。

MSAL に対して要求を行うために使用されるパラメーター オブジェクトでは、次のものがサポートされます。

- `Scope`:承認を要求し、アクセス トークンを受信するスコープのリスト。
- `ExtraScopesToConsent`:別のリソースのアクセス トークンを要求しているときに、承認を要求するスコープの追加リスト。 このスコープのリストを使用すると、ユーザーの承認の要求が必要になる回数を最小限に抑えることができます。 これは、ユーザーの承認や同意を求めるプロンプトが減ることを意味します。

## <a name="migrate-from-authenticationcontext-to-publicclientapplications"></a>AuthenticationContext から PublicClientApplication に移行する

### <a name="constructing-publicclientapplication"></a>PublicClientApplication の構築

MSAL を使用する場合は、`PublicClientApplication` をインスタンス化します。 このオブジェクトでは、アプリ ID をモデル化し、1 つまたは複数の機関に要求を行うために使用されます。 このオブジェクトでは、クライアント ID、リダイレクト URI、既定の機関、デバイス ブラウザーと埋め込み Web ビューのどちらを使用するか、ログ レベルなどを構成します。

このオブジェクトは、JSON を使用して、ファイルとして提供するか、APK 内にリソースとして格納するかを宣言によって構成できます。

このオブジェクトはシングルトンではありませんが、内部的には、対話型とサイレントの両方の要求に対して、共有 `Executors` が使用されます。

### <a name="business-to-business"></a>企業間

ADAL では、アクセス トークンを要求するすべての組織に、`AuthenticationContext` の個別のインスタンスが必要です。 MSAL では、これは要件ではなくなりました。 サイレントまたは対話型の要求の一部として、トークンを要求する機関を指定できます。

### <a name="migrate-from-authority-validation-to-known-authorities"></a>機関の検証から既知の機関に移行する

MSAL には、機関の検証を有効または無効にするフラグがありません。 機関の検証は ADAL の機能であり、また、初期リリースの MSAL では、コードでの悪意のある可能性がある機関からのトークンの要求を防ぎます。 MSAL では、Microsoft にとって既知のが機関のリストを取得し、そのリストを、構成で指定した機関とマージするようになりました。

> [!TIP]
> Azure の企業-消費間 (B2C) ユーザーの場合、これは証明機関の検証を無効にする必要がなくなったことを意味します。 代わりに、サポートされている Azure AD B2C ポリシーをそれぞれ機関として MSAL 構成に含めます。

Microsoft では知られておらず、構成に含まれていない機関を使用しようとすると、`UnknownAuthorityException` が表示されます。

### <a name="logging"></a>ログ記録
次のように、宣言によってログ記録を構成の一部として構成できるようになりました。

 ```
 "logging": {
    "pii_enabled": false,
    "log_level": "WARNING",
    "logcat_enabled": true
  }
  ```

## <a name="migrate-from-userinfo-to-account"></a>UserInfo から Account に移行する

ADAL では、`AuthenticationResult` によって、認証されたアカウントに関する情報を取得するために使用される `UserInfo` オブジェクトが提供されます。 人間またはソフトウェア エージェントを意味する、"ユーザー" という用語は、(人間かソフトウェア エージェントかに関わらず) 複数のアカウントを持つ単一のユーザーが一部のアプリでサポートされる、ということを伝えるのを難しくする方法で適用されました。

銀行口座について考えてみましょう。 複数の金融機関に複数の口座がある場合があります。 口座を開くと、あなた (ユーザー) に、各口座について、残高や請求書の支払いなどにアクセスするために使用される、ATM カードと PIN などの資格情報が発行されます。 これらの資格情報は、それらを発行した金融機関でのみ使用することができます。

たとえば、金融機関の口座のように、Microsoft ID プラットフォームのアカウントには資格情報を使用してアクセスされます。 これらの資格情報は、Microsoft に登録されているか、Microsoft によって発行されます。 または、組織に代わって Microsoft によって行われます。

この例では、Microsoft ID プラットフォームと金融機関との違いは、Microsoft ID プラットフォームでは、ユーザーが 1 つのアカウントとそれに関連付けられた資格情報を使用して、複数の個人と組織に属するリソースにアクセスできるようにするフレームワークが提供されることです。 これは、1 つの銀行によって発行されたカードを、さらに別の金融機関でも使用できるようにするようなものです。 これは、対象のすべての組織で Microsoft ID プラットフォームを使うためです。これにより、1 つのアカウントを複数の組織間で使用できるようになります。 次に例を示します。

Sam は Contoso.com で働いていますが、Fabrikam.com に属している Azure 仮想マシンを管理しています。 Sam が Fabrikam の仮想マシンを管理するには、それらにアクセスする権限が必要です。 このアクセス権の付与は、Sam のアカウントを Fabrikam.com に追加し、そのアカウントに、仮想マシンを操作できるようにするロールを付与することで行うことができます。 これは、Azure portal を使用して行われます。

Sam の Contoso.com アカウントを Fabrikam.com のメンバーとして追加すると、Sam 用に Fabrikam.com の Azure Active Directory に新しいレコードが作成されます。 Azure Active Directory の Sam のレコードは、ユーザー オブジェクトと呼ばれます。 この場合、そのユーザー オブジェクトは Contoso.com の Sam のユーザー オブジェクトを指します。 Sam の Fabrikam ユーザー オブジェクトは、Sam のローカル表現であり、Fabrikam.com のコンテキストで Sam に関連付けられているアカウントに関する情報を格納するために使用されます。 Contoso.com では、Sam の役職はシニア DevOps コンサルタントです。 Fabrikam では、Sam の役職は契約社員 - 仮想マシンです。 Contoso.com では、Sam は仮想マシンを管理する責任も権限もありません。 Fabrikam.com では、それが彼の唯一の職務です。 しかし、Sam にはまだ追跡する 1 セットの資格情報があります。これらは、Contoso.com によって発行されたものです。

正常に `acquireToken` 呼び出しが行われると、後の `acquireTokenSilent` 要求で使用できる `IAccount` オブジェクトへの参照が表示されます。

### <a name="imultitenantaccount"></a>IMultiTenantAccount

アカウントが表されている各テナントのアカウントに関する要求にアクセスするアプリがある場合は、`IAccount` オブジェクトを `IMultiTenantAccount` にキャストできます。 このインターフェイスでは、現在のアカウントを基準とし、トークンを要求した各テナントのアカウントに属している要求にアクセスできるようにする、テナント ID でキー指定された、`ITenantProfiles` のマップが提供されます。

`IAccount` および `IMultiTenantAccount` のルートにある要求には、常にホーム テナントからの要求が含まれます。 ホーム テナント内のトークンをまだ要求していない場合、このコレクションは空になります。

## <a name="other-changes"></a>その他の変更点

### <a name="use-the-new-authenticationcallback"></a>新しい AuthenticationCallback を使用する

```java
// Existing ADAL Interface
public interface AuthenticationCallback<T> {

    /**
     * This will have the token info.
     *
     * @param result returns <T>
     */
    void onSuccess(T result);

    /**
     * Sends error information. This can be user related error or server error.
     * Cancellation error is AuthenticationCancelError.
     *
     * @param exc return {@link Exception}
     */
    void onError(Exception exc);
}
```

```java
// New Interface for Interactive AcquireToken
public interface AuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException}
     */
    void onError(final MsalException exception);

    /**
     * Will be called if user cancels the flow.
     */
    void onCancel();
}

// New Interface for Silent AcquireToken
public interface SilentAuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException} or
     *                  {@link MsalUiRequiredException}.
     */
    void onError(final MsalException exception);
}


```

## <a name="migrate-to-the-new-exceptions"></a>新しい例外に移行する

ADAL には、`AuthenticationException` という 1 つの種類の例外があり、これには、`ADALError` 列挙値を取得するためのメソッドが含まれています。
MSAL には、例外の階層があり、それぞれに関連する特定のエラー コードの独自のセットがあります。

MSAL 例外のリスト

|例外  | 説明  |
|---------|---------|
| `MsalException`     | MSAL によってスローされた既定のチェック例外。  |
| `MsalClientException`     | エラーがクライアント側である場合にスローされます。 |
| `MsalArgumentException`     | 1 つまたは複数の入力引数が無効な場合にスローされます。 |
| `MsalClientException`     | エラーがクライアント側である場合にスローされます。 |
| `MsalServiceException`     | エラーがサーバー側である場合にスローされます。 |
| `MsalUserCancelException`     | ユーザーが認証フローをキャンセルした場合にスローされます。  |
| `MsalUiRequiredException`     | トークンをサイレントで更新できない場合にスローされます。  |
| `MsalDeclinedScopeException`     | 要求された 1 つまたは複数のスコープがサーバーによって拒否された場合にスローされます。  |
| `MsalIntuneAppProtectionPolicyRequiredException` | リソースで MAMCA 保護ポリシーが有効になっている場合にスローされます。 |

### <a name="adalerror-to-msalexception-errorcode"></a>ADALError から MsalException ErrorCode

### <a name="adal-logging-to-msal-logging"></a>ADAL ログ記録から MSAL ログ記録

```java
// Legacy Interface
    StringBuilder logs = new StringBuilder();
    Logger.getInstance().setExternalLogger(new ILogger() {
            @Override
            public void Log(String tag, String message, String additionalMessage, LogLevel logLevel, ADALError errorCode) {
                logs.append(message).append('\n');
            }
        });
```

```java
// New interface
  StringBuilder logs = new StringBuilder();
  Logger.getInstance().setExternalLogger(new ILoggerCallback() {
            @Override
            public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII) {
                logs.append(message).append('\n');
            }
        });

// New Log Levels:
public enum LogLevel
{
        /**
         * Error level logging.
         */
        ERROR,
        /**
         * Warning level logging.
         */
        WARNING,
        /**
         * Info level logging.
         */
        INFO,
        /**
         * Verbose level logging.
         */
        VERBOSE
}
```
