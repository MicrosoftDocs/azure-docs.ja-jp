---
title: "Azure Machine Learning ワークベンチ実行構成ファイル"
description: "このドキュメントでは、Azure ML ワークベンチ実験の実行の構成設定について詳しく説明します。"
services: machine-learning
author: gokhanuluderya-msft
ms.author: gokhanu
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/17/2017
ms.openlocfilehash: e1356439385cc7fe66985bd2b84e4121386ec23d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-machine-learning-workbench-execution-configuration-files"></a>Azure Machine Learning ワークベンチ実行構成ファイル

Azure Machine Learning (Azure ML) ワークベンチのスクリプトを送信するときに、その実行動作は **aml_config** フォルダー内のファイルによって制御されます。 このフォルダーは、プロジェクト フォルダーのルートの下にあります。 最適な方法で目的の実行結果を得るには、これらのファイルの内容を理解しておくことが重要です。

このフォルダーの下にある関連ファイルを次に示します。
- conda_dependencies.yml
- spark_dependencies.yml
- コンピューティング ターゲット ファイル
    - \<コンピューティング ターゲット名>.compute
- 実行構成ファイル
    - \<実行構成名>.runconfig

>[!NOTE]
>通常、作成するコンピューティング ターゲットごとに、コンピューティング ターゲット ファイルと実行構成ファイルがあります。 ただし、これらのファイルは別々に作成でき、複数の実行構成ファイルを同じコンピューティング ターゲットにポイントさせることができます。

