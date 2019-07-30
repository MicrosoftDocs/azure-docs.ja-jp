---
title: 仮想ネットワークで実験と推論を実行する
titleSuffix: Azure Machine Learning service
description: Azure の仮想ネットワーク内を保護する機械学習の実験と推論を実行します。 モデルのトレーニング用のコンピューティング ターゲットを作成する方法と、仮想ネットワーク内で推論する方法について説明します。 必要な受信ポートや送信ポートなど、セキュリティで保護された仮想ネットワークの要件について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 07/10/2019
ms.openlocfilehash: 412eaac2f82a6d09761dcac53192916df215831f
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358788"
---
# <a name="securely-run-experiments-and-inference-inside-an-azure-virtual-network"></a>Azure の仮想ネットワーク内で実験と推論を安全に実行する

この記事では、仮想ネットワーク内で実験と推論を実行する方法について説明します。 仮想ネットワークは、パブリック インターネットから Azure リソースを分離するセキュリティ境界として機能します。 また、Azure の仮想ネットワークをオンプレミス ネットワークに結合することもできます。 これにより、モデルのトレーニングと、推論用にデプロイしたモデルへのアクセスを、安全に行うことができます。 推論、つまりモデル スコアリングとは、通常は運用環境のデータに基づいて、デプロイしたモデルを使用して予測を行うフェーズです。

