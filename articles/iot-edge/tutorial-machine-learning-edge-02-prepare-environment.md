---
title: 環境を設定する - Azure IoT Edge での Machine Learning | Microsoft Docs
description: エッジでの機械学習用のモジュールの開発とデプロイに使用する環境を準備します。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: fd3b5766ec2bd8d1babf847598f1fbe5b6511ce7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67432845"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>チュートリアル:IoT Edge 上に機械学習用の環境を設定する

> [!NOTE]
> この記事は、IoT Edge 上での Azure Machine Learning の使用に関するチュートリアルのシリーズの一部です。 この記事に直接アクセスしている場合は、効果を最大限に高めるために、シリーズの[最初の記事](tutorial-machine-learning-edge-01-intro.md)から始めることをお勧めします。

IoT Edge におけるエンド ツー エンドの Azure Machine Learning に関するチュートリアルに属するこの記事は、開発およびデプロイ用の環境を準備するのに役立ちます。 最初に、必要なすべてのツールがインストールされた開発用マシンを設定します。 次に、必要なクラウド リソースを Azure に作成します。

## <a name="set-up-a-development-machine"></a>開発用マシンを設定する

この手順は、通常はクラウド開発者によって実行されます。 一部のソフトウェアはデータ サイエンティストの役にも立ちます。

この記事では、IoT Edge モジュールと IoT デバイスのコーディング、コンパイル、構成、デプロイなど、さまざまな開発者のタスクを実行します。 使いやすくするために、前提条件の多くが既に構成済みになっている Azure 仮想マシンを作成するための PowerShell スクリプトを作成しました。 作成する VM では、[入れ子になった仮想化](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization)を処理できる必要があります。このため、マシン サイズとして DS8V3 が選択されています。

開発用 VM の設定には次のものが必要です。

