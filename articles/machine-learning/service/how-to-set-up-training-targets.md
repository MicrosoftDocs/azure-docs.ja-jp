---
title: モデル トレーニング用のコンピューティング先を作成して使用する
titleSuffix: Azure Machine Learning service
description: 機械学習モデル トレーニング用のトレーニング環境 (コンピューティング ターゲット) を構成します。 トレーニング環境を簡単に切り替えることができます。 ローカルでトレーニングを開始します。 スケール アウトする必要がある場合は、クラウド ベースのコンピューティング先に切り替えます。
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 06/12/2019
ms.custom: seodec18
ms.openlocfilehash: 267872f2036a0e697f4b2da65064805a0cfbd2b7
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358740"
---
# <a name="set-up-compute-targets-for-model-training"></a>モデル トレーニング用のコンピューティング ターゲットを設定する 

Azure Machine Learning service では、さまざまなリソースまたは環境でモデルをトレーニングでき、それらを総称して[__コンピューティング先__](concept-azure-machine-learning-architecture.md#compute-targets)と呼びます。 コンピューティング先は、ローカル マシンでも、Azure Machine Learning コンピューティング、Azure HDInsight、リモート仮想マシンなどのクラウド リソースでもかまいません。  [モデルをデプロイする場所と方法](how-to-deploy-and-where.md)に関するページで説明されているように、モデルのデプロイ用のコンピューティング先を作成することもできます。

Azure Machine Learning SDK、Azure portal、Azure CLI、または Azure Machine Learning VS Code 拡張機能を使用してコンピューティング ターゲットを作成および管理できます。 別のサービス (たとえば、HDInsight クラスター) によって作成されたコンピューティング ターゲットがある場合、それらを Azure Machine Learning service ワークスペースに接続して使用できます。
 
この記事では、モデル トレーニング用にさまざまなコンピューティング先を使用する方法について説明します。  すべてのコンピューティング先の手順が、同じワークフローに従います。
1. まだない場合は、コンピューティング先を __作成__ します。
2. コンピューティング先をワークスペースに __アタッチ__ します。
3. スクリプトに必要な Python 環境とパッケージ依存関係が含まれるように、コンピューティング先を __構成__ します。


>[!NOTE]
> この記事のコードは、Azure Machine Learning SDK バージョン 1.0.39 を使用してテストされました。

## <a name="compute-targets-for-training"></a>モデル トレーニング用のコンピューティング先

Azure Machine Learning service では、異なるコンピューティング先に対してさまざまなサポートが提供されています。 典型的なモデル開発ライフサイクルは、少量のデータを用いた開発と実験から始まります。 この段階では、ローカル環境を使用することをお勧めします。 たとえば、ローカル コンピューターやクラウドベースの VM などです。 より大規模なデータ セットにトレーニングをスケールアップする、または分散トレーニングを実行する段階で、Azure Machine Learning コンピューティングを使用して、実行を送信するたびに自動スケーリングするシングルノードまたはマルチノード クラスターを作成することをお勧めします。 独自のコンピューティング リソースを接続することもできますが、以下で説明するように、シナリオによってサポートが異なる場合があります:

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]


> [!NOTE]
> Azure Machine Learning コンピューティングは、永続的なリソースとして作成するか、実行を要求するときに動的に作成できます。 実行ベースの作成では、トレーニングの実行の完了後にコンピューティング ターゲットが削除されるため、この方法で作成されたコンピューティング ターゲットは再利用できません。

## <a name="whats-a-run-configuration"></a>実行構成とは

トレーニングのときは、ローカル コンピューターで開始し、後で別のコンピューティング先でそのトレーニング スクリプトを実行するのが一般的です。 Azure Machine Learning service では、スクリプトを変更しなくても、さまざまなコンピューティング先でスクリプトを実行できます。 

必要なのは、**実行構成**で各コンピューティング先の環境を定義することだけです。  その後、異なるコンピューティング先でトレーニング実験を実行するときは、そのコンピューティングの実行構成を指定します。 

