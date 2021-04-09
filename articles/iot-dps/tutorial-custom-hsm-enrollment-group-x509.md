---
title: チュートリアル - カスタム ハードウェア セキュリティ モジュール (HSM) を使って X.509 デバイスを Azure IoT Hub にプロビジョニングする
description: このチュートリアルでは、登録グループを使用します。 このチュートリアルでは、カスタムのハードウェア セキュリティ モジュール (HSM) と Azure IoT Hub Device Provisioning Service (DPS) 向け C デバイス SDK を使用して X.509 デバイスをプロビジョニングする方法を説明します。
author: wesmc7777
ms.author: wesmc
ms.date: 01/28/2021
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: b178aa4a524cb7fcc85c7fc68ac5f772747787a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99052365"
---
# <a name="tutorial-provision-multiple-x509-devices-using-enrollment-groups"></a>チュートリアル:登録グループを使って複数の X.509 デバイスをプロビジョニングする

このチュートリアルでは、認証に X.509 証明書を使用する一連の IoT デバイスをまとめてプロビジョニングする方法を説明します。 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) のサンプル デバイス コードは、X.509 デバイスのプロビジョニングをシミュレートするために開発用マシンで実行されます。 実際のデバイスでは、デバイス コードはデプロイされて IoT デバイスから実行されます。

