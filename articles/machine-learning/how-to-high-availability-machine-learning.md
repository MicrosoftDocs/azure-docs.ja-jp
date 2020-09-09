---
title: 回復性を高める方法
titleSuffix: Azure Machine Learning
description: 高可用性構成を使用して、Azure Machine Learning のリソースの障害に対する回復性を高める方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 07/16/2020
ms.openlocfilehash: 9b298e10d3eb95bcb0ef525eb973259a3ab1dbbb
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88852554"
---
# <a name="increase-azure-machine-learning-resiliency"></a>Azure Machine Learning の回復性を高める

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、高可用性構成を使用して、Microsoft Azure Machine Learning のリソースの回復性を高める方法について説明します。 Azure Machine Learning が依存している Azure サービスを、高可用性対応に構成できます。 この記事では、高可用性のために構成できるサービスと、これらのリソースの構成に関する追加情報へのリンクを示します。

> [!NOTE]
> Azure Machine Learning 自体は、ディザスター リカバリーオプションを提供していません。

## <a name="understand-azure-services-for-azure-machine-learning"></a>Azure Machine Learning のための Azure サービスについて

Azure Machine Learning は複数の Azure サービスに依存しており、これには複数の層があります。 これらのサービスの一部は、ユーザー (顧客) サブスクリプションでプロビジョニングされています。 これらのサービスの高可用性構成は、ユーザーが行う必要があります。 他のサービスは Microsoft サブスクリプションで作成され、Microsoft によって管理されます。 

次のような Azure サービスがあります。

* **Azure Machine Learning インフラストラクチャ**: Azure Machine Learning ワークスペース用の Microsoft によって管理される環境。

* **関連付けられているリソース**: Azure Machine Learning ワークスペースの作成時にサブスクリプションでプロビジョニングされたリソース。 これらのリソースには、Azure Storage、Azure Key Vault、Azure Container Registry、Application Insights が含まれます。 これらのリソースの高可用性の設定の構成は、ユーザーが行う必要があります。
  * 既定のストレージには、モデル、トレーニング ログ データ、データセットなどのデータがあります。
  * Key Vault には、Azure Storage、Container Registry、データ ストアのための資格情報が格納されています。
  * Container Registry には、トレーニングおよび推論環境用の Docker イメージがあります。
  * Application Insights は、Azure Machine Learning を監視するためのものです。

* **コンピューティング リソース**: ワークスペースのデプロイ後に作成するリソース。 たとえば、機械学習モデルをトレーニングするために作成するコンピューティング インスタンスまたはコンピューティング クラスターです。
  * コンピューティング インスタンスとコンピューティング クラスター: Microsoft によって管理されるモデル開発環境。
  * その他のリソース:Azure Kubernetes Service (AKS)、Azure Databricks、Azure Container Instances、Azure HDInsight など、Azure Machine Learning に接続できる Microsoft のコンピューティング リソース。 これらのリソースの高可用性の設定の構成は、ユーザーが行う必要があります。

* **その他のデータ ストア**: Azure Machine Learning には、トレーニング データ用に Azure Storage、Azure Data Lake Storage、Azure SQL Database などの追加のデータ ストアをマウントできます。  これらのデータ ストアは、サブスクリプション内でプロビジョニングされます。 高可用性の設定の構成は、ユーザーが行う必要があります。

次の表では、Microsoft によって管理される Azure サービスとユーザーによって管理される Azure サービス、および既定で高可用性が実現される Azure サービスを示します。

| サービス | 管理者 | 既定で高可用性 |
| ----- | ----- | ----- |
| **Azure Machine Learning インフラストラクチャ** | Microsoft | |
| **関連付けられているリソース** |
| Azure Storage | あなたが | |
| Key Vault | あなたが | ✓ |
| Container Registry | あなたが | |
| Application Insights | あなたが | NA |
| **コンピューティング リソース** |
| コンピューティング インスタンス | Microsoft |  |
| コンピューティング クラスター | Microsoft |  |
| その他のコンピューティング リソース (AKS、 <br>Azure Databricks、Container Instances、HDInsight など) | 自分 |  |
| **追加のデータ ストア** (Azure Storage、SQL Database、<br> Azure Database for PostgreSQL、Azure Database for MySQL、 <br>Azure Databricks ファイル システムなど) | 自分 | |

この記事の残りの部分では、これらの各サービスを高可用性にするために必要な操作について説明します。

