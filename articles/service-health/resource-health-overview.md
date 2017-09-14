---
title: "Azure Resource Health の概要 | Microsoft Docs"
description: "Azure Resource Health の概要"
services: Resource health
documentationcenter: 
author: BernardoAMunoz
manager: 
editor: 
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 07/01/2017
ms.author: BernardoAMunoz
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 040d58a81a9b41fe660e4276d698bf884f90bb6c
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="azure-resource-health-overview"></a>Azure Resource Health の概要
 
Resource Health は、リソースに影響を及ぼす Azure の問題が発生した際の診断とサポート利用を支援します。 リソースの現在と過去の正常性に関する情報が表示され、問題を軽減するのに役立ちます。 Resource Health は、Azure のサービスの問題についてサポートが必要な場合にテクニカル サポートを提供します。

[Azure の状態](https://status.azure.com)は広範囲の Azure ユーザーに影響するサービスの問題の情報を提供する一方で、Resource Health はお使いのリソースの正常性に関するパーソナライズされたダッシュボードを提供します。 Resource Health は、過去に Azure サービスの問題によって指定のリソースを利用できなかったすべての時間を示します。 これにより、SLA に違反していたかどうかを簡単に把握できます。 

## <a name="what-is-considered-a-resource-and-how-does-resource-health-decides-if-a-resource-is-healthy-or-not"></a>どのようなものがリソースと認識されるのか、またリソースが正常であるかどうかはどのようにして判断されるか
リソースとは、Azure Resource Manager を通じて Azure サービスによって提供されているリソースの種類 (仮想マシン、Web アプリ、SQL データベースなど) のインスタンスです。

Resource Health は、異なる Azure サービスから生成される信号を基にリソースが正常であるかどうかを評価します。 リソースが正常でない場合、Resource Health は追加の情報を分析して問題の原因を特定します。 また、問題を修正するためにマイクロソフトがとっているアクションや、問題の原因に対処するためにユーザーが実行できるアクションを特定します。 

正常性を評価する方法に関する追加情報については、[Azure Resource Health](resource-health-checks-resource-types.md) に関する記事でリソースの種類と正常性チェックの全一覧を確認してください。

## <a name="health-status-provided-by-resource-health"></a>Resource Health によって提供される正常性状態
リソースの正常性は、次の状態のいずれかです。

### <a name="available"></a>使用可能
サービスがリソースの正常性に影響を与えるイベントを検出していません。 リソースが過去 24 時間以内に予定外のダウンタイムから回復した場合は、**最近回復された**ことを示す通知が表示されます。

![Resource Health の使用可能な仮想マシン](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>使用不可
サービスがリソースの正常性に影響を与えるプラットフォームまたはプラットフォーム以外の継続的なイベントを検出しています。

#### <a name="platform-events"></a>プラットフォームのイベント
これらのイベントは Azure インフラストラクチャの複数のコンポーネントによってトリガーされており、計画的なメンテナンスなどのスケジュールされたアクションと、計画されていないホストの再起動などの予期しないインシデントの両方が含まれます。

Resource Health はイベントと回復プロセスに関する詳細を提供し、ユーザーはアクティブな Microsoft サポート契約がない場合でもサポートに問い合わせることができます。

![プラットフォームのイベントにより Resource Health で使用不可になっている仮想マシン](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>プラットフォーム以外のイベント
これらのイベントは、仮想マシンを停止するなどのユーザーのアクションや、Redis Cache への接続数が最大数に到達した場合にトリガーされます。

![プラットフォーム以外のイベントにより Resource Health で使用不可になっている仮想マシン](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Unknown
この正常性状態は、Resource Health がこのリソースに関する情報を 10 分以上受け取っていないことを示します。 この状態はリソースの状態を明確に示すものではありませんが、トラブルシューティング プロセスにおいて重要なデータ ポイントです。
* リソースが予期した通りに実行されている場合、リソースの状態は数分後に「使用可能」に更新されます。
* リソースで問題が発生している場合は、正常性状態が「使用不可」になり、リソースがプラットフォームのイベントの影響を受けていることを示すことがあります。

![Resource Health で使用不可になっている仮想マシン](./media/resource-health-overview/Unknown.png)

## <a name="report-an-incorrect-status"></a>正しくない状態を報告する
現在の正常性状態が正しくないと思われる場合はいつでも、**[報告]** をクリックして正常ではない状態をマイクロソフトに報告できます。 Azure の問題の影響を受けている場合は、[Resource Health] ブレードからサポートにお問い合わせください。 

![Resource Health が正しくない状態を報告する](./media/resource-health-overview/incorrect-status.png)

## <a name="historical-information"></a>履歴情報
[Resource Health] ブレードの **[履歴の表示]** をクリックすることで、過去 14 日の正常性データの履歴を核にできます。 

![Resource Health のレポートの履歴](./media/resource-health-overview/history-blade.png)

## <a name="getting-started"></a>使用の開始
1 つのリソースについて Resource Health を開くには
1.  Azure Portal にサインインします。
2.  対象のリソースに移動します。
3.  左側にあるリソース メニューで、**[Resource Health]** をクリックします。

![リソース ブレードから Resource Health を開く](./media/resource-health-overview/from-resource-blade.png)

Resource Health には、**[その他のサービス]** をクリックしてフィルター テキスト ボックスに「**resource health**」と入力し、**[ヘルプとサポート]** ブレードを開いてアクセスすることもできます。 最後に、[**Resource Health**](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth) をクリックします。

![[その他のサービス] から Resource Health を開く](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>次のステップ

Resource Health について詳しくは、次のリソースをご覧ください。
-  [Azure Resource Health で利用できるリソースの種類と正常性チェック](resource-health-checks-resource-types.md)
-  [Azure Resource Health に関してよく寄せられる質問](resource-health-faq.md)





