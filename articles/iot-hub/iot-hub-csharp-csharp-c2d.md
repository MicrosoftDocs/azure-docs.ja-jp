---
title: Azure IoT Hub (.NET) を使用したクラウドからデバイスへのメッセージ | Microsoft Docs
description: Azure IoT SDK for .NET を使用して、クラウドからデバイスへのメッセージを Azure IoT Hub からデバイスへ送信する方法。 クラウドからデバイスへのメッセージを受信するためにデバイス アプリを変更し、クラウドからデバイスへのメッセージを送信するためにバックエンド アプリを変更します。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: robinsh
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- devx-track-csharp
ms.openlocfilehash: d8df9884c0104792240d85d9ebd4235ef2a18741
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92142357"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>デバイスに IoT Hub でクラウドからメッセージを送信する (.NET)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub は、何百万ものデバイスとソリューション バックエンドの間に信頼性のある保護された双方向通信を確立するのに役立つ、フル マネージドのサービスです。 [デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-dotnet.md)に関するクイックスタートには、IoT ハブの作成方法、IoT ハブでデバイス ID をプロビジョニングする方法、および device-to-cloud メッセージを送信するデバイス アプリをコード化する方法が示されています。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

このチュートリアルは、[デバイスから IoT ハブへのテレメトリ送信](quickstart-send-telemetry-dotnet.md)に関する記事に基づいて作成されています。 次のタスクの実行方法について説明します。

* ソリューション バックエンドから IoT Hub を介して単一のデバイスにクラウドからデバイスへのメッセージを送信する。

* クラウドからデバイスへのメッセージをデバイスで受信する。

* ソリューション バックエンドから、IoT Hub からデバイスに送信されたメッセージの配信確認 ("*フィードバック*") を要求する。

cloud-to-device メッセージの詳細については、[IoT Hub における D2C と C2D のメッセージング](iot-hub-devguide-messaging.md)に関するページを参照してください。

このチュートリアルの最後に、次の 2 つの .NET コンソール アプリを実行します。

* **SimulatedDevice**。 このアプリは IoT ハブに接続し、cloud-to-device メッセージを受信します。 このアプリは、「[デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-dotnet.md)」で作成されたアプリの改良版です。

* **SendCloudToDevice**。 このアプリは cloud-to-device メッセージを IoT Hub を介してデバイス アプリに送信し、その配信確認を受け取ります。

> [!NOTE]
> IoT Hub には、[Azure IoT device SDK](iot-hub-devguide-sdks.md) シリーズを介した多数のデバイス プラットフォームや言語 (C、Java、Python、JavaScript など) に対する SDK サポートがあります。 このチュートリアルのコード (一般的には Azure IoT Hub) にデバイスを接続するための詳しい手順については、[IoT Hub 開発者ガイド](iot-hub-devguide.md)を参照してください。
>

## <a name="prerequisites"></a>前提条件

