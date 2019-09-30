---
title: Azure IoT Hub の X.509 セキュリティのチュートリアル | Microsoft Docs
description: シミュレートされた環境で Azure IoT Hub の X.509 ベースのセキュリティの作業を開始します。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 03ac9f878f0869ef33d22f50c6bdba4276bd4d3c
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2019
ms.locfileid: "70048322"
---
# <a name="set-up-x509-security-in-your-azure-iot-hub"></a>Azure IoT Hub での X.509 セキュリティの設定

このチュートリアルでは、"*X.509 証明書認証*" を使用して Azure IoT Hub をセキュリティで保護するために必要な手順を示します。 わかりやすくするために、オープン ソース ツールの OpenSSL を使用して Windows マシン上でローカルに証明書を作成します。 このチュートリアルはテスト目的でのみ使用することをお勧めします。 運用環境では、"*ルート証明機関 (CA)* " から証明書を購入する必要があります。

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、次のリソースを用意しておく必要があります。

* ご利用の Azure サブスクリプションで IoT ハブを作成済みであること。 詳細な手順については、[ポータルを使用した IoT ハブの作成](iot-hub-create-through-portal.md)に関するページを参照してください。

* [Visual Studio 2017 または Visual Studio 2019](https://www.visualstudio.com/vs/) がインストールされている。

## <a name="get-x509-ca-certificates"></a>X.509 CA 証明書を入手する

IoT Hub の X.509 証明書ベースのセキュリティでは、[X.509 証明書チェーン](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification)から始める必要があります。これには、ルート証明書に加えて、リーフ証明書までのすべての中間証明書が含まれます。

証明書を取得するために、次のいずれかの方法を選択できます。

* "*ルート証明機関 (CA)* " から X.509 証明書を購入する。 この方法は運用環境に推奨されます。

* [OpenSSL](https://www.openssl.org/) などのサードパーティ製ツールを使用して、独自の X.509 証明書を作成する。 この手法は、テストや開発の目的に適しています。 PowerShell または Bash を使用したテスト用 CA 証明書の生成については、「[Managing test CA certificates for samples and tutorials](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)」(サンプルおよびチュートリアルのためのテスト用 CA 証明書の管理) を参照してください。 このチュートリアルの残りの部分では、「[Managing test CA certificates for samples and tutorials](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)」(サンプルおよびチュートリアルのためのテスト用 CA 証明書の管理) の手順に従って生成したテスト用 CA 証明書を使用します。

* 既存のルート CA 証明書によって署名された [X.509 中間 CA 証明書](iot-hub-x509ca-overview.md#sign-devices-into-the-certificate-chain-of-trust)を生成して、ハブにアップロードします。 中間証明書がアップロードされて検証されると、以下に説明されているように、前述のルート CA 証明書の代わりに使用できます。 OpenSSL などのツール ([openssl req](https://www.openssl.org/docs/manmaster/man1/openssl-req.html) と [openssl ca](https://www.openssl.org/docs/manmaster/man1/openssl-ca.html)) は、中間 CA 証明書の生成と署名に使用することができます。

## <a name="register-x509-ca-certificates-to-your-iot-hub"></a>IoT ハブに X.509 CA 証明書を登録する

次の手順では、ポータルを使用して IoT ハブに新しい証明機関を追加する方法を示します。

1. Azure portal で、対象の IoT ハブに移動し、ハブのために **[設定]**  >  **[証明書]** の順に選択します。

1. **[追加]** を選択して新しい証明書を追加します。

1. **[証明書の名前]** に表示名を入力し、前のセクションで作成した証明書ファイルをコンピューターから選択します。

1. 証明書が正常にアップロードされたことを示す通知が表示されたら、 **[保存]** を選択します。

    ![証明書のアップロード](./media/iot-hub-security-x509-get-started/iot-hub-add-cert.png)  

   証明書は、 **[未確認]** の状態で証明書の一覧に表示されます。

1. 追加した証明書を選択して **[証明書の詳細]** を表示し、 **[確認コードを生成します]** を選択します。

   ![証明書の確認](./media/iot-hub-security-x509-get-started/copy-verification-code.png)  

1. **確認コード**をクリップボードにコピーします。 これは、証明書の所有権を確認するために使用します。

1. 「[サンプルおよびチュートリアルのためのテスト用 CA 証明書の管理](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)」の手順 3. に従います。  この手順により、X.509 CA 証明書に関連付けられている秘密キーを使用して、確認コードが署名されます。これで、署名が生成されます。 この署名プロセスを実行するには、OpenSSL などのツールを使用できます。 このプロセスは、[所有証明](https://tools.ietf.org/html/rfc5280#section-3.1)として知られています。

1. **[証明書の詳細]** の **[.pem または .cer の検証証明書ファイル]** の下で、署名ファイルを探し、開きます。 次に、 **[確認]** を選択します。

   証明書の状態が **[確認済み]** に変わります。 証明書が自動的に更新されない場合は、 **[最新の状態に更新]** を選択します。

## <a name="create-an-x509-device-for-your-iot-hub"></a>IoT ハブの X.509 デバイスを作成する

1. Azure portal で IoT ハブに移動し、 **[エクスプローラー]**  >  **[IoT デバイス]** の順に選択します。

1. **[新規作成]** を選択して新しいデバイスを追加します。

1. **[デバイス ID]** にわかりやすい表示名を入力します。 **[認証の種類]** で **[X.509 CA 署名済み]** を選択し、 **[保存]** を選択します。

   ![ポータルでの X.509 デバイスの作成](./media/iot-hub-security-x509-get-started/new-x509-device.png)

## <a name="authenticate-your-x509-device-with-the-x509-certificates"></a>X.509 証明書を使用して X.509 デバイスを認証する

X.509 デバイスを認証するには、最初に CA 証明書を使用してデバイスに署名する必要があります。 通常、リーフ デバイスの署名は、製造ツールが適宜有効になっている製造工場で行われます。 デバイスがある製造会社から別の製造会社に移動される際に、各製造会社における署名アクションがチェーン内の中間証明書としてキャプチャされます。 結果は、CA 証明書からデバイスのリーフ証明書までの証明書チェーンとなります。 「[Managing test CA certificates for samples and tutorials](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)」(サンプルおよびチュートリアルのためのテスト用 CA 証明書の管理) の手順 4 ではデバイス証明書を生成します。

次に、IoT ハブに登録された X.509 デバイスをシミュレートする C# アプリケーションを作成する方法を示します。 ここでは、シミュレートされたデバイスからハブに温度と湿度の値を送信します。 このチュートリアルではデバイス アプリケーションのみを作成します。 このシミュレートされたデバイスから送信されたイベントに応答を送信する IoT Hub サービス アプリケーションを作成する作業は、読者のための演習として残されています。 この C# アプリケーションでは、「[Managing test CA certificates for samples and tutorials](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)」(サンプルおよびチュートリアルのためのテスト用 CA 証明書の管理) の手順に従っていることを前提としています。

1. Visual Studio を開き、 **[新しいプロジェクトの作成]** を選択して、 **[コンソール アプリ (.NET Framework)]** プロジェクト テンプレートを選択します。 **[次へ]** を選択します。

1. **[新しいプロジェクトの構成]** で、プロジェクトに *SimulateX509Device* という名前を付け、 **[作成]** を選択します。

   ![Visual Studio で X.509 デバイス プロジェクトを作成する](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. ソリューション エクスプローラーで **SimulateX509Device** プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。

1. **[NuGet パッケージ マネージャー]** で **[参照]** を選択し、**Microsoft.Azure.Devices.Client** を検索して選択します。 **[インストール]** を選択します。

   ![Visual Studio でデバイス SDK NuGet パッケージを追加する](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    この手順により、Azure IoT device SDK NuGet パッケージのダウンロードとインストールが実行され、それとその依存関係への参照が追加されます。

1. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。

    ```CSharp
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using System.Security.Cryptography.X509Certificates;
    ```

1. **Program** クラスに以下のフィールドを追加します。

    ```CSharp
        private static int MESSAGE_COUNT = 5;
        private const int TEMPERATURE_THRESHOLD = 30;
        private static String deviceId = "<your-device-id>";
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();
    ```

    _<your_device_id>_ の場所には、前のセクションで使用したわかりやすいデバイス名を使用します。

1. 次の関数を使用して、温度と湿度のランダムな数値を生成してそれらの値をハブに送信します。

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

1. 最後に、**Main** 関数に次のコード行を追加します。このとき、_device-id_、_your-iot-hub-name_、_absolute-path-to-your-device-pfx-file_ の各プレースホルダーを、設定に必要な値に置き換えます。

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

1. アプリケーションを実行します。 このアプリケーションは *.pfx* ファイルにアクセスするため、このアプリを管理者として実行しなければならない場合があります。

   1. Visual Studio ソリューションをビルドします。

   1. **[管理者として実行]** を使用して、新しいコマンド プロンプト ウィンドウを開きます。  

   1. ソリューションが含まれているフォルダーに移動し、ソリューション フォルダー内の *bin/Debug* パスに移動します。

   1. コマンド プロンプトからアプリケーション **SimulateX509Device.exe** を実行します。

   デバイスが正常にハブに接続し、イベントを送信していることがわかります。

   ![デバイス アプリを実行する](./media/iot-hub-security-x509-get-started/device-app-success.png)

## <a name="next-steps"></a>次の手順

IoT ソリューションのセキュリティ保護の詳細については、次のリンク先をご覧ください。

* [IoT セキュリティのベスト プラクティス](../iot-fundamentals/iot-security-best-practices.md)

* [IoT セキュリティのアーキテクチャ](../iot-fundamentals/iot-security-architecture.md)

* [IoT デプロイのセキュリティ保護](../iot-fundamentals/iot-security-deployment.md)

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [Azure IoT Edge でエッジ デバイスに AI をデプロイする](../iot-edge/tutorial-simulate-device-linux.md)
