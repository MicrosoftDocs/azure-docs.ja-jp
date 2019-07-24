---
title: アラート インスタンスを管理する
description: Azure 全体でのアラートのインスタンスの管理
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: cb93f38c05156d7ab5acb89ffff810949583e507
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58089405"
---
# <a name="manage-alert-instances"></a>アラート インスタンスを管理する
Azure Monitor の[統合アラート エクスペリエンス](https://aka.ms/azure-alerts-overview)では、1 つのウィンドウで、複数のサブスクリプションにまたがる Azure 全体のさまざまな種類のアラートをすべて表示できます。 この記事では、アラート インスタンスを表示する方法と特定のアラート インスタンスのトラブルシューティングを見つけるためにポータルで詳細を確認する方法を説明します。

1. [アラート] ページには、3 つの方法が示されています

   + [ポータル](https://portal.azure.com/)で **[モニター]** を選び、[モニター] セクションで **[アラート]** を選びます。  
     ![監視](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
   + 特定の**リソース**のコンテキスト内からアラートに移動できます。 リソースが開いたら、目次を介して [監視] セクションにナビゲートし、**[アラート]** を選択します。その特定リソースに関するアラートの、事前にフィルター処理されているランディング ページが示されます。
   
     ![監視](media/alerts-managing-alert-instances/alert-resource.JPG)
    
   + 特定の**リソース グループ**のコンテキスト内からの [アラート] に移動できますします。 リソース グループが開いたら、目次を介して [監視] セクションにナビゲートし、**[アラート]** を選択します。その特定リソースに関するアラートの、事前にフィルター処理されているランディング ページが示されます。    
   
     ![監視](media/alerts-managing-alert-instances/alert-rg.JPG)

1. **[アラートの概要]** ページが表示され、Azure 全体の、すべてのアラート インスタンスの概要がわかります。 概要ビューは、**複数のサブスクリプション** (最大 5) を選択するか、**リソース グループ**、特定の**リソース**、または**時間の範囲**でフィルター処理することで変更できます。 [Total Alerts] (アラートの合計) またはいずれかの重要度バンドのどちらかをクリックして、アラートのリスト ビューに移動します。     
   ![Alerts Summary](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
1. Azure 全体のすべてのアラート インスタンスが表示されている **[すべてのアラート]** ページが示されます。アラートの通知から portal を表示した場合に、その特定のアラート インスタンスを絞り込むためにフィルターを使用することができます。 (**注**: 重要度のバンドのいずれかをクリックして、ページを表示した場合、そのリストは表示したときにその重要度に合わせて事前にフィルター処理されています)。 前のページで使用可能なフィルターとは別に、モニター サービス (たとえば、メトリックのプラットフォーム)、モニターの状態 (起動済みまたは解決済み)、重要度、アラートの状態 (new/acknowledged/closed)、またはスマート グループ ID に基づいてフィルター処理することができます。

   ![すべての警告](media/alerts-managing-alert-instances/all-alerts.jpg)

   > [!NOTE]
   >  重要度のバンドのいずれかをクリックして、ページを表示した場合、そのリストはこのページを表示したときにその重要度に合わせて事前にフィルター処理されています。
 
1. アラート インスタンスをクリックして、**[アラートの詳細]** ページが開いたら、その特定のアラート インスタンスに関する情報の詳細を確認できます。   
   ![アラートの詳細](media/alerts-managing-alert-instances/alert-details.jpg)  

