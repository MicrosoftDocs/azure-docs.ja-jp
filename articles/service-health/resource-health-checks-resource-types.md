---
title: Azure Resource Health でサポートされるリソースの種類 |Microsoft Docs
description: Azure Resource Health でサポートされるリソースの種類
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: 1b0958732a96786b2fb86b2b145f5cb8d36c0c5a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97883369"
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Azure Resource Health で利用できるリソースの種類と正常性チェック
Resource Health で実行されるすべてのチェックをリソースの種類別にまとめた完全な一覧を次に示します。

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers
|実行されるチェック|
|---|
|<ul><li>サーバーが稼働しているか</li><li>サーバーのメモリが不足しているか</li><li>サーバーが起動しているか</li><li>サーバーが復旧しているか</li></ul>|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|実行されるチェック|
|---|
|<ul><li>API Management サービスが実行しているか</li></ul>|

## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring
|実行されるチェック|
|---|
|<ul><li>Azure Spring Cloud インスタンスを使用できるか</li></ul>|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts
|実行されるチェック|
|---|
|<ul><li>Batch アカウントが稼働しているか</li><li>この Batch アカウントのプール クォータを超過しているか</li></ul>|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/Redis
|実行されるチェック|
|---|
|<ul><li>すべてのキャッシュ ノードが稼働しているか</li><li>データセンターからキャッシュにアクセスできるか</li><li>キャッシュが接続の最大数に達しているか</li><li> キャッシュが使用可能なメモリを使い果たしているか </li><li>キャッシュで多数のページ フォールトが発生しているか</li><li>キャッシュに大きな負荷がかかっているか</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft.CDN/profile
|実行されるチェック|
|---|
|<ul> <li>CDN 構成操作で補助ポータルにアクセスできるか</li><li>CDN エンドポイントに進行中の配信に関する問題があるか</li><li>ユーザーが CDN リソースの構成を変更できるか</li><li>構成の変更が期待されるレートで反映されるか</li><li>ユーザーが Azure Portal、PowerShell、または API を使用して CDN 構成を管理できるか</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classiccompute/virtualmachines
|実行されるチェック|
|---|
|<ul><li>ホスト サーバーが稼働しているか</li><li>ホスト OS の起動が完了しているか</li><li>仮想マシン コンテナーがプロビジョニングされ、オンになっているか</li><li>ホストとストレージ アカウント間のネットワーク接続が存在するか</li><li>ゲスト OS の起動が完了しているか</li><li>進行中の定期的なメンテナンスはあるか</li><li>ホストのハードウェアの機能が低下し、すぐに障害が起きると予測されるか</li></ul>|

## <a name="microsoftclassiccomputedomainnames"></a>Microsoft.classiccompute/domainnames
|実行されるチェック|
|---|
|<ul><li>運用スロットのデプロイがすべてのロール インスタンスで正常に実行されているかどうか</li><li>すべての VM インスタンスでロールが正常であるかどうか</li><li>クラウド サービスのロール内の各 VM の正常性状態はどうなっているか</li><li>プラットフォームまたは顧客が開始した操作により、VM の状態が変化したかどうか</li><li>ゲスト OS の起動が完了しているか</li><li>進行中の定期的なメンテナンスはあるか</li><li>ホストのハードウェアの機能が低下し、すぐに障害が起きると予測されるか</li><li>実行されるチェックの[詳細](../cloud-services/resource-health-for-cloud-services.md)</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.cognitiveservices/accounts
|実行されるチェック|
|---|
|<ul><li>データセンターからアカウントにアクセスできるか</li><li>Cognitive Services リソース プロバイダーを利用できるか</li><li>Cognitive Services が適切な地域で利用できるか</li><li>リソースのメタデータを保持しているストレージ アカウントで読み取り操作を実行できるか</li><li>API 呼び出しのクォータに達しているか</li><li>API 呼び出しの読み取り制限に達しているか</li></ul>|

## <a name="microsoftcomputehostgroupshosts"></a>Microsoft.compute/hostgroups/hosts
|実行されるチェック|
|---|
|<ul><li>ホストが稼働しているか</li><li>ホストのハードウェアの機能が低下しているかどうか?</li><li>ホストの割り当てが解除されているかどうか?</li><li>ホストのハードウェア サービスが異なるハードウェアに復旧しているかどうか?</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.compute/virtualmachines
|実行されるチェック|
|---|
|<ul><li>この仮想マシンをホストするサーバーは稼働しているか</li><li>ホスト OS の起動が完了しているか</li><li>仮想マシン コンテナーがプロビジョニングされ、オンになっているか</li><li>ホストとストレージ アカウント間のネットワーク接続が存在するか</li><li>ゲスト OS の起動が完了しているか</li><li>進行中の定期的なメンテナンスはあるか</li><li>ホストのハードウェアの機能が低下し、すぐに障害が起きると予測されるか</li></ul>|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters
|実行されるチェック|
|---|
|<ul><li>クラスターが稼働しているか</li><li>コア サービスはクラスターで使用できるか</li><li>すべてのクラスター ノードの準備ができているか</li><li>サービス プリンシパルは最新の状態で有効であるか</li></ul>|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.datafactory/factories
|実行されるチェック|
|---|
|<ul><li>パイプラインの実行エラーがあるか</li><li>Data Factory をホストしているクラスターは正常か</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.datalakeanalytics/accounts
|実行されるチェック|
|---|
|<ul><li>ユーザーに Data Lake Analytics ジョブの送信またはリストに関する問題が発生しているか</li><li>システム エラーが原因で Data Lake Analytics ジョブを完了できないか</li></ul>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.datalakestore/accounts
|実行されるチェック|
|---|
|<ul><li>ユーザーに Data Lake Store へのデータのアップロードに関する問題が発生しているか</li><li>ユーザーに Data Lake Store からのデータのダウンロードに関する問題が発生しているか</li></ul>|