## <a name="condadependenciesyml"></a>conda_dependencies.yml
このファイルは、コードが依存している Python ランタイム バージョンおよびパッケージを指定する [conda 環境ファイル](https://conda.io/docs/using/envs.html#create-environment-file-by-hand)です。 Azure ML ワークベンチは Docker コンテナーや HDInsight クラスターでスクリプトを実行するときに、スクリプトを実行するための [conda 環境](https://conda.io/docs/using/envs.html)を作成します。 

このファイルでは、スクリプトが実行に必要な Python パッケージを指定します。 Azure ML ワークベンチ実行サービスは、依存関係の一覧に従って Docker イメージ内に conda 環境を作成します。 このパッケージの一覧には、実行エンジンから到達できる必要があります。 そのため、パッケージは次のようなチャネルに一覧表示される必要があります。

* [continuum.io](https://anaconda.org/conda-forge/repo)
* [PyPI](https://pypi.python.org/pypi)
* 公開されアクセス可能なエンドポイント (URL)
* またはローカル ファイル パス
* 実行エンジンで到達可能なその他のチャネル

>[!NOTE]
>HDInsight クラスター上で実行する場合、Azure ML ワークベンチは実行しているユーザー専用に conda 環境を作成します。 これにより、さまざまなユーザーが同じクラスター上の別々の python 環境で実行できます。  

一般的な **conda_dependencies.yml** ファイルの例を次に示します。
```yaml
name: project_environment
dependencies:
  # Python version
  - python=3.5.2
  
  # some conda packages
  - scikit-learn
  - cryptography
  
  # use pip to install some more packages
  - pip:
     # a package in PyPi
     - azure-storage
     
     # a package hosted in a public URL endpoint
     - https://cntk.ai/PythonWheel/CPU-Only/cntk-2.1-cp35-cp35m-win_amd64.whl
     
     # a wheel file available locally on disk (this only works if you are executing against local target)
     - C:\temp\my_private_python_pkg.whl
```

Azure ML ワークベンチは、**conda_dependencies.yml** が損なわれずにいる限り、リビルドせずに同じ conda 環境を使用します。 ただし、このファイルで何らかの変更が生じた場合は、Docker イメージがリビルドされます。

>[!NOTE]
>_ローカル_ コンピューティング コンテキストを実行の対象とする場合、**conda_dependencies.yml** ファイルは使用され**ません**。 ローカル Azure ML ワークベンチ Python 環境のパッケージの依存関係は、手動でインストールする必要があります。

## <a name="sparkdependenciesyml"></a>spark_dependencies.yml
このファイルは、インストールする必要のある PySpark スクリプトおよび Spark パッケージを送信するときに、Spark アプリケーション名を指定します。 すべてのパブリック Maven リポジトリのほか、これらの Maven リポジトリに含まれる Spark パッケージも指定できます。

たとえば次のようになります。

```yaml
configuration:
  # Spark application name
  "spark.app.name": "ClassifyingIris"
  
repositories:
  # Maven repository hosted in Azure CDN
  - "https://mmlspark.azureedge.net/maven"
  
  # Maven repository hosted in spark-packages.org
  - "https://spark-packages.org/packages"
  
packages:
  # MMLSpark package hosted in the Azure CDN Maven
  - group: "com.microsoft.ml.spark"
    artifact: "mmlspark_2.11"
    version: "0.5"
    
  # spark-sklearn packaged hosted in the spark-packages.org Maven
  - group: "databricks"
    artifact: "spark-sklearn"
    version: "0.2.0"
```

>[!NOTE]
>worker サイズやコアなどのクラスター チューニング パラメーター は、spark_dependecies.yml ファイル内の "configuration" セクションに入れる必要があります 

>[!NOTE]
>Python 環境でスクリプトを実行している場合、*spark_dependencies.yml* ファイルは無視されます。 Spark に対して (Docker または HDInsight クラスター上で) 実行している場合にのみ効果があります。

## <a name="run-configuration"></a>実行構成
特定の実行構成を指定するには、1 組のファイルが必要です。 これらのファイルは通常、CLI コマンドを使用して生成されます。 ただし、既存のものを複製し、その名前を変更して、編集することもできます。

```azurecli
# create a compute target pointing to a VM via SSH
$ az ml computetarget attach -n <compute target name> -a <IP address or FQDN of VM> -u <username> -w <password> --type remotedocker

# create a compute context pointing to an HDI cluster head-node via SSH
$ az ml computetarget attach -n <compute target name> -a <IP address or FQDN of HDI cluster> -u <username> -w <password> -type cluster
```

このコマンドは、指定されたコンピューティング ターゲットに基づいて 1 組のファイルを作成します。 たとえば、コンピューティング ターゲットに _foo_ という名前を付けたとします。 このコマンドは、**aml_config** フォルダーに _foo.compute_ と _foo.runconfig_ を生成します。

>[!NOTE]
> 実行構成ファイルの _local_ または _docker_ の名前は任意です。 Azure ML ワークベンチは利便性のため、空のプロジェクトを作成するときに、これらの 2 つの実行構成を追加します。 プロジェクト テンプレートに付属する "<run configuration name>.runconfig" ファイルの名前を変更することも、任意の名前で新しいファイルを作成することもできます。

### <a name="compute-target-namecompute"></a>\<コンピューティング ターゲット名>.compute
_\<コンピューティング ターゲット名>.compute_ ファイルは、コンピューティング ターゲットの接続および構成情報を指定します。 これは名前と値のペアの一覧になります。 サポートされている設定は次のとおりです。

**type**: コンピューティング環境の種類。 サポートされる値は次のとおりです。
  - local
  - docker
  - remotedocker
  - cluster

**baseDockerImage**: Python/PySpark スクリプトを実行するために使用される Docker イメージ。 既定値は _microsoft/mmlspark:plus-0.7.91_ です。 その他に _microsoft/mmlspark:plus-gpu-0.7.91_ のイメージもサポートしています。これは、ホスト マシンへの GPU アクセスを可能にします (GPU が存在する場合)。

**address**: 仮想マシンまたは HDInsight クラスター ヘッド ノードの IP アドレスまたは FQDN (完全修飾ドメイン名)。

**username**: 仮想マシンまたは HDInsight ヘッド ノードにアクセスするための SSH ユーザー名。

**password**: SSH 接続の暗号化パスワード。

**sharedVolumes**: プロジェクト ファイルを送受信するために、実行エンジンで Docker 共有ボリューム機能を使用する必要があることを通知するフラグ。 このフラグをオンにすると、Docker が直接プロジェクトにアクセスでき、プロジェクトをコピーする必要がなくなるため、実行速度が向上します。 Windows での Docker のボリューム共有は信頼性に欠けることがあるので、_false_ に設定することをお勧めします。 macOS や Linux で実行している場合は、_true_ に設定してください。

**nvidiaDocker**: _true_ に設定した場合、このフラグは、通常の _docker_ コマンドではなく _nvidia-docker_ コマンドを Docker イメージの起動に使用するように、Azure ML ワークベンチ実行サービスに指示します。 _nvidia-docker_ エンジンは、Docker コンテナーが GPU ハードウェアにアクセスできるようにします。  Docker コンテナーで GPU 実行を行う場合、設定が必要です。 Linux ホストだけが _nvidia-docker_ をサポートしています。 たとえば、Azure の Linux ベースの DSVM には _nvidia-docker_ が付属しています。 _nvidia-docker_ は現時点では Windows でサポートされていません。

**nativeSharedDirectory**: このプロパティは、同じコンピューティング ターゲットでの複数の実行で共有するためにファイルを保存できるベース ディレクトリ (例: _~/.azureml/share/_) を指定します。 Docker コンテナーでの実行時にこの設定を使用する場合、_sharedVolumes_ を true に設定する必要があります。 それ以外の場合、実行は失敗します。

### <a name="run-configuration-namerunconfig"></a>\<実行構成名>.runconfig
_\<実行構成名>.runconfig_ は、Azure ML ワークベンチの実行動作を指定します。 実行履歴の追跡や使用するコンピューティング ターゲットなど、多くの実行動作を構成できます。 実行構成ファイルの名前は、Azure ML ワークベンチのデスクトップ アプリケーションでの実行コンテキスト ドロップダウンの作成に使用されます。

**ArgumentVector**: このセクションでは、この実行の一部として行われるスクリプトと、スクリプトのパラメーターを指定します。 たとえば、"<run configuration name>.runconfig" ファイルに次のスニペットがある場合 

```
 "ArgumentVector":[
  - "myscript.py"
  - 234
  - "-v" 
 ] 
```
_"az ml experiment submit foo.runconfig"_ は自動的に、パラメーターとして 234 を渡す _myscript.py_ ファイルでコマンドを実行し、-verbose フラグを設定します。

**Target**: このパラメーターは、_runconfig_ ファイルが参照する _.compute_ ファイルの名前です。 これは通常、_foo.compute_ ファイルをポイントしますが、別のコンピューティング ターゲットにポイントするように編集できます。

**Environment Variables**: このセクションでは、ユーザーは実行の一部として環境変数を設定できます。 ユーザーは、次の形式で名前と値のペアを使用して、環境変数を指定できます。
```
EnvironmentVariables:
"EXAMPLE_ENV_VAR1": "Example Value1"
"EXAMPLE_ENV_VAR2": "Example Value2"
```

これらの環境変数には、ユーザーのコードでアクセスできます。 たとえば、この Phyton コードでは、"EXAMPLE_ENV_VAR" という環境変数が出力されます
```
print(os.environ.get("EXAMPLE_ENV_VAR1"))
```

**Framework**: このプロパティは、Azure ML ワークベンチが、スクリプトを実行するために Spark セッションを起動する必要があるかどうかを指定します。 既定値は _PySpark_ です。 PySpark コードを実行していない場合は、これを _Python_ に設定してください。オーバーヘッドが少なく、ジョブの迅速な起動に役立ちます。

**CondaDependenciesFile**: このプロパティは、*aml_config* フォルダー内の conda 環境依存関係を指定するファイルをポイントします。 _null_ に設定した場合、既定の **conda_dependencies.yml** ファイルをポイントします。

**SparkDependenciesFile**: このプロパティは、**aml_config** フォルダー内の Spark 依存関係を指定するファイルをポイントします。 _null_ に設定した場合、既定の **spark_dependencies.yml** ファイルをポイントします。

**PrepareEnvironment**:このプロパティは、_true_ に設定した場合、初期実行の一部として指定された conda 依存関係に基づいて、conda 環境を準備するように実行サービスに指示します。 このプロパティは、Docker 環境に対して実行するときにのみ有効です。 この設定は、_local_ 環境に対して実行している場合、何も起こりません。 

**TrackedRun**: このフラグは、Azure ML ワークベンチ実行履歴インフラストラクチャ内での実行を追跡するかどうかを、実行サービスに指示します。 既定値は _true_です。 

**UseSampling**: _UseSampling_ は、データ ソースのアクティブなサンプル データセットを実行に使用するかどうかを指定します。 _false_ に設定した場合、データ ソースを取り込み、データ ストアから読み取られた完全なデータを使用します。 _true_ に設定した場合、アクティブなサンプルが使用されます。 ユーザーは、"DataSourceSettings" を使用して、アクティブなサンプルを上書きする場合に使用する特定のサンプル データセットを指定できます。 

**DataSourceSettings**: この構成セクションは、データ ソース設定を指定します。 このセクションでは、ユーザーは、実行の一部として使用される特定のデータ ソースの既存のデータ サンプルを指定します。 

次の構成設定は、"MySample" という名前のサンプルが、"MyDataSource" という名前のデータソースに使用されることを指定します
```
DataSourceSettings:
    MyDataSource.dsource:
    Sampling:
    Sample: MySample
```

**DataSourceSubstitutions**: データ ソースの置換は、ユーザーがコードを変更せずにあるデータ ソースから別のデータ ソースに切り替えるときに使用できます。 たとえば、ユーザーはデータ ソース参照を変更することにより、サンプリングされたローカル ファイルから、Azure BLOB に格納された元のより大きなデータセットに切り替えることができます。 置換を使用する場合、Azure ML ワークベンチは、代替のデータ ソースを参照して、データ ソースおよびデータ準備パッケージを実行します。

次の例は、Azure ML データ ソースやデータ準備パッケージ内の"mylocal.datasource" 参照を "myremote.dsource" に置き換えます。 
 
```
DataSourceSubstitutions:
    myocal.dsource: myremote.dsource
```

上記の置換に基づいて、次のコード サンプルは、ユーザーがコードを変更することなく、"mylocal.dsource" の代わりに "myremote.dsource" から読み取られるようになります。
```
df = datasource.load_datasource('mylocal.dsource')
```
## <a name="next-steps"></a>次のステップ
[実行環境の構成](experiment-execution-configuration.md)の詳細を確認してください
