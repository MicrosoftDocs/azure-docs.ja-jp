---
title: ネットワークの分離とプライバシー
titleSuffix: Azure Machine Learning
description: Azure Machine Learning で、分離した Azure Virtual Network を使用し、実験/トレーニング ジョブと推論/スコアリング ジョブを安全に実行します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 07/07/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4, tracking-python
ms.openlocfilehash: 0a7a5f21ee868da2b9c3a6c7dc8bb5968531d0d0
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88824204"
---
# <a name="network-isolation-during-training--inference-with-private-virtual-networks"></a>プライベート仮想ネットワークでのトレーニング中や推論中のネットワークの分離
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、Azure Machine Learning トレーニング ジョブや推論ジョブを Azure Virtual Network (vnet) 内で分離して、機械学習のライフサイクルをセキュリティで保護する方法について説明します。 Azure Machine Learning は、モデルのトレーニングとデプロイを行うためのコンピューティング リソース ([コンピューティング先](concept-compute-target.md)とも呼ばれます) を他の Azure サービスに依存しています。 ターゲットは、仮想ネットワーク内に作成することができます。 たとえば、Azure Machine Learning コンピューティングを使用してモデルをトレーニングしてから、そのモデルを Azure Kubernetes Service (AKS) にデプロイすることができます。 

__仮想ネットワーク__ は、パブリック インターネットから Azure リソースを分離するセキュリティ境界として機能します。 また、Azure の仮想ネットワークをオンプレミス ネットワークに結合することもできます。 ネットワークを結合すると、モデルのトレーニングと、推論用にデプロイしたモデルへのアクセスを、安全に行うことができます。

## <a name="prerequisites"></a>前提条件

+ Azure Machine Learning [ワークスペース](how-to-manage-workspace.md)。

+ [Azure Virtual Network サービス](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)と [IP ネットワーク](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)の両方に関する全般かつ実用的な知識。

+ コンピューティング リソースで使用する既存の仮想ネットワークとサブネット。