## <a name="microsoftdatamigrationservices"></a>Microsoft.datamigration/services
|実行されるチェック|
|---|
|<ul><li>データベース移行サービスのプロビジョニングに失敗したか</li><li>非アクティブであることまたはユーザー要求が原因でデータベース移行サービスが停止したか</li></ul>|

## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/accounts
|実行されるチェック|
|---|
|<ul><li>Data Share アカウントが稼働しているか</li><li>Data Share をホストしているクラスターを利用できるか</li></ul>|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers
|実行されるチェック|
|---|
|<ul><li>メンテナンスのためにサーバーが利用不可状態になっているか</li><li>再構成のためにサーバーが利用不可状態になっているか</li></ul>|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers
|実行されるチェック|
|---|
|<ul><li>メンテナンスのためにサーバーが利用不可状態になっているか</li><li>再構成のためにサーバーが利用不可状態になっているか</li></ul>|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers
|実行されるチェック|
|---|
|<ul><li>メンテナンスのためにサーバーが利用不可状態になっているか</li><li>再構成のためにサーバーが利用不可状態になっているか</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.devices/iothubs
|実行されるチェック|
|---|
|<ul><li>IoT ハブが稼働しているか</li></ul>|

## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Microsoft.DigitalTwins/DigitalTwinsInstances
|実行されるチェック|
|---|
|<ul><li>Azure Digital Twins インスタンスは稼働しているか</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseAccounts
|実行されるチェック|
|---|
|<ul><li>Azure Cosmos DB サービスが利用できないために処理されていないデータベースまたはコレクションの要求があるか</li><li>Azure Cosmos DB サービスが利用できないために処理されていないドキュメントの要求があるか</li></ul>|

## <a name="microsofteventhubnamespaces"></a>Microsoft.eventhub/namespaces
|実行されるチェック|
|---|
|<ul><li>Event Hubs 名前空間でユーザーによって生成されたエラーが発生しているか</li><li>Event Hubs 名前空間は現在アップグレード中か</li></ul>|

## <a name="microsofthdinsightclusters"></a>Microsoft.hdinsight/clusters
|実行されるチェック|
|---|
|<ul><li>コア サービスは HDInsight クラスターで使用できるか</li><li>HDInsight クラスターは保存中の BYOK 暗号化のキーにアクセスできるか</li></ul>|

## <a name="microsoftiotcentraliotapps"></a>Microsoft.IoTCentral/IoTApps
|実行されるチェック|
|---|
|<ul><li>IoT Central アプリケーションを使用できるか</li></ul>|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults
|実行されるチェック|
|---|
|<ul><li>Azure KeyVault プラットフォームの問題が原因でキー コンテナーへの要求が失敗するか</li><li>顧客によって行われた要求が多すぎるためにキー コンテナーへの要求がスロットルされているか</li></ul>|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/clusters
|実行されるチェック|
|---|
|<ul><li>クラスターのインジェストの成功率が低いか</li><li>クラスターのインジェストの待ち時間が長いか</li><li>クラスターに大量のクエリ エラーが発生しているか</li></ul>|

## <a name="microsoftmachinelearningwebservices"></a>Microsoft.MachineLearning/webServices
|実行されるチェック|
|---|
|<ul><li>Web サービスが稼働しているか</li></ul>|

## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices
|実行されるチェック|
|---|
|<ul><li>メディア サービスが稼働しているか</li></ul>|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.network/applicationgateways
|実行されるチェック|
|---|
|<ul><li>Application Gateway のパフォーマンスが低下しているか</li><li>Application Gateway を利用できるか</li></ul>|

