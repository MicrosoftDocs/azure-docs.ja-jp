---
title: .NET で Azure AD Authentication を使用して Azure Media Services API にアクセスする | Microsoft Docs
description: このトピックでは、.NET で Azure Active Directory (Azure AD) Authentication を使用して Azure Media Services (AMS) API にアクセスする方法を示します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: has-adal-ref, devx-track-csharp
ms.openlocfilehash: fd19bce05d18eb8f9b8fd3806082caeebe98bdad
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013994"
---
# <a name="use-azure-ad-authentication-to-access-azure-media-services-api-with-net"></a>.NET で Azure AD Authentication を使用して Azure Media Services API にアクセスする

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Media Services v2 には新機能は追加されません。 <br/>最新のバージョンである [Media Services v3](../latest/index.yml) をご確認ください。 また、[v2 から v3 への移行ガイダンス](../latest/migrate-v-2-v-3-migration-introduction.md)を参照してください。

Windowsazure.mediaservices 4.0.0.4 以降では、Azure Media Services で Azure Active Directory (Azure AD) に基づく認証がサポートされます。 このトピックでは、Microsoft .NET で Azure AD Authentication を使用して Azure Media Services API にアクセスする方法を示します。

## <a name="prerequisites"></a>前提条件

- Azure アカウント。 詳細については、[Azure の無料評価版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。
- Media Services アカウント。 詳細については、「[Azure Portal を使用した Azure Media Services アカウントの作成](media-services-portal-create-account.md)」を参照してください。
- 最新の [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) パッケージ。
- [Azure AD 認証による Azure Media Services API へのアクセスの概要](media-services-use-aad-auth-to-access-ams-api.md)を示すトピックの内容をよく理解していること。

Azure Media Services で Azure AD Authentication を使用する場合は、次の 2 つの方法のいずれかで認証できます。

- **ユーザー認証**: Azure Media Services リソースを操作するアプリを使用しているユーザーが認証を受けます。 ユーザーは最初にその操作アプリケーションから資格情報の入力を求められます。 たとえば、承認済みユーザーがエンコード ジョブまたはライブ ストリーミングを監視するために使用する管理コンソール アプリなどです。
- **サービス プリンシパルの認証**: サービスを認証します。 この認証方法がよく使用されるアプリケーションは、デーモン サービス、中間層サービス、またはスケジュールされたジョブを実行するアプリ (例: Web アプリ、関数アプリ、ロジック アプリ、API、マイクロサービス) です。

>[!IMPORTANT]
>現在、Azure Media Services では Azure Access Control Service 認証モデルがサポートされています。 しかし、Access Control 承認は 2018 年 6 月 22 日に非推奨となる予定です。 できるだけ早く Azure Active Directory 認証モデルに移行することをお勧めします。

## <a name="get-an-azure-ad-access-token"></a>Azure AD アクセス トークンを取得する

Azure AD Authentication を使用して Azure Media Services API に接続するには、クライアント アプリから Azure AD アクセス トークンを要求する必要があります。 Media Services .NET クライアント SDK を使用する場合、Azure AD アクセス トークンの取得方法についての詳細の多くは、[AzureAdTokenProvider](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenProvider.cs) および [AzureAdTokenCredentials](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenCredentials.cs) クラスでラップされ、簡略化されます。

たとえば、Azure AD オーソリティ、Media Services リソース URI、またはネイティブ Azure AD アプリケーションの詳細を指定する必要はありません。 これらは、Azure AD アクセス トークンのプロバイダー クラスで構成済みの既知の値です。

Azure Media Service .NET SDK を使用しない場合は、[Azure AD Authentication Library](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) を使用することをお勧めします。 Azure AD Authentication Library で使用する必要があるパラメーターの値を取得する場合は、[Azure Portal を使用した Azure AD Authentication 設定へのアクセス](media-services-portal-get-started-with-aad.md)に関するページを参照してください。

**AzureAdTokenProvider** の既定の実装を、ユーザー独自の実装に置き換えることもできます。

## <a name="install-and-configure-azure-media-services-net-sdk"></a>Azure Media Services .NET SDK をインストールして構成する

>[!NOTE]
>Media Services .NET SDK で Azure AD Authentication を使用するには、最新の [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) パッケージが必要です。 また、**Microsoft.IdentityModel.Clients.ActiveDirectory** アセンブリへの参照を追加します。 既存のアプリを使用する場合は、**Microsoft.WindowsAzure.MediaServices.Client.Common.Authentication.dll** アセンブリを含めます。

1. Visual Studio で、新しい C# コンソール アプリケーションを作成します。
2. NuGet パッケージ [windowsazure.mediaservices](https://www.nuget.org/packages/windowsazure.mediaservices) を使用して、**Azure Media Services .NET SDK** をインストールします。

    NuGet を使用して参照を追加するには、**ソリューション エクスプローラー** で、プロジェクト名を右クリックし、 **[NuGet パッケージの管理]** を選択します。 次に、**windowsazure.mediaservices** を検索し、 **[インストール]** を選択します。

    または

    Visual Studio の **パッケージ マネージャー コンソール** で、次のコマンドを実行します。

    ```console
    Install-Package windowsazure.mediaservices -Version 4.0.0.4
    ```

3. ソース コードに **using** を追加します。

    ```csharp
    using Microsoft.WindowsAzure.MediaServices.Client;
    ```

## <a name="use-user-authentication"></a>ユーザー認証を使用する

ユーザー認証オプションを指定して Azure Media Service API に接続するには、クライアント アプリから、以下のパラメーターを使用して、Azure AD トークンを要求する必要があります。

- Azure AD テナント エンドポイント。 テナント情報は Azure Portal から取得できます。 右上隅のサインインしたユーザーにカーソルを置きます。
- Media Services リソース URI。
- Media Services (ネイティブ) アプリケーション クライアント ID。
- Media Services (ネイティブ) アプリケーション リダイレクト URI。

これらのパラメーターの値は **AzureEnvironments.AzureCloudEnvironment** にあります。 **AzureEnvironments.AzureCloudEnvironment** 定数は、.NET SDK でパブリック Azure データ センターに対する正しい環境変数設定を取得するためのヘルパーです。

パブリック データ センターでのみ Media Services にアクセスするための定義済みの環境設定が含まれています。 ソブリンまたは政府のクラウド領域の場合は、**AzureChinaCloudEnvironment**、**AzureUsGovernmentEnvironment**、または **AzureGermanCloudEnvironment** をそれぞれ使用できます。

次のコード例ではトークンを作成します。

```csharp
var tokenCredentials = new AzureAdTokenCredentials("microsoft.onmicrosoft.com", AzureEnvironments.AzureCloudEnvironment);
var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
```

Media Services に対するプログラミングを開始するには、サーバー コンテキストを表す **CloudMediaContext** インスタンスを作成する必要があります。 **CloudMediaContext** には、ジョブ、アセット、ファイル、アクセス ポリシー、ロケーターなどの重要なコレクションへの参照が含まれています。

**CloudMediaContext** コンストラクターに **Media REST Services のリソース URI** を渡す必要もあります。 Media REST Services のリソース URI を取得するには、Azure Portal にサインインして、Azure Media Services アカウントを選択し、 **[API アクセス]** を選択してから **[Connect to Azure Media Services with user authentication]\(ユーザー認証を使用して Azure Media Services に接続する\)** を選択します。

次のコード例では **CloudMediaContext** インスタンスを作成します。

```csharp
CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
```

次の例には、Azure AD トークンとコンテキストを作成する方法が示されています。

```csharp
namespace AzureADAuthSample
{
    class Program
    {
        static void Main(string[] args)
        {
            // Specify your Azure AD tenant domain, for example "microsoft.onmicrosoft.com".
            var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            // Specify your REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".
            CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);

            var assets = context.Assets;
            foreach (var a in assets)
            {
                Console.WriteLine(a.Name);
            }
        }
    }
}
```

>[!NOTE]
>"リモート サーバーがエラーを返しました: (401) 未承認" という例外を受け取る場合は、Azure AD 認証による Azure Media Services API へのアクセスの概要の「[アクセス制御](media-services-use-aad-auth-to-access-ams-api.md#access-control)」セクションを確認してください。

## <a name="use-service-principal-authentication"></a>サービス プリンシパル認証を使用する

サービス プリンシパル オプションを指定して Azure Media Services API に接続するには、中間層アプリ (Web API または Web アプリケーション) から、以下のパラメーターを使用して Azure AD トークンを要求する必要があります。

- Azure AD テナント エンドポイント。 テナント情報は Azure Portal から取得できます。 右上隅のサインインしたユーザーにカーソルを置きます。
- Media Services リソース URI。
- Azure AD アプリケーションの値: **クライアント ID** と **クライアント シークレット**。

**[クライアント ID]** と **[クライアント シークレット]** パラメーターの値は Azure Portal で確認できます。 詳細については、「[Getting started with Azure AD authentication using the Azure portal](media-services-portal-get-started-with-aad.md)」 (Azure Portal を使用する Azure AD Authentication の概要) を参照してください。

次のコード例では、パラメーターとして **AzureAdClientSymmetricKey** を取る **AzureAdTokenCredentials** コンストラクターを使用して、トークンを作成します。

```csharp
var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}",
                        new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"),
                        AzureEnvironments.AzureCloudEnvironment);

var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
```

パラメーターとして **AzureAdClientCertificate** を取る **AzureAdTokenCredentials** コンストラクターを指定することもできます。

Azure AD で使用できるフォームで証明書を作成して構成する方法の手順については、「[Authenticating to Azure AD in daemon apps with certificates - manual configuration steps](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2)」 (証明書を使用してデーモン アプリで Azure AD を認証する - 手動による構成手順) を参照してください。

```csharp
var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}",
                        new AzureAdClientCertificate("{YOUR CLIENT ID HERE}", "{YOUR CLIENT CERTIFICATE THUMBPRINT}"),
                        AzureEnvironments.AzureCloudEnvironment);
```

Media Services に対するプログラミングを開始するには、サーバー コンテキストを表す **CloudMediaContext** インスタンスを作成する必要があります。 **CloudMediaContext** コンストラクターに **Media REST Services のリソース URI** を渡す必要もあります。 **Media REST Services のリソース URI** は Azure Portal から取得することもできます。

次のコード例では **CloudMediaContext** インスタンスを作成します。

```csharp
CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
```

次の例には、Azure AD トークンとコンテキストを作成する方法が示されています。

```csharp
namespace AzureADAuthSample
{
    class Program
    {
        static void Main(string[] args)
        {
            var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}",
                                        new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"),
                                        AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            // Specify your REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".
            CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);

            var assets = context.Assets;
            foreach (var a in assets)
            {
                Console.WriteLine(a.Name);
            }

            Console.ReadLine();
        }
    }
}
```

## <a name="next-steps"></a>次のステップ

[アカウントへのファイルのアップロード](media-services-dotnet-upload-files.md)を開始します。
