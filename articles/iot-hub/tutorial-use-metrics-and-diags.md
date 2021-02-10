---
title: チュートリアル - Azure IoT ハブでメトリックとログを設定して使用する
description: チュートリアル - Azure IoT ハブでメトリックとログを設定して使用する方法について説明します。 これにより、ハブで発生している可能性がある問題の診断に役立つ分析のためのデータが提供されます。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 10/29/2020
ms.author: robinsh
ms.custom:
- mvc
- mqtt
- devx-track-azurecli
- devx-track-csharp
ms.openlocfilehash: bf834a6dd648ffc8f4b1633dbb383f33cd99335f
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625201"
---
# <a name="tutorial-set-up-and-use-metrics-and-logs-with-an-iot-hub"></a>チュートリアル:IoT ハブでメトリックとログを設定して使用する

Azure Monitor を使用して、IoT ハブのメトリックとログを収集できます。これらは、ソリューションの操作の監視と、発生した問題のトラブルシューティングに役立ちます。 この記事では、メトリックに基づいてグラフを作成する方法、メトリックに対してトリガーされるアラートを作成する方法、IoT Hub の操作とエラーを Azure Monitor ログに送信する方法、ログでエラーを確認する方法について説明します。

このチュートリアルでは、[.NET でのテレメトリの送信に関するクイックスタート](quickstart-send-telemetry-dotnet.md)の Azure サンプルを使用して、IoT ハブにメッセージを送信します。 デバイスまたは別のサンプルを使用していつでもメッセージを送信できますが、いくつかの手順を適宜変更することが必要な場合があります。

このチュートリアルを開始する前に、Azure Monitor の概念をある程度理解しておくと役立つものと思われます。 詳細については、[IoT Hub の監視](monitor-iot-hub.md)に関する記事を参照してください。 IoT Hub によって出力されるメトリックとリソース ログの詳細については、[監視データのリファレンス](monitor-iot-hub-reference.md)に関する記事をご覧ください。

このチュートリアルでは、以下のタスクを実行します。

> [!div class="checklist"]
>
> * Azure CLI を使用して IoT ハブを作成し、シミュレートされたデバイスを登録して、Log Analytics ワークスペースを作成する。  
> * IoT Hub の接続およびデバイス テレメトリ リソース ログを、Log Analytics ワークスペース内の Azure Monitor ログに送信する。
> * メトリックス エクスプローラーを使用して、選択したメトリックに基づいてグラフを作成し、ダッシュボードにピン留めする。
> * 重要性の高い状態が生じたときに電子メールで通知を受け取ることができるように、メトリック アラートを作成する。
> * IoT ハブにメッセージを送信する IoT デバイスをシミュレートするアプリをダウンロードして実行する。
> * 特定の条件に合致したときにアラートを表示する。
> * メトリックのグラフをダッシュボード上に表示する。
> * Azure Monitor ログに IoT Hub のエラーと操作を表示する。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

