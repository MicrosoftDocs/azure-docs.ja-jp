---
title: Microsoft Graph を使用してリソースを管理する
titleSuffix: Azure AD B2C
description: Microsoft Graph API を呼び出し、アプリケーション ID を使用してプロセスを自動化して、Azure AD B2C テナントのリソースを管理する方法。
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/28/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 41336d59d51685d5daf78a1809ce6c0df2cd6124
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781315"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>Microsoft Graph を使用して Azure AD B2C を管理する

Microsoft Graph では、お使いの Azure AD B2C ディレクトリ内のリソースを管理できます。 次の Microsoft Graph API 操作は、ユーザー、ID プロバイダー、ユーザーフロー、カスタム ポリシー、およびポリシー キーなど、Azure AD B2C リソースの管理に対してサポートされています。 次のセクションの各リンクは、その操作の Microsoft Graph API リファレンス内の対応するページを対象としています。 

## <a name="prerequisites"></a>[前提条件]

MS Graph API を使用し、Azure AD B2C テナント内のリソースを操作するには、そのためのアクセス許可を付与するアプリケーション登録が必要になります。 「[Microsoft Graph を使用して Azure AD B2C を管理する](microsoft-graph-get-started.md)」という記事の手順に従って、管理アプリケーションで使用できるアプリケーション登録を作成します。 

## <a name="user-management"></a>[ユーザー管理]

- [ユーザーの一覧表示](/graph/api/user-list)
- [コンシューマー ユーザーを作成する](/graph/api/user-post-users)
- [ユーザーの取得](/graph/api/user-get)
- [ユーザーの更新](/graph/api/user-update)
- [ユーザーの削除](/graph/api/user-delete)

## <a name="user-phone-number-management-beta"></a>ユーザーの電話番号の管理 (ベータ)

