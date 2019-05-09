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
ms.openlocfilehash: a83b8a56a8108f86d868e3420d8368c74fba308a
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2019
ms.locfileid: "58578197"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>チュートリアル:SQL Server データベースを使用したエッジでのデータの格納

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

Azure IoT Edge デバイス:

* [Linux](quickstart-linux.md) 用のクイック スタートに記載された手順に従うことで、Azure 仮想マシンを IoT Edge デバイスとして使用できます。
* SQL Server でサポートされるのは Linux コンテナーのみです。 Windows デバイスを IoT Edge デバイスとして使用し、このチュートリアルを試したい場合は、Linux コンテナーを使用するようにそれを構成する必要があります。 Windows 上の Linux コンテナー向けに IoT Edge ランタイムを構成するための前提条件とインストール手順については、[Windows に Azure IoT Edge ランタイムをインストールする方法](how-to-install-iot-edge-windows.md)に関するページを参照してください。

クラウド リソース:

* Azure の Free レベルまたは Standard レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。 

開発リソース:

* [Visual Studio Code](https://code.visualstudio.com/)。 
* [Visual Studio Code 用の C# (OmniSharp を使用) 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。 
* [Visual Studio Code 用 Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。 
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)。 
* [Docker CE](https://docs.docker.com/install/)。 
  * Windows マシン上で開発している場合は、Docker が [Linux コンテナーを使用するように構成](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)されていることを確認してください。 

## <a name="create-a-container-registry"></a>コンテナー レジストリの作成

このチュートリアルでは、Visual Studio Code 用の Azure IoT Tools を使用してモジュールをビルドし、ファイルから**コンテナー イメージ**を作成します。 その後、このイメージを**レジストリ**にプッシュし、格納および管理します。 最後に、レジストリからイメージを展開し、IoT Edge デバイスで実行します。  

コンテナー イメージは、Docker と互換性のある任意のレジストリを使用して格納できます。 2 つの一般的な Docker レジストリ サービスは、[Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) と [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) です。 このチュートリアルでは、Azure Container Registry を使用します。 

コンテナー レジストリがまだない場合は、次の手順に従って、Azure に新しいコンテナー レジストリを作成します。

1. [Azure portal](https://portal.azure.com) で、**[リソースの作成]** > **[コンテナー]** > **[コンテナー レジストリ]** の順に選択します。

2. コンテナー レジストリを作成するには、以下の値を指定します。

   | フィールド | 値 | 
   | ----- | ----- |
   | レジストリ名 | 一意の名前を指定します。 |
   | サブスクリプション | ドロップダウン リストで、サブスクリプションを選択します。 |
   | リソース グループ | IoT Edge のクイック スタートおよびチュートリアルで作成するすべてのテスト リソースに、同じリソース グループを使用することをお勧めします。 たとえば、**IoTEdgeResources** を使用します。 |
   | Location | 近くの場所を選択します。 |
   | 管理者ユーザー | **[有効]** に設定します。 |
   | SKU | **[Basic]** を選択します。 | 

5. **作成**を選択します。

6. コンテナー レジストリが作成されたら、その場所を参照し、**[アクセス キー]** を選択します。 

7. **ログイン サーバー**、**ユーザー名**、および**パスワード**の値をコピーします。 これらの値は、このチュートリアルで後ほどコンテナー レジストリへのアクセスを提供するために使用します。  

## <a name="create-a-function-project"></a>関数プロジェクトを作成する

データベースにデータを送信するには、データを正しく構造化してテーブルに格納できるモジュールが必要です。 

次の手順では、Visual Studio Code と Azure IoT Tools を使用して、IoT Edge 関数を作成する方法を説明します。

1. Visual Studio Code を開きます。

2. **[表示]** > **[コマンド パレット]** を選択して、VS Code コマンド パレットを開きます。

3. コマンド パレットで、**Azure IoT Edge: New IoT Edge solution** コマンドを入力して実行します。 コマンド パレットで、次の情報を指定してソリューションを作成します。 

   | フィールド | 値 |
   | ----- | ----- |
   | フォルダーの選択 | VS Code によってソリューション ファイルが作成される、開発マシン上の場所を選択します。 |
   | Provide a solution name (ソリューション名の指定) | **SqlSolution** のように、ソリューションのわかりやすい名前を入力するか、既定値をそのまま使用します。 |
   | Select module template (モジュール テンプレートの選択) | **[Azure Functions - C#]** を選択します。 |
   | Provide a module name (モジュール名の指定) | モジュールに **sqlFunction** という名前を付けます。 |
   | Provide Docker image repository for the module (モジュールの Docker イメージ リポジトリの指定) | イメージ リポジトリには、コンテナー レジストリの名前とコンテナー イメージの名前が含まれます。 コンテナー イメージは、前の手順で事前設定されます。 **localhost:5000** を、Azure コンテナー レジストリのログイン サーバーの値に置き換えます。 Azure portal で、コンテナー レジストリの概要ページからログイン サーバーを取得できます。 <br><br>文字列は最終的に、\<レジストリ名\>.azurecr.io/sqlFunction のようになります。 |

   VS Code ウィンドウによって、ご自身の IoT Edge ソリューション ワークスペースが読み込まれます。 
   
4. IoT Edge ソリューション内の \.env ファイルを開きます。 

   新しい IoT Edge ソリューションを作成するたびに、該当するレジストリの資格情報を \.env ファイルで入力するよう VS Code から求められます。 このファイルは git では無視され、IoT Edge 拡張機能が後から、ご利用の IoT Edge デバイスへのレジストリ アクセスを提供するために使用します。 

   前の手順でコンテナー レジストリを指定せずに、既定の localhost:5000 を受け入れた場合、\.env ファイルは作成されません。

5. IoT Edge ランタイムがモジュール イメージにアクセスできるように、.env ファイルでこの IoT Edge ランタイムにレジストリ資格情報を渡します。 **CONTAINER_REGISTRY_USERNAME** と **CONTAINER_REGISTRY_PASSWORD** セクションを探し、等号記号の後に資格情報を挿入します。 

   ```env
   CONTAINER_REGISTRY_USERNAME_yourregistry=<username>
   CONTAINER_REGISTRY_PASSWORD_yourregistry=<password>
   ```

6. .env ファイルを保存します。

7. VS Code エクスプローラーで、**[モジュール]** > **[sqlFunction]** > **[sqlFunction.cs]** の順に開きます。

8. ファイルの内容全体を次のコードに置き換えます。

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

6. 行 35 で、文字列 **\<sql connection string\>** を次の文字列に置き換えます。 **Data Source** プロパティは SQL Server コンテナーを参照します。これはまだ存在しませんが、次のセクションで **SQL** という名前で作成します。 

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

7. **sqlFunction.cs** ファイルを保存します。 

8. **sqlFunction.csproj** ファイルを開きます。

9. パッケージ参照のグループを見つけ、SqlClient をインクルードするための新しいものを追加します。 

   ```csproj
   <PackageReference Include="System.Data.SqlClient" Version="4.5.1"/>
   ```

10. **sqlFunction.csproj** ファイルを保存します。

## <a name="add-the-sql-server-container"></a>SQL Server コンテナーの追加

[配置マニフェスト](module-composition.md)では、IoT Edge ランタイムによって IoT Edge デバイスにインストールされるモジュールを宣言します。 前のセクションではカスタム関数モジュールを作成するコードを追加しましたが、SQL Server モジュールは既に構築済みであり、Azure Marketplace で入手できます。 IoT Edge ランタイムがそれを含み、デバイス上で構成するよう指定する必要があります。 

1. Visual Studio Code で、**[表示]** > **[コマンド パレット]** を選択してコマンド パレットを開きます。

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

1. Visual Studio Code で、**[表示]** > **[ターミナル]** の順に選択して、統合ターミナルを開きます。  

1. Visual Studio Code でコンテナー レジストリにサインインします。これで、イメージをレジストリにプッシュできます。 .env ファイルに追加したのと同じ Azure Container Registry (ACR) 資格情報を使用します。 統合ターミナルで次のコマンドを入力します。

    ```csh/sh
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```
    
    --password-stdin パラメーターの使用を推奨するセキュリティ警告が表示される場合があります。 このパラメーターの使用について、ここでは説明していませんが、このベスト プラクティスに従うことをお勧めします。 詳細については、[docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) コマンドのリファレンスを参照してください。 

2. VS Code エクスプローラーで、**deployment.template.json** ファイルを右クリックし、**[Build and Push IoT Edge solution]\(IoT Edge ソリューションのビルドとプッシュ\)** を選択します。 

ソリューションのビルドを指示すると、最初に Visual Studio Code によって配置テンプレートの情報が読み取られて、**config** という名前の新しいフォルダーに deployment.json ファイルが生成されます。次に、`docker build` と `docker push` の 2 つのコマンドが統合ターミナルで実行されます。 これらの 2 つのコマンドによって、コードがビルドされ、モジュールがコンテナー化されたうえで、ソリューションを初期化したときに指定したコンテナー レジストリにコードがプッシュされます。 

sqlFunction モジュールがコンテナー レジストリに正常にプッシュされたことを確認できます。 Azure ポータルで、自分のコンテナー レジストリに移動します。 **[リポジトリ]** を選択し、**sqlFunction** を検索します。 他の 2 つのモジュール tempSensor と sql は、既に Microsoft レジストリ内でそれらのリポジトリがポイントされているため、コンテナーのレジストリにプッシュされません。

## <a name="deploy-the-solution-to-a-device"></a>デバイスへのソリューションの配置

IoT ハブを通じてデバイスにモジュールを設定できますが、Visual Studio Code を通じて IoT ハブとデバイスにアクセスすることもできます。 このセクションでは、IoT ハブへのアクセスを設定してから、VS Code を使用してソリューションを IoT Edge デバイスに配置します。 

1. VS Code コマンド パレットで、**[Azure IoT Hub: Select IoT Hub]\(Azure IoT Hub: IoT ハブの選択\)** を選びます。

2. プロンプトに従って Azure アカウントにサインインします。 

3. コマンド パレットで、Azure サブスクリプション、IoT ハブの順に選択します。 

4. VS Code エクスプローラーで、**[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションを展開します。 

5. 配置でターゲットにするデバイスを右クリックし、**[Create deployment for single device]\(単一デバイスのデプロイの作成\)** を選択します。 

   ![単一デバイスのデプロイを作成する](./media/tutorial-store-data-sql-server/create-deployment.png)

6. エクスプローラーで、ソリューション内の **config** フォルダーに移動して、**deployment.amd64** を選択します。 **[Select Edge Deployment Manifest]\(Edge 配置マニフェストの選択\)** をクリックします。 

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

引き続き次のチュートリアルを実行すると、Azure IoT Edge を利用して、エッジでデータをビジネス上の分析情報に変える他の方法について学習できます。

> [!div class="nextstepaction"]
> [C# コードを使用してセンサー データをフィルター処理する](tutorial-csharp-module.md)