詳しくは、この記事の最後にある[実験の送信](#submit)に関する説明をご覧ください。

### <a name="manage-environment-and-dependencies"></a>環境と依存関係を管理する

実行構成を作成するときは、コンピューティング先で環境と依存関係を管理する方法を決定する必要があります。 

#### <a name="system-managed-environment"></a>システム管理環境

Python 環境とスクリプトの依存関係を [Conda](https://conda.io/docs/) で自動的に管理したいときは、システム管理環境を使用します。 システム管理環境は既定で想定されており、最も一般的な選択肢です。 リモート コンピューティング先で便利であり、そのターゲットを構成できないときは特に有効です。 

行う必要があるのは、[CondaDependency クラス](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py)を使用して各パッケージ依存関係を指定することだけです。後は、Conda によって、お使いのワークスペースの **aml_config** ディレクトリに、パッケージの依存関係のリストが含まれる **conda_dependencies.yml** という名前のファイルが作成され、トレーニング実験を送信するときに Python 環境がセットアップされます。 

新しい環境の初期セットアップは、必要な依存関係のサイズによっては数分かかる可能性があります。 パッケージの一覧が変更されない限り、セットアップ時間が発生するのは 1 回だけです。
  
次のコードでは、Scikit-learn を必要とするシステム管理環境の例を示します。
    
[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/runconfig.py?name=run_system_managed)]

#### <a name="user-managed-environment"></a>ユーザー管理環境

ユーザー管理環境の場合は、ユーザーが環境を設定し、トレーニング スクリプトで必要なすべてのパッケージをコンピューティング ターゲットにインストールする必要があります。 トレーニング環境が (ローカル コンピューター上などに) 既に構成されている場合は、`user_managed_dependencies` を True に設定することで、セットアップ手順をスキップできます。 Conda で自動的に環境やインストールの確認が行われることはありません。

次のコードでは、ユーザー管理環境用のトレーニング実行構成の例を示します。

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/runconfig.py?name=run_user_managed)]
  
## <a name="set-up-in-python"></a>Python での設定

以下のセクションでは、次のコンピューティング先を構成する方法を示します。

