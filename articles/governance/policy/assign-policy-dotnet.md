---
title: 'クイック スタート: .NET Core を使用した新しいポリシー割り当て'
description: このクイックスタートでは、.NET Core を使用して、準拠していないリソースを特定するための Azure Policy 割り当てを作成します。
ms.date: 09/30/2020
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: c4c8f8e9df544b6fc00b5b7701435f5a606f9764
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "91604578"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-net-core"></a>クイック スタート: .NET Core を使用して準拠していないリソースを特定するためのポリシー割り当てを作成する

Azure のコンプライアンスを理解する第一歩は、リソースの状態を特定することです。 このクイック スタートでは、マネージド ディスクを使用していない仮想マシンを識別するためのポリシー割り当てを作成します。 完了すると、"_準拠していない_" 仮想マシンが特定されます。

.NET Core ライブラリを使用して Azure リソースを管理します。 このガイドでは、Azure Policy 用の .NET Core ライブラリを使用してポリシー割り当てを作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。
- _clientId_ と _clientSecret_ を含む Azure サービス プリンシパル。 Azure Policy で使用するサービス プリンシパルがない場合、または新規作成したい場合は、[.NET 認証のための Azure 管理ライブラリ](/dotnet/azure/sdk/authentication#mgmt-auth)に関するページを参照してください。
  次の手順で実行するので、.NET Core パッケージをインストールする手順はスキップします。

## <a name="create-the-azure-policy-project"></a>Azure Policy プロジェクトを作成する

.NET Core で Azure Policy を管理できるようにするには、新しいコンソール アプリケーションを作成し、必要なパッケージをインストールします。

1. 最新の .NET Core がインストールされていることを確認します (**3.1.8** 以降)。 まだインストールされていない場合は、[dotnet.microsoft.com](https://dotnet.microsoft.com/download/dotnet-core) からダウンロードします。

1. "policyAssignment" という名前の新しい .NET Core コンソール アプリケーションを初期化します。

   ```dotnetcli
   dotnet new console --name "policyAssignment"
   ```

1. 新しいプロジェクト フォルダーにディレクトリを変更し、次のように Azure Policy に必要なパッケージをインストールします。

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.ResourceManager --version 3.10.0-preview

   # Add the Azure app auth package for .NET Core
   dotnet add package Microsoft.Azure.Services.AppAuthentication --version 1.5.0
   ```

1. 既定の `program.cs` を次のコードに置き換え、更新したファイルを保存します。

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   using Microsoft.IdentityModel.Clients.ActiveDirectory;
   using Microsoft.Rest;
   using Microsoft.Azure.Management.ResourceManager;
   using Microsoft.Azure.Management.ResourceManager.Models;
   
   namespace policyAssignment
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strName = args[4];
               string strDisplayName = args[5];
               string strPolicyDefID = args[6];
               string strDescription = args[7];
               string strScope = args[8];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                   "https://management.core.windows.net",
                   new ClientCredential(strClientId, strClientSecret));

               using (var client = new PolicyClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var policyAssignment = new PolicyAssignment
                   {
                       DisplayName = strDisplayName,
                       PolicyDefinitionId = strPolicyDefID,
                       Description = strDescription
                   };
                   var response = await client.PolicyAssignments.CreateAsync(strScope, strName, policyAssignment);
               }
           }
       }
   }
   ```

1. `policyAssignment` コンソール アプリケーションをビルドして発行します。

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="create-a-policy-assignment"></a>ポリシー割り当てを作成する

このクイックスタートでは、ポリシー割り当てを作成し、**マネージド ディスクを使用しない監査 VM** (`06a78e20-9358-41c9-923c-fb736d382a4d`) 定義を割り当てます。 このポリシー定義では、ポリシー定義で設定されている条件に準拠していないリソースが識別されます。

1. ディレクトリを、前の `dotnet publish` コマンドで定義した `{run-folder}` に変更します。

1. ターミナルに次のコマンドを入力します。

   ```bash
   policyAssignment.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{subscriptionId}" `
      "audit-vm-manageddisks" `
      "Audit VMs without managed disks Assignment" `
      "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d" `
      "Shows all virtual machines not using managed disks" `
      "{scope}"
   ```

上記のコマンドでは次の情報が使用されています。

- `{tenantId}` - 実際のテナント ID に置き換えます
- `{clientId}` - ご自分のサービス プリンシパルのクライアント ID に置き換えます
- `{clientSecret}` - ご自分のサービス プリンシパルのクライアント シークレットに置き換えます
- `{subscriptionId}` - サブスクリプション ID で置き換えます
- **name** - ポリシー割り当てオブジェクトの一意の名前。 上記の例では、_audit-vm-manageddisks_ を使用しています。
- **displayName** - ポリシーの割り当てに使用する表示名。 このケースでは、"_Audit VMs without managed disks Assignment_" を使用します。
- **policyDefID** - 割り当てを作成する際に基礎として使用するポリシー定義パス。 ここでは、"_Managed Disks を使用していない VM の監査_" というポリシー定義の ID です。
- **description** - ポリシーの機能またはこのスコープに割り当てる理由の詳細な説明。
- **scope** - スコープによって、ポリシー割り当てを適用するリソースまたはリソース グループが決まります。 スコープには、管理グループから個々のリソースまで指定できます。 `{scope}` は、次のパターンのいずれかに必ず置き換えてください。
  - 管理グループ: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - サブスクリプション: `/subscriptions/{subscriptionId}`
  - リソース グループ: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - リソース: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

以上の手順で、準拠していないリソースを特定し、環境のコンプライアンスの状態を理解できるようになりました。

## <a name="identify-non-compliant-resources"></a>準拠していないリソースを特定する

ポリシー割り当てが作成されたので、準拠していないリソースを特定できます。

1. "policyCompliance" という名前の新しい .NET Core コンソール アプリケーションを初期化します。

   ```dotnetcli
   dotnet new console --name "policyCompliance"
   ```

1. 新しいプロジェクト フォルダーにディレクトリを変更し、次のように Azure Policy に必要なパッケージをインストールします。

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.PolicyInsights --version 3.1.0

   # Add the Azure app auth package for .NET Core
   dotnet add package Microsoft.Azure.Services.AppAuthentication --version 1.5.0
   ```

1. 既定の `program.cs` を次のコードに置き換え、更新したファイルを保存します。

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   using Microsoft.IdentityModel.Clients.ActiveDirectory;
   using Microsoft.Rest;
   using Microsoft.Azure.Management.PolicyInsights;
   using Microsoft.Azure.Management.PolicyInsights.Models;
   
   namespace policyAssignment
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strName = args[4];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                   "https://management.core.windows.net",
                   new ClientCredential(strClientId, strClientSecret));
   
               using (var client = new PolicyInsightsClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var policyQueryOptions = new QueryOptions
                   {
                       Filter = $"IsCompliant eq false and PolicyAssignmentId eq '{strName}'",
                       Apply = "groupby(ResourceId)"
                   };
   
                   var response = await client.PolicyStates.ListQueryResultsForSubscriptionAsync(
                       "latest", strSubscriptionId, policyQueryOptions);
                   Console.WriteLine(response.Odatacount);
               }
           }
       }
   }
   ```

1. `policyAssignment` コンソール アプリケーションをビルドして発行します。

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

1. ディレクトリを、前の `dotnet publish` コマンドで定義した `{run-folder}` に変更します。

1. ターミナルに次のコマンドを入力します。

   ```bash
   policyCompliance.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{subscriptionId}" `
      "audit-vm-manageddisks"
   ```

上記のコマンドでは次の情報が使用されています。

- `{tenantId}` - 実際のテナント ID に置き換えます
- `{clientId}` - ご自分のサービス プリンシパルのクライアント ID に置き換えます
- `{clientSecret}` - ご自分のサービス プリンシパルのクライアント シークレットに置き換えます
- `{subscriptionId}` - サブスクリプション ID で置き換えます
- **name** - ポリシー割り当てオブジェクトの一意の名前。 上記の例では、_audit-vm-manageddisks_ を使用しています。

`response` の結果は、Azure portal ビュー内のポリシー割り当ての **[リソース コンプライアンス]** タブに表示される内容と同じです。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

- ポータルを使用して、ポリシー割り当て _Audit VMs without managed disks Assignment_ を削除します。 ポリシー定義は組み込まれているので、削除できる定義はありません。

- .NET Core コンソール アプリケーションとインストールされているパッケージを削除する場合は、`policyAssignment` および `policyCompliance` プロジェクト フォルダーを削除します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、ポリシー定義を割り当てて、Azure 環境内で準拠していないリソースを特定しました。

新しいリソースが準拠していることを検証するためのポリシー定義の割り当てについて詳しく学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [ポリシーの作成と管理](./tutorials/create-and-manage.md)
