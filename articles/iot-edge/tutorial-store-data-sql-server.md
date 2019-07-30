---
title: 'チュートリアル: SQL モジュールを使用してデータを格納する - Azure IoT Edge | Microsoft Docs'
description: SQL Server モジュールを使用して IoT Edge デバイスでデータをローカルに格納する方法について説明します
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: b77b44856e9623235051470bc087885765ee12c9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080431"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>チュートリアル:SQL Server データベースを使用したエッジでのデータの格納

Azure IoT Edge を実行している Linux デバイスにデータを格納するための SQL Server モジュールをデプロイします。

Azure IoT Edge と SQL Server を使用し、エッジでデータを格納してクエリを実行します。 Azure IoT Edge には、デバイスがオフラインになった場合にメッセージをキャッシュして、接続が再確立されるとそれらのメッセージを転送するという基本のストレージ機能があります。 しかし、ローカルでデータのクエリを実行する機能など、より高度なストレージ機能が必要になる場合があります。 お使いの IoT Edge デバイスは、IoT Hub への接続を維持する必要なく、ローカルなデータベースを使用してより複雑なコンピューティングを実行できます。 

この記事では、SQL Server データベースを IoT Edge デバイスにデプロイするための手順を説明します。 IoT Edge デバイスで実行される Azure Functions は受信データを構造化してデータベースに送信します。 この記事の手順は、MySQL、PostgreSQL などのコンテナーで動作するその他のデータベースにも適用できます。

このチュートリアルでは、以下の内容を学習します。 

> [!div class="checklist"]
> * Visual Studio Code を使用して、Azure 関数を作成する
> * SQL データベースを IoT Edge デバイスに配置する
> * Visual Studio Code を使用してモジュールを作成し、それらを IoT Edge デバイスに配置する
> * 生成されたデータを表示する

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、前のチュートリアルを完了して、Linux コンテナー開発用の開発環境を設定しておく必要があります。[Linux のデバイス用の IoT Edge モジュールを開発する](tutorial-develop-for-linux.md)。 このチュートリアルを完了すると、次の前提条件が満たされます。 

