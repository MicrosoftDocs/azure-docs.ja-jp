---
title: Azure Security Center FAQ - 既存の Log Analytics エージェントに関する質問
description: この FAQ では、Log Analytics エージェントを既に使用しており、脅威の防御、検出、対応を可能にする製品である Azure Security Center を検討しているお客様からの質問に回答します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 1a52b2fec6155959a570f2438a59c14d9f79f368
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101701962"
---
# <a name="faq-for-customers-already-using-azure-monitor-logs"></a>Azure Monitor ログを既に使用しているお客様向けの FAQ<a name="existingloganalyticscust"></a>

## <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Security Center は VM とワークスペース間の既存の接続をオーバーライドしますか?

VM に Azure 拡張機能としてインストールされている Log Analytics エージェントが既にある場合、Security Center は既存のワークスペース接続をオーバーライドしません。 代わりに、Security Center は既存のワークスペースを使用します。 VM のレポート先のワークスペースに "Security" または "SecurityCenterFree" ソリューションがインストールされている場合、VM は保護されます。 

[データ収集] 画面で選択されているワークスペースに Security Center ソリューションがインストールされ (まだ存在しない場合)、ソリューションは関連する VM にのみ適用されます。 ソリューションを追加すると、既定では、そのソリューションは、Log Analytics ワークスペースに接続されているすべての Windows エージェントおよび Linux エージェントに自動的にデプロイされます。 [ソリューションのターゲット設定](../azure-monitor/insights/solution-targeting.md)を使用すると、ソリューションにスコープを適用することができます。

> [!TIP]
> Log Analytics エージェントが VM に (Azure 拡張機能としてではなく) 直接インストールされている場合、Security Center によって Log Analytics エージェントがインストールされず、セキュリティの監視は制限されます。

## <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Security Center は既存の Log Analytics ワークスペースにソリューションをインストールしますか? 課金にどのような影響がありますか?
その VM が、作成したワークスペースに既に接続されていることを Security Center が特定すると、Security Center はご利用の価格構成に従ってそのワークスペースでソリューションを有効にします。 ソリューションは、[ソリューションのターゲット設定](../azure-monitor/insights/solution-targeting.md)に従って関連する Azure VM にのみ適用されるため、課金額は同じままです。

- **Azure Defender オフ** - Security Center によってワークスペースに "SecurityCenterFree" ソリューションがインストールされます。 課金されることはありません。
- **Azure Defender オン** - Security Center によってワークスペースに "Security" ソリューションがインストールされます。

   ![既定のワークスペースのソリューション](./media/security-center-platform-migration-faq/solutions.png)

## <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>環境内に既にワークスペースがありますが、それらを使用してセキュリティ データを収集できますか?
VM に Azure 拡張機能としてインストールされている Log Analytics エージェントが既にある場合、Security Center は既存の接続済みワークスペースを使用します。 Security Center ソリューションがワークスペースにまだ存在しない場合は、インストールされます。また、[ソリューションのターゲット設定](../azure-monitor/insights/solution-targeting.md)に従って、ソリューションは関連する VM にのみ適用されます。

Security Center によって VM 上に Log Analytics エージェントがインストールされると、Security Center では既存のワープスペースを参照していない場合、Security Center によって作成された既定のワークスペースが使用されます。

## <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>ワークスペースにはセキュリティ ソリューションが既にあります。 課金にどのような影響がありますか?
Security & Audit ソリューションは、**Azure Defender for servers** を有効にするために使用されます。 Security & Audit ソリューションがワークスペースに既にインストールされている場合、Security Center は既存のソリューションを使用します。 課金額の変更はありません。