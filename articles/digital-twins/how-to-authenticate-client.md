---
title: アプリ認証コードを作成する
titleSuffix: Azure Digital Twins
description: クライアント アプリケーションに認証コードを書き込む方法を説明します
author: baanders
ms.author: baanders
ms.date: 10/7/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 1b7a846ee92da001ea2ac3ddd02efa9a870f72c6
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501908"
---
# <a name="write-client-app-authentication-code"></a>クライアント アプリの認証コードを書き込む

[Azure Digital Twins のインスタンスと認証を設定](how-to-set-up-instance-portal.md)した後、インスタンスとのやり取りに使用するクライアント アプリケーションを作成できます。 スターター クライアント プロジェクトを設定した後、Azure Digital Twins のインスタンスに対して **認証を行うためのコードをそのクライアント アプリに書き込む** 必要があります。

Azure Digital Twins では [OAUTH 2.0 に基づく Azure AD セキュリティ トークン](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims)を使用して認証が実行されます。 ご使用の SDK を認証するには、Azure Digital Twins に対する適切なアクセス許可を持つベアラー トークンを取得し、API 呼び出しと共にこれを渡す必要があります。 

この記事では、`Azure.Identity` クライアント ライブラリを使用して資格情報を取得する方法について説明します。 この記事では C# のコード サンプルを示していますが ([.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client) 用に記述する内容など)、使用する SDK に関係なく `Azure.Identity` のバージョンを使用できます (Azure Digital Twins で使用できる SDK の詳細については、[*Azure Digital Twins API と SDK の使用方法*](how-to-use-apis-sdks.md)に関する記事をご覧ください)。

## <a name="prerequisites"></a>前提条件

最初に、"[*インスタンスと認証の設定方法*](how-to-set-up-instance-portal.md)" に関する記事のセットアップ手順を完了します。 これにより、Azure Digital Twins インスタンスが作成され、ユーザーにアクセス許可があることが確認されます。 このセットアップが完了したら、クライアント アプリのコードを記述することができます。

先に進むには、コードを記述するクライアント アプリ プロジェクトが必要です。 クライアント アプリ プロジェクトをまだ設定していない場合は、このチュートリアルで使用する基本的なプロジェクトを、選択した言語で作成します。

## <a name="common-authentication-methods-with-azureidentity"></a>Azure.Identity を使用した一般的な認証方法

`Azure.Identity` は、ベアラー トークンを取得して SDK で認証するために使用できる、資格情報を取得するためのメソッドをいくつか提供するクライアント ライブラリです。 この記事では C# の例を示していますが、次のようないくつかの言語で `Azure.Identity` を表示できます。

