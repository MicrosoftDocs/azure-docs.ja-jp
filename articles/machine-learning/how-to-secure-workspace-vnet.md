---
title: 仮想ネットワークを使用して Azure Machine Learning ワークスペースをセキュリティで保護する
titleSuffix: Azure Machine Learning
description: 分離された Azure Virtual Network を使用して、Azure Machine Learning ワークスペースおよび関連付けられているリソースを保護します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 03/17/2021
ms.topic: conceptual
ms.custom: how-to, contperf-fy20q4, tracking-python, contperf-fy21q1
ms.openlocfilehash: a923f65e5c6183d045f4b7455e0a01edda75d499
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104584341"
---
# <a name="secure-an-azure-machine-learning-workspace-with-virtual-networks"></a>仮想ネットワークを使用して Azure Machine Learning ワークスペースをセキュリティで保護する

この記事では、仮想ネットワーク内の Azure Machine Learning ワークスペースとそれに関連付けられているリソースをセキュリティで保護する方法について説明します。

この記事は、Azure Machine Learning ワークフローをセキュリティで保護する手順を説明する全 5 パートのシリーズのパート 2 です。 まずは[パート 1:VNet の概要](how-to-network-security-overview.md)に関するページを読んで、アーキテクチャ全体を理解することを強くお勧めします。 

このシリーズの他の記事は次のとおりです。

[1.VNet の概要](how-to-network-security-overview.md) > **2.ワークスペースをセキュリティで保護する** > [3.トレーニング環境をセキュリティで保護する](how-to-secure-training-vnet.md) > [4.推論環境をセキュリティで保護する](how-to-secure-inferencing-vnet.md) > [5.スタジオの機能を有効にする](how-to-enable-studio-virtual-network.md)

この記事では、仮想ネットワークで次のワークスペース リソースを有効にする方法について説明します。
> [!div class="checklist"]
> - Azure Machine Learning ワークスペース
> - Azure Storage アカウント
> - Azure Machine Learning データストアとデータセット
> - Azure Key Vault
> - Azure Container Registry

## <a name="prerequisites"></a>前提条件

+ 一般的な仮想ネットワークのシナリオと全体的な仮想ネットワーク アーキテクチャについては、[ネットワーク セキュリティの概要](how-to-network-security-overview.md)に関するページを参照してください。

+ コンピューティング リソースで使用する既存の仮想ネットワークとサブネット。

