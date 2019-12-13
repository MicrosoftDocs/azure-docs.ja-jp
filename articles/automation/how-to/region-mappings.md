---
title: Azure Automation と Log Analytics ワークスペースのマッピング
description: この記事では、ソリューションをサポートするために、Automation アカウントと Log Analytics ワークスペース間で許可されているマッピングについて説明します
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 965d6b206bb64e90fe59798ce0c37ccf029117f5
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849515"
---
# <a name="workspace-mappings"></a>ワークスペースのマッピング

Update Management、Change Tracking と Inventory、Start/Stop VMs during off-hours などのソリューションを有効にする場合、Log Analytics ワークスペースと Automation アカウントをリンクするために特定のリージョンのみがサポートされます。 このマッピングは Automation アカウントと Log Analytics ワークスペースのみに適用されます。 Automation アカウントまたは Log Analytics ワークスペースに報告するリソースは、他のリージョンに配置できます。

## <a name="supported-mappings"></a>サポートされるマッピング

サポートするマッピングを次の表に示します。

|**Log Analytics ワークスペース リージョン**|**Azure Automation リージョン**|
|---|---|
|**米国**||
|EastUS<sup>1</sup>|EastUS2|
|WestUS2|WestUS2|
|WestCentralUS<sup>2</sup>|WestCentralUS<sup>2</sup>|
|**カナダ**||
|CanadaCentral|CanadaCentral|
|**アジア太平洋**||
|AustraliaSoutheast|AustraliaSoutheast|
|SoutheastAsia|SoutheastAsia|
|CentralIndia|CentralIndia|
|JapanEast|JapanEast|
|**ヨーロッパ**||
|UKSouth|UKSouth|
|西ヨーロッパ|西ヨーロッパ|
|**米国政府**||
|USGovVirginia|USGovVirginia|

<sup>1</sup> EastUS の Log Analytics ワークスペースと Automation アカウントのマッピングは、正確なリージョン間のマッピングではありませんが、適切なマッピングです。

<sup>2</sup> 容量の制約により、新しいリソースを作成するときにリージョンを使用できません。 これには、Automation アカウントと Log Analytics ワークスペースが含まれます。 ただし、リージョン内のリンクされた既存のリソースは引き続き動作します。

## <a name="unlink-workspace"></a>ワークスペースのリンクの解除

ご使用の Automation アカウントを Log Analytics ワークスペースと統合する必要がなくなった場合は、Azure portal から直接、そのアカウントのリンクを解除できます。 Update Management、Change Tracking と Inventory、または Start/Stop VMs during off-hours のソリューションを使用している場合、続行する前にこれらを削除する必要があります。 これらを削除しない場合、このプロセスは続行できません。 インポート済みのソリューションに関する記事を確認して、削除に必要な手順を理解してください。

これらのソリューションを削除したら、以下の手順を行うと、Automation アカウントのリンクを解除できます。

> [!NOTE]
> Azure SQL 監視ソリューションの以前のバージョンを含む一部のソリューションでは、Automation アセットを作成している可能性があり、ワークスペースのリンクを解除する前にその削除が必要な場合があります。

1. Azure portal から Automation アカウントを開き、[Automation アカウント] ページで、左側にある **[関連リソース]** セクションで **[リンクされたワークスペース]** を選択します。

2. [ワークスペースのリンクを解除] ページ **[ワークスペースのリンクを解除]** をクリックします。 続行するかどうかを確認するプロンプトが表示されます。

3. Azure Automation によってアカウントと Log Analytics ワークスペースとのリンクが解除されている間、メニューの **[通知]** で進行状況を追跡できます。

更新の管理ソリューションを使用していた場合は、ソリューションの削除後に不要になる以下の項目を削除することもできます。

* スケジュールの更新 - 各スケジュールには、作成した更新のデプロイに一致する名前が付いています。

* ソリューション用に作成されたハイブリッド ワーカー グループ -  それぞれ `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8` のような名前が付いています。

勤務時間外の VM の開始/停止ソリューションを使用していた場合は、ソリューションの削除後に不要になる以下の項目を削除することもできます。

* VM の開始/停止の Runbook スケジュール
* VM の開始/停止の Runbook
* 変数

Automation アカウントの自分のワークスペースを Log Analytics ワークスペースからリンク解除することもできます。 自分のワークスペースで、 **[関連リソース]** の **[Automation アカウント]** を選択します。 [Automation アカウント] ページで **[アカウントのリンク解除]** を選択します。

## <a name="next-steps"></a>次の手順

以下のソリューションをオンボードする方法について学習します。

Update Management および Change Tracking と Inventory

* [仮想マシン](../automation-onboard-solutions-from-vm.md)から
* お使いの [Automation アカウント](../automation-onboard-solutions-from-automation-account.md)から
* [複数のマシンを参照する](../automation-onboard-solutions-from-browse.md)とき
* [Runbook](../automation-onboard-solutions.md) から

勤務時間外に VM を起動/停止する

* [Start/Stop VMs during off-hours のデプロイ](../automation-solution-vm-management.md)
