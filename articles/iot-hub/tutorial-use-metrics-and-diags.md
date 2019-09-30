---
title: Azure IoT ハブに関するメトリックと診断ログを設定して使用する | Microsoft Docs
description: Azure IoT ハブに関するメトリックと診断ログを設定して使用する
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 3/13/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 7349287945a56bb7674e364f515d0b763015ed59
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262314"
---
# <a name="tutorial-set-up-and-use-metrics-and-diagnostic-logs-with-an-iot-hub"></a>チュートリアル:IoT ハブに関するメトリックと診断ログを設定して使用する

IoT Hub ソリューションを運用環境で稼働させる場合、なんらかのメトリックを設定して、診断ログを有効にすることが望ましいです。 そうすれば、問題が発生した場合に、着目すべきデータが手元にあり、問題を診断してそれをより速やかに修正するのに役立ちます。 この記事では、診断ログを有効にする方法と、それらにエラーが含まれていないかを確認する方法について説明します。 また、監視するいくつかのメトリックのほか、メトリックが一定の限度に達したときに作動するアラートも設定します。 たとえば、テレメトリ メッセージの数が特定の上限を超えたときや、使用されたメッセージの数が IoT ハブで 1 日に許容されるメッセージのクォータに近づいたときに自分宛てに電子メールを送信することができます。 

ユース ケースの一例は、ポンプが IoT ハブと通信する IoT デバイスであるガソリン スタンドです。 クレジット カードが確認されて、最終的な取引がデータ ストアに書き込まれます。 IoT デバイスによるハブとの通信およびメッセージの送信が停止した場合、何が起きているかを把握できなければ、解決は非常に困難になります。

このチュートリアルでは、[IoT Hub ルーティング](tutorial-routing.md)に関するページの Azure サンプルを使用して、IoT ハブにメッセージを送信します。

このチュートリアルでは、以下のタスクを実行します。

> [!div class="checklist"]
> * Azure CLI を使用して、IoT ハブ、シミュレートされたデバイス、ストレージ アカウントを作成する。  
> * 診断ログを有効にする。
> * メトリックを有効にする。
> * それらのメトリックのアラートを設定する。 
> * IoT デバイスをシミュレートするアプリをダウンロードして実行し、ハブにメッセージを送信する。 
> * アラートが作動し始めるまでアプリを実行する。 
> * メトリックの結果を表示して診断ログを確認する。 

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

