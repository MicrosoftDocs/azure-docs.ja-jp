---
title: Azure Monitor を使用して監視とメトリックを構成する
titleSuffix: Azure Bastion
description: Azure 全体のすべてのメトリック、アラート、診断ログ用のソリューションである Azure Monitor を使用した、Azure Bastion の監視、メトリクス、およびアラートについて説明します。
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: how-to
ms.date: 03/12/2021
ms.author: mialdrid
ms.openlocfilehash: 3a5beba3938b5a845a378ede155f2f64e6baac7a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103417945"
---
# <a name="how-to-configure-monitoring-and-metrics-for-azure-bastion-using-azure-monitor"></a>Azure Monitor を使用して Azure Bastion の監視とメトリックを構成する方法

この記事では、Azure Monitor を使用して Azure Bastion の監視とメトリックを操作する方法について説明します。

>[!NOTE]
>**クラシック メトリック** の使用はお勧めしません。
>

## <a name="about-metrics"></a>メトリックについて

Azure Bastion には、さまざまなメトリックが用意されています。 次の表は、使用可能な各メトリックのカテゴリとディメンションを示しています。

|**メトリック**|**カテゴリ**|**ディメンション**|
| --- | --- | --- |
|Bastion 通信状態**|[可用性](#availability)|該当なし|
|メモリの合計|[可用性](#availability)|インスタンス|
|CPU 使用率|[トラフィック](#traffic)|インスタンス
|メモリ使用量|[トラフィック](#traffic)|インスタンス
|セッション数|[パフォーマンス](#performance)|インスタンス|

** Bastion 通信状態は、2020 年 11 月以降に展開された bastion ホストにのみ適用されます。

### <a name="availability-metrics"></a><a name="availability"></a>可用性のメトリック

#### <a name="bastion-communication-status"></a><a name="communication-status"></a>Bastion 通信状態

Azure Bastion の通信状態を、bastion ホストを構成するすべてのインスタンスで集計して表示できます。

* 値が **1** の場合は、bastion が使用可能であることを示します。
* 値が **0** の場合は、bastion サービスが使用不可であることを示します。

:::image type="content" source="./media/metrics-monitor-alert/communication-status.png" alt-text="通信状態を示すスクリーンショット。":::

#### <a name="total-memory"></a><a name="total-memory"></a>メモリの合計

Azure Bastion のメモリの合計を、各 bastion インスタンスに分割して表示できます。

:::image type="content" source="./media/metrics-monitor-alert/total-memory.png" alt-text="合計メモリを示すスクリーンショット。":::

### <a name="traffic-metrics"></a><a name="traffic"></a>トラフィックメトリック

#### <a name="used-cpu"></a><a name="used-cpu"></a>CPU 使用率

Azure Bastion の CPU 使用率を、各 bastion インスタンスに分割して表示できます。 このメトリックを監視すると、Azure Bastion を構成するインスタンスの可用性と容量を測定できます。

:::image type="content" source="./media/metrics-monitor-alert/used-cpu.png" alt-text="CPU 使用率を示すスクリーンショット。":::

#### <a name="used-memory"></a><a name="used-memory"></a>メモリ使用量

各 bastion インスタンスでのメモリ使用量を、各 bastion インスタンスに分割して表示できます。 このメトリックを監視すると、Azure Bastion を構成するインスタンスの可用性と容量を測定できます。

:::image type="content" source="./media/metrics-monitor-alert/used-memory.png" alt-text="メモリ使用量を示すスクリーンショット。":::

### <a name="performance-metrics"></a><a name="performance"></a>パフォーマンス メトリック

#### <a name="session-count"></a>セッション数

bastion インスタンスあたりのアクティブ セッションの数を、各セッションの種類 (RDP と SSH) で集計して表示できます。 各 Azure Bastion は、アクティブな RDP セッションと SSH セッションの範囲をサポートできます。 このメトリックを監視すると、bastion サービスを実行しているインスタンスの数を調整する必要があるかどうかを理解するのに役立ちます。 Azure Bastion がサポートできるセッション数の詳細については、「[Azure Bastion に関する FAQ](bastion-faq.md)」を参照してください。

:::image type="content" source="./media/metrics-monitor-alert/session-count.png" alt-text="セッション数を示すスクリーンショット。":::

## <a name="how-to-view-metrics"></a><a name="metrics"></a>メトリックを表示する方法

1. メトリックを表示するには、bastion ホストに移動します。
1. **[監視]** の一覧から **[メトリック]** を選択します。
1. パラメーターを選択します。 メトリックが設定されていない場合は、 **[メトリックの追加]** をクリックし、パラメーターを選択します。

   * **[スコープ]:** 既定では、スコープは bastion ホストに設定されます。
   * **[メトリック名前空間]:** [標準メトリック]。
   * **[メトリック]:** 表示するメトリックを選択します。

1. メトリックを選択すると、既定の集計が適用されます。 必要に応じて、さまざまなディメンションでメトリックを表示する分割を適用できます。

## <a name="next-steps"></a>次のステップ

[Azure Bastion に関する FAQ](bastion-faq.md) を読む。
  
