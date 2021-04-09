---
title: Visual Studio Code を使用した対話型デバッグ
titleSuffix: Azure Machine Learning
description: Visual Studio Code を使用して Azure Machine Learning のコード、パイプライン、およびデプロイを対話形式でデバッグします
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: luisquintanilla
ms.author: luquinta
ms.date: 09/30/2020
ms.openlocfilehash: 783b5afdaef369582614cde3525f7968fdb5e567
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102508641"
---
# <a name="interactive-debugging-with-visual-studio-code"></a>Visual Studio Code を使用した対話型デバッグ



Visual Studio Code (VS Code) および [debugpy](https://github.com/microsoft/debugpy/) を使用して Azure Machine Learning の実験、パイプライン、およびデプロイを対話形式でデバッグする方法について説明します。

## <a name="run-and-debug-experiments-locally"></a>ローカルでの実験の実行とデバッグ

Azure Machine Learning 拡張機能を使用して、クラウドに送信する前に Machine Learning の実験を検証、実行、およびデバッグします。

### <a name="prerequisites"></a>前提条件

* Azure Machine Learning VS Code 拡張機能 (プレビュー)。 詳しくは、[Azure Machine Learning VS Code 拡張機能の設定](tutorial-setup-vscode-extension.md)に関するページを参照してください。
* [Docker](https://www.docker.com/get-started)
  * Mac および Windows 用の Docker デスクトップ
  * Linux 用 Docker エンジン。
* [Python 3](https://www.python.org/downloads/)

> [!NOTE]
> Windows では、[Linux コンテナーを使用するように Docker を構成](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)してください。

> [!TIP]
> 必須ではありませんが、Windows の場合は [Linux 用 Windows サブシステム (WSL) 2 で Docker を使用する](/windows/wsl/tutorials/wsl-containers#install-docker-desktop)ことを強くお勧めします。

> [!IMPORTANT]
> 実験をローカルで実行する前に、Docker が実行されていることを確認してください。

### <a name="debug-experiment-locally"></a>ローカルで実験をデバッグする

1. VS Code で、Azure Machine Learning 拡張機能ビューを開きます。
1. ワークスペースが含まれているサブスクリプション ノードを展開します。 既存のものがない場合は、拡張機能を使用して [Azure Machine Learning ワークスペースを作成](how-to-manage-resources-vscode.md#create-a-workspace)できます。
1. ワークスペース ノードを展開します。
1. **[実験]** ノードを右クリックし、 **[実験の作成]** を選択します。 プロンプトが表示されたら、実験の名前を指定します。
1. **[実験]** ノードを展開し、実行する実験を右クリックして、 **[実験の実行]** を選択します。
1. 実験を実行するオプションの一覧から、 **[ローカル]** を選択します。
1. **Windows で初めて使用する場合のみ**。 ファイル共有を許可するように求めるメッセージが表示されたら、 **[はい]** を選択します。 ファイル共有を有効にすると、Docker では、スクリプトが格納されているディレクトリをコンテナーにマウントできます。 さらに、Docker では、実行からのログと出力をシステムの一時ディレクトリに格納することもできます。
1. 実験をデバッグするには **[はい]** を選択します。 それ以外の場合は、 **[いいえ]** を選択します。 [いいえ] を選択すると、デバッガーにアタッチせずにローカルで実験が実行されます。
1. **[新しい実行構成を作成する]** を選択して、実行構成を作成します。 実行構成によって、実行するスクリプト、依存関係、および使用されるデータセットが定義されます。 または、既存のものがある場合は、ドロップダウンから選択します。
    1. 環境を選択します。 [Azure Machine Learning のキュレーションされた環境](resource-curated-environments.md)のいずれかから選択することも、独自のものを作成することもできます。
    1. 実行するスクリプトの名前を指定します。 パスは、VS Code で開かれているディレクトリに対する相対パスです。
    1. Azure Machine Learning データセットを使用するかどうかを選択します。 拡張機能を使用して [Azure Machine Learning データセット](how-to-manage-resources-vscode.md#create-dataset)を作成できます。
    1. debugpy は、実験を実行しているコンテナーにデバッガーをアタッチするために必要です。 debugpy を依存関係として追加するには、 **[debugpy の追加]** を選択します。 その以外の場合は、 **[スキップ]** を選択します。 debugpy を依存関係として追加しないと、デバッガーにアタッチせずに実験が実行されます。
    1. 実行構成の設定が含まれている構成ファイルがエディターで開きます。 設定に問題がなければ、 **[実験を送信する]** を選択します。 または、メニュー バーからコマンド パレットを開き ( **[ビュー] > [コマンド パレット]** )、テキスト ボックスに `Azure ML: Submit experiment` コマンドを入力します。
1. 実験が送信されると、スクリプトおよび実行構成で指定された構成を含む Docker イメージが作成されます。

    Docker イメージのビルド プロセスが開始されると、`60_control_log.txt` ファイルの内容が VS Code の出力コンソールにストリーミングされます。

    > [!NOTE]
    > 初めて Docker イメージを作成するときには、数分かかることがあります。

1. イメージがビルドされると、デバッガーを開始するためのプロンプトが表示されます。 スクリプトにブレークポイントを設定し、デバッグを開始する準備ができたら **[デバッガーを開始]** を選択します。 これにより、実験を実行しているコンテナーに VS Code デバッガーがアタッチされます。 または、Azure Machine Learning 拡張機能で、現在の実行のノードの上にマウス ポインターを移動し、再生アイコンを選択してデバッガーを開始します。

    > [!IMPORTANT]
    > 1 つの実験に対して複数のデバッグ セッションを作成することはできません。 ただし、複数の VS Code インスタンスを使用して複数の実験をデバッグすることはできます。

この時点で、VS Code を使用してコードをステップ実行してデバッグできるようになります。

いずれかの時点で実行を取り消す場合は、実行ノードを右クリックして **[実行の取り消し]** を選択します。

リモート実験の実行と同様に、実行ノードを展開してログと出力を調べることができます。

> [!TIP]
> 環境内で定義されている同じ依存関係を使用する Docker イメージは、実行間で再利用されます。 ただし、新規または別の環境を使用して実験を実行すると、新しいイメージが作成されます。 これらのイメージはローカル ストレージに保存されるため、古いまたは使用されていない Docker イメージは削除することをお勧めします。 システムからイメージを削除するには、[Docker CLI](https://docs.docker.com/engine/reference/commandline/rmi/) または [VS Code Docker 拡張機能](https://code.visualstudio.com/docs/containers/overview)を使用します。

## <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>機械学習パイプラインのデバッグとトラブルシューティング

場合によっては、ML パイプラインで使用される Python コードを対話的にデバッグする必要が生じることがあります。 VS Code と debugpy を使用すると、トレーニング環境で実行されているコードにアタッチできます。

### <a name="prerequisites"></a>前提条件

* __Azure Virtual Network__ を使用するように構成された __Azure Machine Learning ワークスペース__。
* パイプライン ステップの一部として Python スクリプトを使用する __Azure Machine Learning パイプライン__。 たとえば、PythonScriptStep です。
* Azure Machine Learning コンピューティング クラスター。これは __仮想ネットワーク内__ にあり、__パイプラインによってトレーニングのために使用されます__。
* __仮想ネットワーク内__ に存在する __開発環境__。 開発環境は、次のいずれかになります。

  * 仮想ネットワーク内の Azure 仮想マシン
  * 仮想ネットワーク内の Notebook VM のコンピューティング インスタンス
  * VPN または ExpressRoute を使用した仮想ネットワークへのプライベート ネットワーク接続があるクライアント コンピューター。

Azure Machine Learning で Azure Virtual Network を使用する方法の詳細については、「[仮想ネットワークの分離とプライバシーの概要](how-to-network-security-overview.md)」を参照してください。

> [!TIP]
> 仮想ネットワークの内側にない Azure Machine Learning リソースを操作することもできますが、仮想ネットワークの使用をお勧めします。

### <a name="how-it-works"></a>しくみ

ML パイプライン ステップでは、Python スクリプトが実行されます。 これらのスクリプトは、次の操作を実行するために変更されています。

1. それらが実行されるホストの IP アドレスをログに記録します。 この IP アドレスを使用して、デバッガーをスクリプトに接続します。

2. debugpy デバッグ コンポーネントを起動し、デバッガーが接続されるまで待機します。

3. 開発環境から、トレーニング プロセスによって作成されたログを監視して、スクリプトが実行されている IP アドレスを確認します。

4. `launch.json` ファイルを使用して、デバッガーを接続するための IP アドレスを VS Code に示します。

5. デバッガーをアタッチし、スクリプトを対話的にステップ実行します。

### <a name="configure-python-scripts"></a>Python スクリプトを構成する

デバッグを有効にするには、ML パイプライン ステップで使用される Python スクリプトに次の変更を加えます。

1. 次の import ステートメントを追加します。

    ```python
    import argparse
    import os
    import debugpy
    import socket
    from azureml.core import Run
    ```

1. 次の引数を追加します。 これらの引数を使用すると、必要に応じてデバッガーを有効にし、デバッガーをアタッチするためのタイムアウトを設定できます。

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                        default=300,
                        help=f'Defines how much time the AML compute target '
                        f'will await a connection from a debugger client (VSCODE).')
    parser.add_argument('--remote_debug_client_ip', type=str,
                        help=f'Defines IP Address of VS Code client')
    parser.add_argument('--remote_debug_port', type=int,
                        default=5678,
                        help=f'Defines Port of VS Code client')
    ```

1. 次のステートメントを追加します。 これらのステートメントは現在の実行コンテキストを読み込んで、コードが実行されているノードの IP アドレスをログに記録できるようにします。

    ```python
    global run
    run = Run.get_context()
    ```

1. debugpy を起動してデバッガーがアタッチされるまで待機する `if` ステートメントを追加します。 タイムアウト前にデバッガーがアタッチされない場合、スクリプトは通常どおり続行されます。 必ず `listen` 関数の `HOST` と `PORT` の値をご自分のものに置き換えてください。

    ```python
    if args.remote_debug:
        print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
        # Log the IP and port
        try:
            ip = args.remote_debug_client_ip
        except:
            print("Need to supply IP address for VS Code client")
        print(f'ip_address: {ip}')
        debugpy.listen(address=(ip, args.remote_debug_port))
        # Wait for the timeout for debugger to attach
        debugpy.wait_for_client()
        print(f'Debugger attached = {debugpy.is_client_connected()}')
    ```

次の Python の例は、デバッグを有効にするシンプルな `train.py` ファイルを示しています。

```python
# Copyright (c) Microsoft. All rights reserved.
# Licensed under the MIT license.

import argparse
import os
import debugpy
import socket
from azureml.core import Run

print("In train.py")
print("As a data scientist, this is where I use my training code.")

parser = argparse.ArgumentParser("train")

parser.add_argument("--input_data", type=str, help="input data")
parser.add_argument("--output_train", type=str, help="output_train directory")

# Argument check for remote debugging
parser.add_argument('--remote_debug', action='store_true')
parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the AML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
parser.add_argument('--remote_debug_client_ip', type=str,
                    help=f'Defines IP Address of VS Code client')
parser.add_argument('--remote_debug_port', type=int,
                    default=5678,
                    help=f'Defines Port of VS Code client')

# Get run object, so we can find and log the IP of the host instance
global run
run = Run.get_context()

args = parser.parse_args()

# Start debugger if remote_debug is enabled
if args.remote_debug:
    print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
    # Log the IP and port
    # ip = socket.gethostbyname(socket.gethostname())
    try:
        ip = args.remote_debug_client_ip
    except:
        print("Need to supply IP address for VS Code client")
    print(f'ip_address: {ip}')
    debugpy.listen(address=(ip, args.remote_debug_port))
    # Wait for the timeout for debugger to attach
    debugpy.wait_for_client()
    print(f'Debugger attached = {debugpy.is_client_connected()}')

print("Argument 1: %s" % args.input_data)
print("Argument 2: %s" % args.output_train)

if not (args.output_train is None):
    os.makedirs(args.output_train, exist_ok=True)
    print("%s created" % args.output_train)
```

### <a name="configure-ml-pipeline"></a>ML パイプラインを構成する

debugpy を起動して実行コンテキストを取得するために必要な Python パッケージを提供するには、環境を作成して `pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>']` を設定します。 使用しているものと一致するように SDK のバージョンを変更します。 次のコード スニペットでは、環境を作成する方法を示します。

```python
# Use a RunConfiguration to specify some additional requirements for this step.
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_CPU_IMAGE

# create a new runconfig object
run_config = RunConfiguration()

# enable Docker 
run_config.environment.docker.enabled = True

# set Docker base image to the default CPU-based image
run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE

# use conda_dependencies.yml to create a conda environment in the Docker image for execution
run_config.environment.python.user_managed_dependencies = False

# specify CondaDependencies obj
run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
                                                                           pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>'])
```

「[Python スクリプトを構成する](#configure-python-scripts)」セクションでは、ML パイプライン ステップで使用されるスクリプトに新しい引数を追加しました。 次のコード スニペットは、これらの引数を使用して、コンポーネントのデバッグを有効にし、タイムアウトを設定する方法を示しています。 また、`runconfig=run_config` を設定して、前の手順で作成した環境を使用する方法も示しています。

```python
# Use RunConfig from a pipeline step
step1 = PythonScriptStep(name="train_step",
                         script_name="train.py",
                         arguments=['--remote_debug', '--remote_debug_connection_timeout', 300,'--remote_debug_client_ip','<VS-CODE-CLIENT-IP>','--remote_debug_port',5678],
                         compute_target=aml_compute,
                         source_directory=source_directory,
                         runconfig=run_config,
                         allow_reuse=False)
```

パイプラインを実行すると、各ステップで子実行が作成されます。 デバッグが有効になっている場合は、変更後のスクリプトによって、子実行に対して `70_driver_log.txt` に次のテキストのような情報がログに記録されます。

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

`ip_address` の値を保存します。 次のセクションで使用します。

> [!TIP]
> IP アドレスは、このパイプライン ステップの子実行に対する実行ログからも見つけることができます。 この情報を表示する方法の詳細については、「[Azure ML の実験の実行とメトリックを監視する](how-to-track-experiments.md)」を参照してください。

### <a name="configure-development-environment"></a>開発環境の設定

1. VS Code 開発環境に debugpy をインストールするには、次のコマンドを使用します。

    ```bash
    python -m pip install --upgrade debugpy
    ```

    VS Code で debugpy を使用する方法について詳しくは、[リモート デバッグ](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection)に関するページをご覧ください。

1. デバッガーを実行している Azure Machine Learning コンピューティングと通信するように VS Code を構成するには、新しいデバッグ構成を作成します。

    1. VS Code から __[デバッグ]__ メニューを選択し、 __[構成を開く]__ を選択します。 __launch.json__ という名前のファイルが開きます。

    1. __launch.json__ ファイルで、`"configurations": [` を含む行を見つけ、その後に次のテキストを挿入します。 `"host": "<IP-ADDRESS>"` エントリを、前のセクションのログで返された IP アドレスに変更します。 `"localRoot": "${workspaceFolder}/code/step"` エントリを、デバッグ対象のスクリプトのコピーが格納されているローカル ディレクトリに変更します。

        ```json
        {
            "name": "Azure Machine Learning Compute: remote debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "<IP-ADDRESS>",
            "redirectOutput": true,
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}/code/step1",
                    "remoteRoot": "."
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > 構成セクションに既に他のエントリがある場合は、挿入したコードの後にコンマ (,) を追加します。

        > [!TIP]
        > 特にパイプラインの場合は、コードがそれぞれの手順にのみ関連するように、スクリプトのリソースを別々のディレクトリに保存しておくことをお勧めします。 この例では、`localRoot` の値の例が `/code/step1` を参照しています。
        >
        > 複数のスクリプトをデバッグする場合は、別々のディレクトリで、スクリプトごとに個別の構成セクションを作成します。

    1. __launch.json__ ファイルを保存します。

### <a name="connect-the-debugger"></a>デバッガーを接続する

1. VS Code を開き、スクリプトのローカル コピーを開きます。
2. アタッチしたときにスクリプトを停止するブレークポイントを設定します。
3. 子プロセスでスクリプトが実行されていて、`Timeout for debug connection` がログに表示されている間に、F5 キーを使用するか、 __[デバッグ]__ を選択します。 メッセージが表示されたら、 __[Azure Machine Learning Compute: remote debug]\(Azure Machine Learning コンピューティング: リモート デバッグ\)__ 構成を選択します。 サイドバーからデバッグ アイコンを選択し、[デバッグ] ドロップダウン メニューから __[Azure Machine Learning: remote debug]\(Azure Machine Learning: リモートデバッグ\)__ エントリを選択し、緑色の矢印を使用してデバッガーをアタッチすることもできます。

    この時点で、VS Code は計算ノードの debugpy に接続し、前に設定したブレークポイントで停止します。 これで、実行時のようにコードをステップ実行したり、変数を表示したりできます。

    > [!NOTE]
    > ログに `Debugger attached = False` を示すエントリが表示された場合、タイムアウトに達しており、スクリプトはデバッガーなしで続行されます。 `Timeout for debug connection` メッセージの後、タイムアウトに達する前に、パイプラインをもう一度送信してデバッガーを接続します。

## <a name="debug-and-troubleshoot-deployments"></a>デプロイのデバッグとトラブルシューティング

場合によっては、モデル デプロイに含まれる Python コードを対話的にデバッグする必要が生じることがあります。 たとえば、エントリ スクリプトが失敗し、追加のログ記録によっても理由を特定できない場合がこれにあたります。 VS Code と debugpy を使用すると、Docker コンテナー内で実行されているコードにアタッチできます。

> [!IMPORTANT]
> このデバッグ方法は、`Model.deploy()` と `LocalWebservice.deploy_configuration` を使用してローカルでモデルをデプロイしている場合は機能しません。 代わりに、[Model.package()](/python/api/azureml-core/azureml.core.model.model#package-workspace--models--inference-config-none--generate-dockerfile-false-) クラスを使用してイメージを作成する必要があります。

ローカル Web サービスのデプロイでは、ローカル システムで動作する Docker インストールが必要です。 Docker の使用の詳細については、[Docker のドキュメント](https://docs.docker.com/)を参照してください。 コンピューティング インスタンスを使用する場合は、Docker が既にインストールされていることに注意してください。

### <a name="configure-development-environment"></a>開発環境の設定

1. ローカルの VS Code 開発環境に debugpy をインストールするには、次のコマンドを使用します。

    ```bash
    python -m pip install --upgrade debugpy
    ```

    VS Code で debugpy を使用する方法について詳しくは、[リモート デバッグ](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection)に関するページをご覧ください。

1. Docker イメージと通信するように VS Code を構成するには、次のような新しいデバッグ構成を作成します。

    1. VS Code から、 __[実行]__ 拡張機能の __[デバッグ]__ メニューを選択し、 __[構成を開く]__ を選択します。 __launch.json__ という名前のファイルが開きます。

    1. __launch.json__ ファイルで、 __"configurations"__ の項目 (`"configurations": [` を含む行) を見つけ、その後に次のテキストを挿入します。 

        ```json
        {
            "name": "Azure Machine Learning Deployment: Docker Debug",
            "type": "python",
            "request": "attach",
            "connect": {
                "port": 5678,
                "host": "0.0.0.0",
            },
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```
        挿入すると、__launch.json__ ファイルは次のようになります。
        ```json
        {
        // Use IntelliSense to learn about possible attributes.
        // Hover to view descriptions of existing attributes.
        // For more information, visit: https://go.microsoft.com/fwlink/linkid=830387
        "version": "0.2.0",
        "configurations": [
            {
                "name": "Python: Current File",
                "type": "python",
                "request": "launch",
                "program": "${file}",
                "console": "integratedTerminal"
            },
            {
                "name": "Azure Machine Learning Deployment: Docker Debug",
                "type": "python",
                "request": "attach",
                "connect": {
                    "port": 5678,
                    "host": "0.0.0.0"
                    },
                "pathMappings": [
                    {
                        "localRoot": "${workspaceFolder}",
                        "remoteRoot": "/var/azureml-app"
                    }
                ]
            }
            ]
        }
        ```

        > [!IMPORTANT]
        > 構成セクションに既に他のエントリがある場合は、挿入したコードの後にコンマ ( __,__ ) を追加します。

        このセクションは、ポート __5678__ を使用して Docker コンテナーにアタッチされます。

    1. __launch.json__ ファイルを保存します。

### <a name="create-an-image-that-includes-debugpy"></a>debugpy を含むイメージの作成

1. debugpy が含まれるように、デプロイの Conda 環境を変更します。 次の例は、`pip_packages` パラメーターを使用して PTVSD を追加する方法を示しています。

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 


    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.83', 'debugpy'])

    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. サービスの開始時に debugpy を起動し、接続を待機するには、`score.py` ファイルの先頭に次を追加します。

    ```python
    import debugpy
    # Allows other computers to attach to debugpy on this IP address and port.
    debugpy.listen(('0.0.0.0', 5678))
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    debugpy.wait_for_client()
    print("Debugger attached...")
    ```

1. 環境定義に基づいてイメージを作成し、そのイメージをローカル レジストリにプルします。 

    > [!NOTE]
    > この例は、`ws` が Azure Machine Learning ワークスペースをポイントし、`model` がデプロイされているモデルであることを前提としています。 `myenv.yml` ファイルには、手順 1. で作成した Conda の依存関係が含まれています。

    ```python
    from azureml.core.conda_dependencies import CondaDependencies
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
    myenv.docker.base_image = None
    myenv.docker.base_dockerfile = "FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04"
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    package = Model.package(ws, [model], inference_config)
    package.wait_for_creation(show_output=True)  # Or show_output=False to hide the Docker build logs.
    package.pull()
    ```

    イメージが作成されてダウンロードされると (このプロセスには 10 分以上かかる場合があるため、気長にお待ちください)、次のようなメッセージでイメージ パス (リポジトリ、名前、タグ、また、ここではそのダイジェストを含む) が最終的に表示されます。

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. イメージをローカルで簡単に操作できるようにするには、次のコマンドを使用して、このイメージのタグを追加します。 次のコマンドの `myimagepath` を、前の手順の場所の値で置き換えます。

    ```bash
    docker tag myimagepath debug:1
    ```

    残りの手順では、完全なイメージ パス値の代わりに、`debug:1` でローカル イメージを参照できます。

### <a name="debug-the-service"></a>サービスのデバッグ

> [!TIP]
> `score.py` ファイルで debugpy 接続のタイムアウトを設定している場合、タイムアウトの有効期限が切れる前に、VS Code をデバッグ セッションに接続する必要があります。 このセクションの手順を使用する前に、VS Code を起動して `score.py` のローカル コピーを開き、ブレークポイントを設定して準備を整えておく必要があります。
>
> デバッグとブレークポイントの設定の詳細については、「[Debugging (デバッグ)](https://code.visualstudio.com/Docs/editor/debugging)」を参照してください。

1. イメージを使って Docker コンテナーを開始するには、次のコマンドを使用します。

    ```bash
    docker run -it --name debug -p 8000:5001 -p 5678:5678 -v <my_local_path_to_score.py>:/var/azureml-app/score.py debug:1 /bin/bash
    ```

    これにより、`score.py` がコンテナー内のものにローカルでアタッチされます。 そのため、エディターで行われた変更はすべて、コンテナーに自動的に反映されます。

2. エクスペリエンスを向上させるために、新しい VS Code インターフェイスを使用してコンテナーにアクセスできます。 VS Code のサイドバーから `Docker` の拡張機能を選択し、自分のローカル コンテナー (このドキュメントでは `debug:1`) を見つけます。 このコンテナーを右クリックして `"Attach Visual Studio Code"` を選択すると、新しい VS Code インターフェイスが自動的に開きます。このインターフェイスに作成したコンテナーの内部が表示されます。

    ![コンテナー VS Code インターフェイス](./media/how-to-troubleshoot-deployment/container-interface.png)

3. コンテナー内で、シェルから次のコマンドを実行します

    ```bash
    runsvdir /var/runit
    ```
    これにより、コンテナー内のシェルに次の出力が表示されます。

    ![コンテナーの実行コンソールの出力](./media/how-to-troubleshoot-deployment/container-run.png)

4. VS Code をコンテナー内の debugpy に接続するには、VS Code を開き、F5 キーを使用するか __[デバッグ]__ を選択します。 メッセージが表示されたら、 __[Azure Machine Learning Deployment: Docker Debug]__ 構成を選択します。 サイド バーの __[実行]__ 拡張機能アイコンを選択して、[デバッグ] ドロップダウン メニューから __[Azure Machine Learning Deployment: Docker Debug]__ エントリを選択し、緑色の矢印を使用してデバッガーをアタッチすることもできます。

    ![デバッグ アイコン、デバッグの開始ボタン、および構成セレクター](./media/how-to-troubleshoot-deployment/start-debugging.png)
    
    緑色の矢印をクリックしてデバッガーをアタッチすると、コンテナー VS Code インターフェイスに新しい情報が表示されます。
    
    ![コンテナー デバッガーのアタッチされた情報](./media/how-to-troubleshoot-deployment/debugger-attached.png)
    
    また、メインの VS Code インターフェイスには、次のような情報が表示されます。

    ![score.py の VS Code ブレークポイント](./media/how-to-troubleshoot-deployment/local-debugger.png)

ここで、コンテナーにアタッチされているローカル `score.py` は、設定したブレークポイントで既に停止しています。 この時点で、VS Code は Docker コンテナー内の debugpy に接続され、前に設定したブレークポイントで Docker コンテナーを停止させます。 これで、実行時のようにコードをステップ実行したり、変数を表示したりできます。

VS Code を使用した Python のデバッグの詳細については、「[Python コードのデバッグ](https://code.visualstudio.com/docs/python/debugging)」を参照してください。

### <a name="stop-the-container"></a>コンテナーの停止

コンテナーを停止するには、次のコマンドを使用します。

```bash
docker stop debug
```

## <a name="next-steps"></a>次のステップ

VS Code Remote の設定が完了したので、VS Code からコンピューティング インスタンスをリモート コンピューティングとして使用して、自分のコードを対話形式でデバッグすることができます。 

トラブルシューティングについて学習します。

* [ローカルでのモデル デプロイ](how-to-troubleshoot-deployment-local.md)
* [リモートでのモデル デプロイ](how-to-troubleshoot-deployment.md)
* [Machine Learning パイプライン](how-to-debug-pipelines.md)
* [ParallelRunStep](how-to-debug-parallel-run-step.md)

