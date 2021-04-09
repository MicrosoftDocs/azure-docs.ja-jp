---
title: チュートリアル:環境を設定する - Azure IoT Edge での Machine Learning
description: チュートリアル:エッジでの機械学習用のモジュールの開発とデプロイに使用する環境を準備します。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/12/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 723d7b6ae5b96cf6dc622be1b1bb98065151787f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103463155"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>チュートリアル:IoT Edge 上に機械学習用の環境を設定する

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

この記事は、開発およびデプロイ用の環境の準備に役立ちます。 最初に、必要なすべてのツールがインストールされた開発用マシンを設定します。 次に、必要なクラウド リソースを Azure に作成します。

チュートリアルのこのセクションで学習する内容は次のとおりです。

> [!div class="checklist"]
>
> * 開発用の仮想マシンを設定する。
> * 開発環境で使用する IoT Hub とクラウド ストレージを設定する。

## <a name="prerequisites"></a>前提条件

この記事は、IoT Edge 上で Azure Machine Learning を使用するためのチュートリアルのシリーズの一部です。 シリーズの各記事は、前の記事の作業に基づいています。 この記事に直接アクセスしている場合は、シリーズの[最初の記事](tutorial-machine-learning-edge-01-intro.md)を参照してください。

## <a name="set-up-the-development-vm"></a>開発用 VM を設定する

この手順は、通常はクラウド開発者によって実行されます。 一部のソフトウェアはデータ サイエンティストの役にも立ちます。

前提条件の多くが既に構成済みになっている Azure 仮想マシンを作成するための PowerShell スクリプトを作成しました。 作成する VM では、[入れ子になった仮想化](../virtual-machines/windows/nested-virtualization.md)を処理できる必要があります。このため、マシン サイズとして [Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md) が選択されています。

開発用 VM の設定には次のものが必要です。

