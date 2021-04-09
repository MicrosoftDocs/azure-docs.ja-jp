---
title: 仮想ネットワークを使用してトレーニング環境をセキュリティで保護する
titleSuffix: Azure Machine Learning
description: 分離された Azure Virtual Network を使用して、Azure Machine Learning トレーニング環境をセキュリティで保護します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 07/16/2020
ms.custom: contperf-fy20q4, tracking-python, contperf-fy21q1
ms.openlocfilehash: 81a6a6aeb826ba4a61fd4f0a4abdb63fe3674bc4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103573729"
---
# <a name="secure-an-azure-machine-learning-training-environment-with-virtual-networks"></a>仮想ネットワークを使用して Azure Machine Learning トレーニング環境をセキュリティで保護する

この記事では、Azure Machine Learning で仮想ネットワークを使用して、トレーニング環境をセキュリティで保護する方法について説明します。

この記事は、Azure Machine Learning ワークフローをセキュリティで保護する手順を説明する全 5 パートからなるシリーズのパート 3 です。 まずは、[パート 1: VNet の概要](how-to-network-security-overview.md)に関するページを読んで、アーキテクチャ全体を理解することを強くお勧めします。 

このシリーズの他の記事は次のとおりです。

[1.VNet の概要](how-to-network-security-overview.md) > [2.ワークスペースをセキュリティで保護する](how-to-secure-workspace-vnet.md) > **3.トレーニング環境をセキュリティで保護する** > [4.推論環境をセキュリティで保護する](how-to-secure-inferencing-vnet.md)  > [5.Studio の機能を有効にする](how-to-enable-studio-virtual-network.md)

この記事では、仮想ネットワークで次のトレーニング コンピューティング リソースをセキュリティで保護する方法について説明します。
> [!div class="checklist"]
> - Azure Machine Learning コンピューティング クラスター
> - Azure Machine Learning コンピューティング インスタンス
> - Azure Databricks
> - 仮想マシン
> - HDInsight クラスター

## <a name="prerequisites"></a>前提条件

+ 一般的な仮想ネットワークのシナリオと全体的な仮想ネットワーク アーキテクチャについては、[ネットワーク セキュリティの概要](how-to-network-security-overview.md)に関するページを参照してください。

+ コンピューティング リソースで使用する既存の仮想ネットワークとサブネット。