このチュートリアルの以降の内容に進む前に、少なくとも [Azure portal での IoT Hub Device Provisioning Service の設定](quick-setup-auto-provision.md)に関するページの手順を済ませておいてください。 さらに、自動プロビジョニングの処理に慣れていない場合は、[プロビジョニング](about-iot-dps.md#provisioning-process)の概要を確認してください。 

Azure IoT Device Provisioning Service では、デバイスのプロビジョニングについて 2 種類の登録をサポートしています。

* [登録グループ](concepts-service.md#enrollment-group)：複数の関連するデバイスを登録するために使用します。
* [個々の登録](concepts-service.md#individual-enrollment):単一デバイスを登録するために使用します。

このチュートリアルは、登録グループを使用してデバイスのセットをプロビジョニングする方法を示した、これより前のチュートリアルに似ています。 ただし、このチュートリアルでは、対称キーの代わりに X.509 証明書を使用します。 [対称キー](./concepts-symmetric-key-attestation.md)を使用したシンプルなアプローチについては、このセクション内のこれよりも前のチュートリアルを確認してください。

このチュートリアルでは、ハードウェアベースの安全なストレージとのインターフェイスとしての役割を果たすスタブを実装した[カスタム HSM サンプル](https://github.com/Azure/azure-iot-sdk-c/tree/master/provisioning_client/samples/custom_hsm_example)をお見せします。 [ハードウェア セキュリティ モジュール (HSM)](./concepts-service.md#hardware-security-module) は、ハードウェアベースの安全なストレージとしてデバイス シークレットを保管する用途に使用します。 対称キー、X.509 証明書、または TPM の構成証明と HSM を併用すると、シークレットの安全なストレージを実現できます。 デバイス シークレットのハードウェアベースのストレージは、必須ではありませんが、デバイス証明書の秘密キーなどの機密情報を保護するために強くお勧めします。


このチュートリアルの目標は次のとおりです。

> [!div class="checklist"]
> * 証明書の信頼の連鎖を作成して、X.509 証明書を使用する一連のデバイスを整理する。
> * 証明書チェーンで使用する署名証明書を使って所有証明を完了する。
> * 証明書チェーンを使用する新しいグループ登録を作成する。
> * [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) のコードを使用して、デバイスをプロビジョニングするための開発環境をセットアップする。
> * SDK 内のカスタム ハードウェア セキュリティ モジュール (HSM) のサンプルを使用して、証明書チェーンを使用しているデバイスをプロビジョニングする。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

デバイスをシミュレートするために使用される Windows 開発環境の前提条件は次のとおりです。 Linux または macOS については、SDK ドキュメントの「[開発環境を準備する](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md)」の該当するセクションを参照してください。

* [C++ によるデスクトップ開発](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development)ワークロードを有効にした [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019。 Visual Studio 2015 と Visual Studio 2017 もサポートされています。 

    この記事では、IoT デバイスにデプロイされるデバイス サンプル コードをビルドするために Visual Studio を使用します。  これは、デバイス自体に Visual Studio が必要であるという意味ではありません。

* [Git](https://git-scm.com/download/) の最新バージョンがインストールされている。

## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>Azure IoT C SDK の開発環境を準備する

このセクションでは、[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) のビルドに使用する開発環境を準備します。 SDK には、DPS による X.509 デバイスのプロビジョニングに使用するサンプル コードとツールが含まれています。

1. [CMake ビルド システム](https://cmake.org/download/)をダウンロードします。

    `CMake` のインストールを開始する **前に**、Visual Studio の前提条件 ([Visual Studio](https://visualstudio.microsoft.com/vs/) と ["C++ によるデスクトップ開発"](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) ワークロード) をマシンにインストールしておくことが重要です。 前提条件を満たし、ダウンロードを検証したら、CMake ビルド システムをインストールします。

2. Azure IoT C SDK の[最新リリース](https://github.com/Azure/azure-iot-sdk-c/releases/latest)のタグ名を見つけます。

3. コマンド プロンプトまたは Git Bash シェルを開きます。 次のコマンドを実行して、最新リリースの [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub リポジトリを複製します。 `-b` パラメーターの値として、前の手順で見つけたタグを使用します。

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    この操作は、完了するまでに数分かかります。

4. git リポジトリのルート ディレクトリに `cmake` サブディレクトリを作成し、そのフォルダーに移動します。 

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. 作成した `cmake` ディレクトリには、カスタム HSM サンプルと、カスタム HSM を使用して X.509 認証を行うデバイス プロビジョニング コードのサンプルが格納されます。 

    `cmake` ディレクトリで次のコマンドを実行して、開発プラットフォームに応じたバージョンの SDK をビルドします。 ビルドには、カスタム HSM サンプルへの参照が含まれます。 

    以下の `-Dhsm_custom_lib` で使用するパスを指定するときは、既に作成した `cmake` ディレクトリを基準とした相対パスを使用してください。 以下に示した相対パスは、あくまで一例です。

    ```cmd
    $ cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=/d/azure-iot-sdk-c/cmake/provisioning_client/samples/custom_hsm_example/Debug/custom_hsm_example.lib ..
    ```

    `cmake` で C++ コンパイラが見つからない場合は、上記のコマンドの実行中にビルド エラーが発生している可能性があります。 これが発生した場合は、[Visual Studio コマンド プロンプト](/dotnet/framework/tools/developer-command-prompt-for-vs)でこのコマンドを実行してください。

    ビルドに成功すると、`cmake` ディレクトリに Visual Studio ソリューションが生成されます。 出力の最後の数行は次のようになります。

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=/d/azure-iot-sdk-c/cmake/provisioning_client/samples/custom_hsm_example/Debug/custom_hsm_example.lib ..
    -- Building for: Visual Studio 16 2019
    -- The C compiler identification is MSVC 19.23.28107.0
    -- The CXX compiler identification is MSVC 19.23.28107.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: D:/azure-iot-sdk-c/cmake
    ```

## <a name="create-an-x509-certificate-chain"></a>X.509 証明書チェーンを作成する

このセクションでは、このチュートリアルでの各デバイスのテストのために 3 つの証明書から成る X.509 証明書チェーンを生成します。 証明書の階層は次のとおりです。

![チュートリアルのデバイスの証明書チェーン](./media/tutorial-custom-hsm-enrollment-group-x509/example-device-cert-chain.png#lightbox)

[ルート証明書](concepts-x509-attestation.md#root-certificate):ルート証明書の検証のため、[所有証明](how-to-verify-certificates.md)を実施することになります。 この検証により、DPS がその証明書を信頼し、その証明書によって署名された証明書を検証できるようになります。

[中間証明書](concepts-x509-attestation.md#intermediate-certificate):中間証明書は、製品ラインや会社の部門などを基準としてデバイスを論理的にグループ化する用途に使用するのが一般的です。 このチュートリアルでは、中間証明書が 1 つ含まれる証明書チェーンを使用します。 中間証明書の署名には、ルート証明書を使用します。 この証明書は、DPS で作成した登録グループ上でデバイスを論理的にグループ化する用途にも使用します。 この構成を使うと、同じ中間証明書によって署名されているデバイス証明書があるデバイスのグループ全体をまとめて管理できます。 登録グループを作成すると、デバイスをグループ単位で有効または無効にすることができます。 デバイスをグループ単位で無効にすることに関する詳細については、「[登録グループを使用して、X.509 中間証明書またはルート CA 証明書を禁止する](how-to-revoke-device-access-portal.md#disallow-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group)」を参照してください。

[デバイス証明書](concepts-x509-attestation.md#end-entity-leaf-certificate): デバイス (リーフ) 証明書は、中間証明書によって署名され、秘密キーと一緒にデバイスに保存されます。 これらの機密項目は、HSM を使用して安全に保存されるのが理想的です。 プロビジョニングの試行時には、各デバイスによって、この証明書と秘密キーが証明書チェーンと共に提示されます。 

#### <a name="create-root-and-intermediate-certificates"></a>ルートおよび中間証明書を作成する

証明書チェーンのルートおよび中間部分を作成するには:

1. Git Bash のコマンド プロンプトを開きます。 「[サンプルおよびチュートリアル用のテスト CA 証明書を管理する](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md#managing-test-ca-certificates-for-samples-and-tutorials)」の手順 1 および 2 のうち、Bash シェルを使用する部分を完了します。

    ここでは、証明書スクリプトのための作業ディレクトリを作成し、OpenSSL を使って証明書チェーン用のサンプルのルートおよび中間証明書を生成します。 
    
2. 出力で自己署名ルート証明書の場所が表示されていることに注目してください。 後ほど、この証明書に[所有証明](how-to-verify-certificates.md)を実施し、所有権を検証します。

    ```output
    Creating the Root CA Certificate
    CA Root Certificate Generated At:
    ---------------------------------
        ./certs/azure-iot-test-only.root.ca.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number:
                fc:cc:6b:ab:3b:9a:3e:fe
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub CA Cert Test Only
            Validity
                Not Before: Oct 23 21:30:30 2020 GMT
                Not After : Nov 22 21:30:30 2020 GMT
            Subject: CN=Azure IoT Hub CA Cert Test Only
    ```        
    
3. 出力で、先ほどのルート証明書により署名および発行されている中間証明書の場所が表示されていることに注目してください。 この証明書は、後で作成する登録グループに使用します。

    ```output
    Intermediate CA Certificate Generated At:
    -----------------------------------------
        ./certs/azure-iot-test-only.intermediate.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number: 1 (0x1)
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub CA Cert Test Only
            Validity
                Not Before: Oct 23 21:30:33 2020 GMT
                Not After : Nov 22 21:30:33 2020 GMT
            Subject: CN=Azure IoT Hub Intermediate Cert Test Only
    ```    
    
#### <a name="create-device-certificates"></a>デバイス証明書を作成する

チェーンで中間証明書によって署名されるデバイス証明書を作成するには:

1. 次のコマンドを実行し、新しいデバイス (リーフ) 証明書を作成します。このとき、パラメーターとしてサブジェクト名を指定してください。 サブジェクト名は、このチュートリアルのために用意したサンプルの `custom-hsm-device-01` を使用します。 このサブジェクト名が、IoT デバイスのデバイス ID になります。 

    > [!WARNING]
    > サブジェクト名にはスペースを使用しないでください。 このサブジェクト名は、プロビジョニングする IoT デバイスのデバイス ID となります。 デバイス ID の規則に従う必要があります。 詳細については、「[デバイス ID のプロパティ](../iot-hub/iot-hub-devguide-identity-registry.md#device-identity-properties)」を参照してください。

    ```cmd
    ./certGen.sh create_device_certificate_from_intermediate "custom-hsm-device-01"
    ```

    次の出力で、新しいデバイス証明書の場所が表示されていることに注目してください。 このデバイス証明書は、先ほどの中間証明書により署名 (発行) されています。

    ```output
    -----------------------------------
    ./certs/new-device.cert.pem: OK
    Leaf Device Certificate Generated At:
    ----------------------------------------
        ./certs/new-device.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number: 9 (0x9)
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub Intermediate Cert Test Only
            Validity
                Not Before: Nov 10 09:20:33 2020 GMT
                Not After : Dec 10 09:20:33 2020 GMT
            Subject: CN=custom-hsm-device-01
    ```    
    
2. 次のコマンドを実行し、新しいデバイス証明書も含めた証明書チェーン全体の .pem ファイルを `custom-hsm-device-01` 用に作成します。

    ```Bash
    cd ./certs && cat new-device.cert.pem azure-iot-test-only.intermediate.cert.pem azure-iot-test-only.root.ca.cert.pem > new-device-01-full-chain.cert.pem && cd ..
    ```

    テキスト エディターを使用して、証明書チェーン ファイル *./certs/new-device-01-full-chain.cert.pem* を開きます。 証明書チェーンのテキストには、証明書を 3 つすべて使った完全なチェーンが記載されています。 このチュートリアルでは、後ほど HSM デバイス コードと共に、このテキストを証明書チェーンとして `custom-hsm-device-01` に使用します。

    チェーン テキストの全文は次のような形式になっています。
 
    ```output 
    -----BEGIN CERTIFICATE-----
        <Text for the device certificate includes public key>
    -----END CERTIFICATE-----
    -----BEGIN CERTIFICATE-----
        <Text for the intermediate certificate includes public key>
    -----END CERTIFICATE-----
    -----BEGIN CERTIFICATE-----
        <Text for the root certificate includes public key>
    -----END CERTIFICATE-----
    ```

3. 新しいデバイス証明書の秘密キーが *./private/new-device.key.pem* に書き込まれていることに注目してください。 このキー ファイルの名前を `custom-hsm-device-01` デバイス用に *./private/new-device-01.key.pem* に変更します。 このキーのテキストは、プロビジョニング中にデバイスで必要になります。 このテキストは、後ほどカスタム HSM の例に追加します。

    ```bash
    $ mv private/new-device.key.pem private/new-device-01.key.pem
    ```

    > [!WARNING]
    > 証明書のテキストには、公開キーの情報のみが含まれます。 
    >
    > しかし、デバイスはデバイス証明書の秘密キーにもアクセスできる必要があります。 その理由は、プロビジョニングの試行時に、デバイスでそのキーを使って検証を実施する必要があるからです。 このキーの機密性こそ、現実の HSM 内のハードウェアベースのストレージを使用して秘密キーを保護することが推奨される主な理由の 1 つです。

4. デバイス ID が `custom-hsm-device-02` である 2 つ目のデバイスについて、手順 1 から 3 を繰り返します。 このデバイスには次の値を使用します。

    |   説明                 |  値  |
    | :---------------------------- | :--------- |
    | サブジェクト名                  | `custom-hsm-device-02` |
    | 完全な証明書チェーン ファイル   | *./certs/new-device-02-full-chain.cert.pem* |
    | 秘密キーのファイル名          | *private/new-device-02.key.pem* |
    

## <a name="verify-ownership-of-the-root-certificate"></a>ルート証明書の所有権を検証する

1. 「[X.509 証明書のパブリック部分を登録して確認コードを取得する](how-to-verify-certificates.md#register-the-public-part-of-an-x509-certificate-and-get-a-verification-code)」の説明に従ってルート証明書 (`./certs/azure-iot-test-only.root.ca.cert.pem`) をアップロードし、DPS から確認コードを取得します。

2. DPS からルート証明書の確認コードを取得したら、証明書スクリプトの作業ディレクトリから次のコマンドを実行し、検証証明書を生成します。
 
    ここで使用している確認コードは一例です。 DPS により生成されたコードを使用してください。    

    ```Bash
    ./certGen.sh create_verification_certificate 1B1F84DE79B9BD5F16D71E92709917C2A1CA19D5A156CB9F    
    ```    

    このスクリプトでは、ルート証明書によって署名され、サブジェクト名として確認コードが設定された証明書が作成されます。 DPS はこの証明書を使用して、ユーザーがルート証明書の秘密キーにアクセスできるかどうかを確認します。 スクリプトの出力では、検証証明書の場所に注目してください。 この証明書は `.pfx` 形式で生成されます。

    ```output
    Leaf Device PFX Certificate Generated At:
    --------------------------------------------
        ./certs/verification-code.cert.pfx
    ```

3. 「[署名された検証証明書をアップロードする](how-to-verify-certificates.md#upload-the-signed-verification-certificate)」の説明に従って検証証明書をアップロードし、DPS で **[検証]** をクリックすると、ルート証明書の所有証明が完了します。


## <a name="update-the-certificate-store-on-windows-based-devices"></a>Windows ベースのデバイス上の証明書ストアを更新する

Windows 以外のデバイスでは、証明書チェーンをコードから証明書ストアとして渡すことができます。

Windows ベースのデバイスでは、Windows の[証明書ストア](/windows/win32/secauthn/certificate-stores)に署名証明書 (ルートと中間) を追加する必要があります。 そうしないと、トランスポート層セキュリティ (TLS) を使用した安全なチャネルで署名証明書が DPS に転送されなくなります。

> [!TIP]
> C SDK でセキュリティ保護されたチャネル (Schannel) を使用するのではなく、OpenSSL を使用することもできます。 OpenSSL の使用の詳細については、「[SDK での OpenSSL の使用](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#using-openssl-in-the-sdk)」を参照してください。

Windows ベースのデバイスの証明書ストアに署名証明書を追加するには:

1. Git Bash のプロンプトで、署名証明書が格納されている `certs` サブディレクトリに移動し、それらを次のようにして `.pfx` に変換します。

    ルート証明書:

    ```bash
    winpty openssl pkcs12 -inkey ../private/azure-iot-test-only.root.ca.key.pem -in ./azure-iot-test-only.root.ca.cert.pem -export -out ./root.pfx
    ```
    
    中間証明書:   

    ```bash
    winpty openssl pkcs12 -inkey ../private/azure-iot-test-only.intermediate.key.pem -in ./azure-iot-test-only.intermediate.cert.pem -export -out ./intermediate.pfx
    ```

2. Windows の **[スタート]** ボタンを右クリックします。 その後、 **[ファイル名を指定して実行]** を左クリックします。 「*certmgr.mcs*」と入力し、 **[OK]** をクリックして、証明書マネージャー MMC スナップインを起動します。

3. 証明書マネージャーの **[証明書 - 現在のユーザー]** で、 **[信頼されたルート証明機関]** をクリックします。 次に、メニューで **[アクション]**  >  **[すべてのタスク]**  >  **[インポート]** の順にクリックして `root.pfx` をインポートします。

    * **Personal Information Exchange (.pfx)** で検索してください。
    * パスワードには `1234` を使用します。
    * 証明書は、 **[信頼されたルート証明機関]** 証明書ストアに配置します。

4. 証明書マネージャーの **[証明書 - 現在のユーザー]** で、 **[中間証明機関]** をクリックします。 次に、メニューで **[アクション]**  >  **[すべてのタスク]**  >  **[インポート]** の順にクリックして `intermediate.pfx` をインポートします。

    * **Personal Information Exchange (.pfx)** で検索してください。
    * パスワードには `1234` を使用します。
    * 証明書は、 **[中間証明機関]** 証明書ストアに配置します。

これで、Windows ベースのデバイスで署名証明書が信頼され、チェーン全体を DPS に転送できるようになりました。



## <a name="create-an-enrollment-group"></a>登録グループを作成する

1. Azure portal にサインインし、左側のメニューの **[すべてのリソース]** を選択して、Device Provisioning Service を開きます。

2. **[登録を管理します]** タブを選択し、上部にある **[登録グループの追加]** を選択します。

3. **[登録グループの追加]** パネルで次の情報を入力して、 **[保存]** を押します。

      ![ポータルで X.509 構成証明の登録グループを追加する](./media/tutorial-custom-hsm-enrollment-group-x509/custom-hsm-enrollment-group-x509.png#lightbox)

    | フィールド        | 値           |
    | :----------- | :-------------- |
    | **グループ名** | このチュートリアルでは、「**custom-hsm-x509-devices**」と入力します。 |
    | **構成証明の種類** | **[証明書]** を選択します。 |
    | **IoT Edge デバイス** | **[偽]** を選択します。 |
    | **証明書の種類** | **[中間証明書]** を選択します。 |
    | **プライマリ証明書の .pem ファイルまたは .cer ファイル** | 既に作成した中間証明書 ( *./certs/azure-iot-test-only.intermediate.cert.pem*) の場所を指定します。 |


## <a name="configure-the-provisioning-device-code"></a>デバイスのプロビジョニング コードを構成する

このセクションでは、Device Provisioning Service インスタンス情報を使用してサンプル コードを更新します。 デバイスは認証されると、このセクションで構成された Device Provisioning Service インスタンスにリンクされている IoT ハブに割り当てられます。

1. Azure portal で、Device Provisioning Service の **[概要]** タブを選択し、**[_ID スコープ_]** の値をメモします。

    ![ポータルのブレードから Device Provisioning サービスのエンドポイント情報を抽出](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. Visual Studio を起動し、Git リポジトリ azure-iot-sdk-c のルートに作成した `cmake` ディレクトリに作成された新しいソリューション ファイルを開きます。 ソリューション ファイルの名前は `azure_iot_sdks.sln` です。

3. Visual Studio のソリューション エクスプローラーで **[Provisioning_Samples] > [prov_dev_client_sample] > [ソース ファイル]** の順に移動し、*prov_dev_client_sample.c* を開きます。

4. 定数 `id_scope` を探し、以前にコピーした **ID スコープ** の値で置き換えます。 

    ```c
    static const char* id_scope = "0ne00000A0A";
    ```

5. 同じファイル内で `main()` 関数の定義を探します。 以下に示すように `hsm_type` 変数が `SECURE_DEVICE_TYPE_X509` に設定されていることを確認します。

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    hsm_type = SECURE_DEVICE_TYPE_X509;
    //hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. **prov\_dev\_client\_sample** プロジェクトを右クリックし、 **[スタートアップ プロジェクトに設定]** を選択します。


## <a name="configure-the-custom-hsm-stub-code"></a>カスタム HSM スタブ コードを構成する

実際のハードウェアベースのストレージとやり取りする際の詳細は、ハードウェアに応じて異なります。 そのため、このチュートリアルのシミュレートされたデバイスで使用される証明書チェーンを、カスタム HSM スタブ コードにハードコードします。 実際には、機密情報のセキュリティを強化するために、実際の HSM ハードウェアに証明書チェーンが格納されます。 そのうえで、このサンプルで使用されたスタブ メソッドとよく似たメソッドを実装し、ハードウェアベースのストレージからシークレットを読み取れるようにします。 

HSM ハードウェアは必須ではありませんが、証明書の秘密キーなど、機密情報を保護するために推奨されます。 実際の HSM がサンプルによって呼び出されると、秘密キーはソース コードに現れません。 キーをソース コードに含めると、コードを表示できるすべてのユーザーに対してキーが公開されます。 これは、学習を支援するために、この記事でのみ行います。

ID `custom-hsm-device-01` でデバイスの ID をシミュレートするようにカスタム HSM スタブ コードを更新するには、次の手順を実行します。

1. Visual Studio のソリューション エクスプローラーで **[Provisioning_Samples] > [custom_hsm_example] > [ソース ファイル]** の順に移動し、*custom_hsm_example.c* を開きます。

2. `COMMON_NAME` 文字列定数の文字列値を、デバイス証明書の生成時に使用した共通名に更新します。

    ```c
    static const char* const COMMON_NAME = "custom-hsm-device-01";
    ```

3. 同じファイル内で、証明書の生成後に *./certs/new-device-01-full-chain.cert.pem* に保存した証明書チェーンのテキストを使用して、`CERTIFICATE` 定数文字列の文字列値を更新する必要があります。

    証明書のテキストの構文は、次のパターンに従う必要があります。Visual Studio によって余分なスペースが追加されたり、解析が実行されたりしないようにしてください。

    ```c
    // <Device/leaf cert>
    // <intermediates>
    // <root>
    static const char* const CERTIFICATE = "-----BEGIN CERTIFICATE-----\n"
    "MIIFOjCCAyKgAwIBAgIJAPzMa6s7mj7+MA0GCSqGSIb3DQEBCwUAMCoxKDAmBgNV\n"
        ...
    "MDMwWhcNMjAxMTIyMjEzMDMwWjAqMSgwJgYDVQQDDB9BenVyZSBJb1QgSHViIENB\n"
    "-----END CERTIFICATE-----\n"
    "-----BEGIN CERTIFICATE-----\n"
    "MIIFPDCCAySgAwIBAgIBATANBgkqhkiG9w0BAQsFADAqMSgwJgYDVQQDDB9BenVy\n"
        ...
    "MTEyMjIxMzAzM1owNDEyMDAGA1UEAwwpQXp1cmUgSW9UIEh1YiBJbnRlcm1lZGlh\n"
    "-----END CERTIFICATE-----\n"
    "-----BEGIN CERTIFICATE-----\n"
    "MIIFOjCCAyKgAwIBAgIJAPzMa6s7mj7+MA0GCSqGSIb3DQEBCwUAMCoxKDAmBgNV\n"
        ...
    "MDMwWhcNMjAxMTIyMjEzMDMwWjAqMSgwJgYDVQQDDB9BenVyZSBJb1QgSHViIENB\n"
    "-----END CERTIFICATE-----";        
    ```

    この手順でこの文字列値を正しく更新するのは非常に面倒であり、エラーが発生する可能性があります。 Git Bash プロンプトで適切な構文を生成するには、次の Bash シェル コマンドをコピーして Git Bash コマンド プロンプトに貼り付け、**Enter** キーを押します。 これらのコマンドにより、`CERTIFICATE` 文字列定数値の構文が生成されます。

    ```Bash
    input="./certs/new-device-01-full-chain.cert.pem"
    bContinue=true
    prev=
    while $bContinue; do
        if read -r next; then
          if [ -n "$prev" ]; then   
            echo "\"$prev\\n\""
          fi
          prev=$next  
        else
          echo "\"$prev\";"
          bContinue=false
        fi  
    done < "$input"
    ```

    新しい定数値の証明書の出力テキストをコピーして貼り付けます。 


4. 同じファイルで、`PRIVATE_KEY` 定数の文字列値も、デバイス証明書の秘密キーで更新する必要があります。

    秘密キーのテキストの構文は、次のパターンに従う必要があります。Visual Studio によって余分なスペースが追加されたり、解析が実行されたりしないようにしてください。

    ```c
    static const char* const PRIVATE_KEY = "-----BEGIN RSA PRIVATE KEY-----\n"
    "MIIJJwIBAAKCAgEAtjvKQjIhp0EE1PoADL1rfF/W6v4vlAzOSifKSQsaPeebqg8U\n"
        ...
    "X7fi9OZ26QpnkS5QjjPTYI/wwn0J9YAwNfKSlNeXTJDfJ+KpjXBcvaLxeBQbQhij\n"
    "-----END RSA PRIVATE KEY-----";
    ```

    この手順でも、この文字列値を正しく更新するのは非常に面倒であり、エラーが発生する可能性があります。 Git Bash プロンプトで適切な構文を生成するには、次の Bash シェル コマンドをコピーして貼り付け、**Enter** キーを押します。 これらのコマンドにより、`PRIVATE_KEY` 文字列定数値の構文が生成されます。

    ```Bash
    input="./private/new-device-01.key.pem"
    bContinue=true
    prev=
    while $bContinue; do
        if read -r next; then
          if [ -n "$prev" ]; then   
            echo "\"$prev\\n\""
          fi
          prev=$next  
        else
          echo "\"$prev\";"
          bContinue=false
        fi  
    done < "$input"
    ```

    新しい定数値のプライベート キーの出力テキストをコピーして貼り付けます。 

5. *custom_hsm_example.c* を保存します。

6. Visual Studio のメニューで **[デバッグ]**  >  **[デバッグなしで開始]** の順に選択して、ソリューションを実行します。 プロジェクトをリビルドするよう求められたら、 **[はい]** を選択して、プロジェクトをリビルドしてから実行します。

    次の出力は、シミュレートされたデバイス `custom-hsm-device-01` が正常に起動し、プロビジョニング サービスに接続した場合の例です。 デバイスは IoT ハブに割り当てられ、登録されています。

    ```cmd
    Provisioning API Version: 1.3.9
    
    Registering Device
    
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    
    Registration Information received from service: test-docs-hub.azure-devices.net, deviceId: custom-hsm-device-01
    Press enter key to exit:
    ```

7. ポータルで、Provisioning Service にリンクされた IoT ハブに移動し、 **[IoT デバイス]** タブを選択します。X.509 デバイスをハブにプロビジョニングすると、そのデバイスの ID が **[IoT デバイス]** ブレードに表示されます。このとき、 *[状態]* は **[有効]** となります。 場合によっては、一番上にある **[最新の情報に更新]** を押す必要があります。 

    ![IoT ハブにカスタム HSM デバイスが登録されたところ](./media/tutorial-custom-hsm-enrollment-group-x509/hub-provisioned-custom-hsm-x509-device.png) 

8. デバイス ID が `custom-hsm-device-02` である 2 つ目のデバイスについて、手順 1 から 7 を繰り返します。 このデバイスには次の値を使用します。

    |   説明                 |  値  |
    | :---------------------------- | :--------- |
    | `COMMON_NAME`                 | `"custom-hsm-device-02"` |
    | 完全な証明書チェーン        | `input="./certs/new-device-02-full-chain.cert.pem"` を使用してテキストを生成します |
    | 秘密キー                   | `input="./private/new-device-02.key.pem"` を使用してテキストを生成します |

    次の出力は、シミュレートされたデバイス `custom-hsm-device-02` が正常に起動し、プロビジョニング サービスに接続した場合の例です。 デバイスは IoT ハブに割り当てられ、登録されています。

    ```cmd
    Provisioning API Version: 1.3.9
    
    Registering Device
    
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    
    Registration Information received from service: test-docs-hub.azure-devices.net, deviceId: custom-hsm-device-02
    Press enter key to exit:
    ```


## <a name="clean-up-resources"></a>リソースをクリーンアップする

テストを実施し、このデバイス クライアント サンプルの確認が完了したら、次の手順に従い、このチュートリアルで作成したリソースをすべて削除します。

1. マシンに表示されているデバイス クライアント サンプルの出力ウィンドウを閉じます。
1. Azure portal の左側のメニューで **[すべてのリソース]** を選択し、Device Provisioning Service を選択します。 サービスの **[登録を管理します]** を開き、 **[登録グループ]** タブを選択します。このチュートリアルで作成したデバイス グループの *グループ名* の隣にあるチェック ボックスを選択して、ペイン上部の **[削除]** を押します。 
1. DPS 内で **[証明書]** をクリックします。 このチュートリアルでアップロードし、検証した証明書それぞれについて、証明書をクリックして **[削除]** をクリックし、削除します。
1. Azure portal の左側のメニューにある **[すべてのリソース]** を選択し、IoT ハブを選択します。 ハブの **IoT デバイス** を開きます。 このチュートリアルで登録したデバイスの *デバイス ID* の隣にあるチェック ボックスを選択します。 ペイン上部にある **[削除]** をクリックします。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、カスタム HSM を使用して IoT ハブに X.509 デバイスをプロビジョニングしました。 IoT デバイスを複数のハブにプロビジョニングする方法を学習するために、次のチュートリアルに進んでください。 

> [!div class="nextstepaction"]
> [チュートリアル: 負荷分散された IoT ハブへのデバイスのプロビジョニング](tutorial-provision-multiple-hubs.md)