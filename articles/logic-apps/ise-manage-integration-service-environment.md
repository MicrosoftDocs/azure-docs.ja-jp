---
title: Azure Logic Apps で統合サービス環境を管理する
description: ネットワーク正常性を確認し、Azure Logic Apps の統合サービス環境 (ISE) のロジック アプリ、接続、カスタム コネクタ、および統合アカウントを管理します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 6a6b096911ac8596fe29aeb4596f1da6d5266794
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70967810"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>Azure Logic Apps で統合サービス環境 (ISE) を管理する

[統合サービス環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) のネットワーク正常性を確認し、ISE に存在するロジック アプリ、接続、統合アカウント、コネクタを管理するには、このトピックの手順に従います。 これらの成果物を ISE に追加するには、[成果物の統合サービス環境への追加](../logic-apps/add-artifacts-integration-service-environment-ise.md)に関する記事を参照してください。

## <a name="view-your-ise"></a>ISE を表示する

1. [Azure Portal](https://portal.azure.com) にサインインします。

1. ポータルの検索ボックスに「統合サービス環境」と入力し、 **[統合サービス環境]** を選択します。

   ![統合サービス環境を見つける](./media/ise-manage-integration-service-environment/find-integration-service-environment.png)

1. 結果リストで [統合サービス環境] を選択します。

   ![統合サービス環境を選択する](./media/ise-manage-integration-service-environment/select-integration-service-environment.png)

1. 次のセクションに進み、ISE のロジック アプリ、接続、コネクタ、または統合アカウントを見つけます。

<a name="check-network-health"></a>

## <a name="check-network-health"></a>ネットワーク正常性を確認する

ISE メニューの **[設定]** で、 **[ネットワーク正常性]** を選択します。 このウィンドウには、サブネットの正常性状態と、他のサービスに対する送信依存関係が表示されます。

![ネットワーク正常性を確認する](./media/ise-manage-integration-service-environment/ise-check-network-health.png)

<a name="find-logic-apps"></a>

## <a name="manage-your-logic-apps"></a>ロジック アプリを管理する

ISE 内のロジック アプリを表示および管理できます。

1. ISE メニューの **[設定]** で、 **[ロジック アプリ]** を選択します。

   ![ロジック アプリを表示する](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. ISE で不要になったロジック アプリを削除するには、それらのロジック アプリを選択し、 **[削除]** を選択します。 削除することを確認するために、 **[はい]** を選択します。

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>API 接続を管理する

ISE で実行されているロジック アプリによって作成された接続を表示および管理できます。

1. ISE メニューの **[設定]** で **[API 接続]** を選択します。

   ![API 接続を表示する](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. ISE で不要になった接続を削除するには、それらの接続を選択し、 **[削除]** を選択します。 削除することを確認するために、 **[はい]** を選択します。

<a name="manage-api-connectors"></a>

## <a name="manage-ise-connectors"></a>ISE コネクタを管理する

ISE にデプロイされている API コネクタを表示および管理できます。

1. ISE メニューの **[設定]** で、 **[マネージド コネクタ]** を選択します。

   ![マネージド コネクタを表示する](./media/ise-manage-integration-service-environment/ise-view-managed-connectors.png)

1. ISE で使用しないコネクタを削除するには、それらのコネクタを選択し、 **[削除]** を選択します。 削除することを確認するために、 **[はい]** を選択します。

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>カスタム コネクタを管理する

ISE にデプロイしたカスタム コネクタを表示および管理できます。

1. ISE メニューの **[設定]** で、 **[カスタム コネクタ]** を選択します。

   ![カスタム コネクタを見つける](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. ISE で不要になったカスタム コネクタを削除するには、それらのコネクタを選択し、 **[削除]** を選択します。 削除することを確認するために、 **[はい]** を選択します。

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>統合アカウントを管理する

1. ISE メニューの **[設定]** で、 **[統合アカウント]** を選択します。

   ![統合アカウントの検索](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. 不要になった統合アカウントを ISE から削除するには、該当する統合アカウントを選択し、 **[削除]** を選択します。

## <a name="next-steps"></a>次の手順

* [分離されたロジック アプリから Azure Virtual Network に接続する](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)方法を理解する
