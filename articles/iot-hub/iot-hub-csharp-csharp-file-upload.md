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
ms.openlocfilehash: 79288f2204030790b2308905d90ff8e035fe2dd9
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621864"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-using-net"></a>.NET を使用して IoT Hub でデバイスからクラウドにファイルをアップロードする

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

このチュートリアルは、[IoT Hub を使用した cloud-to-device メッセージの送信](iot-hub-csharp-csharp-c2d.md)に関するページ内のコードに基づいて作成されており、IoT Hub のファイル アップロード機能を使用する方法を説明します。 次の方法について説明します。

* ファイルのアップロードで Azure BLOB URI を使用してデバイスをセキュリティで保護する。

* IoT Hub ファイル アップロード通知を使用して、アプリのバックエンドでのファイルの処理を開始する。

[デバイスから IoT ハブにテレメトリを送信する方法](quickstart-send-telemetry-dotnet.md)のクイックスタートと [IoT Hub で cloud-to-device メッセージを送信する方法](iot-hub-csharp-csharp-c2d.md)のチュートリアルには、IoT Hub のデバイスからクラウドへのメッセージングと cloud-to-device メッセージの基本的な機能が示されています。 「[IoT Hub を使用してメッセージ ルーティングを構成する](tutorial-routing.md)」チュートリアルでは、Azure Blob Storage にデバイスからクラウドへのメッセージを確実に格納する方法を説明しています。 ただし、一部のシナリオでは、デバイスから送信されるデータを、IoT Hub が受け取る、クラウドからデバイスへの比較的小さなメッセージにマッピングすることは簡単ではありません。 例:

* イメージを含む大きなファイル
* ビデオ
* 高頻度でサンプリングされる振動データ
* 何らかの形式の前処理済みデータ

これらのファイルは通常、[Azure Data Factory](../data-factory/introduction.md) や [Hadoop](../hdinsight/index.yml) スタックなどのツールを使用してクラウドでバッチ処理されます。 デバイスからファイルをアップロードする必要がある場合も、IoT Hub のセキュリティと信頼性を利用できます。

このチュートリアルの最後に、次の 2 つの .NET コンソール アプリを実行します。

* **SimulatedDevice**、[IoT Hub で cloud-to-device メッセージを送信する方法](iot-hub-csharp-csharp-c2d.md)のチュートリアルで作成したアプリの変更されたバージョンです。 このアプリは、IoT Hub によって提供された SAS URI を使用してストレージにファイルをアップロードします。

* **ReadFileUploadNotification**: IoT Hub からファイル アップロード通知を受信します。

> [!NOTE]
> IoT Hub は、Azure IoT device SDK を介して多数のデバイス プラットフォームや言語 (C、Java、Javascript など) をサポートしています。 Azure IoT Hub にデバイスを接続するための詳しい手順については、[Azure IoT デベロッパー センター](https://azure.microsoft.com/develop/iot)を参照してください。

このチュートリアルを完了するには、以下が必要です。

* Visual Studio

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 (アカウントがない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を数分で作成することができます)。

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>デバイス アプリからのファイルのアップロード

このセクションでは、[IoT Hub でクラウドからデバイスへのメッセージ送信](iot-hub-csharp-csharp-c2d.md)で作成したデバイス アプリを変更して、クラウドからデバイスへのメッセージを IoT Hub から受信するようにします。

1. Visual Studio で **SimulatedDevice** プロジェクトを右クリックして **[追加]** をクリックし、 **[既存の項目]** をクリックします。 イメージ ファイルに移動し、プロジェクトに含めます。 このチュートリアルでは、イメージ名が `image.jpg`という前提で説明します。

1. イメージを右クリックし、 **[プロパティ]** をクリックします。 **[出力ディレクトリにコピー]** が **[常にコピーする]** に設定されていることを確認します。

    ![[出力ディレクトリにコピー] の画像のプロパティを更新する場所を示します](./media/iot-hub-csharp-csharp-file-upload/image-properties.png)

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
> わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用環境のコードでは、「[一時的な障害の処理](/azure/architecture/best-practices/transient-faults)」の記事で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。

## <a name="receive-a-file-upload-notification"></a>ファイル アップロードの通知の受信

このセクションでは、IoT Hub からファイル アップロードの通知メッセージを受信する .NET コンソール アプリケーションを作成します。

1. 現在の Visual Studio ソリューションで、 **コンソール アプリケーション** プロジェクト テンプレートを使用して、Visual C# Windows プロジェクトを作成します。 プロジェクトに **ReadFileUploadNotification**という名前を付けます。

    ![Visual Studio での新しいプロジェクト](./media/iot-hub-csharp-csharp-file-upload/file-upload-project-csharp1.png)

2. ソリューション エクスプローラーで **ReadFileUploadNotification** プロジェクトを右クリックし、 **[NuGet パッケージの管理]** をクリックします。

3. **[NuGet パッケージ マネージャー]** ウィンドウで **Microsoft.Azure.Devices** を検索し、 **[インストール]** をクリックして使用条件に同意します。

    この操作によって、パッケージのダウンロードとインストールが実行され、[Azure IoT サービス SDK NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Devices/)への参照が **ReadFileUploadNotification** プロジェクトに追加されます。

4. **Program.cs** ファイルの先頭に、次のステートメントを追加します。

    ```csharp
    using Microsoft.Azure.Devices;
    ```

5. **Program** クラスに次のフィールドを追加します。 プレースホルダーの値は、[デバイスから IoT ハブへの利用統計情報の送信](quickstart-send-telemetry-dotnet.md)に関するページで使用した IoT ハブの接続文字列に置き換えてください。

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

6. **Program** クラスに次のメソッドを追加します。

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

7. 最後に、**Main** メソッドに次の行を追加します。

    ```csharp
    Console.WriteLine("Receive file upload notifications\n");
    serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
    ReceiveFileUploadNotificationAsync();
    Console.WriteLine("Press Enter to exit\n");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>アプリケーションの実行

これで、アプリケーションを実行する準備が整いました。

1. Visual Studio でソリューションを右クリックし、 **[スタートアップ プロジェクトの設定]** を選択します。 **[マルチ スタートアップ プロジェクト]** を選択してから、**ReadFileUploadNotification** と **SimulatedDevice** に対して **[開始]** アクションを選択します。

2. **F5**キーを押します。 両方のアプリケーションが開始されます。 1 つのコンソール アプリケーションでアップロードの完了が表示され、もう 1 つのコンソール アプリケーションでアップロード通知メッセージが受信されます。 [Azure Portal](https://portal.azure.com/) または Visual Studio サーバー エクスプローラーを使用して、Azure Storage アカウントにアップロードされたファイルがあるかどうかを確認できます。

    ![出力の表示画面のスクリーンショット](./media/iot-hub-csharp-csharp-file-upload/run-apps1.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、IoT Hub のファイル アップロード機能を使用して、デバイスからのファイルのアップロードを簡素化する方法を学習しました。 次の記事で IoT Hub の機能やシナリオをさらに詳しく調べることができます。

* [プログラムによる IoT Hub の作成](iot-hub-rm-template-powershell.md)

* [C SDK の概要](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [Azure IoT Edge でエッジ デバイスに AI をデプロイする](../iot-edge/tutorial-simulate-device-linux.md)