* Visual Studio

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 アカウントがない場合は、 [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を数分で作成することができます。

* ポート 8883 がファイアウォールで開放されていることを確認してください。 この記事のデバイス サンプルでは、ポート 8883 を介して通信する MQTT プロトコルを使用しています。 このポートは、企業や教育用のネットワーク環境によってはブロックされている場合があります。 この問題の詳細と対処方法については、「[IoT Hub への接続 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)」を参照してください。

## <a name="receive-messages-in-the-device-app"></a>デバイス アプリでメッセージを受信する

このセクションでは、「[デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-dotnet.md)」で作成したデバイス アプリを変更し、cloud-to-device メッセージを IoT ハブから受信するようにします。

1. Visual Studio の **SimulatedDevice** プロジェクトで、次のメソッドを **SimulatedDevice** クラスに追加します。

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

1. **Main** メソッドの `Console.ReadLine()` 行の直前に次のメソッドを追加します。

   ```csharp
   ReceiveC2dAsync();
   ```

`ReceiveAsync` メソッドは、受信したメッセージを、そのメッセージがデバイスによって受信されたとき非同期で返します。 指定可能タイムアウト期間を過ぎると *null* が返されます。 この例では、既定の 1 分が使用されます。 アプリは、*null* を受信したら、新しいメッセージを受信するために待機を続ける必要があります。 `if (receivedMessage == null) continue` 行があるのは、この要件があるためです。

`CompleteAsync()` の呼び出しによって、メッセージが正常に処理されたこと、およびメッセージはデバイスのキューから安全に削除できることが IoT Hub に通知されます。 デバイスは、使用しているプロトコルに関係なく、処理が正常に完了したときに、このメソッドを呼び出す必要があります。

MQTT ではなく AMQP と HTTPS を使用する場合、デバイスは次の処理もできます。

* メッセージを破棄します。この場合、IoT Hub は将来の使用に備えてメッセージをデバイスのキュー内に保持します。
* メッセージを拒否します。この場合、メッセージはデバイスのキューから完全に削除されます。

デバイスがメッセージを完了、破棄、または拒否することを妨げる問題が発生した場合、IoT Hub は一定のタイムアウト期間が経過した後で、メッセージの配信をキューに入れます。 このような理由から、同じメッセージを複数回受信した場合に生成される結果が毎回同じになるように、デバイス アプリ内のメッセージ処理ロジックを *べき等* にする必要があります。

cloud-to-device メッセージのライフサイクルの詳細など、IoT Hub が cloud-to-device メッセージを処理する方法の詳細については、「[IoT ハブから cloud-to-device メッセージを送信する](iot-hub-devguide-messages-c2d.md)」を参照してください。

> [!NOTE]
> トランスポートとして MQTT や AMQP ではなく HTTPS を使用している場合、`ReceiveAsync` メソッドは即時に返されます。 HTTPS を使用するクラウドからデバイスへのメッセージに関してサポートされているパターンは、メッセージを低頻度 (最小 25 分の間隔) でチェックするデバイスに断続的に接続されます。 HTTPS 受信の発行が多くなれば、IoT Hub で要求が調整されます。 MQTT、AMQP、および HTTPS のサポートの相違点の詳細については、「[cloud-to-device 通信に関するガイダンス](iot-hub-devguide-c2d-guidance.md)」と「[通信プロトコルの選択](iot-hub-devguide-protocols.md)」を参照してください。
>

## <a name="get-the-iot-hub-connection-string"></a>IoT ハブ接続文字列を取得する

この記事では、「[デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-dotnet.md)」で作成した IoT ハブを介して cloud-to-device メッセージを送信するバックエンド サービスを作成します。 cloud-to-device メッセージを送信するサービスには、**サービス接続** のアクセス許可が必要となります。 既定では、どの IoT Hub も、このアクセス許可を付与する **service** という名前の共有アクセス ポリシーがある状態で作成されます。

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>C2D メッセージを送信する

このセクションでは、クラウドからデバイスへのメッセージを、シミュレートされたデバイス アプリに送信する .NET コンソール アプリを作成します。

1. 現在の Visual Studio ソリューションで、**ファイル** > **新規** > 、**プロジェクト** の順に選択します。 **[新しいプロジェクトの作成]** で、 **[コンソール アプリ (.NET Framework)]** を選択してから、 **[次へ]** を選択します。

1. プロジェクトに *SendCloudToDevice* という名前を付けます。 **[ソリューション]** で **[ソリューションに追加]** を選択し、最新版の .NET Framework をそのまま使用します。 **[作成]** を選択してプロジェクトを作成します。

   ![Visual Studio で新しいプロジェクトを構成する](./media/iot-hub-csharp-csharp-c2d/sendcloudtodevice-project-configure.png)

1. ソリューション エクスプローラーで、新しいプロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。

1. **[NuGet パッケージの管理]** ウィンドウで **[参照]** を選択し、**Microsoft.Azure.Devices** を検索して選択します。 **[インストール]** を選択します。

   この手順により [Azure IoT サービス SDK NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Devices/)がダウンロードされ、インストールされ、パッケージへの参照が追加されます。

1. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。

   ``` csharp
   using Microsoft.Azure.Devices;
   ```

1. **Program** クラスに次のフィールドを追加します。 `{iot hub connection string}` プレースホルダーの値を、先ほど「[IoT ハブ接続文字列を取得する](#get-the-iot-hub-connection-string)」で書き留めた IoT Hub 接続文字列に置き換えます。 `{device id}` プレースホルダー値は、[デバイスから IoT Hub へのテレメトリ送信](quickstart-send-telemetry-dotnet.md)に関するクイックスタートで追加したデバイスのデバイス ID で置き換えてください。

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   static string targetDevice = "{device id}";
   ```

1. メッセージをデバイスに送信するために、次のメソッドを **Program** クラスに追加します。

   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync(targetDevice, commandMessage);
   }
   ```

1. 最後に、 **Main** メソッドに次の行を追加します。

   ``` csharp
   Console.WriteLine("Send Cloud-to-Device message\n");
   serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

   Console.WriteLine("Press any key to send a C2D message.");
   Console.ReadLine();
   SendCloudToDeviceMessageAsync().Wait();
   Console.ReadLine();
   ```

1. ソリューション エクスプローラーで、ソリューションを右クリックし、 **[スタートアップ プロジェクトの設定]** を選択します。

1. **[共通プロパティ]**  >  **[スタートアップ プロジェクト]** で **[マルチ スタートアップ プロジェクト]** を選択してから、**SimulatedDevice** および **SendCloudToDevice** の **[開始]** アクションを選択します。 **[OK]** を選択して変更を保存します。

1. **F5** キーを押します。 両方のアプリケーションが開始されます。 **[SendCloudToDevice]** ウィンドウを選択して **Enter** キーを押します。 デバイス アプリによってメッセージが受信されていることがわかります。

   ![デバイス アプリによるメッセージの受信](./media/iot-hub-csharp-csharp-c2d/sendc2d1.png)

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

1. **Main** メソッドの `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` の直後に次の行を追加します。

   ``` csharp
   ReceiveFeedbackAsync();
   ```

1. クラウドからデバイスへのメッセージの配信に対するフィードバックを要求するには、**SendCloudToDeviceMessageAsync** メソッドでプロパティを指定する必要があります。 `var commandMessage = new Message(...);` 行の直後に次の行を追加します。

   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

1. **F5** キーを押してアプリを実行します。 両方のアプリケーションが開始されていることが確認できます。 **[SendCloudToDevice]** ウィンドウを選択して **Enter** キーを押します。 デバイス アプリがメッセージを受信し、その数秒後に、**SendCloudToDevice** アプリケーションがフィードバック メッセージを受信します。

   ![デバイス アプリによるメッセージの受信とサービス アプリによるフィードバックの受信](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用コードでは、「[一時的な障害の処理](/azure/architecture/best-practices/transient-faults)」で推奨されているように、再試行ポリシー (エクスポネンシャル バックオフなど) を実装してください。
>

## <a name="next-steps"></a>次のステップ

このハウツー記事では、cloud-to-device メッセージを送受信する方法を学習しました。

IoT Hub を使用する完全なエンド ツー エンド ソリューションの例については、[Azure IoT リモート監視ソリューション アクセラレータ](/azure/iot-suite/)に関するページをご覧ください。

IoT Hub を使用したソリューションの開発に関する詳細については、[IoT Hub 開発者ガイド](iot-hub-devguide.md)をご覧ください。