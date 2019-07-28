---
title: Azure リソースのログを SIEM システムと統合する | Microsoft Docs
description: Azure Log Integration の主な機能としくみについて説明します。
services: security
documentationcenter: na
author: tomshinder
manager: barbkess
editor: TerryLanfear
ms.assetid: 9c1346e1-baf8-4975-b2f2-42ae05b2dc0a
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/28/2019
ms.author: tomsh
ms.custom: azlog
ms.openlocfilehash: 65e256b476c1e459ae937d9f6cbb43e0020fd9fe
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66298125"
---
# <a name="introduction-to-azure-log-integration"></a>Azure Log Integration の概要

>[!IMPORTANT]
> Azure ログ統合機能は、2019 年 6 月 15 日までに廃止される予定です。 AzLog ダウンロードは、2018 年 6 月 27 日に無効になりました。 今後必要な対応のガイダンスについては、[Azure 監視を使って SIEM ツールと統合する](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)方法に関する投稿を確認してください。 

Azure Log Integration は、お使いのオンプレミスのセキュリティ情報イベント管理 (SIEM) システムと Azure ログを統合するタスクを簡素化するために、利用できるようになりました。

 Azure ログを統合するための推奨される方法は、お使いの SIEM ベンダーのコネクタを使用することです。 Azure Monitor では、ログをイベント ハブにストリーム配信する機能を提供しており、SIEM ベンダーではさらに、イベント ハブからのログを SIEM に統合するコネクタを作成できます。  このしくみの詳細については、[監視データのイベント ハブへのストリーミング](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)に関する記事の指示に従ってください。 また、直接接続の Azure コネクタを既に使用できる SIEM の一覧も記事に示しています。  

> [!IMPORTANT]
> 主な関心が仮想マシン ログの収集である場合、ほとんどの SIEM ベンダーのソリューションにこのオプションが含まれています。 SIEM ベンダーのコネクタを使用することが常に推奨されます。

Azure Log Integration 機能に関するドキュメントは、機能が非推奨になるまで引き続き保持されています。

Azure Log Integration 機能を詳しく理解するには、以降の記述をお読みください。

Azure Log Integration は、Windows イベント ビューアー ログの Windows イベントと、Azure リソースの [Azure アクティビティ ログ](../azure-monitor/platform/activity-logs-overview.md)、[Azure Security Center のアラート](../security-center/security-center-intro.md)、および [Azure Diagnostics ログ](../azure-monitor/platform/diagnostic-logs-overview.md)を収集します。 Integration によって、オンプレミスとクラウドのすべての資産に対する一元化されたダッシュボードを SIEM ソリューションに用意できます。 ダッシュボードを使用して、セキュリティ イベントに対するアラートの受信、集計、関連付け、および分析を実行できます。

> [!NOTE]
> 現時点では、Azure Log Integration は、Azure 商用クラウドと Azure Government クラウドのみをサポートしています。 その他のクラウドはサポートされていません。

![Azure Log Integration のプロセス][1]

## <a name="what-logs-can-i-integrate"></a>統合できるログ

Azure では、各 Azure サービスの広範なログ記録が作成されます。 ログには、次の 3 つのログの種類があります。

* **コントロール/管理ログ**:[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) の CREATE、UPDATE、および DELETE 操作を可視化します。 Azure アクティビティ ログは、この種類のログの例です。
* **データ関連のログ**:Azure リソースの使用時に発生するイベントを可視化します。 このタイプのログの例は、Windows 仮想マシンにおける Windows イベント ビューアーの**システム**、**セキュリティ**、および**アプリケーション**の各チャンネルです。 別の例は、Azure Monitor を使用して構成する Azure Diagnostics のログです。
* **処理済みイベント**:ユーザーに代わって処理された分析済みのイベントとアラートを示します。 この種類のイベントの例は、Azure Security Center のアラートです。 Azure Security Center は、サブスクリプションを処理して分析し、現在のセキュリティの状態に関連するアラートを生成します。

Azure ログ統合は、ArcSight、QRadar、および Splunk をサポートしています。 ご利用の SIEM ベンダーに問い合わせて、ベンダーにネイティブ コネクタがあるかどうかを確認してください。 ネイティブ コネクタを利用できる場合は、Azure Log Integration を使用しないでください。

他に利用できるオプションがない場合は、Azure Log Integration の使用を検討します。 次の表に、推奨事項を示します。

|SIEM | Azure ログ インテグレーターを既に使用しているお客様 | SIEM の統合オプションを調査しているお客様|
|---------|--------------------------|-------------------------------------------|
|**Splunk** | [Splunk 向けの Azure Monitor アドオン](https://splunkbase.splunk.com/app/3534/)への移行を開始します。 | [Splunk コネクタ](https://splunkbase.splunk.com/app/3534/)を使用します。 |
|**QRadar** | 「[外部ツールで使用する Azure 監視データのイベント ハブへのストリーミング](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)」の最後のセクションに記載されている QRadar コネクタに移行するか、使用を開始します。 | 「[外部ツールで使用する Azure 監視データのイベント ハブへのストリーミング](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)」の最後のセクションに記載されている QRadar コネクタを使用します。 |
|**ArcSight** | コネクタが使用可能になるまで Azure ログ インテグレーターを使い続け、その後コネクタ ベースのソリューションに移行します。  | 別の方法として、Azure Monitor ログの使用を検討してください。 コネクタが利用可能になったときに移行プロセスを実行する意思がない場合は、Azure Log Integration を使用しないでください。 |

> [!NOTE]
> Azure Log Integration は無料のソリューションですが、ログ ファイル情報の保存に関連する Azure ストレージのコストが発生します。

サポートが必要な場合は[サポート リクエスト](../azure-supportability/how-to-create-azure-support-request.md)を作成できます。 サービスとして、 **[Log Integration]** を選択します。

## <a name="next-steps"></a>次の手順

この記事では、Azure Log Integration の概要を説明しました。 Azure Log Integration の詳細と、サポートされているログの種類については、次の記事をご覧ください。

* [Azure Log Integration の使用を開始する](security-azure-log-integration-get-started.md)。 このチュートリアルでは、Azure Log Integration のインストールについて説明します。 Windows Azure Diagnostics (WAD) ストレージ のログ、Azure アクティビティ ログ、Azure Security Center のアラート、および Azure Active Directory の監査ログを統合する方法についても説明します。
* [Azure Log Integration のよく寄せられる質問 (FAQ)](security-azure-log-integration-faq.md)。 この FAQ は、Azure Log Integration について寄せられる質問とその回答です。
* [外部ツールで使用する Azure 監視データのイベント ハブへのストリーミング](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)方法を確認します。

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
