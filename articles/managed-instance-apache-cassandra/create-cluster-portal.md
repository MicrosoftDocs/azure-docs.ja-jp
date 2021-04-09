---
title: クイックスタート - Azure portal から Azure Managed Instance for Apache Cassandra クラスターを作成する
description: このクイックスタートでは、Azure portal を使用して、Azure Managed Instance for Apache Cassandra クラスターを作成する方法について説明します。
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: cb555eefb19b5db7ed7eb0792a813c295a4bf38b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104588615"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-from-the-azure-portal-preview"></a>クイックスタート: Azure portal から Azure Managed Instance for Apache Cassandra クラスターを作成する (プレビュー)
 
Azure Managed Instance for Apache Cassandra では、マネージド オープンソースの Apache Cassandra データセンターのデプロイとスケーリングの自動操作が提供されます。これにより、ハイブリッド シナリオが推進され、継続的なメンテナンスが削減されます。

> [!IMPORTANT]
> Azure Managed Instance for Apache Cassandra は現在、パブリック プレビューの段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

このクイックスタートでは、Azure portal を使用して、Azure Managed Instance for Apache Cassandra クラスターを作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-a-managed-instance-cluster"></a><a id="create-account"></a>マネージド インスタンス クラスターを作成する

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. 検索バーで、**Managed Instance for Apache Cassandra** を検索し、結果を選択します。

   :::image type="content" source="./media/create-cluster-portal/search-portal.png" alt-text="Managed Instance for Apache Cassandra を検索する。" lightbox="./media/create-cluster-portal/search-portal.png" border="true":::

1. **[Create Managed Instance for Apache Cassandra cluster]\(Managed Instance for Apache Cassandra クラスターの作成\)** ボタンを選択します。

   :::image type="content" source="./media/create-cluster-portal/create-cluster.png" alt-text="クラスターを作成する。" lightbox="./media/create-cluster-portal/create-cluster.png" border="true":::

1. **[Create Managed Instance for Apache Cassandra]\(Managed Instance for Apache Cassandra の作成\)** ウィンドウで、次の詳細を入力します。

   * **[サブスクリプション]** - ドロップダウン リストから Azure サブスクリプションを選びます。
   * **[リソース グループ]** - 新しいリソース グループを作成するか、既存のものを使用するかを指定します。 リソース グループは、Azure ソリューションの関連するリソースを保持するコンテナーです。 詳細については、[Azure リソース グループの概要](../azure-resource-manager/management/overview.md)に関する記事を参照してください。
   * **[クラスター名]** - ご自分のクラスターの名前を入力します。
   * **[場所]** - ご自分のクラスターがデプロイされる場所。
   * **[SKU]** - ご自分のクラスターの SKU の種類。
   * **[ノード数]** - クラスター内のノードの数。 これらのノードは、データのレプリカとして機能します。
   * **[Initial Cassandra admin password]\(最初の Cassandra 管理者パスワード\)** - クラスターの作成に使用されるパスワード。
   * **[Confirm Cassandra admin password]\(Cassandra 管理者パスワードの確認\)** - パスワードを再入力します。

    > [!NOTE]
    > パブリック プレビュー期間中は、"*米国東部、米国西部、米国東部 2、米国西部 2、米国中部、米国中南部、北ヨーロッパ、西ヨーロッパ、東南アジア、およびオーストラリア東部*" リージョンでマネージド インスタンス クラスターを作成できます。

   :::image type="content" source="./media/create-cluster-portal/create-cluster-page.png" alt-text="クラスターの作成フォームに入力する。" lightbox="./media/create-cluster-portal/create-cluster-page.png" border="true":::

1. 次に、 **[ネットワーク]** タブを選択します。

1. **[ネットワーク]** ウィンドウで、 **[仮想ネットワーク]** 名と **[サブネット]** を選択します。 既存の仮想ネットワークを選択するか、新しく作成することができます。

   :::image type="content" source="./media/create-cluster-portal/networking.png" alt-text="ネットワークの詳細を構成する。" lightbox="./media/create-cluster-portal/networking.png" border="true":::

1. 最後の手順で新しい VNet を作成した場合は、手順 8. に進みます。 既存の VNet を選択した場合は、クラスターを作成する前に、仮想ネットワークとサブネットに特別なアクセス許可を適用する必要があります。 そのためには、`az role assignment create` コマンドを使用します。`<subscription ID>`、`<resource group name>`、`<VNet name>`、`<subnet name>` は、適切な値に置き換えてください。

   ```azurecli-interactive
   az role assignment create --assignee e5007d2c-4b13-4a74-9b6a-605d99f03501 --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>
   ```

   > [!NOTE]
   > 前のコマンドの `assignee` と `role` の値は固定値です。これらの値は、コマンドに記載されているとおりに入力してください。 そうしないと、クラスターの作成時にエラーが発生します。 このコマンドの実行中にエラーが発生した場合は、実行するためのアクセス許可がない可能性があります。管理者に連絡してアクセス許可を依頼してください。

