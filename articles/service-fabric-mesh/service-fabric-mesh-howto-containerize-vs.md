---
title: 既存の .NET アプリを Service Fabric Mesh 用にコンテナー化する |Microsoft Docs
description: 既存の .NET アプリに Mesh のサポートを追加します
services: service-fabric-mesh
keywords: コンテナー化 service fabric mesh
author: dkkapur
ms.author: dekapur
ms.date: 11/08/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: chakdan
ms.openlocfilehash: cb4e327e1c8c0a653cb94233f568b4847494c439
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57779880"
---
# <a name="containerize-an-existing-net-app-for-service-fabric-mesh"></a>既存の .NET アプリを Service Fabric Mesh 用にコンテナー化する

この記事では、既存の .NET アプリに Service Fabric Mesh のコンテナー オーケストレーションサポートを追加する方法について説明します。

Visual Studio 2017 では、完全な .NET Framework を使用する ASP.NET および Console プロジェクトに、コンテナー化のサポートを追加できます。

> [!NOTE]
> .NET **Core** プロジェクトは、現時点でサポートされません。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)できます。

* [開発環境がセットアップ](service-fabric-mesh-howto-setup-developer-environment-sdk.md)されていることを確認してください。 これには、Service Fabric ランタイム、SDK、Docker、Visual Studio 2017 のインストールと、ローカル クラスターの作成が含まれます。

## <a name="open-an-existing-net-app"></a>既存の .NET アプリを開きます

コンテナー オーケストレーションのサポートを追加するアプリを開きます。

サンプルを使用する場合は、[eShop](https://github.com/MikkelHegn/ContainersSFLab) のコード サンプルを使用できます。 この記事では、このプロジェクトを使用する前提で説明をしていきますが、他のプロジェクトにも同じ手順を使用できます。

**eShop** プロジェクトのコピーを入手します。

```git
git clone https://github.com/MikkelHegn/ContainersSFLab.git
```

ダウンロードが完了したら、Visual Studio 2017 で **ContainersSFLab\eShopLegacyWebFormsSolution\eShopLegacyWebForms.sln** を開きます。

## <a name="add-container-support"></a>コンテナー サポートを追加する
 
Service Fabric Mesh ツールを使用して、既存の ASP.NET または Console プロジェクトにコンテナー オーケストレーション サポートを追加します。手順は次のとおりです。

Visual Studio のソリューション エクスプ ローラーで、プロジェクト名 (この例では **eShopLegacyWebForms**) を右クリックし、**[追加]** > **[コンテナー オーケストレーター サポート]** を選択します。
**[コンテナー オーケストレーター サポートの追加]** ダイアログが表示されます。

![Visual Studio の [コンテナー オーケストレーター サポートの追加] ダイアログ](./media/service-fabric-mesh-howto-containerize-vs/add-container-orchestration-support.png)

ドロップダウン リストから **[Service Fabric Mesh]** を選択し、**[OK]** をクリックします。

ツールによって Docker がインストールされ、プロジェクトに Dockerfile が追加されて、プロジェクト用の docker イメージがプルダウンされます。  
Service Fabric Mesh のアプリケーション プロジェクトがソリューションに追加されます。 これには、Mesh の発行プロファイルと構成ファイルが含まれています。 プロジェクトの名前は、ご使用のプロジェクト名の末尾に "Application" を連結したものになります (例: **eShopLegacyWebFormsApplication**)。 

新しい Mesh プロジェクトに、次の 2 つのフォルダーが表示されていることを確認してください。
- **App Resources**: 追加の Mesh リソース (ネットワークなど) を記述した YAML ファイルが含まれています。
- **Service Resources**: デプロイ時のアプリの実行方法について記述した service.yaml ファイルが含まれています。

コンテナー オーケストレーション サポートがアプリに追加された後、**F5** キーを押すと、ローカルの Service Fabric Mesh クラスター上で .NET アプリをデバッグできます。 次に示すのは、Service Fabric Mesh クラスターで実行されている eShop ASP.NET アプリです。 

![eShop アプリ](./media/service-fabric-mesh-howto-containerize-vs/eshop-running.png)

これで、Azure Service Fabric Mesh にアプリを発行できるようになりました。

## <a name="next-steps"></a>次の手順

Service Fabric Mesh にアプリを発行する方法: [チュートリアル: Service Fabric Mesh アプリケーションをデプロイする](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)