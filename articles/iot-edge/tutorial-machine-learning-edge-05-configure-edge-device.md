---
title: IoT Edge デバイスの構成 - Azure IoT Edge での Machine Learning | Microsoft Docs
description: Linux を実行している Azure 仮想マシンを、透過的なゲートウェイとして機能する Azure IoT Edge デバイスとして構成します。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a2096004a7b389f627c528a8dfb4768ac001f390
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155618"
---
# <a name="tutorial-configure-an-iot-edge-device"></a>チュートリアル:IoT Edge デバイスの構成

> [!NOTE]
> この記事は、IoT Edge 上で Azure Machine Learning を使用するためのチュートリアルのシリーズの一部です。 この記事に直接アクセスしている場合は、最適な結果を得るために、シリーズの[最初の記事](tutorial-machine-learning-edge-01-intro.md) から始めることをお勧めします。

この記事では、Linux を実行している Azure 仮想マシンを、透過的なゲートウェイとして機能する Azure IoT Edge デバイスになるように構成します。 透過的なゲートウェイの構成では、デバイスはゲートウェイの存在を知らなくても、ゲートウェイを介して Azure IoT Hub に接続できます。 同時に、IoT Hub 内のデバイスを操作するユーザーは中間のゲートウェイ デバイスを意識しません。 最終的には、透過的なゲートウェイを使用し、IoT Edge モジュールをゲートウェイに追加することによってエッジ分析をシステムに追加します。

この記事の手順は通常、クラウド開発者によって実行されます。

## <a name="generate-certificates"></a>証明書の生成

ゲートウェイとして機能するデバイスでは、ダウンストリーム デバイスに安全に接続できる必要があります。 Azure IoT Edge では、公開キー基盤 (PKI) を使用して、これらのデバイス間にセキュリティで保護された接続を設定することができます。 この場合、透過的なゲートウェイとして機能する IoT Edge デバイスにダウンストリーム デバイスが接続できるようにします。 妥当なセキュリティを維持するには、ダウン ストリーム デバイスで IoT Edge デバイスの ID を確認してください。 IoT Edge デバイスで証明書を使用するしくみの詳細については、[Azure IoT Edge 証明書の使用の詳細](iot-edge-certs.md)に関するページを参照してください。