[SMS または音声通話](identity-provider-local.md#phone-sign-in-preview)あるいは[多要素認証](multi-factor-authentication.md)を使用してサインインするためにユーザーが使用できる電話番号。 詳細については、[Azure AD 認証方法の API](/graph/api/resources/phoneauthenticationmethod) に関する記事を参照してください。

- [追加](/graph/api/authentication-post-phonemethods)
- [リスト](/graph/api/authentication-list-phonemethods)
- [Get](/graph/api/phoneauthenticationmethod-get)
- [アップデート](/graph/api/phoneauthenticationmethod-update)
- [削除](/graph/api/phoneauthenticationmethod-delete)

[リスト](/graph/api/authentication-list-phonemethods)操作では、有効な電話番号のみが返されます。 リスト操作で使用するためには、次の電話番号を有効にする必要があります。 

![電話によるサインインを有効にする](./media/microsoft-graph-operations/enable-phone-sign-in.png)

## <a name="self-service-password-reset-email-address-beta"></a>セルフサービス パスワード リセットのメール アドレス (ベータ)

パスワードをリセットするために[ユーザー名サインイン アカウント](identity-provider-local.md#username-sign-in)で使用できるメール アドレス。 詳細については、[Azure AD 認証方法の API](/graph/api/resources/emailauthenticationmethod) に関する記事を参照してください。

- [追加](/graph/api/emailauthenticationmethod-post)
- [リスト](/graph/api/emailauthenticationmethod-list)
- [Get](/graph/api/emailauthenticationmethod-get)
- [アップデート](/graph/api/emailauthenticationmethod-update)
- [削除](/graph/api/emailauthenticationmethod-delete)

## <a name="identity-providers"></a>ID プロバイダー

Azure AD B2C テナントのユーザー フローで使用できる [ID プロバイダー](add-identity-provider.md)を管理します。

- [Azure AD B2C テナントに登録されている ID プロバイダーの一覧表示](/graph/api/identityprovider-list)
- [ID プロバイダーの追加](/graph/api/identityprovider-post-identityproviders)
- [ID プロバイダーの取得](/graph/api/identityprovider-get)
- [ID プロバイダーの更新](/graph/api/identityprovider-update)
- [ID プロバイダーの削除](/graph/api/identityprovider-delete)

## <a name="user-flow"></a>ユーザー フロー

サインアップ、サインイン、パスワードのリセット、プロファイルの更新のための事前構築されたポリシーを構成します。

- [ユーザー フローの一覧表示](/graph/api/identitycontainer-list-b2cuserflows)
- [ユーザー フローを作成する](/graph/api/identitycontainer-post-b2cuserflows)
- [ユーザー フローの取得](/graph/api/b2cidentityuserflow-get)
- [ユーザー フローの削除](/graph/api/b2cidentityuserflow-delete)

## <a name="user-flow-authentication-methods-beta"></a>ユーザー フローの認証方法 (ベータ)

ローカル アカウントを使用してユーザーが登録を実行できるメカニズムを選択します。 ローカル アカウントは、Azure AD が ID アサーションを行うアカウントです。 詳細については、「[b2cAuthenticationMethodsPolicy リソースの種類](/graph/api/resources/b2cauthenticationmethodspolicy)」を参照してください。

- [Get](/graph/api/b2cauthenticationmethodspolicy-get)
- [アップデート](/graph/api/b2cauthenticationmethodspolicy-update)

## <a name="custom-policies"></a>カスタム ポリシー

次の操作を使用すると、[カスタムポリシー](custom-policy-overview.md)と呼ばれる Azure AD B2C 信頼フレームワーク ポリシーを管理できます。

- [テナントで構成されているすべての信頼フレームワーク ポリシーを一覧表示する](/graph/api/trustframework-list-trustframeworkpolicies)
- [信頼フレームワーク ポリシーの作成](/graph/api/trustframework-post-trustframeworkpolicy)
- [既存の信頼フレームワーク ポリシーのプロパティの読み取り](/graph/api/trustframeworkpolicy-get)
- [信頼フレームワーク ポリシーを更新または作成します。](/graph/api/trustframework-put-trustframeworkpolicy)
- [既存の信頼フレームワークポリシーを削除する](/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>ポリシー キー

Identity Experience Framework では、カスタムポリシーで参照されているシークレットを格納して、コンポーネント間の信頼を確立します。 これらのシークレットは、対称キー/値または非対称キー/値にすることができます。 Azure portal では、これらのエンティティは **ポリシーキー** として表示されます。

Microsoft Graph API のポリシー キーの最上位レベルのリソースは、[Trusted Framework Keyset](/graph/api/resources/trustframeworkkeyset)です。 各 **キーセット** には、少なくとも1つの **キー** が含まれています。 キーを作成するには、最初に空のキーセットを作成してから、キーセットにキーを生成します。 手動シークレットを作成したり、証明書をアップロードしたり、または PKCS12 キーを使用したりすることができます。 キーには、生成されたシークレット、文字列 (Facebook アプリケーション シークレットなど)、またはアップロードする証明書を指定できます。 キーセットに複数のキーがある場合、1つのキーのみがアクティブになります。

### <a name="trust-framework-policy-keyset"></a>信頼フレームワーク ポリシー キーセット

- [信頼フレームワーク キーセットの一覧表示](/graph/api/trustframework-list-keysets)
- [信頼フレームワーク キーセットの作成](/graph/api/trustframework-post-keysets)
- [キーセットの取得](/graph/api/trustframeworkkeyset-get)
- [信頼フレームワーク キーセットの更新](/graph/api/trustframeworkkeyset-update)
- [信頼フレームワーク キーセットの削除](/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>信頼フレームワーク ポリシー キー

- [キーセットで現在アクティブなキーの取得](/graph/api/trustframeworkkeyset-getactivekey)
- [キーセットでキーの生成](/graph/api/trustframeworkkeyset-generatekey)
- [文字列ベースのシークレットのアップロード](/graph/api/trustframeworkkeyset-uploadsecret)
- [X.509 証明書のアップロード](/graph/api/trustframeworkkeyset-uploadcertificate)
- [PKCS12 形式の証明書のアップロード](/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>アプリケーション

- [List applications](/graph/api/application-list)
- [アプリケーションの作成](/graph/api/resources/application)
- [アプリケーションの更新](/graph/api/application-update)
- [servicePrincipal の作成](/graph/api/resources/serviceprincipal)
- [oauth2Permission Grant の作成](/graph/api/resources/oauth2permissiongrant)
- [アプリケーションを削除する](/graph/api/application-delete)

## <a name="application-extension-properties"></a>アプリケーション拡張機能プロパティ

- [拡張機能プロパティの一覧表示](/graph/api/application-list-extensionproperty)

Azure AD B2C で提供されているディレクトリには、ユーザーごとに 100 個のカスタム属性を保持できます。 ユーザーフローの場合、これらの拡張機能プロパティは、[Azure portal を使用して管理します](user-flow-custom-attributes.md)。 カスタム ポリシーの場合、ポリシーが拡張機能プロパティに値を初めて書き込むときに、Azure AD B2C によってプロパティが作成されます。

## <a name="audit-logs"></a>監査ログ

- [監査ログの一覧表示](/graph/api/directoryaudit-list)

Azure AD B2C 監査ログにアクセスする方法の詳細については、「[Azure AD B2C 監査ログにアクセスする](view-audit-logs.md)」を参照してください。

## <a name="conditional-access"></a>条件付きアクセス

- [すべての条件付きアクセス ポリシーの一覧](/graph/api/conditionalaccessroot-list-policies?view=graph-rest-beta&tabs=http)
- [条件付きアクセス ポリシーのプロパティとリレーションシップを読む](/graph/api/conditionalaccesspolicy-get)
- [新しい条件付きアクセス ポリシーを作成する](/graph/api/resources/application)
- [条件付きアクセス ポリシーを更新する](/graph/api/conditionalaccesspolicy-update)
- [条件付きアクセス ポリシーを削除する](/graph/api/conditionalaccesspolicy-delete)

## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>コード サンプル:プログラムによってユーザー アカウントを管理する方法

このコード サンプルは、[Microsoft Graph SDK](/graph/sdks/sdks-overview) を使用して Microsoft Graph API と対話する .NET Core コンソール アプリケーションです。 このコードは、API を呼び出して、Azure AD B2C テナント内のユーザーをプログラムで管理する方法を示しています。
[サンプル アーカイブ (*.zip) をダウンロード](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip)するか、GitHub の[リポジトリを参照する](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management)か、リポジトリを複製することができます。

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

コード サンプルを入手したら、環境に合わせて構成し、プロジェクトをビルドします。

1. [Visual Studio](https://visualstudio.microsoft.com) または [Visual Studio Code](https://code.visualstudio.com) でプロジェクトを開きます。
1. `src/appsettings.json`を開きます。
1. 「`appSettings`」セクションで、`your-b2c-tenant` をテナントの名前に、`Application (client) ID` と `Client secret` を管理アプリケーション登録の値に置き換えます。 詳しくは、「[Microsoft Graph アプリケーションを登録する](microsoft-graph-get-started.md)」をご覧ください。
1. リポジトリのローカル複製内でコンソール ウィンドウを開き、`src` ディレクトリに切り替えてから、プロジェクトをビルドします。

    ```console
    cd src
    dotnet build
    ```
    
1. `dotnet` コマンドを使用してアプリケーションを実行します。

    ```console
    dotnet bin/Debug/netcoreapp3.1/b2c-ms-graph.dll
    ```

アプリケーションで、実行可能なコマンドの一覧が表示されます。 たとえば、すべてのユーザーの取得、単一ユーザーの取得、ユーザーの削除、ユーザーのパスワードの更新、一括インポートなどです。

### <a name="code-discussion"></a>コードの説明

このサンプル コードでは、Microsoft Graph にアクセスする高品質かつ効率的で回復性があるアプリケーションを簡単に構築できるように設計されている、[Microsoft Graph SDK](/graph/sdks/sdks-overview) を使用します。

Microsoft Graph API への要求には、認証のためのアクセス トークンが必要になります。 このソリューションでは、Microsoft Graph SDK で使用する Microsoft Authentication Library (MSAL) の認証シナリオベースのラッパーを提供する、 [Microsoft.Graph.Auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) NuGet パッケージを使用します。

_Program.cs_ ファイル内の `RunAsync` メソッド:

1. _appsettings.json_ ファイルからアプリケーション設定を読み取ります
1. [OAuth 2.0 クライアント資格情報の付与](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)フローを使用して、認証プロバイダーを初期化します。 クライアント資格情報の付与フローを使用すると、アプリは Microsoft Graph API を呼び出すためのアクセス トークンを取得できます。
1. 認証プロバイダーを使用して Microsoft Graph サービス クライアントを設定します。

    ```csharp
    // Read application settings from appsettings.json (tenant ID, app ID, client secret, etc.)
    AppSettings config = AppSettingsFile.ReadFromJsonFile();

    // Initialize the client credential auth provider
    IConfidentialClientApplication confidentialClientApplication = ConfidentialClientApplicationBuilder
        .Create(config.AppId)
        .WithTenantId(config.TenantId)
        .WithClientSecret(config.ClientSecret)
        .Build();
    ClientCredentialProvider authProvider = new ClientCredentialProvider(confidentialClientApplication);

    // Set up the Microsoft Graph service client with client credentials
    GraphServiceClient graphClient = new GraphServiceClient(authProvider);
    ```

次に、初期化された *GraphServiceClient* を _UserService.cs_ で使用して、ユーザー管理操作を実行します。 たとえば、次のようにしてテナント内のユーザー アカウントの一覧を取得します。

```csharp
public static async Task ListUsers(GraphServiceClient graphClient)
{
    Console.WriteLine("Getting list of users...");

    // Get all users (one page)
    var result = await graphClient.Users
        .Request()
        .Select(e => new
        {
            e.DisplayName,
            e.Id,
            e.Identities
        })
        .GetAsync();

    foreach (var user in result.CurrentPage)
    {
        Console.WriteLine(JsonConvert.SerializeObject(user));
    }
}
```

[Microsoft Graph SDK を使用した API 呼び出し](/graph/sdks/create-requests)に関する記事には、Microsoft Graph から情報を読み書きする方法、`$select` を使用して返されるプロパティを制御する方法、カスタム クエリ パラメーターを指定する方法、`$filter` および `$orderBy` のクエリ パラメーターを使用する方法に関する情報が含まれています。

<!-- LINK -->

[graph-objectIdentity]: /graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
