---
title: チュートリアル:Azure Digital Twins を使用して空間を監視する | Microsoft Docs
description: このチュートリアルの手順を使用して、Azure Digital Twins を使用して空間リソースをプロビジョニングし、作業条件を監視する方法を学習します。
services: digital-twins
author: dsk-2015
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: dkshir
ms.openlocfilehash: ad6c2625dc56dc3a3155183a04b712122a3b10f1
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57535384"
---
# <a name="tutorial-provision-your-building-and-monitor-working-conditions-with-azure-digital-twins"></a>チュートリアル:Azure Digital Twins を使用して建物をプロビジョニングし、作業環境を監視する

このチュートリアルでは、Azure Digital Twins を使用して、望ましい温度条件と快適度を基準に空間を監視する方法について説明します。 [サンプルの建物を構成](tutorial-facilities-setup.md)した後、このチュートリアルの手順を使用して建物をプロビジョニングし、センサー データに対してカスタム関数を実行できます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 監視する条件を定義する。
> * ユーザー定義関数 (UDF) を作成する。
> * センサー データをシミュレートする。
> * ユーザー定義関数の結果を取得する。

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、[Azure Digital Twins の設定が完了](tutorial-facilities-setup.md)していることを前提としています。 次に進む前に、以下が準備されていることを確認します。

