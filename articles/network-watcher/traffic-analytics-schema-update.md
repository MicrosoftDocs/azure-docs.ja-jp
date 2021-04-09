---
title: Azure Traffic Analytics スキーマ更新 - 2020 年 3 月 | Microsoft Docs
description: Traffic Analytics スキーマに新しいフィールドを使用したサンプル クエリ。
services: network-watcher
documentationcenter: na
author: vinigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: d7c4f1853ff8dcb9249ab6ec4f536e1f8cfa10e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018226"
---
# <a name="sample-queries-with-new-fields-in-the-traffic-analytics-schema-august-2019-schema-update"></a>Traffic Analytics スキーマに新しいフィールドを使用したサンプル クエリ (2019 年 8 月のスキーマ更新)

[Traffic Analytics ログ スキーマ](./traffic-analytics-schema.md)に追加された新しいフィールドは、**SrcPublicIPs_s**、**DestPublicIPs_s**、**NSGRule_s** です。 新しいフィールドは、送信元 IP と送信先 IP に関する情報を提供し、クエリを簡素化します。

次の数か月の間、次の古いフィールドが非推奨になります。**VMIP_s**、**Subscription_g**、**Region_s**、**NSGRules_s**、**Subnet_s**、**VM_s**、**NIC_s**、**PublicIPs_s**、**FlowCount_d**。

次の 3 つの例は、古いフィールドを新しいフィールドに置き換える方法を示しています。

## <a name="example-1-vmip_s-subscription_g-region_s-subnet_s-vm_s-nic_s-and-publicips_s-fields"></a>例 1:VMIP_s、Subscription_g、Region_s、Subnet_s、VM_s、NIC_s、PublicIPs_s の各フィールド

AzurePublic フローと ExternalPublic フローの **FlowDirection_s** フィールドから送信元ケースと送信先ケースを推測する必要はありません。 ネットワーク仮想アプライアンスの場合、**FlowDirection_s** フィールドも使用に適さないことがあります。

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend isAzureOrExternalPublicFlows = FlowType_s in ("AzurePublic", "ExternalPublic")
| extend SourceAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), VM1_s),
SourceAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), SrcIP_s),
SourceAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subscription_g, "N/A"), Subscription1_g),
SourceAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Region_s, "N/A"), Region1_s),
SourceAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subnet_s, "N/A"), Subnet1_s),
SourceAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', NIC_s, "N/A"), NIC1_s),
DestAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), VM2_s),
DestAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), DestIP_s),
DestAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subscription_g, "N/A"), Subscription2_g),
DestAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Region_s, "N/A"), Region2_s),
DestAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subnet_s, "N/A"), Subnet2_s),
DestAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', NIC_s, "N/A"), NIC2_s),
SourcePublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'I', PublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'O', PublicIPs_s, "N/A")
```


```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend SourceAzureVM = iif(isnotempty(VM1_s), VM1_s, "N/A"),
SourceAzureVMIP = iif(isnotempty(SrcIP_s), SrcIP_s, "N/A"),
SourceAzureVMSubscription = iif(isnotempty(Subscription1_g), Subscription1_g, "N/A"),
SourceAzureRegion = iif(isnotempty(Region1_s), Region1_s, "N/A"),
SourceAzureSubnet = iif(isnotempty(Subnet1_s), Subnet1_s, "N/A"),
SourceAzureNIC = iif(isnotempty(NIC1_s), NIC1_s, "N/A"),
DestAzureVM = iif(isnotempty(VM2_s), VM2_s, "N/A"),
DestAzureVMIP = iif(isnotempty(DestIP_s), DestIP_s, "N/A"),
DestAzureVMSubscription = iif(isnotempty(Subscription2_g), Subscription2_g, "N/A"),
DestAzureRegion = iif(isnotempty(Region2_s), Region2_s, "N/A"),
DestAzureSubnet = iif(isnotempty(Subnet2_s), Subnet2_s, "N/A"),
DestAzureNIC = iif(isnotempty(NIC2_s), NIC2_s, "N/A"),
SourcePublicIPsAggregated = iif(isnotempty(SrcPublicIPs_s), SrcPublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isnotempty(DestPublicIPs_s), DestPublicIPs_s, "N/A")
```

## <a name="example-2-nsgrules_s-field"></a>例 2:NSGRules_s フィールド

古いフィールドでは次の形式を使用していました。

`<Index value 0)>|<NSG_ RuleName>|<Flow Direction>|<Flow Status>|<FlowCount ProcessedByRule>`

ネットワーク セキュリティ グループ (NSG) 全体でデータを集計しなくなりました。 更新されたスキーマでは、**NSGList_s** に含まれる NSG は 1 つだけです。 また、**NSGRules** に含まれるルールは 1 つだけです。 例で示しているように、ここを含むフィールドで複雑な形式を廃止しました。

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| extend NSGName = NSGList_s // remains same
| extend NSGRuleName = NSGRuleComponents[1],
         FlowDirection = NSGRuleComponents[2],
         FlowStatus = NSGRuleComponents[3],
         FlowCountProcessedByRule = NSGRuleComponents[4]
| project NSGName, NSGRuleName, FlowDirection, FlowStatus, FlowCountProcessedByRule
```

```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| project NSGName = NSGList_s,
NSGRuleName = NSGRule_s ,
FlowDirection = FlowDirection_s,
FlowStatus = FlowStatus_s,
FlowCountProcessedByRule = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d
```

## <a name="example-3-flowcount_d-field"></a>例 3: FlowCount_d フィールド

NSG 全体でデータを集約しないので、**FlowCount_d** は単純に次のようになります。

**AllowedInFlows_d** + **DeniedInFlows_d** + **AllowedOutFlows_d** + **DeniedOutFlows_d**

4 つのフィールドのうち 1 つだけがゼロ以外になります。 他の 3 つのフィールドはゼロになります。 これらのフィールドには、フローがキャプチャされた NIC の状態と数を示すためにデータが入力されます。

条件は次のとおりです。

- フローが許可された場合、"Allowed" というプレフィックスの付いたフィールドの 1 つにデータが入力されます。
- フローが拒否された場合、"Denied" というプレフィックスの付いたフィールドの 1 つにデータが入力されます。
- 入ってくるフローであった場合、"InFlows_d" というサフィックスの付いたフィールドの 1 つにデータが入力されます。
- 出ていくフローであった場合、"OutFlows_d" というサフィックスの付いたフィールドの 1 つにデータが入力されます。

条件に応じて、4 つのフィールドのどれにデータが入力されるかわかります。

## <a name="next-steps"></a>次のステップ

- よく寄せられる質問とその回答については、[Traffic Analytics に関する FAQ](traffic-analytics-faq.md) のページをご覧ください。
- 機能の詳細については、[Traffic Analytics のドキュメント](traffic-analytics.md)を参照してください。