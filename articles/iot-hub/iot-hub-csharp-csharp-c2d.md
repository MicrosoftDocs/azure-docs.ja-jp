---
title: Azure IoT Hub (.NET) を使用したクラウドからデバイスへのメッセージ | Microsoft Docs
description: Azure IoT SDK for .NET を使用して、クラウドからデバイスへのメッセージを Azure IoT Hub からデバイスへ送信する方法。 クラウドからデバイスへのメッセージを受信するためにデバイス アプリを変更し、クラウドからデバイスへのメッセージを送信するためにバックエンド アプリを変更します。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: robinsh
ms.openlocfilehash: 629342e44af16b6d23f9ed85f8c5306c807b8bfc
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621894"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>デバイスに IoT Hub でクラウドからメッセージを送信する (.NET)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>はじめに

Azure IoT Hub は、何百万ものデバイスとソリューション バックエンドの間に信頼性のある保護された双方向通信を確立するのに役立つ、フル マネージドのサービスです。 [デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-dotnet.md)に関するクイックスタートには、IoT ハブの作成方法、IoT ハブでデバイス ID をプロビジョニングする方法、および device-to-cloud メッセージを送信するデバイス アプリをコード化する方法が示されています。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

このチュートリアルは、[デバイスから IoT ハブへのテレメトリ送信](quickstart-send-telemetry-dotnet.md)に関する記事に基づいて作成されています。 次の手順の実行方法について説明します。

* ソリューション バックエンドから IoT Hub を介して単一のデバイスにクラウドからデバイスへのメッセージを送信する。

* クラウドからデバイスへのメッセージをデバイスで受信する。

* IoT Hub からデバイスに送信されたメッセージの配信確認 ("*フィードバック*") を、ソリューション バックエンドから要求する。

cloud-to-device メッセージの詳細については、[IoT Hub における D2C と C2D のメッセージング](iot-hub-devguide-messaging.md)に関するページを参照してください。

このチュートリアルの最後に、次の 2 つの .NET コンソール アプリを実行します。

* **SimulatedDevice**: [デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-dotnet.md)に関するページで作成されたアプリの修正版であり、IoT ハブに接続し、cloud-to-device メッセージを受信します。

* **SendCloudToDevice**。cloud-to-device メッセージを IoT Hub を介してデバイス アプリに送信し、その配信確認を受け取ります。

> [!NOTE]
> IoT Hub には、[Azure IoT device SDK](iot-hub-devguide-sdks.md) を介した多数のデバイス プラットフォームや言語 (C、Java、Javascript など) に対する SDK サポートがあります。 このチュートリアルのコード (一般的には Azure IoT Hub) にデバイスを接続するための詳しい手順については、[IoT Hub 開発者ガイド](iot-hub-devguide.md)を参照してください。
>

このチュートリアルを完了するには、以下が必要です。

* Visual Studio

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 (アカウントがない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を数分で作成できます)。

## <a name="receive-messages-in-the-device-app"></a>デバイス アプリでメッセージを受信する

このセクションでは、[デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-dotnet.md)に関するページで作成したデバイス アプリを変更して、cloud-to-device メッセージを IoT ハブから受信するようにします。

