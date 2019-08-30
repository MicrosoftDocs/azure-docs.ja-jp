---
title: 仮想ネットワーク内に Azure Databricks ワークスペースを作成する
description: この記事では、仮想ネットワークに Azure Databricks をデプロイする方法について説明します。
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 12ac5c44a0ee479d84616b138f9e2369a195c275
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976462"
---
# <a name="quickstart-create-an-azure-databricks-workspace-in-a-virtual-network"></a>クイック スタート:仮想ネットワーク内に Azure Databricks ワークスペースを作成する

このクイック スタートでは、仮想ネットワーク内に Azure Databricks ワークスペースを作成する方法を説明します。 また、そのワークスペース内に Apache Spark クラスターも作成します。

Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/)を作成してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインします

[Azure Portal](https://portal.azure.com/) にサインインします。

> [!Note]
> **Azure 無料試用版サブスクリプション**を使用してこのチュートリアルを実行することはできません。
> 無料アカウントをお持ちの場合は、お使いのプロファイルにアクセスし、サブスクリプションを **[従量課金制]** に変更します。 詳細については、[Azure 無料アカウント](https://azure.microsoft.com/free/)に関するページをご覧ください。 次に、リージョン内の vCPU について[使用制限を削除し](https://docs.microsoft.com/azure/billing/billing-spending-limit#remove-the-spending-limit-in-account-center)、[クォータの増加を依頼](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)します。 Azure Databricks ワークスペースを作成するときに、 **[Trial (Premium - 14-Days Free DBUs)]\(試用版 (Premium - 14 日間の無料 DBU)\)** の価格レベルを選択し、ワークスペースから 14 日間無料の Premium Azure Databricks DBU にアクセスできるようにします。

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

1. Azure Portal で、 **[リソースの作成]**  >  **[ネットワーク]**  >  **[仮想ネットワーク]** の順に選択します。

2. **[仮想ネットワークの作成]** で、次の設定を適用します。 

    |Setting|推奨値|説明|
    |-------|---------------|-----------|
    |Name|databricks-quickstart|仮想ネットワークの名前を選択します。|
    |アドレス空間|10.1.0.0/16|CIDR 表記の仮想ネットワークのアドレス範囲。|
    |Subscription|\<該当するサブスクリプション\>|使用する Azure サブスクリプションを選択します。|
    |Resource group|databricks-quickstart|**[新規作成]** を選択し、アカウントの新しいリソース グループ名を入力します。|
    |Location|\<ユーザーに最も近いリージョンを選択\>|お客様の仮想ネットワークをホストできる地理的な場所を選択します。 お客様のユーザーに最も近い場所を使用します。|
    |サブネット名|default|仮想ネットワークの既定のサブネットの名前を選択します。|
    |サブネットのアドレス範囲|10.1.0.0/24|サブネットのアドレス範囲 (CIDR 表記)。 仮想ネットワークのアドレス空間に含まれている必要があります。 使用中のサブネットのアドレス範囲を編集することはできません。|

    ![Azure portal で仮想ネットワークを作成する](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network.png)

3. デプロイが完了したら、仮想ネットワークに移動し、 **[設定]** で **[アドレス空間]** を選択します。 *[その他のアドレス範囲の追加]* というボックスをオンにし、`10.179.0.0/16` を挿入して **[保存]** を選択します。

    ![Azure 仮想ネットワークのアドレス空間](./media/quickstart-create-databricks-workspace-vnet-injection/add-address-space.png)

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks ワークスペースを作成する

1. Azure portal で、 **[リソースの作成]**  >  **[分析]**  >  **[Databricks]** の順に選択します。

2. **[Azure Databricks サービス]** で、次の設定を適用します。

    |Setting|推奨値|説明|
    |-------|---------------|-----------|
    |ワークスペース名|databricks-quickstart|Azure Databricks ワークスペースの名前を選択します。|
    |Subscription|\<該当するサブスクリプション\>|使用する Azure サブスクリプションを選択します。|
    |Resource group|databricks-quickstart|仮想ネットワークに使用したものと同じリソース グループを選択します。|
    |Location|\<ユーザーに最も近いリージョンを選択\>|仮想ネットワークと同じ場所を選択します。|
    |価格レベル|Standard と Premium のいずれかを選択します。|価格レベルの詳細については、[Databricks の価格に関するページ](https://azure.microsoft.com/pricing/details/databricks/)を参照してください。|
    |仮想ネットワーク内での Azure Databricks ワークスペースのデプロイ|はい|この設定により、仮想ネットワークに Azure Databricks ワークスペースをデプロイすることができます。|
    |Virtual Network|databricks-quickstart|前のセクションで作成した仮想ネットワークを選択します。|
    |パブリック サブネット名|public-subnet|既定のパブリック サブネット名を使用します。|
    |パブリック サブネットの CIDR 範囲|10.179.64.0/18|このサブネットの CIDR 範囲は、/18 から /26 の範囲内である必要があります。|
    |プライベート サブネット名|private-subnet|既定のプライベート サブネット名を使用します。|
    |プライベート サブネットの CIDR 範囲|10.179.0.0/18|このサブネットの CIDR 範囲は、/18 から /26 の範囲内である必要があります。|

    ![Azure portal で Azure Databricks ワークスペースを作成する](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace.png)

3. デプロイが完了したら、Azure Databricks リソースに移動します。 仮想ネットワーク ピアリングが無効になっていることに注意してください。 また、概要ページのリソース グループとマネージド リソース グループにも注目してください。 

    ![Azure portal の Azure Databricks の概要](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-overview-portal.png)

    マネージド リソース グループには、ストレージ アカウント (DBFS)、worker-sg (ネットワーク セキュリティ グループ)、worker-vnet (仮想ネットワーク) の物理的な場所が含まれています。 これは、仮想マシン、ディスク、IP アドレス、およびネットワーク インターフェイスを作成する場所でもあります。 このリソース グループは既定ではロックされています。ただし、仮想ネットワークでクラスターが起動されると、マネージド リソース グループ内の workers-vnet と、"ハブ" の仮想ネットワーク間にネットワーク インターフェイスが作成されます。

    ![Azure Databricks マネージド リソース グループ](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group.png)

## <a name="create-a-cluster"></a>クラスターの作成

> [!NOTE]
> 無料アカウントを使用して Azure Databricks クラスターを作成するには、クラスターを作成する前に、プロファイルにアクセスし、サブスクリプションを**従量課金制**に変更します。 詳細については、[Azure 無料アカウント](https://azure.microsoft.com/free/)に関するページをご覧ください。

1. Azure Databricks サービスに戻り、 **[概要]** ページで **[ワークスペースの起動]** を選択します。

2. **[クラスター]**  >  **[+ クラスターの作成]** の順に選択します。 次に、*databricks-quickstart-cluster* のようなクラスター名を作成し、残りの既定の設定を受け入れます。 **[クラスターの作成]** を選択します。

    ![Azure Databricks クラスターの作成](./media/quickstart-create-databricks-workspace-vnet-injection/create-cluster.png)

3. クラスターが実行中になったら、Azure portal でマネージド リソース グループに戻ります。 新しい仮想マシン、ディスク、IP アドレス、およびネットワーク インターフェイスに注目してください。 ネットワーク インターフェイスは、IP アドレスを持つパブリックとプライベートの各サブネットに作成されます。  

    ![クラスター作成後の Azure Databricks マネージド リソース グループ](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group2.png)

4. Azure Databricks ワークスペースに戻り、作成したクラスターを選択します。 次に、 **[Spark UI]** ページの **[エグゼキュータ]** タブに移動します。 ドライバーとエグゼキュータのアドレスがプライベート サブネットの範囲内にあることに注意してください。 この例では、ドライバーは 10.179.0.6 で、エグゼキュータは 10.179.0.4 と 10.179.0.5 です。 IP アドレスは異なる場合があります。

    ![Azure Databricks の Spark UI エグゼキュータ](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-sparkui-executors.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

記事を完了したら、クラスターを終了できます。 そのためには、Azure Databricks ワークスペースの左側のウィンドウで、 **[クラスター]** を選択します。 終了するクラスターで、 **[アクション]** 列の下にある省略記号をポイントし、 **[終了]** アイコンを選択します。 これによりクラスターが停止します。

クラスター作成時に **[Terminate after \_\_ minutes of inactivity]** \(アクティビティが \_\_ 分ない場合は終了する\) チェック ボックスをオンにしていた場合、手動で終了しなくともクラスターは自動で停止します。 このような場合、クラスターは、一定の時間だけ非アクティブな状態が続くと自動的に停止します。

クラスターを再利用しない場合は、Azure portal で作成したリソース グループを削除できます。

## <a name="next-steps"></a>次の手順

この記事では、仮想ネットワークにデプロイした Azure Databricks に Spark クラスターを作成しました。 次の記事に進み、Azure Databricks ノートブックから JDBC を使用して仮想ネットワーク内の SQL Server Linux Docker コンテナーのクエリを実行する方法を学習してください。  

> [!div class="nextstepaction"]
>[Azure Databricks ノートブックから仮想ネットワーク内の SQL Server Linux Docker コンテナーのクエリを実行する](vnet-injection-sql-server.md)
