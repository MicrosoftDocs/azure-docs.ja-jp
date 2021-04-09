---
title: ファイアウォールを使用する
titleSuffix: Azure Machine Learning
description: Azure Firewall を使用して Azure Machine Learning ワークスペースへのアクセスを制御します。 ファイアウォールの通過を許可する必要があるホストについて説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 11/18/2020
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 295228e9eaa3529b05055869bd46f9aefc938a6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102212775"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>ファイアウォールの内側で Azure Machine Learning のワークスペースを使用する

この記事では、Azure Firewall を構成して、Azure Machine Learning ワークスペースとパブリック インターネットへのアクセスを制御する方法について説明します。 Azure Machine Learning のセキュリティ保護の詳細については、[Azure Machine Learning のエンタープライズ セキュリティ](concept-enterprise-security.md)に関するページを参照してください。

> [!WARNING]
> ファイアウォールの背後にあるデータ ストレージへのアクセスは、コード ファースト エクスペリエンスでのみサポートされます。 [Azure Machine Learning スタジオ](overview-what-is-machine-learning-studio.md)を使用してファイアウォールの背後にあるデータにアクセスすることはサポートされていません。 スタジオを使用してプライベート ネットワーク上のデータ ストレージを操作するには、まず[仮想ネットワークを設定し](../virtual-network/quick-create-portal.md)、[仮想ネットワーク内に格納されているデータへのアクセス権をスタジオに付与する](how-to-enable-studio-virtual-network.md)必要があります。

## <a name="azure-firewall"></a>Azure Firewall

