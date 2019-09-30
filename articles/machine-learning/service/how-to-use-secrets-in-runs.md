---
title: トレーニングの実行でシークレットを使用する
titleSuffix: Azure Machine Learning
description: ワークスペース キー コンテナ―を使用して、セキュリティで保護された方法でトレーニングの実行にシークレットを渡します
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 08/23/2019
ms.custom: seodec18
ms.openlocfilehash: 4872ba8a707192cd61ec371fa982a076d410e918
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996568"
---
# <a name="use-secrets-in-training-runs"></a>トレーニングの実行でシークレットを使用する

この記事では、トレーニングの実行でシークレットを安全に使用する方法について説明します。 たとえば、外部データベースに接続してトレーニング データのクエリを実行するには、リモート実行コンテキストにユーザー名とパスワードを渡す必要があります。 このような値をクリア テキストでトレーニング スクリプトにコーディングすると、シークレットが公開されるので安全ではありません。 

代わりに、Azure Machine Learning ワークスペースには、関連付けられているリソースとして、[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) があります。 このキー コンテナーを使用して、Azure Machine Learning Python SDK 内の一連の API を通してシークレットをリモート実行に安全に渡すことができます。

シークレットを使用するための基本的なフローは次のとおりです。
 1. ローカル コンピューターで Azure にログインし、お使いのワークスペースに接続します。
 2. ローカル コンピューターで、ワークスペース キー コンテナーにシークレットを設定します。
 3. リモート実行を送信します。
 4. リモート実行の中で、キー値からシークレットを取得して使用します。

## <a name="set-secrets"></a>シークレットを設定する

Azure Machine Learning Python SDK では、[Keyvault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py) クラスにシークレットを設定するためのメソッドが含まれています。 ローカル Python セッションで、最初にワークスペース キー コンテナーへの参照を取得します。その後、[set_secret](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secret-name--value-) メソッドを使用して、名前と値でシークレットを設定します。

```python
from azureml.core import Workspace
import os

ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

シークレット値をクリアテキストとしてファイルに格納するのは安全ではないため、その値を Python コードには含めないでください。 代わりに、Azure DevOps のビルド シークレットなどの環境変数から、または対話型ユーザー入力からシークレット値を取得してください。

[list_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secret-name--value-) メソッドを使用して、シークレット名を一覧表示できます。 __set_secret__ メソッドでは、名前が既に存在する場合は、シークレット値が更新されます。

## <a name="get-secrets"></a>シークレットの取得

ローカル コードでは、[Keyvault.get_secret](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#get-secret-name-) メソッドを使用して、シークレット値を名前で取得できます。

[Experiment.submit](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py#submit-config--tags-none----kwargs-) を使用して送信される実行では、[Run.get_secret](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secret-name-) メソッドを使用します。 送信される実行ではワークスペースが認識されるため、このメソッドでは、ワークスペースのインスタンス化がショートカットされ、シークレット値が直接返されます。

```python
# Code in submitted run
from azureml.core import Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

書き込みや出力によってシークレット値が公開されることがないように注意してください。

set メソッドと get メソッドには、複数のシークレットに一度にアクセスするためのバッチ バージョンである [set_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secrets-secrets-batch-) と [get_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secrets-secrets-) もあります。

## <a name="next-steps"></a>次の手順

 * [ノートブックの例を表示する](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Azure Machine Learning のエンタープライズ セキュリティを確認する](concept-enterprise-security.md)
