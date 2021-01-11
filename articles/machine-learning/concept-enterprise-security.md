---
title: エンタープライズ セキュリティ
titleSuffix: Azure Machine Learning
description: 'Azure Machine Learning を安全に使用します: 認証、認可、ネットワーク セキュリティ、データ暗号化、監視。'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 05/19/2020
ms.openlocfilehash: ed95cf0b98edd8a6775c980876a6092c00e3a68d
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88918589"
---
# <a name="enterprise-security-for-azure-machine-learning"></a>Azure Machine Learning のエンタープライズ セキュリティ

この記事では、Azure Machine Learning で利用できるセキュリティ機能について学習します。

クラウド サービスを使用する場合は、アクセスを必要とするユーザーのみに制限することをお勧めします。 まず、サービスによって使用される認証および認可モデルについて理解します。 ネットワーク アクセスを制限したり、オンプレミス ネットワークのリソースをクラウドと安全に結合したりすることも必要になる場合があります。 保存時とサービス間の移動時の両方でデータを暗号化することも不可欠です。 最後に、サービスを監視し、すべてのアクティビティの監査ログを生成できることも必要です。

> [!NOTE]
> この記事の情報は、Azure Machine Learning Python SDK バージョン 1.0.83.1 以降に対応します。

## <a name="authentication"></a>認証

多要素認証がサポートされるのは、それを使用するように Azure Active Directory (Azure AD) が構成されている場合です。 以下に認証プロセスを示します。

1. クライアントでは、Azure AD にサインインして、Azure Resource Manager トークンを取得します。  ユーザーとサービス プリンシパルは完全にサポートされています。
1. クライアントによって、Azure Resource Manager とすべての Azure Machine Learning にトークンが提示されます。
1. Machine Learning service では、ユーザーのコンピューティング先 (たとえば、Machine Learning コンピューティング) に Machine Learning service トークンが提供されます。 このトークンは、実行の完了後に Machine Learning service にコールバックするために、ユーザーのコンピューティング ターゲットによって使用されます。 スコープはワークスペースに制限されます。