* [.NET (C#)](/dotnet/api/azure.identity)
* [Java](/java/api/overview/azure/identity-readme)
* [JavaScript](/javascript/api/overview/azure/identity-readme)
* [Python](/python/api/overview/azure/identity-readme)

`Azure.Identity` で資格情報を取得するための一般的な 3 つのメソッドは次のとおりです。

* [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) では、Azure にデプロイされるアプリケーションに対して既定の `TokenCredential` 認証フローが提供されます。これは、**ローカル開発に推奨** されています。 また、これを有効にして、この記事で推奨されている他の 2 つのメソッドを試すこともできます。つまり、これで `ManagedIdentityCredential` をラップすることや、構成変数で `InteractiveBrowserCredential` にアクセスすることができます。
* [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential) は、[マネージド ID (MSI)](../active-directory/managed-identities-azure-resources/overview.md) を必要とする場合に適しており、Azure Functions を操作する場合や Azure サービスにデプロイする場合の有力な選択肢です。
* [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential) は、対話型アプリケーションを対象とし、認証された SDK クライアントを作成するために使用できます

次の例は、.NET (C#) SDK でこれらの各メソッドを使用する方法を示しています。

## <a name="authentication-examples-net-c-sdk"></a>認証の例: .NET (C#) SDK

このセクションでは、提供されている .NET SDK を使用して認証コードを記述する C# の例を示します。

まず、プロジェクトに SDK パッケージ `Azure.DigitalTwins.Core` と `Azure.Identity` パッケージを含めます。 選択するツールによっては、Visual Studio パッケージ マネージャーまたは `dotnet` コマンド ライン ツールを使用して、パッケージを含めることができます。 

また、次の using ステートメントをプロジェクト コードに追加する必要があります。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="Azure_Digital_Twins_dependencies":::

その後、次のいずれかのメソッドを `Azure.Identity` で使用して、資格情報を取得するためのコードを追加します。

### <a name="defaultazurecredential-method"></a>DefaultAzureCredential メソッド

[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) では、Azure にデプロイされるアプリケーションに対して既定の `TokenCredential` 認証フローが提供されます。これは、**ローカル開発に推奨** されています。

既定の Azure 資格情報を使用するには、Azure Digital Twins インスタンスの URL が必要です ([確認の手順](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))。

`DefaultAzureCredential` をプロジェクトに追加するためのコード サンプルを次に示します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="DefaultAzureCredential_full":::

#### <a name="set-up-local-azure-credentials"></a>ローカルの Azure 資格情報を設定する

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](../../includes/digital-twins-local-credentials-inner.md)]

### <a name="managedidentitycredential-method"></a>ManagedIdentityCredential メソッド

[ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential) メソッドは、Azure Functions を使用する場合など、[マネージド ID (MSI)](../active-directory/managed-identities-azure-resources/overview.md) が必要な場合に適しています。

つまり、同じプロジェクトで `ManagedIdentityCredential` を `DefaultAzureCredential` または `InteractiveBrowserCredential` として使用し、プロジェクトの別の部分を認証することができます。

既定の Azure 資格情報を使用するには、Azure Digital Twins インスタンスの URL が必要です ([確認の手順](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))。

Azure 関数で、次のようにマネージド ID の資格情報を使用できます。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="ManagedIdentityCredential":::

### <a name="interactivebrowsercredential-method"></a>InteractiveBrowserCredential メソッド

[InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential) メソッドは、対話型アプリケーションを対象としており、認証用の Web ブラウザーが開きます。 対話型認証が必要な場合には、これを `DefaultAzureCredential` の代わりに使用できます。

対話型のブラウザー資格情報を使用するには、Azure Digital Twins API へのアクセス許可がある **アプリの登録** が必要になります。 このアプリの登録を設定する手順については、[*方法:アプリ登録の作成*](how-to-create-app-registration.md)に関するページを参照してください。 アプリの登録が設定されたら、次のものが必要になります。
* アプリの登録の "*アプリケーション (クライアント) ID*" ([確認の手順](how-to-create-app-registration.md#collect-client-id-and-tenant-id))
* アプリの登録の "*ディレクトリ (テナント) ID*" ([確認の手順](how-to-create-app-registration.md#collect-client-id-and-tenant-id))
* Azure Digital Twins インスタンスの URL ([確認の手順](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))

`InteractiveBrowserCredential` を使用して認証された SDK クライアントを作成するコードの例を次に示します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="InteractiveBrowserCredential":::

>[!NOTE]
> 前述のように、クライアント ID、テナント ID およびインスタンス URL をコードに直接配置することはできますが、代わりに、コードでこれらの値を構成ファイルまたは環境変数から取得することをお勧めします。

#### <a name="other-notes-about-authenticating-azure-functions"></a>Azure Functions の認証に関するその他の注意事項

"[*データを処理するための Azure 関数の設定*](how-to-create-azure-function.md)" に関するページを参照し、関数のコンテキストでの重要な構成のいくつかの選択について説明する詳細な例を確認してください。

また、関数で認証を使用する場合は、次の点に注意してください。
* [マネージド ID を有効にする](../app-service/overview-managed-identity.md?tabs=dotnet)
* 必要に応じて[環境変数](/sandbox/functions-recipes/environment-variables?tabs=csharp)を使用する
* 関数アプリにアクセス許可を割り当てて、Digital Twins API にアクセスできるようにする。 Azure Functions のプロセスの詳細については、[*データを処理するための Azure 関数の設定*](how-to-create-azure-function.md)に関するページを参照してください。

## <a name="other-credential-methods"></a>その他の資格情報のメソッド

上で取り上げた認証シナリオがご自身のアプリのニーズを満たしていない場合、[**Microsoft ID プラットフォーム**](../active-directory/develop/v2-overview.md#getting-started)で提供されている他の種類の認証を調べることができます。 このプラットフォームのドキュメントでは、アプリケーションの種類別に整理された追加の認証シナリオが取り上げられています。

## <a name="next-steps"></a>次のステップ

以下を参照し、Azure Digital Twins でのセキュリティのしくみの詳細を確認します。
* "[*概念: Azure Digital Twins ソリューションのセキュリティ*](concepts-security.md)"

または、認証が設定されたので、インスタンスでのモデルの作成および管理に進みます。
* [*方法: DTDL モデルの管理*](how-to-manage-model.md)に関する記事