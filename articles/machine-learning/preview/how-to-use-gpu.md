---
title: "Azure Machine Learning で GPU を使用する方法 | Microsoft Docs"
description: "この記事では、Azure Machine Learning Workbench でグラフィカル処理ユニット (GPU) を使用して深層ニューラル ネットワークをトレーニングする方法について説明します。"
services: machine-learning
author: rastala
ms.author: roastala
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 7c0dab5ab2d7342ce4de8899040478a6143c184b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-gpu-in-azure-machine-learning"></a>Azure Machine Learning で GPU を使用する方法
グラフィカル処理ユニット (GPU) は、特定の深層ニューラル ネットワーク モデルをトレーニングするときに通常発生する負荷の大きいタスクの処理に広く使用されます。 GPU の使用により、モデルのトレーニング時間を大幅に削減できます。 このドキュメントでは、GPU を搭載した [DSVM (データ サイエンス仮想マシン)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) を実行ターゲットとして使用するように Azure ML Workbench を構成する方法を説明します。 

## <a name="prerequisites"></a>前提条件
- このハウツー ガイドの手順を実行するには、最初に [Azure ML Workbench をインストールする](quickstart-installation.md)必要があります。
- NVidia GPU を搭載したコンピューターにアクセスできる必要があります。
    - GPU を搭載したローカル コンピューター (Windows または macOS) でスクリプトを直接実行できる必要があります。
    - また、GPU を搭載したコンピューター上の Docker コンテナーでスクリプトを実行できる必要があります。

## <a name="execute-in-local-environment-with-gpus"></a>GPU を搭載した "_ローカル_" 環境での実行
GPU を搭載したコンピューターに Azure ML Workbench をインストールして、"_ローカル_" 環境に対して実行できます。 これは以下のマシンで行えます。
- Windows または macOS の物理コンピューター。
- Azure NC シリーズの VM テンプレートを使用してプロビジョニングされた Windows DSVM などの、Windows VM (仮想マシン)。

この場合、Azure ML Workbench では特別な構成は必要ありません。 すべての必要なドライバー、ツールキット、および GPU に対応した Machine Learning ライブラリがローカルにインストールされていることのみ確認してください。 Python ランタイムがローカルの GPU ハードウェアに直接アクセスできる "_ローカル_" 環境に対して実行します。

1. AML Workbench を開きます。 **[ファイル]** に移動し、**[コマンド プロンプトを開く]** を選択します。 
2. コマンド ラインから、GPU に対応した深層学習フレームワーク (Microsoft Cognitive Toolkit、TensorFlow など) をインストールします。For example:

```batch
REM install latest TensorFlow with GPU support
C:\MyProj> pip install tensorflow-gpu

REM install Microsoft Cognitive Toolkit 2.1 (1-bit SGD) with GPU support on Windows
C:\MyProj> pip install https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.1-cp35-cp35m-win_amd64.whl
```

3. 深層学習ライブラリを利用する Python コードを記述します。
4. コンピューティング環境として _local_ を選択し、Python コードを実行します。

```batch
REM execute Python script in local environment
C:\MyProj> az ml experiment submit -c local my-deep-learning-script.py
```

## <a name="execute-in-docker-environment-on-linux-vm-with-gpus"></a>GPU を搭載した Linux VM 上の _Docker_ 環境での実行
Azure ML Workbench は、Azure Linux VM 上の Docker での実行もサポートしています。 その場合、負荷の高いジョブを強力な仮想ハードウェアで実行し、完了後にハードウェアをオフにすることで使用量に対してのみ料金を支払うという優れた方法があります。 基本的に、GPU はどの Linux 仮想マシンでも使用できますが、Ubuntu ベースの DSVM には必要な CUDA ドライバーとライブラリが事前にインストールされているため、セットアップが非常に簡単です。 次の手順に従ってください。

