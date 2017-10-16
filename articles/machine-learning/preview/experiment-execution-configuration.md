---
title: "Azure Machine Learning の実験実行サービスの概要"
description: "このドキュメントでは、Azure Machine Learning の実験実行サービスについての概要を説明します"
services: machine-learning
author: gokhanuluderya-msft
ms.author: gokhanu
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/17/2017
ms.openlocfilehash: bb1c7d318939c42edb9a51e28dec31593f2485f9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-azure-machine-learning-experiment-execution-service"></a>Azure Machine Learning の実験実行サービスの概要
Azure ML (Azure Machine Learning) の実験実行サービスでは、データ サイエンティストは Azure ML の実行機能と実行管理機能を使用して実験を行うことができます。 これにより、すばやく反復する俊敏な実験のためのフレームワークが提供されます。 Azure ML Workbench では、ご使用のマシンでのローカルな実行から開始でき、GPU を備えたリモートの Data Science VM や Spark が実行されている HDInsight クラスター など、他の環境にスケール アップおよびスケール アウトするための簡単な手段が提供されます。

実験実行サービスは、再現可能な、整合性のある、独立した方法で実験を実行できるように構築されています。 コンピューティング ターゲット、実行環境、実行構成を管理するのに役立ちます。 Azure ML Workbench の実行機能と実行管理機能を使用すると、さまざまな環境の間を簡単に移動できます。 

Python または PySpark スクリプトを Azure ML Workbench プロジェクトでローカルに、またはクラウドで大規模に実行できます。 

次の環境でスクリプトを実行できます。 

* Azure ML Workbench によりインストールされたローカル コンピューター上の Python (3.5.2) 環境。
* ローカル コンピューター上の Docker コンテナー内の Conda Python 環境
* リモート Linux マシン上の Docker コンテナー内の Conda Python 環境。 [Azure 上の Ubuntu ベースの DSVM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) などです。
* Azure 上の [HDInsight for Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/)

>[!IMPORTANT]
>Azure ML の実行サービスでは現在、Python と Spark のランタイム バージョンとしてそれぞれ Python 3.5.2 と Spark 2.1.11 がサポートされています。 


## <a name="key-concepts-in-azure-ml-experiment-execution"></a>Azure ML 実験実行の主要概念
Azure ML 実験実行では、次の概念を理解しておく必要があります。 後続のセクションで、これらの概念の利用方法について詳しく説明します。 
### <a name="compute-target"></a>コンピューティング ターゲット
コンピューティング ターゲットは、ユーザーのデスクトップ、VM 上のリモート Docker、クラスターなど、ユーザーのプログラムを実行する場所を指定します。 コンピューティング ターゲットは、ユーザーがアドレスおよびアクセス可能な場所である必要があります。 Azure ML Workbench では、コンピューティング ターゲットを作成したり、Workbench アプリケーションと CLI を使用したりして、それらのターゲットを管理できます。 

CLI で_az ml computetarget attach_ コマンドを実行すると、実行で使用可能なコンピューティング ターゲットを作成できます。