* [ローカル コンピューター](#local)
* [Azure Machine Learning コンピューティング](#amlcompute)
* [リモート仮想マシン](#vm)
* [Azure HDInsight](#hdinsight)


### <a id="local"></a>ローカル コンピューター

1. **作成してアタッチする**:トレーニング環境としてローカル コンピューターを使用する場合は、コンピューティング先を作成またはアタッチする必要はありません。  

1. **構成する**:ローカル コンピューターをコンピューティング先として使用すると、トレーニング コードは自分の[開発環境](how-to-configure-environment.md)で実行されます。  その環境に必要な Python パッケージが既にある場合は、ユーザー管理環境を使用します。

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

コンピューティングをアタッチし、実行を構成したので、次のステップでは[トレーニング実行を送信](#submit)します。

### <a id="amlcompute"></a>Azure Machine Learning コンピューティング

Azure Machine Learning コンピューティングは、ユーザーがシングルノードまたはマルチノードのコンピューティングを簡単に作成できる、マネージド コンピューティング インフラストラクチャです。 コンピューティングは、リソースとしてワークスペース リージョン内に作成され、ワークスペース内の他のユーザーと共有できます。 コンピューティングはジョブが送信されると自動的にスケールアップされ、Azure 仮想ネットワークに配置できます。 コンピューティングはコンテナー化環境で実行され、モデルの依存関係が [Docker コンテナー](https://www.docker.com/why-docker)にパッケージ化されます。

Azure Machine Learning コンピューティングを使用して、クラウド内の CPU または GPU コンピューティング ノードのクラスター全体にトレーニング プロセスを分散させることができます。 GPU を含む VM サイズの詳細については、「[GPU 最適化済み仮想マシンのサイズ](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)」を参照してください。

Azure Machine Learning コンピューティングには、割り当て可能なコア数などの既定の制限があります。 詳細については、「[Azure リソースのクォータの管理と要求](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas)」を参照してください。


Azure Machine Learning コンピューティング環境は、実行をスケジュールするときにオンデマンドで、または永続的なリソースとして作成できます。

#### <a name="run-based-creation"></a>実行ベースの作成

実行時にコンピューティング先として Azure Machine Learning コンピューティングを作成できます。 実行用にコンピューティングが自動的に作成されます。 実行が完了すると、コンピューティングは自動的に削除されます。 

> [!NOTE]
> 使用するノードの最大数を指定するには、`node_count` にノード数を設定します。 2019 年 4 月 4 日現在、バグが原因で、この操作を行うことができません。 回避策として、実行構成の `amlcompute._cluster_max_node_count` プロパティを使用してください。 たとえば、「 `run_config.amlcompute._cluster_max_node_count = 5` 」のように入力します。

> [!IMPORTANT]
> Azure Machine Learning コンピューティングの実行ベースの作成は現在、プレビュー状態です。 自動化されたハイパーパラメーター チューニングまたは自動化された機械学習を使用する場合は、実行ベースの作成を使用しないでください。 ハイパーパラメーター チューニングまたは自動機械学習を使用するには、代わりに[永続的なコンピューティング](#persistent)先を作成します。

1.  **作成、アタッチ、構成する**:実行ベースの作成により、実行構成に基づいて、コンピューティング先の作成、アタッチ、構成に必要なすべての手順が行われます。  

  [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute.py?name=run_temp_compute)]


コンピューティングをアタッチし、実行を構成したので、次のステップでは[トレーニング実行を送信](#submit)します。

#### <a id="persistent"></a>永続的なコンピューティング

永続的な Azure Machine Learning コンピューティングは、複数のジョブで再利用できます。 コンピューティングはワークスペース内の他のユーザーと共有でき、ジョブ間で保持されます。

1. **作成してアタッチする**:Python で永続的な Azure Machine Learning コンピューティング リソースを作成するには、**vm_size** および **max_nodes** プロパティを指定します。 その後、Azure Machine Learning では他のプロパティに対してスマート既定値が使用されます。 コンピューティングは、使用されていないときは、0 ノードまで自動的にスケールダウンされます。   必要に応じて、ジョブ実行専用の VM が作成されます。
    
    * **vm_size**:Azure Machine Learning コンピューティングによって作成されるノードの VM ファミリ。
    * **max_nodes**:Azure Machine Learning コンピューティングでジョブを実行中に自動スケールアップする最大ノード数。
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   Azure Machine Learning コンピューティングを作成するときに、いくつかの詳細プロパティも設定できます。 これらのプロパティを使用すると、永続的なクラスターを固定サイズで、またはサブスクリプションの既存の Azure 仮想ネットワーク内に作成できます。  詳しくは、「[AmlCompute クラス](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    )」をご覧ください。
    
   または、[Azure portal で](#portal-create)永続的な Azure Machine Learning コンピューティング リソースを作成してアタッチすることもできます。

1. **構成する**:永続的なコンピューティング先の実行構成を作成します。

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

コンピューティングをアタッチし、実行を構成したので、次のステップでは[トレーニング実行を送信](#submit)します。


### <a id="vm"></a>リモート仮想マシン

Azure Machine Learning では、独自のコンピューティング リソースを用意してワークスペースに接続することもサポートされています。 任意のリモート VM もそのようなリソースの一種ですが、Azure Machine Learning service からアクセスできることが条件です。 リソースは、Azure VM でも、組織内またはオンプレミスのリモート サーバーでもかまいません。 具体的には、IP アドレスと資格情報 (ユーザー名とパスワードまたは SSH キー) があれば、任意のアクセス可能な VM をリモート実行に使用できます。

システムで構築済みの conda 環境、既存の Python 環境、または Docker コンテナーを使用できます。 Docker コンテナーで実行するには、Docker エンジンを VM で実行する必要があります。 この機能は、ローカル コンピューターよりも柔軟性がある、クラウドベースの開発/実験環境が必要な場合に特に役立ちます。

このシナリオ向けに選択する Azure VM としては、Data Science Virtual Machine (DSVM) を使用します。 この VM は、Azure での事前構成済みのデータ サイエンスおよび AI 開発環境です。 その VM では、完全なライフサイクルの機械学習開発用に精選されたツールとフレームワークが提供されます。 Azure Machine Learning での DSVM の使用方法について詳しくは、[開発環境の構成](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm)に関する記事をご覧ください。

1. **作成**:モデルのトレーニングに使用する DSVM を事前に作成します。 このリソースの作成については、「[Linux (Ubuntu) データ サイエンス仮想マシンのプロビジョニング](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)」をご覧ください。

    > [!WARNING]
    > Azure Machine Learning は、Ubuntu を実行する仮想マシンのみサポートします。 VM を作成するとき、または既存の VM を選択するときは、Ubuntu を使用する VM を選択する必要があります。

1. **アタッチする**:コンピューティング ターゲットとして既存の仮想マシンを接続するには、仮想マシンの完全修飾ドメイン名 (FQDN)、ユーザー名、およびパスワードを入力する必要があります。 例では、\<fqdn> を VM のパブリック FQDN、またはパブリック IP アドレスに置き換えます。 \<username> と \<password> を、VM の SSH ユーザー名とパスワードで置き換えます。

   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   attach_config = RemoteCompute.attach_configuration(address = "<fqdn>",
                                                    ssh_port=22,
                                                    username='<username>',
                                                    password="<password>")

   # If you authenticate with SSH keys instead, use this code:
   #                                                  ssh_port=22,
   #                                                  username='<username>',
   #                                                  password=None,
   #                                                  private_key_file="<path-to-file>",
   #                                                  private_key_passphrase="<passphrase>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   または、[Azure portal を使用して](#portal-reuse)ワークスペースに DSVM をアタッチすることもできます。

1. **構成する**:DSVM コンピューティング先用の実行構成を作成します。 Docker と conda は、DSVM でトレーニング環境を作成および構成するために使用されます。

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


コンピューティングをアタッチし、実行を構成したので、次のステップでは[トレーニング実行を送信](#submit)します。

### <a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight は、ビッグ データ分析のための一般的なプラットフォームです。 そのプラットフォームでは、モデルのトレーニングに使用できる Apache Spark が提供されます。

1. **作成**:モデルのトレーニングに使用する HDInsight クラスターを、事前に作成します。 HDInsight クラスターで Spark を作成するには、[HDInsight での Spark クラスターの作成](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql)に関する記事をご覧ください。 

    クラスターを作成するとき、SSH ユーザー名とパスワードを指定する必要があります。 コンピューティング先として HDInsight を使用するときに必要になるので、これらの値をメモしておいてください。
    
    クラスターが作成された後、ホスト名 \<clustername>-ssh.azurehdinsight.net でそれに接続します。\<clustername> は、ユーザーがクラスターに指定した名前です。 

1. **アタッチする**:コンピューティング先として HDInsight クラスターをアタッチするには、HDInsight クラスターのホスト名、ユーザー名、およびパスワードを指定する必要があります。 次の例では、SDK を使用してクラスターをワークスペースに接続します。 例の \<clustername> は、実際のクラスターの名前に置き換えます。 \<username> と \<password> を、クラスターの SSH ユーザー名とパスワードで置き換えます。

   ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase
    attach_config = HDInsightCompute.attach_configuration(address='<clustername>-ssh.azureinsight.net', 
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   または、[Azure portal を使用して](#portal-reuse)ワークスペースに HDInsight クラスターをアタッチすることもできます。

1. **構成する**:HDI コンピューティング先用の実行構成を作成します。 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


コンピューティングをアタッチし、実行を構成したので、次のステップでは[トレーニング実行を送信](#submit)します。


### <a id="azbatch"></a>Azure Batch 

Azure Batch は、大規模な並列コンピューティングやハイパフォーマンス コンピューティング (HPC) のアプリケーションをクラウドで効率的に実行するために使用されます。 AzureBatchStep を Azure Machine Learning Pipeline で使用して、マシンの Azure Batch プールにジョブを送信できます。

コンピューティング ターゲットとして Azure Batch に接続するには、Azure Machine Learning SDK を使用し、次の情報を提供する必要があります。

-   **Azure Batch のコンピューティング名**:ワークスペース内のコンピューティングに使用されるフレンドリ名
-   **Azure Batch アカウント名**:Azure Batch アカウントの名前
-   **リソース グループ**:Azure Batch アカウントを含むリソース グループ。

次のコードは、コンピューティング ターゲットとして Azure Batch に接続する方法を示しています。

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

# Name to associate with new compute in workspace
batch_compute_name = 'mybatchcompute'

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>"  # Name of the Batch account
# Name of the resource group which contains this account
batch_resource_group = "<batch_resource_group>"

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(
        resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(
        ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

## <a name="set-up-in-azure-portal"></a>Azure Portal での設定

Azure portal でワークスペースに関連付けられたコンピューティング先にアクセスできます。  ポータルを使用すると以下のことができます。

* ワークスペースにアタッチされている[コンピューティング先を表示する](#portal-view)
* ワークスペースに[コンピューティング先を作成する](#portal-create)
* ワークスペースの外部に作成された[コンピューティング ターゲットにアタッチする](#portal-reuse)

コンピューティング先を作成してワークスペースにアタッチした後は、`ComputeTarget` オブジェクトを使用して実行構成でそれを使用します。 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a id="portal-view"></a>コンピューティング先を表示する


ワークスペースのコンピューティング先を表示するには、次の手順を使用します。

1. [Azure portal](https://portal.azure.com) に移動し、ワークスペースを開きます。 
1. __[アプリケーション]__ で __[Compute]__ を選択します。

    ![[計算] タブを表示する](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)

### <a id="portal-create"></a>コンピューティング先を作成する

コンピューティング先の一覧を表示するには、前の手順に従います。 その後、次の手順を使用してコンピューティング先を作成します。 

1. プラス記号 (+) を選択して、コンピューティング先を追加します。

    ![コンピューティング先を追加する](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. コンピューティング ターゲットの名前を入力します。 

1. __[トレーニング]__ に使用するコンピューティングの種類として **[Machine Learning コンピューティング]** を選択します。 

    >[!NOTE]
    >Azure portal で作成できるマネージド コンピューティング リソースは、Azure Machine Learning コンピューティングだけです。  他のすべてのコンピューティング リソースは、作成した後でアタッチできます。

1. フォームに入力します。 必須のプロパティの値を指定します。特に、コンピューティングの起動に使用する **[VM ファミリ]** と **[最大ノード数]** を指定します。  

    ![フォームに入力する](./media/how-to-set-up-training-targets/add-compute-form.png) 

1. __作成__ を選択します。


1. 一覧からコンピューティング先を選択することによって、作成操作の状態を表示します。

    ![作成操作の状態を表示するコンピューティング先を選択する](./media/how-to-set-up-training-targets/View_list.png)

1. コンピューティング先の詳細が表示されます。 

    ![コンピューティング先の詳細を表示する](./media/how-to-set-up-training-targets/compute-target-details.png) 



### <a id="portal-reuse"></a>コンピューティング ターゲットにアタッチする

Azure Machine Learning service ワークスペースの外部に作成されたコンピューティング ターゲットを使用するには、それらをアタッチする必要があります。 コンピューティング ターゲットをアタッチすることで、ワークスペースで利用できるようにします。

コンピューティング先の一覧を表示するには、前に説明した手順に従います。 その後、次の手順を使用して、コンピューティング ターゲットをアタッチします。 

1. プラス記号 (+) を選択して、コンピューティング先を追加します。 
1. コンピューティング ターゲットの名前を入力します。 
1. __トレーニング__ に接続するコンピューティングの種類を選択します。

    > [!IMPORTANT]
    > Azure portal からすべてのコンピューティングの種類を接続できるわけではありません。 現在トレーニング用に接続できるコンピューティングの種類は次のとおりです。
    >
    > * リモート VM
    > * Azure Databricks (機械学習パイプラインで使用)
    > * Azure Data Lake Analytics (機械学習パイプラインで使用)
    > * Azure HDInsight

1. フォームに入力し、必要なプロパティの値を指定します。

    > [!NOTE]
    > パスワードよりも安全な SSH キーを使用することをお勧めします。 パスワードはブルート フォース攻撃に対して脆弱です。 SSH キーは暗号署名に依存します。 Azure Virtual Machines で使用するための SSH キーを作成する方法の詳細については、次のドキュメントを参照してください。
    >
    > * [Linux または macOS で SSH キーを作成して使用する](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Windows で SSH キーを作成して使用する](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. __[接続]__ を選択します。 
1. 一覧からコンピューティング先を選択することによって、接続操作の状態を表示します。

## <a name="set-up-with-cli"></a>CLI を使用した設定

Azure Machine Learning service 用の [CLI 拡張機能](reference-azure-machine-learning-cli.md)を使用して、ワークスペースに関連付けられたコンピューティング先にアクセスすることができます。  CLI を使用して次のことができます。

* マネージド コンピューティング先を作成する
* マネージド コンピューティング先を更新する
* アンマネージド コンピューティング先をアタッチする

詳しくは、「[リソース管理](reference-azure-machine-learning-cli.md#resource-management)」をご覧ください。

## <a name="set-up-with-vs-code"></a>VS Code を使用した設定

Azure Machine Learning service 用の [VS Code 拡張機能](how-to-vscode-tools.md#create-and-manage-compute-targets)を使用して、ワークスペースに関連付けられたコンピューティング先にアクセスし、これを作成および管理することができます。

## <a id="submit"></a>トレーニングの実行を送信する

実行構成を作成した後は、それを使用して実験を実行します。  トレーニングの実行を送信するためのコード パターンは、すべての種類のコンピューティング先について同じです。

1. 実行する実験を作成する
1. 実行を送信します。
1. 実行が完了するのを待ちます。

> [!IMPORTANT]
> トレーニングの実行を送信すると、トレーニング スクリプトが含まれるディレクトリのスナップショットが作成されて、コンピューティング先に送信されます。 スナップショットは、ご利用のワークスペースにも実験の一部として保存されます。 ファイルを変更して実行を再度送信すると、変更したファイルのみがアップロードされます。
>
> スナップショットにファイルが含まれないようにするには、[.gitignore](https://git-scm.com/docs/gitignore) または `.amlignore` ファイルをディレクトリに作成して、そこにそれらのファイルを追加してください。 `.amlignore` ファイルには、[.gitignore](https://git-scm.com/docs/gitignore) ファイルと同じ構文とパターンが使用されます。 両方のファイルが存在する場合、`.amlignore` ファイルが優先されます。
> 
> 詳細については、「[スナップショット](concept-azure-machine-learning-architecture.md#snapshots)」を参照してください。

### <a name="create-an-experiment"></a>実験の作成

最初に、自分のワークスペース内に実験を作成します。

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=experiment)]

### <a name="submit-the-experiment"></a>実験を送信する

`ScriptRunConfig` オブジェクトを使用して実験を送信します。  このオブジェクトには以下のものが含まれます。

* **source_directory**:トレーニング スクリプトが格納されているソース ディレクトリ
* **script**:トレーニング スクリプトを示します
* **run_config**:トレーニングが行われる場所が定義されている実行構成。

たとえば、[ローカル ターゲット](#local)の構成を使用するには、次のようになります。

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=local_submit)]

[amlcompute ターゲット](#amlcompute)などの別の実行構成を使用して、別のコンピューティング先で実行するように、同じ実験を切り替えます。

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=amlcompute_submit)]

または、次のことができます。

* [Estimator での ML モデルのトレーニング](how-to-train-ml-models.md)に関する記事で示されているように、`Estimator` オブジェクトを使用して実験を送信します。
* [CLI 拡張機能を使用](reference-azure-machine-learning-cli.md#experiments)して実験を送信します。
* [VS Code 拡張機能](how-to-vscode-tools.md#train-and-tune-models)を介して実験を送信します。

## <a name="github-tracking-and-integration"></a>GitHub の追跡と統合

ソース ディレクトリがローカル Git リポジトリであるトレーニング実行を開始すると、リポジトリに関する情報が実行履歴に格納されます。 たとえば、リポジトリの現在のコミット ID が履歴の一部としてログに記録されます。

## <a name="notebook-examples"></a>ノートブックの例

さまざまなコンピューティング先を使用したトレーニングの例については、以下のノートブックをご覧ください。
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>次の手順

* [チュートリアル:モデルのトレーニング](tutorial-train-models-with-aml.md)に関する記事では、マネージド コンピューティング先を使用してモデルをトレーニングします。
* より優れたモデルを構築するために、[ハイパーパラメーター](how-to-tune-hyperparameters.md)を効率的に調整する方法を学習します。
* モデルのトレーニングが済んだら、[モデルをデプロイする方法と場所](how-to-deploy-and-where.md)を確認します。
* [RunConfiguration クラス](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py)の SDK リファレンスを確認します。
* [Azure Machine Learning サービスと Azure Virtual Networks を使用する](how-to-enable-virtual-network.md)
