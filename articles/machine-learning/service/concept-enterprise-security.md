---
title: エンタープライズ セキュリティ
titleSuffix: Azure Machine Learning service
description: 'Azure Machine Learning service を安全に使用します: 認証、認可、ネットワーク セキュリティ、データ暗号化、および監視。'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/07/2019
ms.openlocfilehash: 510f58cc0b71fb75ac6f5e15fc883c3caf4a8f9a
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69897973"
---
# <a name="enterprise-security-for-azure-machine-learning-service"></a>Machine Learning service のエンタープライズ セキュリティ

この記事では、Azure Machine Learning service で利用できるセキュリティ機能について説明します。

クラウド サービスを使用するときは、必要なユーザーにのみアクセスを制限するのがベスト プラクティスです。 そのためには最初に、サービスによって使用される認証と認可のモデルを理解します。 ネットワーク アクセスを制限したり、オンプレミス ネットワークのリソースをクラウドのリソースと安全に結合したりすることも、必要になる場合があります。 保存時とサービス間の移動時の両方でデータを暗号化することも不可欠です。 最後に、サービスを監視し、すべてのアクティビティの監査ログを生成できることも必要です。

## <a name="authentication"></a>Authentication

Azure Active Directory (Azure AD) が同じように構成されている場合、多要素認証がサポートされます。

* クライアントは、Azure AD にログインし、Azure Resource Manager トークンを取得します。  ユーザーとサービス プリンシパルは完全にサポートされています。
* クライアントは、Azure Resource Manager とすべての Azure Machine Learning service にトークンを示します
* Azure Machine Learning service では、ユーザーのコンピューティングに Azure Machine Learning トークンが提供されます。 たとえば、Machine Learning コンピューティングなどです。 このトークンは、実行が完了した後で Azure Machine Learning service にコールバックする (ワークスペースにスコープを制限する) ために、ユーザー コンピューティングによって使用されます。

[![Azure Machine Learning service での認証のしくみを示すスクリーンショット](./media/enterprise-readiness/authentication.png)](./media/enterprise-readiness/authentication-expanded.png)

### <a name="authentication-for-web-service-deployment"></a>Web サービスのデプロイ用の認証

Azure Machine Learning は、Web サービスに関してキーとトークンの 2 つの認証形式をサポートします。 各 Web サービスで使用できる認証形式は一度に 1 つだけです。

|認証方法|ACI|AKS|
|---|---|---|
|キー|既定で無効| 既定で有効|
|トークン| 使用できません。| 既定で無効 |

#### <a name="authentication-with-keys"></a>キーによる認証

デプロイに対してキー認証を有効にした場合、認証キーが自動的に作成されます。

* Azure Kubernetes Service にデプロイする場合、認証は既定で有効になります。
* Azure Container Instances にデプロイする場合、認証は既定で無効になります。

キー認証を有効にするには、デプロイの作成時や更新時に `auth_enabled` パラメーターを使用します。

