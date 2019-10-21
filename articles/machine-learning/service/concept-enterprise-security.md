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
ms.date: 08/07/2019
ms.openlocfilehash: 309cef6ec058d8192bc7a6341b49a59c0000a305
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71035557"
---
# <a name="enterprise-security-for-azure-machine-learning"></a>Azure Machine Learning のエンタープライズ セキュリティ

この記事では、Azure Machine Learning で利用できるセキュリティ機能について学習します。

クラウド サービスを使用する場合は、アクセスを必要とするユーザーのみに制限することをお勧めします。 まず、サービスによって使用される認証および認可モデルについて理解します。 ネットワーク アクセスを制限したり、オンプレミス ネットワークのリソースをクラウドと安全に結合したりすることも必要になる場合があります。 保存時とサービス間の移動時の両方でデータを暗号化することも不可欠です。 最後に、サービスを監視し、すべてのアクティビティの監査ログを生成できることも必要です。

## <a name="authentication"></a>認証

多要素認証がサポートされるのは、それを使用するように Azure Active Directory (Azure AD) が構成されている場合です。 以下に認証プロセスを示します。

1. クライアントでは、Azure AD にサインインして、Azure Resource Manager トークンを取得します。  ユーザーとサービス プリンシパルは完全にサポートされています。
1. クライアントによって、Azure Resource Manager とすべての Azure Machine Learning にトークンが提示されます。
1. Machine Learning service では、ユーザーのコンピューティング先 (たとえば、Machine Learning コンピューティング) に Machine Learning service トークンが提供されます。 このトークンは、実行の完了後に Machine Learning service にコールバックするために、ユーザーのコンピューティング ターゲットによって使用されます。 スコープはワークスペースに制限されます。

[![Azure Machine Learning での認証](./media/enterprise-readiness/authentication.png)](./media/enterprise-readiness/authentication-expanded.png)

### <a name="authentication-for-web-service-deployment"></a>Web サービスのデプロイ用の認証

Azure Machine Learning では、Web サービスに関してキーとトークンの 2 つの認証形式がサポートされます。 各 Web サービスで有効にできる認証形式は一度に 1 つのみです。

|認証方法|Azure Container Instances|AKS|
|---|---|---|
|Key|既定で無効| 既定で有効|
|トークン| 使用できません。| 既定で無効 |

#### <a name="authentication-with-keys"></a>キーによる認証

デプロイに対してキー認証を有効にした場合、認証キーが自動的に作成されます。

* Azure Kubernetes Service (AKS) にデプロイする場合、認証は既定で有効になります。
* Azure Container Instances にデプロイする場合、認証は既定で無効になります。

キー認証を有効にするには、デプロイの作成時や更新時に `auth_enabled` パラメーターを使用します。

