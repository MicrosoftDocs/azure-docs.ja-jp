---
title: Private Link を使用して Azure HDInsight クラスターを保護および分離する (プレビュー)
description: Azure Private Link を使用して、仮想ネットワーク内の Azure HDInsight クラスターを分離する方法について説明します。
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: bc7834a0f8272da3f8954c7dd9f3e18163795cba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98939353"
---
# <a name="secure-and-isolate-azure-hdinsight-clusters-with-private-link-preview"></a>Private Link を使用して Azure HDInsight クラスターを保護および分離する (プレビュー)

Azure HDInsight の[既定の仮想ネットワーク アーキテクチャ](./hdinsight-virtual-network-architecture.md)では、パブリック IP アドレスを使用して HDInsight リソース プロバイダー (RP) とクラスターが通信します。 一部のシナリオでは、パブリック IP アドレスを使用せずにネットワークを完全に分離する必要があります。 この記事では、プライベート HDInsight クラスターを作成するために使用できる高度なコントロールについて説明します。 ネットワークの分離を完了せずにクラスター間のトラフィックを制限する方法については、[Azure HDInsight でのネットワーク トラフィックの制御](./control-network-traffic.md)に関するページを参照してください。

Azure Resource Manager (ARM) テンプレートで特定のネットワーク プロパティを構成すると、プライベート HDInsight クラスターを作成できます。 プライベート HDInsight クラスターを作成するには、次の 2 つのプロパティを使用します。

* `resourceProviderConnection` を [送信] に設定して、パブリック IP アドレスを削除します。
* `privateLink` を [有効] に設定して、Azure Private Link を有効にし、[プライベート エンドポイント](../private-link/private-endpoint-overview.md)を使用します。

## <a name="remove-public-ip-addresses"></a>パブリック IP アドレスを削除する

既定では、パブリック IP を使用したクラスターへの "*受信*" 接続が HDInsight RP で使用します。 `resourceProviderConnection` ネットワーク プロパティが "*送信*" に設定されている場合は、常にクラスター内部から RP への接続が開始されるように、HDInsight RP への接続が元に戻されます。 受信接続を使用しない場合は、受信サービス タグまたはパブリック IP アドレスが必要ありません。

既定の仮想ネットワーク アーキテクチャで使用される基本ロード バランサーでは、必要な送信依存関係 (HDInsight RP など) にアクセスするために、パブリック NAT (ネットワーク アドレス変換) が自動的に提供されます。 パブリック インターネットへの送信接続を制限する場合は、[ファイアウォールを構成](./hdinsight-restrict-outbound-traffic.md)できます。ただし、必須ではありません。

`resourceProviderConnection` を [送信] に構成すると、プライベート エンドポイントを使用してクラスター固有のリソース (Azure Data Lake Storage Gen2 や外部メタストアなど) にアクセスすることもできます。 これらのリソースにプライベート エンドポイントを使用することは必須ではありませんが、これらのリソースでプライベート エンドポイントを使用する予定がある場合は、HDInsight クラスターを作成する前 `before` に、プライベート エンドポイントと DNS エントリを構成する必要があります。 クラスターの作成時は、必要な外部 SQL データベース (Apache Ranger、Ambari、Oozie、Hive メタストアなど) をすべて作成して、提供することが推奨されています。 これらのすべてのリソースが、独自のプライベート エンドポイントを介して、またはそれ以外の方法で、クラスター サブネット内からアクセスできる必要があることが要件です。

Azure Key Vault のプライベート エンドポイントの使用はサポートされていません。 保存時の CMK 暗号化に Azure Key Vault を使用している場合は、プライベート エンドポイントを使用せずに、HDInsight サブネット内から Azure Key Vault エンドポイントにアクセスできる必要があります。

次の図は、`resourceProviderConnection` が [送信] に設定されている場合に考えられる HDInsight 仮想ネットワーク アーキテクチャを示しています。

:::image type="content" source="media/hdinsight-private-link/outbound-resource-provider-connection-only.png" alt-text="送信リソース プロバイダー接続を使用した HDInsight アーキテクチャの図":::

クラスターを作成したら、適切な DNS 解決を設定する必要があります。 次の正規名 DNS レコード (CNAME) は、Azure で管理されているパブリック DNS ゾーン (`azurehdinsight.net`) で作成されます。

```dns
<clustername>    CNAME    <clustername>-int
```

クラスター FQDN を使用してクラスターにアクセスするには、内部ロード バランサーのプライベート IP を直接使用するか、独自のプライベート DNS ゾーンを使用して、必要に応じてクラスター エンドポイントを上書きします。 たとえば、`azurehdinsight.net` というプライベート DNS ゾーンを使用できます。 必要に応じて、プライベート IP を追加します。

```dns
<clustername>        A   10.0.0.1
<clustername-ssh>    A   10.0.0.2
```

## <a name="enable-private-link"></a>Private Link を有効にする

既定で無効になっている Private Link を使用するには、クラスターを作成する前に、ユーザー定義ルート (UDR) とファイアウォール ルールを適切に設定するための幅広いネットワークに関する知識が必要です。 この設定は省略可能ですが、前のセクションで説明したように、`resourceProviderConnection` ネットワーク プロパティが "*送信*" に設定されている場合にのみ使用できます。

