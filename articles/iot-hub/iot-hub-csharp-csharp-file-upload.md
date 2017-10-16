---
title: ".NET を使用してデバイスから Azure IoT Hub にファイルをアップロードする | Microsoft Docs"
description: ".NET 用 Azure IoT device SDK を使用して、デバイスからクラウドにファイルをアップロードする方法。 アップロードしたファイルは Azure Storage Blob コンテナーに格納されます。"
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 4759d229-f856-4526-abda-414f8b00a56d
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/04/2017
ms.author: elioda
ms.openlocfilehash: f0be85d661807f36890fc79090c3f820ae8c376d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-using-net"></a>.NET を使用して IoT Hub でデバイスからクラウドにファイルをアップロードする

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

このチュートリアルは、 [IoT Hub を使用したクラウドからデバイスへのメッセージの送信](iot-hub-csharp-csharp-c2d.md) に関するページ内のコードに基づいて作成されており、IoT Hub のファイル アップロード機能を使用する方法を説明します。 次の方法について説明します。

- ファイルのアップロードで Azure BLOB URI を使用してデバイスをセキュリティで保護する。
- IoT Hub ファイル アップロード通知を使用して、アプリのバックエンドでのファイルの処理を開始する。

[IoT Hub の概要](iot-hub-csharp-csharp-getstarted.md)と [IoT Hub を使用したクラウドからデバイスへのメッセージの送信](iot-hub-csharp-csharp-c2d.md)に関するチュートリアルには、IoT Hub のデバイスからクラウドへのメッセージングとクラウドからデバイスへのメッセージングの基本的な機能が示されています。 [デバイスからクラウドへのメッセージを処理する](iot-hub-csharp-csharp-process-d2c.md)チュートリアルでは、Azure Blob Storage にデバイスからクラウドへのメッセージを確実に格納する方法を説明しています。 ただし、一部のシナリオでは、デバイスから送信されるデータを、IoT Hub が受け取る、クラウドからデバイスへの比較的小さなメッセージにマッピングすることは簡単ではありません。 For example:

* イメージを含む大きなファイル
* ビデオ
* 高頻度でサンプリングされる振動データ
* 何らかの形式の前処理済みデータ

これらのファイルは通常、[Azure Data Factory](../data-factory/introduction.md) や [Hadoop](../hdinsight/index.md) スタックなどのツールを使用してクラウドでバッチ処理されます。 デバイスからファイルをアップロードする必要がある場合も、IoT Hub のセキュリティと信頼性を利用できます。

このチュートリアルの最後に、次の 2 つの .NET コンソール アプリを実行します。

* **SimulatedDevice**: 「[チュートリアル: IoT Hub と .Net でクラウドからデバイスへのメッセージを送信する方法](iot-hub-csharp-csharp-c2d.md)」で作成したアプリの変更済みバージョンです。 このアプリは、IoT Hub によって提供された SAS URI を使用してストレージにファイルをアップロードします。
* **ReadFileUploadNotification**: IoT Hub からファイル アップロード通知を受信します。

> [!NOTE]
> IoT Hub は、Azure IoT device SDK を介して多数のデバイス プラットフォームや言語 (C、Java、Javascript など) をサポートしています。 Azure IoT Hub にデバイスを接続するための詳しい手順については、[Azure IoT デベロッパー センター]を参照してください。

このチュートリアルを完了するには、以下が必要です。

* Visual Studio 2015 または Visual Studio 2017
* アクティブな Azure アカウント。 (アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成できます)。

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>デバイス アプリからのファイルのアップロード

このセクションでは、[IoT Hub を使用したクラウドからデバイスへのメッセージの送信](iot-hub-csharp-csharp-c2d.md)に関するチュートリアルで作成したデバイス アプリを変更して、クラウドからデバイスへのメッセージを IoT Hub から受信するようにします。

1. Visual Studio で **SimulatedDevice** プロジェクトを右クリックして **[追加]** をクリックし、**[既存の項目]** をクリックします。 イメージ ファイルに移動し、プロジェクトに含めます。 このチュートリアルでは、イメージ名が `image.jpg`という前提で説明します。

1. イメージを右クリックし、 **[プロパティ]**をクリックします。 **[出力ディレクトリにコピー]** が **[常にコピーする]** に設定されていることを確認します。

    ![][1]

1. **Program.cs** ファイルの先頭に、次のステートメントを追加します。

    ```csharp
    using System.IO;
    ```

