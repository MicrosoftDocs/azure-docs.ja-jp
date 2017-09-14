---
title: "Azure のリソースから SIEM システムへのログの統合 | Microsoft Docs"
description: "Azure ログ統合と、その主な機能およびしくみについて紹介します。"
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: 9c1346e1-baf8-4975-b2f2-42ae05b2dc0a
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/10/2017
ms.author: TomSh
ms.custom: azlog
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: 1aa93ac52d1d5c4efe222c6da505e3639170cf55
ms.contentlocale: ja-jp
ms.lasthandoff: 08/11/2017

---
# <a name="introduction-to-microsoft-azure-log-integration"></a>Microsoft Azure ログ統合の概要
Azure ログ統合と、その主な機能およびしくみについて紹介します。

## <a name="overview"></a>概要

Azure ログ統合は無料のソリューションで、未加工のログを、Azure リソースからオンプレミスのセキュリティ情報/イベント管理 (SIEM) システムに統合できるようにします。

Azure ログ統合では、Windows イベント ビューアー ログの Windows イベントと、Azure リソースの [Azure アクティビティ ログ](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)、[Azure Security Center アラート](../security-center/security-center-intro.md)、および [Azure 診断ログ](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)を収集します。 この統合は、SIEM ソリューションですべての資産についてのオンプレミスまたはクラウドの統合ダッシュボードを提供する助けとなります。これによりセキュリティ イベントの集計、関連付け、分析を実行し、アラートを生成できます。

>[!NOTE]
現時点でサポートされているクラウドは、Azure 商用クラウドおよび Azure Government クラウドのみです。 その他のクラウドはサポートされていません。

![Azure ログ統合][1]

## <a name="what-logs-can-i-integrate"></a>統合できるログ
Azure サービスでは、すべてのサービスの広範なログ記録を作成します。 これらのログは、次の 3 種類のログを表しています。

* **コントロール/管理ログ**は、[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) の CREATE、UPDATE、および DELETE 操作の可視性を提供します。 Azure アクティビティ ログは、このタイプのログです。
* **データ プレーン ログ**は、Azure リソース使用の一環として発生するイベントの可視性を提供します。 このタイプのログの例は、Windows 仮想マシンにおける Windows イベント ビューアーの**システム**、**セキュリティ**、および**アプリケーション**の各チャンネルです。 もう 1 つの例は、Azure Monitor によって構成される診断ログです。
* **処理済みイベント**は、ユーザーに代わって処理された分析済みのイベントとアラートを提供します。 このタイプのイベントの例は、Azure Security Center アラートです。Azure Security Center は、ユーザーのサブスクリプションを処理および分析して、ユーザーの現在のセキュリティ体制に関するアラートを提供します。

Azure ログ統合は、ArcSight、QRadar、および Splunk をサポートしています。 いずれの場合でも、ご利用の SIEM ベンダーにネイティブ コネクタがあるかどうかを評価することを問い合わせてください。 ネイティブ コネクタを使用できる場合、状況によっては Azure ログ統合を使用する必要はありません。 サポートされているログの種類の詳細については、よく寄せられる質問 (FAQ) のページを参照してください。

>[!NOTE]
Azure ログの統合は無料のソリューションですが、ログ ファイル情報の保存によって発生する Azure Storage のコストがあります。

[Azure ログ統合に関する MSDN フォーラム](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration)を通して、コミュニティの支援を受けることができます。 このフォーラムでは、AzLog コミュニティに、Azure ログ統合を最大限に活用する方法についての質問、回答、ヒント、およびコツによる相互サポート機能を提供しています。 さらに、Azure ログ統合チームがこのフォーラムを監視しており、可能なときにはいつでも支援を提供します。

[サポート要求](../azure-supportability/how-to-create-azure-support-request.md)を出すこともできます。 これを行うには、サポートを依頼しようとしているサービスとして **[ログ統合]** を選択します。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Azure ログ統合について紹介しました。 Azure ログ統合と、サポートされているログのタイプの詳細については、次をご覧ください。

* [Microsoft Azure ログ統合](https://www.microsoft.com/download/details.aspx?id=53324) – Azure ログ統合の詳細情報、システム要件、およびインストール手順のダウンロード センター。
* [Azure ログ統合の使用](security-azure-log-integration-get-started.md) – このチュートリアルでは、Azure ログ統合のインストール、Azure WAD ストレージのログ、Azure アクティビティ ログ、Azure Security Center の警告、および Azure Active Directory の監査ログの統合について説明します。
* [パートナーの構成手順](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – このブログ投稿では、Splunk、HP ArcSight、IBM QRadar などのパートナー ソリューションを使用できるように、Azure ログ統合を構成する方法について説明します。 このブログでは、パートナー ソリューションの構成に関する Microsoft の現在の見解を紹介しています。 すべての場合において、まずパートナー ソリューションのドキュメントを参照してください。
* [Activity and ASC alerts over syslog to QRadar (QRadar に syslog 経由で送られるアクティビティ アラートと ASC アラート)](https://blogs.msdn.microsoft.com/azuresecurity/2016/09/24/integrate-azure-logs-to-qradar/)– このブログ投稿では、アクティビティ アラートと Azure Security Center アラートを syslog 経由で QRadar に送信する手順が示されています
* [Azure ログ統合のよく寄せられる質問 (FAQ)](security-azure-log-integration-faq.md) – この FAQ は、Azure ログ統合について寄せられる質問とその回答です。
* [Security Center の警告と Azure ログの統合](../security-center/security-center-integrating-alerts-with-log-integration.md) – このドキュメントでは、Azure Security Center の警告を Azure ログ統合と同期させる方法について説明しています。

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png