## <a name="microsoftnetworkbastionhosts"></a>Microsoft.network/bastionhosts
|実行されるチェック|
|---|
|<ul><li>bastion ホストが稼働しているか</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft.network/connections
|実行されるチェック|
|---|
|<ul><li>VPN トンネルが接続されているか</li><li>接続に構成の競合があるか</li><li>事前共有キーは正しく構成されているか</li><li>VPN でオンプレミスのデバイスにアクセスできるか</li><li>IPSec/IKE セキュリティ ポリシーに不一致があるか</li><li>S2S VPN 接続が正しくプロビジョニングされているか、障害が発生した状態にあるか</li><li>VNET-to-VNET 接続が正しくプロビジョニングされているか、障害が発生した状態にあるか</li></ul>|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.network/expressroutecircuits
|実行されるチェック|
|---|
|<ul><li>ExpressRoute 回線は正常か</li></ul>|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.network/frontdoors
|実行されるチェック|
|---|
|<ul><li>Front Door バックエンドが正常性プローブに対してエラーを返しているか</li><li>構成変更が遅延しているか</li></ul>|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.network/LoadBalancers
|実行されるチェック|
|---|
|<ul><li>負荷分散エンドポイントを利用できるか</li></ul>|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.network/trafficmanagerprofiles
|実行されるチェック|
|---|
|<ul><li>Traffic Manager プロファイルに影響する問題があるか</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.network/virtualNetworkGateways
|実行されるチェック|
|---|
|<ul><li>インターネットから VPN Gateway にアクセスできるか</li><li>VPN Gateway がスタンバイ モードか</li><li>VPN サービスがゲートウェイで実行されているか</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft.NotificationHubs/namespace
|実行されるチェック|
|---|
|<ul><li>名前空間で、登録、インストール、送信などのランタイム操作を実行できるか</li></ul>|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.operationalinsights/workspaces
|実行されるチェック|
|---|
|<ul><li>ワークスペースのインデックス付けの遅延があるか</li></ul>|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/Capacities
|実行されるチェック|
|---|
|<ul><li>キャパシティ リソースが稼働しているか</li><li>すべてのワークロードが稼働しているか</li></ul>

## <a name="microsoftsearchsearchservices"></a>Microsoft.search/searchServices
|実行されるチェック|
|---|
|<ul><li>クラスターで診断操作を実行できるか</li></ul>|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces
|実行されるチェック|
|---|
|<ul><li>ユーザーによって生成された Service Bus エラーをお客様が経験しているか</li><li>Service Bus 名前空間のアップグレードが原因で、ユーザーが一時的なエラーの増加を経験しているか</li></ul>|

## <a name="microsoftservicefabricclusters"></a>Microsoft.ServiceFabric/clusters
|実行されるチェック|
|---|
|<ul><li>Service Fabric クラスターは稼働しているか</li><li>Service Fabric クラスターは、Azure Resource Manager を使用して管理できるか</li></ul>|

## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft.SQL/managedInstances/databases
|実行されるチェック|
|---|
|<ul><li>データベースが稼働しているか</li></ul>|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.SQL/servers/databases
|実行されるチェック|
|---|
|<ul><li>データベースにログインしたことがあるか</li></ul>|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts
|実行されるチェック|
|---|
|<ul><li>Azure Storage プラットフォームの問題が原因で、Storage アカウントからのデータの読み取り要求が失敗しているか</li><li>Azure Storage プラットフォームの問題が原因で、Storage アカウントへのデータの書き込み要求が失敗しているか</li><li>Storage アカウントが存在する Storage クラスターが利用不可になっているか</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs
|実行されるチェック|
|---|
|<ul><li>ジョブが実行されるホストがすべて稼働しているか</li><li>ジョブを開始できなかったか</li><li>進行中のランタイムのアップグレードはあるか</li><li>ジョブが期待されている状態になっているか (たとえば、顧客によって実行中または停止されている)</li><li>ジョブでメモリ不足例外が発生しているか</li><li>スケジュールされたコンピューティングの進行中の更新はあるか</li><li>実行マネージャー (コントロール プラン) を利用できるか</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft.web/serverFarms
|実行されるチェック|
|---|
|<ul><li>ホスト サーバーが稼働しているか</li><li>インターネット インフォメーション サービスが実行されているか</li><li>ロード バランサーが実行されているか</li><li>データセンターから App Service プランにアクセスできるか</li><li>serverFarm のサイト コンテンツをホストしているストレージ アカウントを利用できるか</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft.web/sites
|実行されるチェック|
|---|
|<ul><li>ホスト サーバーが稼働しているか</li><li>インターネット インフォメーション サーバーが実行されているか</li><li>ロード バランサーが実行されているか</li><li>データセンターから Web アプリにアクセスできるか</li><li>サイト コンテンツをホストしているストレージ アカウントを利用できるか</li></ul>|

## <a name="next-steps"></a>次の手順
-  [Azure Service Health ダッシュボードの概要](service-health-overview.md)に関するページと [Azure Resource Health の概要](resource-health-overview.md)に関するページを読んで、それらについての理解を深めます。 
-  [Azure Resource Health に関してよく寄せられる質問](resource-health-faq.md)
- アラートをセットアップして、正常性に関する問題について通知を受け取ります。 詳細については、[サービス正常性イベントのアラートの構成](./alerts-activity-log-service-notifications-portal.md)に関するページを参照してください。