1. Visual Studio の **SimulatedDevice** プロジェクトで、次のメソッドを **Program** クラスに追加します。

   ```csharp
    private static async void ReceiveC2dAsync()
    {
        Console.WriteLine("\nReceiving cloud to device messages from service");
        while (true)
        {
            Message receivedMessage = await s_deviceClient.ReceiveAsync();
            if (receivedMessage == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received message: {0}", 
            Encoding.ASCII.GetString(receivedMessage.GetBytes()));
            Console.ResetColor();

            await s_deviceClient.CompleteAsync(receivedMessage);
        }
    }
   ```

   `ReceiveAsync` メソッドは、受信したメッセージを、そのメッセージがデバイスによって受信されたとき非同期で返します。 指定可能なタイムアウト期間が経過したら、*null* が返されます (ここでは、既定の 1 分が使用されます)。 アプリは、*null* を受信したら、新しいメッセージを受信するために待機を続ける必要があります。 `if (receivedMessage == null) continue` 行があるのは、この要件があるためです。

    `CompleteAsync()` への呼び出しにより、メッセージが正常に処理されたことが IoT Hub に通知されます。 メッセージはデバイスのキューから安全に削除することができます。 デバイスのアプリケーションがメッセージの処理を完了できなくなる何らかの事態が生じると、IoT Hub はそれをもう一度送信します。 そのため、同じメッセージを複数回受信した場合に生成される結果が毎回同じになるように、デバイス アプリ内のメッセージ処理ロジックを*べき等*にすることが重要です。 

    アプリケーションはメッセージを一時的に破棄することもできます。この場合、IoT Hub は将来の使用に備えてメッセージをキュー内に保持します。 または、メッセージを拒否することもできます。この場合、メッセージはキューから完全に削除されます。 cloud-to-device メッセージのライフサイクルの詳細については、[IoT Hub における D2C と C2D のメッセージング](iot-hub-devguide-messaging.md)に関するページを参照してください。

   > [!NOTE]
   > トランスポートとして MQTT や AMQP ではなく HTTPS を使用している場合、`ReceiveAsync` メソッドは即時に返されます。 HTTPS を使用するクラウドからデバイスへのメッセージに関してサポートされているパターンは、メッセージを低頻度 (25 分未満の間隔) でチェックするデバイスに断続的に接続されます。 HTTPS 受信の発行が多くなれば、IoT Hub で要求が調整されます。 MQTT、AMQP、および HTTPS のサポートの相違点と、IoT Hub スロットルの詳細については、[IoT Hub における D2C と C2D のメッセージング](iot-hub-devguide-messaging.md)に関するページを参照してください。
   >

2. **Main** メソッドの `Console.ReadLine()` 行の直前に次のメソッドを追加します。

   ```csharp
   ReceiveC2dAsync();
   ```

## <a name="get-the-iot-hub-connection-string"></a>IoT ハブ接続文字列を取得する

最初に、ポータルから IoT ハブ接続文字列を取得します。

