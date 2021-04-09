---
title: 'チュートリアル: Azure IoT Edge デバイスを構成する - IoT Edge での Machine Learning'
description: このチュートリアルでは、Linux を実行している Azure 仮想マシンを、透過的なゲートウェイとして機能する Azure IoT Edge デバイスとして構成します。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 2/5/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: amqp, devx-track-azurecli
ms.openlocfilehash: b59f8343c9dff07a32accd471f70ddf9f5309b8d
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/14/2021
ms.locfileid: "103463087"
---
# <a name="tutorial-configure-an-azure-iot-edge-device"></a>チュートリアル: Azure IoT Edge デバイスを構成する

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

この記事では、Linux を実行している Azure 仮想マシンを、透過的なゲートウェイとして機能する Azure IoT Edge デバイスになるように構成します。 透過的なゲートウェイの構成では、デバイスはゲートウェイの存在を知らなくても、ゲートウェイを介して Azure IoT Hub に接続できます。 同時に、IoT Hub 内のデバイスを操作するユーザーは中間のゲートウェイ デバイスを意識しません。 最終的には、この透過的なゲートウェイに IoT Edge モジュールを追加することによって、エッジ分析をシステムに追加します。

>[!NOTE]
>このチュートリアルの概念は IoT Edge のすべてのバージョンに適用されますが、シナリオを試すために作成したサンプル デバイスでは、IoT Edge バージョン 1.1 が実行されます。

この記事の手順は通常、クラウド開発者によって実行されます。

チュートリアルのこのセクションで学習する内容は次のとおりです。

> [!div class="checklist"]
>
> * ゲートウェイ デバイスがダウンストリーム デバイスに安全に接続できるようにするための証明書を作成する。
> * IoT Edge デバイスを作成する。
> * IoT Edge デバイスをシミュレートする Azure 仮想マシンを作成する。

## <a name="prerequisites"></a>前提条件

この記事は、IoT Edge 上で Azure Machine Learning を使用するためのチュートリアルのシリーズの一部です。 シリーズの各記事は、前の記事の作業に基づいています。 この記事に直接アクセスしている場合は、シリーズの[最初の記事](tutorial-machine-learning-edge-01-intro.md)を参照してください。

## <a name="create-certificates"></a>証明書の作成

デバイスがゲートウェイとして機能するためには、ダウンストリーム デバイスに安全に接続できる必要があります。 IoT Edge では、公開キー基盤 (PKI) を使用して、デバイス間にセキュリティで保護された接続を設定することができます。 この場合、透過的なゲートウェイとして機能する IoT Edge デバイスにダウンストリームの IoT デバイスが接続できるようにします。 妥当なセキュリティを維持するには、ダウン ストリーム デバイスで IoT Edge デバイスの ID を確認してください。 IoT Edge デバイスで証明書を使用するしくみの詳細については、[Azure IoT Edge 証明書の使用の詳細](iot-edge-certs.md)に関するページを参照してください。

このセクションでは、Docker イメージを使用して自己署名証明書を作成した後、そのイメージをビルドして実行します。 Docker イメージを使用してこの手順を完了することを選択した理由は、それによって、Windows 開発用マシンで証明書を作成するために必要な手順の数が削減されるからです。 Docker イメージを使用して自動化した内容については、「[IoT Edge デバイスの機能をテストするためのデモ用の証明書を作成する](how-to-create-test-certificates.md)」を参照してください。

1. 開発用の VM にサインインします。
1. **c:\edgeCertificates** というフォルダー名とパスを使用して、新しいフォルダーを作成します。

1. **Docker for Windows** をまだ実行していない場合は、Windows の [スタート] メニューから起動します。

1. Visual Studio Code を開きます。

1. **[ファイル]**  >  **[フォルダーを開く]** を選択し、**C:\\source\\IoTEdgeAndMlSample\\CreateCertificates** を選択します。

1. **[エクスプローラー]** ペインで **[dockerfile]** を右クリックし、 **[イメージのビルド]** を選択します。

1. ダイアログ ボックスで、イメージ名とタグは既定値 **createcertificates:latest** のままにします。

    ![Visual Studio Code で証明書を作成する方法を示すスクリーンショット。](media/tutorial-machine-learning-edge-05-configure-edge-device/create-certificates.png)

