---
title: "Azure Government で利用可能なサービス | Microsoft Docs"
description: "Azure Government で利用可能なサービスの概要を紹介します"
services: azure-government
cloud: gov
documentationcenter: 
author: smichelotti
manager: liki
ms.assetid: a453a23c-bc0f-4203-9075-0f579dea7e23
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: stemi
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 21c2a0faad87b84058093f02c831b374a644b4b6
ms.lasthandoff: 03/06/2017


---
# <a name="available-services-on-azure-government"></a>Azure Government で利用可能なサービス
Azure Government では、利用可能なサービスを継続的に拡大しています。  提供するサービスは、Azure Public と同じコードを使ってデプロイされます。  このセクションでは、Azure Government で現在利用可能なサービスについて、次の&2; つの主要な情報と共に説明します。

* **バリエーション:** 現時点ではまだデプロイされていない機能、または政府機関向けの環境に固有のプロパティ (URL など) によるバリエーションです。  
* **考慮事項:** データをコンプライアンスの管理境界内にとどめるために必要な政府機関特有の実装の詳細です。

ここに挙げたサービスに関してこれ以外に知っておく必要がある情報は、各サービスの一般向けドキュメントに記載されています。

サービスの最新の一覧については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」を参照してください。 

以下の表で「リソース マネージャーが有効」が「はい」になっている場合、そのサービスにはリソース プロバイダーがあり、PowerShell を使って管理することができます。 Resource Manager のプロバイダー、API のバージョン、およびスキーマの詳細については、[こちら](../azure-resource-manager/resource-manager-supported-services.md)を参照してください。 ポータルでの利用が可能なサービスは、[Azure Government ポータル](https://portal.azure.us/)で管理できます。 


## <a name="computedocumentation-government-computemd"></a>[Compute](documentation-government-compute.md)

| サービス | リソース マネージャーが有効 | ポータル |
| --- | --- | --- |
| [Virtual Machines](documentation-government-compute.md#virtual-machines) | はい | はい |
| Batch | はい | はい |
| Cloud Services | はい | はい |
| Service Fabric | はい | はい |
| VM スケール セット | はい | はい |


## <a name="networkingdocumentation-government-networkingmd"></a>[ネットワーク](documentation-government-networking.md)

| サービス | リソース マネージャーが有効 | ポータル |
| --- | --- | --- |
| [ExpressRoute](documentation-government-networking.md#expressroute-private-connectivity) | はい | はい |
| 仮想ネットワーク | はい | はい |
| [Load Balancer](documentation-government-networking.md#support-for-load-balancer) | はい | はい |
| [Traffic Manager](documentation-government-networking.md#support-for-traffic-manger) | はい | はい |
| [VPN Gateway](documentation-government-networking.md#support-for-vpn-gateway) | はい | はい |
| Application Gateway | はい | はい |
| ExpressRoute | はい | はい |



## <a name="storagedocumentation-government-services-storagemd"></a>[Storage](documentation-government-services-storage.md)

| サービス | リソース マネージャーが有効 | ポータル |
| --- | --- | --- |
| [Storage - BLOB](documentation-government-services-storage.md#azure-storage) | はい | はい |
| [Storage - テーブル](documentation-government-services-storage.md#azure-storage) | はい | はい |
| [Storage - キュー](documentation-government-services-storage.md#azure-storage) | はい | はい |
| [Storage - ファイル](documentation-government-services-storage.md#azure-storage) | はい | はい |
| [Storage - ディスク](documentation-government-services-storage.md#azure-storage) | はい | はい |
| [StorSimple](documentation-government-services-storage.md) | はい | はい |
| [Backup](documentation-government-services-storage.md#azure-storage) | はい | はい |
| [Site Recovery](documentation-government-services-storage.md#azure-storage) | はい | はい |
| [Import/Export](documentation-government-services-storage.md#azure-storage) | はい | なし |



## <a name="web--mobiledocumentation-government-services-webandmobilemd"></a>[Web + モバイル](documentation-government-services-webandmobile.md)

| サービス | リソース マネージャーが有効 | ポータル |
| --- | --- | --- |
| [App Service - Web Apps](documentation-government-services-webandmobile.md#app-services) | はい | はい |
| [App Service - API Apps](documentation-government-services-webandmobile.md#app-services) | はい | はい |
| [App Service - Mobile Apps](documentation-government-services-webandmobile.md#app-services) | はい | はい |
| Media Services | はい | はい |


## <a name="databasesdocumentation-government-services-databasemd"></a>[データベース](documentation-government-services-database.md)

| サービス | リソース マネージャーが有効 | ポータル |
| --- | --- | --- |
| [SQL Database](documentation-government-services-database.md#sql-database) | はい | はい |
| SQL Data Warehouse | はい | はい |
| SQL Server Stretch Database | はい | はい |
| [Redis Cache](documentation-government-services-database.md#azure-redis-cache) | はい | はい |


## <a name="intelligence--analyticsdocumentation-government-services-intelligenceandanalyticsmd"></a>[インテリジェンス + 分析](documentation-government-services-intelligenceandanalytics.md)

| サービス | リソース マネージャーが有効 | ポータル |
| --- | --- | --- |
| [HDInsights](documentation-government-services-intelligenceandanalytics.md#hdinsight) | はい | はい |
| [Power BI Pro](documentation-government-services-intelligenceandanalytics.md#power-bi) | いいえ | いいえ (Office 365 管理ポータル) |


## <a name="internet-of-things-iot"></a>モノのインターネット(IoT)

| サービス | リソース マネージャーが有効 | ポータル |
| --- | --- | --- |
| Event Hubs | はい | はい |
| Notification Hubs | いいえ | いいえ ([以前のポータル](https://manage.windowsazure.us/)に移動) |


## <a name="enterprise-integration"></a>エンタープライズ統合

| サービス | リソース マネージャーが有効 | ポータル |
| --- | --- | --- |
| Service Bus | はい | はい |
| [StorSimple](documentation-government-services-storage.md) | はい | はい |
| SQL Server Stretch Database | はい | はい |



## <a name="security--identitydocumentation-government-services-securityandidentitymd"></a>[セキュリティ + ID](documentation-government-services-securityandidentity.md)

| サービス | リソース マネージャーが有効 | ポータル |
| --- | --- | --- |
| Azure Active Directory | はい | はい |
| [Key Vault](documentation-government-services-securityandidentity.md#key-vault) | はい | なし (近日対応予定) |
| Multi-Factor Authentication | はい | はい |


## <a name="intelligence--analytics"></a>インテリジェンス + 分析

| サービス | リソース マネージャーが有効 | ポータル |
| --- | --- | --- |
| Power BI | はい | いいえ |
| HDInsight | はい | はい |



## <a name="monitoring--managementdocumentation-government-services-monitoringandmanagementmd"></a>[監視 + 管理](documentation-government-services-monitoringandmanagement.md)

| サービス | リソース マネージャーが有効 | ポータル |
| --- | --- | --- |
| [Automation](documentation-government-services-monitoringandmanagement.md#automation) | はい | はい |
| [Backup](documentation-government-services-backup.md) | はい | はい |
| [Log Analytics](documentation-government-services-monitoringandmanagement.md#log-analytics) | はい | はい |
| [Site Recovery](documentation-government-services-monitoringandmanagement.md#site-recovery) | はい | はい |
| Scheduler | はい | なし |
| Monitoring and Diagnostics | はい | はい |




## <a name="next-steps"></a>次のステップ
補足情報と最新情報については、[Microsoft Azure Government のブログ](https://blogs.msdn.microsoft.com/azuregov/)をご覧ください。


