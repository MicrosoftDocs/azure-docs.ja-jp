---
title: チュートリアル:セッションやデバイス間でアンカーを共有する
description: このチュートリアルでは、バックエンド サービスを使用して、Unity で Azure Spatial Anchors 識別子を Android/iOS デバイス間で共有する方法について説明します。
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 07/31/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 12a15a2d0cd45207b4adefc7315e48c6d642c0f7
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2020
ms.locfileid: "87810177"
---
# <a name="tutorial-share-azure-spatial-anchors-across-sessions-and-devices"></a>チュートリアル:セッションやデバイス間での Azure Spatial Anchors の共有

このチュートリアルでは、[Azure Spatial Anchors](../overview.md) を使用してあるセッション中にアンカーを作成した後、同じデバイスまたは異なるデバイス上でそれらを探知する方法について学習します。 これらの同じアンカーは、同じ場所にある複数のデバイスが同時に探知することもできます。

![永続化](./media/persistence.gif)

Azure Spatial Anchors は、クロスプラットフォーム対応の開発者向けサービスです。このサービスを使用すると、時間が経過した後でも複数のデバイス間で位置情報を保持するオブジェクトを使用して複合現実エクスペリエンスを作成できます。 完了すると、2 つ以上のデバイスにデプロイ可能なアプリが完成します。 1 つのインスタンスによって作成された Azure 空間アンカーは、他のインスタンスに共有できます。

学習内容は次のとおりです。

> [!div class="checklist"]
> * アンカーを一定期間メモリに格納して共有するための ASP.NET Core Web アプリを Azure にデプロイする。
> * クイック スタートで提供されている Unity サンプルの AzureSpatialAnchorsLocalSharedDemo シーンを構成して、アンカー共有 Web アプリを利用する。
> * 1 つ以上のデバイスにデプロイして実行する。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

このチュートリアルでは Unity と ASP.NET Core Web App を使用しますが、これは Azure Spatial Anchors 識別子を複数デバイス間で共有する方法の一例に過ぎないことに注意してください。 同じ目的を実現するために、他の言語やバックエンド テクノロジを使用することもできます。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-the-sample-project"></a>サンプル プロジェクトのダウンロード

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-your-sharing-anchors-service"></a>アンカー共有サービスのデプロイ

## <a name="visual-studio"></a>[Visual Studio](#tab/VS)

Visual Studio を開き、`Sharing\SharingServiceSample` フォルダーのプロジェクトを開きます。

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

## <a name="visual-studio-code"></a>[Visual Studio Code](#tab/VSC)

VS Code でサービスをデプロイする前に、リソース グループと App Service プランを作成する必要があります。

### <a name="sign-in-to-azure"></a>Azure にサインインする

<a href="https://portal.azure.com/" target="_blank">Azure portal</a> に移動し、Azure サブスクリプションにサインインします。

### <a name="create-a-resource-group"></a>リソース グループを作成する

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

**[リソース グループ]** の横にある **[新規]** をクリックします。

リソース グループに **myResourceGroup** という名前を付けて、**[OK]** をクリックします。

### <a name="create-an-app-service-plan"></a>App Service プランを作成する

[!INCLUDE [app-service-plan](../../../includes/app-service-plan.md)]

**[ホスティング プラン]** の隣にある **[新規]** を選択します。

**[ホスティング プランの構成]** ダイアログ ボックスで、以下の設定を使用します。

| 設定 | 推奨値 | 説明 |
|-|-|-|
|App Service プラン| MySharingServicePlan | App Service プランの名前です。 |
| 場所 | 米国西部 | Web アプリがホストされているデータ センターです。 |
| サイズ | Free | [価格レベル](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)によって、ホスティング機能が決まります。 |

**[OK]** を選択します。

Visual Studio Code を開き、`Sharing\SharingServiceSample` フォルダーのプロジェクトを開きます。 Visual Studio Code を使用して共有サービスをデプロイするには、<a href="https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vscode?view=aspnetcore-2.2#open-it-with-visual-studio-code" target="_blank">こちらのチュートリアル</a>に従ってください。 「Visual Studio Code でプロジェクトを開く」セクションから手順を実行できます。 デプロイして公開する必要があるプロジェクト SharingServiceSample が既に存在しているため、前の手順で説明されているように別の ASP.NET プロジェクトを作成しないでください。

---

## <a name="deploy-the-sample-app"></a>サンプル アプリのデプロイ

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、ASP.NET Core Web アプリを Azure にデプロイした後、Unity アプリを構成してデプロイしました。 そのアプリで空間アンカーを作成し、ASP.NET Core Web アプリを使用して他のデバイスと共有しました。

Azure Cosmos DB を使用して共有済みの空間アンカー識別子のストレージを永続化するように、ASP.NET Core Web アプリを改良できます。 Azure Cosmos DB のサポートを追加することにより、ASP.NET Core Web アプリでは、今日作成したアンカーを、Web アプリに保存されているアンカー識別子を使用して数日後に再度探知できるようになります。

> [!div class="nextstepaction"]
> [Azure Cosmos DB を使用したアンカーの格納](./tutorial-use-cosmos-db-to-store-anchors.md)

