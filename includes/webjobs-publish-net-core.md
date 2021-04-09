---
title: インクルード ファイル
description: インクルード ファイル
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 06/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: e208b52c67f173bd0d289715b63562df656b1ec9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88009780"
---
1. **ソリューション エクスプローラー** で、プロジェクトを右クリックし、 **[発行]** を選択します。

1. **[発行]** ダイアログ ボックスの **[対象]** で **[Azure]** を選択してから、 **[次へ]** を選択します。 

1. **[特定のターゲット]** で **[Azure WebJobs]** を選択してから、 **[次へ]** を選択します。

1. **[新しい Azure Web ジョブを作成する]** を選択します。

   ![発行先の選択](./media/webjobs-publish-netcore/pick-publish-target.png)

1. **[App Service (Windows)]** ダイアログ ボックスでは、次の表のホスティング設定を使用します。

    | 設定      | 推奨値  | 説明                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **名前** | グローバルに一意の名前 | 新しい関数アプリを一意に識別する名前。 |
    | **サブスクリプション** | サブスクリプションの選択 | 使用する Azure サブスクリプション。 |
    | **[リソース グループ](../articles/azure-resource-manager/management/overview.md)** | myResourceGroup |  関数アプリを作成するリソース グループの名前。 新しいリソース グループを作成する場合は、 **[新規]** を選択します。|
    | **[ホスティング プラン](../articles/app-service/overview-hosting-plans.md)** | App Service プラン | [App Service プラン](../articles/app-service/overview-hosting-plans.md)は、アプリのホストとなる Web サーバー ファームの場所、サイズ、機能を規定します。 1 つの App Service プランを共有するように Web アプリを構成することで、複数のアプリをホストするときのコストを抑えることができます。 App Service プランでは、リージョン、インスタンス サイズ、スケール数、および SKU (Free、Shared、Basic、Standard、または Premium) を定義します。 **[新規]** を選択して、新しい App Service プランを作成します。 |

    ![[App Service の作成] ダイアログ ボックス](./media/webjobs-publish-netcore/app-service-dialog.png)

1. **[作成]** を選択して、これらの設定で Azure に Web ジョブと関連リソースを作成し、プロジェクト コードをデプロイします。