1. [Azure portal](https://portal.azure.com) にサインインし、 **[リソース グループ]** を選択します。

2. このハウツー記事で使用しているリソース グループを選択します。

3. 使用している IoT ハブを選択します。

4. ハブ用のウィンドウで、 **[共有アクセス ポリシー]** を選択します。

5. **[iothubowner]** を選びます。 **[iothubowner]** パネルに接続文字列が表示されます。 **[接続文字列--主キー]** のコピー アイコンを選択します。 後で使用できるように接続文字列を保存します。

   ![IoT ハブ接続文字列を取得する](./media/iot-hub-csharp-csharp-c2d/get-iot-hub-connection-string.png)

## <a name="send-a-cloud-to-device-message"></a>C2D メッセージを送信する

次に、デバイスからクラウドへのメッセージを、デバイス アプリに送信する .NET コンソール アプリを作成します。

1. 現在の Visual Studio ソリューション内で、ソリューションを右クリックし、[追加]、[新しいプロジェクト] の順に選択します。 **[Windows デスクトップ]** 、 **[コンソール アプリ (.NET Framework)]** の順に選択します。 プロジェクトに「**SendCloudToDevice**」という名前を付け、.NET Framework の最新バージョンを選択した後、 **[OK]** を選択してプロジェクトを作成します。

   ![Visual Studio での新しいプロジェクト](./media/iot-hub-csharp-csharp-c2d/create-identity-csharp1.png)

2. ソリューション エクスプローラーでソリューションを右クリックし、 **[ソリューション用 NuGet パッケージの管理]** をクリックします。

   この操作によって、 **[NuGet パッケージの管理]** ウィンドウが開きます。

3. 「**Microsoft.Azure.Devices**」を検索し、[参照] タブを選択します。パッケージが見つかったら、 **[インストール]** をクリックし、利用規約に同意します。

   これによりパッケージのダウンロードとインストールが実行され、[Azure IoT - サービス SDK NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Devices/)への参照が追加されます。

4. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。

   ``` csharp
   using Microsoft.Azure.Devices;
   ```

5. **Program** クラスに次のフィールドを追加します。 プレースホルダーの値を、このセクションで保存した IoT ハブ接続文字列に置き換えます。 

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   ```

6. **Program** クラスに次のメソッドを追加します。 [デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-dotnet.md)に関するページでデバイスを定義するときに使用した名前にデバイス名を設定します。

   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync("myDevice", commandMessage);
   }
   ```

   このメソッドは、クラウドからデバイスへの新しいメッセージを ID `myFirstDevice`を持つデバイスに送信します。 [デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-dotnet.md)に関するページで使用したパラメーターに変更を加えた場合にのみ、このパラメーターを変更します。

7. 最後に、 **Main** メソッドに次の行を追加します。

   ``` csharp
   Console.WriteLine("Send Cloud-to-Device message\n");
   serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

   Console.WriteLine("Press any key to send a C2D message.");
   Console.ReadLine();
   SendCloudToDeviceMessageAsync().Wait();
   Console.ReadLine();
   ```

8. Visual Studio 内でソリューションを右クリックし、 **[スタートアップ プロジェクトの設定]** を選択します。 **[マルチ スタートアップ プロジェクト]** を選択してから、**ReadDeviceToCloudMessages**、**SimulatedDevice**、**SendCloudToDevice** の **[開始]** アクションを選択します。

9. **F5**キーを押します。 3 つのすべてのアプリケーションが開始されます。 **[SendCloudToDevice]** ウィンドウを選択して **Enter** キーを押します。 デバイス アプリによってメッセージが受信されていることがわかります。

   ![アプリによるメッセージの受信](./media/iot-hub-csharp-csharp-c2d/sendc2d1.png)

## <a name="receive-delivery-feedback"></a>配信フィードバックの受信

各 cloud-to-device メッセージに対して IoT Hub からの配信 (または有効期限) 確認を要求することができます。 このオプションによって、ソリューション バックエンドで再試行または補正ロジックを簡単に通知できるようになります。 cloud-to-device フィードバックの詳細については、[IoT Hub における D2C と C2D のメッセージング](iot-hub-devguide-messaging.md)に関するページを参照してください。

このセクションでは、フィードバックを要求し、それを IoT ハブから受信するように、**SendCloudToDevice** アプリを変更します。

1. Visual Studio の **SendCloudToDevice** プロジェクトで、次のメソッドを **Program** クラスに追加します。

   ```csharp
   private async static void ReceiveFeedbackAsync()
   {
        var feedbackReceiver = serviceClient.GetFeedbackReceiver();

        Console.WriteLine("\nReceiving c2d feedback from service");
        while (true)
        {
            var feedbackBatch = await feedbackReceiver.ReceiveAsync();
            if (feedbackBatch == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received feedback: {0}",
              string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
            Console.ResetColor();

            await feedbackReceiver.CompleteAsync(feedbackBatch);
        }
    }
    ```

    この受信パターンは、クラウドからデバイスへのメッセージをデバイス アプリから受信するために使用するものと同じであることに注意してください。

2. **Main** メソッドの `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` 行の直後に次のメソッドを追加します。

   ``` csharp
   ReceiveFeedbackAsync();
   ```

3. クラウドからデバイスへのメッセージの配信に対するフィードバックを要求するには、**SendCloudToDeviceMessageAsync** メソッドでプロパティを指定する必要があります。 `var commandMessage = new Message(...);` 行の直後に次の行を追加します。

   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

4. **F5**キーを押してアプリを実行します。 3 つすべてのアプリケーションが開始されていることが確認できます。 **[SendCloudToDevice]** ウィンドウを選択して **Enter** キーを押します。 デバイス アプリがメッセージを受信し、その数秒後に、**SendCloudToDevice** アプリケーションがフィードバック メッセージを受信します。

   ![アプリによるメッセージの受信](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用環境のコードでは、「[一時的な障害の処理](/azure/architecture/best-practices/transient-faults)」の記事で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。
>

## <a name="next-steps"></a>次の手順

このハウツー記事では、cloud-to-device メッセージを送受信する方法を学習しました。

IoT Hub を使用する完全なエンド ツー エンド ソリューションの例については、[Azure IoT リモート監視ソリューション アクセラレータ](https://docs.microsoft.com/azure/iot-suite/)に関するページをご覧ください。

IoT Hub を使用したソリューションの開発に関する詳細については、[IoT Hub 開発者ガイド](iot-hub-devguide.md)をご覧ください。
