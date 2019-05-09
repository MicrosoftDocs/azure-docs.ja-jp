---
title: インクルード ファイル
description: インクルード ファイル
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 02/19/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 97387e24d5b55c1438a69da1a1fd0a9bc1720e47
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/24/2019
ms.locfileid: "56751285"
---
1. **ソリューション エクスプローラー**で、プロジェクトを右クリックし、**[発行]** を選択します。

1. **[発行]** ダイアログで、**[Microsoft Azure App Service]** を選択し、**[新規作成]** を選択して、**[発行]** をクリックします。

   ![発行先の選択](./media/webjobs-publish-netcore/pick-publish-target.png)

1. **[App Service の作成]** ダイアログで、画像の下の表に指定されているようにホスティング設定を使用してください。

    ![[App Service の作成] ダイアログ](./media/webjobs-publish-netcore/app-service-dialog.png)

    | Setting      | 推奨値  | 説明                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **アプリ名** | グローバルに一意の名前 | 新しい関数アプリを一意に識別する名前。 |
    | **サブスクリプション** | サブスクリプションの選択 | 使用する Azure サブスクリプション。 |
    | **[リソース グループ](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  関数アプリを作成するリソース グループの名前。 新しいリソース グループを作成する場合は、**[新規]** を選択します。|
    | **[ホスティング プラン](../articles/app-service/overview-hosting-plans.md)** | App Service プラン | [App Service プラン](../articles/app-service/overview-hosting-plans.md)は、アプリのホストとなる Web サーバー ファームの場所、サイズ、機能を規定します。 1 つの App Service プランを共有するように Web アプリを構成することで、複数のアプリをホストするときのコストを抑えることができます。 App Service プランでは、リージョン、インスタンス サイズ、スケール数、および SKU (Free、Shared、Basic、Standard、または Premium) を定義します。 **[新規]** を選択して、新しい App Service プランを作成します。 |

1. **[作成]** をクリックして、これらの設定で Azure に Web ジョブと関連リソースを作成し、プロジェクト コードをデプロイします。