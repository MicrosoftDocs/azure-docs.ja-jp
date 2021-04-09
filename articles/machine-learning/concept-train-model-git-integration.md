---
title: Azure Machine Learning との Git 統合
titleSuffix: Azure Machine Learning
description: Azure Machine Learning をローカルの Git リポジトリと統合して、トレーニング実行の一環として、リポジトリ、ブランチ、現在のコミット情報を追跡する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 11/16/2020
ms.openlocfilehash: cc844cbd2518bb131f6902d1da3e7653951224b5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102547851"
---
# <a name="git-integration-for-azure-machine-learning"></a>Azure Machine Learning との Git 統合

[Git](https://git-scm.com/) は、プロジェクトでの共有と共同作業を可能にする、一般的なバージョン管理システムです。 

Azure Machine Learning は、作業を追跡するために Git リポジトリを完全にサポートしています。したがって、自分の共有ワークスペース ファイル システムにリポジトリをクローンしたり、ローカル ワークステーションで Git を使用したり、CI/CD パイプラインから Git を使用したりすることができます。

ジョブを Azure Machine Learning に送信するとき、ソース ファイルがローカル git リポジトリに格納されていれば、リポジトリに関する情報がトレーニング プロセスの一部として追跡されます。

Azure Machine Learning は、ローカル git リポジトリにある情報を追跡するため、特定の中央リポジトリには関連付けられません。 リポジトリは、GitHub、GitLab、Bitbucket、Azure DevOps、または他の任意の git 互換サービスから複製できます。

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>Git リポジトリをワークスペース ファイル システムにクローンする
Azure Machine Learning では、ワークスペース内のすべてのユーザーに共有ファイル システムが提供されます。
このファイル共有に Git リポジトリをクローンするには、コンピューティング インスタンスを作成して、[ターミナルを開く](how-to-access-terminal.md)ことをお勧めします。
ターミナルを開くと、完全な Git クライアントにアクセスできるようになり、Git CLI エクスペリエンスを使用して Git のクローンや操作を行うことができます。

作業中のブランチで他のユーザーが直接衝突しないように、リポジトリを自分のユーザー ディレクトリにクローンすることをお勧めします。

認証可能なすべての Git リポジトリ (GitHub、Azure Repos、BitBucket など) をクローンできます。

複製の詳細については、[Git CLI の使用方法](https://guides.github.com/introduction/git-handbook/)に関するガイドを参照してください。

## <a name="authenticate-your-git-account-with-ssh"></a>SSH を使用して Git アカウントを認証する
### <a name="generate-a-new-ssh-key"></a>新しい SSH キーを生成する
1) Azure Machine Learning の [Notebook] タブで[ターミナル ウィンドウを開き](./how-to-access-terminal.md)ます。

2) 次のテキストを自分のメール アドレスに置き換えて貼り付けます。

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

これにより、指定したメール アドレスをラベルとして使う新しい SSH キーが作成されます。

```
> Generating public/private rsa key pair.
```

3) キーを保存するファイルを入力するように求められたら、Enter キーを押します。 これにより、ファイルの既定の場所が受け入れられます。

4) 既定の場所が "/home/azureuser/.ssh" であることを確認し、Enter キーを押します。 または、場所 "/home/azureuser/.ssh" を指定します。

> [!TIP]
> SSH キーが "/home/azureuser/.ssh" に保存されることを確認します。 このファイルは、コンピューティング インスタンスに保存されますが、コンピューティング インスタンスの所有者のみがアクセスできます。

```
> Enter a file in which to save the key (/home/azureuser/.ssh/id_rsa): [Press enter]
```

5) プロンプトが表示されたら、セキュアなパスフレーズを入力します。 セキュリティを強化するため、SSH キーにパスフレーズを追加することをお勧めします。

```
> Enter passphrase (empty for no passphrase): [Type a passphrase]
> Enter same passphrase again: [Type passphrase again]
```

