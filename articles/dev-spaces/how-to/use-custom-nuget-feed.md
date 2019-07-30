---
title: Azure Dev Spaces でのカスタム NuGet フィードの使用方法
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: カスタム NuGet フィードを使用して、Azure Dev Space 内の NuGet パッケージにアクセスし、使用します。
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, コンテナー
manager: gwallace
ms.openlocfilehash: 44a87491d276e09e1fa8fed3f5e6803648c3e4a2
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305394"
---
#  <a name="use-a-custom-nuget-feed-in-an-azure-dev-space"></a>Azure Dev Space でのカスタム NuGet フィードの使用

NuGet フィードは、パッケージ ソースをプロジェクトに取り込むための便利な方法を提供します。 Docker コンテナーで依存関係を正しくインストールするためには、Azure Dev Spaces がこのフィードにアクセスする必要があります。

## <a name="set-up-a-nuget-feed"></a>NuGet フィードのセットアップ

`PackageReference` ノード下の `*.csproj` ファイルで、依存関係の[パッケージ参照](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files)を追加します。 例:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

プロジェクト フォルダー内に [NuGet.Config](https://docs.microsoft.com/nuget/reference/nuget-config-file) ファイルを作成し、NuGet フィードに対する `packageSources` および `packageSourceCredentials` セクションを設定します。 `packageSources` セクションにはフィード URL が含まれ、これはパブリック アクセスが可能なものである必要があります。 `packageSourceCredentials` は、フィードにアクセスするための資格情報です。 例:

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

Dockerfile を更新して、`NuGet.Config` ファイルをイメージにコピーします。 例:

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> Windows では、`NuGet.Config`、`Nuget.Config`、および `nuget.config` はすべて有効なファイル名として機能します。 Linux では、`NuGet.Config` のみがこのファイルの有効なファイル名です。 Azure Dev Spaces では Docker と Linux が使用されるため、このファイルには `NuGet.Config` という名前を付ける必要があります。 名前は手動で、または `dotnet restore --configfile nuget.config` を実行して修正できます。


Git を使用している場合は、NuGet フィードの資格情報をバージョン コントロールに入れないでください。 プロジェクトの `.gitignore` に `NuGet.Config` を追加して、`NuGet.Config` ファイルがバージョン コントロールに追加されないようにします。 Azure Dev Spaces では、コンテナー イメージのビルド プロセス中にこのファイルが必要になりますが、既定では、同期中に `.gitignore` および `.dockerignore` で定義された規則に従います。 既定の動作を変更し、`NuGet.Config` ファイルの同期を Azure Dev Spaces に許可するには、次のように `azds.yaml` ファイルを更新します。

```yaml
build:
useGitIgnore: true
ignore:
- “!NuGet.Config”
```

Git を使用していない場合は、この手順を省略できます。

Visual Studio Code または Visual Studio で次に `azds up` を実行または `F5` にヒットしたとき、Azure Dev Spaces は `NuGet.Config` ファイルを同期し、それを使用してパッケージの依存関係をインストールします。

## <a name="next-steps"></a>次の手順

[NuGet とそのしくみ](https://docs.microsoft.com/nuget/what-is-nuget)について学びます。