- [Visual Studio](https://www.visualstudio.com/) のインストール。 

- メールを受信できる電子メール アカウント。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-resources"></a>リソースを設定する

このチュートリアルには、IoT ハブ、ストレージ アカウント、シミュレートされた IoT デバイスが必要です。 これらのリソースは、Azure CLI または Azure PowerShell を使って作成できます。 すべてのリソースに同じリソース グループと場所を使います。 最後に、リソース グループを削除することによって、すべてのものを一度に削除できます。

必要な手順は以下のとおりです。

1. [リソース グループ](../azure-resource-manager/resource-group-overview.md)を作成します。 

2. IoT Hub を作成します。

3. Standard_LRS レプリケーションで Standard V1 ストレージ アカウントを作成します。

4. ハブにメッセージを送信するシミュレートされたデバイスのデバイス ID を作成します。 テスト フェーズ用にキーを保存します。

### <a name="set-up-resources-using-azure-cli"></a>Azure CLI を使用してリソースを設定する

次のスクリプトをコピーして Cloud Shell に貼り付けます。 既にログインしているものとすると、スクリプトが 1 行ずつ実行されます。 リソース グループ ContosoResources に新しいリソースが作成されます。

グローバルに一意でなければならない変数には `$RANDOM` が連結されています。 スクリプトが実行され、変数が設定されるときに、ランダムな数値文字列が生成され、固定文字列の末尾に連結されて一意の変数を作ります。

```azurecli-interactive

# This is the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-cli-iot-ext

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
location=westus
resourceGroup=ContosoResources
iotDeviceName=Contoso-Test-Device 

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, so add a random number to the end.
iotHubName=ContosoTestHub$RANDOM
echo "IoT hub name = " $iotHubName

# Create the IoT hub in the Free tier.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku F1 --location $location

# The storage account name must be globally unique, so add a random number to the end.
storageAccountName=contosostoragemon$RANDOM
echo "Storage account name = " $storageAccountName

# Create the storage account.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName 

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

```

>[!NOTE]
>デバイス ID の作成時に、"*No keys found for policy iothubowner of IoT Hub ContosoTestHub (IoT Hub ContosoTestHub のポリシー iothubowner のキーが見つかりません)* " というエラーが発生する場合があります。 このエラーを解決するには、Azure CLI IoT 拡張機能を更新したうえで、スクリプトにある最後の 2 つのコマンドをもう一度実行します。 
>
>拡張機能を更新するコマンドを次に示します。 これをお客様の Cloud Shell インスタンスで実行してください。
>
>```cli
>az extension update --name azure-cli-iot-ext
>```

## <a name="enable-the-diagnostic-logs"></a>診断ログを有効にする 

新しい IoT ハブを作成すると、既定では[診断ログ](../azure-monitor/platform/resource-logs-overview.md)が無効になっています。 このセクションでは、お客様のハブの診断ログを有効にします。

1. まず、お客様のハブがポータルに表示されていない場合は、 **[リソース グループ]** をクリックし、Contoso-Resources というリソース グループをクリックしてください。 表示されたリソースの一覧からハブを選択します。 

2. [IoT Hub] ブレードの **[監視]** セクションを探します。 **[診断設定]** をクリックします。 

   ![[IoT Hub] ブレードの診断設定領域を示すスクリーンショット](./media/tutorial-use-metrics-and-diags/01-diagnostic-settings.png)


3. サブスクリプションとリソース グループが正しいことを確認します。 **[リソースの種類]** の **[すべて選択]** チェック ボックスをオフにし、 **[IoT Hub]** チェック ボックスをオンにします。 ( *[すべて選択]* の横に再びチェックマークが表示されますが、無視してください。) **[リソース]** でハブの名前を選択します。 実際の画面は、次の画像のようになります。 

   ![[IoT Hub] ブレードの診断設定領域を示すスクリーンショット](./media/tutorial-use-metrics-and-diags/02-diagnostic-settings-start.png)

4. 次に、 **[診断をオンにする]** をクリックします。 [診断設定] ウィンドウが表示されます。 お客様の診断ログ設定の名前に「diags-hub」と指定します。

5. **[ストレージ アカウントへのアーカイブ]** をオンにします。 

   ![ストレージ アカウントに診断をアーカイブするための設定を示すスクリーンショット。](./media/tutorial-use-metrics-and-diags/03-diagnostic-settings-storage.png)

    **[構成]** をクリックして **[ストレージ アカウントの選択]** 画面を表示します。次に、右側の項目 (*contosostoragemon*) を選択し、 **[OK]** をクリックして [診断設定] ウィンドウに戻ります。 

   ![ストレージ アカウントに診断ログをアーカイブするための設定を示すスクリーンショット](./media/tutorial-use-metrics-and-diags/04-diagnostic-settings-after-storage.png)

6. **[ログ]** の **[接続]** チェック ボックスと **[デバイス テレメトリ]** チェック ボックスをオンにし、それぞれ **[保有期間 (日)]** を 7 日に設定します。 お客様の [診断設定] 画面は、次の画像のようになります。

   ![最終的な診断ログ設定を示すスクリーンショット](./media/tutorial-use-metrics-and-diags/05-diagnostic-settings-done.png)

7. **[保存]** をクリックして設定を保存します。 [診断設定] ウィンドウを閉じます。

後で診断ログを見ると、デバイスに関する接続と切断のログを確認できます。 

## <a name="set-up-metrics"></a>メトリックを設定する 

次は、メッセージがいつハブに送信されたかを監視するメトリックをいくつか設定します。 

1. IoT ハブの設定ウィンドウで、 **[監視]** セクションの **[メトリック]** オプションをクリックします。

2. 画面の上部にある **[Last 24 hours (Automatic)]\(過去 24 時間 (自動)\)** をクリックします。 表示されるドロップダウンで **[時間の範囲]** に **[過去 4 時間]** を選択し、 **[時間の粒度]** を **[1 分]** (ローカル時刻) に設定します。 **[適用]** をクリックしてこれらの設定を保存してください。 

   ![メトリックの時間設定を示すスクリーンショット](./media/tutorial-use-metrics-and-diags/06-metrics-set-time-range.png)

3. 既定では、メトリック項目が 1 つ存在します。 リソース グループとメトリックの名前空間は、既定値のままにしてください。 **[メトリック]** ドロップダウン リストで **[Telemetry messages sent]\(送信済みテレメトリ メッセージ\)** を選択します。 **[集計]** を **[合計]** に設定します。

   ![送信済みテレメトリ メッセージのメトリックを追加する画面のスクリーンショット。](./media/tutorial-use-metrics-and-diags/07-metrics-telemetry-messages-sent.png)


4. 次に **[メトリックの追加]** をクリックして、グラフにもう 1 つメトリックを追加します。 お客様のリソース グループ (**ContosoTestHub**) を選択します。 **[メトリック]** で、 **[Total number of messages used]\(使用されているメッセージの合計数\)** を選択します。 **[集計]** で **[平均]** を選択します。 

   これで画面には、最小化された *[Telemetry messages sent]\(送信済みテレメトリ メッセージ\)* のメトリックに加え、新しく *[Total number of messages used]\(使用されているメッセージの合計数\)* のメトリックが表示されます。

   ![送信済みテレメトリ メッセージのメトリックを追加する画面のスクリーンショット。](./media/tutorial-use-metrics-and-diags/07-metrics-num-messages-used.png)

   **[ダッシュボードにピン留め]** をクリックします。 これで、再度アクセスできるように、お客様の Azure portal のダッシュボードにピン留めされます。 ダッシュボードにピン留めしなかった場合、お客様の設定は保持されません。

## <a name="set-up-alerts"></a>アラートを設定する

ポータルのハブに移動します。 **[リソース グループ]** をクリックし、*ContosoResources* を選択して、IoT ハブ (*ContosoTestHub*) を選択します。 

IoT Hub はまだ [Azure Monitor のメトリック](/azure/azure-monitor/platform/data-collection#metrics)に移行されていないため、[クラシック アラート](/azure/azure-monitor/platform/alerts-classic.overview)を使用する必要があります。

1. **[監視]** の **[アラート]** をクリックします。これでメインのアラート画面が表示されます。 

   ![クラシック アラートの見つけ方を示すスクリーンショット。](./media/tutorial-use-metrics-and-diags/08-find-classic-alerts.png)

2. ここからクラシック アラートを取得するには、 **[クラシック アラートの表示]** をクリックします。 

    ![クラシック アラート画面を示すスクリーンショット。](./media/tutorial-use-metrics-and-diags/09-view-classic-alerts.png)

    フィールドに入力します。 

    **サブスクリプション**:このフィールドはお客様の現在のサブスクリプションに設定しておきます。

    **ソース**:このフィールドは *[メトリック]* に設定します。

    **[リソース グループ]** :このフィールドは、お客様の現在のリソース グループ *ContosoResources* に設定します。 

    **[リソースの種類]** :このフィールドは [IoT Hub] に設定します。 

    **リソース**:お客様の IoT ハブ *ContosoTestHub* を選択します。

3. 新しいアラートを設定するために、 **[メトリック アラートの追加 (クラシック)]** をクリックします。

    フィールドに入力します。

    **Name**:お客様のアラート ルールの名前を指定します (*telemetry-messages* など)。

    **説明**:お客様のアラートの説明を入力します (「*送信済みテレメトリ メッセージが 1,000 件に達したときのアラート*」など)。 

    **ソース**:これは *[メトリック]* に設定します。

    **[サブスクリプション]** 、 **[リソース グループ]** 、 **[リソース]** は、お客様が **[クラシック アラートの表示]** 画面で選択した値に設定する必要があります。 

    **[メトリック]** を *[Telemetry messages sent]\(送信済みテレメトリ メッセージ\)* に設定します。

    ![送信済みテレメトリ メッセージのクラシック アラートを設定する画面のスクリーンショット。](./media/tutorial-use-metrics-and-diags/10-alerts-add-rule-telemetry-top.png)

4. グラフの後にある次のフィールドを設定します。

   **条件**: *[次の値より大きい]* に設定します。

   **しきい値**: 「1000」に設定します。

   **期間**: *[直近 5 分]* に設定します。

   **通知メールの受信者**: ここにはお客様の電子メール アドレスを入力します。 

   ![アラート画面の下半分を示すスクリーンショット。](./media/tutorial-use-metrics-and-diags/11-alerts-add-rule-bottom.png)

   **[OK]** をクリックして、アラートを保存します。 

5. 次は、 *[Total number of messages used]\(使用されているメッセージの合計数\)* 用に、もう 1 つアラートを設定します。 このメトリックは、使用されたメッセージの数が IoT ハブのクォータに近づきつつあるときにアラートを送信したい場合に役立ちます。これにより、ハブで間もなくメッセージの拒否が開始されることを把握できます。

   **[クラシック アラートの表示]** 画面で、 **[メトリック アラートの追加 (クラシック)]** をクリックし、 **[ルールの追加]** ウィンドウで次のフィールドを設定します。

   **Name**:お客様のアラート ルールの名前を指定します (*number-of-messages-used* など)。

   **説明**:お客様のアラートの説明を入力します (「*クォータに近づきつつあるときのアラート*」など)。

   **ソース**:このフィールドは *[メトリック]* に設定します。

    **[サブスクリプション]** 、 **[リソース グループ]** 、 **[リソース]** は、お客様が **[クラシック アラートの表示]** 画面で選択した値に設定する必要があります。 

    **[メトリック]** を *[Total number of messages used]\(使用されているメッセージの合計数\)* に設定します。

6. グラフの下にある次のフィールドを設定します。

   **条件**: *[次の値より大きい]* に設定します。

   **しきい値**: 「1000」に設定します。

   **期間**: このフィールドは、 *[直近 5 分]* に設定します。 

   **通知メールの受信者**: ここにはお客様の電子メール アドレスを入力します。 

   **[OK]** をクリックして、ルールを保存します。 

5. これで、クラシック アラート ウィンドウに 2 つのアラートが表示されます。 

   ![新しいアラート ルールを含んだクラシック アラート画面を示すスクリーンショット。](./media/tutorial-use-metrics-and-diags/12-alerts-done.png)

6. アラート ウィンドウを閉じます。 
    
    これらの設定により、送信済みメッセージの数が 400 件を超えたときと、使用されているメッセージの合計数がしきい値を超えたときにアラートが作動します。

## <a name="run-simulated-device-app"></a>シミュレートされたデバイス アプリを実行する

スクリプト設定セクションの前半では、IoT デバイスを使ってシミュレートするようにデバイスを設定しました。 このセクションでは、デバイスからクラウドへのメッセージを IoT ハブに送信するデバイスをシミュレートする .NET コンソール アプリをダウンロードします。  

[IoT デバイス シミュレーション](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)のソリューションをダウンロードします。 このリンクにより、いくつかのアプリケーションを含むリポジトリがダウンロードされます。探しているソリューションは、iot-hub/Tutorials/Routing/ にあります。

ソリューション ファイル (SimulatedDevice.sln) をダブルクリックしてコードを Visual Studio で開いた後、Program.cs を開きます。 `{iot hub hostname}` を、IoT ハブのホスト名で置き換えます。 IoT ハブのホスト名の形式は、 **{iot-hub-name}.azure-devices.net** です。 このチュートリアルでのハブのホスト名は、**ContosoTestHub.azure-devices.net** です。 次に、`{device key}` を、シミュレートされたデバイスを設定するときに保存したデバイス キーに置き換えます。 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>実行してテストする 

Program.cs で、`Task.Delay` を 1000 から 10 に変更します。これでメッセージの送信間隔が 1 秒から 0.01 秒に短縮されます。 この待ち時間を短くすることで、送信されるメッセージの数が増えます。

```csharp
await Task.Delay(10);
```

コンソール アプリケーションを実行します。 数分待ちます (10 分から 15 分)。 シミュレートされたデバイスからハブにメッセージが送信されていることを、アプリケーションのコンソール画面で確認できます。

### <a name="see-the-metrics-in-the-portal"></a>ポータルでメトリックを確認する

ダッシュボードでお客様のメトリックを開きます。 時間の値を *[過去 30 分間]* (時間の粒度は "*1 分*") に変更します。 送信済みテレメトリ メッセージと、使用されているメッセージの合計数がグラフに示され、最新の数値がグラフの一番下に表示されます。

   ![メトリックを示すスクリーンショット。](./media/tutorial-use-metrics-and-diags/13-metrics-populated.png)

### <a name="see-the-alerts"></a>アラートを確認する

アラートに戻ります。 **[リソース グループ]** をクリックし、*ContosoResources* を選択して、ハブ *ContosoTestHub* を選択します。 ハブについて表示されたプロパティ ページで、 **[アラート]** 、 **[クラシック アラートの表示]** の順に選択します。 

送信されたメッセージの数が上限を超えると、電子メール アラートが届き始めます。 アクティブなアラートがあるかどうかを確認するには、お客様のハブに移動して **[アラート]** を選択します。 アクティブなアラートと共に、警告があるかどうかが表示されます。 

   ![作動したアラートを示すスクリーンショット](./media/tutorial-use-metrics-and-diags/14-alerts-firing.png)

テレメトリ メッセージのアラートをクリックします。 メトリックの結果と、その結果に関するグラフが表示されます。 また、アラートの作動をお客様に警告するために送信された電子メールは、次の図のようになります。

   ![アラートが作動したことを示す電子メールのスクリーンショット](./media/tutorial-use-metrics-and-diags/15-alert-email.png)

### <a name="see-the-diagnostic-logs"></a>診断ログを確認する

お客様の診断ログのエクスポート先は Blob Storage に設定します。 お客様のリソース グループに移動して、お客様のストレージ アカウント *contosostoragemon* を選択します。 [BLOB] を選択し、コンテナー *insights-logs-connections* を開きます。 現在の日付に到達するまでドリルダウンし、最新のファイルを選択します。 

   ![診断ログを表示するためにストレージ コンテナーをドリルダウンする画面のスクリーンショット。](./media/tutorial-use-metrics-and-diags/16-diagnostics-logs-list.png)

**[ダウンロード]** をクリックしてそれをダウンロードし、開きます。 デバイスがハブにメッセージを送信する際に行われた接続と切断のログが表示されます。 サンプルを次に示します。

``` json
{ 
  "time": "2018-12-17T18:11:25Z", 
  "resourceId": 
    "/SUBSCRIPTIONS/your-subscription-id/RESOURCEGROUPS/CONTOSORESOURCES/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/CONTOSOTESTHUB", 
  "operationName": "deviceConnect", 
  "category": "Connections", 
  "level": "Information", 
  "properties": 
      {"deviceId":"Contoso-Test-Device",
       "protocol":"Mqtt",
       "authType":null,
       "maskedIpAddress":"73.162.215.XXX",
       "statusCode":null
       }, 
  "location": "westus"
}
{ 
   "time": "2018-12-17T18:19:25Z", 
   "resourceId": 
     "/SUBSCRIPTIONS/your-subscription-id/RESOURCEGROUPS/CONTOSORESOURCES/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/CONTOSOTESTHUB", 
    "operationName": "deviceDisconnect", 
    "category": "Connections", 
    "level": "Error", 
    "resultType": "404104", 
    "resultDescription": "DeviceConnectionClosedRemotely", 
    "properties": 
        {"deviceId":"Contoso-Test-Device",
         "protocol":"Mqtt",
         "authType":null,
         "maskedIpAddress":"73.162.215.XXX",
         "statusCode":"404"
         }, 
    "location": "westus"
}
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ 

このチュートリアルで作成したリソースをすべて削除するには、リソース グループを削除します。 これにより、そのグループ内に含まれているすべてのリソースも削除されます。 この場合は、IoT ハブ、ストレージ アカウント、リソース グループ自体が削除されます。 ダッシュボードにメトリックをピン留めした場合は、それらを手動で削除する必要があります。それぞれの右上隅にある 3 つのドットをクリックして、 **[削除]** を選択してください。

リソース グループを削除するには、[az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) コマンドを使います。

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下のタスクを実行することにより、メトリックと診断ログの使用方法を学習しました。

> [!div class="checklist"]
> * Azure CLI を使用して、IoT ハブ、シミュレートされたデバイス、ストレージ アカウントを作成する。  
> * 診断ログを有効にする。 
> * メトリックを有効にする。
> * それらのメトリックのアラートを設定する。 
> * IoT デバイスをシミュレートするアプリをダウンロードして実行し、ハブにメッセージを送信する。 
> * アラートが作動し始めるまでアプリを実行する。 
> * メトリックの結果を表示して診断ログを確認する。 

次のチュートリアルに進み、IoT デバイスの状態を管理する方法を学習してください。 

> [!div class="nextstepaction"]
> [バックエンド サービスからデバイスを構成する](tutorial-device-twins.md)