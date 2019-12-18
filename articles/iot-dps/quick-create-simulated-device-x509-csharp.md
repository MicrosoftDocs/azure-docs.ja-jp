---
title: シミュレートされた X.509 デバイスを C# を使用して Azure IoT Hub にプロビジョニングする
description: クイックスタート - Azure IoT Hub Device Provisioning Service (DPS) 対応の C# デバイス SDK を使用して、シミュレートされた X.509 デバイスを作成してプロビジョニングします。 このクイック スタートでは、個別登録を使用します。
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: 639ab07113a0e62cac43af5b79f052da1efd93ab
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976521"
---
# <a name="quickstart-create-and-provision-a-simulated-x509-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>クイック スタート:IoT Hub Device Provisioning Service 対応の C# デバイス SDK を使用して、シミュレートされた X.509 デバイスを作成してプロビジョニングする

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

これらの手順では、[C# 用 の Azure IoT サンプル](https://github.com/Azure-Samples/azure-iot-samples-csharp)を使用して、Windows OS を実行中の開発用コンピューター上で X.509 デバイスをシミュレートします。 サンプルでは、Device Provisioning Service を使用して、シミュレートされたデバイスを IoT ハブに接続する操作も行います。

自動プロビジョニングの処理に慣れていない場合は、「[自動プロビジョニングの概念](concepts-auto-provisioning.md)」も確認してください。 また、先に進む前に、[Azure Portal での IoT Hub Device Provisioning Service の設定](./quick-setup-auto-provision.md)に関するページの手順も済ませておいてください。 

Azure IoT Device Provisioning Service では、次の 2 種類の登録がサポートされています。
- [登録グループ](concepts-service.md#enrollment-group)：複数の関連するデバイスを登録するために使用します。
- [個々の登録](concepts-service.md#individual-enrollment):単一デバイスを登録するために使用します。

この記事では、個別登録の使用方法を示します。

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

<a id="setupdevbox"></a>
## <a name="prepare-the-development-environment"></a>開発環境の準備 

1. お使いのマシンに [.NET Core 2.1 SDK 以降](https://www.microsoft.com/net/download/windows)がインストールされていることを確認します。 

1. マシンに `git` がインストールされ、コマンド ウィンドウからアクセスできる環境変数に追加されていることを確認します。 **Git Bash** (ローカル Git リポジトリと対話する際に使用するコマンドライン アプリ) など、インストールする各種 `git` ツールの最新バージョンについては、[Software Freedom Conservancy の Git クライアント ツール](https://git-scm.com/download/)に関するページを参照してください。 

1. コマンド プロンプトまたは Git Bash を開きます。 C# の GitHub リポジトリ用の Azure IoT サンプルを複製します。
    
    ```cmd
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>自己署名 X.509 デバイス証明書と個々の登録エントリを作成する

このセクションでは、自己署名 X.509 証明書を使用します。次の点に留意することが重要です。

* 自己署名証明書はテスト目的専用であるため、運用環境では使用しないでください。
* 自己署名証明書の既定の有効期限は 1 年間です。

「[Provisioning Device Client Sample - X.509 Attestation](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/X509Sample)」(デバイス クライアントのプロビジョニングの例 - X.509 構成証明) のサンプル コードを使用して、シミュレートされたデバイスの個々の登録エントリで使用する証明書を作成します。


1. コマンド プロンプトで、ディレクトリを X.509 デバイス プロビジョニング サンプルのプロジェクト ディレクトリに変更します。

    ```cmd
    cd .\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample
    ```

2. このサンプル コードは、パスワードで保護された PKCS12 形式のファイル (certificate.pfx) 内に格納された X.509 証明書を使用するように設定されています。 また、このクイックスタートの後半で個々の登録を作成するために、公開キーの証明書ファイル (certificate.cer) が必要です。 自己署名証明書とそれに関連する .cer ファイルと .pfx ファイルを生成するには、次のコマンドを実行します。

    ```cmd
    powershell .\GenerateTestCertificate.ps1
    ```

3. PFX パスワードの入力を求められます。 このパスワードを覚えておいてください。サンプルの実行時に使用する必要があります。

    ![ PFX パスワードを入力する](./media/quick-create-simulated-device-x509-csharp/generate-certificate.png)  


4. Azure portal にサインインし、左側のメニューにある **[すべてのリソース]** を選択してプロビジョニング サービスを開きます。

5. Device Provisioning Service のメニューで、 **[登録を管理します]** を選択します。 **[個々の登録]** タブを選択したうえで、上部にある **[個別登録の追加]** を選択します。 

6. **[登録の追加]** パネルで、次の情報を入力します。
   - ID 構成証明の "*メカニズム*" として **[X.509]** を選択します。
   - *[Primary certificate .pem or .cer file]\(プライマリ証明書 .pem または .cer ファイル\)* の *[ファイルの選択]* を選択し、前の手順で作成した証明書ファイル **certificate.cer** を選択します。
   - **[デバイス ID]** は空白のままにします。 デバイス ID が X.509 証明書の共通名 (CN) **iothubx509device1** に設定されたデバイスがプロビジョニングされます。 これは、個々の登録エントリの登録 ID に使用される名前にもなります。 
   - 必要に応じて、次の情報を入力することができます。
       - プロビジョニング サービスにリンクされた IoT ハブを選択します。
       - **[Initial device twin state]\(初期のデバイス ツインの状態\)** をデバイスの目的の初期構成で更新します。
   - 作業が完了したら、 **[保存]** を押します。 

     [![X.509 構成証明の個々の登録をポータルで追加](./media/quick-create-simulated-device-x509-csharp/device-enrollment.png)](./media/quick-create-simulated-device-x509-csharp/device-enrollment.png#lightbox)
    
   登録に成功すると、 *[個々の登録]* タブの *[登録 ID]* 列に X.509 登録エントリが **iothubx509device1** として表示されます。 

## <a name="provision-the-simulated-device"></a>シミュレーションされたデバイスをプロビジョニングする

1. プロビジョニング サービスの **[概要]** ブレードから、 **_[ID スコープ]_** の値をメモします。

    ![ポータルのブレードから Device Provisioning サービスのエンドポイント情報を抽出](./media/quick-create-simulated-device-x509-csharp/copy-scope.png) 


2. 次のコマンドを入力して、X.509 デバイス プロビジョニング サンプルをビルドして実行します。 `<IDScope>` 値をプロビジョニング サービスの ID スコープに置き換えます。 

    ```cmd
    dotnet run <IDScope>
    ```

3. メッセージに従って、以前に作成した PFX ファイルのパスワードを入力します。 デバイスが起動して Device Provisioning Service に接続し、IoT ハブの情報を取得する動作がシミュレートされるので、そのメッセージに注目してください。 

    ![サンプル デバイスの出力](./media/quick-create-simulated-device-x509-csharp/sample-output.png) 

4. デバイスがプロビジョニングされたことを確認します。 プロビジョニング サービスにリンクされた IoT ハブに対してシミュレートされたデバイスが正常にプロビジョニングされると、ハブの **[IoT デバイス]** ブレードにデバイス ID が表示されます。 

    ![IoT ハブに登録されたデバイス](./media/quick-create-simulated-device-x509-csharp/registration.png) 

    *[Initial device twin state]\(初期のデバイス ツインの状態\)* をデバイスの登録エントリの既定値から変更した場合、デバイスはハブから目的のツインの状態をプルし、それに従って動作することができます。 詳細については、「[IoT Hub のデバイス ツインの理解と使用](../iot-hub/iot-hub-devguide-device-twins.md)」を参照してください。


## <a name="clean-up-resources"></a>リソースのクリーンアップ

引き続きデバイス クライアント サンプルを使用する場合は、このクイックスタートで作成したリソースをクリーンアップしないでください。 使用する予定がない場合は、次の手順を使用して、このクイックスタートで作成したすべてのリソースを削除してください。

1. マシンに表示されているデバイス クライアント サンプルの出力ウィンドウを閉じます。
1. マシンに表示されている TPM シミュレーター ウィンドウを閉じます。
1. Azure portal の左側のメニューで **[すべてのリソース]** を選択し、Device Provisioning Service を選択します。 **[概要]** ブレードの上部で、ペインの上部にある **[削除]** をクリックします。  
1. Azure portal の左側のメニューにある **[すべてのリソース]** を選択し、IoT ハブを選択します。 **[概要]** ブレードの上部で、ペインの上部にある **[削除]** をクリックします。  

## <a name="next-steps"></a>次の手順

このクイックスタートでは、ポータルでシミュレートされた X.509 デバイスを Windows マシン上に作成し、Azure IoT Hub Device Provisioning Service を使用して IoT ハブにプロビジョニングしました。 プログラムで X.509 デバイスを登録する方法については、プログラムによる X.509 デバイスの登録のクイックスタートに進みます。 

> [!div class="nextstepaction"]
> [Azure クイックスタート - X.509 デバイスを Azure IoT Hub Device Provisioning Service に登録する](quick-enroll-device-x509-csharp.md)
