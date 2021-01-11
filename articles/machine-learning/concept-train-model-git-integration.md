---
title: Azure Machine Learning との Git 統合
titleSuffix: Azure Machine Learning
description: Azure Machine Learning をローカル Git リポジトリと統合する方法について説明します。 Git リポジトリであるローカル ディレクトリからトレーニング実行を送信した場合、リポジトリ、ブランチ、および現在のコミットに関する情報が、実行の一部として追跡されます。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.openlocfilehash: 154d28e4df9bad68f3c5e93208ccf62ba2721663
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2020
ms.locfileid: "89144823"
---
# <a name="git-integration-for-azure-machine-learning"></a>Azure Machine Learning との Git 統合

[Git](https://git-scm.com/) は、プロジェクトでの共有と共同作業を可能にする、一般的なバージョン管理システムです。 

Azure Machine Learning は、作業を追跡するために Git リポジトリを完全にサポートしています。したがって、自分の共有ワークスペース ファイル システムにリポジトリをクローンしたり、ローカル ワークステーションで Git を使用したり、CI/CD パイプラインから Git を使用したりすることができます。

ジョブを Azure Machine Learning に送信するとき、ソース ファイルがローカル git リポジトリに格納されていれば、リポジトリに関する情報がトレーニング プロセスの一部として追跡されます。

Azure Machine Learning は、ローカル git リポジトリにある情報を追跡するため、特定の中央リポジトリには関連付けられません。 リポジトリは、GitHub、GitLab、Bitbucket、Azure DevOps、または他の任意の git 互換サービスから複製できます。

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>Git リポジトリをワークスペース ファイル システムにクローンする
Azure Machine Learning では、ワークスペース内のすべてのユーザーに共有ファイル システムが提供されます。
このファイル共有に Git リポジトリをクローンするには、コンピューティング インスタンスを作成して、ターミナルを開くことをお勧めします。
ターミナルを開くと、完全な Git クライアントにアクセスできるようになり、Git CLI エクスペリエンスを使用して Git のクローンや操作を行うことができます。

作業中のブランチで他のユーザーが直接衝突しないように、リポジトリを自分のユーザー ディレクトリにクローンすることをお勧めします。

認証可能なすべての Git リポジトリ (GitHub、Azure Repos、BitBucket など) をクローンできます。

Git CLI の使用方法のガイドについては、[こちら](https://guides.github.com/introduction/git-handbook/)を参照してください。

## <a name="track-code-that-comes-from-git-repositories"></a>Git リポジトリから取得したコードを追跡する

Python SDK または Machine Learning CLI からトレーニング実行を送信すると、モデルのトレーニングに必要なファイルがワークスペースにアップロードされます。 ご利用の開発環境で `git` コマンドが使用可能な場合、アップロード プロセスでは、ファイルが git リポジトリに格納されているかどうかを確認するためにそのコマンドが使用されます。 その場合、git リポジトリからの情報もトレーニング実行の一部としてアップロードされます。 この情報は、トレーニング実行の次のプロパティに格納されます。

| プロパティ | 値の取得に使用される Git コマンド | 説明 |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | リポジトリの複製元の URI。 |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | リポジトリの複製元の URI。 |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | 実行が送信されたときのアクティブなブランチ。 |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | 実行が送信されたときのアクティブなブランチ。 |
| `azureml.git.commit` | `git rev-parse HEAD` | 実行するために送信されたコードのコミット ハッシュ。 |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | 実行するために送信されたコードのコミット ハッシュ。 |
| `azureml.git.dirty` | `git status --porcelain .` | ブランチ/コミットがダーティである場合は `True`、そうでない場合は `false`。 |

この情報は、エスティメーター、機械学習パイプライン、またはスクリプト実行を使用する実行のために送信されます。

トレーニング ファイルが開発環境の git リポジトリに存在しない場合、または `git` コマンドが使用できない場合、git 関連の情報は追跡されません。

> [!TIP]
> 開発環境で git コマンドが使用可能かどうかを確認するには、シェル セッション、コマンド プロンプト、PowerShell、またはその他のコマンド ライン インターフェイスを開き、次のコマンドを入力します。
>
> ```
> git --version
> ```
>
> インストールされ、パスに指定されている場合は、`git version 2.4.1` のような応答が返されます。 開発環境に git をインストールする方法の詳細については、[Git Web サイト](https://git-scm.com/)を参照してください。

## <a name="view-the-logged-information"></a>ログに記録された情報を表示する

Git 情報は、トレーニング実行のプロパティに格納されます。 この情報は、Azure portal、Python SDK、CLI を使用して表示できます。 

### <a name="azure-portal"></a>Azure portal

1. [Azure portal](https://portal.azure.com) でご自身のワークスペースを選択します。
1. __[実験]__ を選択し、ご自身のいずれかの実験を選択します。
1. __[RUN NUMBER]\(実行番号\)__ 列からいずれかの実行を選択します。
1. __[ログ]__ を選択し、 __[ログ]__ エントリと __[azureml]__ エントリを展開します。 __###\_azure__ で始まるリンクを選択します。

    ![ポータルでの ###_azure エントリ](./media/concept-train-model-git-integration/azure-machine-learning-logs.png)

次の JSON のようなテキストを含む情報が記録されています。

```json
"properties": {
    "_azureml.ComputeTargetType": "batchai",
    "ContentSnapshotId": "5ca66406-cbac-4d7d-bc95-f5a51dd3e57e",
    "azureml.git.repository_uri": "git@github.com:azure/machinelearningnotebooks",
    "mlflow.source.git.repoURL": "git@github.com:azure/machinelearningnotebooks",
    "azureml.git.branch": "master",
    "mlflow.source.git.branch": "master",
    "azureml.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "mlflow.source.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "azureml.git.dirty": "True",
    "AzureML.DerivedImageName": "azureml/azureml_9d3568242c6bfef9631879915768deaf",
    "ProcessInfoFile": "azureml-logs/process_info.json",
    "ProcessStatusFile": "azureml-logs/process_status.json"
}
```

### <a name="python-sdk"></a>Python SDK

トレーニング実行を送信すると、[Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) オブジェクトが返されます。 このオブジェクトの `properties` 属性には、ログに記録された git 情報が含まれています。 たとえば、次のコードではコミット ハッシュを取得します。

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>CLI

CLI コマンド `az ml run` を使用して、実行からプロパティを取得できます。 たとえば、次のコマンドでは、`train-on-amlcompute` という名前の実験での、最後の実行のプロパティが返されます。

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

詳細については、[az ml run](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest) のリファレンス ドキュメントを参照してください。

## <a name="next-steps"></a>次のステップ

* [モデル トレーニング用のコンピューティング ターゲットを使用する](how-to-set-up-training-targets.md)