+ リソースを仮想ネットワークまたはサブネットにデプロイするには、ご利用のユーザー アカウントが、Azure のロールベースのアクセス制御 (RBAC) で次のアクションへのアクセス許可を持っている必要があります。

    - 仮想ネットワーク リソース上の "Microsoft.Network/virtualNetworks/join/action"。
    - サブネット リソース上の "Microsoft.Network/virtualNetworks/subnet/join/action"。

    ネットワークでの RBAC の詳細については、[ネットワークの組み込みロール](/azure/role-based-access-control/built-in-roles#networking)に関するページを参照してください

## <a name="private-endpoints"></a>プライベート エンドポイント

[Azure Private Link](how-to-configure-private-link.md) で、プライベート エンドポイントを使用してワークスペースに接続することもできます。 プライベート エンドポイントは、仮想ネットワーク内にある一組のプライベート IP アドレスです。 [このプライベート エンドポイントを設定する方法について説明します。](how-to-configure-private-link.md)



> [!TIP]
> 仮想ネットワークと Private Link を組み合わせて、ワークスペースとその他の Azure リソースとの間の通信を保護することができます。 ただし、一部の組み合わせには Enterprise edition ワークスペースが必要です。 Enterprise edition が必要なシナリオを理解するには、次の表を使用します。
>
> | シナリオ | Enterprise</br>edition | Basic</br>edition |
> | ----- |:-----:|:-----:| 
> | 仮想ネットワークまたは Private Link なし | ✔ | ✔ |
> | Private Link を使用しないワークスペース。 仮想ネットワーク内のその他のリソース (Azure Container Registry を除く) | ✔ | ✔ |
> | Private Link を使用しないワークスペース。 Private Link を使用するその他のリソース | ✔ | |
> | Private Link を使用するワークスペース。 仮想ネットワーク内のその他のリソース (Azure Container Registry を除く) | ✔ | ✔ |
> | ワークスペースと Private Link を使用するその他のリソース | ✔ | |
> | Private Link を使用するワークスペース。 Private Link または仮想ネットワークを使用しないその他のリソース | ✔ | ✔ |
> | 仮想ネットワーク内の Azure Container Registry | ✔ | |
> | ワークスペースのカスタマー マネージド キー | ✔ | |
> 

> [!WARNING]
> 
> Azure Machine Learning コンピューティング インスタンス プレビューは、Private Link が有効になっているワークスペースではサポートされていません。

<a id="amlcompute"></a>

## <a name="machine-learning-studio"></a>Machine Learning スタジオ

データが仮想ネットワークに保存されている場合は、ワークスペース [マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を使用して、スタジオにデータへのアクセス権を許可する必要があります。

> [!IMPORTANT]
> ほとんどのスタジオでは仮想ネットワークに格納されているデータを操作しますが、統合されたノートブックは __機能しません__。 統合されたノートブックでは、仮想ネットワーク内のストレージの使用はサポートされていません。 代わりに、コンピューティング インスタンスから Jupyter Notebook を使用できます。 詳細については、「[コンピューティング インスタンス ノートブック内のデータにアクセスする](#access-data-in-a-compute-instance-notebook)」のセクションを参照してください。

スタジオにアクセス権を許可できない場合、`Error: Unable to profile this dataset. This might be because your data is stored behind a virtual network or your data does not support profile.` というエラーが表示され、次の操作が無効になります。

* スタジオでのデータのプレビュー
* デザイナーでのデータの視覚化
* AutoML 実験の送信
* ラベル付けプロジェクトの開始

スタジオでは、仮想ネットワーク内の次のデータストアの種類からのデータの読み取りがサポートされています。

* Azure BLOB
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Azure SQL データベース

### <a name="add-resources-to-the-virtual-network"></a>リソースを仮想ネットワークに追加する 

ワークスペースとストレージ アカウントは、相互にアクセスできるように、同一の仮想ネットワークに追加します。

1. ワークスペースを仮想ネットワークに接続するため、[Azure Private Link を有効化](how-to-configure-private-link.md)します。 この機能は現在プレビュー段階であり、米国東部および米国西部 2 のリージョンでご利用いただけます。

1. ストレージ アカウントを仮想ネットワークに接続するため、[ファイアウォールと仮想ネットワークの設定を構成](#use-a-storage-account-for-your-workspace)します。

### <a name="configure-a-datastore-to-use-managed-identity"></a>マネージド ID を使用するようにデータストアを構成する

ワークスペースとストレージ サービス アカウントを仮想ネットワークに追加した後、データへのアクセスにマネージド ID を使用するようにデータストアを構成する必要があります。 この手順により、Azure リソースベース アクセス制御 (RBAC) を使用して、ワークスペースのマネージド ID がストレージ サービスに __閲覧者__ として追加されます。 __閲覧者__ のアクセス権を指定することにより、ワークスペースはファイアウォールの設定を取得し、データが仮想ネットワークから離れないようにすることができます。

1. スタジオで、 __[データストア]__ を選択します。

1. 新しいデータストアを作成する場合は、 __[+ 新しいデータストア]__ を選択します。 既存のデータストアを更新する場合は、それを選択して、 __[資格情報の更新]__ を選択します。

1. データストアの設定で、 __[Azure Machine Learning service がワークスペース マネージド ID を使用してストレージにアクセスするのを許可する]__ に対して __[はい]__ に選択します。

> [!NOTE]
> これらの変更は、有効になるまでに最大 10 分かかる場合があります。

### <a name="azure-blob-storage-blob-data-reader"></a>Azure Blob Storage BLOB データ閲覧者

__Azure Blob Storage__ では、ワークスペース マネージド ID は [BLOB データ閲覧者](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)としても追加されるため、Blob Storage からデータを読み取ることができます。


### <a name="azure-machine-learning-designer-default-datastore"></a>Azure Machine Learning デザイナーの既定のデータストア

既定では、デザイナーはワークスペースにアタッチされているストレージ アカウントを使用して出力を格納します。 ただし、アクセスできる任意のデータストアに出力を格納するように指定することもできます。 お使いの環境で仮想ネットワークが使用されている場合は、これらの制御を使用して、データの安全とアクセス可能な状態を維持できます。

パイプラインの新しい既定のストレージを設定するには:

1. パイプライン ドラフトで、パイプラインのタイトルの近くにある**設定の歯車アイコン**を選択します。
1. **[Select default datastore]\(既定のデータストアの選択\)** を選択します。
1. 新しいデータストアを指定します。

また、既定のデータストアをモジュールごとにオーバーライドすることもできます。 これにより、個々のモジュールのストレージの場所を制御できます。

1. 出力を指定するモジュールを選択します。
1. **[Output settings]\(出力設定\)** セクションを展開します。
1. **[Override default output settings]\(既定の出力設定のオーバーライド\)** を選択します。
1. **[Set output settings]\(出力設定の設定\)** を選択します。
1. 新しいデータストアを指定します。

### <a name="azure-data-lake-storage-gen2-access-control"></a>Azure Data Lake Storage Gen2 のアクセスの制御

仮想ネットワーク内のデータ アクセスの制御には、RBAC と POSIX スタイルのアクセス制御リスト (ACL) の両方を使用できます。

RBAC を使用するには、ワークスペース マネージド ID を [BLOB データ閲覧者](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)のロールに追加します。 詳細については、[ロールベースのアクセス制御](../storage/blobs/data-lake-storage-access-control.md#role-based-access-control)に関するページを参照してください。

ACL を使用する場合は、他のセキュリティ原則の場合と同様に、ワークスペース マネージド ID にアクセス権を割り当てることができます。 詳細については、「[ファイルとディレクトリのアクセス制御リスト](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)」を参照してください。


### <a name="azure-data-lake-storage-gen1-access-control"></a>Azure Data Lake Storage Gen1 のアクセスの制御

Azure Data Lake Storage Gen1 では、POSIX スタイルのアクセス制御リストのみをサポートしています。 他のセキュリティ原則の場合と同様に、ワークスペース マネージド ID にリソースに対するアクセス権を割り当てることができます。 詳細については、「[Azure Data Lake Storage Gen1 のアクセス制御](../data-lake-store/data-lake-store-access-control.md)」を参照してください。


### <a name="azure-sql-database-contained-user"></a>Azure SQL Database 包含ユーザー

Azure SQL Database の格納データにマネージド ID を使用してアクセスするには、そのマネージド ID にマップする SQL 包含ユーザーを作成する必要があります。 外部プロバイダーからユーザーを作成する方法の詳細については、「[Azure AD ID にマップされる包含ユーザーを作成する](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)」を参照してください。

SQL 包含ユーザーを作成したら、これに対してアクセス許可を付与するため、[GRANT T-SQL コマンド](https://docs.microsoft.com/sql/t-sql/statements/grant-object-permissions-transact-sql)を使用します。

### <a name="connect-to-the-studio"></a>スタジオに接続する

仮想ネットワーク内のリソース (コンピューティング インスタンスや仮想マシンなど) からスタジオにアクセスする場合は、仮想ネットワークからスタジオへの送信トラフィックを許可する必要があります。 

たとえば、ネットワーク セキュリティ グループ (NSG) を使用して送信トラフィックを制限している場合は、__AzureFrontDoor.Frontend__ の __サービス タグ__ 宛先に規則を追加します。

## <a name="use-a-storage-account-for-your-workspace"></a>ワークスペース用のストレージ アカウントを使用する

> [!IMPORTANT]
> Azure Machine Learning 用の "_既定のストレージ アカウント_" と、"_既定以外のストレージ アカウント_" は、どちらも仮想ネットワークに配置できます。
>
> 既定のストレージ アカウントは、ワークスペースを作成するときに自動的にプロビジョニングされます。
>
> 既定以外のストレージ アカウントの場合、[`Workspace.create()` 関数](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-)の `storage_account` パラメーターを使用すると、Azure リソース ID によってカスタム ストレージ アカウントを指定できます。

仮想ネットワーク内のワークスペースに Azure ストレージ サービスを使用するには、次の手順を使用します。

1. 仮想ネットワークの背後にコンピューティング リソース (Machine Learning コンピューティング インスタンスやクラスターなど) を作成するか、ワークスペースにコンピューティング リソース (HDInsight クラスター、仮想マシン、Azure Kubernetes Service クラスターなど) をアタッチします。 コンピューティング リソースは、実験またはモデル デプロイに使用できます。

   詳細については、この記事の「[Machine Learning コンピューティングを使用する](#amlcompute)」、「[仮想マシンまたは HDInsight クラスターを使用する](#vmorhdi)」、および「[Azure Kubernetes Service を使用する](#aksvnet)」の各セクションを参照してください。

1. Azure portal で、ワークスペースで使用するストレージ サービスに移動します。

   [![Azure Machine Learning のワークスペースにアタッチされているストレージ](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. ストレージのサービス アカウント ページで、 __[ファイアウォールと仮想ネットワーク]__ を選択します。

   ![Azure portal 内の Azure Storage ページの [ファイアウォールと仮想ネットワーク] 領域](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. __[ファイアウォールと仮想ネットワーク]__ ページで、次の操作を行います。
    - __[選択されたネットワーク]__ を選択します。
    - __[仮想ネットワーク]__ で、 __[Add existing virtual network]\(既存の仮想ネットワークを追加\)__ というリンクを選択します。 この操作により、コンピューティングが置かれている仮想ネットワークが追加されます (手順 1 参照)。

        > [!IMPORTANT]
        > ストレージ アカウントは、トレーニングまたは推論に使用されるコンピューティング インスタンスまたはクラスターと同じ仮想ネットワークとサブネット内に存在する必要があります。

    - __[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]__ チェック ボックスをオンにします。

    > [!IMPORTANT]
    > Azure Machine Learning SDK を使用する場合は、開発環境を Azure Storage アカウントに接続できる必要があります。 ストレージ アカウントが仮想ネットワーク内にある場合、ファイアウォールでは開発環境の IP アドレスからのアクセスを許可する必要があります。
    >
    > ストレージ アカウントへのアクセスを有効にするには、*開発クライアントの Web ブラウザーから*ストレージ アカウントの __[ファイアウォールと仮想ネットワーク]__ にアクセスします。 次に、 __[クライアント IP アドレスを追加する]__ チェック ボックスを使用して、クライアントの IP アドレスを __[アドレス範囲]__ に追加します。 また、 __[アドレス範囲]__ フィールドを使用して、開発環境の IP アドレスを手動で入力することもできます。 クライアントの IP アドレスが追加されると、SDK を使用してストレージ アカウントにアクセスできるようになります。

   [![Azure portal 内の [ファイアウォールと仮想ネットワーク] ウィンドウ](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="use-datastores-and-datasets"></a>データストアとデータセットを使用する

このセクションでは、SDK エクスペリエンスでのデータストアとデータセットの使用について説明します。 スタジオ エクスペリエンスの詳細については、「[Machine Learning スタジオ](#machine-learning-studio)」のセクションを参照してください。

既定では、SDK を使用してデータにアクセスしようとすると、Azure Machine Learning によってデータの有効性と資格情報のチェックが実行されます。 データが仮想ネットワークの背後にある場合、Azure Machine Learning によるデータのアクセスが行えず、チェックは失敗します。 これを回避するには、検証をスキップするデータストアとデータセットを作成する必要があります。

### <a name="use-a-datastore"></a>データストアを使用する

 Azure Data Lake Store Gen1 および Azure Data Lake Store Gen2 では、既定で検証がスキップされるため、特別な操作は必要ありません。 ただし、次のサービスでは、類似の構文を使用してデータストアの検証をスキップできます。

- Azure BLOB ストレージ
- Azure ファイル共有
- PostgreSQL
- Azure SQL データベース

次のコード サンプルでは、新しい Azure BLOB データストアが作成され、`skip_validation=True` が設定されます。

```python
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,  

                                                         datastore_name=blob_datastore_name,  

                                                         container_name=container_name,  

                                                         account_name=account_name, 

                                                         account_key=account_key, 

                                                         skip_validation=True ) // Set skip_validation to true
```

### <a name="use-a-dataset"></a>データセットを使用する

次のデータセットの種類では、データセットの検証をスキップする構文は似ています。
- 区切りファイル
- JSON 
- Parquet
- SQL
- ファイル

次のコードでは、新しい JSON データセットが作成され、`validate=False` が設定されます。

```python
json_ds = Dataset.Tabular.from_json_lines_files(path=datastore_paths, 

validate=False) 

```


## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>コンピューティング クラスターとインスタンス 

仮想ネットワーク内で[管理対象の Azure Machine Learning __コンピューティング__](concept-compute-target.md#azure-machine-learning-compute-managed)または [Azure Machine Learning コンピューティング __インスタンス__](concept-compute-instance.md)のどちらかを使用するには、次のネットワーク要件を満たす必要があります。

> [!div class="checklist"]
> * 仮想ネットワークは Azure Machine Learning のワークスペースと同じサブスクリプションとリージョンになければなりません。
> * コンピューティング インスタンスまたはクラスターに指定されたサブネットには、対象となる VM 数に対応できるように十分な未割り当て IP アドレスが必要です。 サブネットに十分な未割り当て IP アドレスがない場合、コンピューティング クラスターは部分的に割り当てられます。
> * 仮想ネットワークのサブスクリプションまたはリソース グループに対するセキュリティ ポリシーまたはロックで、仮想ネットワークを管理するためのアクセス許可が制限されているかどうかを確認します。 トラフィックを制限することで仮想ネットワークをセキュリティで保護する予定の場合は、コンピューティング サービス用にいくつかのポートを開いたままにしてください。 詳細については、「[必須ポート](#mlcports)」のセクションをご覧ください。
> * 複数のコンピューティング インスタンスまたはクラスターを 1 つの仮想ネットワークに配置する場合は、1 つ以上のリソースのクォータの増加を要求することが必要になる場合があります。
> * 仮想ネットワークでワークスペースの Azure Storage アカウントもセキュリティで保護される場合、それらは Azure Machine Learning コンピューティング インスタンスまたはクラスターと同じ仮想ネットワークに存在する必要があります。 
> * コンピューティング インスタンスの Jupyter 機能を動作させるには、Web ソケット通信が無効になっていないことを確認してください。

> [!TIP]
> Machine Learning コンピューティング インスタンスまたはクラスターにより、__仮想ネットワークが含まれているリソース グループ__ に追加のネットワーク リソースが自動的に割り当てられます。 サービスにより、各コンピューティング インスタンスまたはクラスターについて次のリソースが割り当てられます。
> 
> * 1 つのネットワーク セキュリティ グループ
> * 1 つのパブリック IP アドレス
> * 1 つのロード バランサー
> 
> クラスターの場合、クラスターが 0 ノードにスケールダウンするたびに、これらのリソースは削除 (および再作成) されます。しかし、インスタンスの場合は、インスタンスが完全に削除されるまで、リソースは保持されます (停止してもリソースは削除されません)。 
> これらのリソースは、サブスクリプションの[リソース クォータ](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)によって制限されます。


### <a name="required-ports"></a><a id="mlcports"></a>必須ポート

パブリック インターネットとの間のネットワーク トラフィックを制限して仮想ネットワークをセキュリティで保護する場合は、Azure Batch サービスからの受信通信を許可する必要があります。

Batch サービスにより、VM にアタッチされたネットワーク インターフェイス (NIC) レベルでネットワーク セキュリティ グループ (NSG) が追加されます。 これらの NSG によって自動的に、次のトラフィックを許可するためのインバウンド規則とアウトバウンド規則が構成されます。

- __BatchNodeManagement__ の __サービス タグ__ からのポート 29876 と 29877 で受信するインバウンド TCP トラフィック。

    ![BatchNodeManagement サービス タグを使用したインバウンド規則](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- (オプション) リモート アクセスを許可するための、ポート 22 のインバウンド TCP トラフィック。 このポートは、パブリック IP の SSH を使用して接続する場合にのみ使用します。

- 仮想ネットワークに向かう全ポートのアウトバウンド トラフィック。

- インターネットに向かう全ポートのアウトバウンド トラフィック。

- コンピューティング インスタンスの場合は、__AzureMachineLearning__ の __サービス タグ__ からの、ポート 44224 で受信するインバウンド TCP トラフィック。

> [!IMPORTANT]
> Batch によって構成された NSG のインバウンド規則またはアウトバウンド規則を変更したり追加したりする際は注意が必要です。 NSG によってコンピューティング ノードとの通信が拒否された場合は、コンピューティング サービスによってコンピューティング ノードの状態が使用不可に設定されます。
>
> サブネット レベルの NSG を自分で指定する必要はありません。これは、Azure Batch サービスによってその独自の NSG が構成されるためです。 ただし、Azure Machine Learning コンピューティングを含むサブネットに NSG またやファイアウォールが関連付けられている場合は、前述のトラフィックも許可する必要があります。

Azure portal 内での NSG 規則の構成は、次の画像に示したとおりです。

:::image type="content" source="./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png" alt-text="Machine Learning コンピューティングのインバウンド NSG 規則" border="true":::



![Machine Learning コンピューティングのアウトバウンド NSG 規則](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

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
> Microsoft から提供される既定の Docker イメージを使用し、ユーザー マネージドの依存関係を有効にする場合は、次の__サービス タグ__も使用する必要があります。
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

Azure Machine Learning コンピューティングで[強制トンネリング](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm)を使用している場合は、コンピューティング リソースを含むサブネットからのパブリック インターネットを使用した通信を許可する必要があります。 この通信は、タスクのスケジュール設定と Azure Storage へのアクセスに使用されます。

これを行うには、次の 2 つの方法があります。

* [Virtual Network NAT](../virtual-network/nat-overview.md) を使用する。 NAT ゲートウェイにより、お使いの仮想ネットワーク内の 1 つ以上のサブネットからの送信インターネット接続が提供されます。 詳細については、「[NAT ゲートウェイ リソースを使用した仮想ネットワークの設計](../virtual-network/nat-gateway-resource.md)」を参照してください。

* コンピューティング リソースを含むサブネットに[ユーザー定義ルート (UDR)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) を追加します。 自分のリソースが存在するリージョンで Azure Batch サービスによって使用される IP アドレスごとに、UDR を確立します。 これらの UDR により、Batch サービスが、タスクをスケジュールする目的でプールのコンピューティング ノードと通信できるようになります。 コンピューティング インスタンスへのアクセスに必要なため、リソースが存在する Azure Machine Learning service の IP アドレスも追加します。 Batch サービスと Azure Machine Learning service の IP アドレスの一覧を取得するには、次のいずれかの方法を使用します。

    * [Azure の IP 範囲とサービス タグ](https://www.microsoft.com/download/details.aspx?id=56519)をダウンロードし、`BatchNodeManagement.<region>` と `AzureMachineLearning.<region>` のファイルを検索する。ここで、`<region>` は Azure リージョンです。

    * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) を使用して情報をダウンロードする。 次の例では、IP アドレス情報をダウンロードし、米国東部 2 リージョンの情報を除外します。

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
        ```

        > [!TIP]
        > 米国バージニア、米国アリゾナ、または中国東部 2 のリージョンを使用している場合、これらのコマンドは IP アドレスを返しません。 代わりに、次のいずれかのリンクを使用して IP アドレスの一覧をダウンロードします。
        >
        > * [Azure Government の Azure IP 範囲とサービス タグ](https://www.microsoft.com/download/details.aspx?id=57063)
        > * [Azure China の Azure IP 範囲とサービス タグ](https://www.microsoft.com//download/details.aspx?id=57062)
    
    UDR を追加するときに、関連する各 Batch の IP アドレス プレフィックスのルートを定義し、 __[次ホップの種類]__ を __[インターネット]__ に設定します。 次の図に、Azure portal でのこの UDR の例を示します。

    ![アドレス プレフィックスの UDR の例](./media/how-to-enable-virtual-network/user-defined-route.png)

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

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

仮想ネットワークの Azure Kubernetes Service (AKS) をワークスペースに追加するには、次の手順を使用します。

> [!IMPORTANT]
> 次の手順を開始する前に、「[Azure Kubernetes サービス (AKS) における高度なネットワークの構成](https://docs.microsoft.com/azure/aks/configure-azure-cni#prerequisites)」攻略ガイドの前提条件に従って、クラスターの IP アドレス指定を計画してください。
>
> AKS インスタンスと Azure の仮想ネットワークは同じリージョンに存在する必要があります。 仮想ネットワークでワークスペースによって使用される Azure Storage アカウントをセキュリティで保護する場合、それらは AKS インスタンスと同じ仮想ネットワークに存在する必要があります。

1. [Azure Machine Learning Studio](https://ml.azure.com/) にサインインし、お使いのサブスクリプションとワークスペースを選択します。

1. 左側にある __[Compute]__ を選択します。

1. 中央から __[推論クラスター]__ を選択し、 __[+]__ を選択します。

1. __[New Inference Cluster]\(新しい推論クラスター\)__ ダイアログで、 __[ネットワーク構成]__ の下の __[詳細]__ を選択します。

1. このコンピューティング リソースを仮想ネットワークを使用するように構成するには、次の操作を実行します。

    1. __[リソース グループ]__ ボックスの一覧で、目的の仮想ネットワークが含まれているリソース グループを選択します。
    1. __[仮想ネットワーク]__ ボックスの一覧で、目的のサブネットが含まれている仮想ネットワークを選択します。
    1. __[サブネット]__ ボックスの一覧で、サブネットを選択します。
    1. __[Kubernetes サービスのアドレス範囲]__ ボックスに、Kubernetes サービスのアドレス範囲を入力します。 このアドレス範囲は、Classless Inter-Domain Routing (CIDR) 表記の IP 範囲を使用して、クラスターで使用できる IP アドレスを定義します。 どのサブネットの IP 範囲とも重複していてはなりません (例: 10.0.0.0/16)。
    1. __[Kubernetes DNS サービスの IP アドレス]__ ボックスに、Kubernetes DNS サービスの IP アドレスを入力します。 この IP アドレスは Kubernetes DNS サービスに割り当てられます。 Kubernetes サービスのアドレス範囲内に含まれるアドレスを指定する必要があります (例: 10.0.0.10)。
    1. __[Docker ブリッジ アドレス]__ ボックスに、Docker ブリッジ アドレスを入力します。 この IP アドレスは Docker ブリッジに割り当てられます。 サブネットの IP 範囲または Kubernetes サービスのアドレス範囲内にすることはできません (例: 172.17.0.1/16)。

   ![Azure Machine Learning:Machine Learning コンピューティングの仮想ネットワークの設定](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. 仮想ネットワークを制御する NSG グループに、仮想ネットワークの外部から呼び出すことができるように、スコア付けエンドポイントに対して有効になっているインバウンド セキュリティ規則があることを確認します。
   > [!IMPORTANT]
   > NSG に対しては既定のアウトバウンド規則のままにします。 詳細については、「[セキュリティ グループ](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)」の既定のセキュリティ規則をご覧ください。

   [![インバウンド セキュリティ規則](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

また、Azure Machine Learning SDK を使用して仮想ネットワークに Azure Kubernetes Service を追加することもできます。 仮想ネットワークに既に AKS クラスターがある場合は、[AKS にデプロイする方法](how-to-deploy-and-where.md)に関するページで説明されているように、ワークスペースにアタッチすることができます。 次のコードでは、`mynetwork` という名前の仮想ネットワークの `default` サブネットに新しい AKS インスタンスが作成されます。

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

作成プロセスが完了すると、仮想ネットワークの背後にある AKS クラスターで推論 (モデルのスコアリング) を実行できるようになります。 詳細については、[AKS へのデプロイ方法](how-to-deploy-and-where.md)に関するページをご覧ください。

### <a name="use-private-ips-with-azure-kubernetes-service"></a>Azure Kubernetes Service でプライベート IP を使用する

既定で、AKS デプロイにはパブリック IP アドレスが割り当てられます。 仮想ネットワーク内で AKS を使用する場合は、代わりにプライベート IP アドレスを使用できます。 プライベート IP アドレスには、仮想ネットワークまたは結合されたネットワーク内からのみアクセスできます。

プライベート IP アドレスを有効にするには、"_内部ロード バランサー_" を使用するように AKS を構成します。 

#### <a name="network-contributor-role"></a>ネットワーク共同作成者ロール

> [!IMPORTANT]
> 前に作成した仮想ネットワークを提供して AKS クラスターを作成またはアタッチする場合は、AKS クラスターのサービス プリンシパル (SP) またはマネージド ID に、仮想ネットワークを含むリソース グループに対する_ネットワーク共同作成者_ロールを付与する必要があります。 これは、内部ロード バランサーをプライベート IP に変更する前に行う必要があります。
>
> ネットワーク共同作成者として ID を追加するには、次の手順に従います。

1. AKS のサービス プリンシパルまたはマネージド ID を検索するには、次の Azure CLI コマンドを使用します。 `<aks-cluster-name>` をクラスターの名前に置き換えます。 `<resource-group-name>` を、_AKS クラスターが含まれている_リソース グループの名前に置き換えます。

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query servicePrincipalProfile.clientId
    ``` 

    このコマンドによって `msi` の値が返された場合は、次のコマンドを使用して、マネージド ID のプリンシパル ID を識別します。

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query identity.principalId
    ```

1. 仮想ネットワークが含まれているリソース グループの ID を検索するには、次のコマンドを使用します。 `<resource-group-name>` を、_仮想ネットワークが含まれている_リソース グループの名前に置き換えます。

    ```azurecli-interactive
    az group show -n <resource-group-name> --query id
    ```

1. ネットワーク共同作成者としてサービス プリンシパルまたはマネージド ID を追加するには、次のコマンドを使用します。 `<SP-or-managed-identity>` を、サービス プリンシパルまたはマネージド ID 用に返された ID に置き換えます。 `<resource-group-id>` を、仮想ネットワークが含まれているリソース グループ用に返された ID に置き換えます。

    ```azurecli-interactive
    az role assignment create --assignee <SP-or-managed-identity> --role 'Network Contributor' --scope <resource-group-id>
    ```
AKS での内部ロードバランサーの使用の詳細については、「[Azure Kubernetes Service (AKS) で内部ロード バランサーを使用する](/azure/aks/internal-lb)」を参照してください。

#### <a name="enable-private-ip"></a>プライベート IP を有効にする

> [!IMPORTANT]
> Azure Kubernetes Service クラスターを作成しているときに、プライベート IP を有効にすることはできません。 既存のクラスターの更新として有効にする必要があります。

次のコード スニペットは、__新しい AKS クラスターを作成__ し、プライベート IP/内部ロード バランサーを使用するように更新する方法を示しています。

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute, ComputeTarget

# Verify that cluster does not exist already
try:
    aks_target = AksCompute(workspace=ws, name=aks_cluster_name)
    print("Found existing aks cluster")

except:
    print("Creating new aks cluster")

    # Subnet to use for AKS
    subnet_name = "default"
    # Create AKS configuration
    prov_config = AksCompute.provisioning_configuration(location = "eastus2")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = subnet_name
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = "myaks", provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
    
    # Update AKS configuration to use an internal load balancer
    update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
    aks_target.update(update_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

__Azure CLI__

```azurecli-interactive
az rest --method put --uri https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace>/computes/<compute>?api-version=2018-11-19 --body @body.json
```

コマンドによって参照される `body.json` ファイルの内容は、次の JSON ドキュメントに似ています。

```json
{ 
    "location": "<region>", 
    "properties": { 
        "resourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-name>", 
        "computeType": "AKS", 
        "provisioningState": "Succeeded", 
        "properties": { 
            "loadBalancerType": "InternalLoadBalancer", 
            "agentCount": <agent-count>, 
            "agentVmSize": "vm-size", 
            "clusterFqdn": "<cluster-fqdn>" 
        } 
    } 
} 
```

ワークスペースに __既存のクラスターを接続する__ 場合は、アタッチ操作が完了するまで待ってから、ロード バランサーを構成する必要があります。

クラスターのアタッチの詳細については、「[既存の AKS クラスターをアタッチする](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster)」を参照してください。

既存のクラスターをアタッチした後、プライベート IP を使用するようにクラスターを更新できます。

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute

# ws = workspace object. Creation not shown in this snippet
aks_target = AksCompute(ws,"myaks")

# Change to the name of the subnet that contains AKS
subnet_name = "default"
# Update AKS configuration to use an internal load balancer
update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
aks_target.update(update_config)
# Wait for the operation to complete
aks_target.wait_for_completion(show_output = True)
```

## <a name="use-azure-container-instances-aci"></a>Azure Container Instances (ACI) を使用する

Azure Container Instances は、モデルのデプロイ時に動的に作成されます。 Azure Machine Learning で仮想ネットワーク内に ACI を作成できるようにするには、デプロイで使用されるサブネットに対して __サブネットの委任__ を有効にする必要があります。

> [!WARNING]
> 仮想ネットワークで Azure Container Instances を使用する場合、仮想ネットワークは、Azure Machine Learning ワークスペースと同じリソース グループに含まれている必要があります。
>
> 仮想ネットワーク内で Azure Container Instances を使用する場合、ご使用のワークスペースの Azure Container Registry (ACR) もその仮想ネットワーク内に配置することはできません。

ワークスペースに対する仮想ネットワークで ACI を使用するには、次の手順のようにします。

1. 仮想ネットワークでサブネットの委任を有効にするには、「[サブネットの委任を追加または削除する](../virtual-network/manage-subnet-delegation.md)」の記事に書かれている情報を使用します。 仮想ネットワークを作成するときに委任を有効にすることも、既存のネットワークに委任を追加することもできます。

    > [!IMPORTANT]
    > 委任を有効にする場合は、 __[サブネットをサービスに委任]__ の値として `Microsoft.ContainerInstance/containerGroups` を使用します。

2. [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-) を使用してモデルをデプロイします。`vnet_name` パラメーターと `subnet_name` パラメーターを使用します。 これらのパラメーターには、委任を有効にした仮想ネットワークの名前とサブネットを設定します。

## <a name="azure-firewall"></a>Azure Firewall

Azure Firewall での Azure Machine Learning の使用の詳細については、[Azure Firewall の内側で Azure Machine Learning ワークスペースを使用する](how-to-access-azureml-behind-firewall.md)方法に関する記事を参照してください。

## <a name="azure-container-registry"></a>Azure Container Registry

> [!IMPORTANT]
> 仮想ネットワーク内に Azure Container Registry (ACR) を配置できますが、次の前提条件を満たしている必要があります。
>
> * お使いの Azure Machine Learning ワークスペースが Enterprise Edition である必要があります。 アップグレードの詳細については、「[Enterprise Edition へのアップグレード](how-to-manage-workspace.md#upgrade)」を参照してください。
> * Azure Machine Learning ワークスペースのリージョンは、[Private Link が有効なリージョン](https://docs.microsoft.com/azure/private-link/private-link-overview#availability)である必要があります。 
> * Azure Container Registry が Premium バージョンである必要があります。 アップグレードの詳細については、「[SKU の変更](/azure/container-registry/container-registry-skus#changing-skus)」を参照してください。
> * トレーニングまたは推論に使用されるストレージ アカウントとコンピューティング ターゲットと同じ仮想ネットワークとサブネット内に Azure Container Registry が存在している必要があります。
> * Azure Machine Learning ワークスペースに、[Azure Machine Learning コンピューティング クラスター](how-to-set-up-training-targets.md#amlcompute)が含まれている必要があります。
>
>     ACR が仮想ネットワークの背後にある場合、Azure Machine Learning でそれを使用して Docker イメージを直接構築することはできません。 代わりに、コンピューティング クラスターを使用してイメージが構築されます。

1. お使いのワークスペースの Azure Container Registry の名前を見つけるには、次の方法のいずれかを使用します。

    __Azure Portal__

    ワークスペースの [概要] セクションの Azure Container Registry への __[レジストリ]__ 値のリンク。

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="ワークスペースの Azure Container Registry" border="true":::

    __Azure CLI__

    [Azure CLI 用の Machine Learning 拡張機能をインストールしている](reference-azure-machine-learning-cli.md)場合は、`az ml workspace show` コマンドを使用して、ワークスペースの情報を表示できます。

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    このコマンドでは `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"` のような値が返されます。 文字列の最後の部分がワークスペースの Azure Container Registry の名前です。

1. 仮想ネットワークへのアクセスを制限するには、「[レジストリのネットワーク アクセスを構成する](../container-registry/container-registry-vnet.md#configure-network-access-for-registry)」の手順に従います。 仮想ネットワークを追加するときに、Azure Machine Learning リソースの仮想ネットワークとサブネットを選択します。

1. Azure Machine Learning Python SDK を使用して、Docker イメージを構築するようにコンピューティング クラスターを構成します。 次のコード スニペットで、これを実行する方法を示します。

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    ```

    > [!IMPORTANT]
    > ストレージ アカウント、コンピューティング クラスター、および Azure Container Registry のすべてが、仮想ネットワークと同じサブネット内に存在する必要があります。
    
    詳細については、[update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-) メソッド リファレンスを参照してください。

1. 次の Azure Resource Manager テンプレートを適用する必要があります。 このテンプレートによって、お使いのワークスペースで ACR と通信できるようになります。

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "keyVaultArmId": {
        "type": "string"
        },
        "workspaceName": {
        "type": "string"
        },
        "containerRegistryArmId": {
        "type": "string"
        },
        "applicationInsightsArmId": {
        "type": "string"
        },
        "storageAccountArmId": {
        "type": "string"
        },
        "location": {
        "type": "string"
        }
    },
    "resources": [
        {
        "type": "Microsoft.MachineLearningServices/workspaces",
        "apiVersion": "2019-11-01",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "sku": {
            "tier": "enterprise",
            "name": "enterprise"
        },
        "properties": {
            "sharedPrivateLinkResources":
    [{"Name":"Acr","Properties":{"PrivateLinkResourceId":"[concat(parameters('containerRegistryArmId'), '/privateLinkResources/registry')]","GroupId":"registry","RequestMessage":"Approve","Status":"Pending"}}],
            "keyVault": "[parameters('keyVaultArmId')]",
            "containerRegistry": "[parameters('containerRegistryArmId')]",
            "applicationInsights": "[parameters('applicationInsightsArmId')]",
            "storageAccount": "[parameters('storageAccountArmId')]"
        }
        }
    ]
    }
    ```

## <a name="key-vault-instance"></a>キー コンテナー インスタンス 

Azure Machine Learning では、ワークスペースに関連付けられているキー コンテナー インスタンスに以下の資格情報を格納します。
* 関連付けられたストレージ アカウントの接続文字列
* Azure コンテナー リポジトリ インスタンスへのパスワード
* データ ストアへの接続文字列

仮想ネットワークの内側で、Azure Machine Learning 実験機能を Azure Key Vault で使用するには、次の手順を使用します。

1. ワークスペースに関連付けられているキー コンテナーに移動します。

   [![Azure Machine Learning のワークスペースに関連するキー コンテナー](./media/how-to-enable-virtual-network/workspace-key-vault.png)](./media/how-to-enable-virtual-network/workspace-key-vault.png#lightbox)

1. __[キー コンテナー]__ ページの左側のウィンドウで、 __[ファイアウォールと仮想ネットワーク]__ を選択します。

   ![[キー コンテナー] ウィンドウの [ファイアウォールと仮想ネットワーク] セクション](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. __[ファイアウォールと仮想ネットワーク]__ ページで、次の操作を行います。
    - __[許可するアクセス元]__ の __[選択されたネットワーク]__ を選択します。
    - __[仮想ネットワーク]__ で、 __[既存の仮想ネットワークを追加]__ を選択して、実験のコンピューティングが存在する仮想ネットワークを追加します。
    - __[Allow trusted Microsoft services to bypass this firewall]\(信頼された Microsoft サービスがこのファイアウォールをバイパスすることを許可する\)__ で、 __[はい]__ を選択します。

   [![[キー コンテナー] ウィンドウの [ファイアウォールと仮想ネットワーク] セクション](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)


## <a name="azure-databricks"></a>Azure Databricks

ワークスペースが含まれる仮想ネットワークで Azure Databricks を使用するには、次の要件が満たされている必要があります。

> [!div class="checklist"]
> * 仮想ネットワークは Azure Machine Learning のワークスペースと同じサブスクリプションとリージョンになければなりません。
> * 仮想ネットワークでワークスペースの Azure Storage アカウントもセキュリティで保護される場合、それらは Azure Databricks クラスターと同じ仮想ネットワークに存在する必要があります。
> * Azure Databricks によって使用される __databricks-private__ および __databricks-public__ サブネットに加えて、仮想ネットワーク用に作成された __既定の__ サブネットも必要です。

仮想ネットワークでの Azure Databricks の使用に関する具体的な情報については、「[Azure Virtual Network に Azure Databricks をデプロイする](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html)」を参照してください。

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>仮想マシンまたは HDInsight クラスター

> [!IMPORTANT]
> Azure Machine Learning では、Ubuntu を実行する仮想マシンのみがサポートされています。

ワークスペースのある仮想ネットワークで仮想マシンまたは Azure HDInsight クラスターを使用するには、次の手順を使用します。

1. Azure portal または Azure CLI を使用して VM または HDInsight クラスターを作成し、そのクラスターを Azure の仮想ネットワークに配置します。 詳細については、次の記事を参照してください。
    * [Linux VM 用の Azure 仮想ネットワークの作成と管理を行う](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Azure Virtual Network を使用した Azure HDInsight の拡張](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. Azure Machine Learning で VM またはクラスターの SSH ポートと通信できるようにするために、ネットワーク セキュリティ グループ用のソース エントリを構成します。 SSH ポートは、通常はポート 22 です。 このソースからのトラフィックを許可するには、次の操作を実行します。

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

1. VM または HDInsight クラスターをお客様の Azure Machine Learning のワークスペースにアタッチします。 詳細については、「[モデル トレーニング用のコンピューティング ターゲットを設定する](how-to-set-up-training-targets.md)」をご覧ください。


## <a name="next-steps"></a>次のステップ

* [トレーニング環境をセットアップする](how-to-set-up-training-targets.md)
* [プライベート エンドポイントをセットアップする](how-to-configure-private-link.md)
* [モデルをデプロイする場所](how-to-deploy-and-where.md)
* [TLS を使用して Azure Machine Learning による Web サービスをセキュリティで保護する](how-to-secure-web-service.md)
