---
title: Azure Machine Learning を使用したデータの暗号化
titleSuffix: Azure Machine Learning
description: Azure Machine Learning のコンピューティングとデータ ストアが保存時および転送中のデータの暗号化を提供する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 11/09/2020
ms.openlocfilehash: 13d5c02fbb4ae06c7a5279ab7c5d3af90c263f71
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102521069"
---
# <a name="data-encryption-with-azure-machine-learning"></a>Azure Machine Learning を使用したデータの暗号化

Azure Machine Learning は、モデルをトレーニングし、推論を実行する際に、さまざまな Azure データ ストレージ サービスとコンピューティング リソースを使用します。 これらのそれぞれには、保存時と転送中のデータの暗号化を提供する方法に関する独自のストーリーがあります。 この記事では、それぞれのシナリオに最適な方法について説明します。

> [!IMPORTANT]
> __トレーニング__ 中の運用グレードの暗号化の場合、Microsoft は Azure Machine Learning コンピューティング クラスターを使用することをお勧めします。 __推論__ 中の運用グレードの暗号化の場合、Microsoft は Azure Kubernetes Service を使用することをお勧めします。
>
> Azure Machine Learning コンピューティング インスタンスは、開発またはテスト環境です。 これを使用する場合は、ノートブックやスクリプトなどのファイルをファイル共有に保存することをお勧めします。 データは、データストアに格納する必要があります。

## <a name="encryption-at-rest"></a>保存時の暗号化

