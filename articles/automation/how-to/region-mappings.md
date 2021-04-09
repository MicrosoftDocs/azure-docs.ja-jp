---
title: リンクされた Log Analytics ワークスペースでサポートされるリージョン
description: この記事では、Azure Automation の特定の機能に関連している、Automation アカウントと Log Analytics ワークスペースとの間でサポートされているリージョン マッピングについて説明します。
ms.date: 02/17/2021
services: automation
ms.topic: conceptual
ms.custom: references_regions
ms.openlocfilehash: 0599dcb57b46d1e48b4035acac8b64edbbe06912
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101720173"
---
# <a name="supported-regions-for-linked-log-analytics-workspace"></a>リンクされた Log Analytics ワークスペースでサポートされるリージョン

Azure Automation では、お使いのサーバーおよび仮想マシンで、Update Management、Change Tracking とインベントリ、Start/Stop VMs during off-hours の各機能を有効にすることができます。 これらの機能は、Log Analytics ワークスペースに依存しているため、ワークスペースを Automation アカウントにリンクする必要があります。 ただし、それらとリンクすることがサポートされているのは、特定のリージョンのみです。 一般に、これらの機能が有効になっていないワークスペースに Automation アカウントをリンクさせる予定の場合、マッピングは適用 *されません*。

ここで説明するマッピングは、Log Analytics ワークスペースを Automation アカウントにリンクする場合にのみ適用されます。 これらは、Automation アカウントにリンクされたワークスペースに接続されている仮想マシン (VM) には適用されません。 VM は、Log Analytics ワークスペースでサポートされているリージョンに限定されません。 VM は任意のリージョンに配置できます。 異なるリージョンに VM を配置した場合、州、地域、国の規制要件や、会社のコンプライアンス要件に影響を与える可能性があることに注意してください。 VM を別のリージョンに配置した場合には、データ帯域幅の料金も発生する可能性があります。

VM を別のリージョンのワークスペースに接続する場合は、事前に要件や潜在的コストについて検討し、法的影響やコストへの影響を確認したうえで、それらを把握しておく必要があります。

この記事では、Automation アカウントでこれらの機能を正常に有効にして使用するために、サポートされているマッピングについて説明します。

詳細については、[Log Analytics ワークスペースと Automation アカウント](../../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account)に関する記事を参照してください。

## <a name="supported-mappings"></a>サポートされるマッピング

> [!NOTE]
> 次の表に示すように、Log Analytics と Azure Automation の間に存在できるマッピングは 1 つに限られます。

サポートするマッピングを次の表に示します。

|**Log Analytics ワークスペース リージョン**|**Azure Automation リージョン**|
|---|---|
|**米国**||
|EastUS<sup>1</sup>|EastUS2|
|EastUS2<sup>2</sup>|EastUS|
|WestUS|WestUS|
|WestUS2|WestUS2|
|CentralUS|CentralUS|
|SouthCentralUS|SouthCentralUS|
|WestCentralUS|WestCentralUS|
|**カナダ**||
|CanadaCentral|CanadaCentral|
|**アジア太平洋**||
|AustraliaEast|AustraliaEast|
|AustraliaSoutheast|AustraliaSoutheast|
|EastAsia|EastAsia|
|SoutheastAsia|SoutheastAsia|
|CentralIndia|CentralIndia|
|ChinaEast2<sup>3</sup>|ChinaEast2|
|JapanEast|JapanEast|
|**ヨーロッパ**||
|NorthEurope|NorthEurope|
|FranceCentral|FranceCentral|
|UKSouth|UKSouth|
|西ヨーロッパ|西ヨーロッパ|
|SwitzerlandNorth|SwitzerlandNorth|
|**US Gov**||
|USGovVirginia|USGovVirginia|
|USGovArizona<sup>3</sup>|USGovArizona|



<sup>1</sup> EastUS の Log Analytics ワークスペースと Automation アカウントのマッピングは、正確なリージョン間のマッピングではありませんが、適切なマッピングです。

<sup>2</sup> EastUS2 の Log Analytics ワークスペースと Automation アカウントのマッピングは、正確なリージョン間のマッピングではありませんが、適切なマッピングです。

<sup>3</sup> このリージョンでは、Update Management のみがサポートされており、現時点では、Change Tracking やインベントリなどの他の機能はご利用いただけません。

## <a name="unlink-a-workspace"></a>ワークスペースのリンク解除

ご使用の Automation アカウントを Log Analytics ワークスペースと統合する必要がなくなった場合は、Azure portal から直接、そのアカウントのリンクを解除できます。 Update Management、Change Tracking とインベントリ、または Start/Stop VMs during off-hours を使用している場合、続行する前にまずこれらを[削除する](move-account.md#remove-features)必要があります。 削除しないと、リンク解除操作を完了できません。

これらの機能を削除した後、以下の手順に従って Automation アカウントのリンクを解除できます。

> [!NOTE]
> Azure SQL 監視ソリューションの以前のバージョンを含む一部の機能では、ワークスペースのリンクを解除する前に削除する必要のある Automation アセットが作成されている可能性があります。

1. Azure portal で、Automation アカウントを開きます。 [Automation アカウント] ページで、 **[関連リソース]** の下にある **[リンクされたワークスペース]** を選択します。

2. [ワークスペースのリンクを解除] ページで、 **[ワークスペースのリンクを解除]** を選択します。 続行するかどうかを確認するプロンプトが表示されます。

3. Azure Automation によってアカウントと Log Analytics ワークスペースとのリンクの解除が処理されている間、メニューの **[通知]** で進行状況を追跡できます。

4. Update Management を使用していた場合は、不要になる以下の項目を削除することもできます。

    * 更新スケジュール:それぞれに、作成した更新プログラムのデプロイと一致する名前が付いています。
    * この機能のために作成したハイブリッド worker グループ:それぞれに `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8` のような名前が付いています。

5. Start/Stop VMs during off-hours を使用していた場合は、不要になる以下の項目を削除することもできます。

    * VM の開始/停止の Runbook スケジュール
    * VM の開始/停止の Runbook
    * 変数

または、ワークスペース内の Automation アカウントからワークスペースのリンクを解除することもできます。

1. ワークスペースで、 **[関連リソース]** の **[Automation アカウント]** を選択します。
2. [Automation アカウント] ページで **[アカウントのリンク解除]** を選択します。

## <a name="next-steps"></a>次のステップ

* Update Management について、[Update Management の概要](../update-management/overview.md)に関する記事で学習する。
* Change Tracking とインベントリについて、「[変更履歴とインベントリの概要](../change-tracking/overview.md)」で学習する。
* Start/Stop VMs during off-hours について、[Start/Stop VMs during off-hours の概要](../automation-solution-vm-management.md)に関する記事で学習する。