## <a name="associated-resources"></a>関連付けられているリソース

> [!IMPORTANT]
> Azure Machine Learning では、geo 冗長ストレージ (GRS)、geo ゾーン冗長ストレージ (GZRS)、読み取りアクセス geo 冗長ストレージ (RA-GRS)、または読み取りアクセス geo ゾーン冗長ストレージ (RA-GZRS) を使用した既定のストレージ アカウントのフェールオーバーはサポートされていません。

次のドキュメントを参照して、各リソースの高可用性の設定を構成してください。

* **Azure Storage**:高可用性の設定を構成するには、「[Azure Storage の冗長性](https://docs.microsoft.com/azure/storage/common/storage-redundancy)」を参照してください。
* **Key vault**:Key Vault では既定で高可用性が提供されるので、ユーザーの操作は必要ありません。  詳細については、「[Azure Key Vault の可用性と冗長性](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance)」を参照してください。
* **[コンテナー レジストリ]** :geo レプリケーションの場合は、Premium レジストリ オプションを選択します。 詳細については、「[Azure Container Registry の geo レプリケーション](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)」を参照してください。
* **Application Insights**:Application Insights には、高可用性の設定はありません。 データ保持期間と詳細を調整するには、「[Application Insights でのデータの収集、保持、保存](https://docs.microsoft.com/azure/azure-monitor/app/data-retention-privacy#how-long-is-the-data-kept)」を参照してください。

## <a name="compute-resources"></a>コンピューティング リソース

次のドキュメントを参照して、各リソースの高可用性の設定を構成してください。

* **Azure Kubernetes Service**:「[Azure Kubernetes Service (AKS) での事業継続とディザスター リカバリーに関するベスト プラクティス](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region)」および「[可用性ゾーンを使用する Azure Kubernetes Service (AKS) クラスターを作成する](https://docs.microsoft.com/azure/aks/availability-zones)」を参照してください。 AKS クラスターが Azure Machine Learning Studio、SDK、または CLI を使用して作成された場合、複数のリージョンにわたる高可用性はサポートされません。
* **Azure Databricks**:詳細については、「[Azure Databricks クラスターに対するリージョンのディザスター リカバリー](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery)」を参照してください。
* **Container Instances**:フェールオーバーは、オーケストレーターによって行われます。 詳細については、「[Azure Container Instances とコンテナー オーケストレーター](https://docs.microsoft.com/azure/container-instances/container-instances-orchestrator-relationship)」を参照してください。
* **HDInsight**:詳細については、「[Azure HDInsight でサポートされている高可用性サービス](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-components)」を参照してください。

## <a name="additional-data-stores"></a>その他のデータ ストア

次のドキュメントを参照して、各リソースの高可用性の設定を構成してください。

* **Azure BLOB コンテナー、Azure Files、Data Lake Storage Gen2**: 既定のストレージと同じです。
* **[Data Lake Storage Gen1]** :「[Data Lake Storage Gen1 の高可用性とディザスター リカバリーのガイダンス](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-disaster-recovery-guidance)」を参照してください。
* **SQL Database**:「[Azure SQL Database と SQL Managed Instance の高可用性](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability)」を参照してください。
* **Azure Database for PostgreSQL**:詳細については、「[Azure Database for PostgreSQL - Single Server での高可用性の概念](https://docs.microsoft.com/azure/postgresql/concepts-high-availability)」を参照してください。
* **Azure Database for MySQL**:詳細については、「[Azure Database for MySQL でのビジネス継続性を理解する](https://docs.microsoft.com/azure/mysql/concepts-business-continuity)」を参照してください。
* **Azure Databricks ファイル システム**: 詳細については、「[Azure Databricks クラスターに対するリージョンのディザスター リカバリー](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery)」を参照してください。

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

独自のカスタマー マネージド キーを指定して Azure Machine Learning ワークスペースをデプロイした場合、Azure Cosmos DB もサブスクリプション内にプロビジョニングされます。 その場合、その高可用性の設定の構成は、ユーザーが行う必要があります。 詳細については、「[Azure Cosmos DB での高可用性](https://docs.microsoft.com/azure/cosmos-db/high-availability)」を参照してください。

## <a name="next-steps"></a>次のステップ

関連付けられたリソースを使用して高可用性設定で Azure Machine Learning をデプロイするには、[Azure Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced)を使用します。