* Azure の Free レベルまたは Standard レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。
* [Azure IoT Edge を実行している Linux デバイス](quickstart-linux.md)
* コンテナー レジストリ ([Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) など)。
* [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) を使用して構成された [Visual Studio Code](https://code.visualstudio.com/)。
* Linux コンテナーを実行するように構成された [Docker CE](https://docs.docker.com/install/)。

このチュートリアルでは、Azure Functions モジュールを使用して SQL Server にデータを送信します。 Azure Functions を使用して IoT Edge モジュールを開発するには、開発用マシンに次の追加の前提条件をインストールします。 

* [Visual Studio Code 用の C# (OmniSharp を使用) 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。 
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)。 

## <a name="create-a-function-project"></a>関数プロジェクトを作成する

データベースにデータを送信するには、データを正しく構造化してテーブルに格納できるモジュールが必要です。 

### <a name="create-a-new-project"></a>新しいプロジェクトを作成する

次の手順では、Visual Studio Code と Azure IoT Tools を使用して、IoT Edge 関数を作成する方法を説明します。

1. Visual Studio Code を開きます。

2. **[表示]**  >  **[コマンド パレット]** を選択して、VS Code コマンド パレットを開きます。

3. コマンド パレットで、**Azure IoT Edge: New IoT Edge solution** コマンドを入力して実行します。 コマンド パレットで、次の情報を指定してソリューションを作成します。 

   | フィールド | 値 |
   | ----- | ----- |
   | フォルダーの選択 | VS Code によってソリューション ファイルが作成される、開発マシン上の場所を選択します。 |
   | Provide a solution name (ソリューション名の指定) | **SqlSolution** のように、ソリューションのわかりやすい名前を入力するか、既定値をそのまま使用します。 |
   | Select module template (モジュール テンプレートの選択) | **[Azure Functions - C#]** を選択します。 |
   | Provide a module name (モジュール名の指定) | モジュールに **sqlFunction** という名前を付けます。 |
   | Provide Docker image repository for the module (モジュールの Docker イメージ リポジトリの指定) | イメージ リポジトリには、コンテナー レジストリの名前とコンテナー イメージの名前が含まれます。 コンテナー イメージは、前の手順で事前設定されます。 **localhost:5000** を、Azure コンテナー レジストリのログイン サーバーの値に置き換えます。 Azure portal で、コンテナー レジストリの概要ページからログイン サーバーを取得できます。 <br><br>文字列は最終的に、\<レジストリ名\>.azurecr.io/sqlfunction のようになります。 |

   VS Code ウィンドウによって、ご自身の IoT Edge ソリューション ワークスペースが読み込まれます。 
   
### <a name="add-your-registry-credentials"></a>レジストリ資格情報を追加する

コンテナー レジストリの資格情報は、環境ファイルに格納され、IoT Edge ランタイムと共有されます。 ランタイムでご自身のプライベート イメージを IoT Edge デバイスにプルするとき、ランタイムではこれらの資格情報が必要になります。

1. VS Code エクスプローラーで、.env ファイルを開きます。
2. ご自身の Azure コンテナー レジストリからコピーした**ユーザー名**と**パスワード**の値を使用して、フィールドを更新します。
3. このファイルを保存します。

### <a name="select-your-target-architecture"></a>ターゲット アーキテクチャを選択する

現在、Visual Studio Code では、Linux AMD64 および Linux ARM32v7 デバイス用の C モジュールを開発できます。 ソリューションごとにターゲットとするアーキテクチャを選択する必要があります。これは、アーキテクチャの種類によって、コンテナーのビルド方法と実行方法が異なるためです。 既定値は Linux AMD64 です。 

1. コマンド パレットを開き、次を検索します: 「**Azure IoT Edge: Set Default Target Platform for Edge Solution (Azure IoT Edge: Edge ソリューションの既定のターゲット プラットフォームの設定)** 」。または、ウィンドウの下部にあるサイド バーで、ショートカット アイコンを選択します。 

2. コマンド パレットで、オプションの一覧からターゲット アーキテクチャを選択します。 このチュートリアルでは、Ubuntu 仮想マシンを IoT Edge デバイスとして使用するため、既定値の **amd64** のままにします。 

### <a name="update-the-module-with-custom-code"></a>カスタム コードでモジュールを更新する

1. VS Code エクスプローラーで、 **[モジュール]**  >  **[sqlFunction]**  >  **[sqlFunction.cs]** の順に開きます。

2. ファイルの内容全体を次のコードに置き換えます。

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Client;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;
   using Newtonsoft.Json;
   using Sql = System.Data.SqlClient;

   namespace Functions.Samples
   {
       public static class sqlFunction
       {
           [FunctionName("sqlFunction")]
           public static async Task FilterMessageAndSendMessage(
               [EdgeHubTrigger("input1")] Message messageReceived,
               [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
               ILogger logger)
           {
               const int temperatureThreshold = 20;
               byte[] messageBytes = messageReceived.GetBytes();
               var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

               if (!string.IsNullOrEmpty(messageString))
               {
                   logger.LogInformation("Info: Received one non-empty message");
                   // Get the body of the message and deserialize it.
                   var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                   //Store the data in SQL db
                   const string str = "<sql connection string>";
                   using (Sql.SqlConnection conn = new Sql.SqlConnection(str))
                   {
                       conn.Open();
                       var insertMachineTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'machine', " + messageBody.machine.temperature + ");";
                       var insertAmbientTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'ambient', " + messageBody.ambient.temperature + ");"; 
                       using (Sql.SqlCommand cmd = new Sql.SqlCommand(insertMachineTemperature + "\n" + insertAmbientTemperature, conn))
                       {
                           //Execute the command and log the # rows affected.
                           var rows = await cmd.ExecuteNonQueryAsync();
                           logger.LogInformation($"{rows} rows were updated");
                       }
                   }

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threashold.
                       var filteredMessage = new Message(messageBytes);
                       // Copy the properties of the original message into the new Message object.
                       foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                       {filteredMessage.Properties.Add(prop.Key, prop.Value);}
                       // Add a new property to the message to indicate it is an alert.
                       filteredMessage.Properties.Add("MessageType", "Alert");
                       // Send the message.       
                       await output.AddAsync(filteredMessage);
                       logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                   }
               }
           }
       }
       //Define the expected schema for the body of incoming messages.
       class MessageBody
       {
           public Machine machine {get; set;}
           public Ambient ambient {get; set;}
           public string timeCreated {get; set;}
       }
       class Machine
       {
           public double temperature {get; set;}
           public double pressure {get; set;}         
       }
       class Ambient
       {
           public double temperature {get; set;}
           public int humidity {get; set;}         
       }
   }
   ```

3. 行 35 で、文字列 **\<sql connection string\>** を次の文字列に置き換えます。 **Data Source** プロパティは SQL Server コンテナーを参照します。これはまだ存在しませんが、次のセクションで **SQL** という名前で作成します。 

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

4. **sqlFunction.cs** ファイルを保存します。 

5. **sqlFunction.csproj** ファイルを開きます。

6. パッケージ参照のグループを見つけ、SqlClient をインクルードするための新しいものを追加します。 

   ```csproj
   <PackageReference Include="System.Data.SqlClient" Version="4.5.1"/>
   ```

7. **sqlFunction.csproj** ファイルを保存します。

## <a name="add-the-sql-server-container"></a>SQL Server コンテナーの追加

[配置マニフェスト](module-composition.md)では、IoT Edge ランタイムによって IoT Edge デバイスにインストールされるモジュールを宣言します。 前のセクションではカスタム関数モジュールを作成するコードを追加しましたが、SQL Server モジュールは既に構築済みであり、Azure Marketplace で入手できます。 IoT Edge ランタイムがそれを含み、デバイス上で構成するよう指定する必要があります。 

1. Visual Studio Code で、 **[表示]**  >  **[コマンド パレット]** を選択してコマンド パレットを開きます。

2. コマンド パレットで、**Azure IoT Edge: Add IoT Edge module** コマンドを入力して実行します。 コマンド パレットで、次の情報を入力して新しいモジュールを追加します。 

   | フィールド | 値 | 
   | ----- | ----- |
   | Select deployment template file (配置テンプレート ファイルの選択) | コマンド パレットで、現在のソリューション フォルダー内の deployment.template.json ファイルが強調表示されます。 そのファイルを選択します。  |
   | Select module template (モジュール テンプレートの選択) | **[Module from Azure Marketplace]\(Azure Marketplace のモジュール\)** を選択します。 |

3. Azure IoT Edge モジュール マーケットプレースで、**SQL Server モジュール**を検索して選択します。 

4. モジュール名を **sql** (すべて小文字) に変更します。 この名前は、sqlFunction.cs ファイル内の接続文字列で宣言されているコンテナー名と一致します。 

5. **[インポート]** を選択して、モジュールを自分のソリューションに追加します。 

6. ソリューション フォルダー内の **deployment.template.json** ファイルを開きます。 

7. **modules** セクションを探します。 3 つのモジュールが見つかります。 モジュール *tempSensor* は、既定で新しいソリューションに含まれ、他のモジュールで使用するテスト データを提供します。 モジュール *sqlFunction* は、最初に作成して新しいコードで更新したモジュールです。 最後のモジュール *sql* は、Azure Marketplace からインポートしたモジュールです。 

   >[!Tip]
   >SQL Server モジュールでは、配置マニフェストの環境変数に既定のパスワードが付いています。 運用環境で SQL Server コンテナーを作成するときに、[既定のシステム管理者パスワードを変更](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker)する必要があります。

8. **deployment.template.json** ファイルを閉じます。

## <a name="build-your-iot-edge-solution"></a>IoT Edge ソリューションのビルド

これまでのセクションでは、1 つのモジュールと共にソリューションを作成して、別のモジュールを配置マニフェスト テンプレートに追加しました。 SQL Server モジュールは Microsoft によって公式にホストされていますが、自分でコードを Functions モジュールにコンテナー化する必要があります。 このセクションでは、ソリューションを構築し、sqlFunction モジュールのコンテナー イメージを作成して、そのイメージをコンテナー レジストリにプッシュします。 

1. Visual Studio Code で、 **[表示]**  >  **[ターミナル]** の順に選択して、統合ターミナルを開きます。  

1. Visual Studio Code でコンテナー レジストリにサインインします。これで、イメージをレジストリにプッシュできます。 .env ファイルに追加したのと同じ Azure Container Registry (ACR) 資格情報を使用します。 統合ターミナルで次のコマンドを入力します。

    ```csh/sh
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```
    
    --password-stdin パラメーターの使用を推奨するセキュリティ警告が表示される場合があります。 このパラメーターの使用について、ここでは説明していませんが、このベスト プラクティスに従うことをお勧めします。 詳細については、[docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) コマンドのリファレンスを参照してください。 

2. VS Code エクスプローラーで、**deployment.template.json** ファイルを右クリックし、 **[Build and Push IoT Edge solution]\(IoT Edge ソリューションのビルドとプッシュ\)** を選択します。 

ソリューションのビルドを指示すると、最初に Visual Studio Code によって配置テンプレートの情報が読み取られて、**config** という名前の新しいフォルダーに deployment.json ファイルが生成されます。次に、`docker build` と `docker push` の 2 つのコマンドが統合ターミナルで実行されます。 これらの 2 つのコマンドによって、コードがビルドされ、モジュールがコンテナー化されたうえで、ソリューションを初期化したときに指定したコンテナー レジストリにコードがプッシュされます。 

sqlFunction モジュールがコンテナー レジストリに正常にプッシュされたことを確認できます。 Azure ポータルで、自分のコンテナー レジストリに移動します。 **[リポジトリ]** を選択し、**sqlFunction** を検索します。 他の 2 つのモジュール tempSensor と sql は、既に Microsoft レジストリ内でそれらのリポジトリがポイントされているため、コンテナーのレジストリにプッシュされません。

## <a name="deploy-the-solution-to-a-device"></a>デバイスへのソリューションの配置

IoT ハブを通じてデバイスにモジュールを設定できますが、Visual Studio Code を通じて IoT ハブとデバイスにアクセスすることもできます。 このセクションでは、IoT ハブへのアクセスを設定してから、VS Code を使用してソリューションを IoT Edge デバイスに配置します。 

1. VS Code エクスプローラーで、 **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションを展開します。 

2. 配置でターゲットにするデバイスを右クリックし、 **[Create deployment for single device]\(単一デバイスのデプロイの作成\)** を選択します。 

3. エクスプローラーで、ソリューション内の **config** フォルダーに移動して、**deployment.amd64** を選択します。 **[Select Edge Deployment Manifest]\(Edge 配置マニフェストの選択\)** をクリックします。 

   deployment.template.json ファイルを配置マニフェストとして使用しないでください。

デプロイが成功すると、確認メッセージが VS Code の出力に表示されます。 

VS Code の Azure IoT Hub Devices セクションのデバイスの状態を更新します。 新しいモジュールが一覧表示されます。コンテナーがインストールされ、開始されてから数分間実行されると、レポートが開始されます。 デバイス上ですべてのモジュールが実行されているのを確認することもできます。 IoT Edge デバイスで、次のコマンドを実行してモジュールの状態を表示します。 

   ```cmd/sh
   iotedge list
   ```

## <a name="create-the-sql-database"></a>SQL データベースの作成

配置マニフェストをデバイスに適用すると、3 つのモジュールが実行されます。 tempSensor モジュールによって、シミュレートされた環境データが生成されます。 sqlFunction モジュールによってデータが取得され、データベース用にその書式が設定されます。 このセクションでは、温度データを格納するように SQL データベースを設定する手順を説明します。 

IoT Edge デバイスで次のコマンドを実行します。 これらのコマンドによって、デバイス上で実行されている **sql** モジュールに接続され、送信された温度データを保持するデータベースとテーブルが作成されます。 

1. IoT Edge デバイス上のコマンドライン ツールで、データベースに接続します。 
      ```bash
      sudo docker exec -it sql bash
      ```

2. SQL コマンド ツールを開きます。
      ```bash
      /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
      ```

3. データベースを作成します。 
      ```sql
      CREATE DATABASE MeasurementsDB
      ON
      (NAME = MeasurementsDB, FILENAME = '/var/opt/mssql/measurementsdb.mdf')
      GO
      ```

4. テーブルを定義します。

   ```sql
   CREATE TABLE MeasurementsDB.dbo.TemperatureMeasurements (measurementTime DATETIME2, location NVARCHAR(50), temperature FLOAT)
   GO
   ```

SQL Server が複数の IoT Edge デバイスにデプロイされるよう自動的に設定するように SQL Server ドッカー ファイルをカスタマイズします。 詳しくは、[Microsoft SQL Server コンテナーのデモ プロジェクト](https://github.com/twright-msft/mssql-node-docker-demo-app)に関する記事を参照してください。 

## <a name="view-the-local-data"></a>ローカル データの表示

テーブルが作成されると、IoT Edge デバイス上にあるローカル SQL Server 2017 データベースへのデータの格納が、sqlFunction モジュールによって開始されます。 

SQL コマンド ツール内から次のコマンドを実行して、書式設定されたテーブル データを表示します。 

   ```sql
   SELECT * FROM MeasurementsDB.dbo.TemperatureMeasurements
   GO
   ```

   ![ローカル データベースのコンテンツを表示する](./media/tutorial-store-data-sql-server/view-data.png)



## <a name="clean-up-resources"></a>リソースのクリーンアップ

次の推奨記事に進む場合は、作成したリソースおよび構成を維持して、再利用することができます。 また、同じ IoT Edge デバイスをテスト デバイスとして使用し続けることもできます。 

それ以外の場合は、課金されないようにするために、ローカル構成と、この記事で作成した Azure リソースを削除してもかまいません。 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>次の手順

このチュートリアルでは、IoT Edge デバイスで生成された生データをフィルター処理するコードを含む、Azure Functions モジュールを作成しました。 独自のモジュールをビルドする準備ができたら、[Visual Studio Code 用の Azure IoT Edge で Azure 関数を開発する](how-to-develop-csharp-function.md)方法の詳細をご覧ください。 

エッジで別の格納方法を試す場合は、IoT Edge 上の Azure Blob Storage を使用する方法に関する記事を参照してください。 

> [!div class="nextstepaction"]
> [IoT Edge 上の Azure Blob Storage を使用してエッジにデータを格納する](how-to-store-data-blob.md)
