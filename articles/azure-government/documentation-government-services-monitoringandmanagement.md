---
title: "Azure Government の監視と管理 | Microsoft Docs"
description: "Azure Government アプリケーションの機能の比較と開発におけるガイダンスを示します。"
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 3/13/2017
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 4d7de786dc902cb1c32e70a1f69bc74282de44f1
ms.lasthandoff: 03/14/2017


---
# <a name="azure-government-monitoring--management"></a>Azure Government の監視と管理
この記事では、Azure Government 環境での監視と管理に関するサービスのバリエーションと、考慮事項の概要を紹介します。

## <a name="automation"></a>Automation
Automation は、Azure Government で一般提供されています。

### <a name="variations"></a>バリエーション
Automation の機能のうち以下のものは、Azure Government では現在使用できません。

* 認証するためのサービス プリンシパル資格情報の作成

詳細については、[Automation のパブリック ドキュメント](../automation/automation-intro.md)をご覧ください。

## <a name="backup"></a>バックアップ
バックアップは、Azure Government で一般提供されています。

詳細については、「[Azure Government Backup (Azure Government のバックアップ)](documentation-government-services-backup.md)」を参照してください。

## <a name="resource-policy"></a>リソース ポリシー

[Azure リソース ポリシー](../azure-resource-manager/resource-manager-policy.md) は、Azure Government では利用できません。

## <a name="site-recovery"></a>Site Recovery
Azure Site Recovery は、Azure Government で一般提供されています。

詳細については、[Site Recovery のパブリック ドキュメント](../site-recovery/site-recovery-overview.md)をご覧ください。

### <a name="variations"></a>バリエーション
Site Recovery の機能のうち以下のものは、Azure Government では現在使用できません。

* Azure Resource Manager の Site Recovery コンテナー
* 電子メール通知

| Site Recovery | クラシック | リソース マネージャー |
| --- | --- | --- |
| VMware/物理  | 一般公開 | 一般公開 |
| Hyper-V | 一般公開 | 一般公開 |
| サイト間 | 一般公開 | 一般公開 |

>[!NOTE]
>この表は、米国政府バージニアおよび米国政府アイオワに適用されます。

Azure Government では、Site Recovery の次の URL が異なります。

| Azure Public | Azure Government | メモ |
| --- | --- | --- |
| \*.hypervrecoverymanager.windowsazure.com | \*.hypervrecoverymanager.windowsazure.us | Site Recovery サービスへのアクセス |
| \*.backup.windowsazure.com  | \*.backup.windowsazure.us | 保護サービスへのアクセス |
| \*.blob.core.windows.net | \*.blob.core.usgovcloudapi.net | VM スナップショットの格納用 |
| http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi | http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi | MySQL のダウンロード用 |

## <a name="log-analytics"></a>Log Analytics
Log Analytics は、Azure Government で一般提供されています。

### <a name="variations"></a>バリエーション
次の Log Analytics の機能とソリューションは、Azure Government では現在使用できません。

* Microsoft Azure のプレビュー段階のソリューション。これには以下が含まれます。
  * ネットワーク監視ソリューション
  * サービス マップ
  * Office 365 ソリューション
  * Windows 10 アップグレード分析ソリューション
  * Application Insights ソリューション
  * Azure Networking Analytics ソリューション
  * Azure Automation Analytics ソリューション
  * Key Vault Analytics ソリューション
* オンプレミスのソフトウェアを更新する必要のあるソリューションと機能。これには以下が含まれます。
  * Surface Hub ソリューション
* パブリック Azure のプレビュー段階の機能。これには以下が含まれます。
  * Power BI へのデータのエクスポート
* Azure メトリックと Azure 診断
* Operations Management Suite のモバイル アプリケーション

Azure Government では、Log Analytics の URL が異なります。

| Azure Public | Azure Government | メモ |
| --- | --- | --- |
| mms.microsoft.com |oms.microsoft.us |Log Analytics ポータル |
| *workspaceId*.ods.opinsights.azure.com |*workspaceId*.ods.opinsights.azure.us |[データ コレクター API](../log-analytics/log-analytics-data-collector-api.md) |
| \*.ods.opinsights.azure.com |\*.ods.opinsights.azure.us |エージェント通信 - [ファイアウォール設定の構成](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.oms.opinsights.azure.com |\*.oms.opinsights.azure.us |エージェント通信 - [ファイアウォール設定の構成](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.blob.core.windows.net |\*.blob.core.usgovcloudapi.net |エージェント通信 - [ファイアウォール設定の構成](../log-analytics/log-analytics-proxy-firewall.md) |

次の Log Analytics 機能は、Azure Government では動作が異なります。

* System Center Operations Manager 管理サーバーを Log Analytics に接続するには、更新された管理パックをダウンロードしてインポートする必要があります。
  + System Center Operations Manager 2016
    1. [Update Rollup 2 for System Center Operations Manager 2016](https://support.microsoft.com/help/3209591) をインストールします。
    2. Update Rollup 2 の一部として含まれている管理パックを、Operations Manager にインポートします。 ディスクから管理パックをインポートする方法については、「[Operations Manager 管理パックのインポート方法](http://technet.microsoft.com/library/hh212691.aspx)」をご覧ください。
    3. Operations Manager を Log Analytics に接続するには、「[Operations Manager を Log Analytics に接続する](../log-analytics/log-analytics-om-agents.md)」の手順に従ってください。
  + System Center Operations Manager 2012 R2 UR3 (またはそれ以降) / Operations Manager 2012 SP1 UR7 (またはそれ以降)
    1. [更新された管理パック](http://go.microsoft.com/fwlink/?LinkId=828749)をダウンロードして保存します。
    2. ダウンロードしたファイルを解凍します。
    3. 管理パックを Operations Manager にインポートします。 ディスクから管理パックをインポートする方法については、「[Operations Manager 管理パックのインポート方法](http://technet.microsoft.com/library/hh212691.aspx)」をご覧ください。
    4. Operations Manager を Log Analytics に接続するには、「[Operations Manager を Log Analytics に接続する](../log-analytics/log-analytics-om-agents.md)」の手順に従ってください。
  
* [System Center Configuration Manager 2016 のコンピューター グループ](../log-analytics/log-analytics-sccm.md)を使用するには、[テクニカル プレビュー 1701](https://docs.microsoft.com/en-us/sccm/core/get-started/technical-preview)またはそれ以降を使用する必要があります。

### <a name="frequently-asked-questions"></a>よく寄せられる質問
* Microsoft Azure の Log Analytics のデータを Azure Government に移行できますか。
  * いいえ。 Microsoft Azure のデータやワークスペースを Azure Government に移動することはできません。
* Operations Management Suite Log Analytics ポータルから、Microsoft Azure と Azure Government のワークスペースを切り替えることはできますか。
  * いいえ。 Microsoft Azure と Azure Government のポータルは別個であり、情報を共有することはできません。

詳細については、 [Log Analytics のパブリック ドキュメント](../log-analytics/log-analytics-overview.md)をご覧ください。

## <a name="next-steps"></a>次のステップ
補足情報と最新情報については、<a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government ブログ</a>を講読してください。

