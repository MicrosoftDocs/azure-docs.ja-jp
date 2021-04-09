---
title: Azure Functions を設定する
description: このチュートリアルでは、Azure 関数アプリを作成して、Azure カスタム プロバイダーと連携するように設定する方法を説明します
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 55554678047faeedd16b78dea61a42d50fd59491
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98737322"
---
# <a name="set-up-azure-functions-for-azure-custom-providers"></a>Azure カスタム プロバイダー用の Azure Functions を設定する

カスタム プロバイダーは、Azure とエンドポイントの間のコントラクトです。 カスタム プロバイダーを使うと、Azure 内のワークフローに変更を加えることができます。 このチュートリアルでは、カスタム プロバイダーのエンドポイントとして機能するように Azure 関数アプリを設定する方法について説明します。

## <a name="create-the-azure-function-app"></a>Azure 関数アプリを作成する

> [!NOTE]
> このチュートリアルでは、Azure 関数アプリを使用するシンプルなサービス エンドポイントを作成します。 ただしカスタム プロバイダーは、パブリックにアクセスできる任意のエンドポイントを使用できます。 その代替候補としては、Azure Logic Apps や Azure API Management、Azure App Service の Web Apps 機能などが挙げられます。

このチュートリアルを始めるには、まず、[Azure portal で初めての Azure 関数アプリを作成する](../../azure-functions/functions-get-started.md)チュートリアルを先に行う必要があります。 そのチュートリアルでは、Azure portal で変更可能な .NET Core の Webhook 関数を作成します。 このチュートリアルの土台にもなっているチュートリアルです。

## <a name="install-azure-table-storage-bindings"></a>Azure Table Storage のバインディングをインストールする

Azure Table Storage のバインディングをインストールするには、次の手順に従います。

1. HttpTrigger の **[統合]** タブに移動します。
1. **[+ 新しい入力]** を選択します。
1. **[Azure Table Storage]** を選択します。
1. Microsoft.Azure.WebJobs.Extensions.Storage 拡張機能をまだインストールしていない場合はインストールします。
1. **[テーブル パラメーター名]** ボックスに「**tableStorage**」と入力します。
1. **[テーブル名]** ボックスに「**myCustomResources**」と入力します。
1. **[保存]** を選択して、更新した入力パラメーターを保存します。

![テーブル バインディングを示すカスタム プロバイダーの概要](./media/create-custom-provider/azure-functions-table-bindings.png)

## <a name="update-restful-http-methods"></a>RESTful HTTP メソッドを更新する

カスタム プロバイダーの RESTful 要求メソッドを含むように Azure 関数を設定するには、次の手順に従います。

1. HttpTrigger の **[統合]** タブに移動します。
1. **[選択した HTTP メソッド]** で、**[GET]**、**[POST]**、**[DELETE]**、**[PUT]** を選択します。

![HTTP メソッドを示すカスタム プロバイダーの概要](./media/create-custom-provider/azure-functions-http-methods.png)

## <a name="add-azure-resource-manager-nuget-packages"></a>Azure Resource Manager NuGet パッケージを追加する

> [!NOTE]
> C# プロジェクト ファイルがプロジェクト ディレクトリに欠落している場合は、手動で追加できます。 または、Microsoft.Azure.WebJobs.Extensions.Storage 拡張機能が関数アプリにインストールされると表示されるようになります。

次に、有用な NuGet ライブラリをインクルードするように C# プロジェクト ファイルを更新します。 これらのライブラリによって、カスタム プロバイダーからの受信要求が解析しやすくなります。 [ポータルからの拡張機能の追加](../../azure-functions/functions-bindings-register.md)に関する記事の手順に従い、次のパッケージ参照をインクルードするように C# プロジェクト ファイルを更新します。

```xml
<PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
<PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
<PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
```

次の XML 要素は C# プロジェクト ファイルの例です。

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>netstandard2.0</TargetFramework>
    <WarningsAsErrors />
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
    <PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
    <PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
  </ItemGroup>
</Project>
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure カスタム プロバイダーのエンドポイントとして機能するように Azure 関数アプリを設定しました。

RESTful カスタム プロバイダー エンドポイントを作成する方法については、[RESTful カスタム プロバイダー エンドポイントの作成のチュートリアル](./tutorial-custom-providers-function-authoring.md)に関する記事をご覧ください。