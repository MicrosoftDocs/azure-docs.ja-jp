---
title: Azure Monitor で System Center Operations Manager、Zabbix、Nagios からのアラートを管理する
description: Azure Monitor で System Center Operations Manager、Zabbix、Nagios からのアラートを管理する
ms.topic: conceptual
ms.date: 09/24/2018
ms.subservice: alerts
ms.openlocfilehash: 9fbad920bdfa26419e4b865db5d4e53dad97acce
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86539450"
---
# <a name="manage-alerts-from-system-center-operations-manager-zabbix-and-nagios-in-azure-monitor"></a>Azure Monitor で System Center Operations Manager、Zabbix、Nagios からのアラートを管理する

[Azure Monitor](https://aka.ms/azure-alerts-overview) で Nagios、Zabbix、および System Center Operations Manager からのアラートを確認できるようになりました。 Nagios/Zabbix サーバーまたは System Center Operations Manager と Log Analytics が統合されたことで、このようなアラートが実現しました。 

## <a name="prerequisites"></a>前提条件
Azure Monitor には Log Analytics リポジトリ内の種類がアラートであるレコードがインポートされるので、これらのレコードを収集するために必要な構成を実行する必要があります。
1. **Nagios** と **Zabbix** のアラートの場合は、Log Analytics に[アラートを送信](./data-sources-custom-logs.md?toc=/azure/azure-monitor/toc.json)するように[これらのサーバーを構成](../learn/quick-collect-linux-computer.md)します。
1. **System Center Operations Manager** のアラートの場合は、[Log Analytics ワークスペースに Operations Manager 管理グループを接続します](./om-agents.md)。 その後、Azure ソリューション マーケットプレースから [Alert Management](./alert-management-solution.md) ソリューションをデプロイします。 これが完了すると、System Center Operations Manager で作成されたすべてのアラートが Log Analytics にインポートされます。

## <a name="view-your-alert-instances"></a>アラート インスタンスを表示する
Log Analytics へのインポートを構成すると、[Azure Monitor](https://aka.ms/azure-alerts-overview) でこれらの監視サービスからのアラート インスタンスの表示を開始できます。 Azure Monitor に表示された後は、[アラート インスタンスを管理](https://aka.ms/managing-alert-instances)し、[これらのアラートに対して作成されたスマート グループを管理](https://aka.ms/managing-smart-groups)し、[アラートとスマート グループの状態を変更する](https://aka.ms/managing-alert-smart-group-states)ことができます。

> [!NOTE]
>  1. このソリューションでは、System Center Operations Manager/Zabbix/Nagios で発行されたアラート インスタンスのみを Azure Monitor に表示できます。 アラート ルールの構成は、それぞれの監視ツールでのみ表示または編集できます。 
>  1. 発行されたすべてのアラート インスタンスは、Azure Monitor と Azure Log Analytics の両方で利用できます。 現時点では、2 つのどちらかを選択したり、特定の発行されたアラートのみを取り込んだりする方法はありません。
>  1. System Center Operations Manager、Zabbix、Nagios からのすべてのアラートでは、基になるテレメトリの種類を入手できないため、シグナルの種類は "不明" になります。
>  1. Nagios アラートはステートフルではありません。たとえば、アラートの[監視条件](https://aka.ms/azure-alerts-overview)は "発生" から "解決済み" に移行しません。 代わりに、"発生" と "解決済み" の両方が別々の警告インスタンスとして表示されます。 