- [Azure アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- Digital Twins のインスタンスが実行中であること。 
- [Digital Twins C# のサンプル](https://github.com/Azure-Samples/digital-twins-samples-csharp)がダウンロードされ、作業マシン上に展開されていること。 
- サンプルをビルドして実行する開発用マシンに [.NET Core SDK バージョン 2.1.403 以降](https://www.microsoft.com/net/download)があること。 適切なバージョンがインストールされていることを確認するには、`dotnet --version` を実行します。 
- [Visual Studio Code](https://code.visualstudio.com/)。サンプル コードを確認するために使用します。 

## <a name="define-conditions-to-monitor"></a>監視する条件を定義する

デバイスまたはセンサーのデータについて監視する特定の条件のセット ("*マッチャー*" と呼ばれます) を定義できます。 その後、"*ユーザー定義関数*" と呼ばれる関数を定義できます。 ユーザー定義関数では、マッチャーによって指定された条件が発生したときに、空間およびデバイスから受け取ったデータに対してカスタム ロジックが実行されます。 詳細については、「[データ処理とユーザー定義関数](concepts-user-defined-functions.md)」を参照してください。 

Visual Studio Code で、**occupancy-quickstart** サンプル プロジェクトの **src\actions\provisionSample.yaml** ファイルを開きます。 型 **matchers** で始まるセクションに注目してください。 この型の各エントリによって、指定された**名前**のマッチャーが作成されます。 このマッチャーによって、型 **dataTypeValue** のセンサーが監視されます。 これが *Focus Room A1* という名前の空間とどのように関係しているかに注目してください。ここには、いくつかのセンサーがある **devices** ノードが含まれています。 これらのセンサーの 1 つを追跡するマッチャーをプロビジョニングするには、その **dataTypeValue** がセンサーの **dataType** と一致している必要があります。 

既存のマッチャーの下に次のマッチャーを追加します。 キーの位置が揃っていて、空白がタブで置き換えられていないことを確認してください。

```yaml
      - name: Matcher Temperature
        dataTypeValue: Temperature
```

このマッチャーは、[最初のチュートリアル](tutorial-facilities-setup.md)で追加した SAMPLE_SENSOR_TEMPERATURE センサーを追跡します。 これらの行は、コメントアウトされた行として *provisionSample.yaml* ファイル内にもあります。 各行の先頭にある `#` 文字を削除するだけでコメント解除することができます。

<a id="udf"></a>

## <a name="create-a-user-defined-function"></a>ユーザー定義関数を作成する

ユーザー定義関数を使用すると、センサー データの処理をカスタマイズできます。 これらは、マッチャーによって記述された特定の条件が発生したときに Azure Digital Twins インスタンス内で実行できるカスタム JavaScript コードです。 マッチャーとユーザー定義関数は、監視したいセンサーごとに作成できます。 詳細については、「[データ処理とユーザー定義関数](concepts-user-defined-functions.md)」を参照してください。 

サンプルの provisionSample.yaml ファイルで、型 **userdefinedfunctions** で始まるセクションを見つけます。 このセクションによって、指定された**名前**のユーザー定義関数がプロビジョニングされます。 この UDF は、**matcherNames** の下のマッチャーの一覧を対象にして動作します。 独自の JavaScript ファイルを**スクリプト**として UDF に提供する方法に注目してください。

さらに、**roleassignments** という名前のセクションに注目してください。 これによって、ユーザー定義関数にスペース管理者ロールが割り当てられます。 このロールでは、すべてのプロビジョニング済み空間から届いたイベントへのアクセスが許可されます。 

1. 温度マッチャーを含めるように UDF を構成します。そのためには、provisionSample.yaml ファイルの `matcherNames` ノードに次の行を追加するか、この行をコメント解除します。

    ```yaml
            - Matcher Temperature
    ```

1. エディターで **src\actions\userDefinedFunctions\availability.js** ファイルを開きます。 これは、provisionSample.yaml の **script** 要素で参照されているファイルです。 このファイル内のユーザー定義関数では、室内でモーションが検出されず、二酸化炭素レベルが 1,000 ppm 未満の条件が検索されます。 

   温度とその他の条件を監視するように JavaScript ファイルを変更します。 室内でモーションが検出されず、二酸化炭素レベルが 1,000 ppm 未満、温度が華氏 78 度未満の条件を検索するために、次のコード行を追加します。

   > [!NOTE]
   > このセクションでは、ユーザー定義関数の 1 つの記述方法を詳しく学習できるように、ファイル *src\actions\userDefinedFunctions\availability.js* を変更しています。 ただし、セットアップ内の [src\actions\userDefinedFunctions\availabilityForTutorial.js](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) ファイルを直接使用してもかまいません。 このファイルには、このチュートリアルに必要なすべての変更が含まれています。 代わりにこのファイルを使用する場合は、[src\actions\provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml) 内の **script** キーに適切なファイル名を使用するようにしてください。

    a. ファイルの先頭で、コメント `// Add your sensor type here` の下に温度用の次の行を追加します。

    ```JavaScript
        var temperatureType = "Temperature";
        var temperatureThreshold = 78;
    ```

    b. `var motionSensor` を定義するステートメントの後ろにあるコメント `// Add your sensor variable here` の下に、次の行を追加します。

     ```JavaScript
        var temperatureSensor = otherSensors.find(function(element) {
            return element.DataType === temperatureType;
        });
    ```

    c. `var carbonDioxideValue` を定義するステートメントの後ろにあるコメント `// Add your sensor latest value here` の下に、次の行を追加します。

    ```JavaScript
        var temperatureValue = getFloatValue(temperatureSensor.Value().Value);
    ```

    d. コメント `// Modify this line to monitor your sensor value` の下にある次のコード行を削除します。

     ```JavaScript
        if(carbonDioxideValue === null || motionValue === null) {
            sendNotification(telemetry.SensorId, "Sensor", "Error: Carbon dioxide or motion are null, returning");
            return;
        }
    ```

    それらを次の行に置き換えます。

    ```JavaScript
        if(carbonDioxideValue === null || motionValue === null || temperatureValue === null){
            sendNotification(telemetry.SensorId, "Sensor", "Error: Carbon dioxide, motion, or temperature are null, returning");
            return;
        }
    ```

    e. コメント `// Modify these lines as per your sensor` の下にある次のコード行を削除します。

    ```JavaScript
        var availableFresh = "Room is available and air is fresh";
        var noAvailableOrFresh = "Room is not available or air quality is poor";
    ```

    それらを次の行に置き換えます。

    ```JavaScript
        var alert = "Room with fresh air and comfortable temperature is available.";
        var noAlert = "Either room is occupied, or working conditions are not right.";
    ```

    f. コメント `// Modify this code block for your sensor` の後ろにある次の *if-else* コード ブロックを削除します。

    ```JavaScript
        // If carbonDioxide less than threshold and no presence in the room => log, notify and set parent space computed value
        if(carbonDioxideValue < carbonDioxideThreshold && !presence) {
            log(`${availableFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
            setSpaceValue(parentSpace.Id, spaceAvailFresh, availableFresh);

            // Set up custom notification for air quality
            parentSpace.Notify(JSON.stringify(availableFresh));
        }
        else {
            log(`${noAvailableOrFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
            setSpaceValue(parentSpace.Id, spaceAvailFresh, noAvailableOrFresh);

            // Set up custom notification for air quality
            parentSpace.Notify(JSON.stringify(noAvailableOrFresh));
        }
    ```

    これを次の *if-else* ブロックに置き換えます。

    ```JavaScript
        // If sensor values are within range and room is available
        if(carbonDioxideValue < carbonDioxideThreshold && temperatureValue < temperatureThreshold && !presence) {
            log(`${alert}. Carbon Dioxide: ${carbonDioxideValue}. Temperature: ${temperatureValue}. Presence: ${presence}.`);

            // log, notify and set parent space computed value
            setSpaceValue(parentSpace.Id, spaceAvailFresh, alert);

            // Set up notification for this alert
            parentSpace.Notify(JSON.stringify(alert));
        }
        else {
            log(`${noAlert}. Carbon Dioxide: ${carbonDioxideValue}. Temperature: ${temperatureValue}. Presence: ${presence}.`);

            // log, notify and set parent space computed value
            setSpaceValue(parentSpace.Id, spaceAvailFresh, noAlert);
        }
    ```

    変更後の UDF では、部屋が使用可能で、二酸化炭素と温度が許容限度内にある状態が検索されます。 この条件が満たされると、`parentSpace.Notify(JSON.stringify(alert));` ステートメントによって通知が生成されます。 条件が満たされているかどうかに関係なく、監視されている空間の値が対応するメッセージと共に設定されます。

    g. ファイルを保存します。

1. コマンド ウィンドウを開き、**occupancy-quickstart\src** フォルダーに移動します。次のコマンドを実行して、空間インテリジェンス グラフおよびユーザー定義関数をプロビジョニングします。

    ```cmd/sh
    dotnet run ProvisionSample
    ```

   > [!IMPORTANT]
   > Digital Twins Management API への不正アクセスを防ぐために、**occupancy-quickstart** アプリケーションでは Azure アカウントの資格情報を使用してサインインする必要があります。 資格情報は一時的に保存されるため、実行するたびにサインインする必要はありません。 このプログラムを初めて実行したときと、その後保存された資格情報が期限切れになったときは、アプリケーションによってサインイン ページが表示され、そのページに入力するセッション固有のコードが与えられます。 プロンプトに従って、Azure アカウントを使用してサインインします。

1. アカウントが認証された後、アプリケーションによって、provisionSample.yaml の構成どおりにサンプル空間グラフの作成が開始されます。 プロビジョニングが完了するまで待機します。 これには数分かかります。 その後、コマンド ウィンドウのメッセージを見て、空間グラフがどのように作成されているかを確認します。 アプリケーションによってルート ノードまたは `Venue` に IoT ハブがどのように作成されているかに注目します。

1. コマンド ウィンドウの出力から、`Devices` セクションの `ConnectionString` の値をクリップボードにコピーします。 この値は、次のセクションでデバイス接続をシミュレートするために必要です。

    ![サンプルをプロビジョニングする](./media/tutorial-facilities-udf/run-provision-sample.png)

> [!TIP]
> プロビジョニングの途中で "The I/O operation has been aborted because of either a thread exit or an application request (スレッドの終了またはアプリケーション要求のため I/O 操作が中止されました)" のようなエラー メッセージが表示された場合は、コマンドをもう一度実行してみてください。 これは、ネットワークの問題が原因で HTTP クライアントがタイムアウトしたときに発生する可能性があります。

<a id="simulate"></a>

## <a name="simulate-sensor-data"></a>センサー データをシミュレートする

このセクションでは、サンプル内の *device-connectivity* という名前のプロジェクトを使用します。 モーション、温度、二酸化炭素を検出するためのセンサー データをシミュレートします。 このプロジェクトでは、センサーのランダムな値が生成され、デバイス接続文字列を使用してそれらが IoT ハブに送信されます。

1. 別のコマンド ウィンドウで、Azure Digital Twins サンプルに移動し、**device-connectivity** フォルダーに移動します。

1. 次のコマンドを実行して、プロジェクトの依存関係が正しいことを確認します。

    ```cmd/sh
    dotnet restore
    ```

1. エディターで [appsettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/device-connectivity/appsettings.json) ファイルを開き、次の値を編集します。

   a. **DeviceConnectionString**: 前のセクションの出力ウィンドウに含まれている `ConnectionString` の値を割り当てます。 シミュレーターが IoT ハブに正常に接続できるように、引用符で囲まれたこの文字列を完全にコピーしてください。

   b. **Sensors** 配列内の **HardwareId**: Azure Digital Twins インスタンスにプロビジョニングされたセンサーからのイベントをシミュレートしているので、このファイル内のハードウェア ID とセンサーの名前が provisionSample.yaml ファイルの `sensors` ノードと一致している必要があります。

      温度センサーの新しいエントリを追加します。 appsettings.json の **Sensors** ノードは次のようになります。

      ```JSON
      "Sensors": [{
        "DataType": "Motion",
        "HardwareId": "SAMPLE_SENSOR_MOTION"
      },{
        "DataType": "CarbonDioxide",
        "HardwareId": "SAMPLE_SENSOR_CARBONDIOXIDE"
      },{
        "DataType": "Temperature",
        "HardwareId": "SAMPLE_SENSOR_TEMPERATURE"
      }]
      ```

1. 次のコマンドを実行して、温度、モーション、二酸化炭素に関するデバイス イベントのシミュレーションを開始します。

    ```cmd/sh
    dotnet run
    ```

   > [!NOTE]
   > シミュレーション サンプルは Digital Twins インスタンスと直接通信しないため、認証を行う必要はありません。

## <a name="get-results-of-the-user-defined-function"></a>ユーザー定義関数の結果を取得する

ユーザー定義関数は、インスタンスがデバイスとセンサーのデータを受信するたびに実行されます。 このセクションでは、Azure Digital Twins インスタンスに対するクエリを実行して、ユーザー定義関数の結果を取得します。 部屋が使用可能な場合、空気が新鮮であり温度が適切であることが、ほぼリアルタイムでわかります。 

1. サンプルのプロビジョニングに使用したコマンド ウィンドウを開くか、新しいコマンド ウィンドウを開き、再度サンプルの **occupancy-quickstart\src** フォルダーに移動します。

1. 次のコマンドを実行します。メッセージが表示されたら、サインインします。

    ```cmd/sh
    dotnet run GetAvailableAndFreshSpaces
    ```

出力ウィンドウに、どのようにユーザー定義関数が実行され、デバイス シミュレーションからのイベントがインターセプトされているかが表示されます。 

   ![UDF の出力](./media/tutorial-facilities-udf/udf-running.png)

監視されている条件が満たされると、[前述](#udf)のとおりに、ユーザー定義関数によって空間の値が関連メッセージと共に設定されます。 メッセージは、`GetAvailableAndFreshSpaces` 関数によってコンソールに出力されます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

この時点で Azure Digital Twins の探索を中止する場合は、このチュートリアルで作成されたリソースを削除してかまいません。

1. [Azure portal](https://portal.azure.com) の左側のメニューにある **[すべてのリソース]** をクリックし、目的の Digital Twins リソース グループを選択して **[削除]** を選択します。

    > [!TIP]
    > ご自分の Digital Twins インスタンスの削除で問題が発生していた場合は、サービス更新が修正と共にロールアウトされています。 ご自分のインスタンスの削除を再試行してください。

2. 必要に応じて、作業マシン上のサンプル アプリケーションを削除します。

## <a name="next-steps"></a>次の手順

これで、空間をプロビジョニングし、カスタム通知をトリガーするフレームワークを作成できたので、次のいずれかのチュートリアルに進むことができます。

> [!div class="nextstepaction"]
> [チュートリアル:Logic Apps を使用して Azure Digital Twins 空間から通知を受け取る](tutorial-facilities-events.md)

> [!div class="nextstepaction"]
> [チュートリアル:Time Series Insights を使用して Azure Digital Twins 空間からのイベントを視覚化および分析する](tutorial-facilities-analyze.md)
