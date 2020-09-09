---
title: アプリ認証コードを作成する
titleSuffix: Azure Digital Twins
description: クライアント アプリケーションに認証コードを書き込む方法を説明します
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-javascript
ms.openlocfilehash: c211c0e5ef0b39f778db7c922fafc735e2411068
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88930045"
---
# <a name="write-client-app-authentication-code"></a>クライアント アプリの認証コードを書き込む

[Azure Digital Twins のインスタンスと認証を設定](how-to-set-up-instance-scripted.md)した後、インスタンスとのやり取りに使用するクライアント アプリケーションを作成できます。 この記事では、スターター クライアント プロジェクトを設定した後で、Azure Digital Twins のインスタンスに対して**認証を行うためのコードをそのクライアント アプリに書き込む**を示します。

この記事のサンプル コードには、2 つの方法があります。 選択した言語に応じて、適切なものを使用できます。
* サンプル コードの最初のセクションでは、Azure Digital Twins .NET (C#) SDK を使用します。 SDK は Azure SDK for .NET に含まれており、次の場所にあります: "[ *.NET 用 Azure IoT Digital Twins クライアント ライブラリ*](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)"。
* サンプル コードの 2 番目のセクションは、.NET SDK を使用していないユーザー向けで、代わりに AutoRest で生成された SDK を他の言語で使用するためのものです。 この方法の詳細については、"[*AutoRest を使用して Azure Digital Twins 用のカスタム SDK を作成する方法*](how-to-create-custom-sdks.md)" に関する記事を参照してください。

Azure Digital Twins 用の API と SDK の詳細については、"[*Azure Digital Twins の API と SDK の使用方法*](how-to-use-apis-sdks.md)" に関する記事も参照してください。

## <a name="prerequisites"></a>前提条件

最初に、"[*インスタンスと認証の設定方法*](how-to-set-up-instance-scripted.md)" に関する記事のセットアップ手順を完了します。 これにより、Azure Digital Twins のインスタンスが作成され、ユーザーにアクセス許可が与えられ、クライアント アプリケーションに対するアクセス許可が設定されます。 このセットアップがすべて完了したら、クライアント アプリのコードを記述することができます。

先に進むには、コードを記述するクライアント アプリ プロジェクトが必要です。 クライアント アプリ プロジェクトをまだ設定していない場合は、このチュートリアルで使用する基本的なプロジェクトを、選択した言語で作成します。

## <a name="authentication-and-client-creation-net-c-sdk"></a>認証およびクライアントの作成: .NET (C#) SDK

最初に、このハウツー用の .NET SDK と認証ツールを使用するために、次のパッケージをプロジェクトに含めます。
* `Azure.DigitalTwins.Core` (バージョン `1.0.0-preview.2`)
* `Azure.Identity`

選択するツールによっては、Visual Studio パッケージ マネージャーまたは `dotnet` コマンド ライン ツールを使用して、パッケージを含めることができます。 

次の using ステートメントも必要です。

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
```
.NET SDK を使用して認証するには、[Azure.Identity](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet) ライブラリに定義されている資格情報の取得方法のいずれかを使用します。 次に、一般的に使用される 2 つの方法を示します (同じアプリケーションで一緒に使用される場合もあります)。

* [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet) は、対話型アプリケーションを対象とし、認証された SDK クライアントを作成するために使用できます
* [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet) は、マネージド ID (MSI) を必要とする場合に適しており、Azure Functions を操作する場合に適しています

### <a name="interactivebrowsercredential-method"></a>InteractiveBrowserCredential メソッド
[InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet) メソッドは、対話型アプリケーションを対象としており、認証用の Web ブラウザーが開きます。

対話型ブラウザーの資格情報を使用して、認証された SDK クライアントを作成するには、このコードを追加します。

```csharp
// Your client / app registration ID
private static string clientId = "<your-client-ID>"; 
// Your tenant / directory ID
private static string tenantId = "<your-tenant-ID>";
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "<your-Azure-Digital-Twins-instance-URL>";

//...

DigitalTwinsClient client;
try
{
    var credential = new InteractiveBrowserCredential(tenantId, clientId);
    client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
} catch(Exception e)
{
    Console.WriteLine($"Authentication or client creation error: {e.Message}");
    Environment.Exit(0);
}
```

>[!NOTE]
> 前述のように、クライアント ID、テナント ID およびインスタンス URL をコードに直接配置することはできますが、代わりに、コードでこれらの値を構成ファイルまたは環境変数から取得することをお勧めします。

### <a name="managedidentitycredential-method"></a>ManagedIdentityCredential メソッド
 [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet) メソッドは、Azure Functions を使用する場合など、[マネージド ID (MSI)](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) が必要な場合に適しています。
Azure 関数で、次のようにマネージド ID の資格情報を使用できます。

```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
DigitalTwinsClientOptions opts = 
    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
```

"[*データを処理するための Azure 関数の設定*](how-to-create-azure-function.md)" に関するページを参照し、関数のコンテキストでの重要な構成のいくつかの選択について説明する詳細な例を確認してください。

また、関数で認証を使用する場合は、次の点に注意してください。
* [マネージド ID を有効にする](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)
* 必要に応じて[環境変数](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables?tabs=csharp)を使用する
* 関数アプリにアクセス許可を割り当てて、Digital Twins API にアクセスできるようにする。 Azure Functions のプロセスの詳細については、[*データを処理するための Azure 関数の設定*](how-to-create-azure-function.md)に関するページを参照してください。

## <a name="authentication-with-an-autorest-generated-sdk"></a>AutoRest で生成された SDK での認証

.NET を使用しない場合は、任意の言語で SDK ライブラリを構築することを選択できます。"[*AutoRest を使用して Azure Digital Twins 用のカスタム SDK を作成する方法*](how-to-create-custom-sdks.md)" に関する記事を参照してください。

このセクションでは、その場合に認証する方法について説明します。

### <a name="prerequisites"></a>前提条件

最初に、AutoRest でカスタム SDK を作成する手順を完了する必要があります。使用する手順については、"[*AutoRest を使用して Azure Digital Twins 用のカスタム SDK を作成する方法*](how-to-create-custom-sdks.md)" に関する記事を参照してください。

この例では、AutoRest で生成された Typescript SDK を使用します。 その結果、次のものも必要になります。
* [msal-js](https://github.com/AzureAD/microsoft-authentication-library-for-js)
* [ms-rest-js](https://github.com/Azure/ms-rest-js)

### <a name="minimal-authentication-code-sample"></a>最小限の認証コード サンプル

Azure サービスを使用してアプリの認証を行う場合は、クライアント アプリ内で次の最小限のコードを使用できます。

Azure Digital Twins インスタンスの URL だけでなく、前述の "*アプリケーション (クライアント) ID*" と "*Directory (テナント) ID*" が必要になります。

> [!TIP]
> Azure Digital Twins インスタンスの URL は、Azure Digital Twins インスタンスの *hostName* の先頭に *https://* を追加することによって作成されます。 インスタンスのすべてのプロパティと共に *hostName* を表示する場合は、`az dt show --dt-name <your-Azure-Digital-Twins-instance>` を実行できます。 `az account show --query tenantId` コマンドを使用して、"*ディレクトリ (テナント) ID*" を表示できます。 

```javascript
import * as Msal from "msal";
import { TokenCredentials } from "@azure/ms-rest-js";
// Autorest-generated SDK
import { AzureDigitalTwinsAPI } from './azureDigitalTwinsAPI';

// Client / app registration ID
var ClientId = "<your-client-ID>";
// Azure tenant / directory ID
var TenantId = "<your-tenant-ID>";
// URL of the Azure Digital Twins instance
var AdtInstanceUrl = "<your-instance-URL>"; 

var AdtAppId = "https://digitaltwins.azure.net";

let client = null;

export async function login() {

    const msalConfig = {
        auth: {
            clientId: ClientId,
            redirectUri: "http://localhost:3000",
            authority: "https://login.microsoftonline.com/"+TenantId
        }
    };

    const msalInstance = new Msal.UserAgentApplication(msalConfig);

    msalInstance.handleRedirectCallback((error, response) => {
        // handle redirect response or error
    });

    var loginRequest = {
        scopes: [AdtAppId + "/.default"] 
    };

    try {
        await msalInstance.loginPopup(loginRequest)
        var accessToken;
        // if the user is already logged in you can acquire a token
        if (msalInstance.getAccount()) {
            var tokenRequest = {
                scopes: [AdtAppId + "/.default"]
            };
            try {
                const response = await msalInstance.acquireTokenSilent(tokenRequest);
                accessToken = response.accessToken;
            } catch (err) {
                if (err.name === "InteractionRequiredAuthError") {
                    const response = await msalInstance.acquireTokenPopup(tokenRequest)
                    accessToken = response.accessToken;
                }
            }
        }
        if (accessToken!=null)
        {
            var tokenCredentials = new TokenCredentials(accessToken);
                
            // Add token and server URL to service instance
            const clientConfig = {
                baseUri: AdtInstanceUrl
            };
            client = new AzureDigitalTwinsAPI(tokenCredentials, clientConfig);
            appDataStore.client = client;
        }
    } catch (err) {
        ...
    }
}
```

ここでも、わかりやすくするために、上記のコードではクライアント ID、テナント ID およびインスタンス URL をコードに直接配置していますが、代わりに、コードでこれらの値を構成ファイルまたは環境変数から取得することをお勧めします。

MSAL には、キャッシュなどの認証フローを実装するために、使用できるオプションが他にも多数用意されています。 この詳細については、「[*Microsoft Authentication Library (MSAL) の概要*](../active-directory/develop/msal-overview.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

以下を参照し、Azure Digital Twins でのセキュリティのしくみの詳細を確認します。
* "[*概念: Azure Digital Twins ソリューションのセキュリティ*](concepts-security.md)"

または、認証が設定されたので、以下を参照し、インスタンスでのモデルの作成に進みます。
* [*方法: カスタム モデルを管理する*](how-to-manage-model.md)"