### <a name="create-a-ubuntu-based-linux-data-science-virtual-machine-in-azure"></a>Azure で Ubuntu ベースの Linux データ サイエンス仮想マシンを作成する
1. Web ブラウザーを開き、[Azure Portal](https://portal.azure.com) に移動します。

2. ポータルの左側で**+ 新規**を選択します。

3. マーケットプレースで「Linux (Ubuntu) データ サイエンス仮想マシン」を検索します。

4. **[作成]** をクリックして Ubuntu DSVM を作成します。

5. **[基本]** フォームに必要な情報を入力します。
VM の場所を選択する際には、GPU VM が一部の Azure リージョン (**[米国中南部]** など) でのみ利用可能であることに注意してください。 [リージョン別の利用可能なコンピューティング製品](https://azure.microsoft.com/en-us/regions/services/)に関するページをご覧ください。
[OK] をクリックして **[基本]** の情報を保存します。

6. 仮想マシンのサイズを選択します。 NC というプレフィックスの付いた VM (NVidia GPU チップ搭載の VM) のサイズから選択してください。  必要な場合は、**[すべて表示]** をクリックすると完全な一覧が表示されます。 詳細については、[GPU 搭載の Azure VM](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes-gpu) に関する記事をご覧ください。

7. 残りの設定を完了し、購入情報を確認します。 [購入] をクリックして VM を作成します。 この仮想マシンに割り当てられた IP アドレスを書き留めます。 

### <a name="create-a-new-project-in-azure-ml-workbench"></a>Azure ML Workbench で新しいプロジェクトを作成する 
_TensorFlow を使用して MNIST を分類する_例、または _CNTK を使用して MNIST データセットを分類する_例を使用できます。

### <a name="create-a-new-compute-target"></a>新しい計算ターゲットを作成する
Azure ML Workbench からコマンド ラインを起動します。 次のコマンドを入力します。 下の例に含まれるプレースホルダー テキストは、実際の名前、IP アドレス、ユーザー名、およびパスワードの値に置き換えてください。 

```batch
C:\MyProj> az ml computetarget attach --name "my_dsvm" --address "my_dsvm_ip_address" --username "my_name" --password "my_password" --type remotedocker
```

### <a name="configure-azure-ml-workbench-to-access-gpu"></a>GPU にアクセスするように Azure ML Workbench を構成する
プロジェクトに戻って **[ファイル ビュー]** を開き、**[最新の情報に更新]** ボタンをクリックします。 `my_dsvm.compute` と `my_dsvm.runconfig` という 2 つの新しい構成ファイルが表示されます。
 
`my_dsvm.compute` を開きます。 `baseDockerImage` を `microsoft/mmlspark:plus-gpu-0.7.9` に変更し、新しい行 `nvidiaDocker: true` を追加します。 ファイルには次の 2 行が含まれることになります。
 
```yaml
...
baseDockerImage: microsoft/mmlspark:plus-gpu-0.7.91
nvidiaDocker: true
```
 
次に、`my_dsvm.runconfig` を開き、`Framework` の値を `PySpark` から `Python` に変更します。 既定の値は `PySpark` なので、この行が表示されない場合は追加します。

```yaml
"Framework": "Python"
```
### <a name="reference-deep-learning-framework"></a>深層学習フレームワークを参照する 
`conda_dependencies.yml` ファイルを開いて、GPU バージョンの 深層学習フレームワーク Python パッケージを使用していることを確認します。 サンプル プロジェクトでは CPU バージョンが一覧表示されるため、この変更を行う必要があります。

TensorFlow の例: 
```
name: project_environment
dependencies:
  - python=3.5.2
  # latest TensorFlow library with GPU support
  - tensorflow-gpu
```

Microsoft Cognitive Toolkit の例:
```yaml
name: project_environment
dependencies:
  - python=3.5.2
  - pip: 
    # use the Linux build of Microsoft Cognitive Toolkit 2.1 with GPU support
    - https://cntk.ai/PythonWheel/GPU/cntk-2.1-cp35-cp35m-win_amd64.whl
```

マルチ GPU の VM のパフォーマンスを向上する、1 ビット SGD バージョンの Microsoft Cognitive Toolkit を使用することもできます。 [1 ビット SGD のライセンス要件](https://docs.microsoft.com/en-us/cognitive-toolkit/cntk-1bit-sgd-license) に関する記事をご覧ください。

```yaml
name: project_environment
dependencies:
  - python=3.5.2
  - pip:    
    # use the Linux build of the Microsoft Cognitive Toolkit 2.1 with 1-bit SGD and GPU support
    - https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.1-cp35-cp35m-linux_x86_64.whl
```

### <a name="execute"></a>Execute
これで、Python スクリプトを実行する準備が整いました。 `my_dsvm` コンテキストを使用して Azure ML Workbench 内でスクリプトを実行することも、コマンド ラインからスクリプトを起動することもできます。
 
```batch
C:\myProj> az ml experiment submit -c my_dsvm my_tensorflow_or_cntk_script.py
```
 
GPU が使用されていることを確認するには、実行の出力を調べて次のような行があるかどうかを確認します。

```
name: Tesla K80
major: 3 minor: 7 memoryClockRate (GHz) 0.8235
pciBusID 06c3:00:00.0
Total memory: 11.17GiB
Free memory: 11.11GiB
```

ご利用ありがとうございます。 スクリプトで、Docker コンテナーを通じて GPU の機能が利用されています。

## <a name="next-steps"></a>次のステップ
Azure ML Gallery.で、GPU を使用して深層ニューラル ネットワークのトレーニングを高速化する例を参照します。
