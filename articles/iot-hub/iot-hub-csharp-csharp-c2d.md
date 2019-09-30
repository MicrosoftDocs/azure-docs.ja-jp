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
ms.openlocfilehash: 99acd43128bedcf3dba470f84c0a406861d77e2d
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147786"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>デバイスに IoT Hub でクラウドからメッセージを送信する (.NET)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub は、何百万ものデバイスとソリューション バックエンドの間に信頼性のある保護された双方向通信を確立するのに役立つ、フル マネージドのサービスです。 [デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-dotnet.md)に関するクイックスタートには、IoT ハブの作成方法、IoT ハブでデバイス ID をプロビジョニングする方法、および device-to-cloud メッセージを送信するデバイス アプリをコード化する方法が示されています。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

このチュートリアルは、[デバイスから IoT ハブへのテレメトリ送信](quickstart-send-telemetry-dotnet.md)に関する記事に基づいて作成されています。 次のタスクの実行方法について説明します。

* ソリューション バックエンドから IoT Hub を介して単一のデバイスにクラウドからデバイスへのメッセージを送信する。

* クラウドからデバイスへのメッセージをデバイスで受信する。

* IoT Hub からデバイスに送信されたメッセージの配信確認 ("*フィードバック*") を、ソリューション バックエンドから要求する。

cloud-to-device メッセージの詳細については、[IoT Hub における D2C と C2D のメッセージング](iot-hub-devguide-messaging.md)に関するページを参照してください。

このチュートリアルの最後に、次の 2 つの .NET コンソール アプリを実行します。

* **SimulatedDevice**。 このアプリは IoT ハブに接続し、cloud-to-device メッセージを受信します。 このアプリは、「[デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-dotnet.md)」で作成されたアプリの改良版です。

* **SendCloudToDevice**。 このアプリは cloud-to-device メッセージを IoT Hub を介してデバイス アプリに送信し、その配信確認を受け取ります。

> [!NOTE]
> IoT Hub には、[Azure IoT device SDK](iot-hub-devguide-sdks.md) シリーズを介した多数のデバイス プラットフォームや言語 (C、Java、Python、JavaScript など) に対する SDK サポートがあります。 このチュートリアルのコード (一般的には Azure IoT Hub) にデバイスを接続するための詳しい手順については、[IoT Hub 開発者ガイド](iot-hub-devguide.md)を参照してください。
>

## <a name="prerequisites"></a>前提条件

