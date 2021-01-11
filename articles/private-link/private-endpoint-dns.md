---
title: Azure プライベート エンドポイントの DNS 構成
description: Azure プライベート エンドポイントの DNS 構成について説明します
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: allensu
ms.openlocfilehash: 5657741a1496084b55d2f76aef12c5e84c274feb
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88918130"
---
# <a name="azure-private-endpoint-dns-configuration"></a>Azure プライベート エンドポイントの DNS 構成


接続文字列の一部として完全修飾ドメイン名 (FQDN) を使用してプライベート リンク リソースに接続する場合は、割り当てられたプライベート IP アドレスに解決されるように DNS 設定を正しく構成することが重要です。 既存の Microsoft Azure サービスには、パブリック エンドポイント経由で接続するときに使用する DNS 構成が既に存在している場合があります。 プライベート エンドポイントを使用して接続するには、この構成をオーバーライドする必要があります。 
 
プライベート エンドポイントに関連付けられたネットワーク インターフェイスには、特定のプライベート リンク リソースに割り当てられた FQDN やプライベート IP アドレスなど、DNS を構成するために必要な情報の完全なセットが含まれています。 
 
次のオプションを使用して、プライベート エンドポイントの DNS 設定を構成できます。 
- **ホスト ファイルを使用する (テストにのみ推奨)** 。 仮想マシン上のホスト ファイルを使用して、DNS をオーバーライドすることができます。  
- **プライベート DNS ゾーンを使用する**。 [プライベート DNS ゾーン](../dns/private-dns-privatednszone.md)を使用して、特定のプライベート エンドポイントの DNS 解決をオーバーライドすることができます。 プライベート DNS ゾーンを自分の仮想ネットワークにリンクして、特定のドメインを解決することができます。
- **DNS フォワーダーを使用する (オプション)** 。 DNS フォワーダーを使用して、特定のプライベート リンク リソースの DNS 解決をオーバーライドすることができます。 お使いの [DNS サーバー](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)が仮想ネットワーク上にホストされている場合は、プライベート DNS ゾーンを使用する DNS 転送規則を作成して、すべてのプライベート リンク リソースの構成を簡略化することができます。
 
> [!IMPORTANT]
> パブリック エンドポイントを解決する目的でアクティブに使用されているゾーンをオーバーライドすることはお勧めしません。 リソースへの接続は、パブリック DNS への DNS 転送なしでは正しく解決できません。 問題を回避するには、別のドメイン名を作成するか、次の各サービスの推奨される名前に従ってください。 

## <a name="azure-services-dns-zone-configuration"></a>Azure サービス DNS ゾーンの構成
提案されたプライベート ドメイン名に解決をリダイレクトするために、Azure サービスによってパブリック DNS サービスに正規名の DNS レコード (CNAME) が作成されます。 この解決は、プライベート エンドポイントのプライベート IP アドレスでオーバーライドすることができます。 
 
アプリケーションで接続 URL を変更する必要はありません。 パブリック DNS サービスを使用して解決を試行すると、DNS サーバーによってプライベート エンドポイントに対する解決が行われます。 このプロセスは既存のアプリケーションには影響しません。 

> [!IMPORTANT]
> 特定の種類にプライベート DNS ゾーンを既に使用しているプライベート ネットワークは、プライベート エンドポイント接続が与えられていない場合、パブリック リソースにのみ接続できます。それ以外の場合、DNS 解決シーケンスを完了するには、プライベート DNS ゾーンに、対応する DNS 構成が必要になります。 

Azure サービスについては、次の表に示すように、推奨されるゾーン名を使用します。

