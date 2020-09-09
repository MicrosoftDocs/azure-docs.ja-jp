---
title: Azure Machine Learning コンピューティング インスタンスとは
titleSuffix: Azure Machine Learning
description: フル マネージドのクラウドベース ワークステーションである Azure Machine Learning コンピューティング インスタンスについて説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 07/27/2020
ms.openlocfilehash: c72777bf2a4415a7f773f82a21a121f5e58f2ec0
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88651917"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Azure Machine Learning コンピューティング インスタンスとは

Azure Machine Learning コンピューティング インスタンスは、データ サイエンティスト向けのマネージド クラウドベース ワークステーションです。

コンピューティング インスタンスを使用すると、Azure Machine Learning の開発を簡単に開始できると共に、IT 管理者向けの管理機能とエンタープライズ対応機能が提供されます。  

コンピューティング インスタンスは、機械学習のためにクラウド内で完全に構成され管理される自分の開発環境として使用します。 また、開発やテストのためのトレーニングや推論のコンピューティング ターゲットとして使用することもできます。  

実稼働レベルのモデルのトレーニングの場合は、マルチノード スケーリング機能を備える [Azure Machine Learning コンピューティング クラスター](how-to-set-up-training-targets.md#amlcompute)を使用してください。 実稼働レベルのモデル デプロイの場合は [Azure Kubernetes Service クラスター](how-to-deploy-azure-kubernetes-service.md)を使用してください。

## <a name="why-use-a-compute-instance"></a>コンピューティング インスタンスを使用する理由

コンピューティング インスタンスは、自分の機械学習開発環境向けに最適化されるクラウドベースのフルマネージド ワークステーションです。 これには、次のようなメリットがあります。

|主な利点|説明|
|----|----|
|生産性|統合ノートブックや次のツールを Azure Machine Learning Studio で使用して、モデルを構築およびデプロイすることができます。<br/>-  Jupyter<br/>-  JupyterLab<br/>-  RStudio (プレビュー)<br/>コンピューティング インスタンスは Azure Machine Learning ワークスペースおよびスタジオと完全に統合されています。 ワークスペース内の他のデータ サイエンティストとノートブックやデータを共有できます。 [SSH](how-to-set-up-vs-code-remote.md) を使用して VS Code リモート開発をセットアップすることもできます |
|マネージドおよび安全|セキュリティ占有領域を削減し、エンタープライズ セキュリティ要件へのコンプライアンスを追加できます。 コンピューティング インスタンスでは、厳格な管理ポリシーと安全なネットワーク構成が提供されます。<br/><br/>- Resource Manager テンプレートまたは Azure Machine Learning SDK からの自動プロビジョニング。<br/>- [Azure ロールベースのアクセス制御 (Azure RBAC)](/azure/role-based-access-control/overview)<br/>- [仮想ネットワークのサポート](how-to-enable-virtual-network.md#compute-instance)<br/>- SSH アクセスを有効または無効にする SSH ポリシー<br/>TLS 1.2 対応 |
|ML &nbsp;用&nbsp;に事前構成済み|事前に構成された最新の ML パッケージ、ディープ ラーニング フレームワーク、GPU ドライバーを使用して、セットアップ タスクの時間を節約できます。|
|フル カスタマイズが可能|Azure VM の種類 (GPU を含む) や永続的な低レベル カスタマイズ (パッケージやドライバーのインストールなど) が広範にサポートされているので、高度なシナリオに簡単に対応できます。 |

## <a name="tools-and-environments"></a><a name="contents"></a>ツールと環境

> [!IMPORTANT]
> 以下に "(プレビュー)" と付記されているツールは、現在、パブリック プレビュー段階です。
> プレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Machine Learning コンピューティング インスタンスを使用すると、ワークスペース内の完全統合型ノートブックでモデルを作成し、トレーニングし、デプロイすることができます。

コンピューティング インスタンスには、次のツールと環境がインストールされています。 

|一般的なツールと環境|詳細|
|----|:----:|
|ドライバー|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Intel MPI ライブラリ||
|Azure CLI ||
|Azure Machine Learning サンプル ||
|Docker||
|Nginx||
|NCCL 2.0 ||
|Protobuf|| 

|**R** ツールと環境|詳細|
|----|:----:|
|RStudio Server Open Source Edition (プレビュー)||
|R カーネル||
|Azure Machine Learning SDK for R|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>SDK のサンプル|

|**PYTHON** ツールおよび環境|詳細|
|----|----|
|Anaconda Python||
|Jupyter と拡張機能||
|Jupyterlab と拡張機能||
[Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)</br>PyPI から|ほとんどの azureml 追加パッケージが含まれています。  完全な一覧を表示するには [コンピューティング インスタンスでターミナル ウィンドウを開き](how-to-run-jupyter-notebooks.md#terminal)、以下を実行します <br/> `conda list -n azureml_py36 azureml*` |
|その他の PyPI パッケージ|`jupytext`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Conda パッケージ|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|ディープ ラーニング パッケージ|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|ONNX パッケージ|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Azure Machine Learning の Python と R SDK のサンプル||

Python パッケージはすべて、**Python 3.6 - AzureML** 環境にインストールされます。  

### <a name="installing-packages"></a>パッケージのインストール

パッケージを Jupyter Notebook または RStudio に直接インストールできます。

* RStudio: 右下にある **[Packages]** タブ、または左上にある **[Console]** タブを使用します。  
* Python: インストール コードを追加して、Jupyter Notebook のセルで実行します。

または、次のいずれかの方法でターミナル ウィンドウにアクセスすることもできます。

* RStudio: 左上にある **[Terminal]** を選択します。
* Jupyter Lab: [Launcher] タブの見出し **[Other]** の下にある **[Terminal]** タイルを選択します。
* Jupyter: [Files] タブの右上にある **[New] > [Terminal]** を選択します。
* マシンに SSH 接続します。  次に、Python パッケージを **Python 3.6 - AzureML** 環境にインストールします。  R パッケージを **R** 環境にインストールします。

## <a name="accessing-files"></a>ファイルにアクセスする

ノートブックと R スクリプトは、Azure ファイル共有内のワークスペースの既定のストレージアカウントに格納されます。  これらのファイルは、"User files" ディレクトリの下に置かれます。 このストレージによって、コンピューティング インスタンス間でノートブックを簡単に共有できるようになります。 また、ストレージ アカウントでは、コンピューティング インスタンスを停止したり削除したりした場合でも、ノートブックが安全に維持されます。

ワークスペースの Azure ファイル共有アカウントは、コンピューティング インスタンスにドライブとしてマウントされます。 このドライブは、Jupyter、Jupyter Labs、および RStudio の既定の作業ディレクトリです。 つまり、Jupyter、JupyterLab、または RStudio で作成したノートブックとその他のファイルは、ファイル共有に自動的に格納され、他のコンピューティング インスタンスでも使用できるようになります。

ファイル共有内のファイルには、同じワークスペース内のすべてのコンピューティング インスタンスからアクセスできます。 コンピューティング インスタンスでこれらのファイルに加えた変更は、ファイル共有に確実に反映されます。

また、最新の Azure Machine Learning サンプルを、ワークスペース ファイル共有の user files ディレクトリの下にある自分のフォルダーに複製することもできます。

小さいファイルの書き込みをネットワーク ドライブに対して行うと、コンピューティング インスタンス自体のローカル ディスクに書き込む場合よりも遅くなる可能性があります。  小さなファイルを多数作成する場合は、コンピューティング インスタンス上に直接配置されているディレクトリ (`/tmp` ディレクトリなど) を使用するようにしてください。 なお、これらのファイルには、他のコンピューティング インスタンスからはアクセスできなくなることに注意してください。 

一時データにはコンピューティング インスタンスの `/tmp` ディレクトリを使用できます。  ただし、コンピューティング インスタンスの OS ディスクに大量のデータ ファイルを書き込むことは避けてください。  代わりに[データストア](concept-azure-machine-learning-architecture.md#datasets-and-datastores)を使用してください。 JupyterLab git 拡張機能をインストールしている場合も、コンピューティング インスタンスのパフォーマンスが低下する可能性があります。

## <a name="managing-a-compute-instance"></a>コンピューティング インスタンスの管理

Azure Machine Learning Studio 内のご利用のワークスペースで、 **[コンピューティング]** を選択してから、上部にある **[コンピューティング インスタンス]** を選択します。

![コンピューティング インスタンスを管理する](./media/concept-compute-instance/manage-compute-instance.png)

次の操作を行うことができます。

* [コンピューティング インスタンスを作成する](#create)。 
* コンピューティング インスタンス タブを更新する。
* コンピューティング インスタンスを開始、停止、再起動する。  インスタンスは、実行されるたびに支払いが発生します。 コンピューティング インスタンスを使用していないときは、コストを削減するために、コンピューティング インスタンスを停止します。 コンピューティング インスタンスを停止すると、そのインスタンスは解放されます。 その後、必要なときにもう一度開始します。 
* コンピューティング インスタンスを削除する。
* コンピューティング インスタンスのリストに自分が作成したインスタンスが表示されるようにフィルター処理する。  これらのインスタンスは、自分がアクセスできるコンピューティング インスタンスです。

アクセス可能なワークスペース内のコンピューティング インスタンスごとに、次のことができます。

* コンピューティング インスタンス上の Jupyter、JupyterLab、RStudio にアクセスする
* コンピューティング インスタンスに SSH 接続する。 SSH アクセスは既定で無効になっていますが、コンピューティング インスタンスの作成時に有効にすることができます。 SSH アクセスは、公開/秘密キーのメカニズムを通じて実行されます。 このタブには、SSH 接続の詳細が表示されます (IP アドレス、ユーザー名、ポート番号など)。
* 特定のコンピューティング インスタンスに関する詳細を取得する (IP アドレス、リージョンなど)。

[RBAC](/azure/role-based-access-control/overview) を使用すると、ワークスペース内のどのユーザーにコンピューティング インスタンスの作成、削除、開始、停止、再起動を許可するかを制御できます。 ワークスペースの共同作成者および所有者ロール内のユーザーはすべて、ワークスペース全体でコンピューティング インスタンスを作成、削除、開始、停止、および再起動することができます。 ただし、特定のコンピューティング インスタンス上の Jupyter、JupyterLab、および RStudio へのアクセスが許可されるのは、そのコンピューティング インスタンスの作成者だけです。 コンピューティング インスタンスの作成者は、専用のコンピューティング インスタンスを持ち、ルート アクセス権を有し、Jupyter/JupyterLab/RStudio を介してターミナルを使用できます。 コンピューティング インスタンスには、作成者ユーザーのシングルユーザー ログインが用意され、すべてのアクションで、実験実行の属性および RBAC にそのユーザー ID が使用されます。 SSH アクセスは、公開/秘密キーのメカニズムを通じて制御されます。

次のアクションは RBAC で制御できます。
* *Microsoft.MachineLearningServices/workspaces/computes/read*
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft.MachineLearningServices/workspaces/computes/delete*
* *Microsoft.MachineLearningServices/workspaces/computes/start/action*
* *Microsoft.MachineLearningServices/workspaces/computes/stop/action*
* *Microsoft.MachineLearningServices/workspaces/computes/restart/action*

### <a name="create-a-compute-instance"></a><a name="create"></a>コンピューティング インスタンスを作成する

Azure Machine Learning Studio のワークスペースで、いずれかのノートブックを実行する準備ができたら、 **[計算]** セクションまたは **[ノートブック]** セクションから新しいコンピューティング インスタンスを作成します。

:::image type="content" source="media/concept-compute-instance/create-compute-instance.png" alt-text="新しいコンピューティング インスタンスの作成":::


|フィールド  |説明  |
|---------|---------|
|コンピューティング名     |  <li>名前は必須であり、3 文字から 24 文字の長さにする必要があります。</li><li>有効な文字は、大文字、小文字、数字、 **-** 文字です。</li><li>名前の先頭は文字である必要があります</li><li>名前は、Azure リージョン内のすべての既存のコンピューティングで一意である必要があります。 選択した名前が一意でない場合は、アラートが表示されます</li><li>名前に **-** 文字が使用されている場合は、その後に少なくとも 1 文字を続ける必要があります</li>     |
|仮想マシンのタイプ |  CPU または GPU を選択します。 このタイプは作成後に変更することはできません     |
|仮想マシンのサイズ     |  サポートされる仮想マシンのサイズがお客様のリージョンで制限されている可能性があります。 [利用可能な製品のリスト](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)に関するページを確認してください     |
|SSH アクセスの有効化/無効化     |   SSH アクセスは既定では無効になっています。  SSH アクセスは、 作成後に変更することはできません。 [VS Code Remote](how-to-set-up-vs-code-remote.md) を使用して対話形式でデバッグする場合は、アクセスを有効にする必要があります   |
|詳細設定     |  省略可能。 仮想ネットワークを構成する。 **[リソース グループ]** 、 **[仮想ネットワーク]** 、および **[サブネット]** を指定して、Azure Virtual Network (vnet) 内にコンピューティング インスタンスを作成します。 詳細については、vnet でのこれらの[ネットワーク要件](how-to-enable-virtual-network.md#compute-instance)に関するページを参照してください。        |

インスタンスを作成することもできます
* [統合ノートブックのエクスペリエンス](tutorial-1st-experiment-sdk-setup.md#azure)から直接
* Azure Portal
* Azure Resource Manager テンプレートから。 テンプレートの例については、[Azure Machine Learning コンピューティング インスタンス テンプレートの作成](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance)に関する記事を参照してください。
* Azure Machine Learning SDK を使用して
* [Azure Machine Learning 用の CLI 拡張機能](reference-azure-machine-learning-cli.md#computeinstance)から

コンピューティング インスタンスの作成に適用される、VM ファミリのクォータごとのリージョンごとの専用コア数とリージョンの合計クォータ は、Azure Machine Learning のトレーニング コンピューティング クラスターのクォータと統合され、共有されます。 コンピューティング インスタンスを停止しても、コンピューティング インスタンスを再起動できるように、クォータは解放されません。

## <a name="compute-target"></a>コンピューティング ターゲット

コンピューティング インスタンスは、Azure Machine Learning コンピューティング クラスターのように、[トレーニング コンピューティング ターゲット](concept-compute-target.md#train)として使用できます。 

コンピューティング インスタンスは、
* ジョブ キューがあります。
* 企業で SSH ポートを開かなくても、仮想ネットワーク環境でジョブを安全に実行できます。 ジョブはコンテナー化された環境で実行され、モデルの依存関係が Docker コンテナーにパッケージ化されます。
* 複数の小さいジョブを並列で実行できます (プレビュー)。  コアごとに 2 つのジョブを並列で実行できますが、残りのジョブはキューに登録されます。
* シングルノード マルチ GPU 分散トレーニング ジョブをサポートする

コンピューティング インスタンスは、テストまたはデバッグのシナリオのためのローカル推論配置ターゲットとして使用できます。

## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>Notebook VM はどうなったのか

Notebook VM は、コンピューティング インスタンスに置き換えられつつあります。  

ワークスペースのファイル共有に格納されているノートブック ファイルと、ワークスペースのデータストア内にあるデータには、コンピューティング インスタンスからアクセスできます。 ただし、以前に Notebook VM にインストールしたカスタム パッケージは、コンピューティング インスタンスに再インストールする必要があります。 コンピューティング クラスターの作成に適用されるクォータ制限は、コンピューティング インスタンスの作成にも適用されます。

新しい Notebook VM を作成できません。 ただし、既に作成した Notebook VM には引き続きアクセスでき、フル機能で使用することができます。 コンピューティング インスタンスは、既存の Notebook VM と同じワークスペース内に作成できます。


## <a name="next-steps"></a>次のステップ

 * [チュートリアル:最初の ML モデルをトレーニングする)](tutorial-1st-experiment-sdk-train.md)」では、統合ノートブックでコンピューティング インスタンスを使用する方法を示しています。