### <a name="supported-compute-targets-are"></a>サポートされているコンピューティング ターゲットを次に示します。
* Azure ML Workbench によりインストールされたコンピューター上のローカルの Python (3.5.2) 環境。
* コンピューター上のローカル Docker
* Linux-Ubuntu VM 上のリモート Docker。 [Azure 上の Ubuntu ベースの DSVM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) などです。
* Azure 上の [HDInsight for Spark クラスター](https://azure.microsoft.com/services/hdinsight/apache-spark/)

Azure ML の実行サービスでは現在、Python と Spark のランタイム バージョンとしてそれぞれ Python 3.5.2 と Spark 2.1.11 がサポートされています。 

>[!IMPORTANT]
> Docker が実行されている Windows VM は、リモートのコンピューティング ターゲットとしてサポート**されていません**。

### <a name="execution-environment"></a>実行環境
実行環境は、Azure ML Workbench でプログラムを実行するために必要なランタイム構成と依存関係を定義します。

Azure ML Workbench の既定のランタイム上で実行されている場合、ユーザーはお好みのツールやパッケージ マネージャーを使用してローカルの実行環境を管理します。 

Conda は、ローカル Docker とリモート Docker の実行だけでなく、HDInsigh ベースの実行を管理するのにも使用されます。 これらのコンピューティング ターゲットでは、実行環境の構成は、**Conda_dependencies.yml ファイル**と **Spark_dependencies.yml ファイル**によって管理されます。 これらのファイルは、プロジェクト内の **aml_config** フォルダーにあります。

**実行環境でサポートされているランタイムは、次のとおりです。**
* Python 3.5.2
* Spark 2.1.11

### <a name="run-configuration"></a>実行構成
Azure ML では、コンピューティング ターゲットと実行環境だけでなく、実行構成を定義および変更するためのフレームワークが用意されています。 さまざまな実験を行うなかで、繰り返し行う実験の一部で異なる構成が必要になる場合があります。 異なるパラメーター範囲やデータ ソースを使用したり、Spark パラメーターを調整したりできます。 Azure ML 実行サービスでは、実行構成を管理するためのフレームワークが用意されています。

_az ml computetarget attach_ コマンドの実行により、ご使用のプロジェクト内の **aml_config** フォルダーに .compute と .runconfig という 2 つのファイルが作成されます。これらのファイルは、_<your_computetarget_name>.compute_ と _<your_computetarget_name>.runconfig_ という命名規則に従っています。 コンピューティング ターゲットの作成時には、利便性のために .runconfig ファイルが自動作成されます。 CLI で _az ml runconfigurations_ コマンドを使用して、他の実行構成を作成し、管理できます。 ファイル システム上で作成し、編集することもできます。

Azure ML Workbench での実行構成では、環境変数を指定することもできます。 ご使用の .runconfig ファイルに次のセクションを追加すると、環境変数を指定してそれをコードで使用できます。 

```
EnvironmentVariables:
"EXAMPLE_ENV_VAR1": "Example Value1"
"EXAMPLE_ENV_VAR2": "Example Value2"
```

これらの環境変数には、コードでアクセスできます。 たとえば、この Phyton コード スニペットでは、"EXAMPLE_ENV_VAR1" という環境変数が出力されます
```
print(os.environ.get("EXAMPLE_ENV_VAR1"))
```

_**次の図は、最初の実験実行のおおまかなフローを示しています。**_
![](media/experiment-execution-configuration/experiment-execution-flow.png)

## <a name="azure-ml-experiment-execution-scenarios"></a>Azure ML の実験実行のシナリオ
このセクションでは、実行シナリオを掘り下げ、Azure ML で実験がどのように行われるかについて説明します。具体的には、ローカルでの実験、リモート VM での実験、HDInsight クラスターでの実験の実行について説明します。 このセクションでは、コンピューティング ターゲットの作成から実験の実行までを段階的に説明します。

>[!NOTE]
>この記事の残りの部分では、CLI (コマンド ライン インターフェイス) コマンドを使用して概念と機能を示します。 ここで説明する機能は、Workbench デスクトップ アプリケーションから使用することもできます。

## <a name="launching-the-cli"></a>CLI の起動
CLI を簡単に起動するには、Azure ML Workbench デスクトップ アプリケーションでプロジェクトを開き、**[ファイル]、[コマンド プロンプトを開く]** の順に選択します。

![](media/experiment-execution-configuration/opening-cli.png)

このコマンドを実行すると、ターミナル ウィンドウが起動されます。ここで、現在のプロジェクト フォルダー内のスクリプトを実行するコマンドを入力できます。 このターミナル ウィンドウは、Workbench によってインストールされる Python 3.5.2 環境で構成されます。

>[!NOTE]
> コマンド ウィンドウから _az ml_ コマンドを実行する場合、ユーザーが Azure に対して認証されている必要があります。 CLI では、独立した認証キャッシュを使用した後にデスクトップ アプリを使用するため、Workbench デスクトップ アプリにログインできても、ご使用の CLI 環境で認証されているとは限りません。 認証するには、次の手順に従ってください。 認証トークンは一定期間ローカルにキャッシュされるため、これらの手順を繰り返す必要があるのは、トークンの有効期限が切れた場合のみです。 トークンの有効期限が切れたか、認証エラーが発生した場合は、次のコマンドを実行します。

```
# to authenticate 
$ az login

# to list subscriptions
$ az account list -o table

# to set current subscription to a particular subscription ID 
$ az account set -s <subscription_id>

# to verify your current Azure subscription
$ az account show
```

>[!NOTE] 
>プロジェクト フォルダー内で _az ml_ コマンドを実行する場合、そのプロジェクトが_現在の_ Azure サブスクリプションの Azure ML 実験アカウントに属していることを確認してください。 属していないと、実行エラーが発生する場合があります。


## <a name="running-scripts-and-experiments"></a>スクリプトと実験の実行
Azure ML Workbench では、_az ml experiment submit_ コマンドを使用して、さまざまなコンピューティング ターゲットに対して Python スクリプトと PySpark スクリプトを実行できます。 このコマンドでは、実行構成定義が必要となります。 

ユーザーがコンピューティング ターゲットを作成すると、Azure ML Workbench で対応する .runconfig ファイルが作成されますが、ユーザーは _az ml runconfiguration  create_ コマンドを使用してさらに実行構成を作成することができます。 実行構成ファイルを手動で編集することもできます。

実行構成は、Workbench アプリケーションの実験実行エクスペリエンスの一部として表示されます。 

>[!NOTE]
>実行構成ファイルについて詳しくは、[実験実行構成リファレンス](experiment-execution-configuration-reference.md)に関するセクションをご覧ください。

## <a name="running-a-script-locally-on-azure-ml-workbench-installed-runtime"></a>Azure ML Workbench でインストールされたランタイムでのスクリプトのローカル実行
Azure ML Workbench では、Azure ML Workbench でインストールされた Python 3.5.2 ランタイムに対してスクリプトを直接実行できます。 この既定のランタイムは、Azure ML Workbench のセットアップ時にインストールされ、Azure ML のライブラリと依存関係が含まれています。 ローカル実行の実行結果とアーティファクトは、クラウド上の実行履歴サービスに引き続き保存されます。

Docker ベースの実行とは異なり、この構成は Conda によって管理_されません_。 ローカルの Azure ML Workbench Python 環境のパッケージ依存関係は、手動でプロビジョニングする必要があります。

Workbench でインストールされた Python 環境でスクリプトをローカルに実行するには、次のコマンドを実行します。 

```
$az ml experiment submit -c local myscript.py
```

Azure ML Workbench CLI ウィンドウで次のコマンドを入力すると、既定の Python 環境へのパスを確認できます。
```
$ conda env list
```

>[!NOTE]
>ローカルの Spark 環境に対して直接 PySpark をローカルに実行することは、現在サポート**されていません**。 Azure ML Workbench では、PySpark スクリプトのローカル Docker での実行がサポートされています。 Azure ML ベースの Docker イメージには、Spark 2.1.11 がプレインストールされています。 

_**Python スクリプトのローカル実行の概要:**_
![](media/experiment-execution-configuration/local-native-run.png)

## <a name="running-a-script-on-local-docker"></a>ローカル Docker でのスクリプトの実行
Azure ML の実行サービスを使用して、プロジェクトをローカル コンピューター上の Docker コンテナーで実行することもできます。 Azure ML Workbench には基本 Docker イメージが用意されています。このイメージには Azure ML ライブラリと Spark 2.1.11 ランタイムが付属しており、ローカルでの Spark の実行が簡単になっています。 ローカル コンピューターで Docker が既に実行されている必要があります。

ローカル Docker 上で Python スクリプトや PySpark スクリプトを実行するには、CLI で次のコマンドを実行します。

```
$az ml experiment submit -c docker myscript.py
```
or
```
az ml experiment submit --run-configuration docker myscript.py
```

ローカル Docker 上の実行環境は、Azure ML ベースの Docker イメージを使用して準備されます。 Azure ML Workbench では、初回実行時にこのイメージがダウンロードされ、ユーザーの conda_dependencies.yml ファイルに指定されたパッケージでそのイメージがオーバーレイされます。 この処理により、初回の実行は速度が遅くなりますが、後続の実行はキャッシュされたレイヤーが Workbench で再使用されるため、はるかに速くなります。 

>[!IMPORTANT]
>初回実行向けに Docker イメージを準備するには、最初に _az ml experiment prepare -c docker_ コマンドを実行する必要があります。 ご使用の docker.runconfig ファイルで、**PrepareEnvironment** パラメーターを true に設定することもできます。 この操作により、実行の一部で環境が自動的に準備されます。  

>[!NOTE]
>PySpark スクリプトを Spark 上で実行している場合は、conda_dependencies.yml に加えて、spark_dependencies.yml も使用されます。

Docker イメージ上でスクリプトを実行すると、次のような利点があります。

1. スクリプトを他の実行環境で正確に実行できます。 Docker コンテナー上での実行により、移植性に影響する恐れのあるローカル参照を発見したり、回避したりできます。 

2. Apache Spark などの複雑なインストールと構成のランタイムやフレームワークを、自分でインストールすることなくすばやくテストできます。


_**Python スクリプトのローカル Docker 実行の概要:**_
![](media/experiment-execution-configuration/local-docker-run.png)

## <a name="running-a-script-on-a-remote-docker"></a>リモート Docker でのスクリプトの実行
一部の例では、ユーザーのローカル コンピューター上で使用可能なリソースが、必要なモデルをトレーニングするのに十分でないことがあります。 このような場合には、Azure ML 実行サービスを使用すると、リモート Docker の実行を利用して、より高性能な VM 上で Python スクリプトや PySpark スクリプトを簡単に実行できます。 

リモート VM は次の要件を満たしている必要があります。
* リモート VM で Linux-Ubuntu が実行されている必要があります。また、SSH を使用してリモート VM アクセスできる必要があります。 
* リモート VM で Docker が実行されている必要があります。

>[!IMPORTANT]
> Docker が実行されている Windows VM は、リモート コンピューティング ターゲットとしてサポート**されていません**。


リモート Docker ベースの実行のコンピューティング ターゲット定義と実行構成を作成するには、次のコマンドを使用します。

```
az ml computetarget attach --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --password "sshpassword" --type remotedocker
```

コンピューティング ターゲットを構成したら、次のコマンドを使用してスクリプトを実行できます。
```
$ az ml experiment submit -c remotevm myscript.py
```
>[!NOTE]
>実行環境は conda_dependencies.yml 内の仕様を使用して構成されていることを覚えておいてください。 .runconfig ファイル内で PySpark フレームワークが指定されている場合には、spark_dependencies.yml も使用されます。 

リモート VM の Docker 構築プロセスはローカル Docker 実行のプロセスと全く同じであるため、同様の実行エクスペリエンスとなることが予想されます。

>[!TIP]
>初回実行向けに Docker イメージを構築して待ち時間が発生しないようにするには、次のコマンドを使用して、スクリプトを実行する前にコンピューティング ターゲットを準備します。 az ml experiment prepare -c <remotedocker>


_**Python スクリプトのリモート VM 実行の概要:**_
![](media/experiment-execution-configuration/remote-vm-run.png)


## <a name="running-a-script-on-hdinsight-cluster"></a>HDInsight クラスター上でのスクリプトの実行
HDInsight は、Apache Spark をサポートする、ビッグ データ分析のための一般的なプラットフォームです。 Azure ML Workbench では、HDInsight Spark クラスターを使用してビッグ データに関する実験を行うことができます。 

コンピューティング ターゲットを作成して HDInsight Spark クラスター向けに構成するには、次のコマンドを使用します。

```
$ az ml computetarget attach --name "myhdi" --address "<FQDN or IP address>" --username "sshuser" --password "sshpassword" --type cluster 
```

>[!NOTE]
>IP アドレスではなく FQDN を使用する場合、HDI Spark クラスターの名前は _foo_ となり、SSH エンドポイントの場所は _foo-ssh.azurehdinsight.net_ というドライバー ノード上になります。 _--address_ パラメーターに FQDN を使用する場合、サーバー名に **-ssh** という接尾辞を付けてください。


コンピューティング コンテキストを用意したら、次のコマンドを実行して PySpark スクリプトを実行できます。

```
$ az ml experiment submit -c myhdi myscript.py
```

Azure ML Workbench では、Conda を使用して HDInsight クラスター上の実行環境が準備および管理されます。 構成は、_conda_dependencies.yml_ および _spark_dependencies.yml_ の構成ファイルで管理されます。 

このモードで実験を行うには、ユーザーは HDInsight クラスターへの SSH アクセス権を保有している必要があります。 

>[!NOTE]
>サポートされている構成は、Linux (Python/PySpark 3.5.2 と Spark 2.1.11 を備えた Ubuntu) が実行されている HDInsight Spark クラスターです。

_**PySpark スクリプトの HDInsight ベースの実行の概要**_
![](media/experiment-execution-configuration/hdinsight-run.png)


## <a name="running-a-script-on-gpu"></a>GPU 上でのスクリプトの実行
GPU 上でスクリプトを実行するには、「[How to use GPU in Azure Machine Learning (Azure Machine Learning での GPU の使用方法)](how-to-use-gpu.md)」という記事で説明されているガイダンスに従います


## <a name="next-steps"></a>次のステップ
* [Azure Machine Learning を作成およびインストールする](quickstart-installation.md)
* [モデル管理](model-management-overview.md)