> [!IMPORTANT]
> ワークスペースに機密データが含まれている場合は、ワークスペースの作成時に [hbi_workspace フラグ](/python/api/azureml-core/azureml.core.workspace%28class%29#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-)を設定することをお勧めします。 `hbi_workspace` フラグは、ワークスペースの作成時にのみ設定できます。 既存のワークスペースに対して変更することはできません。

`hbi_workspace` フラグにより、[Microsoft が診断目的で収集するデータ](#microsoft-collected-data)の量が制御され、[Microsoft が管理する環境での追加の暗号化](../security/fundamentals/encryption-atrest.md)が可能になります。 さらに、次のアクションが可能になります。

* そのサブスクリプションで以前にクラスターを作成していない場合に、Azure Machine Learning コンピューティング クラスターでローカル スクラッチ ディスクの暗号化を開始します。 それ以外の場合は、コンピューティング クラスターのスクラッチ ディスクの暗号化を有効にするためにサポート チケットを作成する必要があります。 
* 実行間でローカル スクラッチ ディスクをクリーンアップします
* Key Vault を使用して、ストレージ アカウント、コンテナー レジストリ、SSH アカウントの資格情報を実行層からコンピューティング クラスターに安全に渡します。
* AzureMachineLearningService 以外の外部サービスから基になる Batch プールを呼び出すことができないように、IP フィルタリングを有効にします。
* コンピューティング インスタンスは HBI ワークスペースではサポートされていないことに注意してください

### <a name="azure-blob-storage"></a>Azure BLOB ストレージ

Azure Machine Learning では、Azure Machine Learning ワークスペースとサブスクリプションに関連付けられている Azure BLOB ストレージ アカウントにスナップショット、出力、ログを格納します。 Azure BLOB ストレージに格納されるすべてのデータは、Microsoft によって管理されたキーを使用して保存時に暗号化されます。

Azure BLOB ストレージに格納されるデータに独自のキーを使用する方法については、[Azure Key Vault でのカスタマー マネージド キーによる Azure Storage の暗号化](../storage/common/customer-managed-keys-configure-key-vault.md)に関するページを参照してください。

通常はトレーニング データも、トレーニング コンピューティング先にアクセスできるように Azure BLOB ストレージに格納されます。 このストレージは、Azure Machine Learning によって管理されませんが、リモート ファイル システムとしてコンピューティング先にマウントされます。

キーを __交換または取り消す__ 必要がある場合は、いつでもこの操作を行うことができます。 キーを交換すると、ストレージ アカウントは、新しいキー (最新バージョン) を使用して保存データを暗号化します。 キーを取り消す (無効にする) と、ストレージ アカウントは失敗した要求を処理します。 通常、交換または失効が有効になるまでに 1 時間かかります。

アクセス キーを再生成する方法の詳細については、「[ストレージ アカウント アクセス キーの再生成](how-to-change-storage-access-key.md)」を参照してください。

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Machine Learning では、メトリックとメタデータは Azure Cosmos DB インスタンスに格納されます。 このインスタンスは、Azure Machine Learning によって管理される Microsoft サブスクリプションに関連付けられています。 Azure Cosmos DB に格納されているすべてのデータは、Microsoft によって管理されるキーを使用して保存時に暗号化されます。

独自の (カスタマーマネージド) キーを使用して Azure Cosmos DB インスタンスを暗号化する場合は、ワークスペースで使用する専用の Cosmos DB インスタンスを作成できます。 実行履歴情報などのデータを、Microsoft サブスクリプションでホストされているマルチテナント Cosmos DB インスタンスの外部に格納する場合は、この方法をお勧めします。 

カスタマー マネージド キーを使用してサブスクリプションに Cosmos DB インスタンスをプロビジョニングできるようにするには、次の操作を実行します。

* Microsoft.MachineLearning と Microsoft.DocumentDB リソース プロバイダーをサブスクリプションに登録します (まだ行っていない場合)。

* Azure Machine Learning ワークスペースを作成するときは、次のパラメーターを使用します。 SDK、CLI、REST API、および Resource Manager のテンプレートでは、両方のパラメーターが必須であり、サポートされています。

    * `resource_cmk_uri`:このパラメーターは、[キーのバージョン情報](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning)を含む、キー コンテナー内のカスタマー マネージド キーの完全なリソース URI です。 

    * `cmk_keyvault`:このパラメーターは、サブスクリプション内のキー コンテナーのリソース ID です。 このキー コンテナーは、Azure Machine Learning ワークスペースに使用するのと同じリージョンおよびサブスクリプションにある必要があります。 
    
        > [!NOTE]
        > このキー コンテナー インスタンスは、ワークスペースをプロビジョニングするときに Azure Machine Learning によって作成されるキー コンテナーとは異なる場合があります。 ワークスペースに同じキー コンテナー インスタンスを使用する場合は、[key_vault パラメーター](/python/api/azureml-core/azureml.core.workspace%28class%29#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-)を使用してワークスペースをプロビジョニングするときに、同じキー コンテナーを渡します。 

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

キーを __交換または取り消す__ 必要がある場合は、いつでもこの操作を行うことができます。 キーを交換すると、Cosmos DB は新しいキー (最新バージョン) を使用して保存データを暗号化します。 キーを取り消す (無効にする) と、Cosmos DB は失敗した要求を処理します。 通常、交換または失効が有効になるまでに 1 時間かかります。

カスタマー マネージド キーと Cosmos DB の詳細については、[Azure Cosmos DB アカウントのカスタマー マネージド キーの構成](../cosmos-db/how-to-setup-cmk.md)に関する記事を参照してください。

### <a name="azure-container-registry"></a>Azure Container Registry

レジストリ (Azure Container Registry) 内のすべてのコンテナー イメージは、保存時に暗号化されます。 Azure では、イメージは保存前に自動的に暗号化され、Azure Machine Learning がイメージをプルするときに暗号解除されます。

独自の (カスタマー マネージド) キーを使用して Azure Container Registry を暗号化するには、独自の ACR を作成し、ワークスペースのプロビジョニング中にアタッチするか、ワークスペースのプロビジョニング時に作成された既定のインスタンスを暗号化する必要があります。

> [!IMPORTANT]
> Azure Machine Learning を使用するには、Azure Container Registry で管理者アカウントが有効になっている必要があります。 既定では、コンテナー レジストリを作成するときに、この設定は無効になっています。 管理者アカウントを有効にする方法の詳細については、「[管理者アカウント](../container-registry/container-registry-authentication.md#admin-account)」を参照してください。
>
> いったん、Azure Container Registry をワークスペースに対して作成したら、削除しないでください。 それを行うと、Azure Machine Learning ワークスペースが破損します。

既存の Azure Container Registry を使用してワークスペースを作成する例については、次の記事を参照してください。

* [Azure CLI を使用して Azure Machine Learning のワークスペースを作成する](how-to-manage-workspace-cli.md)。
* [Python SDK でワークスペースを作成する](how-to-manage-workspace.md?tabs=python#create-a-workspace)。
* [Azure Resource Manager テンプレートを使用して Azure Machine Learning のワークスペースを作成する](how-to-create-workspace-template.md)

### <a name="azure-container-instance"></a>Azure Container Instances

カスタマー マネージド キーを使用して、デプロイされた Azure Container Instance (ACI) リソースを暗号化することができます。 ACI に使用するカスタマー マネージド キーは、ワークスペースの Azure Key Vault に格納できます。 キーの生成の詳細については、「[カスタマー マネージド キーを使用してデータを暗号化する](../container-instances/container-instances-encrypt-data.md#generate-a-new-key)」を参照してください。

Azure Container Instance にモデルをデプロイするときにキーを使用するには、`AciWebservice.deploy_configuration()` を使用して新しいデプロイ構成を作成します。 次のパラメーターを使用して、キーの情報を指定します。

* `cmk_vault_base_url`:キーが含まれるキー コンテナーの URL。
* `cmk_key_name`:キーの名前です。
* `cmk_key_version`:キーのバージョン。

デプロイ構成の作成と使用の詳細については、次の記事を参照してください。

* [AciWebservice.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-) 参照
* [デプロイする場所と方法](how-to-deploy-and-where.md)
* [Azure Container Instances にモデルをデプロイする](how-to-deploy-azure-container-instance.md)

カスタマー マネージド キーを ACI で使用する方法の詳細については、「[カスタマー マネージド キーを使用してデータを暗号化する](../container-instances/container-instances-encrypt-data.md#encrypt-data-with-a-customer-managed-key)」を参照してください。

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

デプロイされた Azure Kubernetes Service リソースは、カスタマー マネージド キーを使用していつでも暗号化できます。 詳細については、[Azure Kubernetes Service で独自のキーを取り込む](../aks/azure-disk-customer-managed-keys.md)方法に関するページを参照してください。 

このプロセスでは、Kubernetes クラスター内のデプロイされた仮想マシンのデータと OS ディスクの両方を暗号化できます。

> [!IMPORTANT]
> このプロセスは、AKS K8s バージョン 1.17 以降でのみ機能します。 Azure Machine Learning は、2020 年 1 月 13 日に AKS 1.17 のサポートを追加しました。

### <a name="machine-learning-compute"></a>Machine Learning コンピューティング

Azure Storage に格納されている各コンピューティング ノードの OS ディスクは、Azure Machine Learning ストレージ アカウント内の Microsoft によって管理されるキーを使用して暗号化されます。 このコンピューティング先は一時的なものであり、キューに入れられた実行がない場合、通常はクラスターがスケールダウンされます。 基になる仮想マシンのプロビジョニングは解除され、OS ディスクは削除されます。 OS ディスクでは Azure Disk Encryption はサポートされません。

各仮想マシンにも、OS 操作用にローカルな一時ディスクがあります。 必要に応じて、ディスクを使用してトレーニング データをステージできます。 `hbi_workspace` パラメーターが `TRUE` に設定されているワークスペースでは、ディスクは既定で暗号化されます。 この環境は、実行中だけ有効期間が短く、暗号化のサポートはシステム管理キーのみに制限されています。

### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks は Azure Machine Learning パイプラインで使用できます。 既定では、Azure Databricks によって使用される Databricks ファイル システム (DBFS) は、Microsoft が管理するキーを使用して暗号化されます。 顧客が管理するキーを使用するように Azure Databricks を構成するには、「[既定 (ルート) の DBFS で顧客が管理するキーを構成する](/azure/databricks/security/customer-managed-keys-dbfs)」を参照してください。

### <a name="microsoft-generated-data"></a>Microsoft が生成したデータ

自動化された機械学習などのサービスを使用する場合、Microsoft では、複数のモデルをトレーニングするために、一時的な処理済みのデータを生成することがあります。 このデータはワークスペース内のデータストアに格納されます。これにより、アクセス制御と暗号化を適切に適用できます。

また、[デプロイされているエンドポイントからログ記録された診断情報](how-to-enable-app-insights.md)を Azure Application Insights インスタンスに暗号化することもできます。

## <a name="encryption-in-transit"></a>転送中の暗号化

Azure Machine Learning では、さまざまな Azure Machine Learning マイクロサービス間の内部通信をセキュリティで保護するために、TLS を使用します。 すべての Azure Storage アクセスも、セキュリティで保護されたチャネル経由で行われます。

スコアリング エンドポイントへの外部呼び出しをセキュリティで保護するために、Azure Machine Learning では TLS が使用されます。 詳細については、「[TSL を使用して Azure Machine Learning による Web サービスをセキュリティで保護する](./how-to-secure-web-service.md)」を参照してください。

## <a name="data-collection-and-handling"></a>データの収集と処理

### <a name="microsoft-collected-data"></a>Microsoft が収集したデータ

Microsoft は、リソース名 (データセット名や機械学習の実験名など)、または診断目的でのジョブ環境変数など、ユーザー以外を識別する情報を収集する場合があります。 このようなデータはすべて、Microsoft が所有するサブスクリプションでホストされているストレージに Microsoft が管理するキーを使用して保存され、[Microsoft の標準のプライバシー ポリシーとデータ処理規格](https://privacy.microsoft.com/privacystatement)に従います。

また、機密情報 (アカウント キー シークレットなど) を環境変数に保存しないこともお勧めしています。 環境変数は、Microsoft によってログに記録され、暗号化され、保存されます。 同様に、[run_id](/python/api/azureml-core/azureml.core.run%28class%29) に名前を付けるときは、ユーザー名や秘密のプロジェクト名などの機密情報を含めないようにしてください。 この情報は、Microsoft サポート エンジニアがアクセスできるテレメトリ ログに表示されることがあります。

ワークスペースのプロビジョニング中に `hbi_workspace` パラメーターを `TRUE` に設定して、収集される診断データをオプトアウトすることができます。 この機能は、AzureML Python SDK、CLI、REST API、または Azure Resource Manager のテンプレートを使用する場合にサポートされます。

## <a name="using-azure-key-vault"></a>Azure Key Vault の使用

Azure Machine Learning では、ワークスペースに関連付けられた Azure Key Vault インスタンスを使用して、さまざまな種類の資格情報を格納します。

* 関連付けられたストレージ アカウントの接続文字列
* Azure コンテナー リポジトリ インスタンスへのパスワード
* データ ストアへの接続文字列

Azure HDInsight や VM などのコンピューティング先に対する SSH パスワードとキーは、Microsoft サブスクリプションに関連付けられている別のキー コンテナーに格納されます。 Azure Machine Learning には、ユーザーによって提供されるパスワードやキーは格納されません。 代わりに、VM と HDInsight に接続して実験を行うために、独自の SSH キーを生成、承認、および格納します。

各ワークスペースには、ワークスペースと同じ名前を持つ、関連付けられたシステム割り当て済みマネージド ID があります。 このマネージド ID では、キー コンテナー内のすべてのキー、シークレット、および証明書にアクセスできます。

## <a name="next-steps"></a>次のステップ

* [Azure Storage への接続](how-to-access-data.md)
* [データストアからデータを取得する](how-to-create-register-datasets.md)
* [データに接続する](how-to-connect-data-ui.md)
* [データセットを使ってトレーニングする](how-to-train-with-datasets.md)