このセクションでは、Docker イメージを使用して自己署名証明書を作成した後、そのイメージをビルドして実行します。 Docker イメージを使用してこのステップを完了することを選択した理由は、それによって、Windows 開発用コンピューターで証明書を作成するために必要なステップ数が大幅に削減されたからです。 コンテナーを使用せずに証明書を生成する方法の詳細については、[Windows での証明書の生成](how-to-create-transparent-gateway.md#generate-certificates-with-windows)に関する項目を参照してください。 [Linux での証明書の生成](how-to-create-transparent-gateway.md#generate-certificates-with-linux)に関する項目には、Docker イメージを使用して自動化された一連の指示があります。

1. 開発用の仮想マシンにサインインします。

2. コマンド ライン プロンプトを開き、次のコマンドを実行して VM 上にディレクトリを作成します。

    ```cmd
    mkdir c:\edgeCertificates
    ```

3. Windows の [スタート] メニューから **[Docker for Windows]** を起動します。

4. Visual Studio Code を開きます。

5. **[ファイル]**  >  **[フォルダーを開く...]** を選択し、**C:\\source\\IoTEdgeAndMlSample\\CreateCertificates** を選択します。

6. Dockerfile を右クリックし、 **[Build Image]\(イメージのビルド\)** を選択します。

7. ダイアログで、イメージ名とタグは既定値 **createcertificates:latest** のままにします。

8. ビルドが完了するまで待ちます。

    > [!NOTE]
    > 公開キーが見つからないという警告が表示される場合があります。 この警告は無視しても問題ありません。 同様に、イメージに対するアクセス許可を確認/再設定することを推奨するセキュリティ警告が表示されますが、このイメージに関しては無視しても問題ありません。

9. Visual Studio Code ターミナル ウィンドウで、createcertificates コンテナーを実行します。

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

10. Docker が **c:\\** ドライブへのアクセスを求めます。 **[Share it]\(共有する\)** を選択します。

11. メッセージが表示されたら、資格情報を入力します。

12. コンテナーの実行が終了したら、**c:\\edgeCertificates** にある次のファイルを確認します。

    * c:\\edgeCertificates\\certs\\azure-iot-test-only.root.ca.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device-full-chain.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pfx
    * c:\\edgeCertificates\\private\\new-edge-device.key.pem

## <a name="upload-certificates-to-azure-key-vault"></a>Azure Key Vault に証明書をアップロードする

証明書を安全に保存し、複数のデバイスからアクセスできるようにするために、証明書を Azure Key Vault にアップロードします。 上記の一覧からわかるように、2 種類の証明書ファイルがあります: PFX と PEM。 PFX は、Key Vault にアップロードされる Key Vault 証明書として扱います。 PEM ファイルはプレーンテキストであり、Key Vault シークレットとして扱います。 [Azure Notebooks](tutorial-machine-learning-edge-04-train-model.md#run-azure-notebooks) を実行して作成した、Azure Machine Learning service ワークスペースに関連付けられている Key Vault を使用します。

1. [Azure portal](https://portal.azure.com) から、Azure Machine Learning service ワークスペースに移動します。

2. Azure Machine Learning service ワークスペースの概要ページから、**Key Vault** の名前を探します。

    ![キー コンテナー名をコピーする](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

3. 開発用コンピューターで、証明書を Key Vault にアップロードします。 **\<subscriptionId\>** と **\<keyvaultname\>** は、実際に使用するリソース情報に置き換えます。

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

4. メッセージが表示されたら、Azure にサインインします。

5. スクリプトが数分間実行され、新しい Key Vault エントリの一覧が出力されます。

    ![Key Vault スクリプト出力](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-iot-edge-device"></a>IoT Edge デバイスを作成する

Azure IoT Edge デバイスを IoT ハブに接続するために、まずハブ内のデバイスの ID を作成します。 クラウドでデバイス ID から接続文字列を取り、それを使用して IoT Edge デバイス上でランタイムを構成します。 デバイスが構成されてハブに接続したら、モジュールをデプロイしてメッセージを送信することができます。 対応するデバイス ID の構成を IoT ハブで変更することによって、物理 IoT Edge デバイスの構成を変更することもできます。

このチュートリアルでは、Visual Studio Code を使用して新しいデバイス ID を作成します。 これらの手順は、[Azure portal](how-to-register-device-portal.md) または [Azure CLI](how-to-register-device-cli.md) を使用して完了することもできます。

1. 開発用コンピューターで Visual Studio Code を開きます。

2. Visual Studio Code のエクスプローラー ビューから、 **[Azure IoT Hub devices]\(Azure IoT Hub デバイス\)** フレームを開きます。

3. 省略記号をクリックして **[Create IoT Edge Device]\(IoT Edge デバイスの作成\)** を選択します。

4. デバイスに名前を付けます。 便宜上、**aaTurbofanEdgeDevice** という名前を使用しますが、これは、以前にテスト データ送信用にデバイス ハーネスを通じて作成したすべてのクライアント デバイスよりも前の並び順になります。

5. 新しいデバイスがデバイスの一覧に表示されます。

    ![VS Code エクスプローラーで新しい aaTurbofanEdgeDevice を表示](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-azure-virtual-machine"></a>Azure 仮想マシンをデプロイする

Azure Marketplace にある [[Azure IoT Edge on Ubuntu]](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) イメージを使用して、このチュートリアル用の IoT Edge デバイスを作成します。 Azure IoT Edge on Ubuntu イメージは、起動時に、最新の Azure IoT Edge ランタイムとその依存関係をインストールします。 PowerShell スクリプト `Create-EdgeVM.ps1`、Resource Manager テンプレート `IoTEdgeVMTemplate.json`、およびシェル スクリプト `install packages.sh` を使用して VM をデプロイします。

### <a name="enable-programmatic-deployment"></a>プログラムによるデプロイを有効にする

スクリプト化されたデプロイでマーケットプレースからのイメージを使用するには、そのイメージに対してプログラムによるデプロイを有効にする必要があります。

1. Azure ポータルにサインインします。

1. **[すべてのサービス]** を選択します。

1. 検索バーに「**Marketplace**」と入力して選択します。

1. 検索バーに「**Azure IoT Edge on Ubuntu**」と入力して選択します。

1. 次を選択します: **[プログラムによるデプロイを行いますか? 開始する]** ハイパーリンク。

1. **[Enable]\(有効にする\)** ボタン、 **[保存]** の順に選択します。

    ![プログラムによる VM のデプロイを有効にする](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. 成功通知が表示されます。

### <a name="create-virtual-machine"></a>仮想マシンの作成

次に、IoT Edge デバイス用の仮想マシンを作成するスクリプトを実行します。

1. PowerShell ウィンドウを開き、**EdgeVM** ディレクトリに移動します。

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

2. スクリプトを実行して仮想マシンを作成します。

    ```powershell
    .\Create-EdgeVm.ps1
    ```

3. 入力を求められたら、各パラメーターの値を指定します。 サブスクリプション、リソース グループ、場所については、このチュートリアル全体ですべてのリソースに使用しているのと同じものを使用することをお勧めします。

    * **[Azure サブスクリプション ID]** : Azure portal で確認できます
    * **[リソース グループ名]** : このチュートリアル用のリソースをグループ化するための覚えやすい名前
    * **[場所]** :仮想マシンの作成先となる Azure の場所 たとえば、westus2 または northeurope などです。 詳細については、すべての [Azure の場所](https://azure.microsoft.com/global-infrastructure/locations/)を参照してください。
    * **[AdminUsername]** : 仮想マシンへのサインインに使用する管理者アカウントの名前
    * **[AdminPassword]** : 仮想マシン上の AdminUsername に対して設定するパスワード

4. スクリプトで VM を設定できるようにするには、使用している Azure サブスクリプションに関連付けられた資格情報で Azure にサインインする必要があります。

5. スクリプトにより、VM を作成するために必要な情報が確認されます。 **y** を選択するか、**Enter** キーを押して続行します。

6. スクリプトが数分間実行され、次のステップが実行されます。

    * まだ存在しない場合、リソース グループを作成する
    * 仮想マシンの作成
    * ポート 22 (SSH)、5671 (AMQP)、5672 (AMPQ)、および 443 (SSL) に対して VM の NSG 例外を追加する
    * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest) をインストールする

7. スクリプトは、VM に接続するための SSH 接続文字列を出力します。 次のステップのために接続文字列をコピーします。

    ![VM の SSH 接続文字列をコピーする](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>IoT Edge デバイスに接続する

次のいくつかのセクションでは、作成した Azure 仮想マシンを構成します。 最初のステップで、仮想マシンに接続します。

1. コマンド プロンプトを開き、スクリプトの出力からコピーした SSH 接続文字列を貼り付けます。 ユーザー名、サフィックス、リージョンについて、前のセクションで PowerShell スクリプトに対して指定した値に従って、自分自身の情報を入力します。

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

2. ホストの信頼性を確認するよう求められたら、「**yes**」と入力して **Enter** キーを押します。

3. 求められたら、パスワードを入力します。

4. Ubuntu のウェルカム メッセージと、`<username>@<machinename>:~$` のようなプロンプトが表示されます。

## <a name="download-key-vault-certificates"></a>Key Vault 証明書をダウンロードする

この記事の前半では、証明書を Key Vault にアップロードして、IoT Edge デバイスとリーフ デバイス (IoT Hub と通信するためのゲートウェイとして IoT Edge デバイスを使用するダウンストリーム デバイス) で使用できるようにしました。 リーフ デバイスについては、チュートリアルの後半で扱います。 このセクションでは、証明書を IoT Edge デバイスにダウンロードします。

1. Linux 仮想マシン上の SSH セッションから、Azure CLI を使用して Azure にサインインします。

    ```bash
    az login
    ```

1. ブラウザーで <https://microsoft.com/devicelogin> を開いて固有のコードを入力するよう求められます。 これらの手順はローカル コンピューターで実行できます。 認証が完了したら、ブラウザー ウィンドウを閉じます。

1. 認証に成功すると、Linux VM がサインインして Azure サブスクリプションを一覧表示します。

1. Azure CLI コマンドで使用する Azure サブスクリプションを設定します。

    ```bash
    az account set --subscription <subscriptionId>
    ```

1. VM 上に証明書用のディレクトリを作成します。

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. キー コンテナーに保存した証明書をダウンロードします: new-edge-device-full-chain.cert.pem、new-edge-device.key.pem、および azure-iot-test-only.root.ca.cert.pem

    ```bash
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>IoT Edge デバイス構成を更新する

IoT Edge ランタイムは、ファイル /etc/iotedge/config.yaml を使用してその構成を保持します。 このファイル内の 3 つの情報を更新する必要があります。

* **Device connection string** (デバイス接続文字列): IoT Hub におけるこのデバイスの ID からの接続文字列
* **Certificates** (証明書): ダウンストリーム デバイスで行われる接続に使用する証明書
* **Hostname** (ホスト名): VM IoT Edge デバイスの完全修飾ドメイン名 (FQDN)

IoT Edge VM の作成に使用した *[Azure IoT Edge on Ubuntu]* イメージには、config.yaml 内の接続文字列を更新するシェル スクリプトが付属します。

1. Visual Studio Code で、IoT Edge デバイスを右クリックして **[Copy Device Connection String]\(デバイス接続文字列のコピー\)** を選択します。

    ![Visual Studio Code から接続文字列をコピーする](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

2. SSH セッションでコマンドを実行して、そのデバイス接続文字列で config.yaml ファイルを更新します。

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

次に、config.yaml を直接編集して証明書とホスト名を更新します。

1. config.yaml ファイルを開きます。

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

2. config.yaml の certificates セクションを更新します。先頭の `#` を削除してパスを設定し、ファイルの内容を次の例のようにします。

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    "certificates:" の前にスペースがないこと、また各証明書の前に 2 つのスペースがあることを確認してください。

    nano 内で右クリックすると、クリップボードの内容が現在のカーソル位置に貼り付けられます。 文字列を置換するには、キーボードの矢印キーを使って置換する文字列に移動し、文字列を削除した後、右クリックしてバッファーから貼り付けます。

3. Azure portal で仮想マシンに移動します。 **[概要]** セクションから DNS 名 (マシンの FQDN) をコピーします。

4. その FQDN を config.yml の hostname セクションに貼り付けます。 名前がすべて小文字であることを確認します。

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

5. ファイルを保存して閉じます (`Ctrl + X`、`Y`、`Enter`)。

6. iotedge デーモンを再起動します。

    ```bash
    sudo systemctl restart iotedge
    ```

7. IoT Edge デーモンのステータスを確認します (コマンドの後、終了するには「:q」と入力します)。

    ```bash
    systemctl status iotedge
    ```

8. ステータスにエラー ("\[ERROR\]" のプレフィックスが付いた色付きのテキスト) がある場合、デーモンのログを調べて詳細なエラー情報を確認します。

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```

## <a name="next-steps"></a>次の手順

Azure IoT Edge の透過的なゲートウェイとして Azure VM を構成する作業が完了しました。 まず、テスト証明書を生成して Azure Key Vault にアップロードしました。 次に、スクリプトと Resource Manager テンプレート、さらに Azure Marketplace の "Ubuntu Server 16.04 LTS + Azure IoT Edge runtime" イメージを使用して VM をデプロイしました。 スクリプトは Azure CLI をインストールする追加のステップを実行しました ([apt による Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli-apt))。 VM を起動して実行し、SSH 経由で接続し、Azure にサインインし、Key Vault から証明書をダウンロードし、config.yaml ファイルを更新して IoT Edge ランタイムの構成をいくつか更新しました。 ゲートウェイとしての IoT Edge の使用について、詳しくは「[IoT Edge デバイスをゲートウェイとして使用する方法](iot-edge-as-gateway.md)」を参照してください。 透過的なゲートウェイとして IoT Edge デバイスを構成する方法の詳細については、「[透過的なゲートウェイとして機能するように IoT Edge デバイスを構成する](how-to-create-transparent-gateway.md)」を参照してください。

次の記事に進んで、IoT Edge モジュールをビルドします。

> [!div class="nextstepaction"]
> [カスタム IoT Edge モジュールの作成とデプロイ](tutorial-machine-learning-edge-06-custom-modules.md)