+ リソースを仮想ネットワークまたはサブネットにデプロイするには、ご利用のユーザー アカウントが、Azure ロールベースのアクセス制御 (Azure RBAC) で次のアクションへのアクセス許可を保持している必要があります。

    - 仮想ネットワーク リソース上の "Microsoft.Network/virtualNetworks/join/action"。
    - サブネット リソース上の "Microsoft.Network/virtualNetworks/subnet/join/action"。

    ネットワークでの Azure RBAC の詳細については、[ネットワークの組み込みロール](../role-based-access-control/built-in-roles.md#networking)に関するページを参照してください


## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>コンピューティング クラスターとインスタンス 

仮想ネットワーク内で [管理対象の Azure Machine Learning __コンピューティング__](concept-compute-target.md#azure-machine-learning-compute-managed)または [Azure Machine Learning コンピューティング __インスタンス__](concept-compute-instance.md)のどちらかを使用するには、次のネットワーク要件を満たす必要があります。

> [!div class="checklist"]
> * 仮想ネットワークは Azure Machine Learning のワークスペースと同じサブスクリプションとリージョンになければなりません。
> * コンピューティング インスタンスまたはクラスターに指定されたサブネットには、対象となる VM 数に対応できるように十分な未割り当て IP アドレスが必要です。 サブネットに十分な未割り当て IP アドレスがない場合、コンピューティング クラスターは部分的に割り当てられます。
> * 仮想ネットワークのサブスクリプションまたはリソース グループに対するセキュリティ ポリシーまたはロックで、仮想ネットワークを管理するためのアクセス許可が制限されているかどうかを確認します。 トラフィックを制限することで仮想ネットワークをセキュリティで保護する予定の場合は、コンピューティング サービス用にいくつかのポートを開いたままにしてください。 詳細については、「[必須ポート](#mlcports)」のセクションをご覧ください。
> * 複数のコンピューティング インスタンスまたはクラスターを 1 つの仮想ネットワークに配置する場合は、1 つ以上のリソースのクォータの増加を要求することが必要になる場合があります。
> * 仮想ネットワークでワークスペースの Azure Storage アカウントもセキュリティで保護される場合、それらは Azure Machine Learning コンピューティング インスタンスまたはクラスターと同じ仮想ネットワークに存在する必要があります。 
> * コンピューティング インスタンスの Jupyter 機能を動作させるには、Web ソケット通信が無効になっていないことを確認してください。 お使いのネットワークで、*. instances.azureml.net と *. instances.azureml.ms への websocket 接続が許可されていることを確認してください。 
> * コンピューティング インスタンスがプライベート リンク ワークスペースにデプロイされている場合は、仮想ネットワーク内からのみアクセスできます。 カスタム DNS またはホスト ファイルを使用している場合は、ワークスペースのプライベート エンドポイントのプライベート IP アドレスを使用して `<instance-name>.<region>.instances.azureml.ms` のエントリを追加してください。 詳細については、[カスタム DNS](./how-to-custom-dns.md)に関する記事をご覧ください。
> * コンピューティング クラスターやインスタンスをデプロイするために使用されるサブネットを、ACI などの他のサービスには委任しないでください。
> * 仮想ネットワーク サービス エンドポイント ポリシーは、コンピューティング クラスターやインスタンスのシステム ストレージ アカウントに対して機能しません。

    
> [!TIP]
> Machine Learning コンピューティング インスタンスまたはクラスターにより、__仮想ネットワークが含まれているリソース グループ__ に追加のネットワーク リソースが自動的に割り当てられます。 サービスにより、各コンピューティング インスタンスまたはクラスターについて次のリソースが割り当てられます。
> 
> * 1 つのネットワーク セキュリティ グループ
> * 1 つのパブリック IP アドレス。 Azure Policy によってパブリック IP の作成が禁止されている場合、クラスターやインスタンスのデプロイが失敗します。
> * 1 つのロード バランサー
> 
> クラスターの場合、クラスターが 0 ノードにスケールダウンするたびに、これらのリソースは削除 (および再作成) されます。しかし、インスタンスの場合は、インスタンスが完全に削除されるまで、リソースは保持されます (停止してもリソースは削除されません)。 
> これらのリソースは、サブスクリプションの[リソース クォータ](../azure-resource-manager/management/azure-subscription-service-limits.md)によって制限されます。 仮想ネットワーク リソース グループがロックされている場合、コンピューティング クラスターやインスタンスの削除が失敗します。 コンピューティング クラスターやインスタンスが削除されるまで、ロード バランサーを削除することはできません。 また、ネットワーク セキュリティ グループの作成を禁止する Azure ポリシーがないことも確認してください。


### <a name="required-ports"></a><a id="mlcports"></a>必須ポート

パブリック インターネットとの間のネットワーク トラフィックを制限して仮想ネットワークをセキュリティで保護する場合は、Azure Batch サービスからの受信通信を許可する必要があります。

Batch サービスにより、VM にアタッチされたネットワーク インターフェイス (NIC) レベルでネットワーク セキュリティ グループ (NSG) が追加されます。 これらの NSG によって自動的に、次のトラフィックを許可するためのインバウンド規則とアウトバウンド規則が構成されます。

- __BatchNodeManagement__ の __サービス タグ__ からのポート 29876 と 29877 で受信するインバウンド TCP トラフィック。 これらのポートを介するトラフィックは暗号化され、スケジューラ/ノードの通信用に Azure Batch によって使用されます。

    ![BatchNodeManagement サービス タグを使用したインバウンド規則](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- (オプション) リモート アクセスを許可するための、ポート 22 のインバウンド TCP トラフィック。 このポートは、パブリック IP の SSH を使用して接続する場合にのみ使用します。

- 仮想ネットワークに向かう全ポートのアウトバウンド トラフィック。

- インターネットに向かう全ポートのアウトバウンド トラフィック。

- コンピューティング インスタンスの場合は、__AzureMachineLearning__ の __サービス タグ__ からの、ポート 44224 で受信するインバウンド TCP トラフィック。 このポートを介するトラフィックは暗号化され、コンピューティング インスタンスで実行されているアプリケーションと通信するために Azure Machine Learning によって使用されます。

> [!IMPORTANT]
> Batch によって構成された NSG のインバウンド規則またはアウトバウンド規則を変更したり追加したりする際は注意が必要です。 NSG によってコンピューティング ノードとの通信が拒否された場合は、コンピューティング サービスによってコンピューティング ノードの状態が使用不可に設定されます。
>
> サブネット レベルの NSG を自分で指定する必要はありません。これは、Azure Batch サービスによってその独自の NSG が構成されるためです。 ただし、Azure Machine Learning コンピューティングを含むサブネットに NSG またやファイアウォールが関連付けられている場合は、前述のトラフィックも許可する必要があります。

Azure portal 内での NSG 規則の構成は、次の画像に示したとおりです。

:::image type="content" source="./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png" alt-text="Machine Learning コンピューティングのインバウンド NSG 規則" border="true":::



![Machine Learning コンピューティングのインバウンド NSG 規則](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="limit-outbound-connectivity-from-the-virtual-network"></a><a id="limiting-outbound-from-vnet"></a> 仮想ネットワークからのアウトバウンド接続を制限する

既定のアウトバウンド規則を使用せずに仮想ネットワークのアウトバウンド アクセスを制限する場合は、次の手順を使用します。

- NSG 規則を使用して、アウトバウンドのインターネット接続を拒否します。

- __コンピューティング インスタンス__ または __コンピューティング クラスター__ の場合は、次の項目への送信トラフィックを制限します。
   - Azure Storage (__Storage.RegionName__ の __サービス タグ__ を使用)。 ここで、`{RegionName}` は Azure リージョンの名前です。
   - Azure Container Registry (__AzureContainerRegistry.RegionName__ の __サービス タグ__ を使用)。 ここで、`{RegionName}` は Azure リージョンの名前です。
   - Azure Machine Learning (__AzureMachineLearning__ の __サービス タグ__ を使用)
   - Azure Resource Manager (__AzureResourceManager__ の __サービス タグ__ を使用)
   - Azure Active Directory (__AzureActiveDirectory__ の __サービス タグ__ を使用)

Azure portal 内での NSG 規則の構成は、次の画像に示したとおりです。

[![Machine Learning コンピューティングのアウトバウンド NSG 規則](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

> [!NOTE]
> Microsoft から提供される既定の Docker イメージを使用し、ユーザー マネージドの依存関係を有効にする場合は、次の __サービス タグ__ も使用する必要があります。
>
> * __MicrosoftContainerRegistry__
> * __AzureFrontDoor.FirstParty__
>
> この構成は、トレーニング スクリプトの一部として、以下のスニペットに似たコードを使用している場合に必要です。
>
> __RunConfig のトレーニング__
> ```python
> # create a new runconfig object
> run_config = RunConfiguration()
> 
> # configure Docker 
> run_config.environment.docker.enabled = True
> # For GPU, use DEFAULT_GPU_IMAGE
> run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE 
> run_config.environment.python.user_managed_dependencies = True
> ```
>
> __Estimator のトレーニング__
> ```python
> est = Estimator(source_directory='.',
>                 script_params=script_params,
>                 compute_target='local',
>                 entry_script='dummy_train.py',
>                 user_managed=True)
> run = exp.submit(est)
> ```

### <a name="forced-tunneling"></a>強制トンネリング

Azure Machine Learning コンピューティングで[強制トンネリング](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md)を使用している場合は、コンピューティング リソースを含むサブネットからのパブリック インターネットを使用した通信を許可する必要があります。 この通信は、タスクのスケジュール設定と Azure Storage へのアクセスに使用されます。

これを行うには、次の 2 つの方法があります。

* [Virtual Network NAT](../virtual-network/nat-overview.md) を使用する。 NAT ゲートウェイにより、お使いの仮想ネットワーク内の 1 つ以上のサブネットからの送信インターネット接続が提供されます。 詳細については、「[NAT ゲートウェイ リソースを使用した仮想ネットワークの設計](../virtual-network/nat-gateway-resource.md)」を参照してください。

* コンピューティング リソースを含むサブネットに[ユーザー定義ルート (UDR)](../virtual-network/virtual-networks-udr-overview.md) を追加します。 自分のリソースが存在するリージョンで Azure Batch サービスによって使用される IP アドレスごとに、UDR を確立します。 これらの UDR により、Batch サービスが、タスクをスケジュールする目的でプールのコンピューティング ノードと通信できるようになります。 コンピューティング インスタンスへのアクセスに必要なため、Azure Machine Learning service の IP アドレスも追加します。 Azure Machine Learning service の IP アドレスを追加する場合は、__プライマリとセカンダリ__ の両方の Azure リージョンに IP を追加する必要があります。 プライマリ リージョンは、ワークスペースが配置されているところです。

    セカンダリ リージョンを見つけるには、[Azure のペアになっているリージョンを使用したビジネス継続性とディザスター リカバリー](../best-practices-availability-paired-regions.md#azure-regional-pairs)に関するページを参照してください。 たとえば、Azure Machine Learning service が米国東部 2 にある場合、セカンダリ リージョンは米国中部です。 

    Batch サービスと Azure Machine Learning service の IP アドレスの一覧を取得するには、次のいずれかの方法を使用します。

    * [Azure の IP 範囲とサービス タグ](https://www.microsoft.com/download/details.aspx?id=56519)をダウンロードし、`BatchNodeManagement.<region>` と `AzureMachineLearning.<region>` のファイルを検索する。ここで、`<region>` は Azure リージョンです。

    * [Azure CLI](/cli/azure/install-azure-cli) を使用して情報をダウンロードする。 次の例では、IP アドレス情報をダウンロードし、米国東部 2 リージョン (プライマリ) と米国中部リージョン (セカンダリ) の情報を除外します。

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        # Get primary region IPs
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
        # Get secondary region IPs
        az network list-service-tags -l "Central US" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='centralus']"
        ```

        > [!TIP]
        > 米国バージニア、米国アリゾナ、または中国東部 2 のリージョンを使用している場合、これらのコマンドは IP アドレスを返しません。 代わりに、次のいずれかのリンクを使用して IP アドレスの一覧をダウンロードします。
        >
        > * [Azure Government の Azure IP 範囲とサービス タグ](https://www.microsoft.com/download/details.aspx?id=57063)
        > * [Azure China の Azure IP 範囲とサービス タグ](https://www.microsoft.com//download/details.aspx?id=57062)
    
    UDR を追加するときに、関連する各 Batch の IP アドレス プレフィックスのルートを定義し、 __[次ホップの種類]__ を __[インターネット]__ に設定します。 次の図に、Azure portal でのこの UDR の例を示します。

    ![アドレス プレフィックスの UDR の例](./media/how-to-enable-virtual-network/user-defined-route.png)

    > [!IMPORTANT]
    > IP アドレスは、時間の経過と共に変化する可能性があります。

    定義する UDR に加えて、Azure Storage への送信トラフィックがオンプレミスのネットワーク アプライアンス経由で許可されている必要があります。 具体的には、このトラフィックの URL は、`<account>.table.core.windows.net`、`<account>.queue.core.windows.net`、および `<account>.blob.core.windows.net` の形式になります。 

    詳細については、「[仮想ネットワーク内に Azure Batch プールを作成する](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling)」を参照してください。

### <a name="create-a-compute-cluster-in-a-virtual-network"></a>仮想ネットワーク内にコンピューティング クラスターを作成する

Machine Learning コンピューティング クラスターを作成するには、次の手順を使用します。

1. [Azure Machine Learning Studio](https://ml.azure.com/) にサインインし、お使いのサブスクリプションとワークスペースを選択します。

1. 左側にある __[Compute]__ を選択します。

1. 中央から __[トレーニング クラスター]__ を選択し、 __[+]__ を選択します。

1. __[New Training Cluster]\(新しいトレーニング クラスター\)__ ダイアログで、 __[詳細設定]__ セクションを展開します。

1. このコンピューティング リソースを仮想ネットワークを使用するように構成するには、「__仮想ネットワークを構成する__」セクションの操作を実行します。

    1. __[リソース グループ]__ ボックスの一覧で、目的の仮想ネットワークが含まれているリソース グループを選択します。
    1. __[仮想ネットワーク]__ ボックスの一覧で、目的のサブネットが含まれている仮想ネットワークを選択します。
    1. __[サブネット]__ ボックスの一覧で、使用するサブネットを選択します。

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

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="access-data-in-a-compute-instance-notebook"></a>コンピューティング インスタンス ノートブック内のデータにアクセスする

Azure のコンピューティング インスタンスでノートブックを使用している場合は、データと同じ仮想ネットワークおよびサブネットの背後にあるコンピューティング リソースでノートブックが実行されていることを確認する必要があります。 

コンピューティング インスタンスが同一の仮想ネットワーク内に存在するようにするには、作成時に **[詳細設定]**  >  **[仮想ネットワークの構成]** で構成する必要があります。 既存のコンピューティング インスタンスを仮想ネットワークに追加することはできません。

## <a name="azure-databricks"></a>Azure Databricks

ワークスペースが含まれる仮想ネットワークで Azure Databricks を使用するには、次の要件が満たされている必要があります。

> [!div class="checklist"]
> * 仮想ネットワークは Azure Machine Learning のワークスペースと同じサブスクリプションとリージョンになければなりません。
> * 仮想ネットワークでワークスペースの Azure Storage アカウントもセキュリティで保護される場合、それらは Azure Databricks クラスターと同じ仮想ネットワークに存在する必要があります。
> * Azure Databricks によって使用される __databricks-private__ および __databricks-public__ サブネットに加えて、仮想ネットワーク用に作成された __既定の__ サブネットも必要です。

仮想ネットワークでの Azure Databricks の使用に関する具体的な情報については、「[Azure Virtual Network に Azure Databricks をデプロイする](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)」を参照してください。

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>仮想マシンまたは HDInsight クラスター

> [!IMPORTANT]
> Azure Machine Learning では、Ubuntu を実行する仮想マシンのみがサポートされています。

このセクションでは、ワークスペースのある仮想ネットワークで仮想マシンまたは Azure HDInsight クラスターを使用する方法について説明します。

### <a name="create-the-vm-or-hdinsight-cluster"></a>VM または HDInsight クラスターを作成する

Azure portal または Azure CLI を使用して VM または HDInsight クラスターを作成し、そのクラスターを Azure の仮想ネットワークに配置します。 詳細については、次の記事を参照してください。
* [Linux VM 用の Azure 仮想ネットワークの作成と管理を行う](../virtual-machines/linux/tutorial-virtual-network.md)

* [Azure Virtual Network を使用した Azure HDInsight の拡張](../hdinsight/hdinsight-plan-virtual-network-deployment.md)

### <a name="configure-network-ports"></a>ネットワーク ポートを構成する 

Azure Machine Learning で VM またはクラスターの SSH ポートと通信できるようにするために、ネットワーク セキュリティ グループ用のソース エントリを構成します。 SSH ポートは、通常はポート 22 です。 このソースからのトラフィックを許可するには、次の操作を実行します。

1. __[ソース]__ ボックスの一覧で、 __[サービス タグ]__ を選択します。

1. __[ソース サービス タグ]__ ボックスの一覧で、 __[AzureMachineLearning]__ を選択します。

    ![仮想ネットワーク内の VM または HDInsight クラスターで実験を行うためのインバウンド規則](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

1. __[ソース ポート範囲]__ ボックスの一覧で、 __[*]__ を選択します。

1. __[宛先]__ ボックスの一覧で __[すべて]__ を選択します。

1. __[宛先ポート範囲]__ ボックスの一覧で __[22]__ を選択します。

1. __[プロトコル]__ で __[すべて]__ を選択します。

1. __[アクション]__ で、 __[許可]__ を選択します。

ネットワーク セキュリティ グループの既定のアウトバウンド規則を保持します。 詳細については、「[セキュリティ グループ](../virtual-network/network-security-groups-overview.md#default-security-rules)」の既定のセキュリティ規則をご覧ください。

既定のアウトバウンド規則を使用せずに仮想ネットワークのアウトバウンド アクセスを制限したい場合は、「[仮想ネットワークからのアウトバウンド接続を制限する](#limiting-outbound-from-vnet)」セクションを参照してください。

### <a name="attach-the-vm-or-hdinsight-cluster"></a>VM または HDInsight クラスターをアタッチする

VM または HDInsight クラスターをお客様の Azure Machine Learning のワークスペースにアタッチします。 詳細については、「[モデル トレーニング用のコンピューティング ターゲットを設定する](how-to-set-up-training-targets.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

この記事は、全 5 パートからなる仮想ネットワーク シリーズのパート 3 です。 仮想ネットワークをセキュリティで保護する方法については、記事の残りの部分を参照してください。

* [パート 1: 仮想ネットワークの概要](how-to-network-security-overview.md)
* [パート 2: ワークスペース リソースをセキュリティで保護する](how-to-secure-workspace-vnet.md)
* [パート 4: 推論環境をセキュリティで保護する](how-to-secure-inferencing-vnet.md)
* [パート 5: スタジオの機能を有効にする](how-to-enable-studio-virtual-network.md)

名前の解決については、[カスタム DNS](how-to-custom-dns.md) の使用に関する記事も参照してください。