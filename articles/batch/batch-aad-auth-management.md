---
title: Azure Active Directory を使用して Batch 管理ソリューションを認証する
description: Batch Management .NET ライブラリを使用するアプリケーションでの認証に Azure Active Directory を使用する方法について説明します。
ms.topic: how-to
ms.date: 04/27/2017
ms.custom: has-adal-ref, devx-track-csharp
ms.openlocfilehash: ff49d5e88df7c56ed4dee0685f09e45eb372aa5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88930215"
---
# <a name="authenticate-batch-management-solutions-with-active-directory"></a>Batch 管理ソリューションの認証に Active Directory を使用する

Azure Batch 管理サービスを呼び出すアプリケーションは、[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) を使用して認証します。 Azure AD は、Microsoft が提供する、マルチテナントに対応したクラウド ベースのディレクトリと ID の管理サービスです。 Azure AD は、顧客、サービス管理者、組織のユーザーを認証するために、Azure 自体で使用されています。

Batch Management .NET ライブラリでは、Batch アカウント、アカウント キー、アプリケーション、およびアプリケーション パッケージを操作するための型を公開します。 Batch Management .NET ライブラリは Azure リソースプロバイダー クライアントであり、[Azure Resource Manager](../azure-resource-manager/management/overview.md) と共に使用してこれらのリソースをプログラムで管理します。 Azure AD は、Batch Management .NET ライブラリを含むすべての Azure リソース プロバイダー クライアントや、Azure Resource Manager を通じて行われた要求の認証に必要です。

この記事では、Batch Management .NET ライブラリを使用するアプリケーションでの認証に Azure AD を使用する方法について説明します。 Azure AD の統合認証を使用して、サブスクリプション管理者や共同管理者を認証する方法を説明します。 GitHub で入手できる [AccountManagement](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement) サンプル プロジェクトを使いながら、Batch Management .NET ライブラリで Azure AD を使用する手順を説明します。

Batch Management .NET ライブラリと AccountManagement サンプルの使用の詳細については、「[.NET 用の Batch 管理クライアント ライブラリを使用した Batch アカウントとクォータの管理](batch-management-dotnet.md)」を参照してください。

## <a name="register-your-application-with-azure-ad"></a>Azure AD にアプリケーションを登録する

[Azure Active Directory Authentication Library](../active-directory/azuread-dev/active-directory-authentication-libraries.md) (ADAL) は、お使いのアプリケーション内で使用できる Azure AD へのプログラム インターフェイスです。 アプリケーションから ADAL を呼び出すには、Azure AD テナントにアプリケーションを登録する必要があります。 アプリケーションを登録する場合は、アプリケーションに関する情報 (Azure AD テナント内でのアプリケーション名など) を Azure AD で指定します。 これで、Azure AD から、実行時にアプリケーションを Azure AD と関連付ける際に使用するアプリケーション ID が提供されます。 アプリケーション ID の詳細については、「[Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](../active-directory/develop/app-objects-and-service-principals.md)」を参照してください。

AccountManagement サンプル アプリケーションを登録するには、[Azure Active Directory とアプリケーションの統合](../active-directory/develop/quickstart-register-app.md)に関するページの[アプリケーションの追加](../active-directory/develop/quickstart-register-app.md)の手順に従います。 アプリケーションの種類として、 **[ネイティブ クライアント アプリケーション]** を指定します。 **リダイレクト URI** の業界標準 OAuth 2.0 に準拠した URI は `urn:ietf:wg:oauth:2.0:oob` です。 ただし、実際のエンドポイントである必要はないので、**Redirect URI** には (`http://myaccountmanagementsample` などの) 任意の URI を指定できます。

![アプリケーションの追加](./media/batch-aad-auth-management/app-registration-management-plane.png)

登録プロセスが完了すると、アプリケーションのアプリケーション ID とオブジェクト (サービス プリンシパル) ID が表示されます。

![登録プロセスの完了](./media/batch-aad-auth-management/app-registration-client-id.png)

## <a name="grant-the-azure-resource-manager-api-access-to-your-application"></a>アプリケーションに Azure Resource Manager APIへ のアクセスを許可する

次に、Azure Resource Manager API へのアクセスをアプリケーションに委任する必要があります。 Resource Manager API の Azure AD 識別子は、**Windows Azure Service Management API** です。

Azure Portal で次の手順に従います。

1. Azure Portal の左側のナビゲーション ウィンドウで、 **[すべてのサービス]** を選択し、 **[アプリの登録]** をクリックして、 **[追加]** をクリックします。
2. アプリケーション登録の一覧で、アプリケーションの名前を検索します。

    ![アプリケーションの名前を検索する](./media/batch-aad-auth-management/search-app-registration.png)