- 開発用マシン上に .NET Core SDK 2.1 以上が必要です。 複数のプラットフォームに対応する .NET Core SDK を [.NET](https://www.microsoft.com/net/download/all) からダウンロードできます。

  開発コンピューターに現在インストールされている C# のバージョンは、次のコマンドを使って確認できます。

  ```cmd/sh
  dotnet --version
  ```

- メールを受信できる電子メール アカウント。

- ポート 8883 がファイアウォールで開放されていることを確認してください。 このチュートリアルのデバイス サンプルでは、ポート 8883 を介して通信する MQTT プロトコルを使用しています。 このポートは、企業や教育用のネットワーク環境によってはブロックされている場合があります。 この問題の詳細と対処方法については、「[IoT Hub への接続 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)」を参照してください。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="set-up-resources"></a>リソースを設定する

このチュートリアルには、IoT ハブ、Log Analytics ワークスペース、シミュレートされた IoT デバイスが必要です。 これらのリソースは、Azure CLI または Azure PowerShell を使って作成できます。 すべてのリソースに同じリソース グループと場所を使います。 このチュートリアルを完了したら、リソース グループを削除することによって、すべてのものを一度に削除できます。

必要な手順は次のとおりです。

1. [リソース グループ](../azure-resource-manager/management/overview.md)を作成します。

2. IoT Hub を作成します。

3. Log Analytics ワークスペースを作成します。

4. IoT ハブにメッセージを送信するシミュレートされたデバイスのデバイス ID を登録します。 シミュレートされたデバイスの構成に使用するデバイス接続文字列を保存します。

### <a name="set-up-resources-using-azure-cli"></a>Azure CLI を使用してリソースを設定する

次のスクリプトをコピーして Cloud Shell に貼り付けます。 既にログインしているものとすると、スクリプトが 1 行ずつ実行されます。 一部のコマンドは、実行に時間がかかる場合があります。 リソース グループ *ContosoResources* に新しいリソースが作成されます。

一部のリソースの名前は、Azure 全体で一意である必要があります。 このスクリプトでは、`$RANDOM` 関数を使用してランダム値が生成され、変数に格納されます。 これらのリソースでは、スクリプトによってこのランダム値がリソースのベース名に追加され、リソース名が一意になります。

サブスクリプションごとに許可される無料の IoT ハブは 1 つだけです。 自分のサブスクリプションに無料の IoT ハブが既にある場合は、スクリプトを実行する前に削除するか、その無料の IoT ハブか、Standard または Basic レベルを使用する IoT ハブを使用するようにスクリプトを変更してください。

スクリプトでは、IoT ハブの名前、Log Analytics ワークスペースの名前、登録するデバイスの接続文字列が出力されます。 この記事で後ほど必要になるため、これらを必ず書き留めておいてください。

```azurecli-interactive

# This is the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity.
az extension add --name azure-iot

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
location=westus
resourceGroup=ContosoResources
iotDeviceName=Contoso-Test-Device
randomValue=$RANDOM

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, so add a random number to the end.
iotHubName=ContosoTestHub$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub in the Free tier. Partition count must be 2.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --partition-count 2 \
    --sku F1 --location $location

# The Log Analytics workspace name must be globally unique, so add a random number to the end.
workspaceName=contoso-la-workspace$randomValue
echo "Log Analytics workspace name = " $workspaceName


# Create the Log Analytics workspace
az monitor log-analytics workspace create --resource-group $resourceGroup \
    --workspace-name $workspaceName --location $location

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the primary connection string for the device identity, then copy it to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show-connection-string --device-id $iotDeviceName \
    --hub-name $iotHubName

```

>[!NOTE]
>デバイス ID の作成時に、次のエラーが発生する場合があります: *No keys found for policy iothubowner of IoT Hub ContosoTestHub (IoT Hub ContosoTestHub のポリシー iothubowner のキーが見つかりません)* 。 このエラーを解決するには、Azure CLI IoT 拡張機能を更新したうえで、スクリプトにある最後の 2 つのコマンドをもう一度実行します。 
>
>拡張機能を更新するコマンドを次に示します。 このコマンドを Cloud Shell インスタンスで実行してください。
>
>```cli
>az extension update --name azure-iot
>```

## <a name="collect-logs-for-connections-and-device-telemetry"></a>接続とデバイス テレメトリのログを収集する

IoT Hub は、操作の複数のカテゴリに対応するリソース ログを出力します。ただし、これらのログを表示するには、送信先に送るための診断設定を作成する必要があります。 そのような送信先の 1 つが、Log Analytics ワークスペースで収集される Azure Monitor ログです。 IoT Hub リソース ログは、さまざまなカテゴリにグループ化されます。 Azure Monitor ログに送信するカテゴリを診断設定で選択できます。 この記事では、接続とデバイス テレメトリに関連して発生する操作とエラーのログを収集します。 IoT Hub でサポートされているカテゴリの完全な一覧については、[IoT Hub リソース ログ](monitor-iot-hub-reference.md#resource-logs)に関するセクションをご覧ください。

IoT Hub リソース ログを Azure Monitor ログに送信するための診断設定を作成するには、次の手順に従います。

1. まず、対象のハブがポータルにまだ表示されていない場合は、 **[リソース グループ]** を選択し、ContosoResources リソース グループを選択します。 表示されたリソースの一覧から対象の IoT ハブを選択します。

1. [IoT Hub] ブレードの **[監視]** セクションを探します。 **[診断設定]** を選択します。 次に **[診断設定を追加する]** を選択します。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/open-diagnostic-settings.png" alt-text="[モニター] セクションの [診断設定] が強調表示されているスクリーンショット。":::

1. **[診断設定]** ペインで、設定にわかりやすい名前を付けます (例: "Send connections and telemetry to logs")。

1. **[カテゴリの詳細]** で、 **[Connections]** と **[DeviceTelemetry]** を選択します。

1. **[宛先の詳細]** で **[Log Analytics への送信]** を選択し、Log Analytics ワークスペース ピッカーを使用して、前に書き留めておいたワークスペースを選択します。 完了すると、診断設定は次のスクリーンショットのようになります。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/add-diagnostic-setting.png" alt-text="最終的な診断ログ設定を示すスクリーンショット":::

1. **[保存]** を選択して設定を保存します。 **[診断設定]** ペインを閉じます。 診断設定の一覧に新しい設定が表示されます。

## <a name="set-up-metrics"></a>メトリックを設定する

次に、メトリックス エクスプローラーを使用して、追跡するメトリックを表示するグラフを作成します。Azure portal の既定のダッシュボードにこのグラフをピン留めします。

1. IoT ハブの左ペインで、 **[監視]** セクションの **[メトリック]** を選択します。

1. 画面の上部にある **[Last 24 hours (Automatic)]\(過去 24 時間 (自動)\)** を選択します。 表示されるドロップダウンでは、 **[時間の範囲]** で **[過去 4 時間]** を選択し、 **[時間の粒度]** を **[1 分]** に設定します。また、 **[時刻の表示形式]** で **[ローカル]** を選択します。 **[適用]** を選択して、これらの設定を保存します。 設定に、 **[Local Time: Last 4 hours (1 minute)]\(ローカル時刻: 過去 4 時間 (1 分)\)** と表示されます。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-select-time-range.png" alt-text="メトリックの時間設定を示すスクリーンショット":::

1. グラフには、IoT ハブをスコープとする部分的なメトリック設定が表示されます。 **[スコープ]** と **[メトリック名前空間]** の値は既定値のままにしておきます。 **[メトリック]** 設定を選択して「Telemetry」と入力し、ドロップダウンから **[Telemetry messages sent]\(送信済みテレメトリ メッセージ\)** を選択します。 **[集計]** は、自動的に **[合計]** に設定されます。 グラフのタイトルも変更されることに注意してください。

   グラフへの :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-telemetry-messages-sent.png" alt-text="[Telemetry messages sent]\(送信済みテレメトリ メッセージ\) メトリックの追加を示すスクリーンショット。":::

1. 次に、 **[メトリックの追加]** を選択して、グラフに別のメトリックを追加します。 **[メトリック]** で、 **[Total number of messages used]\(使用されているメッセージの合計数\)** を選択します。 **[集計]** は、動的に **[平均]** に設定されます。ここでも、このメトリックが含まれるようにグラフのタイトルが変更されたことがわかります。

   これで画面には、最小化された *[Telemetry messages sent]\(送信済みテレメトリ メッセージ\)* のメトリックに加え、新しく *[Total number of messages used]\(使用されているメッセージの合計数\)* のメトリックが表示されます。

   グラフへの :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-total-number-of-messages-used.png" alt-text="[Total number of messages used]\(使用されているメッセージの合計数\) メトリックの追加を示すスクリーンショット。":::

1. グラフの右上にある **[ダッシュボードにピン留め]** を選択します。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-total-number-of-messages-used-pin.png" alt-text="[ダッシュボードにピン留め] ボタンが強調表示されているスクリーンショット。":::

1. **[ダッシュボードにピン留め]** ペインで、 **[既存]** タブを選択します。 **[プライベート]** を選択し、[ダッシュボード] ドロップダウンから **[ダッシュボード]** を選択します。 最後に、 **[ピン留め]** を選択して、Azure portal の既定のダッシュボードにグラフをピン留めします。 グラフをダッシュボードにピン留めしていない場合、メトリックス エクスプローラーを終了すると、設定は保持されません。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/pin-to-dashboard.png" alt-text="[ダッシュボードにピン留め] の設定を示すスクリーンショット。":::

## <a name="set-up-metric-alerts"></a>メトリック アラートを設定する

次に、"*Telemetry messages sent (送信済みテレメトリ メッセージ)* " と "*Total number of messages used (使用されているメッセージの合計数)* " の 2 つのメトリックに対してトリガーするアラートを設定します。

"*Telemetry messages sent (送信済みテレメトリ メッセージ)* " は、メッセージ スループットを追跡し、調整を回避するために監視するのに適したメトリックです。 Free レベルの IoT ハブの場合、調整制限は 1 秒あたり 100 メッセージです。単一のデバイスでは、そのようなスループットを達成できないため、代わりに、5 分間でメッセージ数が 1000 を超えた場合にトリガーするアラートを設定します。 運用環境では、IoT ハブのレベル、エディション、ユニット数に基づいて、このシグナルをより大きな値に設定できます。

"*Total number of messages used (使用されているメッセージの合計数)* " では、使用されているメッセージの 1 日あたりの数を追跡します。 このメトリックは、毎日 00:00 UTC にリセットされます。 1 日あたりのクォータが特定のしきい値を超えると、IoT ハブはメッセージを受け入れなくなります。 Free レベルの IoT ハブの場合、1 日あたりのメッセージ クォータは 8000 です。 メッセージの合計数が 4000 (クォータの 50%) を超えた場合にトリガーするアラートを設定します。 実際には、この割合をより大きな値に設定すると考えられます。 1 日あたりのクォータ値は、IoT ハブのレベル、エディション、ユニット数によって異なります。

IoT Hub のクォータと調整制限の詳細については、[クォータと調整](iot-hub-devguide-quotas-throttling.md)に関する記事をご覧ください。

メトリック アラートを設定するには、次の手順に従います。

1. Azure portal で IoT ハブに移動します。

1. **[監視]** で **[アラート]** を選択します。 次に、 **[新しいアラート ルール]** を選択します。  **[アラート ルールの作成]** ペインが開きます。

    :::image type="content" source="media/tutorial-use-metrics-and-diags/create-alert-rule-pane.png" alt-text="[アラート ルールの作成] ペインを示すスクリーンショット。":::

    **[アラート ルールの作成]** ペインには、次の 4 つのセクションがあります。

    * **スコープ** は IoT ハブに既に設定されているため、このセクションはそのままにしておきます。
    * **[条件]** では、アラートをトリガーするシグナルと条件を設定します。
    * **[アクション]** では、アラートがトリガーされたときの動作を構成します。
    * **[アラート ルールの詳細]** では、アラートの名前と説明を設定できます。

1. まず、アラートがトリガーされる条件を構成します。

    1. **[条件]** で、 **[条件の選択]** を選択します。 **[シグナル ロジックの構成]** ペインで、検索ボックスに「telemetry」と入力し、 **[Telemetry messages sent]\(送信済みテレメトリ メッセージ\)** を選択します。

       :::image type="content" source="media/tutorial-use-metrics-and-diags/configure-signal-logic-telemetry-messages-sent.png" alt-text="メトリックの選択を示すスクリーンショット。":::

    1. **[シグナル ロジックの構成]** ペインで、 **[アラート ロジック]** の次のフィールドを設定または確認します (グラフは無視してかまいません)。

       **しきい値**: *静的*。

       **オペレーター**:*より大きい*。

       **集計の種類**: *合計*。

       **しきい値**: 1000。

       **集約粒度 (期間)** : *5 分*。

       **評価の頻度**: *1 分ごと*。

        :::image type="content" source="media/tutorial-use-metrics-and-diags/configure-signal-logic-set-conditions.png" alt-text="アラートの条件設定を示すスクリーンショット。":::

       これらの設定により、5 分間のメッセージの数を合計するようにシグナルが設定されます。 この合計が 1 分ごとに評価され、前の 5 分間の合計が 1000 メッセージを超えると、アラートがトリガーされます。

       **[完了]** を選択して、シグナル ロジックを保存します。

1. 次に、アラートのアクションを構成します。

    1. **[アラート ルールの作成]** ペインに戻り、 **[アクション]** で **[アクション グループの選択]** を選択します。 **[このアラート ルールにアタッチするアクション グループを選択する]** ペインで、 **[アクション グループの作成]** を選択します。

    1. **[アクション グループの作成]** ペインの **[基本]** タブで、アクション グループの名前と表示名を指定します。

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-basics.png" alt-text="[アクション グループの作成] ペインの [基本] タブを示すスクリーンショット。":::

    1. **[通知]** タブを選択します。 **[通知の種類]** で、ドロップダウンから **[Email/SMS message/Push/Voice]\(メール/SMS メッセージ/プッシュ/音声\)** を選択します。 **[Email/SMS message/Push/Voice]\(メール/SMS メッセージ/プッシュ/音声\)** ペインが開きます。

    1. **[Email/SMS message/Push/Voice]\(メール/SMS メッセージ/プッシュ/音声\)** ペインで、メールを選択し、メール アドレスを入力して、 **[OK]** を選択します。

        :::image type="content" source="media/tutorial-use-metrics-and-diags/set-email-address.png" alt-text="メール アドレスの設定を示すスクリーンショット。":::

    1. **[通知]** ペインに戻り、通知の名前を入力します。

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-notification-complete.png" alt-text="入力が完了した [通知] ペインを示すスクリーンショット。":::

    1. (省略可能) **[アクション]** タブを選択し、 **[アクションの種類]** ドロップダウンを選択すると、アラートでトリガーできるアクションの種類が表示されます。 この記事では通知のみを使用するため、このタブの設定は無視してかまいません。

        :::image type="content" source="media/tutorial-use-metrics-and-diags/action-types.png" alt-text="[アクション] ペインで使用可能なアクションの種類を示すスクリーンショット。":::

    1. **[確認および作成]** タブを選択し、設定を確認して、 **[作成]** を選択します。

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-review-and-create.png" alt-text="[確認および作成] ペインを示すスクリーンショット。":::

    1. **[アラート ルールの作成]** ペインに戻ると、アラートのアクションに新しいアクション グループが追加されていることがわかります。  

1. 最後に、アラート ルールの詳細を構成し、アラート ルールを保存します。

    1. **[アラート ルールの作成]** ペインの [アラート ルールの詳細] で、アラートの名前と説明 (例: "Alert if more than 1000 messages over 5 minutes") を入力します。 **[Enable alert rule upon creation]\(作成時にアラート ルールを有効にする\)** がオンになっていることを確認します。 完成したアラート ルールは、次のスクリーンショットのようになります。

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-alert-rule-final.png" alt-text="入力が完了した [アラート ルールの作成] ペインを示すスクリーンショット。":::

    1. **[アラート ルールの作成]** を選択して、新しいルールを保存します。

1. 次は、 *[Total number of messages used]\(使用されているメッセージの合計数\)* 用に、もう 1 つアラートを設定します。 このメトリックは、使用されているメッセージの数が IoT ハブの 1 日あたりのクォータ (IoT ハブがメッセージを拒否し始める時点) に近づきつつあるときにアラートを送信したい場合に役立ちます。 次の違いを除き、前に行った手順に従います。

    * **[シグナル ロジックの構成]** ペインのシグナルでは、 **[Total number of messages used]\(使用されているメッセージの合計数\)** を選択します。

    * **[シグナル ロジックの構成]** ペインで、次のフィールドを設定または確認します (グラフは無視してかまいません)。

       **しきい値**: *静的*。

       **オペレーター**:*より大きい*。

       **集計の種類**: *最大値*。

       **しきい値**: 4000。

       **集約粒度 (期間)** : *1 分*。

       **評価の頻度**: *1 分ごと*。

       これらの設定により、メッセージ数が 4000 に達したときにシグナルを発するように設定されます。 メトリックは 1 分ごとに評価されます。

    * アラート ルールのアクションを指定するときは、前に作成したアクション グループを選択するだけです。

    * アラートの詳細では、前とは異なる名前と説明を使用します。

1. IoT ハブの左ペインの **[監視]** で、 **[アラート]** を選択します。 次に、 **[アラート]** ペインの上部にあるメニューで、 **[アラート ルールの管理]** を選択します。 **[ルール]** ペインが開きます。 次のように、2 つのアラートが表示されます。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/rules-management.png" alt-text="新しいアラート ルールが含まれた [ルール] ペインを示すスクリーンショット。":::

1. **[ルール]** ペインを閉じます。

これらの設定により、5 分以内に 1000 件を超えるメッセージが送信されたときと、使用されているメッセージの合計数が 4000 (Free レベルの IoT ハブの、1 日あたりのクォータの 50%) を超えたときに、アラートがトリガーされ、電子メール通知が送信されます。

## <a name="run-the-simulated-device-app"></a>シミュレーション済みデバイス アプリを実行する

「[リソースを設定する](#set-up-resources)」で、IoT デバイスの使用をシミュレートするために使用するデバイス ID を登録しました。 このセクションでは、device-to-cloud メッセージを IoT ハブに送信するデバイスをシミュレートする .NET コンソール アプリをダウンロードし、これらのメッセージを IoT ハブに送信するようにアプリを構成して実行します。 

> [!IMPORTANT]
>
> アラートが IoT Hub によって完全に構成され、有効になるまでに最大 10 分かかることがあります。 最後のアラートを構成してから、シミュレートされたデバイス アプリを実行するまで、少なくとも 10 分お待ちください。

[IoT デバイス シミュレーション](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)のソリューションをダウンロードします。 このリンクにより、複数のアプリケーションを含むリポジトリがダウンロードされます。探しているものは、iot-hub/Quickstarts/simulated-device/ にあります。

1. ローカル ターミナル ウィンドウで、ソリューションのルート フォルダーに移動します。 **iot-hub\Quickstarts\simulated-device** フォルダーに移動します。

1. 適当なテキスト エディターで **SimulatedDevice.cs** ファイルを開きます。

    1. `s_connectionString` 変数の値を、スクリプトを実行してリソースを設定したときに書き留めておいたデバイス接続文字列に置き換えます。

    1. `SendDeviceToCloudMessagesAsync` メソッドで、`Task.Delay` を 1000 から 1 に変更します。これにより、メッセージの送信間隔が 1 秒から 0.001 秒に短縮されます。 この待ち時間を短くすることで、送信されるメッセージの数が増えます。 (1 秒あたり 100 メッセージのメッセージ レートは得られない可能性があります。)

        ```csharp
        await Task.Delay(1);
        ```

    1. 変更を **SimulatedDevice.cs** に保存します。

1. ローカル ターミナル ウィンドウで次のコマンドを実行して、シミュレートされたデバイス アプリケーションに必要なパッケージをインストールします。

    ```cmd/sh
    dotnet restore
    ```

1. ローカル ターミナル ウィンドウで次のコマンドを実行し、シミュレートされたデバイス アプリケーションをビルドして実行します。

    ```cmd/sh
    dotnet run
    ```

    次のスクリーンショットは、シミュレートされたデバイス アプリケーションが IoT Hub にテレメトリを送信したときの出力を示しています。

    :::image type="content" source="media/tutorial-use-metrics-and-diags/simulated-device-output.png" alt-text="シミュレートされたデバイスの出力を示すスクリーンショット。":::

アプリケーションを少なくとも 10 から 15 分間実行します。 メッセージの送信を停止するまで実行するのが理想的です (約 20 から 30 分)。 これは、IoT ハブの 1 日あたりのメッセージ クォータを超え、それ以上のメッセージの受け入れを停止したときに発生します。

> [!NOTE]
> メッセージの送信を停止した後、デバイス アプリを長時間実行したままにすると、例外が発生する場合があります。 この例外を無視し、アプリ ウィンドウを閉じてかまいません。

## <a name="view-metrics-chart-on-your-dashboard"></a>ダッシュボードにメトリックのグラフを表示する

1. Azure portal の左上隅にあるポータル メニューを開き、 **[ダッシュボード]** を選択します。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/select-dashboard.png" alt-text="ダッシュボードを選択する方法を示すスクリーンショット。":::

1. 以前にピン留めしたグラフを見つけ、グラフ データの外側のタイル上の任意の場所をクリックして展開します。 送信済みテレメトリ メッセージと、使用されているメッセージの合計数がグラフに示されます。 グラフの下部に最新の数値が表示されます。 グラフ内でカーソルを移動して、特定の時間のメトリック値を確認できます。 グラフの上部にある時間値と粒度を変更して、データを関心がある期間に絞り込んだり拡大したりすることもできます。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-on-dashboard-last-hour.png" alt-text="メトリックのグラフを示すスクリーンショット。":::

   このシナリオでは、シミュレートされたデバイスのメッセージ スループットは、IoT Hub によるメッセージ数の調整が発生するほど大きくありません。 実際に調整を伴うシナリオでは、限られた期間、送信済みテレメトリ メッセージが IoT ハブの調整制限を超える場合があります。 これは、バースト トラフィックに対応するためです。 詳細については、「[トラフィック シェイプ](iot-hub-devguide-quotas-throttling.md#traffic-shaping)」をご覧ください。

## <a name="view-the-alerts"></a>アラートを表示する

送信されたメッセージの数がアラート ルールで設定した上限を超えると、電子メール アラートが届き始めます。

アクティブなアラートがあるかどうかを確認するには、IoT ハブの左ペインの **[監視]** で **[アラート]** を選択します。 **[アラート]** ペインには、指定した時間範囲に発生したアラートの数が重大度順に表示されます。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-alerts.png" alt-text="アラートの概要を示すスクリーンショット。":::

重大度 3 の行を選択します。 **[すべてのアラート]** ペインが開き、発生した重大度 3 のアラートが一覧表示されます。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-all-alerts.png" alt-text="[すべてのアラート] ペインを示すスクリーンショット。":::

アラートのいずれかを選択すると、そのアラートの詳細が表示されます。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-individual-alert.png" alt-text="アラートの詳細を示すスクリーンショット。":::

受信トレイで Microsoft Azure からの電子メールを確認します。 件名にトリガーされたアラートが記載されています (例: *Azure: Activated Severity:3 Alert if more than 1000 messages over 5 minutes*)。 本文は次の画像のようになります。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/alert-mail.png" alt-text="アラートが作動したことを示す電子メールのスクリーンショット":::

## <a name="view-azure-monitor-logs"></a>Azure Monitor ログを表示する

「[接続とデバイス テレメトリのログを収集する](#collect-logs-for-connections-and-device-telemetry)」で、接続とデバイス テレメトリの操作について、IoT ハブによって出力されたリソース ログを Azure Monitor ログに送信するための診断設定を作成しました。 このセクションでは、Azure Monitor ログに対して Kusto クエリを実行して、発生したエラーを監視します。

1. Azure portal の IoT ハブの左ペインにある **[監視]** で **[ログ]** を選択します。 最初の **[クエリ]** ウィンドウが開いた場合は閉じます。

1. [新しいクエリ] ペインで、 **[クエリ]** タブを選択し、 **[IoT Hub]** を展開して既定のクエリの一覧を表示します。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/new-query-pane.png" alt-text="IoT Hub の既定のクエリのスクリーンショット。":::

1. *[エラーの概要]* クエリを選択します。 クエリ エディター ペインにクエリが表示されます。 エディター ペインで **[実行]** を選択し、クエリ結果を確認します。 行のいずれかを展開すると、詳細が表示されます。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/logs-errors.png" alt-text="[エラーの概要] クエリで返されたログのスクリーンショット。":::

   > [!NOTE]
   > エラーが表示されない場合は、 *[Recently connected devices]\(最近接続されたデバイス\)* クエリを実行してみてください。 これにより、シミュレートされたデバイスの行が返されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルで作成したリソースをすべて削除するには、リソース グループを削除します。 これにより、そのグループ内に含まれているすべてのリソースも削除されます。 ここでは、IoT ハブ、Log Analytics ワークスペース、およびリソース グループ自体が削除されます。 メトリックのグラフをダッシュボードにピン留めしている場合は、それらを手動で削除する必要があります。各グラフの右上隅にある 3 つのドットをクリックし、 **[削除]** を選択します。 グラフを削除したら、必ず変更を保存してください。

リソース グループを削除するには、[az group delete](/cli/azure/group#az-group-delete) コマンドを使います。

```azurecli-interactive
az group delete --name ContosoResources
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次のタスクを実行することで、IoT Hub のメトリックとログの使用方法を学習しました。

> [!div class="checklist"]
>
> * Azure CLI を使用して IoT ハブを作成し、シミュレートされたデバイスを登録して、Log Analytics ワークスペースを作成する。  
> * IoT Hub の接続およびデバイス テレメトリ リソース ログを、Log Analytics ワークスペース内の Azure Monitor ログに送信する。
> * メトリックス エクスプローラーを使用して、選択したメトリックに基づいてグラフを作成し、ダッシュボードにピン留めする。
> * 重要性の高い状態が生じたときに電子メールで通知を受け取ることができるように、メトリック アラートを作成する。
> * IoT ハブにメッセージを送信する IoT デバイスをシミュレートするアプリをダウンロードして実行する。
> * 特定の条件に合致したときにアラートを表示する。
> * メトリックのグラフをダッシュボード上に表示する。
> * Azure Monitor ログに IoT Hub のエラーと操作を表示する。

次のチュートリアルに進み、IoT デバイスの状態を管理する方法を学習してください。 

> [!div class="nextstepaction"]
> [バックエンド サービスからデバイスを構成する](tutorial-device-twins.md)