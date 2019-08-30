---
title: 仮想ネットワーク内で実験と推論を安全に実行する
titleSuffix: Azure Machine Learning service
description: Azure Virtual Network 内の Azure Machine Learning で実験/トレーニング ジョブと推論/スコアリング ジョブを安全に実行する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 08/05/2019
ms.openlocfilehash: 3f13405a3660f8b2aaa053f82ed03d088fb4d733
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69897537"
---
# <a name="secure-azure-ml-experimentation-and-inference-jobs-within-an-azure-virtual-network"></a>Azure Virtual Network 内で Azure ML の実験と推論のジョブを安全に実行する

この記事では、Azure Virtual Network (VNet) 内の Azure Machine Learning で実験/トレーニング ジョブと推論/スコアリング ジョブを安全に実行する方法について説明します。 

**仮想ネットワーク**は、パブリック インターネットから Azure リソースを分離するセキュリティ境界として機能します。 また、Azure の仮想ネットワークをオンプレミス ネットワークに結合することもできます。 ネットワークを結合すると、モデルのトレーニングと、推論用にデプロイしたモデルへのアクセスを、安全に行うことができます。

Azure Machine Learning service は、コンピューティング リソースのために他の Azure サービスに依存します。 コンピューティング リソース ([コンピューティング先](concept-compute-target.md)) は、モデルのトレーニングとデプロイに使用されます。 ターゲットは、仮想ネットワーク内に作成することができます。 たとえば、Microsoft Data Science Virtual Machine を使用してモデルをトレーニングしてから、そのモデルを Azure Kubernetes Service (AKS) にデプロイすることができます。 仮想ネットワークの詳細については、[Azure Virtual Network の概要](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)に関するページを参照してください。

また、この記事では "*セキュリティの詳細設定*" について詳しく説明します。この情報は、基本的または試験的なユースケースには必要ありません。 この記事の一部のセクションでは、さまざまなシナリオに応じた構成情報を記載しています。 手順を順番に実行したり、全部を漏れなく完了したりする必要はありません。

## <a name="prerequisites"></a>前提条件

+ Azure Machine Learning service [ワークスペース](how-to-manage-workspace.md)。 

+ [Azure Virtual Network サービス](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)と [IP ネットワーク](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)の両方に関する全般かつ実用的な知識。 

+ コンピューティング リソースで使用する既存の仮想ネットワークとサブネット。 

## <a name="use-a-storage-account-for-your-workspace"></a>ワークスペース用のストレージ アカウントを使用する

仮想ネットワークでワークスペース用の Azure ストレージ アカウントを使用するには、次のようにします。

1. 仮想ネットワークの背後に実験用のコンピューティング インスタンス (Machine Learning コンピューティング インスタンスなど) を作成するか、ワークスペースに実験用のコンピューティング インスタンス (HDInsight クラスターや仮想マシンなど) をアタッチします。 

   詳細については、この記事の「Machine Learning コンピューティング インスタンスを使用する」と「仮想マシンまたは HDInsight クラスターを使用する」セクションを参照してください。

1. Azure portal で、ワークスペースにアタッチされているストレージにアクセスします。 

   ![Azure Machine Learning service のワークスペースにアタッチされているストレージ](./media/how-to-enable-virtual-network/workspace-storage.png)

