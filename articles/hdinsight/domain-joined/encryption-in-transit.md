---
title: Azure HDInsight の転送中の暗号化
description: Azure HDInsight クラスターで転送中の暗号化を提供するセキュリティ機能について説明します。
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/24/2020
ms.openlocfilehash: fb3761ce7839cb4450997da094646b6604aeb895
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946846"
---
# <a name="ipsec-encryption-in-transit-for-azure-hdinsight"></a>Azure HDInsight での転送中の IPSec 暗号化

この記事では、Azure HDInsight クラスター ノード間の通信に対する転送中の暗号化の実装について説明します。

## <a name="background"></a>バックグラウンド

Azure HDInsight には、エンタープライズ データをセキュリティで保護するためのさまざまなセキュリティ機能が用意されています。 これらのソリューションは、境界セキュリティ、認証、承認、監査、暗号化、コンプライアンスの柱の下にグループ化されています。 暗号化は、保存データと転送中のデータの両方に適用できます。

保存時の暗号化は、Azure ストレージ アカウントでのサーバー側暗号化と、HDInsight クラスターの一部である Azure VM でのディスク暗号化によってカバーされます。

HDInsight での転送中データの暗号化は、クラスター ゲートウェイにアクセスするための[トランスポート層セキュリティ (TLS)](../transport-layer-security.md) と、クラスター ノード間の[インターネット プロトコル セキュリティ (IPSec)](https://wikipedia.org/wiki/IPsec) を使用して実現されます。 IPSec は、すべてのヘッド ノード、ワーカー ノード、エッジ ノード、Zookeeper ノードのほか、ゲートウェイや [ID ブローカー](./identity-broker.md) ノードの間で、オプションで有効にすることができます。

## <a name="enable-encryption-in-transit"></a>転送中の暗号化を有効にする

### <a name="azure-portal"></a>Azure portal

Azure portal を使用して、転送中の暗号化を有効にした新しいクラスターを作成するには、次の手順を実行します。

1. 通常のクラスター作成プロセスを開始します。 最初のクラスター作成手順については、「[Azure portal を使用して HDInsight で Linux ベースのクラスターを作成する](../hdinsight-hadoop-create-linux-clusters-portal.md)」を参照してください。
1. **[基本]** タブと **[ストレージ]** タブに入力します。 **[セキュリティとネットワーク]** タブに進みます。

    :::image type="content" source="media/encryption-in-transit/create-cluster-security-networking-tab.png" alt-text="クラスターの作成 - [セキュリティとネットワーク] タブ":::

1. **[セキュリティとネットワーク]** タブで、 **[転送中の暗号化を有効にする]** チェック ボックスを選択します。

    :::image type="content" source="media/encryption-in-transit/enable-encryption-in-transit.png" alt-text="クラスターの作成 - 転送中の暗号化を有効にする":::

### <a name="create-a-cluster-with-encryption-in-transit-enabled-through-the-azure-cli"></a>転送中の暗号化を有効にしたクラスターを Azure CLI を使用して作成する

転送中の暗号化は `isEncryptionInTransitEnabled` プロパティを使用して有効化します。

[サンプル テンプレートとパラメーター ファイルをダウンロード](https://github.com/Azure-Samples/hdinsight-enterprise-security)できます。 このテンプレートと以下の Azure CLI のコード スニペットを使用する前に、次のプレース ホルダーを正しい値に置き換えます。

| プレースホルダー | 説明 |
|---|---|
| `<SUBSCRIPTION_ID>` | Azure サブスクリプションの ID です |
| `<RESOURCE_GROUP>` | 新しいクラスターとストレージ アカウントを作成するリソース グループです。 |
| `<STORAGEACCOUNTNAME>` | クラスターで使用する既存のストレージ アカウント。 名前は `ACCOUNTNAME.blob.core.windows.net` の形式にする必要があります |
| `<CLUSTERNAME>` | HDInsight クラスターの名前です。 |
| `<PASSWORD>` | SSH と Ambari ダッシュボードを使用してクラスターにサインインするために選択したパスワードです。 |
| `<VNET_NAME>` | クラスターがデプロイされる仮想ネットワーク。 |

以下のコード スニペットでは、次の初期手順が実行されます。

1. Azure アカウントにログインします。
1. 作成操作が実行されるアクティブなサブスクリプションを設定します。
1. 新しいデプロイ アクティビティ用の新しいリソース グループを作成します。
1. テンプレートをデプロイして、新しいクラスターを作成します。

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus2

az deployment group create --name HDInsightEnterpriseSecDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-enterprise-security.json \
    --parameters parameters.json
```

## <a name="next-steps"></a>次のステップ

* [Azure HDInsight のエンタープライズ セキュリティの概要](hdinsight-security-overview.md)
* [Azure Active Directory ユーザーを HDInsight クラスターに同期する](../disk-encryption.md)