1. **Program** クラスに次のメソッドを追加します。

    ```csharp
    private static async void SendToBlobAsync()
    {
        string fileName = "image.jpg";
        Console.WriteLine("Uploading file: {0}", fileName);
        var watch = System.Diagnostics.Stopwatch.StartNew();

        using (var sourceData = new FileStream(@"image.jpg", FileMode.Open))
        {
            await deviceClient.UploadToBlobAsync(fileName, sourceData);
        }

        watch.Stop();
        Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
    }
    ```

    `UploadToBlobAsync` メソッドは、アップロードするファイルのファイル名とストリーム ソースを取り込み、ストレージへのアップロードを処理します。 コンソール アプリには、ファイルのアップロードにかかる時間が表示されます。

1. **Main** メソッドの `Console.ReadLine()` 行の直前に次のメソッドを追加します。

    ```csharp
    SendToBlobAsync();
    ```

> [!NOTE]
> わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用環境のコードでは、MSDN の記事「 [Transient Fault Handling (一時的な障害の処理)]」で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。

## <a name="receive-a-file-upload-notification"></a>ファイル アップロードの通知の受信

このセクションでは、IoT Hub からファイル アップロードの通知メッセージを受信する .NET コンソール アプリケーションを作成します。

1. 現在の Visual Studio ソリューションで、 **コンソール アプリケーション** プロジェクト テンプレートを使用して、Visual C# Windows プロジェクトを作成します。 プロジェクトに **ReadFileUploadNotification**という名前を付けます。

    ![Visual Studio での新しいプロジェクト][2]

1. ソリューション エクスプローラーで **ReadFileUploadNotification** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。

1. **[NuGet パッケージ マネージャー]** ウィンドウで **Microsoft.Azure.Devices** を検索し、**[インストール]** をクリックして使用条件に同意します。

    この操作によって、パッケージのダウンロードとインストールが実行され、[Azure IoT サービス SDK NuGet パッケージ]への参照が **ReadFileUploadNotification** プロジェクトに追加されます。

1. **Program.cs** ファイルの先頭に、次のステートメントを追加します。

    ```csharp
    using Microsoft.Azure.Devices;
    ```

1. **Program** クラスに次のフィールドを追加します。 プレースホルダーの値を、IoT Hub の概要に関するページで取得した IoT Hub の接続文字列に置き換えます。

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
            Console.WriteLine("Received file upload noticiation: {0}", string.Join(", ", fileUploadNotification.BlobName));
            Console.ResetColor();

            await notificationReceiver.CompleteAsync(fileUploadNotification);
        }
    }
    ```

    この受信パターンは、クラウドからデバイスへのメッセージをデバイス アプリから受信するために使用するものと同じであることに注意してください。

1. 最後に、 **Main** メソッドに次の行を追加します。

    ```csharp
    Console.WriteLine("Receive file upload notifications\n");
    serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
    ReceiveFileUploadNotificationAsync();
    Console.WriteLine("Press Enter to exit\n");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>アプリケーションの実行

これで、アプリケーションを実行する準備が整いました。

1. Visual Studio でソリューションを右クリックし、 **[スタートアップ プロジェクトの設定]**を選択します。 **[マルチ スタートアップ プロジェクト]** を選択してから、**ReadFileUploadNotification** と **SimulatedDevice** に対して **[開始]** アクションを選択します。

1. **F5**キーを押します。 両方のアプリケーションが開始されます。 1 つのコンソール アプリケーションでアップロードの完了が表示され、もう 1 つのコンソール アプリケーションでアップロード通知メッセージが受信されます。 [Azure Portal] または Visual Studio サーバー エクスプローラーを使用して、Azure Storage アカウントにアップロードされたファイルがあるかどうかを確認できます。

    ![][50]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、IoT Hub のファイル アップロード機能を使用して、デバイスからのファイルのアップロードを簡素化する方法を学習しました。 次の記事で IoT Hub の機能やシナリオをさらに詳しく調べることができます。

* [プログラムによる IoT Hub の作成][lnk-create-hub]
* [C SDK の概要][lnk-c-sdk]
* [Azure IoT SDK][lnk-sdks]

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [IoT Edge を使用したデバイスのシミュレーション][lnk-iotedge]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/file-upload-project-csharp1.png

<!-- Links -->

[Azure Portal]: https://portal.azure.com/

[Azure IoT デベロッパー センター]: http://www.azure.com/develop/iot

[Transient Fault Handling (一時的な障害の処理)]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure IoT サービス SDK NuGet パッケージ]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: iot-hub-windows-iot-edge-simulated-device.md