1. ビルドが完了するまで待ちます。

    > [!NOTE]
    > 公開キーが見つからないという警告が表示される場合があります。 この警告は無視しても問題ありません。 同様に、イメージに対するアクセス許可を確認またはリセットすることを推奨するセキュリティ警告が表示されますが、このイメージに関しては無視しても問題ありません。

1. Visual Studio Code ターミナル ウィンドウで、createcertificates コンテナーを実行します。

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

1. Docker が **c:\\** ドライブへのアクセスを求めます。 **[Share it]\(共有する\)** を選択します。

1. メッセージが表示されたら、資格情報を入力します。

1. コンテナーの実行が終了したら、**c:\\edgeCertificates** で、次のファイルを確認します。

    * c:\\edgeCertificates\\certs\\azure-iot-test-only.root.ca.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device-full-chain.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pfx
    * c:\\edgeCertificates\\private\\new-edge-device.key.pem

## <a name="upload-certificates-to-azure-key-vault"></a>Azure Key Vault に証明書をアップロードする

証明書を安全に保存し、複数のデバイスからアクセスできるようにするために、証明書を Azure キー コンテナーにアップロードします。 前の一覧からわかるように、PFX と PEM という 2 種類の証明書ファイルがあります。 PFX ファイルは、キー コンテナーにアップロードされる Key Vault 証明書として扱います。 PEM ファイルはプレーンテキストであり、キー コンテナー シークレットとして扱います。 [Jupyter Notebook](tutorial-machine-learning-edge-04-train-model.md#run-the-jupyter-notebooks) を実行して作成した、Azure Machine Learning ワークスペースに関連付けられているキー コンテナー インスタンスを使用します。

1. [Azure portal](https://portal.azure.com) から、Azure Machine Learning ワークスペースに移動します。

1. Machine Learning ワークスペースの概要ページで、**キー コンテナー** の名前を見つけます。

    ![キー コンテナー名をコピーする方法を示すスクリーンショット。](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

1. 開発用コンピューターで、証明書を Key Vault にアップロードします。 **\<subscriptionId\>** と **\<keyvaultname\>** は、実際のリソース情報に置き換えてください。

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

1. サインインを求められたら、Azure にサインインします。

1. スクリプトが数分間実行され、新しいキー コンテナー エントリの一覧が出力されます。

    ![Key Vault スクリプトの出力を示すスクリーンショット。](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-an-iot-edge-device"></a>IoT Edge デバイスを作成する

Azure IoT Edge デバイスを IoT ハブに接続するために、まずハブ内のデバイスの ID を作成します。 クラウドでデバイス ID から接続文字列を取り、それを使用して IoT Edge デバイス上でランタイムを構成します。 構成済みのデバイスがハブに接続したら、モジュールをデプロイしてメッセージを送信することができます。 対応するデバイス ID を IoT ハブで変更することによって、物理 IoT Edge デバイスの構成を変更することもできます。

このチュートリアルでは、Visual Studio Code を使用して新しいデバイス ID を作成します。 これらの手順は、Azure portal または Azure CLI を使用して完了することもできます。

1. 開発用コンピューターで Visual Studio Code を開きます。

1. Visual Studio Code の **[エクスプローラー]** ビューから、 **[Azure IoT Hub]** フレームを展開します。

1. 省略記号を選択して、 **[Create IoT Edge Device]\(IoT Edge デバイスの作成\)** を選択します。

1. デバイスに名前を付けます。 並べ替えでデバイス一覧の一番上に表示されるように、便宜上、名前は **aaTurbofanEdgeDevice** としています。

1. 新しいデバイスがデバイス一覧に表示されます。

    ![Visual Studio Code エクスプローラーのデバイスのビューを示すスクリーンショット。](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-an-azure-virtual-machine"></a>Azure 仮想マシンをデプロイする

Azure Marketplace にある [Azure IoT Edge on Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) イメージを使用して、このチュートリアル用の IoT Edge デバイスを作成します。 Azure IoT Edge on Ubuntu イメージを起動すると、最新の IoT Edge ランタイムとその依存関係がインストールされます。 次のものを使用して VM をデプロイします。

- PowerShell スクリプト、`Create-EdgeVM.ps1`。
- Azure Resource Manager テンプレート、`IoTEdgeVMTemplate.json`。
- シェル スクリプト、`install packages.sh`。

### <a name="enable-programmatic-deployment"></a>プログラムによるデプロイを有効にする

スクリプト化されたデプロイで Azure Marketplace からのイメージを使用するには、そのイメージに対してプログラムによるデプロイを有効にする必要があります。

1. Azure portal にサインインします。

1. **[すべてのサービス]** を選択します。

1. 検索バーに「**Marketplace**」と入力して選択します。

1. Marketplace の検索バーに「**Azure IoT Edge on Ubuntu**」と入力して選択します。

1. **[Get started]\(開始する\)** ハイパーリンクを選択して、プログラムからデプロイを行います。

1. **[Enable]\(有効にする\)** ボタン、 **[保存]** の順に選択します。

    ![プログラムによる仮想マシンのデプロイを有効にする方法を示すスクリーンショット。](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. 成功通知が表示されます。

### <a name="create-a-virtual-machine"></a>仮想マシンの作成

次に、IoT Edge デバイス用の仮想マシンを作成するスクリプトを実行します。

1. PowerShell ウィンドウを開き、**EdgeVM** ディレクトリに移動します。

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

1. スクリプトを実行して仮想マシンを作成します。

    ```powershell
    .\Create-EdgeVm.ps1
    ```

1. 入力を求められたら、各パラメーターの値を指定します。 サブスクリプション、リソース グループ、場所については、このチュートリアル全体ですべてのリソースに使用しているのと同じ値を使用することをお勧めします。

    * **[Azure サブスクリプション ID]** : Azure portal で確認できます。
    * **[リソース グループ名]** : このチュートリアル用のリソースをグループ化するための覚えやすい名前。
    * **[場所]** :仮想マシンの作成先となる Azure の場所 westus2 や northeurope などです。 詳細については、すべての [Azure の場所](https://azure.microsoft.com/global-infrastructure/locations/)を参照してください。
    * **[AdminUsername]** : 仮想マシンへのサインインに使用する管理者アカウントの名前。
    * **[AdminPassword]** : 仮想マシンの管理者ユーザー名に対して設定するパスワード。

1. スクリプトで VM を設定するには、使用している Azure サブスクリプションに関連付けられた資格情報で Azure にサインインします。

1. スクリプトにより、VM を作成するための情報が確認されます。 **y** を選択するか、**Enter** キーを押して続行します。

1. スクリプトが数分間実行され、その間に次のステップが実行されます。

    * まだリソース グループがない場合は、作成される
    * 仮想マシンが作成される
    * ポート 22 (SSH)、5671 (AMQP)、5672 (AMPQ)、および 443 (TLS) に対して VM の NSG 例外が追加される
    * [Azure CLI](/cli/azure/install-azure-cli-apt) がインストールされる

1. スクリプトは、VM に接続するための SSH 接続文字列を出力します。 次のステップのために接続文字列をコピーします。

    ![コピーする仮想マシンの SSH 接続文字列を示すスクリーンショット。](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>IoT Edge デバイスに接続する

次のいくつかのセクションでは、作成した Azure 仮想マシンを構成します。 最初のステップで、仮想マシンに接続します。

1. コマンド プロンプトを開き、スクリプトの出力からコピーした SSH 接続文字列を貼り付けます。 ユーザー名、サフィックス、リージョンについて、前のセクションで PowerShell スクリプトに対して指定した値に従って、自分自身の情報を入力します。

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

1. ホストの信頼性を確認するよう求められたら、「**yes**」と入力して **Enter** キーを押します。

1. 入力を求められたら、パスワードを指定します。

1. Ubuntu のウェルカム メッセージが表示された後、`<username>@<machinename>:~$` のようなプロンプトが表示されます。

## <a name="download-key-vault-certificates"></a>Key Vault 証明書をダウンロードする

この記事の前半では、証明書を Key Vault にアップロードして、IoT Edge デバイスとリーフ デバイスで使用できるようにしました。 リーフ デバイスは、IoT Hub と通信するためのゲートウェイとして IoT Edge デバイスを使用するダウンストリーム デバイスです。

リーフ デバイスについては、チュートリアルの後半で扱います。 このセクションでは、証明書を IoT Edge デバイスにダウンロードします。

1. Linux 仮想マシン上の SSH セッションから、Azure CLI を使用して Azure にサインインします。

    ```azurecli
    az login
    ```

1. ブラウザーで [Microsoft デバイスのサインイン](https://microsoft.com/devicelogin) ページを開き、一意のコードを入力するように求められます。 これらの手順はローカル コンピューターで実行できます。 認証が完了したら、ブラウザー ウィンドウを閉じます。

1. 認証に成功すると、Linux VM がサインインして Azure サブスクリプションを一覧表示します。

1. Azure CLI コマンドで使用する Azure サブスクリプションを設定します。

    ```azurecli
    az account set --subscription <subscriptionId>
    ```

1. VM 上に証明書用のディレクトリを作成します。

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. キー コンテナーに保存した証明書をダウンロードします: new-edge-device-full-chain.cert.pem、new-edge-device.key.pem、および azure-iot-test-only.root.ca.cert.pem。

    ```azurecli
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>IoT Edge デバイス構成を更新する

IoT Edge ランタイムは、ファイル /etc/iotedge/config.yaml を使用してその構成を保持します。 このファイル内の 3 つの情報を更新する必要があります。

* **Device connection string** (デバイス接続文字列): IoT Hub におけるこのデバイスの ID からの接続文字列
* **Certificates** (証明書): ダウンストリーム デバイスとの間で行われる接続に使用する証明書
* **Hostname** (ホスト名): VM IoT Edge デバイスの完全修飾ドメイン名 (FQDN)

IoT Edge VM の作成に使用した Azure IoT Edge on Ubuntu イメージには、config.yaml ファイル内の接続文字列を更新するシェル スクリプトが付属します。

1. Visual Studio Code で、IoT Edge デバイスを右クリックして **[Copy Device Connection String]\(デバイス接続文字列のコピー\)** を選択します。

    ![Visual Studio Code での接続文字列のコピーを示すスクリーンショット。](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

1. SSH セッションでコマンドを実行して、そのデバイス接続文字列で config.yaml ファイルを更新します。

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

次に、config.yaml ファイルを直接編集して証明書とホスト名を更新します。

1. config.yaml ファイルを開きます。

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. config.yaml ファイルの certificates セクションを更新します。先頭の **#** を削除してパスを設定し、ファイルの内容を次の例のようにします。

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    **certificates:** 行の前にスペースがないこと、また入れ子になった各証明書が 2 つのスペースによってインデントされていることを確認してください。

    nano 内で右クリックすると、クリップボードの内容が現在のカーソル位置に貼り付けられます。 文字列を置換するには、キーボードの矢印キーを使って置換する文字列に移動し、文字列を削除した後、右クリックしてバッファーから貼り付けます。

1. Azure portal で、お使いの仮想マシンに移動します。 **[概要]** セクションから DNS 名 (マシンの FQDN) をコピーします。

1. その FQDN を config.yml ファイルの hostname セクションに貼り付けます。 名前がすべて小文字であることを確認してください。

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

1. **Ctrl + X**、**Y**、および **Enter** キーを押してファイルを保存し、閉じます。

1. IoT Edge デーモンを再起動します。

    ```bash
    sudo systemctl restart iotedge
    ```

1. IoT Edge デーモンの状態を確認します。 コマンドの後に「 **:q**」と入力し、終了します。

    ```bash
    systemctl status iotedge
    ```

1. 状態にエラー ("\[ERROR\]" のプレフィックスが付いた色付きのテキスト) がある場合、デーモンのログを調べて詳細なエラー情報を確認します。

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```
## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルはセットの一部であり、各記事は前の記事の作業が行われたことが前提になっています。 最後のチュートリアルを完了するまで、リソースのクリーンアップはしないでください。

## <a name="next-steps"></a>次のステップ

IoT Edge の透過的なゲートウェイとして Azure VM を構成する作業が完了しました。 まず、テスト証明書を生成してキー コンテナーにアップロードしました。 次に、スクリプトと Resource Manager テンプレート、さらに Azure Marketplace の Ubuntu Server 16.04 LTS + Azure IoT Edge ランタイム イメージを使用して VM をデプロイしました。 VM を起動し、SSH 経由で接続しました。 次に、Azure にサインインし、キー コンテナーから証明書をダウンロードしました。 config.yaml ファイルを更新して IoT Edge ランタイムの構成にいくつかの変更を加えました。

次の記事に進んで、IoT Edge モジュールをビルドします。

> [!div class="nextstepaction"]
> [カスタム IoT Edge モジュールの作成とデプロイ](tutorial-machine-learning-edge-06-custom-modules.md)