キー認証が有効になっている場合は、`get_keys` メソッドを使用して、プライマリおよびセカンダリ認証キーを取得できます。

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> キーを再生成する必要がある場合は、[`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) を使用します

#### <a name="authentication-with-tokens"></a>トークンによる認証

Web サービスのトークン認証を有効にする場合、ユーザーは、Web サービスにアクセスするために Azure Machine Learning JSON Web トークンを提供する必要があります。

* Azure Kubernetes Service にデプロイする場合、トークン認証は既定で無効になります。
* Azure Container Instances にデプロイする場合、トークン認証はサポートされません。

トークン認証を制御するには、デプロイの作成や更新時に `token_auth_enabled` パラメーターを使用します。

トークン認証が有効になっている場合は、`get_token` メソッドを使用して、JWT トークンとそのトークンの有効期限を取得できます。

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> トークンの `refresh_by` 時刻の後に新しいトークンを要求する必要があります。
>
> Azure Machine Learning ワークスペースは、ご利用の Azure Kubernetes Service クラスターと同じリージョンに作成することを強くお勧めします。 トークンを使用して認証するために、Web サービスは、Azure Machine Learning ワークスペースの作成先のリージョンに対して呼び出しを行います。 ワークスペースのリージョンが利用不可になった場合、ワークスペースとは異なるリージョンにクラスターがあったとしても、Web サービスのトークンがフェッチできなくなります。 その場合、ワークスペースのリージョンが利用可能な状態に戻るまで、事実上、Azure AD Authentication が利用できない状態となります。 また、クラスターのリージョンとワークスペースのリージョンとの間の距離が長くなるほど、トークンのフェッチにかかる時間も長くなります。

## <a name="authorization"></a>Authorization

複数のワークスペースを作成でき、各ワークスペースを複数のユーザーで共有できます。 ワークスペースを共有する場合は、次のロールをユーザーに割り当てることで、ワークスペースへのアクセスを制御できます。

* Owner
* Contributor
* Reader

次の表では、いくつかの主要な Azure Machine Learning service の操作とそれらを実行できるロールを示します。

| Azure Machine Learning service の操作 | Owner | Contributor | Reader |
| ---- |:----:|:----:|:----:|
| ワークスペースを作成する | ✓ | ✓ | |
| ワークスペースを共有する | ✓ | |  |
| コンピューティングを作成する | ✓ | ✓ | |
| コンピューティングをアタッチする | ✓ | ✓ | |
| データストアをアタッチする | ✓ | ✓ | |
| 実験を実行する | ✓ | ✓ | |
| 実行/メトリックを表示する | ✓ | ✓ | ✓ |
| モデルの登録 | ✓ | ✓ | |
| イメージを作成する | ✓ | ✓ | |
| Web サービスのデプロイ | ✓ | ✓ | |
| モデル/イメージを表示する | ✓ | ✓ | ✓ |
| Web サービスを呼び出す | ✓ | ✓ | ✓ |

組み込みのロールがニーズに十分ではない場合は、カスタムのロールを作成することもできます。 サポートされているカスタム ロールは、ワークスペースと Machine Learning コンピューティングでの操作用だけです。 カスタム ロールは、ワークスペースとそのワークスペース内のコンピューティング リソースに対する読み取り、書き込み、または削除のアクセス許可を持つことができます。 ロールは、特定のワークスペース レベル、特定のリソース グループ レベル、または特定のサブスクリプション レベルで使用できるようにすることができます。 詳しくは、「[Manage users and roles in an Azure Machine Learning workspace (Azure Machine Learning workspace でユーザーとロールを管理する)](how-to-assign-roles.md)」をご覧ください

### <a name="securing-compute-and-data"></a>コンピューティングとデータをセキュリティで保護する

所有者と共同作成者は、ワークスペースにアタッチされているすべてのコンピューティング ターゲットとデータ ストアを使用できます。  
各ワークスペースには、ワークスペースで使用されるアタッチされたリソースに対して次のアクセス許可を持つ、システムによって割り当てられた (ワークスペースと同じ名前の) マネージド ID も関連付けられています。

マネージド ID の詳細については、「[Azure リソースのマネージド ID とは](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)」をご覧ください

| Resource | アクセス許可 |
| ----- | ----- |
| ワークスペース | Contributor |
| ストレージ アカウント | ストレージ BLOB データ共同作成者 |
| Key Vault | すべてのキー、シークレット、証明書へのアクセス |
| Azure Container Registry | Contributor |
| ワークスペースを含むリソース グループ | Contributor |
| キー コンテナーを含むリソース グループ (ワークスペースを含むものと異なる場合) | Contributor |

管理者には、上記のリソースに対するマネージド ID のアクセス権を取り消さないことをお勧めします。 アクセスはキーの再同期操作で復元できます。

Azure Machine Learning service では、すべてのワークスペース リージョンのサブスクリプションに、共同作成者レベルのアクセス権を持つ追加のアプリケーション (名前が `aml-` で始まるもの) が作成されます。 たとえば、 同じサブスクリプションで米国東部と北ヨーロッパに異なるワークスペースを持っている場合、このようなアプリケーションが 2 つ表示されます。 これは、Azure Machine Learning service がコンピューティング リソースを管理できるために必要です。

## <a name="network-security"></a>ネットワークのセキュリティ

Azure Machine Learning service は、コンピューティング リソースのために他の Azure サービスに依存します。 コンピューティング リソース (コンピューティング ターゲット) は、モデルのトレーニングとデプロイに使用されます。 これらのコンピューティング ターゲットは、仮想ネットワーク内に作成することができます。 たとえば、Microsoft Data Science Virtual Machine を使用して、モデルをトレーニングしてから、そのモデルを Azure Kubernetes Service (AKS) にデプロイすることができます。  

詳しくは、[仮想ネットワークで実験と推論を実行する方法](how-to-enable-virtual-network.md)に関する記事をご覧ください。

## <a name="data-encryption"></a>データの暗号化

### <a name="encryption-at-rest"></a>保存時の暗号化

#### <a name="azure-blob-storage"></a>Azure Blob Storage

Azure Machine Learning service では、Azure Machine Learning service ワークスペースに結び付けられていてユーザーのサブスクリプション内に存在する Azure Blob Storage アカウントに、スナップショット、出力、ログが格納されます。 Azure Blob Storage に格納されるすべてのデータは、Microsoft によって管理されたキーを使用して保存時に暗号化されます。

Azure Blob Storage に格納されるデータに独自のキーを使用する方法について詳しくは、「[ユーザーが管理する Azure Key Vault キーを Storage Service Encryption に使用する](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys)」をご覧ください。

通常は、トレーニング データもトレーニング コンピューティングでアクセスできるように Azure Blob Storage に格納されます。 このストレージは、Azure Machine Learning によっては管理されず、リモート ファイル システムとしてコンピューティングにマウントされます。

ご使用のワークスペースで使われる Azure ストレージ アカウントのアクセス キーの再生成については、[ストレージ アクセス キーの再生成](how-to-change-storage-access-key.md)に関するページをご覧ください。

#### <a name="cosmos-db"></a>Cosmos DB

Azure Machine Learning service では、Azure Machine Learning service によって管理される Microsoft サブスクリプションに存在する Cosmos DB に、メトリックとメタデータが格納されます。 Cosmos DB に格納されるすべてのデータは、Microsoft によって管理されたキーを使用して保存時に暗号化されます。

#### <a name="azure-container-registry-acr"></a>Azure Container Registry (ACR)

レジストリ (ACR) 内のすべてのコンテナー イメージは、保存時に暗号化されます。 Azure では、イメージは保存前に自動的に暗号化され、Azure Machine Learning service がイメージをプルするときにその場で暗号解除されます。

#### <a name="machine-learning-compute"></a>Machine Learning コンピューティング

各コンピューティング ノードの OS ディスクは、Azure Storage に格納され、Azure Machine Learning service ストレージ アカウント内の Microsoft によって管理されたキーを使用して暗号化されます。 このコンピューティング先は一時的なもので、通常、キューに実行がないときはクラスターはスケールダウンされます。 基になる仮想マシンはプロビジョニング解除され、OS ディスクは削除されます。 Azure Disk Encryption は、OS ディスクに対してはサポートされません。
各仮想マシンにも、OS 操作用にローカルな一時ディスクがあります。 必要に応じて、トレーニング データのステージング用にこのディスクも使用できます。 このディスクは暗号化されません。
Azure での保存時の暗号化のしくみについて詳しくは、「[Azure Data Encryption-at-Rest](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)」をご覧ください。

### <a name="encryption-in-transit"></a>転送中の暗号化

さまざまな Azure Machine Learning マイクロ サービス間の内部通信と、スコアリング エンドポイントを呼び出す外部通信の両方が、SSL を使用してサポートされています。 すべての Azure Storage アクセスも、セキュリティで保護されたチャネル経由で行われます。
詳しくは、[SSL を使用した Azure Machine Learning Web サービスのセキュリティ保護](https://docs.microsoft.com/azure/machine-learning/service/how-to-secure-web-service)に関する記事をご覧ください。

### <a name="using-azure-key-vault"></a>Azure Key Vault の使用

Azure Machine Learning service では、さまざまな種類の資格情報を格納するために、ワークスペースに関連付けられた Key Vault インスタンスが使用されます。

* 関連付けられたストレージ アカウントの接続文字列
* Azure コンテナー リポジトリ インスタンスへのパスワード
* データ ストアへの接続文字列。

HDI HDInsight や VM などのコンピューティング先に対する SSH パスワードとキーは、Microsoft サブスクリプションに関連付けられた別の Key Vault に格納されます。 Azure Machine Learning service では、実験の実行を目的として VM/HDInsight に接続するために、独自の SSH キーを生成、認可、格納する代わりに、ユーザーによって提供されたパスワードまたはキーは格納されません。
各ワークスペースには、Key Vault 内のすべてのキー、シークレット、証明書へのアクセス権を持つ、システムによって割り当てられた (ワークスペースと同じ名前を持つ) マネージド ID が関連付けられています。

## <a name="monitoring"></a>監視

### <a name="metrics"></a>メトリック

Azure Monitor メトリックは、Azure Machine Learning service ワークスペースのメトリックを表示および監視するために使用できます。 [Azure portal](https://portal.azure.com) からワークスペースを選択し、 __[メトリック]__ リンクを使用します。

[![ワークスペースのメトリックの例を示すスクリーンショット](./media/enterprise-readiness/workspace-metrics.png)](./media/enterprise-readiness/workspace-metrics-expanded.png)

メトリックには、実行、デプロイ、および登録に関する情報が含まれます。

詳しくは、「[Metrics in Azure Monitor (Azure Monitor のメトリック)](/azure/azure-monitor/platform/data-platform-metrics)」をご覧ください。

### <a name="activity-log"></a>アクティビティ ログ

ワークスペース下のアクティビティ ログを見て、ワークスペースで実行されたさまざまな操作を確認し、操作名、イベント開始者、タイムスタンプなどの基本的な情報を取得できます。

次のスクリーンショットでは、ワークスペースのアクティビティ ログを示します。

[![ワークスペース下のアクティビティ ログを示すスクリーンショット](./media/enterprise-readiness/workspace-activity-log.png)](./media/enterprise-readiness/workspace-activity-log-expanded.png)

スコアリング要求の詳細は、ワークスペース作成時にユーザーのサブスクリプションに作成される Application Insight に格納されます。 ログに記録される情報には、HTTPMethod、UserAgent、ComputeType、RequestUrl、StatusCode、RequestId、Duration などのフィールドが含まれます。

> [!IMPORTANT]
> Azure Machine Learning ワークスペース内の一部の操作では、情報はアクティビティ ログに記録されません。 たとえば、トレーニングの開始やモデルの登録などです。
>
> これらの操作の一部はワークスペースの __[アクティビティ]__ 領域に表示されますが、アクティビティを開始したユーザーは示されません。

## <a name="data-flow-diagram"></a>データ フロー図

### <a name="create-workspace"></a>ワークスペースの作成

次の図は、ワークスペース作成のワークフローを示したものです。
ユーザーは、サポートされている Azure Machine Learning service クライアント (CLI、Python SDK、Azure portal) のいずれかから Azure AD にログインし、適切な Azure Resource Manager トークンを要求します。 その後、ユーザーはワークスペースを作成するために Azure Resource Manager を呼び出します。  Azure Resource Manager は、Azure Machine Learning service のリソース プロバイダーと通信し、ワークスペースをプロビジョニングします。  ワークスペースの作成中に、その他のリソースが顧客のサブスクリプションに作成されます。

* キー コンテナー (シークレット格納用)
* Azure ストレージ アカウント (BLOB と FileShare を含む)
* Azure Container Registry (推論/スコアリングと実験のための Docker イメージの格納用)
* Application Insights (テレメトリ格納用)

お客様は、必要に応じて、ワークスペースにアタッチされているその他のコンピューティング (Azure Kubernetes Service、VM など) もプロビジョニングできます。

[![ワークスペース作成のワークフローを示すスクリーンショット](./media/enterprise-readiness/create-workspace.png)](./media/enterprise-readiness/create-workspace-expanded.png)

### <a name="save-source-code-training-scripts"></a>ソース コードを保存する (トレーニング スクリプト)

次の図は、コード スナップショットのワークフローを示したものです。
Azure Machine Learning service ワークスペースに関連付けられているディレクトリ (実験) には、ソース コード (トレーニング スクリプト) が含まれます。  これらのスクリプトは、お客様のローカル コンピューターとクラウド (お客様のサブスクリプションの Azure Blob Storage) に格納されます。 このコード スナップショットは、履歴監査の実行または検査に使用されます。

[![ワークスペース作成のワークフローを示すスクリーンショット](./media/enterprise-readiness/code-snapshot.png)](./media/enterprise-readiness/code-snapshot-expanded.png)

### <a name="training"></a>トレーニング

次の図はトレーニングのワークフローを示したものです。

* 上で保存したコード スナップショットのスナップショット ID を指定して、Azure Machine Learning service が呼び出されます
* Azure Machine Learning service では実行 ID (オプション) と Azure Machine Learning service トークンが作成され、これは後で Machine Learning コンピューティング/VM などのコンピューティング先によって、Azure Machine Learning service にトークバックするために使用されます
* マネージド コンピューティング (例: Machine Learning コンピューティング) またはアンマネージド コンピューティング (例: VM) を選択して、トレーニング ジョブを実行できます。 両方のシナリオのデータ フローについては下で説明します。
* (VM/HDInsight – Microsoft サブスクリプションの Key Vault 内の SSH 資格情報を使用してアクセスされる) Azure Machine Learning service はコンピューティング先で管理コードを実行し、次のことを行います。

   1. 環境を準備します (Docker も VM と ローカルに対するオプションです。 Docker コンテナーで実験を実行する方法については、以下の Machine Learning コンピューティングに対する手順を参照してください)。
   1. コードをダウンロードします。
   1. 環境変数と構成を設定します。
   1. ユーザー スクリプトを実行します (上記のコード スナップショット)。

* (Machine Learning コンピューティング – ワークスペースのマネージド ID を使用してアクセスされる) Machine Learning コンピューティングはマネージド コンピューティングであり、Microsoft によって管理されるので、結果として Microsoft サブスクリプションで実行されます。

   1. 必要な場合は、リモートの Docker の構築が開始されます。
   1. ユーザーの Azure FileShare に管理コードが書き込まれます。
   1. 前のステップで説明した管理コードである初期コマンドでコンテナーが開始されます。

#### <a name="querying-runs-and-metrics"></a>実行とメトリックのクエリを実行する

このステップは、"*実行メトリック*" がトレーニング コンピューティングによって Azure Machine Learning service に書き込まれ、そこから Cosmos DB に格納されるフローで示されます。 クライアントは Azure Machine Learning service を呼び出すことができ、Azure Machine Learning service は Cosmos DB からメトリックを取得してクライアントに戻します。

[![ワークスペース作成のワークフローを示すスクリーンショット](./media/enterprise-readiness/training-and-metrics.png)](./media/enterprise-readiness/training-and-metrics-expanded.png)

### <a name="creating-web-services"></a>Web サービスを作成する

次の図は、推論のワークフローを示しています。 推論、つまりモデル スコアリングとは、最も一般的には運用環境のデータに基づいて、デプロイしたモデルを使用して予測を行うフェーズです。
詳細は以下を参照してください。

* ユーザーは、Azure ML SDK などのクライアントを使用して、モデルを登録します
* ユーザーは、モデル、スコア ファイル、その他のモデルの依存関係を使用して、イメージを作成します
* Docker イメージが作成されて、ACR に格納されます
* 上で作成されたイメージを使用して、Web サービスがコンピューティング先 (ACI/AKS) にデプロイされます
* スコアリング要求の詳細は、ユーザーのサブスクリプション内の Application Insight に格納されます
* テレメトリも Microsoft/Azure サブスクリプションにプッシュされます

[![ワークスペース作成のワークフローを示すスクリーンショット](./media/enterprise-readiness/inferencing.png)](./media/enterprise-readiness/inferencing-expanded.png)

## <a name="next-steps"></a>次の手順

* [SSL を使用して Azure Machine Learning Web サービスをセキュリティで保護する](how-to-secure-web-service.md)
* [Web サービスとしてデプロイされた ML モデルを使用する](how-to-consume-web-service.md)
* [バッチ予測を実行する方法](how-to-run-batch-predictions.md)
* [Application Insights を使用して Azure Machine Learning のモデルを監視する](how-to-enable-app-insights.md)
* [実稼働環境でモデルのデータを収集する](how-to-enable-data-collection.md)
* [Azure Machine Learning service SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Azure Machine Learning service と Azure Virtual Network を使用する](how-to-enable-virtual-network.md)
* [推奨システムを構築するためのベスト プラクティス](https://github.com/Microsoft/Recommenders)
* [Azure 上でリアルタイム レコメンデーション API を構築する](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