Azure Machine Learning service は、コンピューティング リソースのために他の Azure サービスに依存します。 コンピューティング リソース (コンピューティング ターゲット) は、モデルのトレーニングとデプロイに使用されます。 これらのコンピューティング ターゲットは、仮想ネットワーク内に作成することができます。 たとえば、Microsoft Data Science Virtual Machine を使用して、モデルをトレーニングしてから、そのモデルを Azure Kubernetes Service (AKS) にデプロイすることができます。 仮想ネットワークの詳細については、[Azure Virtual Network の概要](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

このドキュメントでは、一般に、読者が Azure Virtual Network と IP ネットワーキングに慣れていることを前提としています。 このドキュメントでは、コンピューティング リソースで使用する仮想ネットワークとサブネットを作成していることも想定しています。 Azure Virtual Network に慣れていない場合は、以下の記事をお読みになりサービスについて学習してください。

* [IP アドレス指定](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)
* [セキュリティ グループ](https://docs.microsoft.com/azure/virtual-network/security-overview)
* [クイック スタート:仮想ネットワークを作成する](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)
* [ネットワーク トラフィックをフィルター処理する](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

## <a name="storage-account-for-your-workspace"></a>ワークスペースのストレージ アカウント

> [!IMPORTANT]
> Azure Machine Learning service 用の__既定のストレージ アカウント__は、__実験を行っている間だけ__、仮想ネットワークに配置できます。
>
> __実験用の既定以外のストレージ アカウント__の場合、または__推論__用にストレージ アカウントを使っている場合は、__ストレージ アカウントに対する無制限のアクセス__が必要です.
> 
> これらの設定を変更済みかどうかわからない場合は、「[Azure Storage ファイアウォールおよび仮想ネットワークを構成する](https://docs.microsoft.com/azure/storage/common/storage-network-security)」の「__既定のネットワーク アクセス ルールの変更__」をご覧ください。 推論時、つまりモデルのスコア付け時に、すべてのネットワークからのアクセスを許可する手順を使用します。

ワークスペース用の既定の Azure ストレージ アカウントを仮想ネットワークに配置するには、次の手順のようにします。

1. 仮想ネットワークの背後で実験のコンピューティング (例: Machine Learning コンピューティング) を作成するか、実験のコンピューティングをワークスペース (例: HDInsight クラスターまたは仮想マシン) にアタッチします。 詳細については、このドキュメントの「[Machine Learning コンピューティングを使用する](#use-machine-learning-compute)」と「[仮想マシンまたは HDInsight クラスターを使用する](#use-a-virtual-machine-or-hdinsight-cluster)」セクションを参照してください。
2. ワークスペースにアタッチされているストレージに移動します。 ![Azure Machine Learning service のワークスペースにアタッチされている Azure Storage を示す Azure portal の画像](./media/how-to-enable-virtual-network/workspace-storage.png)
3. Azure Storage ページで、 __[ファイアウォールと仮想ネットワーク]__ を選択します。 ![Azure Storage ページの [ファイアウォールと仮想ネットワーク] セクションが表示されている Azure portal の画像](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)
4. __[ファイアウォールと仮想ネットワーク]__ ページで、次のエントリを選択します。
    - __[選択されたネットワーク]__ を選択します。
    - __[仮想ネットワーク]__ の下で、 __[既存の仮想ネットワークを追加]__ を選択して、実験のコンピューティングが存在する仮想ネットワークを追加します (手順 1 を参照)。
    - __[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]__ を選択します。
![Azure Storage の下に [ファイアウォールと仮想ネットワーク] ページが表示されている Azure portal の画像](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png) 

5. 実験を実行している間に、実験コードで、BLOB ストレージを使用するように実行構成を変更します。
    ```python
    run_config.source_directory_data_store = "workspaceblobstore"
    ```
    
## <a name="key-vault-for-your-workspace"></a>ワークスペースのキー コンテナー
Azure Machine Learning service では、さまざまな種類の資格情報を格納するために、ワークスペースに関連付けられた Key Vault インスタンスが使用されます。
* 関連付けられたストレージ アカウントの接続文字列
* Azure コンテナー リポジトリ インスタンスへのパスワード
* データ ストアへの接続文字列。 

仮想ネットワークの背後で、Azure Machine Learning 実験機能を Key Vault で使用するには、次の手順に従います。
1. ワークスペースに関連付けられている Key Vault に移動します。 ![Azure Machine Learning service のワークスペースに関連付けられている Key Vault を示す Azure portal の画像](./media/how-to-enable-virtual-network/workspace-key-vault.png)
2. Key Vault ページで、 __[ファイアウォールと仮想ネットワーク]__ セクションを選択します。 ![Key Vault ページの [ファイアウォールと仮想ネットワーク] セクションが表示されている Azure portal の画像](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)
3. __[ファイアウォールと仮想ネットワーク]__ ページで、次のエントリを選択します。
    - __[選択されたネットワーク]__ を選択します。
    - __[仮想ネットワーク]__ の下で、 __[既存の仮想ネットワークを追加]__ を選択して、実験のコンピューティング能力が存在する仮想ネットワークを追加します。
    - __[Allow trusted Microsoft services to bypass this firewall]\(信頼された Microsoft サービスがこのファイアウォールをバイパスすることを許可する\)__ を選択します。
![Key Vault の下に [ファイアウォールと仮想ネットワーク] ページが表示されている Azure portal の画像](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png) 


## <a name="use-machine-learning-compute"></a>Machine Learning コンピューティングを使用する

仮想ネットワークで Azure Machine Learning コンピューティングを使用するには、ネットワーク要件に関する次の情報を使用します。

- 仮想ネットワークは Azure Machine Learning service のワークスペースと同じサブスクリプションおよびリージョンになければなりません。

- Machine Learning コンピューティング クラスターに指定されたサブネットには、クラスターの対象となる VM 数に対応できる十分な未割り当て IP アドレスが必要です。 サブネットの未割り当て IP アドレスが十分でない場合、クラスターは部分的に割り当てられます。

- トラフィックを制限することで仮想ネットワークをセキュリティで保護する予定の場合は、Machine Learning コンピューティング サービス用にいくつかのポートを開いたままにしてください。 詳細については、「[必須ポート](#mlcports)」をご覧ください。

- 仮想ネットワークのサブスクリプションまたはリソース グループに対するセキュリティ ポリシーまたはロックで、仮想ネットワークを管理するためのアクセス許可が制限されているかどうかを確認します。

- 複数の Machine Learning コンピューティング クラスターを 1 つの仮想ネットワークに配置する場合は、1 つまたは複数のリソースのクォータの増加を要求する必要がある場合があります。

    Machine Learning コンピューティングは、仮想ネットワークが含まれているリソース グループに追加のネットワーク リソースを自動的に割り当てます。 Azure Machine Learning service は、Machine Learning コンピューティング クラスターごとに次のリソースを割り当てます。

    - 1 つのネットワーク セキュリティ グループ (NSG)

    - 1 つのパブリック IP アドレス

    - 1 つのロード バランサー

  これらのリソースは、サブスクリプションの[リソース クォータ](https://docs.microsoft.com/azure/azure-subscription-service-limits)によって制限されます。

### <a id="mlcports"></a>必須ポート

Machine Learning コンピューティングは、現在、Azure Batch サービスを使用して、指定された仮想ネットワークに VM をプロビジョニングします。 サブネットは、Batch サービスからの受信方向の通信を許可する必要があります。 この通信を使用して、Machine Learning コンピューティング ノードでの実行スケジュールの設定と、Azure Storage とその他のリソースとの通信を行います。 VM にアタッチされたネットワーク インターフェイス (NIC) レベルで NSG が追加されます。 これらの NSG によって自動的に、次のトラフィックを許可するためのインバウンド規則とアウトバウンド規則が構成されます。

- __BatchNodeManagement__ の __サービス タグ__ からのポート 29876 と 29877 で受信するインバウンド TCP トラフィック。

    ![BatchNodeManagement サービス タグを使用したインバウンド規則を示した Azure Portal のイメージ](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)
 
- (オプション) リモート アクセスを許可するための、ポート 22のインバウンド TCP トラフィック。 このポートはパブリック IP の SSH を使用して接続する場合にのみ必要です。
 
- 仮想ネットワークに向かう全ポートのアウトバウンド トラフィック。

- インターネットに向かう全ポートのアウトバウンド トラフィック。 

Batch によって構成された NSG のインバウンド規則またはアウトバウンド規則を変更したり追加したりする際は注意が必要です。 NSG によってコンピューティング ノードとの通信が NSG によって拒否された場合は、Machine Learning コンピューティング サービスによってコンピューティング ノードの状態が使用不可に設定されます。

サブネット レベルの NSG を自分で指定する必要はありません。これは、Batch によってその独自の NSG が構成されるためです。 ただし、指定したサブネットに NSG やファイアウォールが関連付けられている場合は、前述のようにインバウンドとアウトバウンドのセキュリティ規則を構成します。 

次のスクリーンショットは、NSG 規則の構成が Azure portal にどのように表示されるかを示しています。

![Machine Learning コンピューティングのインバウンド NSG 規則のスクリーンショット](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)

![Machine Learning コンピューティングのアウトバウンド NSG 規則のスクリーンショット](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a id="limiting-outbound-from-vnet"></a> 仮想ネットワークからの送信接続を制限する

既定のアウトバウンド規則を使用せず、仮想ネットワークの発信アクセスを制限する場合は、次の手順に従います。

- NSG 規則を使用して、送信インターネット接続を拒否する 

- Azure Storage (__Storage.Region_Name__ (例: Storage.EastUS) の__サービス タグ__を使用)、 Azure Container Registry (__AzureContainerRegistry.Region_Name__ (例: AzureContainerRegistry.EastUS) の__サービス タグ__を使用)、 および Azure Machine Learning service (__AzureMachineLearning__ の__サービス タグ__を使用) への送信トラフィックを制限する

次のスクリーンショットは、NSG 規則の構成が Azure portal にどのように表示されるかを示しています。

![Machine Learning コンピューティングのアウトバウンド NSG 規則のスクリーンショット](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)

### <a name="user-defined-routes-for-forced-tunneling"></a>強制トンネリングのユーザー定義ルート

Azure Machine Learning コンピューティングで強制トンネリングを使用している場合は、[ユーザー定義ルート (UDR)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) をコンピューティング リソースを含むサブネットに追加する必要があります。

* 自分のリソースが存在するリージョンで Azure Batch サービスによって使用される IP アドレスごとのユーザー定義ルート。 これらの UDR により、バッチ サービスが、タスクをスケジュールする目的でプールのコンピューティング ノードと通信できるようになります。 Batch サービスの IP アドレスの一覧を取得するには、Azure サポートにお問い合わせください。

* Azure Storage への送信トラフィック (具体的には、`<account>.table.core.windows.net`、`<account>.queue.core.windows.net`、`<account>.blob.core.windows.net` 形式の URL) が、オンプレミス ネットワーク アプライアンスによってブロックされないこと。

ユーザー定義ルートを追加するときに、関連する各 Batch の IP アドレス プレフィックスのルートを定義し、 __[次ホップの種類]__ を __[インターネット]__ に設定します。 次の図に、Azure portal でのこの UDR の例を示します。

![アドレス プレフィックスのユーザー定義ルートの例](./media/how-to-enable-virtual-network/user-defined-route.png)

詳細については、「[仮想ネットワーク内に Azure Batch プールを作成する](../../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling)」を参照してください。

### <a name="create-machine-learning-compute-in-a-virtual-network"></a>仮想ネットワークに Machine Learning コンピューティングを作成する

Azure portal を使用して Machine Learning コンピューティング クラスターを作成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) で、Azure Machine Learning service のワークスペースを選択します。

1. __[アプリケーション]__ セクションで、 __[コンピューティング]__ を選択します。 次に、 __[コンピューティングの追加]__ を選択します。 

    ![Azure Machine Learning service にコンピューティングを追加する方法](./media/how-to-enable-virtual-network/add-compute.png)

1. このコンピューティング リソースで仮想ネットワークを使用するように構成するには、以下のオプションを使用します。

    - __ネットワーク構成__: __[Advanced] \(詳細設定)__ を選択します。

    - __[リソース グループ]__ :仮想ネットワークが含まれているリソース グループを選択します。

    - __仮想ネットワーク__:サブネットが含まれている仮想ネットワークを選択します。

    - __サブネット__:使用するサブネットを選択します。

   ![機械学習コンピューティングの仮想ネットワークの設定を示すスクリーンショット](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

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

    # Wait for the cluster to complete, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

作成プロセスが完了したら、クラスターを使用してモデルをトレーニングできます。 詳細については、[トレーニング用のコンピューティング ターゲットの選択と使用](how-to-set-up-training-targets.md)に関するページをご覧ください。

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>仮想マシンまたは HDInsight クラスターを使用する

ワークスペースのある仮想ネットワークで仮想マシンまたは Azure HDInsight クラスターを使用するには、次の手順に従います。

> [!IMPORTANT]
> Azure Machine Learning service では、Ubuntu を実行する仮想マシンのみがサポートされています。

1. Azure portal または Azure CLI を使用して VM または HDInsight クラスターを作成し、Azure の仮想ネットワークに配置します。 詳細については、以下のドキュメントをご覧ください。
    * [Linux VM 用の Azure 仮想ネットワークの作成と管理を行う](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Azure Virtual Network を使用した Azure HDInsight の拡張](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network) 

1. Azure Machine Learning service で VM またはクラスターの SSH ポートと通信できるようにするには、NSG 用のソース エントリを構成する必要があります。 SSH ポートは、通常はポート 22 です。 このソースからのトラフィックを許可するには、次の情報を使用します。

    * __ソース__: __[Service Tag]\(サービス タグ\)__ を選択します。

    * __[ソース サービス タグ]__ : __[AzureMachineLearning]__ を選択します。

    * __[ソース ポート範囲]__ : __[*]__ を選びます。

    * __[接続先]__ : __[すべて]__ を選択します。

    * __[宛先ポート範囲]__ : __[22]__ を選択します。

    * __プロトコル__: __[すべて]__ を選択します。

    * __アクション__: __[許可]__ を選択します。

   ![仮想ネットワーク内の VM または HDInsight クラスターで実験を行うためのインバウンド規則のスクリーンショット](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    NSG に対しては既定のアウトバウンド規則のままにします。 詳細については、「[セキュリティ グループ](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)」の既定のセキュリティ規則をご覧ください。

    既定のアウトバウンド規則を使用せず、仮想ネットワークの発信アクセスを制限する場合は、「[仮想ネットワークからの送信接続を制限する](#limiting-outbound-from-vnet)」を参照してください。
    
1. VM または HDInsight クラスターをお客様の Azure Machine Learning service のワークスペースにアタッチします。 詳細については、「[モデル トレーニング用のコンピューティング ターゲットを設定する](how-to-set-up-training-targets.md)」をご覧ください。

## <a name="use-azure-kubernetes-service"></a>Azure Kubernetes Service を使用する

> [!IMPORTANT]
> 手順を進める前に、前提条件を確認して、クラスターの IP のアドレス指定を計画してください。 詳細については、[Azure Kubernetes Service での高度なネットワークの構成](https://docs.microsoft.com/azure/aks/configure-advanced-networking)に関するページをご覧ください。
> 
>
> NSG に対しては既定のアウトバウンド規則のままにします。 詳細については、「[セキュリティ グループ](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)」の既定のセキュリティ規則をご覧ください。
>
> Azure Kubernetes Service と Azure の仮想ネットワークは同じリージョンに存在する必要があります。

仮想ネットワークの Azure Kubernetes Service をワークスペースに追加するには、Azure portal で次の手順に従います。

1. 仮想ネットワークを制御する NSG グループに、__AzureMachineLearning__ の__サービス タグ__を使用して Azure Machine Learning service に対して有効になっている受信規則があることを確認します。

    ![Azure Machine Learning service にコンピューティングを追加する方法](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png)     
 
1. [Azure portal](https://portal.azure.com) で、Azure Machine Learning service のワークスペースを選択します。

1. __[アプリケーション]__ セクションで、 __[コンピューティング]__ を選択します。 次に、 __[コンピューティングの追加]__ を選択します。 

    ![Azure Machine Learning service にコンピューティングを追加する方法](./media/how-to-enable-virtual-network/add-compute.png)

1. このコンピューティング リソースで仮想ネットワークを使用するように構成するには、以下のオプションを使用します。

    - __ネットワーク構成__: __[Advanced] \(詳細設定)__ を選択します。

    - __[リソース グループ]__ :仮想ネットワークが含まれているリソース グループを選択します。

    - __仮想ネットワーク__:サブネットが含まれている仮想ネットワークを選択します。

    - __サブネット__:サブネットを選択します。

    - __Kubernetes サービスのアドレス範囲__:Kubernetes サービスのアドレス範囲を選択します。 このアドレス範囲は、CIDR 表記の IP 範囲を使用して、クラスターで使用できる IP アドレスを定義します。 どのサブネット IP 範囲とも重複していてはなりません。 例: 10.0.0.0/16。

    - __Kubernetes DNS サービスの IP アドレス__:Kubernetes DNS サービスの IP アドレスを選択します。 この IP アドレスは Kubernetes DNS サービスに割り当てられます。 Kubernetes サービスのアドレス範囲内に含まれるアドレスを指定する必要があります。 例: 10.0.0.10。

    - __Docker ブリッジのアドレス__:Docker ブリッジのアドレスを選択します。 この IP アドレスは Docker ブリッジに割り当てられます。 サブネット IP 範囲または Kubernetes サービスのアドレス範囲内にすることはできません。 例: 172.17.0.1/16。

   ![Azure Machine Learning service:Machine Learning コンピューティングの仮想ネットワークの設定](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. 仮想ネットワークを制御する NSG グループに、仮想ネットワークの外部から呼び出すことができるように、スコア付けエンドポイントに対して有効になっている受信規則があることを確認します。

    ![Azure Machine Learning service にコンピューティングを追加する方法](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)

    > [!TIP]
    > 仮想ネットワークに既に AKS クラスターがある場合は、ワークスペースにアタッチすることができます。 詳細については、[AKS へのデプロイ方法](how-to-deploy-to-aks.md)に関するページをご覧ください。

また、**Azure Machine Learning SDK** を使用して仮想ネットワークに Azure Kubernetes Service を追加することもできます。 次のコードでは、`mynetwork` という名前の仮想ネットワークの `default` サブネットに新しい Azure Kubernetes Service インスタンスが作成されます。

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

作成プロセスが完了すると、仮想ネットワークの背後にある AKS クラスターで推論/スコア付けを行うことができます。 詳細については、[AKS へのデプロイ方法](how-to-deploy-to-aks.md)に関するページをご覧ください。

## <a name="next-steps"></a>次の手順

* [トレーニング環境をセットアップする](how-to-set-up-training-targets.md)
* [モデルをデプロイする場所](how-to-deploy-and-where.md)
* [SSL を使用してモデルを安全にデプロイする](how-to-secure-web-service.md)