* Windows 10
* [Chocolatey](https://chocolatey.org/)
* [Docker Desktop for Windows](https://www.docker.com/products/docker-desktop)
* [Git for Windows](https://gitforwindows.org/)
* [Git Credential Manager for Windows](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)
* [.Net Core SDK](https://dotnet.microsoft.com/)
* [Python 3](https://www.python.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.1.0)
* [VS Code 拡張機能](https://marketplace.visualstudio.com/search?target=VSCode)
  * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

開発者用の VM は必ずしも必要ではありません。開発ツールはすべてローカル マシン上で実行できます。 ただし、作業環境を一様にするために、VM を使用することを強くお勧めします。

仮想マシンの作成と構成には約 30 分かかります。

### <a name="get-the-script"></a>スクリプトを入手する

PowerShell スクリプトを [Machine Learning と IoT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample) のサンプル リポジトリから複製またはダウンロードします。

### <a name="create-an-azure-virtual-machine"></a>Azure 仮想マシンを作成する

DevVM ディレクトリには、このチュートリアルを完了するための適切な Azure 仮想マシンの作成に必要なファイルが含まれています。

1. 管理者として PowerShell を開き、コードをダウンロードしたディレクトリに移動します。 ソースのルート ディレクトリは、`<srcdir>` と呼びます。

    ```powershell
    cd <srcdir>\IoTEdgeAndMlSample\DevVM
    ```

2. 次のコマンドを実行してスクリプトの実行を許可します。 確認を求められたら、 **[すべてはい]** を選択します。

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

3. このディレクトリから Create-AzureDevVM.ps1 を実行します。

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    * 入力を求められたら、次の情報を指定します。
      * **Azure サブスクリプション ID**:サブスクリプション ID。Azure portal で確認できます
      * **リソース グループ名**: Azure 内にある新規または既存のリソース グループの名前
      * **[場所]** :仮想マシンの作成先となる Azure の場所を選択します。 westus2 や northeurope などです。 詳しくは、「[Azure の場所](https://azure.microsoft.com/global-infrastructure/locations/)」を参照してください。
      * **AdminUsername**: 仮想マシン上で作成して使用する管理者アカウントの覚えやすい名前を指定します。
      * **AdminPassword**: 仮想マシン上の管理者アカウント用のパスワードを設定します。

    * Azure PowerShell がインストールされていない場合、スクリプトによって [Azure PowerShell Az モジュール](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.1.0)がインストールされます。

    * Azure へのサインインを要求されます。

    * スクリプトにより、VM を作成するための情報が確認されます。 `y` キーまたは `Enter` キーを押して続行します。

スクリプトが数分間実行され、その間に次のステップが実行されます。

* リソース グループがまだ作成されていない場合は、作成する
* 仮想マシンをデプロイする
* VM 上で Hyper-V を有効にする
* 開発に必要なソフトウェアをインストールし、サンプル リポジトリを複製する
* VM を再起動する
* デスクトップ上に、VM に接続するための RDP ファイルを作成する

### <a name="set-auto-shutdown-schedule"></a>自動シャットダウンのスケジュールを設定する

コスト削減に役立つように、VM は自動シャットダウンのスケジュールが 1900 PST に設定された状態で作成されています。 このタイミングは、ご自分の場所やスケジュールに応じて更新しなければならない場合もあります。 シャットダウンのスケジュールを更新するには、次の手順を実施します。

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. 前のセクションで指定したリソース グループ内の仮想マシンに移動します。

3. 左側のナビゲーターにある **[自動シャットダウン]** を選択します。

4. 新しいシャットダウン時刻を **[スケジュールされたシャットダウン]** に入力するか、 **[タイム ゾーン]** を変更してから、 **[保存]** をクリックします。

### <a name="connect-and-configure-development-machine"></a>開発用マシンを接続し、構成する

これで VM を作成できたので、次はチュートリアルを完了するうえで必要なソフトウェアをインストールし終える必要があります。

#### <a name="start-a-remote-desktop-session"></a>リモート デスクトップ セッションを開始する

1. VM 作成スクリプトによって、デスクトップ上に RDP ファイルが作成されています。

2. **\<Azure VM name\>.rdp** という名前のファイルをダブルクリックします。

3. リモート接続の発行元が不明であることを知らせるダイアログが表示されます。 **[Don’t ask me again for connections to this computer]\(このコンピューターへの接続について今後確認しない\)** チェック ボックスをオンにして、 **[接続]** を選択します。

4. 入力を求められたら、VM の設定スクリプトの実行時に使用した AdminPassword を入力して、 **[OK]** をクリックします。

5. VM の証明書を受け入れるように求められます。 **[Don’t ask me again for connections to this computer]\(このコンピューターへの接続について今後確認しない\)** を選択して、 **[はい]** を選択します。

#### <a name="install-visual-studio-code-extensions"></a>Visual Studio Code 拡張機能をインストールする

これで開発用マシンへの接続が完了したので、Visual Studio Code にいくつかの便利な拡張機能を追加して、よりスムーズな開発エクスペリエンスを実現しましょう。

1. PowerShell ウィンドウで、**C:\\source\\IoTEdgeAndMlSample\\DevVM** に移動します。

2. 入力による仮想マシン上でのスクリプトの実行を許可します。

    ```powershell
    Set-ExecutionPolicy Bypass -Scope CurrentUser -Force
    ```

3. スクリプトを実行します。

    ```powershell
    .\Enable-CodeExtensions.ps1
    ```

4. スクリプトが数分間実行され、VS code 拡張機能がインストールされます。

    * Azure IoT Tools
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>IoT Hub と Storage を設定する

これらの手順は、通常はクラウド開発者によって実行されます。

Azure IoT Hub は、あらゆる IoT アプリケーションの中核です。 IoT デバイスとクラウドの間で、セキュリティで保護された通信を処理します。 これは、IoT Edge の機械学習ソリューションを運用する際の主要な調整ポイントです。

* IoT Hub は、ルートを使用して、IoT デバイスからの受信データを他のダウンストリーム サービスに転送します。 IoT Hub のルートを利用して、デバイス データを Azure Storage に送信します。そこでは、データは Azure Machine Learning によって残存耐用年数 (RUL) 分類器のトレーニングに使用されます。

* この後のチュートリアルで、IoT Hub を使用して Azure IoT Edge デバイスを構成および管理します。

このセクションでは、スクリプトを使用して Azure IoT ハブと Azure Storage アカウントを作成します。 次に、Azure portal を使用して、ハブで受信したデータを Azure Storage Blob コンテナーに転送するルートを構成します。 これらの手順は、完了するまでに約 10 分かかります。

### <a name="create-cloud-resources"></a>クラウド リソースを作成する

1. 開発用マシン上で、PowerShell ウィンドウを開きます。

1. IoTHub ディレクトリに変更します。

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. 作成スクリプトを実行します。 サブスクリプション ID、場所、リソース グループには、開発用 VM を作成したときと同じ値を使用します。

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location
    <location> -ResourceGroupName <resource group>
    ```

    * Azure へのサインインを要求されます。
    * スクリプトにより、ハブと Storage アカウントを作成するための情報が確認されます。 `y` キーまたは `Enter` キーを押して続行します。

このスクリプトは、実行するのに約 2 分かかります。 完了すると、スクリプトによって、ハブと Storage アカウントの名前が出力されます。

### <a name="review-route-to-storage-in-iot-hub"></a>IoT Hub 内での Storage へのルートを確認する

前のセクションで実行したスクリプトでは、IoT ハブの作成処理の一環として、カスタム エンドポイントとルートも作成されています。 IoT Hub ルートは、クエリ式とエンドポイントで構成されています。 メッセージが式と一致すると、データはルートに沿って、関連付けられているエンドポイントに送信されます。 エンドポイントにできるのは、イベント ハブ、Service Bus キュー、トピックです。 この場合は、Storage アカウント内の BLOB コンテナーがエンドポイントです。 Azure portal を使用して、スクリプトで作成されたルートを確認してみましょう。

1. [Azure Portal](https://portal.azure.com)を開きます。

1. 左側のナビゲーターからすべてのサービスを選択し、検索ボックスに「IoT」と入力して、 **[IoT Hub]** を選択します。

1. 前の手順で作成した IoT ハブを選択します。

1. IoT ハブ側のナビゲーターで、 **[メッセージ ルーティング]** を選択します。

1. [メッセージ ルーティング] ページには、 **[ルート]** と **[カスタム エンドポイント]** という 2 つのタブがあります。 **[カスタム エンドポイント]** タブを選択します。

1. **[Blob ストレージ]** で **[turbofanDeviceStorage]** を選択します。

1. このエンドポイントは、前回の手順で作成した、Storage アカウント内の **devicedata** と呼ばれる BLOB コンテナーを指しており、名前が **iotedgeandml\<unique suffix\>** であることに注意してください。

1. また、**BLOB ファイル名の形式**が既定の形式から変更されていることにも注意してください。その代わりに、この部分は名前の最後の要素として配置されています。 この形式は、後のチュートリアルで行う Azure Notebooks でのファイル操作の際、さらに便利です。

1. [エンドポイントの詳細] ブレードを閉じて、 **[メッセージ ルーティング]** ページに戻ります。

1. **[ルート]** タブを選択します。

1. **turbofanDeviceDataToStorage** という名前のルートを選択します。

1. そのルートのエンドポイントは **turbofanDeviceStorage** カスタム エンドポイントであることに注意してください。

1. **true** に設定されたルーティング クエリを確認します。 これは、すべてのデバイス テレメトリ メッセージがこのルートに一致しており、そのためにすべてのメッセージが **turbofanDeviceStorage** エンドポイントに送信されることを意味します。

1. [ルートの詳細] を閉じます。

## <a name="next-steps"></a>次の手順

この記事では、IoT ハブを作成し、Azure Storage アカウントへのルートを構成しました。 次の記事では、シミュレートされたデバイスのセットから送信されたデータを、その IoT ハブを通じて Storage アカウントに送信します。 後のチュートリアルでは、IoT Edge デバイスおよびモジュールを構成した後、ルートを再確認し、ルーティング クエリについてもう少し詳しく確認します。

IoT Edge での Machine Learning チュートリアルのこの部分で説明されているステップの詳細については、次を参照してください。

* [Azure IoT の基礎](https://docs.microsoft.com/azure/iot-fundamentals/)
* [IoT Hub を使用してメッセージ ルーティングを構成する](../iot-hub/tutorial-routing.md)
* [Azure portal を使用して IoT ハブを作成する](../iot-hub/iot-hub-create-through-portal.md)

次の記事に進んで、監視対象にするシミュレートされたデバイスを作成してください。

> [!div class="nextstepaction"]
> [デバイス データを生成する](tutorial-machine-learning-edge-03-generate-data.md)
