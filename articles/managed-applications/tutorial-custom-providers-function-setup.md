---
title: Azure カスタム プロバイダー用に Azure Functions を設定する
description: このチュートリアルでは、Azure 関数を作成し、Azure カスタム プロバイダーで動作するように設定する方法を説明します
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: d7e4de43659db88bfd9aad40cc3b9f1753189bba
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799121"
---
# <a name="setup-azure-functions-for-azure-custom-providers"></a>Azure カスタム プロバイダー用に Azure Functions を設定する

カスタム プロバイダーを使うと、Azure 上でワークフローをカスタマイズできます。 カスタム プロバイダーは、Azure と`endpoint`の間のコントラクトです。 このチュートリアルでは、カスタム プロバイダーの`endpoint`として動作するように Azure 関数を設定するプロセスについて説明します。

このチュートリアルは、次のステップに分かれています。

- Azure 関数を作成する
- Azure Table バインドをインストールする
- RESTful HTTP メソッドを更新する
- Azure Resource Manager NuGet パッケージを追加する

このチュートリアルは、次のチュートリアルが基になっています。

- [Azure portal で初めての Azure 関数を作成する](../azure-functions/functions-create-first-azure-function.md)

## <a name="creating-the-azure-function"></a>Azure 関数を作成する

> [!NOTE]
> このチュートリアルでは Azure 関数を使って簡単なサービス エンドポイントを作成しますが、カスタム プロバイダーでは任意のパブリックにアクセス可能な`endpoint`を使うことができます。 Azure Logic Apps、Azure API Management、Azure Web Apps などは、優れた代替手段です。

このチュートリアルを始めるには、[、Azure portal で初めての Azure 関数を作成する](../azure-functions/functions-create-first-azure-function.md)チュートリアルを先に行う必要があります。 そのチュートリアルでは、Azure portal で変更可能な .NET Core の Webhook 関数を作成します。

## <a name="install-azure-table-bindings"></a>Azure Table バインドをインストールする

このセクションでは、Azure Table Storage バインドをインストールする手順を説明します。

1. HttpTrigger の [`Integrate`] タブに移動します。
2. [`+ New Input`] をクリックします。
3. [`Azure Table Storage`] を選択します。
4. まだインストールされていない場合は、`Microsoft.Azure.WebJobs.Extensions.Storage` をインストールします。
5. [`Table parameter name`] を「tableStorage」に、[`Table name`] を「myCustomResources」に更新します。
6. 更新した入力パラメーターを保存します。

![カスタム プロバイダーの概要](./media/create-custom-providers/azure-functions-table-bindings.png)

## <a name="update-restful-http-methods"></a>RESTful HTTP メソッドを更新する

このセクションでは、カスタム プロバイダーの RESTful 要求メソッドを含むように Azure 関数を設定する手順について説明します。

1. HttpTrigger の [`Integrate`] タブに移動します。
2. [`Selected HTTP methods`] を次のように更新します: GET、POST、DELETE、PUT。

![カスタム プロバイダーの概要](./media/create-custom-providers/azure-functions-http-methods.png)

## <a name="modifying-the-csproj"></a>csproj を変更する

> [!NOTE]
> csproj がディレクトリにない場合は、手動で追加できます。または、`Microsoft.Azure.WebJobs.Extensions.Storage` 拡張機能が関数にインストールされると、表示されるようになります。

次に、カスタム プロバイダーからの着信要求を解析しやすくする便利な NuGet ライブラリを含むように、csproj ファイルを更新します。 [ポータルからの拡張機能の追加](../azure-functions/install-update-binding-extensions-manual.md)に関する記事の手順に従い、次のパッケージ参照を含むように csproj を更新します。

```xml
<PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
<PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
<PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
```

csproj ファイルのサンプル:

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

## <a name="next-steps"></a>次の手順

この記事では、Azure カスタム プロバイダーの`endpoint`として機能するように Azure 関数を設定しました。 次の記事に進み、RESTful カスタム プロバイダーの`endpoint`を作成する方法について学習してください。

- [チュートリアル:RESTful カスタム プロバイダーのエンドポイントを作成する](./tutorial-custom-providers-function-authoring.md)
