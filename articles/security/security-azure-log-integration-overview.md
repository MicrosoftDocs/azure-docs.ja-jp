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
ms.date: 03/03/2017
ms.author: TomSh
translationtype: Human Translation
ms.sourcegitcommit: 2c9877f84873c825f96b62b492f49d1733e6c64e
ms.openlocfilehash: 434961f7d25195eee3310461a3a97e03ffdd8917
ms.lasthandoff: 03/15/2017


---
# <a name="introduction-to-microsoft-azure-log-integration"></a>Microsoft Azure ログ統合の概要
Azure ログ統合と、その主な機能およびしくみについて紹介します。

## <a name="overview"></a>Overview
Azure でホストされるサービスとしてのプラットフォーム (PaaS) とサービスとしてのインフラストラクチャ (IaaS) によって、大量のデータがセキュリティ ログに生成されます。 このログには、ポリシー違反、内部および外部の脅威、法規制遵守に関する問題、および異常なネットワーク、ホスト、ユーザー アクティビティに関する強力な洞察、およびインテリジェンスを提供する重要な情報が含まれます。

Azure ログ統合は無料のソリューションで、未加工のログを、Azure リソースからオンプレミスのセキュリティ情報/イベント管理 (SIEM) システムに統合できるようにします。 Azure ログ統合では、Windows *(WAD)* 仮想マシン、Azure アクティビティ ログ、Azure Security Center の警告、および Azure リソース プロバイダーのログから Azure 診断を収集します。 この統合は、すべての資産に対してオンプレミスまたはクラウドの統合ダッシュボードを提供します。これによりセキュリティ イベントの集計、関連付け、分析を実行し、警告を生成できます。

![Azure ログ統合][1]

## <a name="what-logs-can-i-integrate"></a>統合できるログ
Azure サービスでは、すべてのサービスの広範なログ記録を作成します。 これらのログは、主に次のタイプに分類されます。

* **コントロール/管理ログ**は、Azure Resource Manager の CREATE、UPDATE、および DELETE 操作の可視性を提供します。 Azure アクティビティ ログは、このタイプのログです。
* **データ プレーン ログ**は、Azure リソース使用の一環として発生するイベントの可視性を提供します。 仮想マシンの Windows イベント システム ログ、セキュリティ ログ、アプリケーション ログや、Azure Monitor によって構成される診断ログは、このタイプのログです。
* **処理済みイベント**は、ユーザーに代わって処理された分析済みのイベント/アラートを提供します。Azure Security Center がユーザーのサブスクリプションを処理して分析し、非常に簡潔なセキュリティ アラートを提供する Azure Security Center アラートは、このタイプのログです。

現在、Azure ログ統合は、Azure アクティビティ ログ、Azure サブスクリプション内の Windows 仮想マシンからの Windows イベント ログ、Azure Security Center アラート、Azure 診断ログ、および Azure Active Directory 監査ログの統合をサポートします。

次の表では、ログのカテゴリと SIEM 統合の詳細について説明します。

| ログのタイプ  |JSON (Splunk、ELK) をサポートする Log Analytics| ArcSight  | QRadar  |   
|---|---|---|---|
|  AAD 監査ログ |  ○ | FlexConnector JSON パーサー ファイルの作成が必要です。 詳しくは、ArcSight のドキュメントをご覧ください。  |  ログ ソース拡張機能を作成する必要があります。 詳しくは、QRadar のドキュメントをご覧ください。 |  
| アクティビティ ログ  | はい  |  ダウンロード センターおよび Azure ログ統合ダウンロードで入手可能な FlexConnector JSON パーサー ファイル |  [QRadar DSM](https://www.ibm.com/support/knowledgecenter/SSKMKU/com.ibm.dsm.doc/c_dsm_guide_microsoft_azure_overview.html) (Syslog 経由で送信) |  
| ASC アラート  | はい  |  FlexConnector JSON パーサー ファイルの作成が必要です。 詳しくは、ArcSight のドキュメントをご覧ください。 | [QRadar DSM](https://www.ibm.com/support/knowledgecenter/SSKMKU/com.ibm.dsm.doc/c_dsm_guide_microsoft_azure_overview.html) (Syslog 経由で送信)   |   
| 診断ログ (リソース ログ) | はい | エンド ユーザーは FlexConnector JSON パーサー ファイルを作成する必要があります。 方法については ArcSight のドキュメントをご覧ください。 | ログ ソース拡張機能を作成する必要があります。 詳しくは、QRadar のドキュメントをご覧ください。 |
| VM ログ | はい (JSON 経由ではなく転送されたイベントの場合) | はい (転送されたイベントの場合) | はい (転送されたイベントの場合) |

Azure ログ統合は無料のソリューションで、Azure ログ統合バイナリに対して料金を支払う必要はありません。 ただし、ログ ファイル情報に必要な Azure Storage に関連するコストがあります。

Azure ログ統合に関する質問がある場合は、 [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Azure ログ統合について紹介しました。 Azure ログ統合と、サポートされているログのタイプの詳細については、次をご覧ください。

* [Azure ログ用の Microsoft Azure ログ統合](https://www.microsoft.com/download/details.aspx?id=53324) – Azure ログ統合の詳細情報、システム要件、およびインストール手順のダウンロード センター。
* [Azure ログ統合の使用](security-azure-log-integration-get-started.md) – このチュートリアルでは、Azure ログ統合のインストール、Azure WAD ストレージのログ、Azure アクティビティ ログ、Azure Security Center の警告、および Azure Active Directory の監査ログの統合について説明します。
* [Integrate Diagnostics Logs (診断ログの統合)](https://blogs.msdn.microsoft.com/azuresecurity/2016/09/25/integrate-azure-logs-streamed-to-event-hubs-to-siem/) – このブログ投稿では、Azure ログ統合を使って診断ログを統合する手順が示されています
* [パートナーの構成手順](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – このブログ投稿では、Splunk、HP ArcSight、IBM QRadar などのパートナー ソリューションを使用できるように、Azure ログ統合を構成する方法について説明します。
* [Activity and ASC alerts over syslog to QRadar (QRadar に syslog 経由で送られるアクティビティ アラートと ASC アラート)](https://blogs.msdn.microsoft.com/azuresecurity/2016/09/24/integrate-azure-logs-to-qradar/)– このブログ投稿では、アクティビティ アラートと Azure Security Center アラートを syslog 経由で QRadar に送信する手順が示されています
* [Azure ログ統合のよく寄せられる質問 (FAQ)](security-azure-log-integration-faq.md) – この FAQ は、Azure ログ統合について寄せられる質問とその回答です。
* [Azure ログ統合への Security Center の警告の統合](../security-center/security-center-integrating-alerts-with-log-integration.md) – このドキュメントでは、Azure Security Center の警告を、Azure 診断および Azure 監査ログによって収集された仮想マシンのセキュリティ イベントとともに、ログ分析または SIEM ソリューションと同期させる方法について説明します。

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png