| プライベート リンク リソースの種類/サブリソース |プライベート DNS ゾーンの名前 | パブリック DNS ゾーンのフォワーダー |
|---|---|---|---|
| Azure Automation (Microsoft.Automation/automationAccounts)/Webhook、DSCAndHybridWorker | privatelink.azure-automation.net | azure-automation.net |
| Azure SQL Database (Microsoft.Sql/servers)/SQL Server | privatelink.database.windows.net | database.windows.net |
| Azure Synapse Analytics (Microsoft.Sql/servers)/SQL Server  | privatelink.database.windows.net | database.windows.net |
| ストレージ アカウント (Microsoft.Storage/storageAccounts)/BLOB (blob、blob_secondary) | privatelink.blob.core.windows.net | blob.core.windows.net |
| ストレージ アカウント (Microsoft.Storage/storageAccounts)/テーブル (table、table_secondary) | privatelink.table.core.windows.net | table.core.windows.net |
| ストレージ アカウント (Microsoft.Storage/storageAccounts)/キュー (queue、queue_secondary) | privatelink.queue.core.windows.net | queue.core.windows.net |
| ストレージ アカウント (Microsoft.Storage/storageAccounts)/ファイル (file、file_secondary) | privatelink.file.core.windows.net | file.core.windows.net |
| ストレージ アカウント (Microsoft.Storage/storageAccounts)/Web (web、web_secondary) | privatelink.web.core.windows.net | web.core.windows.net |
| Azure Data Lake ファイル システム Gen2 (Microsoft.Storage/storageAccounts)/Data Lake ファイル システム Gen2 (dfs、dfs_secondary) | privatelink.dfs.core.windows.net | dfs.core.windows.net |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)/SQL | privatelink.documents.azure.com | documents.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)/MongoDB | privatelink.mongo.cosmos.azure.com | mongo.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)/Cassandra | privatelink.cassandra.cosmos.azure.com | cassandra.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)/Gremlin | privatelink.gremlin.cosmos.azure.com | gremlin.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)/テーブル | privatelink.table.cosmos.azure.com | table.cosmos.azure.com |
| Azure Database for PostgreSQL - シングル サーバー (Microsoft DBforPostgreSQL/servers)/postgresqlServer | privatelink.postgres.database.azure.com | postgres.database.azure.com |
| Azure Database for MySQL (Microsoft.DBforMySQL/servers)/mysqlServer | privatelink.mysql.database.azure.com | mysql.database.azure.com |
| Azure Database for MariaDB (Microsoft.DBforMariaDB/servers)/mariadbServer | privatelink.mariadb.database.azure.com | mariadb.database.azure.com |
| Azure Key Vault (Microsoft.KeyVault/vaults)/コンテナー | privatelink.vaultcore.azure.net | vault.azure.net <br> vaultcore.azure.net |
| Azure Kubernetes Service - Kubernetes API (Microsoft.ContainerService/managedClusters)/管理 | privatelink.{region}.azmk8s.io | {region}.azmk8s.io |
| Azure Search (Microsoft.Search/searchServices)/searchService | privatelink.search.windows.net | search.windows.net |
| Azure Container Registry (Microsoft.ContainerRegistry/registries)/レジストリ | privatelink.azurecr.io | azurecr.io |
| Azure App Configuration (Microsoft.Appconfiguration/configurationStores)/configurationStore | privatelink.azconfig.io | azconfig.io |
| Azure Backup (Microsoft.RecoveryServices/vaults)/コンテナー | privatelink.{region}.backup.windowsazure.com | {region}.backup.windowsazure.com |
| Azure Event Hubs (Microsoft.EventHub/namespaces)/名前空間 | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Service Bus (Microsoft.ServiceBus/namespaces)/名前空間 | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure IoT Hub (Microsoft.Devices/IotHubs)/iotHub | privatelink.azure-devices.net | azure-devices.net |
| Azure Relay (Microsoft.Relay/namespaces)/名前空間 | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Event Grid (Microsoft.EventGrid/topics)/トピック | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure Event Grid (Microsoft.EventGrid/domains)/ドメイン | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure Web Apps (Microsoft.Web/sites)/サイト | privatelink.azurewebsites.net | azurewebsites.net |
| Azure Machine Learning (Microsoft.MachineLearningServices/workspaces)/ワークスペース | privatelink.api.azureml.ms | api.azureml.ms |
| IoT Hub (Microsoft.Devices/IotHubs)/IotHub | privatelink.azure-devices.net | azure-devices.net |
| SignalR (Microsoft.SignalRService/SignalR)/signalR | privatelink.service.signalr.net | service.signalr.net |
| Azure Monitor (Microsoft.Insights/privateLinkScopes)/azuremonitor | privatelink.monitor.azure.com<br/> privatelink.oms.opinsights.azure.com <br/> privatelink.ods.opinsights.azure.com <br/> privatelink.agentsvc.azure-automation.com | monitor.azure.com<br/> oms.opinsights.azure.com<br/> ods.opinsights.azure.com<br/> agentsvc.azure-automation.com |
| Cognitive Services (Microsoft.CognitiveServices/accounts)/アカウント | privatelink.cognitiveservices.azure.com  | cognitiveservices.azure.com  |
| Azure File Sync (Microsoft.StorageSync/storageSyncServices) / afs |  privatelink.afs.azure.net  |  afs.azure.net  |

 
## <a name="dns-configuration-scenarios"></a>DNS の構成シナリオ

サービスの FQDN は、自動的にパブリック IP アドレスに解決されます。 プライベート エンドポイントのプライベート IP アドレスに解決するには、それに応じて DNS 構成を変更する必要があります。

DNS は、プライベート エンドポイント IP アドレスを正常に解決することでアプリケーションを正しく動作させるために不可欠なコンポーネントです。