+ リソースを仮想ネットワークまたはサブネットにデプロイするには、ご利用のユーザー アカウントが、Azure ロールベースのアクセス制御 (Azure RBAC) で次のアクションへのアクセス許可を保持している必要があります。

    - 仮想ネットワーク リソース上の "Microsoft.Network/virtualNetworks/join/action"。
    - サブネット リソース上の "Microsoft.Network/virtualNetworks/subnet/join/action"。

    ネットワークでの Azure RBAC の詳細については、[ネットワークの組み込みロール](../role-based-access-control/built-in-roles.md#networking)に関するページを参照してください


## <a name="secure-the-workspace-with-private-endpoint"></a>プライベート エンドポイントを使用してワークスペースをセキュリティで保護する

Azure Private Link では、プライベート エンドポイントを使用してワークスペースに接続できます。 プライベート エンドポイントは、仮想ネットワーク内にある一組のプライベート IP アドレスです。 これにより、ワークスペースへのアクセスが、プライベート IP アドレスでのみ行われるように制限できます。 Private Link を使用すると、データ窃盗のリスクを軽減できます。

プライベート リンク ワークスペースの設定の詳細については、「[Private Link を構成する方法](how-to-configure-private-link.md)」を参照してください。

> [!Warning]
> プライベート エンドポイントを使用してワークスペースをセキュリティで保護しても、エンドツーエンドのセキュリティは保証されません。 ソリューションの個々のコンポーネントをセキュリティで保護するには、この記事の残りの手順と VNet シリーズの手順に従う必要があります。

## <a name="secure-azure-storage-accounts-with-service-endpoints"></a>Azure のストレージ アカウントをサービス エンドポイントで保護する

Azure Machine Learning では、サービス エンドポイントまたはプライベート エンドポイントを使用するようにストレージ アカウントを構成できます。 このセクションでは、サービス エンドポイントを使用して Azure ストレージ アカウントをセキュリティで保護する方法について説明します。 プライベート エンドポイントについては、次のセクションを参照してください。

仮想ネットワーク内のワークスペースに Azure ストレージ アカウントを使用するには、次の手順を使用します。

1. Azure portal で、ワークスペースで使用するストレージ サービスに移動します。

   [![Azure Machine Learning のワークスペースにアタッチされているストレージ](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. ストレージのサービス アカウント ページで、 __[ネットワーク]__ を選択します。

   ![Azure portal 内の Azure Storage ページの [ネットワーク] 領域](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. __[ファイアウォールと仮想ネットワーク]__ タブで、次の操作を行います。
    1. __[選択されたネットワーク]__ を選択します。
    1. __[仮想ネットワーク]__ で、 __[Add existing virtual network]\(既存の仮想ネットワークを追加\)__ というリンクを選択します。 この操作により、コンピューティングが置かれている仮想ネットワークが追加されます (手順 1 参照)。

        > [!IMPORTANT]
        > ストレージ アカウントは、トレーニングまたは推論に使用されるコンピューティング インスタンスまたはクラスターと同じ仮想ネットワークとサブネット内に存在する必要があります。

    1. __[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]__ チェック ボックスをオンにします。 この変更は、すべての Azure サービスにユーザーのストレージ アカウントへのアクセス許可を与えるものではありません。
    
        * 一部のサービスのリソースは、**ユーザーのサブスクリプションで登録されている場合に**、特定の操作のために **同じサブスクリプション内の** ストレージ アカウントにアクセスできます。 たとえば、ログの書き込みやバックアップの作成などです。
        * 一部のサービスのリソースでは、そのシステム割り当てマネージド ID に __Azure ロールを割り当てる__ ことで、ストレージ アカウントへのアクセスを明示的に許可できます。

        詳細については、[Azure Storage ファイアウォールおよび仮想ネットワークの構成](../storage/common/storage-network-security.md#trusted-microsoft-services)に関する記事を参照してください。

    > [!IMPORTANT]
    > Azure Machine Learning SDK を使用する場合は、開発環境を Azure Storage アカウントに接続できる必要があります。 ストレージ アカウントが仮想ネットワーク内にある場合、ファイアウォールでは開発環境の IP アドレスからのアクセスを許可する必要があります。
    >
    > ストレージ アカウントへのアクセスを有効にするには、*開発クライアントの Web ブラウザーから* ストレージ アカウントの __[ファイアウォールと仮想ネットワーク]__ にアクセスします。 次に、 __[クライアント IP アドレスを追加する]__ チェック ボックスを使用して、クライアントの IP アドレスを __[アドレス範囲]__ に追加します。 また、 __[アドレス範囲]__ フィールドを使用して、開発環境の IP アドレスを手動で入力することもできます。 クライアントの IP アドレスが追加されると、SDK を使用してストレージ アカウントにアクセスできるようになります。

   [![Azure portal 内の [ファイアウォールと仮想ネットワーク] ウィンドウ](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="secure-azure-storage-accounts-with-private-endpoints"></a>Azure のストレージ アカウントをプライベート エンドポイントで保護する

Azure Machine Learning では、サービス エンドポイントまたはプライベート エンドポイントを使用するようにストレージ アカウントを構成できます。 ストレージ アカウントでプライベート エンドポイントが使用される場合、既定のストレージ アカウントにプライベート エンドポイントを 2 つ構成する必要があります。
1. **BLOB** ターゲット サブリソースのあるプライベート エンドポイント。
1. **ファイル** ターゲット サブリソースのあるプライベート エンドポイント (ファイル共有)。

![プライベート エンドポイント構成ページのスクリーンショット。BLOB オプションとファイル オプションを確認できます。](./media/how-to-enable-studio-virtual-network/configure-storage-private-endpoint.png)

既定のストレージでは **ない** ストレージ アカウントにプライベート エンドポイントを構成するには、追加するストレージ アカウントに相当する **ターゲット サブリソース** タイプを選択します。

詳細については、「[Azure Storage のプライベート エンドポイントを使用する](../storage/common/storage-private-endpoints.md)」を参照してください。

## <a name="secure-datastores-and-datasets"></a>データストアとデータセットをセキュリティで保護する

このセクションでは、仮想ネットワークの SDK エクスペリエンスでデータストアとデータセットを使用する方法について説明します。 スタジオ エクスペリエンスの詳細については、[仮想ネットワークで Azure Machine Learning スタジオを使用する](how-to-enable-studio-virtual-network.md)方法に関するページを参照してください。

SDK を使用してデータにアクセスするには、データが格納されている個々のサービスで求められる認証方法を使用する必要があります。 たとえば、Azure Data Lake Store Gen2 にアクセスするためにデータストアを登録する場合は、「[Azure Storage サービスに接続する](how-to-access-data.md#azure-data-lake-storage-generation-2)」に記載されているサービス プリンシパルを引き続き使用する必要があります。

### <a name="disable-data-validation"></a>データの検証を無効にする

既定では、SDK を使用してデータにアクセスしようとすると、Azure Machine Learning によってデータの有効性と資格情報のチェックが実行されます。 データが仮想ネットワークの背後にある場合、Azure Machine Learning はこれらのチェックを完了できません。 このチェックを回避するには、検証をスキップするデータストアとデータセットを作成する必要があります。

### <a name="use-datastores"></a>データストアを使用する

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

### <a name="use-datasets"></a>データセットを使用する

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

## <a name="secure-azure-key-vault"></a>Azure Key Vault をセキュリティで保護する

Azure Machine Learning では、関連付けられた Key Vault インスタンスを使用して、次の資格情報が格納されます。
* 関連付けられたストレージ アカウントの接続文字列
* Azure コンテナー リポジトリ インスタンスへのパスワード
* データ ストアへの接続文字列

仮想ネットワークの内側で、Azure Machine Learning 実験機能を Azure Key Vault で使用するには、次の手順を使用します。

1. ワークスペースに関連付けられている Key Vault に移動します。

1. __[Key Vault]__ ページの左側のウィンドウで、 __[ネットワーク]__ を選択します。

1. __[ファイアウォールと仮想ネットワーク]__ タブで、次の操作を行います。
    1. __[許可するアクセス元]__ で __[プライベート エンドポイントと選択されたネットワーク]__ を選択します。
    1. __[仮想ネットワーク]__ で、 __[既存の仮想ネットワークを追加]__ を選択して、実験のコンピューティングが存在する仮想ネットワークを追加します。
    1. __[Allow trusted Microsoft services to bypass this firewall]\(信頼された Microsoft サービスがこのファイアウォールをバイパスすることを許可する\)__ で、 __[はい]__ を選択します。

   [![[キー コンテナー] ウィンドウの [ファイアウォールと仮想ネットワーク] セクション](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="enable-azure-container-registry-acr"></a>Azure Container Registry (ACR) の有効化

仮想ネットワーク内で Azure Container Registry を使用するには、次の要件を満たしている必要があります。

* Azure Container Registry が Premium バージョンである必要があります。 アップグレードの詳細については、「[SKU の変更](../container-registry/container-registry-skus.md#changing-tiers)」を参照してください。

* トレーニングまたは推論に使用されるストレージ アカウントとコンピューティング ターゲットと同じ仮想ネットワークとサブネット内に Azure Container Registry が存在している必要があります。

* Azure Machine Learning ワークスペースに、[Azure Machine Learning コンピューティング クラスター](how-to-create-attach-compute-cluster.md)が含まれている必要があります。

    ACR が仮想ネットワークの背後にある場合、Azure Machine Learning でそれを使用して Docker イメージを直接構築することはできません。 代わりに、コンピューティング クラスターを使用してイメージが構築されます。

    > [!IMPORTANT]
    > Docker イメージの作成に使用されるコンピューティング クラスターは、モデルのトレーニングとデプロイに使用されるパッケージ リポジトリにアクセスできる必要があります。 パブリック リポジトリへのアクセスを許可するネットワーク セキュリティ規則の追加、[プライベート Python パッケージの使用](how-to-use-private-python-packages.md)、またはパッケージが既に含まれている [カスタム Docker イメージ](how-to-train-with-custom-image.md) の使用が必要になる場合があります。

これらの要件が満たされたら、次の手順を使用して Azure Container Registry を有効にします。

> [!TIP]
> ワークスペースの作成時に既存の Azure Container Registry を使用しなかった場合、それが存在しない可能性があります。 既定では、必要になるまで、ワークスペースによって ACR インスタンスが作成されることはありません。 強制的に作成するには、このセクションの手順を使用する前に、ワークスペースを使用してモデルをトレーニングまたはデプロイします。

1. 次の方法のいずれかを使用して、お使いのワークスペースの Azure Container Registry の名前を見つけます。

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

1. ワークスペースの ACR を構成し、[信頼されたサービスによるアクセスを許可](../container-registry/allow-access-trusted-services.md)します。

1. Azure Machine Learning Python SDK を使用して、Docker イメージを構築するようにコンピューティング クラスターを構成します。 次のコード スニペットで、これを実行する方法を示します。

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    # To switch back to using ACR to build (if ACR is not in the VNet):
    # ws.update(image_build_compute = None)
    ```

    > [!IMPORTANT]
    > ストレージ アカウント、コンピューティング クラスター、および Azure Container Registry のすべてが、仮想ネットワークと同じサブネット内に存在する必要があります。
    
    詳細については、[update()](/python/api/azureml-core/azureml.core.workspace.workspace#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-) メソッド リファレンスを参照してください。

## <a name="next-steps"></a>次のステップ

この記事は、全 5 パートからなる仮想ネットワーク シリーズのパート 2 です。 仮想ネットワークをセキュリティで保護する方法については、記事の残りの部分を参照してください。

* [パート 1: 仮想ネットワークの概要](how-to-network-security-overview.md)
* [パート 3: トレーニング環境をセキュリティで保護する](how-to-secure-training-vnet.md)
* [パート 4: 推論環境をセキュリティで保護する](how-to-secure-inferencing-vnet.md)
* [パート 5: スタジオの機能を有効にする](how-to-enable-studio-virtual-network.md)

名前の解決については、[カスタム DNS](how-to-custom-dns.md) の使用に関する記事も参照してください。