1. **Azure Storage** のページで、 __[ファイアウォールと仮想ネットワーク]__ を選択します。 

   ![Azure portal 内の Azure Storage ページの [ファイアウォールと仮想ネットワーク] 領域](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. __[ファイアウォールと仮想ネットワーク]__ ページで、次のようにします。
    - __[選択されたネットワーク]__ を選択します。
    - __[仮想ネットワーク]__ で、 __[Add existing virtual network]\(既存の仮想ネットワークを追加\)__ というリンクを選択します。 この操作により、実験用コンピューティング インスタンスが置かれている仮想ネットワークが追加されます (手順 1 参照)。
    - __[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]__ チェック ボックスをオンにします。

    > [!IMPORTANT]
    > Azure Machine Learning SDK を使用する場合は、開発環境を Azure Storage アカウントに接続できる必要があります。 ストレージ アカウントが仮想ネットワーク内にある場合、ファイアウォールでは開発環境の IP アドレスからのアクセスを許可する必要があります。
    >
    > ストレージ アカウントへのアクセスを有効にするには、*開発クライアントの Web ブラウザーから*ストレージ アカウントの __[ファイアウォールと仮想ネットワーク]__ にアクセスします。 次に、 __[クライアント IP アドレスを追加する]__ チェック ボックスを使用して、クライアントの IP アドレスを __[アドレス範囲]__ に追加します。 また、 __[アドレス範囲]__ フィールドを使用して、開発環境の IP アドレスを手動で入力することもできます。 クライアントの IP アドレスが追加されると、SDK を使用してストレージ アカウントにアクセスできるようになります。

   ![Azure portal の内の [ファイアウォールと仮想ネットワーク] ウィンドウ](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)

1. 実験を実行している間に、実験コードで、Azure Blob Storage を使用するように実行構成を変更します。

    ```python
    run_config.source_directory_data_store = "workspaceblobstore"
    ```

> [!IMPORTANT]
> Azure Machine Learning service 用の "_既定のストレージ アカウント_" は、"_実験専用_" の仮想ネットワークに配置できます。
>
> "_既定でないストレージ アカウント_" は、"_実験専用_" の仮想ネットワークに配置できます。
>
> "_推論_" に使用するストレージ アカウントは、既定のものと既定以外のもののどちらも、"_そのストレージ アカウントに対する無制限のアクセス_" が必要です。
>
> これらの設定を変更したかどうかがわからない場合は、「[Azure Storage ファイアウォールおよび仮想ネットワークを構成する](https://docs.microsoft.com/azure/storage/common/storage-network-security)」の「既定のネットワーク アクセス ルールの変更」セクションを参照してください。 その手順に従うと、推論 (モデルのスコアリング) 中にあらゆるネットワークから発生するアクセスを許可することができます。

## <a name="use-a-key-vault-instance-with-your-workspace"></a>ワークスペース内でキー コンテナー インスタンスを使用する

Azure Machine Learning service では、ワークスペースに関連付けられているキー コンテナー インスタンスに以下の資格情報を格納します。
* 関連付けられたストレージ アカウントの接続文字列
* Azure コンテナー リポジトリ インスタンスへのパスワード
* データ ストアへの接続文字列

仮想ネットワークの背後で、Azure Key Vault と一緒に Azure Machine Learning 実験機能を使用するには、次のようにします。
1. ワークスペースに関連付けられているキー コンテナーに移動します。 

   ![Azure Machine Learning service のワークスペースに関連付けられているキー コンテナー](./media/how-to-enable-virtual-network/workspace-key-vault.png)

1. **[キー コンテナー]** ページの左側のウィンドウで、 __[ファイアウォールと仮想ネットワーク]__ を選択します。 

   ![[キー コンテナー] ウィンドウの [ファイアウォールと仮想ネットワーク] セクション](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. __[ファイアウォールと仮想ネットワーク]__ ページで、次のようにします。
    - __[許可するアクセス元]__ の __[選択されたネットワーク]__ を選択します。
    - __[仮想ネットワーク]__ で __[Add existing virtual network]\(既存の仮想ネットワークを追加\)__ を選択して、実験用のコンピューティング インスタンスが置かれている仮想ネットワークを追加します。
    - __[Allow trusted Microsoft services to bypass this firewall]\(信頼された Microsoft サービスがこのファイアウォールをバイパスすることを許可する\)__ で、 __[はい]__ を選択します。

   ![[キー コンテナー] ウィンドウの [ファイアウォールと仮想ネットワーク] セクション](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)

## <a name="use-a-machine-learning-compute-instance"></a>Machine Learning コンピューティング インスタンスを使用する

仮想ネットワーク内で Azure Machine Learning コンピューティング インスタンスを使用するには、次のネットワーク要件を考慮してください。

- 仮想ネットワークは Azure Machine Learning service のワークスペースと同じサブスクリプションおよびリージョンになければなりません。

- コンピューティング クラスターに指定されたサブネットには、クラスターの対象となる VM 数に対応できる十分な未割り当て IP アドレスが必要です。 サブネットの未割り当て IP アドレスが十分でない場合、クラスターは部分的に割り当てられます。

- トラフィックを制限することで仮想ネットワークをセキュリティで保護する予定の場合は、コンピューティング サービス用にいくつかのポートを開いたままにしてください。 詳細については、「[必須ポート](#mlcports)」のセクションをご覧ください。

- 仮想ネットワークのサブスクリプションまたはリソース グループに対するセキュリティ ポリシーまたはロックで、仮想ネットワークを管理するためのアクセス許可が制限されているかどうかを確認します。

- 複数のコンピューティング クラスターを 1 つの仮想ネットワークに配置する場合は、1 つまたは複数のリソースのクォータの増加を要求する必要がある場合があります。

    Azure Machine Learning コンピューティング インスタンスにより、仮想ネットワークが含まれているリソース グループに追加のネットワーク リソースが自動的に割り当てられます。 各コンピューティング クラスターについて、サービスは次のリソースを割り当てます。

    - 1 つのネットワーク セキュリティ グループ

    - 1 つのパブリック IP アドレス

    - 1 つのロード バランサー

  これらのリソースは、サブスクリプションの[リソース クォータ](https://docs.microsoft.com/azure/azure-subscription-service-limits)によって制限されます。

### <a id="mlcports"></a>必須ポート

Machine Learning コンピューティングは、現在、Azure Batch サービスを使用して、指定された仮想ネットワークに VM をプロビジョニングします。 サブネットは、Batch サービスからの受信方向の通信を許可する必要があります。 この通信を使用して、Machine Learning コンピューティング ノードでの実行スケジュールの設定と、Azure Storage とその他のリソースとの通信を行います。 Batch サービスにより、VM にアタッチされたネットワーク インターフェイス (NIC) レベルでネットワーク セキュリティ グループ (NSG) が追加されます。 これらの NSG によって自動的に、次のトラフィックを許可するためのインバウンド規則とアウトバウンド規則が構成されます。

- __BatchNodeManagement__ の __サービス タグ__ からのポート 29876 と 29877 で受信するインバウンド TCP トラフィック。

    ![BatchNodeManagement サービス タグを使用したインバウンド規則](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- (オプション) リモート アクセスを許可するための、ポート 22 のインバウンド TCP トラフィック。 このポートは、パブリック IP の SSH を使用して接続する場合にのみ使用します。

- 仮想ネットワークに向かう全ポートのアウトバウンド トラフィック。

- インターネットに向かう全ポートのアウトバウンド トラフィック。

Batch によって構成された NSG のインバウンド規則またはアウトバウンド規則を変更したり追加したりする際は注意が必要です。 NSG によってコンピューティング ノードとの通信が拒否された場合は、コンピューティング サービスによってコンピューティング ノードの状態が使用不可に設定されます。

サブネット レベルの NSG を自分で指定する必要はありません。これは、Azure Batch サービスによってその独自の NSG が構成されるためです。 ただし、指定したサブネットに NSG またはファイアウォールが関連付けられている場合は、前述のようにインバウンドとアウトバウンドのセキュリティ規則を構成します。

Azure portal 内での NSG 規則の構成は、次の画像に示したとおりです。

![Machine Learning コンピューティングのインバウンド NSG 規則](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)

![Machine Learning コンピューティングのアウトバウンド NSG 規則](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a id="limiting-outbound-from-vnet"></a> 仮想ネットワークからのアウトバウンド接続を制限する

既定のアウトバウンド規則を使用せずに仮想ネットワークのアウトバウンド アクセスを制限したい場合には、次のようにします。

- NSG 規則を使用して、アウトバウンドのインターネット接続を拒否します。

- 次のものを宛先とするアウトバウンド トラフィックを制限します。
   - Azure Storage (__Storage.Region_Name__ (例: Storage.EastUS) の__サービス タグ__を使用)
   - Azure Container Registry (__AzureContainerRegistry.Region_Name__ (例: AzureContainerRegistry.EastUS) の__サービス タグ__を使用)
   - Azure Machine Learning service (__AzureMachineLearning__ の__サービス タグ__を使用)

Azure portal 内での NSG 規則の構成は、次の画像に示したとおりです。

![Machine Learning コンピューティングのアウトバウンド NSG 規則](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)

### <a name="user-defined-routes-for-forced-tunneling"></a>強制トンネリングのユーザー定義ルート

Machine Learning コンピューティングで強制トンネリングを使用している場合は、コンピューティング リソースを含むサブネットに[ユーザー定義ルート (UDR)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) を追加します。

* 自分のリソースが存在するリージョンで Azure Batch サービスによって使用される IP アドレスごとに、UDR を確立します。 これらの UDR により、Batch サービスが、タスクをスケジュールする目的でプールのコンピューティング ノードと通信できるようになります。 Batch サービスの IP アドレスの一覧を取得するには、次のいずれかの方法を使用します。

    * [Azure の IP 範囲とサービス タグ](https://www.microsoft.com/download/details.aspx?id=56519)をダウンロードし、`BatchNodeManagement.<region>` のファイルを検索する。ここで、`<region>` は Azure リージョンです。

    * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) を使用して情報をダウンロードする。 次の例では、IP アドレス情報をダウンロードし、米国東部 2 リージョンの情報を除外します。

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        ```

* Azure Storage へのアウトバウンド トラフィックは、オンプレミス ネットワーク アプライアンスによってブロックされないようにする必要があります。 具体的には、URL を `<account>.table.core.windows.net`、`<account>.queue.core.windows.net`、`<account>.blob.core.windows.net` の形式にします。

UDR を追加するときに、関連する各 Batch の IP アドレス プレフィックスのルートを定義し、 __[次ホップの種類]__ を __[インターネット]__ に設定します。 次の図に、Azure portal でのこの UDR の例を示します。

![アドレス プレフィックスの UDR の例](./media/how-to-enable-virtual-network/user-defined-route.png)

詳細については、「[仮想ネットワーク内に Azure Batch プールを作成する](../../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling)」を参照してください。

### <a name="create-a-machine-learning-compute-cluster-in-a-virtual-network"></a>仮想ネットワーク内に Machine Learning コンピューティング クラスターを作成する

Machine Learning コンピューティング クラスターを作成するには、次のようにします。

1. [Azure portal](https://portal.azure.com) で、Azure Machine Learning service のワークスペースを選択します。

1. __[アプリケーション]__ セクションで、 __[コンピューティング]__ 、 __[コンピューティングの追加]__ の順に選択します。

1. このコンピューティング リソースで仮想ネットワークを使用するように構成するには、次のようにします。

    a. __[ネットワーク構成]__ で、 __[詳細設定]__ を選択します。

    b. __[リソース グループ]__ ボックスの一覧で、目的の仮想ネットワークが含まれているリソース グループを選択します。

    c. __[仮想ネットワーク]__ ボックスの一覧で、目的のサブネットが含まれている仮想ネットワークを選択します。

    d. __[サブネット]__ ボックスの一覧で、使用するサブネットを選択します。

   ![Machine Learning コンピューティングの仮想ネットワークの設定](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

また、Azure Machine Learning SDK を使用して Machine Learning コンピューティング クラスターを作成することもできます。 次のコードでは、`mynetwork` という名前の仮想ネットワークの `default` サブネットに新しい Machine Learning コンピューティング クラスターが作成されます。

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure virtual network name, subnet, and resource group
vnet_name = 'mynetwork'
subnet_name = 'default'
vnet_resourcegroup_name = 'mygroup'

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print("Found existing cpucluster")
except ComputeTargetException:
    print("Creating new cpucluster")

    # Specify the configuration for the new cluster
    compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                           min_nodes=0,
                                                           max_nodes=4,
                                                           vnet_resourcegroup_name=vnet_resourcegroup_name,
                                                           vnet_name=vnet_name,
                                                           subnet_name=subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

    # Wait for the cluster to be completed, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

作成プロセスが完了したら、実験でクラスターを使用してモデルをトレーニングします。 詳細については、[トレーニング用のコンピューティング ターゲットの選択と使用](how-to-set-up-training-targets.md)に関するページをご覧ください。

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>仮想マシンまたは HDInsight クラスターを使用する

> [!IMPORTANT]
> Azure Machine Learning service では、Ubuntu を実行する仮想マシンのみがサポートされています。

ワークスペースのある仮想ネットワークで仮想マシンまたは Azure HDInsight クラスターを使用するには、次のようにします。

1. Azure portal または Azure CLI を使用して VM または HDInsight クラスターを作成し、そのクラスターを Azure の仮想ネットワークに配置します。 詳細については、次の記事を参照してください。
    * [Linux VM 用の Azure 仮想ネットワークの作成と管理を行う](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Azure Virtual Network を使用した Azure HDInsight の拡張](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. Azure Machine Learning service で VM またはクラスターの SSH ポートと通信できるようにするために、ネットワーク セキュリティ グループ用のソース エントリを構成します。 SSH ポートは、通常はポート 22 です。 このソースからのトラフィックを許可するには、次のようにします。

    * __[ソース]__ ボックスの一覧で、 __[サービス タグ]__ を選択します。

    * __[ソース サービス タグ]__ ボックスの一覧で、 __[AzureMachineLearning]__ を選択します。

    * __[ソース ポート範囲]__ ボックスの一覧で、 __[*]__ を選択します。

    * __[宛先]__ ボックスの一覧で __[すべて]__ を選択します。

    * __[宛先ポート範囲]__ ボックスの一覧で __[22]__ を選択します。

    * __[プロトコル]__ で __[すべて]__ を選択します。

    * __[アクション]__ で、 __[許可]__ を選択します。

   ![仮想ネットワーク内の VM または HDInsight クラスターで実験を行うためのインバウンド規則](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    ネットワーク セキュリティ グループの既定のアウトバウンド規則を保持します。 詳細については、「[セキュリティ グループ](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)」の既定のセキュリティ規則をご覧ください。

    既定のアウトバウンド規則を使用せずに仮想ネットワークのアウトバウンド アクセスを制限したい場合は、「[仮想ネットワークからのアウトバウンド接続を制限する](#limiting-outbound-from-vnet)」セクションを参照してください。

1. VM または HDInsight クラスターをお客様の Azure Machine Learning service のワークスペースにアタッチします。 詳細については、「[モデル トレーニング用のコンピューティング ターゲットを設定する](how-to-set-up-training-targets.md)」をご覧ください。

<a id="aksvnet"></a>

## <a name="use-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)を使用する

仮想ネットワーク内の AKS をワークスペースに追加するには、次のようにします。

> [!IMPORTANT]
> 以下の手順を進める前に、前提条件を確認して、クラスターの IP のアドレス指定を計画してください。 詳細については、[Azure Kubernetes Service (AKS) での高度なネットワークの構成](https://docs.microsoft.com/azure/aks/configure-advanced-networking)に関するページを参照してください。
>
> AKS インスタンスと Azure の仮想ネットワークは同じリージョンに存在する必要があります。

1. [Azure portal](https://portal.azure.com) で、仮想ネットワークを制御する NSG に、**SOURCE** として __AzureMachineLearning__ を使用して Azure Machine Learning service に対して有効になっているインバウンド規則があることを確認します。

    ![Azure Machine Learning service の [コンピューティングの追加] ウィンドウ](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png)

1. Azure Machine Learning service のワークスペースを選択します。

1. __[アプリケーション]__ セクションで、 __[コンピューティング]__ 、 __[コンピューティングの追加]__ の順に選択します。

1. このコンピューティング リソースで仮想ネットワークを使用するように構成するには、次のようにします。

    - __[ネットワーク構成]__ で、 __[詳細設定]__ を選択します。

    - __[リソース グループ]__ ボックスの一覧で、目的の仮想ネットワークが含まれているリソース グループを選択します。

    - __[仮想ネットワーク]__ ボックスの一覧で、目的のサブネットが含まれている仮想ネットワークを選択します。

    - __[サブネット]__ ボックスの一覧で、サブネットを選択します。

    - __[Kubernetes サービスのアドレス範囲]__ ボックスに、Kubernetes サービスのアドレス範囲を入力します。 このアドレス範囲は、Classless Inter-Domain Routing (CIDR) 表記の IP 範囲を使用して、クラスターで使用できる IP アドレスを定義します。 どのサブネットの IP 範囲とも重複していてはなりません (例: 10.0.0.0/16)。

    - __[Kubernetes DNS サービスの IP アドレス]__ ボックスに、Kubernetes DNS サービスの IP アドレスを入力します。 この IP アドレスは Kubernetes DNS サービスに割り当てられます。 Kubernetes サービスのアドレス範囲内に含まれるアドレスを指定する必要があります (例: 10.0.0.10)。

    - __[Docker ブリッジ アドレス]__ ボックスに、Docker ブリッジ アドレスを入力します。 この IP アドレスは Docker ブリッジに割り当てられます。 サブネットの IP 範囲または Kubernetes サービスのアドレス範囲内にすることはできません (例: 172.17.0.1/16)。

   ![Azure Machine Learning service:Machine Learning コンピューティングの仮想ネットワークの設定](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. 仮想ネットワークを制御する NSG グループに、仮想ネットワークの外部から呼び出すことができるように、スコア付けエンドポイントに対して有効になっているインバウンド セキュリティ規則があることを確認します。
   > [!IMPORTANT]
   > NSG に対しては既定のアウトバウンド規則のままにします。 詳細については、「[セキュリティ グループ](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)」の既定のセキュリティ規則をご覧ください。
  
   ![インバウンド セキュリティ規則](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)

また、Azure Machine Learning SDK を使用して仮想ネットワークに Azure Kubernetes Service を追加することもできます。 仮想ネットワークに既に AKS クラスターがある場合は、[AKS にデプロイする方法](how-to-deploy-to-aks.md)に関するページで説明されているように、ワークスペースにアタッチすることができます。 次のコードでは、`mynetwork` という名前の仮想ネットワークの `default` サブネットに新しい AKS インスタンスが作成されます。

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace=ws,
                                  name="myaks",
                                  provisioning_configuration=config)
```

作成プロセスが完了すると、仮想ネットワークの背後にある AKS クラスターで推論 (モデルのスコアリング) を実行できるようになります。 詳細については、[AKS へのデプロイ方法](how-to-deploy-to-aks.md)に関するページをご覧ください。

## <a name="next-steps"></a>次の手順

* [トレーニング環境をセットアップする](how-to-set-up-training-targets.md)
* [モデルをデプロイする場所](how-to-deploy-and-where.md)
* [SSL を使用してモデルを安全にデプロイする](how-to-secure-web-service.md)

