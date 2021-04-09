---
title: .NET を使用してデバイスから Azure IoT Hub にファイルをアップロードする | Microsoft Docs
description: .NET 用 Azure IoT device SDK を使用して、デバイスからクラウドにファイルをアップロードする方法。 アップロードしたファイルは Azure Storage Blob コンテナーに格納されます。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/04/2017
ms.author: robinsh
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: 43cafb8c5efe0581fe7c4136aa41980b3d817be2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99981410"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-net"></a>IoT Hub を使用してデバイスからクラウドにファイルをアップロードする (.NET)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

このチュートリアルは、[IoT Hub を使用した cloud-to-device メッセージの送信](iot-hub-csharp-csharp-c2d.md)に関するページ内のコードに基づいて作成されており、IoT Hub のファイル アップロード機能を使用する方法を説明します。 次の方法について説明します。

* ファイルのアップロードで Azure BLOB URI を使用してデバイスをセキュリティで保護する。

* IoT Hub ファイル アップロード通知を使用して、アプリのバックエンドでのファイルの処理を開始する。

[デバイスから IoT ハブにテレメトリを送信する方法](quickstart-send-telemetry-dotnet.md)のクイックスタートと [IoT Hub で cloud-to-device メッセージを送信する方法](iot-hub-csharp-csharp-c2d.md)のチュートリアルには、IoT Hub のデバイスからクラウドへのメッセージングと cloud-to-device メッセージの基本的な機能が示されています。 [IoT Hub を使用したメッセージ ルーティングの構成](tutorial-routing.md)に関するチュートリアルでは、デバイスからクラウドへのメッセージを Microsoft Azure Blob ストレージに確実に格納する方法について説明しています。 ただし、一部のシナリオでは、デバイスが送信するデータを、IoT Hub が受け付けるデバイスからクラウドへの比較的小さなメッセージには簡単にマップできません。 次に例を示します。

* イメージを含む大きなファイル

* ビデオ

* 高頻度でサンプリングされる振動データ

* 何らかの形式の前処理済みデータ

これらのファイルは通常、[Azure Data Factory](../data-factory/introduction.md) や [Hadoop](../hdinsight/index.yml) スタックなどのツールを使用してクラウドでバッチ処理されます。 デバイスからファイルをアップロードする必要がある場合も、IoT Hub のセキュリティと信頼性を利用できます。

このチュートリアルの最後に、次の 2 つの .NET コンソール アプリを実行します。

* **SimulatedDevice**。 このアプリは、IoT Hub によって提供された SAS URI を使用してストレージにファイルをアップロードします。 これは、[IoT Hub を使用したクラウドからデバイスへのメッセージの送信](iot-hub-csharp-csharp-c2d.md)に関するチュートリアルで作成されたアプリの変更されたバージョンです。

* **ReadFileUploadNotification**。 このアプリは、IoT ハブからのファイル アップロード通知を受信します。