[![Azure Machine Learning での認証](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

詳細については、「[Azure Machine Learning のリソースとワークフローの認証を設定する](how-to-setup-authentication.md)」を参照してください。 この記事では、サービス プリンシパルや自動化ワークフローの使用など、認証に関する情報と例を提供します。

### <a name="authentication-for-web-service-deployment"></a>Web サービスのデプロイ用の認証

Azure Machine Learning では、Web サービスに関してキーとトークンの 2 つの認証形式がサポートされます。 各 Web サービスで有効にできる認証形式は一度に 1 つのみです。

|認証方法|説明|Azure Container Instances|AKS|
|---|---|---|---|
|Key|キーは静的であり、更新する必要はありません。 キーは手動で再生成できます。|既定で無効| 既定で有効|
|トークン|トークンは、指定した期間が経過すると期限切れになり、更新する必要があります。| 使用不可| 既定で無効 |

コード例については、[「Web サービス認証」のセクション](how-to-setup-authentication.md#web-service-authentication)を参照してください。

## <a name="authorization"></a>承認

複数のワークスペースを作成でき、各ワークスペースを複数のユーザーで共有できます。 ワークスペースを共有する場合は、これらのロールをユーザーに割り当てることで、ワークスペースへのアクセスを制御できます。

* 所有者
* Contributor
* Reader

次の表では、いくつかの主要な Azure Machine Learning の操作とそれらを実行できるロールを示します。

| Azure Machine Learning の操作 | 所有者 | Contributor | Reader |
| ---- |:----:|:----:|:----:|
| ワークスペースの作成 | ✓ | ✓ | |
| ワークスペースを共有する | ✓ | |  |
| ワークスペースを Enterprise Edition にアップグレードする | ✓ | |
| コンピューティング先を作成する | ✓ | ✓ | |
| コンピューティング先をアタッチする | ✓ | ✓ | |
| データ ストアをアタッチする | ✓ | ✓ | |
| 実験を実行する | ✓ | ✓ | |
| 実行/メトリックを表示する | ✓ | ✓ | ✓ |
| モデルの登録 | ✓ | ✓ | |
| イメージを作成する | ✓ | ✓ | |
| Web サービスのデプロイ | ✓ | ✓ | |
| モデル/イメージを表示する | ✓ | ✓ | ✓ |
| Web サービスを呼び出す | ✓ | ✓ | ✓ |

組み込みのロールがニーズを満たしていない場合は、カスタムのロールを作成できます。 カスタム ロールは、コンピューティングの作成、実行の送信、データストアの登録、モデルの配置など、ワークスペース内のすべての操作を制御するためにサポートされています。 カスタム ロールには、クラスター、データストア、モデル、エンドポイントなど、ワークスペースのさまざまなリソースに対する読み取り、書き込み、または削除のアクセス許可を付与することができます。 ロールは、特定のワークスペース レベル、特定のリソース グループ レベル、または特定のサブスクリプション レベルで使用できるようにすることができます。 詳細については、[Azure Machine Learning ワークスペースでのユーザーとロールの管理](how-to-assign-roles.md)に関するページを参照してください。

> [!WARNING]
> Azure Machine Learning は Azure Active Directory の B2B の共同作業ではサポートされていますが、現時点では Azure Active Directory の B2C の共同作業ではサポートされていません。

### <a name="securing-compute-targets-and-data"></a>コンピューティング先とデータのセキュリティ保護

所有者と共同作成者は、ワークスペースにアタッチされているすべてのコンピューティング ターゲットとデータ ストアを使用できます。  

各ワークスペースには、ワークスペースと同じ名前を持つ、関連付けられたシステム割り当て済みマネージド ID もあります。 マネージド ID には、ワークスペースで使用されるアタッチされたリソースに対する次のアクセス許可があります。

マネージド ID の詳細については、[Azure リソースのマネージド ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) に関するページを参照してください。

| リソース | アクセス許可 |
| ----- | ----- |
| ワークスペース | Contributor |
| ストレージ アカウント | ストレージ BLOB データ共同作成者 |
| Key Vault | すべてのキー、シークレット、証明書へのアクセス |
| Azure Container Registry | Contributor |
| ワークスペースを含むリソース グループ | Contributor |
| キー コンテナーを含むリソース グループ (ワークスペースを含むものと異なる場合) | Contributor |

管理者が上記の表のリソースに対するマネージド ID のアクセスを取り消すことはお勧めできません。 キーの再同期操作を使用して、アクセスを復元できます。

Azure Machine Learning では、すべてのワークスペース リージョンのサブスクリプションに、共同作成者レベルのアクセス権を持つ追加のアプリケーション (名前が `aml-` または `Microsoft-AzureML-Support-App-` で始まるもの) が作成されます。 たとえば、同じサブスクリプション内で、米国東部に 1 つのワークスペースがあり、北ヨーロッパに 1 つのワークスペースがある場合、これらのアプリケーションが 2 つ表示されます。 これらのアプリケーションでは、コンピューティング リソースの管理に役立つ Azure Machine Learning を有効にします。

## <a name="network-security"></a>ネットワークのセキュリティ

Azure Machine Learning は、コンピューティング リソースに関して他の Azure サービスに依存します。 コンピューティング リソース (コンピューティング ターゲット) は、モデルのトレーニングとデプロイに使用されます。 これらのコンピューティング先は、仮想ネットワーク内に作成することができます。 たとえば、Azure Data Science Virtual Machine を使用してモデルをトレーニングしてから、そのモデルを AKS にデプロイできます。  

詳しくは、[分離された仮想ネットワークで実験と推論を安全に実行する方法](how-to-enable-virtual-network.md)に関するページをご覧ください。

また、ワークスペースに対して Azure Private Link を有効にすることもできます。 Private Link を使用すると、Azure Virtual Network からワークスペースへの通信を制限することができます。 詳細については、[Private Link を構成する方法](how-to-configure-private-link.md)に関するページを参照してください。

## <a name="data-encryption"></a>データの暗号化

> [!IMPORTANT]
> __トレーニング__ 中の運用グレードの暗号化の場合、Microsoft は Azure Machine Learning コンピューティング クラスターを使用することをお勧めします。 __推論__ 中の運用グレードの暗号化の場合、Microsoft は Azure Kubernetes Service を使用することをお勧めします。
>
> Azure Machine Learning コンピューティング インスタンスは、開発またはテスト環境です。 これを使用する場合は、ノートブックやスクリプトなどのファイルをファイル共有に保存することをお勧めします。 データは、データストアに格納する必要があります。

### <a name="encryption-at-rest"></a>保存時の暗号化

> [!IMPORTANT]
> ワークスペースに機密データが含まれている場合は、ワークスペースの作成時に [hbi_workspace フラグ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-)を設定することをお勧めします。 `hbi_workspace` フラグは、ワークスペースの作成時にのみ設定できます。 既存のワークスペースに対して変更することはできません。

`hbi_workspace` フラグにより、[Microsoft が診断目的で収集するデータ](#microsoft-collected-data)の量が制御され、[Microsoft が管理する環境での追加の暗号化](../security/fundamentals/encryption-atrest.md)が可能になります。 さらに、次のアクションが可能になります。

* そのサブスクリプションで以前にクラスターを作成していない場合に、Azure Machine Learning コンピューティング クラスターでローカル スクラッチ ディスクの暗号化を開始します。 それ以外の場合は、コンピューティング クラスターのスクラッチ ディスクの暗号化を有効にするためにサポート チケットを作成する必要があります。 
* 実行間でローカル スクラッチ ディスクをクリーンアップします
* Key Vault を使用して、ストレージ アカウント、コンテナー レジストリ、SSH アカウントの資格情報を実行層からコンピューティング クラスターに安全に渡します。
* AzureMachineLearningService 以外の外部サービスから基になる Batch プールを呼び出すことができないように、IP フィルタリングを有効にします。

#### <a name="azure-blob-storage"></a>Azure BLOB ストレージ

Azure Machine Learning では、Azure Machine Learning ワークスペースとサブスクリプションに関連付けられている Azure BLOB ストレージ アカウントにスナップショット、出力、ログを格納します。 Azure BLOB ストレージに格納されるすべてのデータは、Microsoft によって管理されたキーを使用して保存時に暗号化されます。

Azure BLOB ストレージに格納されるデータに独自のキーを使用する方法については、[Azure Key Vault でのカスタマー マネージド キーによる Azure Storage の暗号化](../storage/common/storage-encryption-keys-portal.md)に関するページを参照してください。

通常はトレーニング データも、トレーニング コンピューティング先にアクセスできるように Azure BLOB ストレージに格納されます。 このストレージは、Azure Machine Learning によって管理されませんが、リモート ファイル システムとしてコンピューティング先にマウントされます。

キーを __交換または取り消す__ 必要がある場合は、いつでもこの操作を行うことができます。 キーを交換すると、ストレージ アカウントは、新しいキー (最新バージョン) を使用して保存データを暗号化します。 キーを取り消す (無効にする) と、ストレージ アカウントは失敗した要求を処理します。 通常、交換または失効が有効になるまでに 1 時間かかります。

アクセス キーを再生成する方法の詳細については、「[ストレージ アカウント アクセス キーの再生成](how-to-change-storage-access-key.md)」を参照してください。

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Machine Learning では、Azure Cosmos DB インスタンスにメトリックとメタデータが格納されます。 このインスタンスは、Azure Machine Learning によって管理される Microsoft サブスクリプションに関連付けられています。 Azure Cosmos DB に格納されているすべてのデータは、Microsoft によって管理されるキーを使用して保存時に暗号化されます。

独自の (カスタマーマネージド) キーを使用して Azure Cosmos DB インスタンスを暗号化する場合は、ワークスペースで使用する専用の Cosmos DB インスタンスを作成できます。 実行履歴情報などのデータを、Microsoft サブスクリプションでホストされているマルチテナント Cosmos DB インスタンスの外部に格納する場合は、この方法をお勧めします。 

カスタマー マネージド キーを使用してサブスクリプションに Cosmos DB インスタンスをプロビジョニングできるようにするには、次の操作を実行します。

* Microsoft.MachineLearning と Microsoft.DocumentDB リソース プロバイダーをサブスクリプションに登録します (まだ行っていない場合)。

* Azure Machine Learning ワークスペースを作成するときは、次のパラメーターを使用します。 SDK、CLI、REST API、および Resource Manager のテンプレートでは、両方のパラメーターが必須であり、サポートされています。

    * `resource_cmk_uri`:このパラメーターは、[キーのバージョン情報](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning)を含む、キー コンテナー内のカスタマー マネージド キーの完全なリソース URI です。 

    * `cmk_keyvault`:このパラメーターは、サブスクリプション内のキー コンテナーのリソース ID です。 このキー コンテナーは、Azure Machine Learning ワークスペースに使用するのと同じリージョンおよびサブスクリプションにある必要があります。 
    
        > [!NOTE]
        > このキー コンテナー インスタンスは、ワークスペースをプロビジョニングするときに Azure Machine Learning によって作成されるキー コンテナーとは異なる場合があります。 ワークスペースに同じキー コンテナー インスタンスを使用する場合は、[key_vault パラメーター](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-)を使用してワークスペースをプロビジョニングするときに、同じキー コンテナーを渡します。 

この Cosmos DB インスタンスは、サブスクリプション内の Microsoft が管理対象リソース グループに作成されます。 マネージド リソース グループの名前は、`<AML Workspace Resource Group Name><GUID>` という形式で指定されます。

> [!IMPORTANT]
> * この Cosmos DB インスタンスを削除する必要がある場合は、それを使用する Azure Machine Learning ワークスペースを削除する必要があります。 
> * この Cosmos DB アカウントの既定の[__要求ユニット__](../cosmos-db/request-units.md)は __8000__ に設定されています。 この値の変更はサポートされていません。 

キーを __交換または取り消す__ 必要がある場合は、いつでもこの操作を行うことができます。 キーを交換すると、Cosmos DB は新しいキー (最新バージョン) を使用して保存データを暗号化します。 キーを取り消す (無効にする) と、Cosmos DB は失敗した要求を処理します。 通常、交換または失効が有効になるまでに 1 時間かかります。

カスタマー マネージド キーと Cosmos DB の詳細については、[Azure Cosmos DB アカウントのカスタマー マネージド キーの構成](../cosmos-db/how-to-setup-cmk.md)に関する記事を参照してください。

#### <a name="azure-container-registry"></a>Azure Container Registry

レジストリ (Azure Container Registry) 内のすべてのコンテナー イメージは、保存時に暗号化されます。 Azure では、イメージは保存前に自動的に暗号化され、Azure Machine Learning がイメージをプルするときに暗号解除されます。

独自の (カスタマー マネージド) キーを使用して Azure Container Registry を暗号化するには、独自の ACR を作成し、ワークスペースのプロビジョニング中にアタッチするか、ワークスペースのプロビジョニング時に作成された既定のインスタンスを暗号化する必要があります。

> [!IMPORTANT]
> Azure Machine Learning を使用するには、Azure Container Registry で管理者アカウントが有効になっている必要があります。 既定では、コンテナー レジストリを作成するときに、この設定は無効になっています。 管理者アカウントを有効にする方法の詳細については、「[管理者アカウント](/azure/container-registry/container-registry-authentication#admin-account)」を参照してください。
>
> いったん、Azure Container Registry をワークスペースに対して作成したら、削除しないでください。 それを行うと、Azure Machine Learning ワークスペースが破損します。

既存の Azure Container Registry を使用してワークスペースを作成する例については、次の記事を参照してください。

* [Azure CLI を使用して Azure Machine Learning のワークスペースを作成する](how-to-manage-workspace-cli.md)。
* [Azure Resource Manager テンプレートを使用して Azure Machine Learning のワークスペースを作成する](how-to-create-workspace-template.md)

#### <a name="azure-container-instance"></a>Azure Container Instances

カスタマー マネージド キーを使用して、デプロイされた Azure Container Instance (ACI) リソースを暗号化することができます。 ACI に使用するカスタマー マネージド キーは、ワークスペースの Azure Key Vault に格納できます。 キーの生成の詳細については、「[カスタマー マネージド キーを使用してデータを暗号化する](../container-instances/container-instances-encrypt-data.md#generate-a-new-key)」を参照してください。

Azure Container Instance にモデルをデプロイするときにキーを使用するには、`AciWebservice.deploy_configuration()` を使用して新しいデプロイ構成を作成します。 次のパラメーターを使用して、キーの情報を指定します。

* `cmk_vault_base_url`:キーが含まれるキー コンテナーの URL。
* `cmk_key_name`:キーの名前です。
* `cmk_key_version`:キーのバージョン。

デプロイ構成の作成と使用の詳細については、次の記事を参照してください。

* [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-) 参照
* [デプロイする場所と方法](how-to-deploy-and-where.md)
* [Azure Container Instances にモデルをデプロイする](how-to-deploy-azure-container-instance.md)

カスタマー マネージド キーを ACI で使用する方法の詳細については、「[カスタマー マネージド キーを使用してデータを暗号化する](../container-instances/container-instances-encrypt-data.md#encrypt-data-with-a-customer-managed-key)」を参照してください。

#### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

デプロイされた Azure Kubernetes Service リソースは、カスタマー マネージド キーを使用していつでも暗号化できます。 詳細については、[Azure Kubernetes Service で独自のキーを取り込む](../aks/azure-disk-customer-managed-keys.md)方法に関するページを参照してください。 

このプロセスでは、Kubernetes クラスター内のデプロイされた仮想マシンのデータと OS ディスクの両方を暗号化できます。

> [!IMPORTANT]
> このプロセスは、AKS K8s バージョン 1.17 以降でのみ機能します。 Azure Machine Learning は、2020 年 1 月 13 日に AKS 1.17 のサポートを追加しました。

#### <a name="machine-learning-compute"></a>Machine Learning コンピューティング

Azure Storage に格納されている各コンピューティング ノードの OS ディスクは、Azure Machine Learning ストレージ アカウント内の Microsoft によって管理されるキーを使用して暗号化されます。 このコンピューティング先は一時的なものであり、キューに入れられた実行がない場合、通常はクラスターがスケールダウンされます。 基になる仮想マシンのプロビジョニングは解除され、OS ディスクは削除されます。 OS ディスクでは Azure Disk Encryption はサポートされません。

各仮想マシンにも、OS 操作用にローカルな一時ディスクがあります。 必要に応じて、ディスクを使用してトレーニング データをステージできます。 `hbi_workspace` パラメーターが `TRUE` に設定されているワークスペースでは、ディスクは既定で暗号化されます。 この環境は、実行中だけ有効期間が短く、暗号化のサポートはシステム管理キーのみに制限されています。

#### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks は Azure Machine Learning パイプラインで使用できます。 既定では、Azure Databricks によって使用される Databricks ファイル システム (DBFS) は、Microsoft が管理するキーを使用して暗号化されます。 顧客が管理するキーを使用するように Azure Databricks を構成するには、「[既定 (ルート) の DBFS で顧客が管理するキーを構成する](/azure/databricks/security/customer-managed-keys-dbfs)」を参照してください。

### <a name="encryption-in-transit"></a>転送中の暗号化

Azure Machine Learning では、さまざまな Azure Machine Learning マイクロサービス間の内部通信をセキュリティで保護するために、TLS を使用します。 すべての Azure Storage アクセスも、セキュリティで保護されたチャネル経由で行われます。

スコアリング エンドポイントへの外部呼び出しをセキュリティで保護するために、Azure Machine Learning では TLS が使用されます。 詳細については、「[TSL を使用して Azure Machine Learning による Web サービスをセキュリティで保護する](https://docs.microsoft.com/azure/machine-learning/how-to-secure-web-service)」を参照してください。

### <a name="using-azure-key-vault"></a>Azure Key Vault の使用

Azure Machine Learning では、ワークスペースに関連付けられた Azure Key Vault インスタンスを使用して、さまざまな種類の資格情報を格納します。

* 関連付けられたストレージ アカウントの接続文字列
* Azure コンテナー リポジトリ インスタンスへのパスワード
* データ ストアへの接続文字列

Azure HDInsight や VM などのコンピューティング先に対する SSH パスワードとキーは、Microsoft サブスクリプションに関連付けられている別のキー コンテナーに格納されます。 Azure Machine Learning には、ユーザーによって提供されるパスワードやキーは格納されません。 代わりに、VM と HDInsight に接続して実験を行うために、独自の SSH キーを生成、承認、および格納します。

各ワークスペースには、ワークスペースと同じ名前を持つ、関連付けられたシステム割り当て済みマネージド ID があります。 このマネージド ID では、キー コンテナー内のすべてのキー、シークレット、および証明書にアクセスできます。

## <a name="data-collection-and-handling"></a>データの収集と処理

### <a name="microsoft-collected-data"></a>Microsoft が収集したデータ

Microsoft は、リソース名 (データセット名や機械学習の実験名など)、または診断目的でのジョブ環境変数など、ユーザー以外を識別する情報を収集する場合があります。 このようなデータはすべて、Microsoft が所有するサブスクリプションでホストされているストレージに Microsoft が管理するキーを使用して保存され、[Microsoft の標準のプライバシー ポリシーとデータ処理規格](https://privacy.microsoft.com/privacystatement)に従います。

また、機密情報 (アカウント キー シークレットなど) を環境変数に保存しないこともお勧めしています。 環境変数は、Microsoft によってログに記録され、暗号化され、保存されます。 同様に、[run_id](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) に名前を付けるときは、ユーザー名や秘密のプロジェクト名などの機密情報を含めないようにしてください。 この情報は、Microsoft サポート エンジニアがアクセスできるテレメトリ ログに表示されることがあります。

ワークスペースのプロビジョニング中に `hbi_workspace` パラメーターを `TRUE` に設定して、収集される診断データをオプトアウトすることができます。 この機能は、AzureML Python SDK、CLI、REST API、または Azure Resource Manager のテンプレートを使用する場合にサポートされます。

### <a name="microsoft-generated-data"></a>Microsoft が生成したデータ

自動化された機械学習などのサービスを使用する場合、Microsoft では、複数のモデルをトレーニングするために、一時的な処理済みのデータを生成することがあります。 このデータはワークスペース内のデータストアに格納されます。これにより、アクセス制御と暗号化を適切に適用できます。

また、[デプロイされているエンドポイントからログ記録された診断情報](how-to-enable-app-insights.md)を Azure Application Insights インスタンスに暗号化することもできます。

## <a name="monitoring"></a>監視

### <a name="metrics"></a>メトリック

Azure Monitor メトリックを使用し、Azure Machine Learning ワークスペースのメトリックを表示したり、監視したりできます。 [Azure portal](https://portal.azure.com) で、ワークスペースを選び、 **[メトリック]** を選択します。

[![ワークスペースのメトリックの例を示すスクリーンショット](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

メトリックには、実行、デプロイ、および登録に関する情報が含まれます。

詳しくは、「[Metrics in Azure Monitor (Azure Monitor のメトリック)](/azure/azure-monitor/platform/data-platform-metrics)」をご覧ください。

### <a name="activity-log"></a>アクティビティ ログ

ワークスペースのアクティビティ ログを表示して、ワークスペース上で実行されるさまざまな操作を確認できます。 ログには、操作名、イベント イニシエーター、タイムスタンプなどの基本情報が含まれています。

このスクリーンショットは、ワークスペースのアクティビティ ログを示しています。

[![ワークスペースのアクティビティ ログを示すスクリーンショット](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

スコアリング要求の詳細は Application Insights に格納されます。 Application Insights は、ワークスペースの作成時にサブスクリプションに作成されます。 ログに記録される情報には、次のようなフィールドがあります。

* HTTPMethod
* UserAgent
* ComputeType
* RequestUrl
* StatusCode
* RequestId
* Duration

> [!IMPORTANT]
> Azure Machine Learning ワークスペース内の一部のアクションでは、情報はアクティビティ ログに記録されません。 たとえば、トレーニングの実行の開始やモデルの登録はログに記録されません。
>
> これらのアクションの一部はワークスペースの **[アクティビティ]** 領域に表示されますが、これらの通知ではアクティビティを開始したユーザーは示されません。

## <a name="data-flow-diagrams"></a>データ フロー図

### <a name="create-workspace"></a>ワークスペースの作成

次の図は、ワークスペース作成のワークフローを示したものです。

* サポートされている Azure Machine Learning クライアント (Azure CLI、Python SDK、Azure portal) のいずれかから Azure AD にサインインし、適切な Azure Resource Manager トークンを要求します。
* ワークスペースを作成するために Azure Resource Manager を呼び出します。 
* Azure Resource Manager では、Azure Machine Learning のリソース プロバイダーに連絡し、ワークスペースをプロビジョニングします。

ワークスペースの作成中に、追加のリソースがユーザーのサブスクリプションに作成されます。

* Key Vault (シークレットの格納用)
* Azure ストレージ アカウント (BLOB とファイル共有を含む)
* Azure Container Registry (推論/スコアリングと実験のための Docker イメージの格納用)
* Application Insights (テレメトリ格納用)

ユーザーは、必要に応じて、(Azure Kubernetes Service や VM などの) ワークスペースにアタッチされている他のコンピューティング先をプロビジョニングすることもできます。

[![ワークスペースの作成ワークフロー](media/concept-enterprise-security/create-workspace.png)](media/concept-enterprise-security/create-workspace.png#lightbox)

### <a name="save-source-code-training-scripts"></a>ソース コードを保存する (トレーニング スクリプト)

次の図は、コード スナップショットのワークフローを示したものです。

Azure Machine Learning ワークスペースに関連付けられているディレクトリ (実験) には、ソース コード (トレーニング スクリプト) が含まれます。 これらのスクリプトは、ご利用のローカル コンピューターとクラウド (ご利用のサブスクリプションの Azure BLOB ストレージ内) に格納されます。 このコード スナップショットは、履歴監査の実行または検査に使用されます。

[![コード スナップショット ワークフロー](media/concept-enterprise-security/code-snapshot.png)](media/concept-enterprise-security/code-snapshot.png#lightbox)

### <a name="training"></a>トレーニング

次の図はトレーニングのワークフローを示したものです。

* 前のセクションで保存されたコード スナップショットのスナップショット ID を指定して、Azure Machine Learning が呼び出されます。
* Azure Machine Learning では実行 ID (省略可能) と Machine Learning service トークンが作成され、これは後で Machine Learning コンピューティング/VM などのコンピューティング先によって、Machine Learning service と通信するために使用されます。
* トレーニング ジョブを実行するために、マネージド コンピューティング先 (Machine Learning コンピューティングなど) またはアンマネージド コンピューティング先 (VM など) のいずれかを選択できます。 両方のシナリオのデータ フローを次に示します。
   * VM/HDInsight。Microsoft サブスクリプションのキー コンテナー内の SSH 資格情報でアクセスされます。 Azure Machine Learning では、以下を行うコンピューティング先で管理コードを実行します。

   1. 環境を準備します (Docker は VM とローカル コンピューターのオプションです。 Docker コンテナーで実験を行う方法については、Machine Learning コンピューティングに関する以下の手順を参照してください)。
   1. コードをダウンロードします。
   1. 環境変数と構成を設定します。
   1. ユーザー スクリプト (前のセクションで説明したコード スナップショット) を実行します。

   * Machine Learning コンピューティング。ワークスペース マネージド ID を使用してアクセスされます。
Machine Learning コンピューティングはマネージド コンピューティング先である (つまり、Microsoft によって管理される) ため、Microsoft サブスクリプションで実行されます。

   1. 必要な場合は、リモートの Docker の構築が開始されます。
   1. 管理コードは、ユーザーの Azure Files 共有に書き込まれます。
   1. コンテナーは、初期コマンドを使用して開始されます。 つまり、前の手順で説明した管理コードです。

#### <a name="querying-runs-and-metrics"></a>実行とメトリックのクエリを実行する

以下のフロー図では、トレーニング コンピューティング先で、Cosmos DB データベースのストレージから Azure Machine Learning に実行メトリックが書き戻された場合に、この手順が行われます。 クライアントで、Azure Machine Learning を呼び出すことができます。 その後、Machine Learning によって Cosmos DB データベースからメトリックがプルされ、クライアントに戻されます。

[![トレーニング ワークフロー](media/concept-enterprise-security/training-and-metrics.png)](media/concept-enterprise-security/training-and-metrics.png#lightbox)

### <a name="creating-web-services"></a>Web サービスを作成する

次の図は、推論のワークフローを示しています。 推論 (つまり、モデルのスコアリング) は、通常は運用データに基づいて、デプロイされたモデルを使用して予測を行うフェーズです。

これらについて詳しく説明します。

* ユーザーは、Azure Machine Learning SDK などのクライアントを使用して、モデルを登録します。
* ユーザーは、モデル、スコア ファイル、およびその他のモデルの依存関係を使用して、イメージを作成します。
* Docker イメージが作成されて、Azure Container Registry で格納されます。
* Web サービスは、前の手順で作成されたイメージを使用して、コンピューティング先 (Container Instances/AKS) にデプロイされます。
* スコアリング要求の詳細は、ユーザーのサブスクリプション内の Application Insights に格納されます。
* テレメトリも Microsoft/Azure サブスクリプションにプッシュされます。

[![推論のワークフロー](media/concept-enterprise-security/inferencing.png)](media/concept-enterprise-security/inferencing.png#lightbox)

## <a name="next-steps"></a>次のステップ

* [TLS を使用して Azure Machine Learning Web サービスをセキュリティで保護する](how-to-secure-web-service.md)
* [Web サービスとしてデプロイされた Machine Learning モデルを使用する](how-to-consume-web-service.md)
* [Azure Firewall と共に Azure Machine Learning を使用する](how-to-access-azureml-behind-firewall.md)
* [Azure Machine Learning と Azure Virtual Network を使用する](how-to-enable-virtual-network.md)
* [推奨システムを構築するためのベスト プラクティス](https://github.com/Microsoft/Recommenders)
* [Azure 上でリアルタイム レコメンデーション API を構築する](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