1. これでネットワークが完成しました。 **[確認および作成]**  >  **[作成]** をクリックします。

    > [!NOTE]
    > クラスターの作成には、最大 15 分かかることがあります。

   :::image type="content" source="./media/create-cluster-portal/review-create.png" alt-text="概要を確認して、クラスターを作成する。" lightbox="./media/create-cluster-portal/review-create.png" border="true":::


1. デプロイが完了したら、リソース グループを調べて、新しく作成されたマネージド インスタンス クラスターを確認します。

   :::image type="content" source="./media/create-cluster-portal/managed-instance.png" alt-text="クラスターが作成された後の [概要] ページ。" lightbox="./media/create-cluster-portal/managed-instance.png" border="true":::

1. クラスター ノードを参照するには、クラスターの作成に使用した [仮想ネットワーク] ウィンドウに移動し、 **[概要]** ウィンドウを開いてこれらを表示します。

   :::image type="content" source="./media/create-cluster-portal/resources.png" alt-text="クラスター リソースを表示する。" lightbox="./media/create-cluster-portal/resources.png" border="true":::


## <a name="connecting-to-your-cluster"></a>クラスターに接続する

Azure Managed Instance for Apache Cassandra では、パブリック IP アドレスを持つノードが作成されないため、新しく作成された Cassandra クラスターに接続するには、VNet 内に別のリソースを作成する必要があります。 これは、アプリケーション、または Apache のオープンソース クエリ ツール [CQLSH](https://cassandra.apache.org/doc/latest/tools/cqlsh.html) がインストールされている仮想マシンにすることができます。 [テンプレート](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/)を使用して、Ubuntu 仮想マシンをデプロイできます。 デプロイされたら、SSH を使用してマシンに接続し、次のコマンドを使用して CQLSH をインストールします。

```bash
# Install default-jre and default-jdk
sudo apt update
sudo apt install openjdk-8-jdk openjdk-8-jre

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to CQLSH (replace <IP> with the private IP addresses of the nodes in your Datacenter):
host=("<IP>" "<IP>" "<IP>")
cqlsh $host 9042 -u cassandra -p cassandra --ssl
```

## <a name="troubleshooting"></a>トラブルシューティング

Virtual Network にアクセス許可を適用するときにエラー (例えば、"*Cannot find user or service principal in graph database for 'e5007d2c-4b13-4a74-9b6a-605d99f03501' ('e5007d2c-4b13-4a74-9b6a-605d99f03501' に対するユーザーまたはサービス プリンシパルがグラフ データベース内で見つかりません)* " など) が発生した場合は、Azure portal から同じアクセス許可を手動で適用できます。 ポータルからアクセス許可を適用するには、既存の仮想ネットワークの **[アクセス制御 (IAM)]** ペインにアクセスし、"Azure Cosmos DB" のロール割り当てを "ネットワーク管理者" ロールに追加します。 "Azure Cosmos DB" を検索したときに 2 つのエントリが表示される場合は、次の図に示すように両方のエントリを追加します。 

   :::image type="content" source="./media/create-cluster-cli/apply-permissions.png" alt-text="アクセス許可を適用する" lightbox="./media/create-cluster-cli/apply-permissions.png" border="true":::

> [!NOTE] 
> Azure Cosmos DB のロールの割り当ては、デプロイの目的にのみ使用されます。 Azure Managed Instance for Apache Cassandra には、Azure Cosmos DB に対するバックエンドの依存関係はありません。   

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このマネージド インスタンス クラスターを引き続き使用しない場合は、次の手順でそれを削除します。

1. Azure portal の左側にあるメニューで、 **[リソース グループ]** を選択します。
1. 一覧から、このクイック スタートで作成したリソース グループを選択します。
1. リソース グループの **[概要]** ペインで、 **[リソース グループの削除]** を選択します。
1. 次のウィンドウで、削除するリソース グループの名前を入力し、**[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure portal を使用して、Azure Managed Instance for Apache Cassandra クラスターを作成する方法について学習しました。 これで、クラスターの操作を開始できます。

> [!div class="nextstepaction"]
> [Azure Databricks でマネージド Apache Spark クラスターをデプロイする](deploy-cluster-databricks.md)
