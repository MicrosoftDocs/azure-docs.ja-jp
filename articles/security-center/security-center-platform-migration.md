---
title: "Azure Security Center のプラットフォームの移行 | Microsoft Docs"
description: "このドキュメントでは、Azure Security Center がデータを収集する方法に対するいくつかの変更について説明します。"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 80246b00-bdb8-4bbc-af54-06b7d12acf58
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: 22aa82e5cbce5b00f733f72209318c901079b665
ms.openlocfilehash: 5ddf71dcd9c5a2b03e3b1441d8c9b4d91b6bad12
ms.contentlocale: ja-jp
ms.lasthandoff: 07/24/2017

---
# <a name="azure-security-center-platform-migration"></a>Azure Security Center のプラットフォームの移行

2017 年 6 月上旬以降、Azure Security Center は、セキュリティ データの収集と格納方法の重要な変更をロールアウトします。  これらの変更により、セキュリティ データを簡単に検索する機能などの新機能が公開され、他の Azure 管理サービスや監視サービスとの整合性が高まります。

> [!NOTE]
> このプラットフォームの移行は、運用環境のリソースに影響を与えることはなく、ユーザー側で必要なアクションはありません。


## <a name="whats-happening-during-this-platform-migration"></a>このプラットフォームの移行中に何が行われるか

これまで、Security Center では、Azure Monitoring Agent を使用して、ユーザーの VM からセキュリティ データを収集していました。 これには、脆弱性を識別するために使用されるセキュリティ構成と、脅威を検出するために使用されるセキュリティ イベントに関する情報が含まれています。 このデータは、Azure のストレージ アカウントに保存されていました。

今後、Security Center は、Microsoft Monitoring Agent を使用します。これは Operations Management Suite と Log Analytics サービスで使用されるものと同じエージェントです。 このエージェントから収集されたデータは、VM の位置情報を考慮して、Azure サブスクリプションに関連付けられている既存の *Log Analytics* [ワークスペース](../log-analytics/log-analytics-manage-access.md)または新規のワークスペースのいずれかに格納されます。

## <a name="agent"></a>エージェント

移行の一環として、現在データが収集されているすべての Azure VM に Microsoft Monitoring Agent ([Windows ](../log-analytics/log-analytics-windows-agents.md) 用または [Linux](../log-analytics/log-analytics-linux-agents.md) 用) がインストールされます。  VM に Microsoft Monitoring Agent が既にインストールされている場合、Security Center は、現在インストールされているエージェントを活用します。

一定期間 (通常は数日間) にわたって、データの損失がないスムーズな移行を保証するために、両方のエージェントが共存して実行されます。 これにより、Microsoft は、現在のパイプラインの使用を中止する前に、新しいデータ パイプラインが動作することを検証することができます。 検証が終わったら、Azure Monitoring Agent は VM から削除されます。 ユーザー側で必要な作業はありません。 すべての顧客の移行が完了すると、電子メールで通知されます。
 
セキュリティ データが欠落する可能性があるため、移行中に Azure Monitoring Agent を手動でアンインストールすることは推奨されません。 さらにサポートが必要な場合は、[Microsoft カスタマー サービス & サポート](https://support.microsoft.com/contactus/)にお問い合わせください。 

Microsoft Monitoring Agent for Windows は TCP ポート 443 を使用する必要があります。詳細については、「[Azure Security Center トラブルシューティング ガイド](security-center-troubleshooting-guide.md)」を参照してください。


> [!NOTE] 
> Microsoft Monitoring Agent は他の Azure 管理サービスや監視サービスで使用されている場合があるため、Security Center でデータ収集をオフにしても、このエージェントが自動的にアンインストールされることはありません。 ただし、必要な場合は、手動でエージェントをアンインストールできます。

## <a name="workspace"></a>ワークスペース

前述のように、(Security Center に代わって) Microsoft Monitoring Agent から収集されたデータは、VM の位置情報を考慮して、Azure サブスクリプションに関連付けられている既存の Log Analytics ワークスペースまたは新規のワークスペースのいずれかに格納されます。

Azure ポータルで、Log Analytics ワークスペースの一覧を参照して表示できます。一覧には、Security Center によって作成されたワークスペースも含まれます。 新しいワークスペースに対して、関連するリソース グループが作成されます。 それらは、次の名前付け規則に従います。

- ワークスペース: *DefaultWorkspace-[subscription-ID]-[geo]*
- リソース グループ: *DefaultResouceGroup-[geo]* 
 
Security Center によって作成されたワークスペースでは、データは 30 日間保持されます。 既存のワークスペースでは、リテンション期間は、ワークスペースの価格レベルに基づきます。

> [!NOTE]
> これまで Security Center で収集されたデータは、ストレージ アカウントに残ります。 移行が完了したら、これらのストレージ アカウントを削除することができます。

### <a name="oms-security-solution"></a>OMS セキュリティ ソリューション 

OMS セキュリティ ソリューションがインストールされていない既存のユーザーに対して、Microsoft は、そのワークスペースにソリューションをインストールしますが、Azure VM のみを対象とします。 このソリューションをアンインストールしないでください。これは、アンインストールが OMS 管理コンソールから実行された場合、自動修復する方法がないためです。


## <a name="other-updates"></a>他の更新プログラム

プラットフォームの移行に併せて、いくつかの小規模の追加更新も展開します。

- サポートされる OS バージョンが追加されます。 [こちら](security-center-faq.md#virtual-machines)の一覧を参照してください。
- OS の脆弱性の一覧が拡張されます。 [こちら](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)の一覧を参照してください。
- [価格](https://azure.microsoft.com/pricing/details/security-center/)が時間単位になります (以前は日単位でした)。その結果、一部のお客様にとってはコスト削減になります。
- データの収集は必須になり、Standard 価格レベルのお客様では自動的に有効になります。
- Azure Security Center は、Azure 拡張機能でデプロイされなかったマルウェア対策ソリューションの検出を開始します。 Symantec Endpoint Protection と Defender for Windows 2016 の検出が最初に利用可能になります。
- 防止ポリシーと通知は、"*サブスクリプション*" レベルのみで構成可能ですが、価格設定は、"*リソース グループ*" レベルでこれまでどおり設定できます。


