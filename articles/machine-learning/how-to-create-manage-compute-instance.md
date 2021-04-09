---
title: コンピューティング インスタンスを作成および管理する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning コンピューティング インスタンスを作成して管理する方法について説明します。 開発環境として、または Dev/Test 用のコンピューティング ターゲットとして使用します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 10/02/2020
ms.openlocfilehash: 6c29bf87c5f0ecaaeb6d608069791431a949c89b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103009965"
---
# <a name="create-and-manage-an-azure-machine-learning-compute-instance"></a>Azure Machine Learning コンピューティング インスタンスを作成して管理する

Azure Machine Learning ワークスペースで[コンピューティング インスタンス](concept-compute-instance.md)を作成および管理する方法について説明します。

コンピューティング インスタンスは、クラウド内で完全に構成され管理される自分の開発環境として使用します。 開発およびテストの場合、このインスタンスを[トレーニング コンピューティング ターゲット](concept-compute-target.md#train)として、または[推論ターゲット](concept-compute-target.md#deploy)に対して使用することもできます。   コンピューティング インスタンスは複数のジョブを並列に実行でき、ジョブ キューを備えています。 ご利用のワークスペース内の他のユーザーと、コンピューティング インスタンスを開発環境として共有することはできません。

この記事では、次のことについて説明します。

* コンピューティング インスタンスを作成する 
* コンピューティング インスタンスを管理する (開始、停止、再起動、削除)
* ターミナル ウィンドウにアクセスする 
* R または Python パッケージをインストールする
* 新しい環境または Jupyter カーネルを作成する

コンピューティング インスタンスは、企業で SSH ポートを開かなくても、[仮想ネットワーク環境](how-to-secure-training-vnet.md)でジョブを安全に実行できます。 ジョブはコンテナー化された環境で実行され、モデルの依存関係が Docker コンテナーにパッケージ化されます。 

## <a name="prerequisites"></a>前提条件

* Azure Machine Learning ワークスペース。 詳細については、[Azure Machine Learning ワークスペースの作成](how-to-manage-workspace.md)に関するページをご覧ください。

* [Machine Learning サービス向けの Azure CLI 拡張機能](reference-azure-machine-learning-cli.md)、[Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro)、または [Azure Machine Learning Visual Studio Code 拡張機能](tutorial-setup-vscode-extension.md)。

## <a name="create"></a>作成

**推定所要時間**: 約 5 分です。

コンピューティング インスタンスの作成は、ワークスペースに対する 1 回限りのプロセスです。 このコンピューティングは、開発ワークステーションとして、またはトレーニング用のコンピューティング先として再利用できます。 ワークスペースには複数のコンピューティング インスタンスをアタッチすることができます。

コンピューティング インスタンスの作成に適用されるリージョンあたりの専用コア数は、VM ファミリ クォータ別およびリージョン合計クォータ別に、Azure Machine Learning コンピューティング クラスターのクォータと統合され、共有されます。 コンピューティング インスタンスを停止しても、コンピューティング インスタンスを再起動できるように、クォータは解放されません。 コンピューティング インスタンスを一旦作成したら、その仮想マシンのサイズを変更できないことにご注意ください。

次の例では、コンピューティング インスタンスの作成方法を示します。

# <a name="python"></a>[Python](#tab/python)

```python
import datetime
import time

from azureml.core.compute import ComputeTarget, ComputeInstance
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your instance
# Compute instance name should be unique across the azure region
compute_name = "ci{}".format(ws._workspace_id)[:10]

# Verify that instance does not exist already
try:
    instance = ComputeInstance(workspace=ws, name=compute_name)
    print('Found existing instance, use it.')
except ComputeTargetException:
    compute_config = ComputeInstance.provisioning_configuration(
        vm_size='STANDARD_D3_V2',
        ssh_public_access=False,
        # vnet_resourcegroup_name='<my-resource-group>',
        # vnet_name='<my-vnet-name>',
        # subnet_name='default',
        # admin_user_ssh_public_key='<my-sshkey>'
    )
    instance = ComputeInstance.create(ws, compute_name, compute_config)
    instance.wait_for_completion(show_output=True)
```

この例で使われているクラス、メソッド、パラメーターの詳細については、次のリファレンス ドキュメントをご覧ください。

* [ComputeInstance クラス](/python/api/azureml-core/azureml.core.compute.computeinstance.computeinstance)
* [ComputeTarget.create](/python/api/azureml-core/azureml.core.compute.computetarget#create-workspace--name--provisioning-configuration-)
* [ComputeInstance.wait_for_completion](/python/api/azureml-core/azureml.core.compute.computeinstance(class)#wait-for-completion-show-output-false--is-delete-operation-false-)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az ml computetarget create computeinstance  -n instance -s "STANDARD_D3_V2" -v
```

詳細については、[az ml computetarget create computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext_azure_cli_ml_az_ml_computetarget_create_computeinstance) のリファレンスを参照してください。

# <a name="studio"></a>[スタジオ](#tab/azure-studio)

Azure Machine Learning Studio のワークスペースで、いずれかのノートブックを実行する準備ができたら、 **[計算]** セクションまたは **[ノートブック]** セクションから新しいコンピューティング インスタンスを作成します。

スタジオでコンピューティング インスタンスを作成する方法については、[Azure Machine Learning スタジオでのコンピューティング ターゲットの作成](how-to-create-attach-compute-studio.md#compute-instance)に関するページを参照してください。

---

また、[Azure Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance)を使用してコンピューティング インスタンスを作成することもできます。 

### <a name="create-on-behalf-of-preview"></a>代理作成 (プレビュー)

管理者は、データ科学者に代わってコンピューティング インスタンスを作成し、次のようにして彼らにそのインスタンスを割り当てることができます。
* [Azure Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance)  このテンプレートに必要な TenantID と ObjectID を見つける方法の詳細については、「[認証構成のための ID オブジェクト ID を見つける](../healthcare-apis/fhir/find-identity-object-ids.md)」を参照してください。  これらの値は Azure Active Directory ポータルでも見つけることができます。
* REST API

データ科学者向けにコンピューティング インスタンスを作成する場合は、次に示す [Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/overview.md) アクセス許可が必要です。 
* *Microsoft.MachineLearningServices/workspaces/computes/start/action*
* *Microsoft.MachineLearningServices/workspaces/computes/stop/action*
* *Microsoft.MachineLearningServices/workspaces/computes/restart/action*
* *Microsoft.MachineLearningServices/workspaces/computes/applicationaccess/action*

データ科学者は、コンピューティング インスタンスを開始、停止、再起動できます。 コンピューティング インスタンスは、次に使用できます。
* Jupyter
* JupyterLab
* RStudio
* 統合されたノートブック

## <a name="manage"></a>管理する

コンピューティング インスタンスを開始、停止、再起動、削除します。 コンピューティング インスタンスは自動的にスケールダウンされません。そのため、継続して課金されることがないように、リソースを必ず停止してください。

# <a name="python"></a>[Python](#tab/python)

次の例では、コンピューティング インスタンスの名前は **instance** になります

* 状態を取得する

    ```python
    # get_status() gets the latest status of the ComputeInstance target
    instance.get_status()
    ```

* Stop

    ```python
    # stop() is used to stop the ComputeInstance
    # Stopping ComputeInstance will stop the billing meter and persist the state on the disk.
    # Available Quota will not be changed with this operation.
    instance.stop(wait_for_completion=True, show_output=True)
    ```

* [開始]

    ```python
    # start() is used to start the ComputeInstance if it is in stopped state
    instance.start(wait_for_completion=True, show_output=True)
    ```

* やり直し

    ```python
    # restart() is used to restart the ComputeInstance
    instance.restart(wait_for_completion=True, show_output=True)
    ```

* 削除

    ```python
    # delete() is used to delete the ComputeInstance target. Useful if you want to re-use the compute name 
    instance.delete(wait_for_completion=True, show_output=True)
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

次の例では、コンピューティング インスタンスの名前は **instance** になります

* Stop

    ```azurecli-interactive
    az ml computetarget stop computeinstance -n instance -v
    ```

    詳細については、[az ml computetarget の computeinstance の停止](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance#ext-azure-cli-ml-az-ml-computetarget-computeinstance-stop)に関する記事をご覧ください。

* [開始] 

    ```azurecli-interactive
    az ml computetarget start computeinstance -n instance -v
    ```

    詳細については、[az ml computetarget の computeinstance の開始](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance#ext-azure-cli-ml-az-ml-computetarget-computeinstance-start)に関する記事をご覧ください。

* やり直し 

    ```azurecli-interactive
    az ml computetarget restart computeinstance -n instance -v
    ```

    詳細については、[az ml computetarget の computeinstance 再起動](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance#ext-azure-cli-ml-az-ml-computetarget-computeinstance-restart)に関する記事をご覧ください。

* 削除

    ```azurecli-interactive
    az ml computetarget delete -n instance -v
    ```

    詳細については、[az ml computetarget の computeinstance の削除](/cli/azure/ext/azure-cli-ml/ml/computetarget#ext-azure-cli-ml-az-ml-computetarget-delete)に関する記事をご覧ください。

# <a name="studio"></a>[スタジオ](#tab/azure-studio)

Azure Machine Learning Studio 内のご利用のワークスペースで、 **[コンピューティング]** を選択してから、上部にある **[コンピューティング インスタンス]** を選択します。

![コンピューティング インスタンスを管理する](./media/concept-compute-instance/manage-compute-instance.png)

次の操作を行うことができます。

* 新しいコンピューティング インスタンスの作成 
* コンピューティング インスタンス タブを更新する。
* コンピューティング インスタンスを開始、停止、再起動する。  インスタンスは、実行されるたびに支払いが発生します。 コンピューティング インスタンスを使用していないときは、コストを削減するために、コンピューティング インスタンスを停止します。 コンピューティング インスタンスを停止すると、そのインスタンスは解放されます。 その後、必要なときにもう一度開始します。
* コンピューティング インスタンスを削除する。
* コンピューティング インスタンスのリストをフィルター処理して、作成したもののみを表示する。

ワークスペース内で作成した (または作成してもらった) 各コンピューティング インスタンスに対しては、次のことを行うことができます。

* コンピューティング インスタンス上の Jupyter、JupyterLab、RStudio にアクセスする
* コンピューティング インスタンスに SSH 接続する。 SSH アクセスは既定で無効になっていますが、コンピューティング インスタンスの作成時に有効にすることができます。 SSH アクセスは、公開/秘密キーのメカニズムを通じて実行されます。 このタブには、SSH 接続の詳細が表示されます (IP アドレス、ユーザー名、ポート番号など)。
* 特定のコンピューティング インスタンスに関する詳細を取得する (IP アドレス、リージョンなど)。

---

[Azure RBAC](../role-based-access-control/overview.md) を使用すると、ワークスペース内のどのユーザーにコンピューティング インスタンスの作成、削除、開始、停止、再起動を許可するかを制御できます。 ワークスペースの共同作成者および所有者ロール内のユーザーはすべて、ワークスペース全体でコンピューティング インスタンスを作成、削除、開始、停止、および再起動することができます。 ただし、特定のコンピューティング インスタンスの作成者、またはその作成者に代わって作成された場合は割り当てられたユーザーのみが、そのコンピューティング インスタンス上の Jupyter、JupyterLab、および RStudio にアクセスすることが許可されます。 コンピューティング インスタンスは、ルート アクセス権を持つ 1 人のユーザー専用で、Jupyter/JupyterLab/RStudio を介してターミナルを使用できます。 コンピューティング インスタンスには、シングルユーザー サインインが用意され、すべてのアクションで、そのユーザーの ID が Azure RBAC と実験実行の属性で使用されます。 SSH アクセスは、公開/秘密キーのメカニズムを通じて制御されます。

Azure RBAC によって、次のアクションを制御できます。
* *Microsoft.MachineLearningServices/workspaces/computes/read*
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft.MachineLearningServices/workspaces/computes/delete*
* *Microsoft.MachineLearningServices/workspaces/computes/start/action*
* *Microsoft.MachineLearningServices/workspaces/computes/stop/action*
* *Microsoft.MachineLearningServices/workspaces/computes/restart/action*

## <a name="next-steps"></a>次のステップ

* [コンピューティング インスタンス ターミナルにアクセスする](how-to-access-terminal.md)
* [ファイルの作成と管理](how-to-manage-files.md)
* [トレーニングの実行を送信する](how-to-set-up-training-targets.md)
