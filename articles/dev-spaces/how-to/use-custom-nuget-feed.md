---
title: Azure Dev Spaces でのカスタム NuGet フィードの使用方法
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: johnsta
ms.author: johnsta
ms.date: 05/11/2018
ms.topic: conceptual
description: カスタム NuGet フィードを使用して、Azure Dev Space 内の NuGet パッケージにアクセスし、使用します。
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, コンテナー
manager: ghogen
ms.openlocfilehash: 1a000e378a9b8ecfb09d778fd6444e3f24b3df7b
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57772468"
---
#  <a name="use-a-custom-nuget-feed-in-an-azure-dev-space"></a>Azure Dev Space でのカスタム NuGet フィードの使用

NuGet フィードは、パッケージ ソースをプロジェクトに取り込むための便利な方法を提供します。 Docker コンテナーで依存関係を正しくインストールするためには、Azure Dev Spaces がこのフィードにアクセスできる必要があります。

## <a name="set-up-a-nuget-feed"></a>NuGet フィードのセットアップ

NuGet フィードをセットアップするには:
1. `PackageReference` ノード下の `*.csproj` ファイルで[パッケージ参照](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files)を追加します。

   ```xml
   <ItemGroup>
       <!-- ... -->
       <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
       <!-- ... -->
   </ItemGroup>
   ```

2. プロジェクト フォルダー内に [NuGet.Config](https://docs.microsoft.com/nuget/reference/nuget-config-file) ファイルを作成します。
     * `packageSources` セクションを使用して NuGet フィードの場所を参照します。 重要:NuGet フィードはパブリックからアクセスできる必要があります。
     * `packageSourceCredentials` セクションを使用して、ユーザー名とパスワードの資格情報を構成します。 

   ```xml
   <packageSources>
       <add key="Contoso" value="https://contoso.com/packages/" />
   </packageSources>

   <packageSourceCredentials>
       <Contoso>
           <add key="Username" value="user@contoso.com" />
           <add key="ClearTextPassword" value="33f!!lloppa" />
       </Contoso>
   </packageSourceCredentials>
   ```

3. ソース コード管理を使用している場合:
    - 資格情報をソース リポジトリに誤ってコミットしないように、`.gitignore` ファイルで `NuGet.Config` を参照します。
    - `azds.yaml` ファイルをプロジェクトで開き、`build` セクションを探して次のスニペットを挿入することで、`NuGet.Config` ファイルが確実に Azure に同期され、コンテナー イメージのビルド プロセス中にこのファイルが使用されるようにします。 (既定では、Azure Dev Spaces は `.gitignore` および `.dockerignore` 規則に一致するファイルを同期しません。)

        ```yaml
        build:
        useGitIgnore: true
        ignore:
        - “!NuGet.Config”
        ```


## <a name="next-steps"></a>次の手順

上記の手順が完了したら、次に `azds up` を実行したとき (あるいは、VSCode または Visual Studio で `F5` にヒットしたとき) に、Azure Dev Spaces が `NuGet.Config` ファイルを Azure に同期します。このファイルはその後、パッケージの依存関係をコンテナーにインストールするために `dotnet restore` によって利用されます。