キー認証が有効になっている場合は、`get_keys` メソッドを使用して、プライマリおよびセカンダリ認証キーを取得できます。

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> キーを再生成する必要がある場合は、[`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) を使用します。

#### <a name="authentication-with-tokens"></a>トークンによる認証

Web サービスのトークン認証を有効にする場合、ユーザーは、Web サービスにアクセスするために Azure Machine Learning JSON Web トークンを提示する必要があります。

* Azure Kubernetes Service にデプロイする場合、トークン認証は既定で無効になります。
* Azure Container Instances にデプロイする場合、トークン認証はサポートされません。

トークン認証を制御するには、デプロイの作成時や更新時に `token_auth_enabled` パラメーターを使用します。

トークン認証が有効になっている場合は、`get_token` メソッドを使用して、JSON Web トークン (JWT) とそのトークンの有効期限を取得できます。

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> トークンの `refresh_by` 時刻の後に新しいトークンを要求する必要があります。
>
> Azure Machine Learning ワークスペースは、ご利用の Azure Kubernetes Service クラスターと同じリージョンに作成することを強くお勧めします。 
>
> トークンを使用して認証するために、Web サービスは、Azure Machine Learning ワークスペースの作成先のリージョンに対して呼び出しを行います。 ワークスペースのリージョンが利用不可になった場合、ワークスペースとは異なるリージョンにクラスターがあったとしても、Web サービスのトークンがフェッチできなくなります。 その結果、ワークスペースのリージョンが再び使用可能になるまで Azure AD Authentication は使用できなくなります。 
>
> また、クラスターのリージョンとワークスペースのリージョンとの間の距離が長くなるほど、トークンのフェッチにかかる時間も長くなります。

## <a name="authorization"></a>Authorization

複数のワークスペースを作成でき、各ワークスペースを複数のユーザーで共有できます。 ワークスペースを共有する場合は、これらのロールをユーザーに割り当てることで、ワークスペースへのアクセスを制御できます。

* Owner
* Contributor
* Reader

次の表では、いくつかの主要な Azure Machine Learning の操作とそれらを実行できるロールを示します。

| Azure Machine Learning の操作 | Owner | Contributor | Reader |
| ---- |:----:|:----:|:----:|
| ワークスペースの作成 | ✓ | ✓ | |
| ワークスペースを共有する | ✓ | |  |
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

組み込みのロールがニーズを満たしていない場合は、カスタムのロールを作成できます。 カスタム ロールは、ワークスペースと Machine Learning コンピューティングに対する操作でのみサポートされます。 カスタム ロールでは、ワークスペースとそのワークスペース内のコンピューティング リソースに対する読み取り、書き込み、または削除のアクセス許可を持つことができます。 ロールは、特定のワークスペース レベル、特定のリソース グループ レベル、または特定のサブスクリプション レベルで使用できるようにすることができます。 詳細については、[Azure Machine Learning ワークスペースでのユーザーとロールの管理](how-to-assign-roles.md)に関するページを参照してください。

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

Azure Machine Learning では、すべてのワークスペース リージョンのサブスクリプションに、共同作成者レベルのアクセス権を持つ追加のアプリケーション (名前が `aml-` または `Microsoft-AzureML-Support-App-` で始まるもの) が作成されます。 たとえば、同じサブスクリプション内で、米国東部にあるワークスペースがあり、北ヨーロッパには別のワークスペースがある場合、これらのアプリケーションが 2 つ表示されます。 これらのアプリケーションでは、コンピューティング リソースの管理に役立つ Azure Machine Learning を有効にします。

## <a name="network-security"></a>ネットワークのセキュリティ

Azure Machine Learning は、コンピューティング リソースに関して他の Azure サービスに依存します。 コンピューティング リソース (コンピューティング ターゲット) は、モデルのトレーニングとデプロイに使用されます。 これらのコンピューティング先は、仮想ネットワーク内に作成することができます。 たとえば、Azure Data Science Virtual Machine を使用してモデルをトレーニングしてから、そのモデルを AKS にデプロイできます。  

詳しくは、[仮想ネットワークで実験と推論を実行する方法](how-to-enable-virtual-network.md)に関する記事をご覧ください。

## <a name="data-encryption"></a>データの暗号化

### <a name="encryption-at-rest"></a>保存時の暗号化

#### <a name="azure-blob-storage"></a>Azure BLOB ストレージ

Azure Machine Learning では、Azure Machine Learning ワークスペースとサブスクリプションに関連付けられている Azure BLOB ストレージ アカウントにスナップショット、出力、ログを格納します。 Azure BLOB ストレージに格納されるすべてのデータは、Microsoft によって管理されたキーを使用して保存時に暗号化されます。

Azure BLOB ストレージに格納されるデータに独自のキーを使用する方法については、[Azure Key Vault でのカスタマー マネージド キーによる Azure Storage の暗号化](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys)に関するページを参照してください。

通常はトレーニング データも、トレーニング コンピューティング先にアクセスできるように Azure BLOB ストレージに格納されます。 このストレージは、Azure Machine Learning によって管理されませんが、リモート ファイル システムとしてコンピューティング先にマウントされます。

ご利用のワークスペースで使われる Azure ストレージ アカウントのアクセス キーの再生成については、[ストレージ アクセス キーの再生成](how-to-change-storage-access-key.md)に関するページを参照してください。

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Machine Learning では、Azure Machine Learning によって管理される Microsoft サブスクリプションに関連付けられた Azure Cosmos DB インスタンスにメトリックとメタデータが格納されます。 Azure Cosmos DB に格納されているすべてのデータは、Microsoft によって管理されるキーを使用して保存時に暗号化されます。

#### <a name="azure-container-registry"></a>Azure Container Registry

レジストリ (Azure Container Registry) 内のすべてのコンテナー イメージは、保存時に暗号化されます。 Azure では、イメージは保存前に自動的に暗号化され、Azure Machine Learning がイメージをプルするときにその場で暗号解除されます。

#### <a name="machine-learning-compute"></a>Machine Learning コンピューティング

Azure Storage に格納されている各コンピューティング ノードの OS ディスクは、Azure Machine Learning ストレージ アカウント内の Microsoft によって管理されるキーを使用して暗号化されます。 このコンピューティング先は一時的なものであり、キューに入れられた実行がない場合、通常はクラスターがスケールダウンされます。 基になる仮想マシンのプロビジョニングは解除され、OS ディスクは削除されます。 OS ディスクでは Azure Disk Encryption はサポートされません。

各仮想マシンにも、OS 操作用にローカルな一時ディスクがあります。 必要に応じて、ディスクを使用してトレーニング データをステージできます。 ディスクは暗号化されません。
Azure での保存時の暗号化のしくみについて詳しくは、[保存時の Azure データの暗号化](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)を参照してください。

### <a name="encryption-in-transit"></a>転送中の暗号化

SSL を使用して、Azure Machine Learning マイクロサービス間の内部通信をセキュリティで保護し、スコアリング エンドポイントへの外部呼び出しをセキュリティで保護することができます。 すべての Azure Storage アクセスも、セキュリティで保護されたチャネル経由で行われます。

詳細については、「[SSL を使用して Azure Machine Learning による Web サービスをセキュリティで保護する](https://docs.microsoft.com/azure/machine-learning/service/how-to-secure-web-service)」を参照してください。

### <a name="using-azure-key-vault"></a>Azure Key Vault の使用

Azure Machine Learning では、ワークスペースに関連付けられた Azure Key Vault インスタンスを使用して、さまざまな種類の資格情報を格納します。

* 関連付けられたストレージ アカウントの接続文字列
* Azure コンテナー リポジトリ インスタンスへのパスワード
* データ ストアへの接続文字列

Azure HDInsight や VM などのコンピューティング先に対する SSH パスワードとキーは、Microsoft サブスクリプションに関連付けられている別のキー コンテナーに格納されます。 Azure Machine Learning には、ユーザーによって提供されるパスワードやキーは格納されません。 代わりに、VM と HDInsight に接続して実験を行うために、独自の SSH キーを生成、承認、および格納します。

各ワークスペースには、ワークスペースと同じ名前を持つ、関連付けられたシステム割り当て済みマネージド ID があります。 このマネージド ID では、キー コンテナー内のすべてのキー、シークレット、および証明書にアクセスできます。

## <a name="monitoring"></a>監視

### <a name="metrics"></a>メトリック

Azure Monitor メトリックを使用し、Azure Machine Learning ワークスペースのメトリックを表示したり、監視したりできます。 [Azure portal](https://portal.azure.com) で、ワークスペースを選び、 **[メトリック]** を選択します。

[![ワークスペースのメトリックの例を示すスクリーンショット](./media/enterprise-readiness/workspace-metrics.png)](./media/enterprise-readiness/workspace-metrics-expanded.png)

メトリックには、実行、デプロイ、および登録に関する情報が含まれます。

詳しくは、「[Metrics in Azure Monitor (Azure Monitor のメトリック)](/azure/azure-monitor/platform/data-platform-metrics)」をご覧ください。

### <a name="activity-log"></a>アクティビティ ログ

ワークスペースのアクティビティ ログを表示して、ワークスペース上で実行されるさまざまな操作を確認できます。 ログには、操作名、イベント イニシエーター、タイムスタンプなどの基本情報が含まれています。

このスクリーンショットは、ワークスペースのアクティビティ ログを示しています。

[![ワークスペースのアクティビティ ログを示すスクリーンショット](./media/enterprise-readiness/workspace-activity-log.png)](./media/enterprise-readiness/workspace-activity-log-expanded.png)

スコアリング要求の詳細は Application Insights に格納されます。 Application Insights は、ワークスペースの作成時にサブスクリプションに作成されます。 ログに記録される情報には、HTTPMethod、UserAgent、ComputeType、RequestUrl、StatusCode、RequestId、および Duration などのフィールドが含まれます。

> [!IMPORTANT]
> Azure Machine Learning ワークスペース内の一部のアクションでは、情報はアクティビティ ログに記録されません。 たとえば、トレーニングの実行の開始やモデルの登録はログに記録されません。
>
> これらのアクションの一部はワークスペースの **[アクティビティ]** 領域に表示されますが、これらの通知ではアクティビティを開始したユーザーは示されません。

## <a name="data-flow-diagrams"></a>データ フロー図

### <a name="create-workspace"></a>ワークスペースの作成

次の図は、ワークスペース作成のワークフローを示したものです。

* ユーザーは、サポートされている Azure Machine Learning クライアント (Azure CLI、Python SDK、Azure portal) のいずれかから Azure AD にサインインし、適切な Azure Resource Manager トークンを要求します。
* ユーザーは、ワークスペースを作成するために Azure Resource Manager を呼び出します。 
* Azure Resource Manager では、Azure Machine Learning のリソース プロバイダーに連絡し、ワークスペースをプロビジョニングします。

ワークスペースの作成中に、追加のリソースがユーザーのサブスクリプションに作成されます。

* Key Vault (シークレットの格納用)
* Azure ストレージ アカウント (BLOB とファイル共有を含む)
* Azure Container Registry (推論/スコアリングと実験のための Docker イメージの格納用)
* Application Insights (テレメトリ格納用)

ユーザーは、必要に応じて、(Azure Kubernetes Service や VM などの) ワークスペースにアタッチされている他のコンピューティング先をプロビジョニングすることもできます。

[![ワークスペースの作成ワークフロー](./media/enterprise-readiness/create-workspace.png)](./media/enterprise-readiness/create-workspace-expanded.png)

### <a name="save-source-code-training-scripts"></a>ソース コードを保存する (トレーニング スクリプト)

次の図は、コード スナップショットのワークフローを示したものです。

Azure Machine Learning ワークスペースに関連付けられているディレクトリ (実験) には、ソース コード (トレーニング スクリプト) が含まれます。 これらのスクリプトは、ご利用のローカル コンピューターとクラウド (ご利用のサブスクリプションの Azure BLOB ストレージ内) に格納されます。 このコード スナップショットは、履歴監査の実行または検査に使用されます。

[![コード スナップショット ワークフロー](./media/enterprise-readiness/code-snapshot.png)](./media/enterprise-readiness/code-snapshot-expanded.png)

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

[![トレーニング ワークフロー](./media/enterprise-readiness/training-and-metrics.png)](./media/enterprise-readiness/training-and-metrics-expanded.png)

### <a name="creating-web-services"></a>Web サービスを作成する

次の図は、推論のワークフローを示しています。 推論 (つまり、モデルのスコアリング) は、通常は運用データに基づいて、デプロイされたモデルを使用して予測を行うフェーズです。

これらについて詳しく説明します。

* ユーザーは、Azure Machine Learning SDK などのクライアントを使用して、モデルを登録します。
* ユーザーは、モデル、スコア ファイル、およびその他のモデルの依存関係を使用して、イメージを作成します。
* Docker イメージが作成されて、Azure Container Registry で格納されます。
* Web サービスは、前の手順で作成されたイメージを使用して、コンピューティング先 (Container Instances/AKS) にデプロイされます。
* スコアリング要求の詳細は、ユーザーのサブスクリプション内の Application Insights に格納されます。
* テレメトリも Microsoft/Azure サブスクリプションにプッシュされます。

[![推論のワークフロー](./media/enterprise-readiness/inferencing.png)](./media/enterprise-readiness/inferencing-expanded.png)

## <a name="next-steps"></a>次の手順

* [SSL を使用して Azure Machine Learning Web サービスをセキュリティで保護する](how-to-secure-web-service.md)
* [Web サービスとしてデプロイされた Machine Learning モデルを使用する](how-to-consume-web-service.md)
* [バッチ予測を実行する方法](how-to-run-batch-predictions.md)
* [Application Insights を使用して Azure Machine Learning のモデルを監視する](how-to-enable-app-insights.md)
* [実稼働環境でモデルのデータを収集する](how-to-enable-data-collection.md)
* [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Azure Machine Learning と Azure Virtual Network を使用する](how-to-enable-virtual-network.md)
* [推奨システムを構築するためのベスト プラクティス](https://github.com/Microsoft/Recommenders)
* [Azure 上でリアルタイム レコメンデーション API を構築する](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