### <a name="add-the-public-key-to-git-account"></a>Git アカウントに公開キーを追加する
1) ターミナル ウィンドウで、公開キー ファイルの内容をコピーします。 キーの名前を変更した場合は、id_rsa.pub を公開キーのファイル名に置き換えます。

```bash
cat ~/.ssh/id_rsa.pub
```
> [!TIP]
> **ターミナルでのコピーと貼り付け**
> * Windows: コピーの場合は `Ctrl-Insert`、貼り付けの場合は `Ctrl-Shift-v` または `Shift-Insert` を使用します。
> * Mac OS: コピーの場合は `Cmd-c`、貼り付けの場合は `Cmd-v` を使用します。
> * Firefox や IE ではクリップボードのアクセス許可が正しくサポートされない場合があります。

2) クリップボードのキー出力を選択してコピーします。

+ [GitHub](https://docs.github.com/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account)

+ [GitLab](https://docs.gitlab.com/ee/ssh/#adding-an-ssh-key-to-your-gitlab-account)

+ [Azure DevOps](/azure/devops/repos/git/use-ssh-keys-to-authenticate#step-2--add-the-public-key-to-azure-devops-servicestfs) **手順 2** から開始します。

+ [BitBucket](https://support.atlassian.com/bitbucket-cloud/docs/set-up-an-ssh-key/#SetupanSSHkey-ssh2)。 **手順 4** から開始します。

### <a name="clone-the-git-repository-with-ssh"></a>SSH を使用して Git リポジトリを複製する

1) SSH Git クローン URL を Git リポジトリからコピーします。

2) URL を以下の `git clone` コマンドに貼り付け、SSH Git リポジトリ URL を使用できるようにします。 これは、次のような見た目になります。

```bash
git clone git@example.com:GitUser/azureml-example.git
Cloning into 'azureml-example'...
```

次のような応答が表示されます。

```bash
The authenticity of host 'example.com (192.30.255.112)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,192.30.255.112' (RSA) to the list of known hosts.
```

SSH では、サーバーの SSH フィンガープリントが表示され、確認を求めるメッセージが表示される場合があります。 表示されているフィンガープリントが SSH 公開キー ページのいずれかのフィンガープリントに一致していることを確認する必要があります。

SSH は、不明なホストに接続するときにこのフィンガープリントを表示して、[中間者攻撃](/previous-versions/windows/it-pro/windows-2000-server/cc959354(v=technet.10))から保護します。 ホストのフィンガープリントを受け入れると、フィンガープリントが変更されない限り、SSH によって再度メッセージが表示されることはありません。

3) 接続を続行するかどうかを確認するメッセージが表示されたら、「`yes`」と入力します。 Git はリポジトリを複製し、以降の Git コマンドで SSH と接続するように元のリモートを設定します。

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

1. [Studio ポータル](https://ml.azure.com)で、ワークスペースを選択します。
1. __[実験]__ を選択し、ご自身のいずれかの実験を選択します。
1. __[RUN NUMBER]\(実行番号\)__ 列からいずれかの実行を選択します。
1. __[出力とログ]__ を選択し、 __[ログ]__ エントリと __[azureml]__ エントリを展開します。 __###\_azure__ で始まるリンクを選択します。

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

トレーニング実行を送信すると、[Run](/python/api/azureml-core/azureml.core.run%28class%29) オブジェクトが返されます。 このオブジェクトの `properties` 属性には、ログに記録された git 情報が含まれています。 たとえば、次のコードではコミット ハッシュを取得します。

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>CLI

CLI コマンド `az ml run` を使用して、実行からプロパティを取得できます。 たとえば、次のコマンドでは、`train-on-amlcompute` という名前の実験での、最後の実行のプロパティが返されます。

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

詳細については、[az ml run](/cli/azure/ext/azure-cli-ml/ml/run) のリファレンス ドキュメントを参照してください。

## <a name="next-steps"></a>次のステップ

* [モデル トレーニング用のコンピューティング ターゲットを使用する](how-to-set-up-training-targets.md)