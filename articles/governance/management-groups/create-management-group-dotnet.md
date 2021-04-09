---
title: 'クイック スタート: .NET Core を使用して管理グループを作成する'
description: このクイックスタートでは、.NET Core を使用して、リソースを階層で整理する管理グループを作成します。
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: a74cea9b142785c093b8ed235fc40049746f11a0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99592570"
---
# <a name="quickstart-create-a-management-group-with-net-core"></a>クイック スタート: .NET Core を使用して管理グループを作成する

管理グループは、複数のサブスクリプションのアクセス、ポリシー、コンプライアンスを管理するのに役立つコンテナーです。 これらのコンテナーを作成して、[Azure Policy](../policy/overview.md) と [Azure ロール ベースのアクセス制御](../../role-based-access-control/overview.md)で使用できる効果的で効率的な階層を構築します。 管理グループについて詳しくは、「[Azure 管理グループでリソースを整理する](overview.md)」をご覧ください。

ディレクトリに作成される最初の管理グループは、完了までに最大 15 分かかる場合があります。 Azure 内でディレクトリの管理グループ サービスを初めて設定する際に実行するプロセスがあります。 プロセスが完了すると、通知を受け取ります。 詳細については、「[管理グループの初期セットアップ](./overview.md#initial-setup-of-management-groups)」を参照してください。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

- _clientId_ と _clientSecret_ を含む Azure サービス プリンシパル。 Azure Policy で使用するサービス プリンシパルがない場合、または新規作成したい場合は、[.NET 認証のための Azure 管理ライブラリ](/dotnet/azure/sdk/authentication#mgmt-auth)に関するページを参照してください。
  次の手順で実行するので、.NET Core パッケージをインストールする手順はスキップします。

- [階層の保護](./how-to/protect-resource-hierarchy.md#setting---require-authorization)が有効になっていない場合、テナント内のすべての Azure AD ユーザーは、そのユーザーに割り当てられた管理グループの書き込みアクセス許可なしで管理グループを作成できます。 この新しい管理グループは、ルート管理グループ ([既定の管理グループ](./how-to/protect-resource-hierarchy.md#setting---default-management-group)) の子になり、作成者には "所有者" ロールの割り当てが付与されます。 管理グループ サービスでは、この機能が許可されるため、ルート レベルでのロールの割り当ては必要ありません。 ルート管理グループは、作成されると、どのユーザーもアクセスできません。 管理グループの使用を開始する場合に Azure AD の全体管理者を見つけるという困難を回避するために、ルート レベルで最初の管理グループを作成できるようになっています。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="application-setup"></a>アプリケーションのセットアップ

.NET Core で管理グループを管理できるようにするには、新しいコンソール アプリケーションを作成し、必要なパッケージをインストールします。

1. 最新の .NET Core がインストールされていることを確認します (**3.1.8** 以降)。 まだインストールされていない場合は、[dotnet.microsoft.com](https://dotnet.microsoft.com/download/dotnet-core) からダウンロードします。

1. "mgCreate" という名前の新しい .NET Core コンソール アプリケーションを初期化します。

   ```dotnetcli
   dotnet new console --name "mgCreate"
   ```

1. 新しいプロジェクト フォルダーにディレクトリを変更し、次のように Azure Policy に必要なパッケージをインストールします。

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.ManagementGroups --version 1.1.1-preview

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
   using Microsoft.Azure.Management.ManagementGroups;
   using Microsoft.Azure.Management.ManagementGroups.Models;
   
   namespace mgCreate
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strGroupId = args[3];
               string strDisplayName = args[4];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                      "https://management.core.windows.net",
                      new ClientCredential(strClientId, strClientSecret));
   
               using (var client = new ManagementGroupsAPIClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var mgRequest = new CreateManagementGroupRequest
                   {
                       DisplayName = strDisplayName
                   };
                   var response = await client.ManagementGroups.CreateOrUpdateAsync(strGroupId, mgRequest);
               }
           }
       }
   }
   ```

1. `mgCreate` コンソール アプリケーションをビルドして発行します。

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="create-the-management-group"></a>管理グループの作成

このクイックスタートでは、ルート管理グループに新しい管理グループを作成します。

1. ディレクトリを、前の `dotnet publish` コマンドで定義した `{run-folder}` に変更します。

1. ターミナルに次のコマンドを入力します。

   ```bash
   mgCreate.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{groupID}" `
      "{displayName}"
   ```

上記のコマンドでは次の情報が使用されています。

- `{tenantId}` - 実際のテナント ID に置き換えます
- `{clientId}` - ご自分のサービス プリンシパルのクライアント ID に置き換えます
- `{clientSecret}` - ご自分のサービス プリンシパルのクライアント シークレットに置き換えます
- `{groupID}` - 新しい管理グループの ID に置き換えます。
- `{displayName}` - 新しい管理グループのフレンドリ名に置き換えます。

その結果、ルート管理グループ内に新しい管理グループが作成されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

- ポータルを使用して新しい管理グループを削除します。

- .NET Core コンソール アプリケーションとインストールされているパッケージを削除する場合は、`mgCreate` プロジェクト フォルダーを削除します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、リソース階層を整理するための管理グループを作成しました。 管理グループには、サブスクリプションや他の管理グループを含めることができます。

管理グループについて、またリソース階層の管理方法について詳しくは、次の記事に進んでください。

> [!div class="nextstepaction"]
> [管理グループを使用してリソースを管理する](./manage.md)