3. **[設定]** ブレードを表示します。 **[API アクセス]** セクションで、 **[必要なアクセス許可]** を選択します。
4. **[追加]** をクリックして、新しい必要なアクセス許可を追加します。
5. ステップ 1 で、「**Windows Azure Service Management API**」と入力し、結果の一覧からその API を選択して **[選択]** ボタンをクリックします。
6. ステップ 2 で、 **[Access Azure classic deployment model as organization users]\(組織のユーザーとして Azure クラシック デプロイ モデルにアクセスする\)** の横のチェック ボックスをオンにして、 **[選択]** ボタンをクリックします。
7. **[完了]** ボタンをクリックします。

**[必要なアクセス許可]** ブレードに、ADAL および Resource Manager API へのアプリケーションのアクセスが許可されたことが表示されます。 アプリを Azure AD に最初に登録する際に、既定で ADAL へのアクセス許可が付与されます。

![Azure Resource Manager API へのアクセス許可の委任](./media/batch-aad-auth-management/required-permissions-management-plane.png)

## <a name="azure-ad-endpoints"></a>Azure AD エンドポイント

Azure AD でバッチ管理ソリューションを認証するには、よく知られている 2 つのエンドポイントが必要です。

- 統合認証などのように、特定のテナントが提供されていない場合は、インターフェイスを収集する汎用的な資格情報が **Azure AD 共通のエンドポイント** から次のように提供されます。

    `https://login.microsoftonline.com/common`

- **Azure Resource Manager エンドポイント** は、Batch 管理サービスへの要求を認証するするためのトークンを取得するために次のように使用します。

    `https://management.core.windows.net/`

これらのエンドポイントの定数が AccountManagement のサンプル アプリケーションで定義されます。 これらの定数は変更しないようにします。

```csharp
// Azure Active Directory "common" endpoint.
private const string AuthorityUri = "https://login.microsoftonline.com/common";
// Azure Resource Manager endpoint
private const string ResourceUri = "https://management.core.windows.net/";
```

## <a name="reference-your-application-id"></a>アプリケーション ID の参照

クライアント アプリケーションは実行時にアプリケーション ID (クライアント ID とも呼ばれる) を使用して Azure AD にアクセスします。 Azure Portal にアプリケーションを登録したら、そのアプリケーション用に Azure AD から提供されたアプリケーション ID を使用するように、コードを更新します。 AccountManagement サンプル アプリケーションで、アプリケーション ID を Azure Portal から適切な定数にコピーします。

```csharp
// Specify the unique identifier (the "Client ID") for your application. This is required so that your
// native client application (i.e. this sample) can access the Microsoft Graph API. For information
// about registering an application in Azure Active Directory, please see "Register an application with the Microsoft identity platform" here:
// https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app
private const string ClientId = "<application-id>";
```
また、登録プロセス時に指定したリダイレクト URI をコピーします。 コードに指定するリダイレクト URI は、アプリケーションを登録したときに指定したリダイレクト URI と一致している必要があります。

```csharp
// The URI to which Azure AD will redirect in response to an OAuth 2.0 request. This value is
// specified by you when you register an application with AAD (see ClientId comment). It does not
// need to be a real endpoint, but must be a valid URI (e.g. https://accountmgmtsampleapp).
private const string RedirectUri = "http://myaccountmanagementsample";
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Azure AD 認証トークンを取得する

AccountManagement サンプルを Azure AD テナントに登録し、サンプル ソース コードの値を更新すると、Azure AD を使用してサンプルを認証できるようになります。 サンプルを実行すると、ADAL は認証トークンの取得を試みます。 この段階で、Microsoft 資格情報の入力が求められます。

```csharp
// Obtain an access token using the "common" AAD resource. This allows the application
// to query AAD for information that lies outside the application's tenant (such as for
// querying subscription information in your Azure account).
AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
AuthenticationResult authResult = authContext.AcquireToken(ResourceUri,
                                                        ClientId,
                                                        new Uri(RedirectUri),
                                                        PromptBehavior.Auto);
```

資格情報を入力すると、サンプル アプリケーションは認証された要求を Batch 管理サービスに発行できるようになります。

## <a name="next-steps"></a>次のステップ

- [AccountManagement サンプル アプリケーション](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement)の実行に関する詳細については、「[.NET 用の Batch 管理クライアント ライブラリを使用した Batch アカウントとクォータの管理](batch-management-dotnet.md)」を参照してください。
- Azure AD の詳細については、「[Azure Active Directory のドキュメント](../active-directory/index.yml)」を参照してください。
- ADAL の使用方法の詳細な例については、[Azure のコード サンプル](https://azure.microsoft.com/resources/samples/?service=active-directory) ライブラリを参照してください。
- Azure AD を使用して Batch 管理アプリケーションを認証するには、「[Batch 管理ソリューションの認証に Active Directory を使用する](batch-aad-auth.md)」を参照してください。
