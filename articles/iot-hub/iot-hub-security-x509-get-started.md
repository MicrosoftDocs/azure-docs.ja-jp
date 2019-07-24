---
title: Azure IoT Hub の X.509 セキュリティのチュートリアル | Microsoft Docs
description: シミュレートされた環境で Azure IoT Hub の X.509 ベースのセキュリティの作業を開始します。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/10/2017
ms.openlocfilehash: 5795cde35d53a64620c4fdb6c3af99a7f56b12d9
ms.sourcegitcommit: e89b9a75e3710559a9d2c705801c306c4e3de16c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2019
ms.locfileid: "59571139"
---
# <a name="set-up-x509-security-in-your-azure-iot-hub"></a>Azure IoT Hub での X.509 セキュリティの設定

このチュートリアルでは、"*X.509 証明書認証*" を使用して Azure IoT Hub をセキュリティで保護するために必要な手順をシミュレートします。 わかりやすくするために、オープン ソース ツールの OpenSSL を使用して Windows マシンのローカルに証明書を作成する方法を紹介します。 このチュートリアルはテスト目的でのみ使用することをお勧めします。 運用環境では、"*ルート証明機関 (CA)*" から証明書を購入する必要があります。

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、次のリソースを用意しておく必要があります。

* ご利用の Azure サブスクリプションで IoT ハブを作成済みであること。 詳細な手順については、[ポータルを使用した IoT ハブの作成](iot-hub-create-through-portal.md)に関するページを参照してください。