> [!NOTE]
> IoT Hub は、Azure IoT device SDK を通して、多数のデバイス プラットフォームと言語 (C、Java、Python、Javascript を含む) をサポートしています。 Azure IoT Hub にデバイスを接続するための詳しい手順については、[Azure IoT デベロッパー センター](https://azure.microsoft.com/develop/iot)を参照してください。

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>前提条件

* Visual Studio

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 アカウントがない場合は、 [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を数分で作成することができます。

* ポート 8883 がファイアウォールで開放されていることを確認してください。 この記事のデバイス サンプルでは、ポート 8883 を介して通信する MQTT プロトコルを使用しています。 このポートは、企業や教育用のネットワーク環境によってはブロックされている場合があります。 この問題の詳細と対処方法については、「[IoT Hub への接続 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)」を参照してください。

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>デバイス アプリからのファイルのアップロード

このセクションでは、[IoT Hub でクラウドからデバイスへのメッセージ送信](iot-hub-csharp-csharp-c2d.md)で作成したデバイス アプリを変更して、クラウドからデバイスへのメッセージを IoT Hub から受信するようにします。

1. Visual Studio ソリューション エクスプローラーで、 **[SimulatedDevice]** プロジェクトを右クリックし、 **[追加]**  >  **[既存の項目]** の順に選択します。 イメージ ファイルを見つけ、それをプロジェクトに含めます。 このチュートリアルでは、イメージ名が `image.jpg`という前提で説明します。

1. イメージを右クリックし、 **[プロパティ]** を選択します。 **[出力ディレクトリにコピー]** が **[常にコピーする]** に設定されていることを確認します。

    ![[出力ディレクトリにコピー] の画像のプロパティを更新する場所を示します](./media/iot-hub-csharp-csharp-file-upload/image-properties.png)

1. **Program.cs** ファイルの先頭に、次のステートメントを追加します。

    ```csharp
    using System.IO;
    ```

1. **Program** クラスに次のメソッドを追加します。

    ```csharp
    private static async Task SendToBlobAsync(string fileName)
    {
        Console.WriteLine("Uploading file: {0}", fileName);
        var watch = System.Diagnostics.Stopwatch.StartNew();

        await deviceClient.GetFileUploadSasUriAsync(new FileUploadSasUriRequest { BlobName = fileName });
        var blob = new CloudBlockBlob(sas.GetBlobUri());
        await blob.UploadFromFileAsync(fileName);
        await deviceClient.CompleteFileUploadAsync(new FileUploadCompletionNotification { CorrelationId = sas.CorrelationId, IsSuccess = true });

        watch.Stop();
        Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
    }
    ```

    `UploadToBlobAsync` メソッドは、アップロードするファイルのファイル名とストリーム ソースを取り込み、ストレージへのアップロードを処理します。 コンソール アプリには、ファイルのアップロードにかかる時間が表示されます。

1. **Main** メソッドの `Console.ReadLine()` の直前に次の行を追加します。

    ```csharp
    await SendToBlobAsync("image.jpg");
    ```

> [!NOTE]
> わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用コードでは、「[一時的な障害の処理](/azure/architecture/best-practices/transient-faults)」で推奨されているように、再試行ポリシー (エクスポネンシャル バックオフなど) を実装してください。

## <a name="get-the-iot-hub-connection-string"></a>IoT ハブ接続文字列を取得する

この記事では、[デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-dotnet.md)に関するページで作成した IoT ハブからのファイル アップロード通知メッセージを受信するバックエンド サービスを作成します。 ファイル アップロード通知メッセージを受信するサービスには、**サービス接続** のアクセス許可が必要となります。 既定では、どの IoT Hub も、このアクセス許可を付与する **service** という名前の共有アクセス ポリシーがある状態で作成されます。

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>ファイル アップロードの通知の受信

このセクションでは、IoT Hub からファイル アップロードの通知メッセージを受信する .NET コンソール アプリケーションを作成します。

1. 現在の Visual Studio ソリューションで、**ファイル** > **新規** > 、**プロジェクト** の順に選択します。 **[新しいプロジェクトの作成]** で、 **[コンソール アプリ (.NET Framework)]** を選択してから、 **[次へ]** を選択します。

1. プロジェクトに *ReadFileUploadNotification* という名前を付けます。 **[ソリューション]** で、 **[ソリューションに追加]** を選択します。 **[作成]** を選択してプロジェクトを作成します。

    ![Visual Studio で ReadFileUploadNotification プロジェクトを構成する](./media/iot-hub-csharp-csharp-file-upload/read-file-upload-project-configure.png)

1. ソリューション エクスプローラーで、 **[ReadFileUploadNotification]** プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。

1. **NuGet パッケージ マネージャー** で、 **[参照]** を選択します。 **[Microsoft.Azure.Devices]** を検索して選択した後、 **[インストール]** を選択します。

    この手順によって [Azure IoT サービス SDK NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Devices/)がダウンロードしてインストールされ、その参照が **ReadFileUploadNotification** プロジェクトに追加されます。

1. このプロジェクトの **Program.cs** ファイルで、ファイルの先頭に次のステートメントを追加します。

    ```csharp
    using Microsoft.Azure.Devices;
    ```

1. **Program** クラスに次のフィールドを追加します。 プレースホルダー `{iot hub connection string}` の値を、先ほど「[IoT ハブ接続文字列を取得する](#get-the-iot-hub-connection-string)」でコピーしておいた IoT ハブ接続文字列に置き換えます。

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

1. **Program** クラスに次のメソッドを追加します。

    ```csharp
    private async static void ReceiveFileUploadNotificationAsync()
    {
        var notificationReceiver = serviceClient.GetFileNotificationReceiver();

        Console.WriteLine("\nReceiving file upload notification from service");
        while (true)
        {
            var fileUploadNotification = await notificationReceiver.ReceiveAsync();
            if (fileUploadNotification == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received file upload notification: {0}", 
              string.Join(", ", fileUploadNotification.BlobName));
            Console.ResetColor();

            await notificationReceiver.CompleteAsync(fileUploadNotification);
        }
    }
    ```

    この受信パターンは、クラウドからデバイスへのメッセージをデバイス アプリから受信するために使用するものと同じであることに注意してください。

1. 最後に、**Main** メソッドに次の行を追加します。

    ```csharp
    Console.WriteLine("Receive file upload notifications\n");
    serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
    ReceiveFileUploadNotificationAsync();
    Console.WriteLine("Press Enter to exit\n");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>アプリケーションの実行

これで、アプリケーションを実行する準備が整いました。

1. ソリューション エクスプローラーで、ソリューションを右クリックし、 **[スタートアップ プロジェクトの設定]** を選択します。

1. **[共通プロパティ]**  >  **[スタートアップ プロジェクト]** で、 **[マルチ スタートアップ プロジェクト]** を選択してから、 **[ReadFileUploadNotification]** と **[SimulatedDevice]** の **[開始]** アクションを選択します。 **[OK]** を選択して変更を保存します。

1. **F5** キーを押します。 両方のアプリケーションが開始されます。 1 つのコンソール アプリケーションでアップロードの完了が表示され、もう 1 つのコンソール アプリケーションでアップロード通知メッセージが受信されます。 [Azure Portal](https://portal.azure.com/) または Visual Studio サーバー エクスプローラーを使用して、Azure Storage アカウントにアップロードされたファイルがあるかどうかを確認できます。

    ![出力の表示画面のスクリーンショット](./media/iot-hub-csharp-csharp-file-upload/run-apps1.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、IoT Hub のファイル アップロード機能を使用して、デバイスからのファイルのアップロードを簡素化する方法を学習しました。 IoT Hub の機能やシナリオを次の記事で引き続き調べることができます。

* [プログラムによる IoT Hub の作成](iot-hub-rm-template-powershell.md)

* [C SDK の概要](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [Azure IoT Edge でエッジ デバイスに AI をデプロイする](../iot-edge/quickstart-linux.md)