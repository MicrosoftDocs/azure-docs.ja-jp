---
title: "Azure Security Center でのネットワークの保護 | Microsoft Docs"
description: "このドキュメントでは、Azure Security Center での推奨事項に従ってご使用の Azure ネットワークを保護し、セキュリティ ポリシーを使用してコンプライアンスを順守する方法について説明します。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 174546d59124296711731de6c8d8929bada56baf
ms.openlocfilehash: 00b715507a7c3a4d784b800e7bf0c700f6ea6ff1


---
# <a name="protecting-your-network-in-azure-security-center"></a>Azure Security Center でのネットワークの保護
Azure セキュリティ センターは、Azure リソースのセキュリティの状態を分析します。 潜在的なセキュリティの脆弱性を識別すると、Security Center は、必要な管理を構成するプロセスを説明する推奨事項を作成します。  推奨事項は、仮想マシン (VM)、ネットワーク、SQL、およびアプリケーションといった、Azure のリソースの種類に適用されます。

この記事では、ネットワークに適用される推奨事項について説明します。  ネットワークに関する推奨事項は、次世代ファイアウォール、ネットワーク セキュリティ グループ、受信トラフィック規則の構成などが中心です。  次の表を参考にすると、ネットワークに関する利用可能な推奨事項と、それぞれを適用した場合の結果を理解しやすくなります。

## <a name="available-network-recommendations"></a>ネットワークに関する利用可能な推奨事項
| 推奨 | Description |
| --- | --- |
| [次世代ファイアウォールの追加](security-center-add-next-generation-firewall.md) |セキュリティ保護を強化するために、Microsoft パートナーの次世代ファイアウォール (NGFW) を追加することをお勧めします。 |
| [NGFW 経由に限定したトラフィックのルーティング](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only) |VM への受信トラフィックを必ず NGFW 経由にするようにネットワーク セキュリティ グループ (NSG) の規則を構成することをお勧めします。 |
| [サブネットまたは仮想マシンでのネットワーク セキュリティ グループの有効化](security-center-enable-network-security-groups.md) |サブネットまたは VM で NSG を有効にすることをお勧めします。 |
| [インターネットに接続するエンドポイント経由のアクセスの制限](security-center-restrict-access-through-internet-facing-endpoints.md) |NSG に着信トラフィックのルールを構成することをお勧めします。 |

## <a name="see-also"></a>関連項目
その他の Azure リソースの種類に適用される推奨事項の詳細については、次をご覧ください。

* [Azure Security Center での仮想マシンの保護](security-center-virtual-machine-recommendations.md)
* [Azure Security Center でのアプリケーションの保護](security-center-application-recommendations.md)
* [Azure Security Center での Azure SQL サービスの保護](security-center-sql-service-recommendations.md)

セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md) 」-- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md) 」-- セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md) 」-- このサービスの使用に関してよく寄せられる質問が記載されています。



<!--HONumber=Nov16_HO3-->