* [Visual Studio 2017 または Visual Studio 2019](https://www.visualstudio.com/vs/) をマシンにインストール済みであること。

## <a name="get-x509-ca-certificates"></a>X.509 CA 証明書を入手する

IoT Hub の X.509 証明書ベースのセキュリティでは、[X.509 証明書チェーン](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification)から始める必要があります。これには、ルート証明書に加えて、リーフ証明書までのすべての中間証明書が含まれます。

証明書を取得するために、次のいずれかの方法を選択できます。

* "*ルート証明機関 (CA)*" から X.509 証明書を購入する。 これは運用環境に推奨されます。

* [OpenSSL](https://www.openssl.org/) などのサード パーティ製ツールを使用して、独自の X.509 証明書を作成する。 これは、テストや開発の目的に適しています。 PowerShell または Bash を使用したテスト用 CA 証明書の生成については、「[Managing test CA certificates for samples and tutorials](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)」(サンプルおよびチュートリアルのためのテスト用 CA 証明書の管理) を参照してください。 このチュートリアルの残りの部分では、「[Managing test CA certificates for samples and tutorials](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)」(サンプルおよびチュートリアルのためのテスト用 CA 証明書の管理) の手順に従って生成したテスト用 CA 証明書を使用します。

## <a name="register-x509-ca-certificates-to-your-iot-hub"></a>IoT ハブに X.509 CA 証明書を登録する

次の手順では、ポータルを使用して IoT ハブに新しい証明機関を追加する方法を示します。

1. Azure Portal で、対象の IoT ハブに移動し、**[設定]** > **[証明書]** メニューの順に開きます。

2. **[追加]** をクリックして新しい証明書を追加します。

3. 証明書のわかりやすい表示名を入力します。 前のセクションで作成した *RootCA.cer* という名前のルート証明書ファイルをマシンから選択します。 **[アップロード]** をクリックします。

4. 証明書が正常にアップロードされたことを示す通知が表示されたら、**[保存]** をクリックします。

    ![証明書のアップロード](./media/iot-hub-security-x509-get-started/add-new-cert.png)  

   これにより、**[証明書エクスプローラー]** の一覧に対象の証明書が表示されます。 この証明書の **[状態]** が *[未確認]* になっていることに注意してください。

5. 前の手順で追加した証明書をクリックします。

6. **[証明書の詳細]** ブレードで、**[確認コードを生成します]** をクリックします。

7. 証明書の所有権を確認するための**確認コード**が作成されます。 このコードをクリップボードにコピーします。

   ![証明書の確認](./media/iot-hub-security-x509-get-started/verify-cert.png)  

8. 次に、X.509 CA 証明書に関連付けられた秘密キーを使用して、この "*確認コード*" に署名する必要があります。これにより、署名が生成されます。 この署名プロセスを実行するには、OpenSSL などのツールを使用できます。 これは、[所有証明](https://tools.ietf.org/html/rfc5280#section-3.1)として知られています。 「[Managing test CA certificates for samples and tutorials](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)」(サンプルおよびチュートリアルのためのテスト用 CA 証明書の管理) の手順 3 では確認コードを生成します。

9. ポータルで、上記の手順 8 の結果の署名を IoT ハブにアップロードします。 Azure Portal の **[証明書の詳細]** ブレードで、**[.pem または .cer の検証証明書ファイル]** に移動し、"_エクスプローラー_" アイコンを使用して署名 (例: PowerShell のサンプル コマンドで作成された *VerifyCert4.cer*) を選択します。

10. 証明書が正常にアップロードされたら、**[確認]** をクリックします。 **[証明書]** ブレードでは、証明書の **[状態]** が **_[確認済み]_** に変わります。 自動的に更新されない場合は、**[更新]** をクリックしてください。

    ![証明書のアップロードの確認](./media/iot-hub-security-x509-get-started/upload-cert-verification.png)  

## <a name="create-an-x509-device-for-your-iot-hub"></a>IoT ハブの X.509 デバイスを作成する

1. Azure Portal で、IoT ハブの **[エクスプローラー] > [IoT デバイス]** ページに移動します。

2. **[+ 追加]** をクリックして新しいデバイスを追加します。

3. **[デバイス ID]** にわかりやすい表示名を指定し、**[認証の種類]** として **_[X.509 CA Signed]\(X.509 CA 署名済み\)_** を選択します。 **[Save]** をクリックします。

   ![ポータルでの X.509 デバイスの作成](./media/iot-hub-security-x509-get-started/create-x509-device.png)

## <a name="authenticate-your-x509-device-with-the-x509-certificates"></a>X.509 証明書を使用して X.509 デバイスを認証する

X.509 デバイスを認証するには、最初に CA 証明書を使用してデバイスに署名する必要があります。 通常、リーフ デバイスの署名は、製造ツールが適宜有効になっている製造工場で行われます。 デバイスがある製造会社から別の製造会社に移動される際に、各製造会社における署名アクションがチェーン内の中間証明書としてキャプチャされます。 最終的な結果は、CA 証明書からデバイスのリーフ証明書までの証明書チェーンとなります。 「[Managing test CA certificates for samples and tutorials](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)」(サンプルおよびチュートリアルのためのテスト用 CA 証明書の管理) の手順 4 ではデバイス証明書を生成します。

次に、IoT ハブに登録された X.509 デバイスをシミュレートする C# アプリケーションを作成する方法を示します。 ここでは、シミュレートされたデバイスからハブに温度と湿度の値を送信します。 このチュートリアルではデバイス アプリケーションのみを作成することに注意してください。 このシミュレートされたデバイスから送信されたイベントに応答を送信する IoT Hub サービス アプリケーションを作成する作業は、読者のための演習として残されています。 この C# アプリケーションでは、「[Managing test CA certificates for samples and tutorials](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)」(サンプルおよびチュートリアルのためのテスト用 CA 証明書の管理) の手順に従っていることを前提としています。

1. Visual Studio で、コンソール アプリケーション プロジェクト テンプレートを使用して、新しい Visual C# Windows クラシック デスクトップ プロジェクトを作成します。 プロジェクトに **SimulateX509Device** という名前を付けます。

   ![Visual Studio で X.509 デバイス プロジェクトを作成する](./media/iot-hub-security-x509-get-started/create-device-project.png)

2. ソリューション エクスプローラーで **SimulateX509Device** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。[NuGet パッケージ マネージャー] ウィンドウで **[参照]** を選択し、**microsoft.azure.devices.client** を検索します。 **[インストール]** を選択して、**Microsoft.Azure.Devices.Client** パッケージをインストールし、使用条件に同意します。 この手順により、パッケージのダウンロードとインストールが実行され、Azure IoT device SDK NuGet パッケージへの参照とその依存関係が追加されます。

   ![Visual Studio でデバイス SDK NuGet パッケージを追加する](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

3. *Program.cs* ファイルの先頭に次のコード行を追加します。

    ```CSharp
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using System.Security.Cryptography.X509Certificates;
    ```

4. **Program** クラス内に次のコード行を追加します。

    ```CSharp
        private static int MESSAGE_COUNT = 5;
        private const int TEMPERATURE_THRESHOLD = 30;
        private static String deviceId = "<your-device-id>";
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();
    ```

     _<your_device_id>_ プレースホルダーの位置に、前のセクションで使用したわかりやすいデバイス名を使用します。

5. 次の関数を使用して、温度と湿度のランダムな数値を生成してそれらの値をハブに送信します。

    ```CSharp
    static async Task SendEvent(DeviceClient deviceClient)
    {
        string dataBuffer;
        Console.WriteLine("Device sending {0} messages to IoTHub...\n", MESSAGE_COUNT);

        for (int count = 0; count < MESSAGE_COUNT; count++)
        {
            temperature = rnd.Next(20, 35);
            humidity = rnd.Next(60, 80);
            dataBuffer = string.Format("{{\"deviceId\":\"{0}\",\"messageId\":{1},\"temperature\":{2},\"humidity\":{3}}}", deviceId, count, temperature, humidity);
            Message eventMessage = new Message(Encoding.UTF8.GetBytes(dataBuffer));
            eventMessage.Properties.Add("temperatureAlert", (temperature > TEMPERATURE_THRESHOLD) ? "true" : "false");
            Console.WriteLine("\t{0}> Sending message: {1}, Data: [{2}]", DateTime.Now.ToLocalTime(), count, dataBuffer);

            await deviceClient.SendEventAsync(eventMessage);
        }
    }
    ```

6. 最後に、**Main** 関数に次のコード行を追加します。このとき、_device-id_、_your-iot-hub-name_、_absolute-path-to-your-device-pfx-file_ の各プレースホルダーを、設定に必要な値に置き換えます。

    ```CSharp
    try
    {
        var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "1234");
        var auth = new DeviceAuthenticationWithX509Certificate("<device-id>", cert);
        var deviceClient = DeviceClient.Create("<your-iot-hub-name>.azure-devices.net", auth, TransportType.Amqp_Tcp_Only);

        if (deviceClient == null)
        {
            Console.WriteLine("Failed to create DeviceClient!");
        }
        else
        {
            Console.WriteLine("Successfully created DeviceClient!");
            SendEvent(deviceClient).Wait();
        }

        Console.WriteLine("Exiting...\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

   このコードは、X.509 デバイスの接続文字列を作成して、IoT ハブに接続します。 正常に接続すると、温度と湿度のイベントをハブに送信し、その応答を待ちます。 
7. このアプリケーションは *.pfx* ファイルにアクセスするため、"*管理者*" モードでの実行が必要な場合があります。 Visual Studio ソリューションをビルドします。 **管理者**として新しいコマンド ウィンドウを開き、このソリューションが含まれているフォルダーに移動します。 ソリューション フォルダー内の *bin/Debug* パスに移動します。 "_管理者_" コマンド ウィンドウからアプリケーション **SimulateX509Device.exe** を実行します。 デバイスが正常にハブに接続し、イベントを送信していることがわかります。 

   ![デバイス アプリを実行する](./media/iot-hub-security-x509-get-started/device-app-success.png)

## <a name="next-steps"></a>次の手順

IoT ソリューションのセキュリティ保護の詳細については、次のリンク先をご覧ください。

* [IoT セキュリティのベスト プラクティス](../iot-fundamentals/iot-security-best-practices.md)

* [IoT セキュリティのアーキテクチャ](../iot-fundamentals/iot-security-architecture.md)

* [IoT デプロイのセキュリティ保護](../iot-fundamentals/iot-security-deployment.md)

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [Azure IoT Edge でエッジ デバイスに AI をデプロイする](../iot-edge/tutorial-simulate-device-linux.md)
