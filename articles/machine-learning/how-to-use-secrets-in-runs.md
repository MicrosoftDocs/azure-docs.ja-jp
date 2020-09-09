---
title: トレーニングの認証シークレット
titleSuffix: Azure Machine Learning
description: ワークスペース キー コンテナ―を使用して、セキュリティで保護された方法でトレーニングの実行にシークレットを渡します
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 03/09/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: e131992f9c70feba9553b8ac278048e6e5e10061
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87319407"
---
# <a name="use-authentication-credential-secrets-in-azure-machine-learning-training-runs"></a>Azure Machine Learning トレーニングの実行で認証資格情報シークレットを使用する
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、トレーニングの実行でシークレットを安全に使用する方法について説明します。 ユーザー名やパスワードなどの認証情報はシークレットです。 たとえば、トレーニング データを問い合わせる目的で外部データベースに接続する場合、リモート実行コンテキストにユーザー名とパスワードを渡す必要があります。 このような値をクリアテキストでトレーニング スクリプトにコーディングすると、シークレットが公開されるので安全ではありません。 

代わりに、Azure Machine Learning ワークスペースには、[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) という名前のリソースが関連付けられています。 この Key Vault を使用して、Azure Machine Learning Python SDK の一連の API を介してシークレットをリモート実行に安全に渡します。

シークレットを使用するための基本的なフローは次のとおりです。
 1. ローカル コンピューターで Azure にログインし、お使いのワークスペースに接続します。
 2. ローカル コンピューターで、ワークスペース キー コンテナーにシークレットを設定します。
 3. リモート実行を送信します。
 4. リモート実行の中で、Key Vault からシークレットを取得して使用します。

## <a name="set-secrets"></a>シークレットを設定する

Azure Machine Learning では、[Keyvault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py) クラスにシークレットを設定するためのメソッドが含まれています。 ローカル Python セッションで、最初にワークスペース Key Vault への参照を取得します。その後、[`set_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secret-name--value-) メソッドを使用して、名前と値でシークレットを設定します。 __set_secret__ メソッドでは、名前が既に存在する場合は、シークレット値が更新されます。

```python
from azureml.core import Workspace
from azureml.core import Keyvault
import os


ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

シークレット値をクリアテキストとしてファイルに格納するのは安全ではないため、その値を Python コードには含めないでください。 代わりに、Azure DevOps のビルド シークレットなどの環境変数から、または対話型ユーザー入力からシークレット値を取得してください。

[`list_secrets()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#list-secrets--) メソッドを利用してシークレット名を一覧表示できます。バッチ バージョンの [set_secrets()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secrets-secrets-batch-) もありますが、これを利用すると、複数シークレットを一度に設定できます。

## <a name="get-secrets"></a>シークレットの取得

ローカル コードでは、[`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#get-secret-name-) メソッドを使用して、名前でシークレット値を取得できます。

[`Experiment.submit`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py#submit-config--tags-none----kwargs-) が送信される実行の場合、[`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) クラスを使用して [`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secret-name-) メソッドを使用します。 送信される実行ではワークスペースが認識されるため、このメソッドでは、ワークスペースのインスタンス化がショートカットされ、シークレット値が直接返されます。

```python
# Code in submitted run
from azureml.core import Experiment, Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

書き込みや出力によってシークレット値が公開されることがないように注意してください。

複数のシークレットに一度にアクセスするための [get_secrets()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secrets-secrets-) というバッチ バージョンもあります。

## <a name="next-steps"></a>次のステップ

 * [ノートブックの例を表示する](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Azure Machine Learning のエンタープライズ セキュリティを確認する](concept-enterprise-security.md)
