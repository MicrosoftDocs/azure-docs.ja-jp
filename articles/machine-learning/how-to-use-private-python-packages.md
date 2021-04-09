---
title: 非公開の Python パッケージを使用する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 環境から非公開の Python パッケージで安全に作業する方法について説明します。
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: laobri
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/10/2020
ms.openlocfilehash: 5cc1025528959f8c81aa80703e3fc26d59c5266f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102618102"
---
# <a name="use-private-python-packages-with-azure-machine-learning"></a>Azure Machine Learning で非公開の Python パッケージを使用する


この記事では、Azure Machine Learning 内で非公開の Python パッケージを安全に使用する方法について説明します。 非公開の Python パッケージのユース ケースは次のとおりです。

 * パブリックに共有したくない非公開パッケージを開発した場合。
 * エンタープライズ ファイアウォール内に格納されているキュレーションされたパッケージのリポジトリを使用する場合。

推奨される方法は、1 つの Azure Machine Learning ワークスペースのパッケージが少数ある場合と、組織内のすべてのワークスペースのパッケージのリポジトリ全体がある場合で異なります。

非公開パッケージは、[Environment](/python/api/azureml-core/azureml.core.environment.environment) クラスを通じて使用されます。 環境内では、使用する Python パッケージ (非公開パッケージを含む) を宣言します。 Azure Machine Learning 全般における環境の詳細については、[環境の使用方法](how-to-use-environments.md)に関する記事を参照してください。 

## <a name="prerequisites"></a>前提条件

 * [Azure Machine Learning SDK for Python](/python/api/overview/azure/ml/install)
 * [Azure Machine Learning ワークスペース](how-to-manage-workspace.md)

## <a name="use-small-number-of-packages-for-development-and-testing"></a>開発とテストに少数のパッケージを使用する

1 つのワークスペースの非公開パッケージの数が少ない場合は、静的な [`Environment.add_private_pip_wheel()`](/python/api/azureml-core/azureml.core.environment.environment#add-private-pip-wheel-workspace--file-path--exist-ok-false-) メソッドを使用します。 この方法により、非公開パッケージをワークスペースにすばやく追加することができます。これは、開発とテスト目的に適しています。

ファイル パス引数をローカルの wheel ファイルにポイントし、```add_private_pip_wheel``` コマンドを実行します。 このコマンドによって、ワークスペース内のパッケージの場所を追跡するために使用される URL が返されます。 ストレージ URL をキャプチャし、`add_pip_package()` メソッドに渡します。

```python
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

内部的には、Azure Machine Learning service によって URL がセキュリティで保護された SAS URL で置き換えられます。これにより、wheel ファイルは非公開で安全なまま維持されます。

## <a name="use-a-repository-of-packages-from-azure-devops-feed"></a>Azure DevOps フィードからパッケージのリポジトリを使用する

機械学習アプリケーションの Python パッケージを積極的に開発している場合は、それらを Azure DevOps リポジトリで成果物としてホストし、フィードとして公開することができます。 この方法により、パッケージをビルドするための DevOps ワークフローを Azure Machine Learning ワークスペースに統合することができます。 Azure DevOps を使用して Python フィードを設定する方法については、[Azure Artifacts で Python パッケージの使用を開始する](/azure/devops/artifacts/quickstarts/python-packages)方法に関する記事をお読みください。

この方法では、個人用アクセス トークンを使用してリポジトリに対する認証を行います。 プライベート GitHub リポジトリなど、トークン ベースの認証を使用する他のリポジトリにも同じアプローチが適用できます。 

 1. Azure DevOps インスタンスの[個人用アクセス トークン (PAT) を作成します](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?tabs=preview-page#create-a-pat)。 トークンのスコープを __[Packaging]\(パッケージ\) > [Read]\(読み取り\)__ に設定します。 

 2. [Workspace.set_connection](/python/api/azureml-core/azureml.core.workspace.workspace#set-connection-name--category--target--authtype--value-) メソッドを使用して、Azure DevOps URL と PAT をワークスペースのプロパティとして追加します。

     ```python
    from azureml.core import Workspace
    
    pat_token = input("Enter secret token")
    ws = Workspace.from_config()
    ws.set_connection(name="connection-1", 
        category = "PythonFeed",
        target = "https://<my-org>.pkgs.visualstudio.com", 
        authType = "PAT", 
        value = pat_token) 
     ```

 3. Azure Machine Learning 環境を作成し、フィードから Python パッケージを追加します。
    
    ```python
    from azureml.core import Environment
    from azureml.core.conda_dependencies import CondaDependencies
    
    env = Environment(name="my-env")
    cd = CondaDependencies()
    cd.add_pip_package("<my-package>")
    cd.set_pip_option("--extra-index-url https://<my-org>.pkgs.visualstudio.com/<my-project>/_packaging/<my-feed>/pypi/simple")
    env.python.conda_dependencies=cd
    ```

これで、環境をトレーニングの実行や、Web サービス エンドポイントのデプロイで使用する準備ができました。 環境をビルドするとき、Azure Machine Learning service では PAT を使用して、一致するベース URL を持つフィードに対して認証を行います。

## <a name="use-a-repository-of-packages-from-private-storage"></a>プライベート ストレージからパッケージのリポジトリを使用する

組織のファイアウォール内の Azure ストレージ アカウントからパッケージを使用できます。 このストレージ アカウントでは、キュレーションされたパッケージのセットや、一般に公開されているパッケージの内部ミラーを保持することができます。

このようなプライベート ストレージを設定するには、[Azure Machine Learning ワークスペースおよび関連付けられているリソースのセキュリティ保護](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints)に関する記事を参照してください。 また、[Azure Container Registry (ACR) を VNet の背後に配置する](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr)必要があります。

> [!IMPORTANT]
> プライベート パッケージ リポジトリを使用してモデルをトレーニングまたはデプロイできるようにするには、この手順を完了する必要があります。

これらの構成を完了すると、完全な URL を使用して、Azure Blob Storage で Azure Machine Learning 環境定義内のパッケージを参照できます。

## <a name="next-steps"></a>次のステップ

 * [Azure Machine Learning のエンタープライズ セキュリティ](concept-enterprise-security.md)の詳細情報