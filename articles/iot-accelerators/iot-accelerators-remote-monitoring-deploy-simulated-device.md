---
title: IoT によるカスタムのシミュレートされたデバイスの展開 - Azure | Microsoft Docs
description: このハウツー ガイドでは、カスタムのシミュレートされたデバイスをリモート監視ソリューション アクセラレータに展開する方法について説明します。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/15/2018
ms.topic: conceptual
ms.openlocfilehash: cdd2b24e6f833ee0acb229a37c7cd3b8b6f36339
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682051"
---
# <a name="deploy-a-new-simulated-device"></a>新しいシミュレートされたデバイスをデプロイする

リモート監視およびデバイス シミュレーション ソリューション アクセラレータは、いずれも独自のシミュレートされたデバイスを定義できます。 この記事では、カスタマイズされた chiller デバイスの種類と新しい lightbulb デバイスの種類をリモート監視ソリューション アクセラレータに展開する方法について説明します。

この記事の手順では、「[Create and test a new simulated device](iot-accelerators-remote-monitoring-create-simulated-device.md)」(新しいシミュレートされたデバイスの作成とテスト) ハウツー ガイドを完了し、カスタマイズされた chiller デバイスの種類と新しい lightbulb デバイスの種類を定義するファイルがあることを前提としています。

このハウツー ガイドの手順では、以下の方法を説明します。

1. SSH を使用して、リモート監視ソリューション アクセラレータをホストしている仮想マシンのファイル システムにアクセスします。

1. Docker コンテナーの外部からデバイス モデルを読み込むように Docker を構成します。

1. カスタム デバイス モデル ファイルを使用してリモート監視ソリューション アクセラレータを実行します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

このハウツー ガイドの手順を完了するには、アクティブな Azure サブスクリプションが必要です。

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドに従うには、次のものが必要です。

- [リモート監視ソリューション アクセラレータ](https://www.azureiotsolutions.com/Accelerators#solutions/types/RM2)のデプロイ済みインスタンス。
- `ssh` および `scp` コマンドを実行するローカルの **bash** シェル。 Windows の場合、[git](https://git-scm.com/download/win) をインストールするには、**bash** をインストールする方法が簡単です。
- カスタム デバイス モデル ファイル (「[Create and test a new simulated device](iot-accelerators-remote-monitoring-create-simulated-device.md)」(新しいシミュレーションされたデバイスの作成とテスト) で説明されているファイルなど)。

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="configure-docker"></a>Docker を構成する

このセクションでは、Docker コンテナー内からではなく、仮想マシンの **/tmp/devicemodels** フォルダーからデバイス モデル ファイルを読み込むように Docker を構成します。 このセクションのコマンドは、ローカル マシンの **bash** シェルで実行してください。

このセクションでは、Docker コンテナー内からではなく、仮想マシンの **/tmp/devicemodels** フォルダーからデバイス モデル ファイルを読み込むように Docker を構成します。 このセクションのコマンドは、ローカル マシンの **bash** シェルで実行してください。

1. SSH を使用して、ローカル マシンから Azure の仮想マシンに接続します。 次のコマンドでは、仮想マシン **vm-vikxv** のパブリック IP アドレスが **104.41.128.108** であることを前提としています。この値を前のセクションの仮想マシンのパブリック IP アドレスに置き換えます。

   ```sh
    ssh azureuser@104.41.128.108
    ```

    プロンプトに従い、前のセクションで設定したパスワードを使用して仮想マシンにサインインします。

1. コンテナーの外部からデバイス モデルを読み込むようにデバイス シミュレーション サービスを構成します。 まず Docker 構成ファイルを開きます。

    ```sh
    sudo nano /app/docker-compose.yml
    ```

    **devicesimulation** コンテナーの設定を探し、次のスニペットのように **volumes** の設定を編集します。

    ```yml
    devicesimulation:
      image: azureiotpcs/device-simulation-dotnet:1.0.0
      networks:
        - default_net
      depends_on:
        - storageadapter
      environment:
        - PCS_KEYVAULT_NAME
        - PCS_AAD_APPID
        - PCS_AAD_APPSECRET
      # How one could mount custom device models
      volumes:
        - /tmp/devicemodels:/app/webservice/data/devicemodels:ro
    ```

    変更を保存します。

1. 既存のデバイス モデル ファイルをコンテナーから新しい場所にコピーします。 まず、デバイス シミュレーション コンテナーのコンテナー ID を探します。

    ```sh
    docker ps
    ```

    次に、デバイス モデル ファイルを仮想マシンの **tmp** フォルダーにコピーします。 次のコマンドでは、コンテナー ID が c378d6878407 であることを前提としています。この値をデバイス シミュレーション コンテナー ID に置き換えます。

    ```sh
    docker cp c378d6878407:/app/webservice/data/devicemodels /tmp
    ```

    **[bash]** ウィンドウで SSH セッションを開いたままにしておきます。

1. カスタム デバイス モデル ファイルを仮想マシンにコピーします。 このコマンドは、カスタム デバイス モデルを作成したマシン上の別の **bash** シェルで実行します。 まず、デバイス モデルの JSON ファイルが保存されているローカル フォルダーに移動します。 次のコマンドでは、仮想マシンのパブリック IP アドレスが **104.41.128.108** であることを前提としています。この値を実際の仮想マシンのパブリック IP アドレスに置き換えます。 プロンプトが表示されたら、仮想マシンのパスワードを入力します。

    ```sh
    scp *json azureuser@104.41.128.108:/tmp/devicemodels
    cd scripts
    scp *js azureuser@104.41.128.108:/tmp/devicemodels/scripts
    ```

1. 新しいデバイス モデルを使用するためにデバイス シミュレーション Docker コンテナーを再起動します。 仮想マシンへの SSH セッションを開いた状態で、**bash** シェルで次のコマンドを実行します。

    ```sh
    sudo /app/start.sh
    ```

    実行中の Docker コンテナーの状態とそのコンテナー ID を確認するには、次のコマンドを使用します。

    ```sh
    docker ps
    ```

    デバイス シミュレーション コンテナーからログを表示するには、次のコマンドを実行します。 コンテナー ID を実際のデバイス シミュレーション コンテナーの ID に置き換えます。

    ```sh
    docker logs -f 5d3f3e78822e
    ```

## <a name="run-simulation"></a>シミュレーションを実行する

リモート監視ソリューションで、カスタム デバイス モデルを使用できるようになりました。

1. [Microsoft Azure IoT Solution Accelerators](https://www.azureiotsolutions.com/Accelerators#dashboard) からリモート監視ダッシュボードを起動します。

1. **[デバイス]** ページを使用して、シミュレートされたデバイスを追加します。 新しいシミュレートされたデバイスを追加するときに、新しいデバイス モデルを選択できます。

1. ダッシュボードを使用すると、デバイスのテレメトリを表示し、デバイス メソッドを呼び出すことができます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

さらに調べる場合は、リモート監視ソリューション アクセラレータをデプロイしたままにします。

ソリューション アクセラレータが不要になった場合は、[[プロビジョニングされたソリューション]](https://www.azureiotsolutions.com/Accelerators#dashboard) ページでそれを選択し、**[ソリューションの削除]** をクリックして削除してください。

## <a name="next-steps"></a>次の手順

このガイドでは、カスタム デバイス モデルをリモート監視ソリューション アクセラレータに展開する方法について説明しました。 次は、[実際のデバイスをご利用のリモート監視ソリューションに接続する](iot-accelerators-connecting-devices-node.md)方法について学習することをお勧めします。
