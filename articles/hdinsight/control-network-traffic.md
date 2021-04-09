---
title: Azure HDInsight のネットワーク トラフィックを制御する
description: Azure HDInsight クラスターへのインバウンドおよびアウトバウンド トラフィックを制御するための手法について説明します。
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: 66b078e8dfa1daa100978f04283e9bba7158bddf
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867034"
---
# <a name="control-network-traffic-in-azure-hdinsight"></a>Azure HDInsight のネットワーク トラフィックを制御する

Azure Virtual Network のネットワーク トラフィックは次のメソッドを使用してコントロールできます。

* **ネットワーク セキュリティ グループ** (NSG) を使用すると、ネットワーク の送受信トラフィックをフィルター処理できます。 詳細については、「[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルタリング](../virtual-network/network-security-groups-overview.md)」をご覧ください。

* **ネットワーク仮想アプライアンス** (NVA) は送信トラフィックでのみ使用できます。 NVA では、ファイアウォールやルーターなどのデバイスの機能がレプリケートされます。 詳細については、「[ネットワーク アプライアンス](https://azure.microsoft.com/solutions/network-appliances)」をご覧ください。

マネージド サービスとして HDInsight には、VNET からの受信および送信トラフィックの両方に対して、HDInsight の正常性および管理サービスへの無制限アクセスが必要です。 NSG を使用する場合は、これらのサービスが引き続き HDInsight クラスターと通信できることを確認する必要があります。

:::image type="content" source="./media/control-network-traffic/hdinsight-vnet-diagram.png" alt-text="Azure のカスタム VNET に作成された HDInsight のエンティティの図" border="false":::

## <a name="hdinsight-with-network-security-groups"></a>HDInsight とネットワーク セキュリティ グループ

**ネットワーク セキュリティ グループ** を使用してネットワーク トラフィックを制御する予定の場合は、HDInsight をインストールする前に、次の操作を実行します。

1. HDInsight を使用する予定の Azure リージョンを特定します。

2. 自分のリージョンで HDInsight が必要とするサービス タグを特定します。 これらのサービス タグを取得するには、次の複数の方法があります。
    1. [Azure HDInsight のネットワーク セキュリティ グループ (NSG) サービス タグ](hdinsight-service-tags.md)に関するページで、公開されているサービス タグの一覧を調べます。 
    2. この一覧に自分のリージョンが存在しない場合は、[Service Tag Discovery API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) を使用して、そのリージョンのサービス タグを検索します。
    3. この API を使用できない場合は、[サービス タグの JSON ファイル](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)をダウンロードし、目的のリージョンを検索します。


3. HDInsight をインストールする予定のサブネットのネットワーク セキュリティ グループを作成または変更します。

    * __ネットワーク セキュリティ グループ__: IP アドレスからの __受信__ トラフィックをポート __443__ で許可します。 これにより、HDInsight 管理サービスが仮想ネットワークの外部から、クラスターに確実に到達できます。 __Kafka REST プロキシ__ が有効なクラスターの場合、__受信__ トラフィックをポート __9400__ でも許可します。 これにより、Kafka REST プロキシ サーバーに到達できるようになります。

ネットワーク セキュリティ グループの詳細については、[ネットワーク セキュリティ グループの概要](../virtual-network/network-security-groups-overview.md)に関する記事を参照してください。

## <a name="controlling-outbound-traffic-from-hdinsight-clusters"></a>HDInsight クラスターからの送信トラフィックの制御

HDInsight クラスターからの送信トラフィックを制御する方法の詳細については、「[Azure HDInsight クラスターの送信ネットワーク トラフィック制限の構成](hdinsight-restrict-outbound-traffic.md)」を参照してください。

### <a name="forced-tunneling-to-on-premises"></a>オンプレミスへの強制トンネリング

強制トンネリングは、サブネットからのすべてのトラフィックを強制的に、特定のネットワークまたは場所 (オンプレミスのネットワークやファイアウォールなど) に送るユーザー定義のルーティングの構成です。 オンプレミスへのすべてのデータ転送の強制トンネリングは、大量のデータ転送が発生し、パフォーマンスに影響する可能性があるため、推奨 "_されません_"。

強制トンネリングの設定を検討しているお客様は、[カスタム メタストア](./hdinsight-use-external-metadata-stores.md)を使用し、クラスター サブネットまたはオンプレミス ネットワークからそれらのカスタム メタストアへの適切な接続を設定する必要があります。

Azure Firewall を使用した UDR セットアップの例については、[Azure HDInsight クラスターのアウトバウンド ネットワーク トラフィック制限の構成](hdinsight-restrict-outbound-traffic.md)に関するページを参照してください。

## <a name="required-ports"></a>必須ポート

**ファイアウォール** を使用して、特定のポートで外部からクラスターにアクセスすることを計画している場合、シナリオに必要なポートでトラフィックを許可することが必要な可能性があります。 既定では、前のセクションで説明した Azure 管理トラフィックがポート 443 でクラスターに到達することを許可されている限り、ポートの特別なフィルター処理は必要ありません。

特定のサービスのポート一覧については、「[HDInsight 上の Apache Hadoop サービスで使用されるポート](hdinsight-hadoop-port-settings-for-services.md)」をご覧ください。

仮想アプライアンスのファイアウォール ルールの詳細については、「[virtual appliance scenario (仮想アプライアンス シナリオ)](../virtual-network/virtual-network-scenario-udr-gw-nva.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

* コード サンプルおよび Azure Virtual Network の作成例については、[Azure HDInsight クラスター用の仮想ネットワークの作成](hdinsight-create-virtual-network.md)に関するページを参照してください。
* HDInsight を オンプレミス ネットワークに接続する構成方法の詳しい例については、 [HDInsight のオンプレミス ネットワークへの接続](./connect-on-premises-network.md)に関するページをご覧ください。
* Azure 仮想ネットワークの詳細については、[Azure Virtual Network の概要](../virtual-network/virtual-networks-overview.md)に関するページをご覧ください。
* ネットワーク セキュリティ グループの詳細については、[ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md)に関するページをご覧ください。
* ユーザー定義のルートについて詳しくは、「[ユーザー定義のルートと IP 転送](../virtual-network/virtual-networks-udr-overview.md)」をご覧ください。
* 仮想ネットワークの詳細については、[HDInsight 用の VNET の計画](./hdinsight-plan-virtual-network-deployment.md)に関するページを参照してください。