Azure Firewall を使用する場合は、__宛先ネットワーク アドレス変換 (DNAT)__ を使用して受信トラフィックの NAT 規則を作成します。 送信トラフィックの場合は、__ネットワーク__ や __アプリケーション__ の規則を作成します。 これらの規則コレクションの詳細については、「[Azure Firewall の概念をいくつか教えてください](../firewall/firewall-faq.yml#what-are-some-azure-firewall-concepts)」を参照してください。

### <a name="inbound-configuration"></a>受信の構成

Azure Machine Learning __コンピューティング インスタンス__ または __コンピューティング クラスター__ を使用する場合は、Azure Machine Learning リソースを含むサブネットの [ユーザー定義ルート (UDR)](../virtual-network/virtual-networks-udr-overview.md) を追加します。 このルートによって、`BatchNodeManagement` と `AzureMachineLearning` リソースの IP アドレス __から__ コンピューティング インスタンスとコンピューティング クラスターのパブリック IP にトラフィックが強制的に送信されます。

これらの UDR により、Batch サービスが、タスクをスケジュールする目的でプールのコンピューティング ノードと通信できるようになります。 コンピューティング インスタンスへのアクセスに必要なため、Azure Machine Learning service の IP アドレスも追加します。 Azure Machine Learning service の IP アドレスを追加する場合は、__プライマリとセカンダリ__ の両方の Azure リージョンに IP を追加する必要があります。 プライマリ リージョンは、ワークスペースが配置されているところです。

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

詳細については、「[仮想ネットワーク内に Azure Batch プールを作成する](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling)」を参照してください。

### <a name="outbound-configuration"></a>送信の構成

1. 次のサービス タグ __への__、またサービス タグ __からの__ トラフィックを許可する __ネットワーク規則__ を追加します。

    * AzureActiveDirectory
    * AzureMachineLearning
    * AzureResourceManager
    * Storage.region
    * KeyVault.region
    * ContainerRegistry.region

    Microsoft から提供される既定の Docker イメージを使用し、ユーザー マネージドの依存関係を有効にする場合は、次のサービス タグも使用する必要があります。

    * MicrosoftContainerRegistry.region
    * AzureFrontDoor.FirstParty

    `region` が含まれているエントリの場合は、使用している Azure リージョンに置き換えます。 たとえば、「 `keyvault.westus` 」のように入力します。

    __プロトコル__ には、`TCP` を選択します。 送信元と送信先の __ポート__ には、`*` を選択します。

1. 以下のホストに __アプリケーション規則__ を追加します。

    > [!NOTE]
    > これは、インターネット上のすべての Python リソースに必要なホストの完全な一覧ではなく、最も一般的に使用されているもののみを取り上げています。 たとえば、GitHub リポジトリまたはその他のホストにアクセスする必要がある場合は、そのシナリオに必要なホストを特定して追加する必要があります。

    | **ホスト名** | **目的** |
    | ---- | ---- |
    | **graph.windows.net** | Azure Machine Learning コンピューティング インスタンスまたはクラスターによって使用されます。 |
    | **anaconda.com**</br>**\*.anaconda.com** | 既定のパッケージをインストールするために使用されます。 |
    | **\*.anaconda.org** | リポジトリ データを取得するために使用されます。 |
    | **pypi.org** | 既定のインデックスからの依存関係 (存在する場合) を一覧表示するために使用されます。ユーザー設定によってこのインデックスが上書きされることはありません。 インデックスが上書きされる場合は、 **\*pythonhosted.org** も許可する必要があります。 |
    | **cloud.r-project.org** | R 開発用の CRAN パッケージをインストールするときに使用されます。 |
    | **\*pytorch.org** | PyTorch に基づくいくつかのサンプルによって使用されます。 |
    | **\*.tensorflow.org** | Tensorflow に基づくいくつかのサンプルによって使用されます。 |

    __プロトコル/ポート__ には、__http、https__ の使用を選択します。

    アプリケーション規則の構成について詳しくは、[Azure Firewall のデプロイと構成](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule)に関する記事をご覧ください。

1. Azure Kubernetes Service (AKS) にデプロイされたモデルへのアクセスを制限するには、[Azure Kubernetes Service でのエグレス トラフィックの制限](../aks/limit-egress-traffic.md)に関する記事を参照してください。

## <a name="other-firewalls"></a>その他のファイアウォール

このセクションのガイダンスは一般的なもので、各ファイアウォールには独自の用語や特定の構成があります。 ファイアウォール経由の通信を許可する方法について不明な点がある場合は、使用しているファイアウォールのドキュメントを参照してください。

正しく構成しないと、ワークスペースを使用したときにファイアウォールが原因で問題が発生する可能性があります。 いずれも Azure Machine Learning ワークスペースで使用されるさまざまなホスト名があります。 次のセクションでは、Azure Machine Learning に必要なホストの一覧を示します。

### <a name="microsoft-hosts"></a>Microsoft のホスト

このセクションのホストは Microsoft によって所有されており、ホストではワークスペースが適切に機能するために必要なサービスが提供されます。 次の表は、Azure Public、Azure Government、Azure China 21Vianet リージョンのホスト名を一覧表示しています。

**一般的な Azure ホスト**

| **次のために必須:** | **Azure Public** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Active Directory | login.microsoftonline.com | login.microsoftonline.us | login.chinacloudapi.cn |
| Azure portal | management.azure.com | management.azure.us | management.azure.cn |
| Azure Resource Manager | management.azure.com | management.usgovcloudapi.net | management.chinacloudapi.cn |

**Azure Machine Learning ホスト**

| **次のために必須:** | **Azure Public** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Machine Learning Studio | ml.azure.com | ml.azure.us | studio.ml.azure.cn |
| API |\*.azureml.ms | \*.ml.azure.us | \*.ml.azure.cn |
| 実験、履歴、Hyperdrive、ラベル付け | \*.experiments.azureml.net | \*.ml.azure.us | \*.ml.azure.cn |
| モデル管理 | \*.modelmanagement.azureml.net | \*.ml.azure.us | \*.ml.azure.cn |
| パイプライン | \*.aether.ms | \*.ml.azure.us | \*.ml.azure.cn |
| デザイナー (スタジオ サービス) | \*.studioservice.azureml.com | \*.ml.azure.us | \*.ml.azure.cn |
| 統合されたノートブック | \*.notebooks.azure.net | \*.notebooks.usgovcloudapi.net |\*.notebooks.chinacloudapi.cn |
| 統合されたノートブック | \*.file.core.windows.net | \*.file.core.usgovcloudapi.net | \*.file.core.chinacloudapi.cn |
| 統合されたノートブック | \*.dfs.core.windows.net | \*.dfs.core.usgovcloudapi.net | \*.dfs.core.chinacloudapi.cn |
| 統合されたノートブック | \*.blob.core.windows.net | \*.blob.core.usgovcloudapi.net | \*.blob.core.chinacloudapi.cn |
| 統合されたノートブック | graph.microsoft.com | graph.microsoft.us | graph.chinacloudapi.cn |
| 統合されたノートブック | \*.aznbcontent.net |  | |

**Azure Machine Learning コンピューティング インスタンスおよびコンピューティング クラスターのホスト**

| **次のために必須:** | **Azure Public** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| コンピューティング クラスター/インスタンス | \*.batchai.core.windows.net | \*.batchai.core.usgovcloudapi.net |\*.batchai.ml.azure.cn |
| コンピューティング クラスター/インスタンス | graph.windows.net | graph.windows.net | graph.chinacloudapi.cn |
| コンピューティング インスタンス | \*.instances.azureml.net | \*.instances.azureml.us | \*.instances.azureml.cn |
| コンピューティング インスタンス | \*.instances.azureml.ms |  |  |

**Azure Machine Learning によって使用される関連リソース**

| **次のために必須:** | **Azure Public** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Storage アカウント | core.windows.net | core.usgovcloudapi.net | core.chinacloudapi.cn |
| Azure Key Vault | vault.azure.net | vault.usgovcloudapi.net | vault.azure.cn |
| Azure Container Registry | azurecr.io | azurecr.us | azurecr.cn |
| Microsoft Container Registry | mcr.microsoft.com | mcr.microsoft.com | mcr.microsoft.com |


> [!TIP]
> フェデレーション ID を使用する予定の場合は、「[Active Directory フェデレーション サービス (AD FS) をセキュリティで保護するためのベスト プラクティス](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs)」の記事に従ってください。

また、[強制トンネリング](how-to-secure-training-vnet.md#forced-tunneling)の情報を使用して、`BatchNodeManagement` と `AzureMachineLearning` の IP アドレスを追加します。

Azure Kubernetes Service (AKS) にデプロイされたモデルへのアクセスの制限については、[Azure Kubernetes Service でのエグレス トラフィックの制限](../aks/limit-egress-traffic.md)に関する記事を参照してください。

### <a name="python-hosts"></a>Python のホスト

このセクションのホストは、Python パッケージをインストールするために使用されます。 開発、トレーニング、デプロイ時に必要になります。 

> [!NOTE]
> これは、インターネット上のすべての Python リソースに必要なホストの完全な一覧ではなく、最も一般的に使用されているもののみを取り上げています。 たとえば、GitHub リポジトリまたはその他のホストにアクセスする必要がある場合は、そのシナリオに必要なホストを特定して追加する必要があります。

| **ホスト名** | **目的** |
| ---- | ---- |
| **anaconda.com**</br>**\*.anaconda.com** | 既定のパッケージをインストールするために使用されます。 |
| **\*.anaconda.org** | リポジトリ データを取得するために使用されます。 |
| **pypi.org** | 既定のインデックスからの依存関係 (存在する場合) を一覧表示するために使用されます。ユーザー設定によってこのインデックスが上書きされることはありません。 インデックスが上書きされる場合は、 **\*pythonhosted.org** も許可する必要があります。 |
| **\*pytorch.org** | PyTorch に基づくいくつかのサンプルによって使用されます。 |
| **\*.tensorflow.org** | Tensorflow に基づくいくつかのサンプルによって使用されます。 |

### <a name="r-hosts"></a>R のホスト

このセクションのホストは、R パッケージをインストールするために使用されます。 開発、トレーニング、デプロイ時に必要になります。

> [!NOTE]
> これは、インターネット上のすべての R リソースに必要なホストの完全な一覧ではなく、最も一般的に使用されているもののみを取り上げています。 たとえば、GitHub リポジトリまたはその他のホストにアクセスする必要がある場合は、そのシナリオに必要なホストを特定して追加する必要があります。

| **ホスト名** | **目的** |
| ---- | ---- |
| **cloud.r-project.org** | CRAN パッケージをインストールするときに使用されます。 |

> [!IMPORTANT]
> 内部的には、Azure Machine Learning 用の R SDK では Python パッケージが使用されます。 そのため、Python ホストにもファイアウォールの通過を許可する必要があります。
## <a name="next-steps"></a>次のステップ

* [チュートリアル:Azure portal を使用して Azure Firewall をデプロイして構成する](../firewall/tutorial-firewall-deploy-portal.md)
* [Azure Virtual Network 内で Azure ML の実験と推論のジョブを安全に実行する](how-to-network-security-overview.md)