`privateLink` が "*有効*" に設定されている場合は、内部 [標準ロード バランサー](../load-balancer/load-balancer-overview.md) (SLB) が作成され、SLB ごとに Azure Private Link サービスがプロビジョニングされます。 Private Link サービスを使用すると、プライベート エンドポイントから HDInsight クラスターにアクセスできます。

標準ロード バランサーでは、[パブリック送信 NAT](../load-balancer/load-balancer-outbound-connections.md) (基本ロードバランサーなど) が自動的に提供されません。 送信依存関係のために、独自の NAT ソリューション ([Virtual Network NAT](../virtual-network/nat-overview.md) や[ファイアウォール](./hdinsight-restrict-outbound-traffic.md)など) を提供する必要があります。 HDInsight クラスターから送信依存関係へのアクセスは引き続き必要です。 このような送信依存関係が許可されていない場合は、クラスターの作成に失敗する可能性があります。

### <a name="prepare-your-environment"></a>環境を準備する

Private Link サービスの作成を成功させるには、[Private Link サービスのネットワーク ポリシーを明示的に無効にする](../private-link/disable-private-link-service-network-policy.md)必要があります。

次の図は、クラスターを作成する前に必要なネットワーク構成の例を示しています。 この例では、すべての送信トラフィックが UDR を使用する Azure Firewall に[強制的に適用](../firewall/forced-tunneling.md)され、クラスターを作成する前に、ファイアウォールで必要な送信依存関係が "許可される" 必要があります。 Enterprise セキュリティ パッケージ クラスターでは、Azure Active Directory Domain Services へのネットワーク接続を VNet ピアリングによって提供できます。

:::image type="content" source="media/hdinsight-private-link/before-cluster-creation.png" alt-text="クラスターを作成する前のプライベート リンク環境の図":::

ネットワークを設定したら、次の図に示すように、送信リソース プロバイダー接続とプライベート リンクが有効になっているクラスターを作成できます。 この構成では、パブリック IP が使用されず、標準ロード バランサーごとに Private Link サービスがプロビジョニングされています。

:::image type="content" source="media/hdinsight-private-link/after-cluster-creation.png" alt-text="クラスターを作成した後のプライベート リンク環境の図":::

### <a name="access-a-private-cluster"></a>プライベート クラスターにアクセスする

プライベート クラスターにアクセスするには、内部ロード バランサーのプライベート IP を直接使用するか、Private Link の DNS 拡張機能とプライベート エンドポイントを使用します。 `privateLink` 設定が [有効] に設定されている場合は、独自のプライベート エンドポイントを作成し、プライベート DNS ゾーンを使用して DNS 解決を構成できます。

Azure で管理されているパブリック DNS ゾーン `azurehdinsight.net` で作成される Private Link エントリは、次のとおりです。

```dns
<clustername>        CNAME    <clustername>.privatelink
<clustername>-int    CNAME    <clustername>-int.privatelink
<clustername>-ssh    CNAME    <clustername>-ssh.privatelink
```

次の図は、ピアリングされていないか、クラスター ロード バランサーに直接アクセスできない仮想ネットワークからクラスターにアクセスするために必要なプライベート DNS エントリの例を示しています。 Azure プライベート ゾーンを使用して `*.privatelink.azurehdinsight.net` FQDN を上書きし、独自のプライベート エンドポイント IP アドレスに解決できます。

:::image type="content" source="media/hdinsight-private-link/access-private-clusters.png" alt-text="プライベート リンク アーキテクチャの図":::

## <a name="how-to-create-clusters"></a>クラスターを作成する方法
### <a name="use-arm-template-properties"></a>ARM テンプレートのプロパティの使用

次の JSON コード スニペットには、プライベート HDInsight クラスターを作成するために ARM テンプレートで構成する必要がある 2 つのネットワーク プロパティが含まれています。

```json
networkProperties: {
    "resourceProviderConnection": "Inbound" | "Outbound",
    "privateLink": "Enabled" | "Disabled"
}
```

数多くの HDInsight エンタープライズ セキュリティ機能 (Private Link など) を備えた完全なテンプレートについては、[HDInsight エンタープライズ セキュリティのテンプレート](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template)を参照してください。

### <a name="use-azure-powershell"></a>Azure PowerShell の使用

PowerShell を使用する場合は、[こちら](/powershell/module/az.hdinsight/new-azhdinsightcluster#example-4--create-an-azure-hdinsight-cluster-with-relay-outbound-and-private-link-feature)の例を参照してください。

### <a name="use-azure-cli"></a>Azure CLI の使用
Azure CLI を使用する場合は、[こちら](/cli/azure/hdinsight#az_hdinsight_create-examples)の例を参照してください。

## <a name="next-steps"></a>次の手順

* [Azure HDInsight 用の Enterprise セキュリティ パッケージ](enterprise-security-package.md)
* [Azure HDInsight でのエンタープライズ セキュリティの一般情報とガイドライン](./domain-joined/general-guidelines.md)
