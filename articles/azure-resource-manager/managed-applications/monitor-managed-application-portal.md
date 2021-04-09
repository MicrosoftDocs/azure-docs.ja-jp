---
title: Azure portal を使用してマネージド アプリを監視する
description: Azure portal を使用してマネージド アプリケーションの可用性やアラートを監視する方法について説明します。
author: tfitzmac
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: a02062edd1a940bcc6588ab53457e0af91fedd9a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100578277"
---
# <a name="monitor-a-deployed-instance-of-a-managed-application"></a>マネージド アプリケーションのデプロイしたインスタンスの監視

Azure サブスクリプションにマネージ アプリケーションをデプロイしたら、そのアプリケーションの状態を確認することができます。 この記事では、状態を確認するための Azure portal でのオプションについて説明します。 マネージド アプリケーションにおけるリソースの可用性を監視できます。 アラートを設定し、表示することもできます。

## <a name="view-resource-health"></a>リソースの正常性を表示する

1. マネージド アプリケーション インスタンスを選択します。

   ![マネージド アプリケーションの選択](./media/monitor-managed-application-portal/select-managed-application.png)

1. **[リソース正常性]** を選択します。

   ![[リソース正常性] の選択](./media/monitor-managed-application-portal/select-resource-health.png)

1. マネージド アプリケーションにおけるリソースの可用性を表示します。

   ![リソースの正常性を表示する](./media/monitor-managed-application-portal/view-health.png)

## <a name="view-alerts"></a>アラートを表示する

1. **[Alerts]** (アラート) を選択します。

   ![アラートの選択](./media/monitor-managed-application-portal/select-alerts.png)

1. アラート ルールを構成した場合、発生しているアラートに関する情報が表示されます。

   ![アラートを表示する](./media/monitor-managed-application-portal/view-alerts.png)

1. アラート ルールを追加するには、 **[+ 新しいアラート ルール]** を選択します。

   ![アラートを作成する](./media/monitor-managed-application-portal/create-new-alert.png)

マネージド アプリケーション インスタンスのアラートまたはマネージド アプリケーションのリソースを作成できます。 アラートの作成の詳細については、「[Microsoft Azure のアラートの概要](../../azure-monitor/alerts/alerts-overview.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* マネージド アプリケーションの例については、[Azure マネージド アプリケーションのサンプル プロジェクト](sample-projects.md)に関する記事を参照してください。
* マネージ アプリケーションをデプロイするには、[Azure portal を通したサービス カタログ アプリのデプロイ](deploy-service-catalog-quickstart.md)に関するページを参照してください。