* Visual Studio

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 アカウントがない場合は、 [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を数分で作成することができます。

## <a name="receive-messages-in-the-device-app"></a>デバイス アプリでメッセージを受信する

このセクションでは、「[デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-dotnet.md)」で作成したデバイス アプリを変更し、cloud-to-device メッセージを IoT ハブから受信するようにします。

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

1. **Main** メソッドの `Console.ReadLine()` 行の直前に次のメソッドを追加します。

   ```csharp
   ReceiveC2dAsync();
   ```

`ReceiveAsync` メソッドは、受信したメッセージを、そのメッセージがデバイスによって受信されたとき非同期で返します。 指定可能タイムアウト期間を過ぎると *null* が返されます。 この例では、既定の 1 分が使用されます。 アプリは、*null* を受信したら、新しいメッセージを受信するために待機を続ける必要があります。 `if (receivedMessage == null) continue` 行があるのは、この要件があるためです。

`CompleteAsync()` への呼び出しにより、メッセージが正常に処理されたことが IoT Hub に通知されます。 メッセージはデバイスのキューから安全に削除することができます。 デバイスのアプリケーションがメッセージの処理を完了できなくなる何らかの事態が生じると、IoT Hub はそれをもう一度送信します。 同じメッセージを複数回受信した場合に生成される結果が毎回同じになるように、デバイス アプリ内のメッセージ処理ロジックを*べき等*にする必要があります。

アプリケーションはメッセージを一時的に破棄することもできます。この場合、IoT Hub は将来の使用に備えてメッセージをキュー内に保持します。 または、メッセージを拒否することもできます。この場合、メッセージはキューから完全に削除されます。 cloud-to-device メッセージのライフサイクルの詳細については、[IoT Hub における D2C と C2D のメッセージング](iot-hub-devguide-messaging.md)に関するページを参照してください。

   > [!NOTE]
   > トランスポートとして MQTT や AMQP ではなく HTTPS を使用している場合、`ReceiveAsync` メソッドは即時に返されます。 HTTPS を使用するクラウドからデバイスへのメッセージに関してサポートされているパターンは、メッセージを低頻度 (25 分未満の間隔) でチェックするデバイスに断続的に接続されます。 HTTPS 受信の発行が多くなれば、IoT Hub で要求が調整されます。 MQTT、AMQP、および HTTPS のサポートの相違点と、IoT Hub スロットルの詳細については、[IoT Hub における D2C と C2D のメッセージング](iot-hub-devguide-messaging.md)に関するページを参照してください。
   >

## <a name="get-the-iot-hub-connection-string"></a>IoT ハブ接続文字列を取得する

この記事では、「[デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-dotnet.md)」で作成した IoT ハブを介して cloud-to-device メッセージを送信するバックエンド サービスを作成します。 cloud-to-device メッセージを送信するサービスには、**サービス接続**のアクセス許可が必要となります。 既定では、どの IoT Hub も、このアクセス許可を付与する **service** という名前の共有アクセス ポリシーがある状態で作成されます。

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>C2D メッセージを送信する

次に、デバイスからクラウドへのメッセージを、デバイス アプリに送信する .NET コンソール アプリを作成します。

1. 現在の Visual Studio ソリューションで、**ファイル** > **新規** > 、**プロジェクト** の順に選択します。 **[新しいプロジェクトの作成]** で、 **[コンソール アプリ (.NET Framework)]** を選択してから、 **[次へ]** を選択します。

1. プロジェクトに *SendCloudToDevice*という名前を付けます。 **[ソリューション]** で **[ソリューションに追加]** を選択し、最新版の .NET Framework をそのまま使用します。 **[作成]** を選択してプロジェクトを作成します。

   ![Visual Studio で新しいプロジェクトを構成する](./media/iot-hub-csharp-csharp-c2d/sendcloudtodevice-project-configure.png)

1. ソリューション エクスプローラーで、新しいソリューションを右クリックし、 **[NuGet パッケージの管理]** を選択します。

1. **[NuGet パッケージの管理]** ウィンドウで **[参照]** を選択し、**Microsoft.Azure.Devices** を検索して選択します。 **[インストール]** を選択します。

   この手順により [Azure IoT サービス SDK NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Devices/)がダウンロードされ、インストールされ、パッケージへの参照が追加されます。

1. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。

   ``` csharp
   using Microsoft.Azure.Devices;
   ```

1. **Program** クラスに次のフィールドを追加します。 プレースホルダーの値を、先ほど「[IoT ハブ接続文字列を取得する](#get-the-iot-hub-connection-string)」でコピーしておいた IoT Hub 接続文字列に置き換えます。

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   ```

1. **Program** クラスに次のメソッドを追加します。 [デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-dotnet.md)に関するページでデバイスを定義するときに使用した名前にデバイス名を設定します。

   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync("myFirstDevice", commandMessage);
   }
   ```

   このメソッドは、クラウドからデバイスへの新しいメッセージを ID `myFirstDevice`を持つデバイスに送信します。 [デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-dotnet.md)に関するページで使用したパラメーターに変更を加えた場合にのみ、このパラメーターを変更します。

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

1. **[共通プロパティ]**  >  の **[スタートアップ プロジェクト]** で、 **[マルチ スタートアップ プロジェクト]** を選択してから、**ReadDeviceToCloudMessages**、**SimulatedDevice**、**SendCloudToDevice** の **[開始]** アクションを選択します。 **[OK]** を選択して変更を保存します。

1. **F5**キーを押します。 3 つのすべてのアプリケーションが開始されます。 **[SendCloudToDevice]** ウィンドウを選択して **Enter** キーを押します。 デバイス アプリによってメッセージが受信されていることがわかります。

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

1. **Main** メソッドの `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` の直後に次の行を追加します。

   ``` csharp
   ReceiveFeedbackAsync();
   ```

1. クラウドからデバイスへのメッセージの配信に対するフィードバックを要求するには、**SendCloudToDeviceMessageAsync** メソッドでプロパティを指定する必要があります。 `var commandMessage = new Message(...);` 行の直後に次の行を追加します。

   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

1. **F5**キーを押してアプリを実行します。 3 つすべてのアプリケーションが開始されていることが確認できます。 **[SendCloudToDevice]** ウィンドウを選択して **Enter** キーを押します。 デバイス アプリがメッセージを受信し、その数秒後に、**SendCloudToDevice** アプリケーションがフィードバック メッセージを受信します。

   ![アプリによるメッセージの受信](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用コードでは、「[一時的な障害の処理](/azure/architecture/best-practices/transient-faults)」で推奨されているように、再試行ポリシー (エクスポネンシャル バックオフなど) を実装してください。
>

## <a name="next-steps"></a>次の手順

このハウツー記事では、cloud-to-device メッセージを送受信する方法を学習しました。

IoT Hub を使用する完全なエンド ツー エンド ソリューションの例については、[Azure IoT リモート監視ソリューション アクセラレータ](https://docs.microsoft.com/azure/iot-suite/)に関するページをご覧ください。

IoT Hub を使用したソリューションの開発に関する詳細については、[IoT Hub 開発者ガイド](iot-hub-devguide.md)をご覧ください。