* Windows 10
* [Chocolatey](https://chocolatey.org/)
* [Docker Desktop for Windows](https://www.docker.com/products/docker-desktop)
* [Git for Windows](https://gitforwindows.org/)
* [Git Credential Manager for Windows](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)
* [.NET Core SDK](https://dotnet.microsoft.com/)
* [Python 3](https://www.python.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure PowerShell](/powershell/azure/)
* [VS Code 拡張機能](https://marketplace.visualstudio.com/search?target=VSCode)
  * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

開発者用の VM は必ずしも必要ではありません。開発ツールはすべてローカル マシン上で実行できます。 ただし、作業環境を一様にするために、VM を使用することを強くお勧めします。

仮想マシンの作成と構成には約 30 分かかります。

1. [Machine Learning と IoT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample) のサンプル リポジトリを自分のローカル コンピューターにクローンまたはダウンロードします。

1. 管理者として PowerShell を開き、コードのダウンロード先のルート ディレクトリにある **\IoTEdgeAndMlSample\DevVM** ディレクトリに移動します。 ソースのルート ディレクトリは、`srcdir` と呼びます。

    ```powershell
    cd c:\srcdir\IoTEdgeAndMlSample\DevVM
    ```

   DevVM ディレクトリには、このチュートリアルを完了するための適切な Azure 仮想マシンの作成に必要なファイルが含まれています。

1. 次のコマンドを実行してスクリプトの実行を許可します。 確認を求められたら、**[すべてはい]** を選択します。

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. Create-AzureDevVM.ps1 を実行します。

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    入力を求められたら、次の情報を指定します。

    * **Azure サブスクリプション ID**:ご利用のサブスクリプション ID です。ポータルの [[Azure サブスクリプション]](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) で確認できます。
    * **リソース グループ名**: Azure 内にある新規または既存のリソース グループの名前。
    * **[場所]** :仮想マシンの作成先となる Azure の場所を選択します。 たとえば、"米国西部 2" や "北ヨーロッパ" があります。 詳しくは、「[Azure の場所](https://azure.microsoft.com/global-infrastructure/locations/)」を参照してください。
    * **[ユーザー名]** : VM の管理者アカウントに覚えやすい名前を付けます。
    * **パスワード**:VM の管理者アカウントのパスワードを設定します。

   スクリプトが数分間実行され、その間に次のステップが実行されます。

    1. [Azure PowerShell Az モジュール](/powershell/azure/new-azureps-module-az)をインストールします。
    1. Azure にサインインするよう求められます。
    1. VM を作成するための情報を確認します。 **y** キーまたは **Enter** キーを押して続行します。
    1. リソース グループがまだ作成されていない場合は、作成します。
    1. 仮想マシンをデプロイします。
    1. VM 上で Hyper-V を有効にします。
    1. 開発に必要なソフトウェアをインストールし、サンプル リポジトリをクローンします。
    1. VM を再起動します。
    1. VM に接続するための RDP ファイルをデスクトップ上に作成します。

   VM を再起動する際にその名前を入力するよう求められた場合は、スクリプトの出力から名前をコピーできます。 VM 接続用の RDP ファイルのパスも出力で確認できます。

### <a name="set-auto-shutdown-schedule"></a>自動シャットダウンのスケジュールを設定する

コスト削減に役立つように、開発用 VM は自動シャットダウンのスケジュールが 1900 PST に設定された状態で作成されています。 この設定は、ご自分の場所とスケジュールに応じて更新が必要な場合があります。 シャットダウンのスケジュールを更新するには、次の手順を実施します。

1. Azure portal で、スクリプトによって作成された VM に移動します。

1. 左ペインのメニューの **[操作]** で **[自動シャットダウン]** を選択します。

1. 必要に応じて **[スケジュールされたシャットダウン]** と **[タイム ゾーン]** を調整し、**[保存]** を選択します。

## <a name="connect-to-the-development-vm"></a>開発用 VM に接続する

これで VM を作成できたので、次はチュートリアルを完了するうえで必要なソフトウェアをインストールし終える必要があります。

1. スクリプトによってデスクトップ上に作成された RDP ファイルをダブルクリックします。

1. リモート接続の発行元が不明であることを知らせるダイアログが表示されます。 これは問題ないので、**[接続]** を選択します。

1. VM の作成時に指定した管理者のパスワードを入力し、**[OK]** をクリックします。

1. VM の証明書を受け入れるように求められます。 **[はい]** を選択します。

## <a name="install-visual-studio-code-extensions"></a>Visual Studio Code 拡張機能をインストールする

これで開発用マシンへの接続が完了したので、Visual Studio Code にいくつかの便利な拡張機能を追加して、よりスムーズな開発エクスペリエンスを実現しましょう。

1. 開発用 VM に接続して PowerShell ウィンドウを開き、**C:\source\IoTEdgeAndMlSample\DevVM** ディレクトリに移動します。 このディレクトリは、VM を作成したスクリプトによって作成されたものです。

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\DevVM
    ```

1. 次のコマンドを実行してスクリプトの実行を許可します。 確認を求められたら、**[すべてはい]** を選択します。

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. Visual Studio Code 拡張機能スクリプトを実行します。

    ```powershell
    .\Enable-CodeExtensions.ps1
    ```

1. スクリプトが数分間実行され、VS code 拡張機能がインストールされます。

    * Azure IoT Tools
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>IoT Hub と Storage を設定する

これらの手順は、通常はクラウド開発者によって実行されます。

Azure IoT Hub は、IoT デバイスとクラウドとの間の安全な通信を処理する、あらゆる IoT アプリケーションの核です。 これは、IoT Edge の機械学習ソリューションを運用する際の主要な調整ポイントです。

* IoT Hub は、ルートを使用して、IoT デバイスからの受信データを他のダウンストリーム サービスに転送します。 ここでは、IoT Hub のルートを利用して、デバイス データを Azure Storage に送信します。 Azure Storage にあるデバイス データは、残存耐用年数 (RUL) 分類器をトレーニングするために Azure Machine Learning によって使用されます。

* この後のチュートリアルで、IoT Hub を使用して Azure IoT Edge デバイスを構成および管理します。

このセクションでは、スクリプトを使用して Azure IoT ハブと Azure Storage アカウントを作成します。 次に、ハブで受信したデータを Azure Storage コンテナーに転送するルートを Azure portal で構成します。 これらの手順は、完了するまでに約 10 分かかります。

1. 開発用 VM に接続して PowerShell ウィンドウを開き、**IoTHub** ディレクトリに移動します。

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. 作成スクリプトを実行します。 サブスクリプション ID、場所、リソース グループには、開発用 VM を作成したときと同じ値を使用します。

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location <location> -ResourceGroupName <resource group>
    ```

    * Azure へのサインインを要求されます。
    * スクリプトにより、ハブと Storage アカウントを作成するための情報が確認されます。 **y** キーまたは **Enter** キーを押して続行します。

このスクリプトは、実行するのに約 2 分かかります。 完了すると、スクリプトによって、IoT ハブとストレージ アカウントの名前が出力されます。

## <a name="review-route-to-storage-in-iot-hub"></a>IoT Hub 内での Storage へのルートを確認する

前のセクションで実行したスクリプトでは、IoT ハブの作成処理の一環として、カスタム エンドポイントとルートも作成されています。 IoT Hub ルートは、クエリ式とエンドポイントで構成されています。 メッセージが式と一致すると、データはルートに沿って、関連付けられているエンドポイントに送信されます。 エンドポイントにできるのは、イベント ハブ、Service Bus キュー、トピックです。 この場合は、ストレージ アカウント内の BLOB コンテナーがエンドポイントです。 Azure portal を使用して、スクリプトによって作成されたルートを確認してみましょう。

1. [Azure portal](https://portal.azure.com) を開いて、このチュートリアルで使用しているリソース グループに移動します。

1. リソースの一覧で、スクリプトによって作成された IoT ハブを選択します。 末尾にランダムな文字が付いた名前になります (例: `IotEdgeAndMlHub-jrujej6de6i7w`)。

1. 左ペインのメニューの **[メッセージング]** にある **[メッセージ ルーティング]** を選択します。

1. **[メッセージ ルーティング]** ページで **[カスタム エンドポイント]** タブを選択します。

1. **[ストレージ]** セクションを展開します。

   ![カスタム エンドポイントの一覧に turbofanDeviceStorage があることを確認する](media/tutorial-machine-learning-edge-02-prepare-environment/custom-endpoints.png)

   カスタム エンドポイントの一覧に **turbofanDeviceStorage** があることを確認します。 このエンドポイントについて、次の特性に注目してください。

   * **[コンテナー名]** に表示されている `devicedata` という名前で作成した Blob Storage コンテナーを参照します。
   * **[ファイル名の形式]** に、名前の最後の要素として partition が含まれています。 この形式は、後のチュートリアルで行う Azure Notebooks でのファイル操作の際、さらに便利です。
   * **[状態]** が正常である必要があります。

1. **[ルート]** タブを選択します。

1. **turbofanDeviceDataToStorage** という名前のルートを選択します。

1. **[Routes details]\(ルートの詳細\)** ページで、ルートのエンドポイントが **turbofanDeviceStorage** エンドポイントであることに注目してください。

   ![turbofanDeviceDataToStorage ルートの詳細を確認する](media/tutorial-machine-learning-edge-02-prepare-environment/route-details.png)

1. **ルーティング クエリ** を見ると、**true** に設定されています。 この設定は、すべてのデバイス テレメトリ メッセージがこのルートに一致しており、そのためすべてのメッセージが **turbofanDeviceStorage** エンドポイントに送信されることを意味します。

1. このページは編集していないので、そのまま閉じます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルはセットの一部であり、各記事は前の記事の作業が行われたことが前提になっています。 最後のチュートリアルを完了するまで、リソースのクリーンアップはしないでください。

## <a name="next-steps"></a>次のステップ

この記事では、IoT ハブを作成し、Azure Storage アカウントへのルートを構成しました。 次に、シミュレートされたデバイスのセットから IoT ハブを通じてストレージ アカウントにデータを送信します。 後のチュートリアルでは、IoT Edge デバイスおよびモジュールを構成した後、ルートを再確認し、ルーティング クエリについてもう少し詳しく確認します。

次の記事に進んで、監視対象にするシミュレートされたデバイスを作成してください。

> [!div class="nextstepaction"]
> [デバイス データを生成する](tutorial-machine-learning-edge-03-generate-data.md)
