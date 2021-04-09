---
title: アラートを Azure Security Center からセキュリティ情報イベント管理 (SIEM) システムとその他の監視ソリューションにストリーミングする
description: セキュリティ アラートを Azure Sentinel、サードパーティ製の SIEM、SOAR、または ITSM ソリューションにストリーミングする方法について説明します
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 10/06/2020
ms.author: memildin
ms.openlocfilehash: 3ddc385b9d489e0c2ab4abf35a6ade011970342b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100572954"
---
# <a name="stream-alerts-to-a-siem-soar-or-it-service-management-solution"></a>SIEM、SOAR、または IT サービス管理ソリューションにアラートをストリーミングする

Azure Security Center では、セキュリティ アラートを最も一般的なセキュリティ情報イベント管理 (SIEM)、セキュリティ オーケストレーション自動応答 (SOAR)、および IT サービス管理 (ITSM) ソリューションにストリーミングできます。

現在使用されている最も一般的なソリューション (以下が含まれます) のすべてでアラート データを表示できるようにする Azure ネイティブ ツールがあります。

- **Azure Sentinel**
- **Splunk Enterprise and Splunk Cloud**
- **IBM の QRadar**
- **ServiceNow**
- **ArcSight**
- **Power BI**
- **Palo Alto Networks**

## <a name="stream-alerts-to-azure-sentinel"></a>Azure Sentinel にアラートをストリーミングする 

Security Center には、Azure Sentinel (Azure のクラウドネイティブな SIEM および SOAR ソリューション) がネイティブに統合されています。 

[Azure Sentinel の詳細](../sentinel/overview.md)。

### <a name="azure-sentinels-connectors-for-security-center"></a>Security Center 用 Azure Sentinel のコネクタ

Azure Sentinel には、サブスクリプションとテナントのレベルで Azure Security Center 用の組み込みコネクタが含まれています。

- [サブスクリプション レベルで Azure Sentinel にアラートをストリーミングする](../sentinel/connect-azure-security-center.md)
- [テナント内のすべてのサブスクリプションを Azure Sentinel に接続する](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-security-center-auto-connect-to-sentinel/ba-p/1387539) 

### <a name="configure-ingestion-of-all-audit-logs-into-azure-sentinel"></a>すべての監査ログの Azure Sentinel への取り込みを構成する 

Azure Sentinel で Security Center アラートを調査する別の方法として、監査ログの Azure Sentinel へのストリーミングがあります。
    - [Windows セキュリティ イベントの接続](../sentinel/connect-windows-security-events.md)
    - [Syslog を使用して Linux ベースのソースからデータを収集する](../sentinel/connect-syslog.md)
    - [Azure アクティビティ ログからデータを接続する](../sentinel/connect-azure-activity.md)

> [!TIP]
> Azure Sentinel の課金は、分析のために Azure Sentinel に取り込まれ、Azure Monitor Log Analytics ワークスペースに格納されたデータの量に基づいて行われます。 Azure Sentinel には、柔軟で予測可能な価格モデルが用意されています。 [Azure Sentinel の価格ページを確認してください](https://azure.microsoft.com/pricing/details/azure-sentinel/)。


## <a name="stream-alerts-with-microsoft-graph-security-api"></a>Microsoft Graph Security API を使用してアラートをストリーミングする

Security Center は、Microsoft Graph Security API とすぐに統合できます。 構成は不要であり、追加のコストは発生しません。 

この API を使用して、**テナント全体** (およびその他の多くの Microsoft セキュリティ製品のデータ) から、サードパーティ製の SIEM と その他の一般的なプラットフォームにアラートをストリーミングできます。

- **Splunk Enterprise と Splunk Cloud** - [Splunk 用の Microsoft Graph Security API アドオンを使用します](https://splunkbase.splunk.com/app/4564/) 
- **Power BI** - [Power BI Desktop で Microsoft Graph Security API に接続します](/power-bi/connect-data/desktop-connect-graph-security)
- **ServiceNow** - [ServiceNow Store から Microsoft Graph Security API アプリケーションをインストールして構成する手順に従います](https://docs.servicenow.com/bundle/orlando-security-management/page/product/secops-integration-sir/secops-integration-ms-graph/task/ms-graph-install.html)
- **QRadar** - [Microsoft Graph API を介した Azure Security Center 用の IBM のデバイス サポート モジュール](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/c_dsm_guide_ms_azure_security_center_overview.html) 
- **Palo Alto Networks**、**Anomali**、**Lookout**、**InSpark** など - [Microsoft Graph Security API](https://www.microsoft.com/security/business/graph-security-api#office-MultiFeatureCarousel-09jr2ji)

[Microsoft Graph Security API の詳細](https://www.microsoft.com/security/business/graph-security-api)。


## <a name="stream-alerts-with-azure-monitor"></a>Azure Monitor を使用してアラートをストリーミングする 

**ArcSight**、**Splunk**、**SumoLogic**、Syslog servers、**LogRhythm**、**Logz.io Cloud Observability Platform**、およびその他の監視ソリューションにアラートをストリーミングするには、 Azure Event Hubs 経由で Security Center を Azure Monitor に接続します。

1. サブスクリプション レベルで Security Center アラートを専用の Azure イベント ハブにストリーミングするために、[連続エクスポート](continuous-export.md)を有効にします。 
    > [!TIP]
    > Azure Policy を使用して管理グループ レベルでこれを行うには、「[連続エクスポートの自動化の構成を大規模に作成する](continuous-export.md?tabs=azure-policy#configure-continuous-export-at-scale-using-the-supplied-policies)」を参照してください。

1. [Azure Monitor の組み込みコネクタを使用して、Azure イベント ハブを任意のソリューションに接続します](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)。

1. 必要に応じて、未加工のログを Azure イベント ハブにストリーミングし、任意のソリューションに接続します。 「[使用可能なデータの監視](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#monitoring-data-available)」で詳細を確認してください。

> [!TIP]
> エクスポートされたデータ型のイベント スキーマを表示するには、[イベント ハブのイベント スキーマ](https://aka.ms/ASCAutomationSchemas)にアクセスします。


## <a name="next-steps"></a>次のステップ

このページでは、選択した SIEM、SOAR、または ITSM ツールで Azure Security Center アラート データを使用できるようにする方法について説明しました。 関連資料については、以下を参照してください。

- [Azure Sentinel とは](../sentinel/overview.md)
- [Azure Security Center でのアラートの検証](security-center-alert-validation.md) - アラートが正しく構成されていることを確認します
- [継続的に Security Center データをエクスポートする](continuous-export.md)