お客様の設定に応じて、DNS 解決の統合では次のシナリオを利用できます。

- [カスタム DNS サーバーのない仮想ネットワークのワークロード](#virtual-network-workloads-without-custom-dns-server)
- [DNS フォワーダーを使用しているオンプレミスのワークロード](#on-premises-workloads-using-a-dns-forwarder)
- [DNS フォワーダーを使用した仮想ネットワークとオンプレミス ワークロード](#virtual-network-and-on-premises-workloads-using-a-dns-forwarder)


## <a name="virtual-network-workloads-without-custom-dns-server"></a>カスタム DNS サーバーのない仮想ネットワークのワークロード

この構成は、カスタム DNS サーバーのない仮想ネットワーク ワークロードに適しています。 このシナリオでは、Azure から提供される DNS サービス [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md) に対してクライアントがプライベート エンドポイント IP アドレスを照会します。 Azure DNS はプライベート DNS ゾーンの DNS 解決を担当します。

> [!NOTE]
> このシナリオでは、Azure SQL Database から推奨されるプライベート DNS ゾーンを使用します。 その他のサービスの場合、次のリファレンスを使用してモデルを調整できます (「[Azure サービス DNS ゾーンの構成](#azure-services-dns-zone-configuration)」)。

適切に構成するには、次のリソースが必要です。

- クライアント仮想ネットワーク

- プライベート DNS ゾーン [privatelink.database.windows.net](../dns/private-dns-privatednszone.md) と[タイプ A レコード](../dns/dns-zones-records.md#record-types)

- プライベート エンドポイント情報 (FQDN レコード名とプライベート IP アドレス)

次のスクリーンショットには、プライベート DNS ゾーンを利用した仮想ネットワーク ワークロードからの DNS 解決シーケンスが示されています。

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="単一の仮想ネットワークと Azure 提供の DNS":::

このモデルは、同じプライベート エンドポイントに関連付けられている複数のピアリングされた仮想ネットワークに拡張できます。 これは、ピアリングされたすべての仮想ネットワークに対してプライベート DNS ゾーンに[新しい仮想ネットワーク リンクを追加する](../dns/private-dns-virtual-network-links.md)ことで行うことができます。

> [!IMPORTANT]
> この構成には単一のプライベート DNS ゾーンが必要です。 異なる仮想ネットワークに対して同じ名前を使って複数のゾーンを作成する場合、手作業で DNS レコードをマージする必要があります。

> [!IMPORTANT]
> 別のサブスクリプションのハブアンドスポーク モデルでプライベート エンドポイントを使用している場合は、ハブで同じプライベート DNS ゾーンを再利用します。

このシナリオには、[ハブ アンド スポーク](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)のネットワーク トポロジがあります。スポーク ネットワークで共通プライベート エンドポイントを共有し、すべてのスポーク仮想ネットワークが同じプライベート DNS ゾーンにリンクされています。 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="ハブとスポーク、および Azure 提供の DNS":::

## <a name="on-premises-workloads-using-a-dns-forwarder"></a>DNS フォワーダーを使用しているオンプレミスのワークロード

オンプレミス ワークロードでプライベート エンドポイントの FQDN をプライベート IP アドレスに解決するには、DNS フォワーダーを使用して、Azure で Azure サービス [パブリック DNS ゾーン](#azure-services-dns-zone-configuration)の解決をデプロイする必要があります。

次のシナリオは、Azure に DNS フォワーダーがあるオンプレミス ネットワークに適しています。DNS フォワーダーには、サーバーレベル フォワーダー経由のすべての DNS クエリを Azure 提供の DNS [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md) に解決する役割があります。 

> [!NOTE]
> このシナリオでは、Azure SQL Database から推奨されるプライベート DNS ゾーンを使用します。 その他のサービスの場合は、「 [Azure サービス DNS ゾーンの構成](#azure-services-dns-zone-configuration)」を参照してモデルを調整できます。

適切に構成するには、次のリソースが必要です。

- オンプレミスのネットワーク
-  [オンプレミスに接続されている](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)仮想ネットワーク
- Azure にデプロイされた DNS フォワーダー 
- プライベート DNS ゾーン  [privatelink.database.windows.net](../dns/private-dns-privatednszone.md) と [タイプ A レコード](../dns/dns-zones-records.md#record-types)
- プライベート エンドポイント情報 (FQDN レコード名とプライベート IP アドレス)

次の図は、Azure にデプロイされた DNS フォワーダーを使用するオンプレミス ネットワークからの DNS の解決シーケンスを示しています。この解決は、[仮想ネットワークにリンクされた](../dns/private-dns-virtual-network-links.md)プライベート DNS ゾーンによって行われます。

:::image type="content" source="media/private-endpoint-dns/on-premises-using-azure-dns.png" alt-text="Azure DNS を使用したオンプレミス":::

この構成は、DNS ソリューションが既に配置されているオンプレミス ネットワーク用に拡張できます。 
オンプレミスの DNS ソリューションは、Azure にデプロイされた DNS フォワーダーを参照する [条件付きフォワーダー](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)を介して、Azure DNS に DNS トラフィックを転送するように構成する必要があります。

> [!NOTE]
> このシナリオでは、Azure SQL Database から推奨されるプライベート DNS ゾーンを使用します。 その他のサービスの場合は、「 [Azure サービス DNS ゾーンの構成](#azure-services-dns-zone-configuration)」を参照してモデルを調整できます。

適切に構成するには、次のリソースが必要です。

- カスタム DNS ソリューションが配置されているオンプレミス ネットワーク 
-  [オンプレミスに接続されている](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)仮想ネットワーク
- Azure にデプロイされた DNS フォワーダー
- プライベート DNS ゾーン  [privatelink.database.windows.net](../dns/private-dns-privatednszone.md)  と [タイプ A レコード](../dns/dns-zones-records.md#record-types)
- プライベート エンドポイント情報 (FQDN レコード名とプライベート IP アドレス)

次の図は、Azure に DNS トラフィックを条件付きで転送するオンプレミス ネットワークからの DNS の解決シーケンスを示しています。この解決は、 [仮想ネットワークにリンクされた](../dns/private-dns-virtual-network-links.md)プライベート DNS ゾーンによって行われます。

> [!IMPORTANT]
> 条件付き転送は、推奨される[パブリック DNS ゾーン フォワーダー](#azure-services-dns-zone-configuration)に対して行われる必要があります。 たとえば、 **privatelink**.database.windows.net ではなく `database.windows.net` です。

:::image type="content" source="media/private-endpoint-dns/on-premises-forwarding-to-azure.png" alt-text="Azure DNS に転送しているオンプレミス":::

## <a name="virtual-network-and-on-premises-workloads-using-a-dns-forwarder"></a>DNS フォワーダーを使用した仮想ネットワークとオンプレミス ワークロード

仮想およびオンプレミス ネットワークからプライベート エンドポイントへのアクセスが必要なワークロードに適している一般的なアプローチでは、共有 DNS フォワーダーを使用して、Azure にデプロイされた Azure サービス [パブリック DNS ゾーン](#azure-services-dns-zone-configuration)の解決を実行する必要があります。

次のシナリオは、Azure に DNS フォワーダーがあるオンプレミス ネットワークと、共有ハブ ネットワークに配置されたプライベート エンドポイントへのアクセスが必要な仮想ネットワークに適しています。  

この DNS フォワーダーには、サーバーレベル フォワーダー経由のすべての DNS クエリを、Azure 提供の DNS サービス [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md) に解決する役割があります。

> [!IMPORTANT]
> この構成には単一のプライベート DNS ゾーンが必要です。 また、オンプレミスおよび[ピアリングされた仮想ネットワーク](../virtual-network/virtual-network-peering-overview.md)から実行されるすべてのクライアント接続では、同じプライベート DNS ゾーンを使用する必要があります。

> [!NOTE]
> このシナリオでは、Azure SQL Database から推奨されるプライベート DNS ゾーンを使用します。 その他のサービスの場合は、「 [Azure サービス DNS ゾーンの構成](#azure-services-dns-zone-configuration)」を参照してモデルを調整できます。

適切に構成するには、次のリソースが必要です。

- オンプレミスのネットワーク
-  [オンプレミスに接続されている](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)仮想ネットワーク
- [ピアリングされた仮想ネットワーク](../virtual-network/virtual-network-peering-overview.md) 
- Azure にデプロイされた DNS フォワーダー
- プライベート DNS ゾーン  [privatelink.database.windows.net](../dns/private-dns-privatednszone.md)  と [タイプ A レコード](../dns/dns-zones-records.md#record-types)
- プライベート エンドポイント情報 (FQDN レコード名とプライベート IP アドレス)

次の図は、Azure にデプロイされた DNS フォワーダーを使用するオンプレミスおよび仮想ネットワークからの DNS の解決シーケンスを示しています。この解決は、 [仮想ネットワークにリンクされた](../dns/private-dns-virtual-network-links.md)プライベート DNS ゾーンによって行われます。

:::image type="content" source="media/private-endpoint-dns/hybrid-scenario.png" alt-text="ハイブリッド シナリオ":::

## <a name="next-steps"></a>次のステップ
- [プライベート エンドポイントについて学習します](private-endpoint-overview.md)
