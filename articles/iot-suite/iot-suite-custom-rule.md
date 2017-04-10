---
title: "Azure IoT Suite でカスタム規則を作成する |Microsoft ドキュメント"
description: "IoT Suite 事前構成済みソリューションでカスタム規則を作成する方法です。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 562799dc-06ea-4cdd-b822-80d1f70d2f09
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/09/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 900f0b4c38f08d1e3017954db01cf32362765578
ms.lasthandoff: 03/10/2017


---
# <a name="create-a-custom-rule-in-the-remote-monitoring-preconfigured-solution"></a>リモート監視の事前構成済みソリューションでカスタム規則を作成する

## <a name="introduction"></a>はじめに

構成済みのソリューションで、[デバイスのテレメトリ値が一定のしきい値に達したときにトリガーする規則][lnk-builtin-rule]を設定できます。 「[事前構成済みのリモート監視ソリューションによる動的テレメトリの使用][lnk-dynamic-telemetry]」では、*ExternalTemperature* など、テレメトリのカスタム値をソリューションに追加する方法について説明しています。 この記事では、ソリューションの動的テレメトリ タイプのカスタム規則を作成する方法を説明します。

このチュートリアルでは、変更を加えやすい単純な Node.js のシミュレーション対象デバイスを使用して、動的テレメトリを生成し、構成済みソリューションのバックエンドに送信します。 そして、**RemoteMonitoring** Visual Studio ソリューションにカスタム規則を追加して、そのカスタマイズしたバックエンドを Azure サブスクリプションにデプロイします。

このチュートリアルを完了するには、次のものが必要です。

* 有効な Azure サブスクリプション アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、[Azure の無料試用版サイト][lnk_free_trial]をご覧ください。
* シミュレートされたデバイスを作成するための [Node.js][lnk-node] バージョン 0.12.x 以降。
* 構成済みソリューションのバックエンドを新しい規則で変更するための Visual Studio 2015 または Visual Studio 2017。

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

デプロイに選択したソリューション名をメモしておきます。 このソリューション名は本チュートリアルの後半で必要になります。

[!INCLUDE [iot-suite-send-external-temperature](../../includes/iot-suite-send-external-temperature.md)]

構成済みソリューションに **ExternalTemperature** テレメトリを送信していることを確認したら、Node.js コンソール アプリを停止できます。 ソリューションにカスタム規則を追加した後でこの Node.js コンソール アプリをもう一度実行するため、コンソール ウィンドウは開いたままにしておきます。

## <a name="rule-storage-locations"></a>規則の保存先

規則についての情報は、次の&2; つの場所に保存されます。

* **DeviceRulesNormalizedTable** テーブル – このテーブルには、ソリューション ポータルで定義された規則への正規化された参照が保存されます。 ソリューション ポータルにデバイス ルールが表示されるときは、規則の定義をこのテーブルに照会します。
* **DeviceRules** BLOB – この BLOB には、登録されているすべてのデバイスに対して定義されたすべての規則が保存され、Azure Stream Analytics ジョブへの参照入力として定義されます。
 
既存の規則を更新するか、ソリューション ポータルで新しい規則を定義すると、テーブルと BLOB の両方が更新され、変更が反映されます。 ポータルに表示される規則の定義はテーブル ストアから取得され、Stream Analytics ジョブが参照する規則の定義は BLOB から取得されます。 

## <a name="update-the-remotemonitoring-visual-studio-solution"></a>RemoteMonitoring Visual Studio ソリューションを更新する

RemoteMonitoring Visual Studio ソリューションを変更して、シミュレートされたデバイスから送信された **ExternalTemperature** テレメトリを使用する新しい規則を含める方法は、次のとおりです。

1. まだ実行していない場合は、次の Git コマンドを使用して、**azure-iot-remote-monitoring** リポジトリをローカル コンピューターの適切な場所に複製します。

    ```
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```

2. Visual Studio で、**azure-iot-remote-monitoring** リポジトリのローカル コピーから RemoteMonitoring.sln ファイルを開きます。

3. Infrastructure\Models\DeviceRuleBlobEntity.cs ファイルを開いて、次のように **ExternalTemperature** プロパティを追加します。

    ```csharp
    public double? Temperature { get; set; }
    public double? Humidity { get; set; }
    public double? ExternalTemperature { get; set; }
    ```

4. 同じファイルで、次のように **ExternalTemperatureRuleOutput** プロパティを追加します。

    ```csharp
    public string TemperatureRuleOutput { get; set; }
    public string HumidityRuleOutput { get; set; }
    public string ExternalTemperatureRuleOutput { get; set; }
    ```

5. Infrastructure\Models\DeviceRuleDataFields.cs ファイルを開いて、次のように、既存の **Humidity** プロパティの後に **ExternalTemperature** プロパティを追加します。

    ```csharp
    public static string ExternalTemperature
    {
        get { return "ExternalTemperature"; }
    }
    ```

6. 同じファイルで、次のように、**_availableDataFields** メソッドを更新して **ExternalTemperature** を含めます。

    ```csharp
    private static List<string> _availableDataFields = new List<string>
    {                    
        Temperature, Humidity, ExternalTemperature
    };
    ```

7. Infrastructure\Repository\DeviceRulesRepository.cs ファイルを開いて、次のように **BuildBlobEntityListFromTableRows** メソッドを変更します。

    ```csharp
    else if (rule.DataField == DeviceRuleDataFields.Humidity)
    {
        entity.Humidity = rule.Threshold;
        entity.HumidityRuleOutput = rule.RuleOutput;
    }
    else if (rule.DataField == DeviceRuleDataFields.ExternalTemperature)
    {
      entity.ExternalTemperature = rule.Threshold;
      entity.ExternalTemperatureRuleOutput = rule.RuleOutput;
    }
    ```

## <a name="rebuild-and-redeploy-the-solution"></a>ソリューションをリビルドして再デプロイする

更新したソリューションを Azure サブスクリプションにデプロイできるようになりました。

1. 管理者特権のコマンド プロンプトを開いて、azure-iot-remote-monitoring リポジトリのローカル コピーのルートに移動します。

2. 更新したソリューションをデプロイするには、次のコマンドの **{deployment name}** を、さきほどメモした構成済みソリューションのデプロイの名前に置き換えて実行します。

    ```
    build.cmd cloud release {deployment name}
    ```

## <a name="update-the-stream-analytics-job"></a>Stream Analytics ジョブを更新する

デプロイが完了したら、Stream Analytics ジョブを更新して新しいルール定義を使用できます。

1. Azure Portal で、構成済みのソリューション リソースを含むリソース グループに移動します。 このリソース グループの名前は、デプロイ時にソリューションに指定した名前と同じです。

2. {deployment name}-Rules Stream Analytics ジョブに移動します。 

3. **[Stop (停止)]** をクリックして、Stream Analytics ジョブの実行を停止します。 (クエリを編集する前に、ストリーミング ジョブが停止するのを待つ必要があります。)

4. **[クエリ]** をクリックします。 クエリを編集して、**ExternalTemperature** の **SELECT** ステートメントを含めます。 次の例は、新しい**SELECT** ステートメントを含む完全なクエリです。

    ```
    WITH AlarmsData AS 
    (
    SELECT
         Stream.IoTHub.ConnectionDeviceId AS DeviceId,
         'Temperature' as ReadingType,
         Stream.Temperature as Reading,
         Ref.Temperature as Threshold,
         Ref.TemperatureRuleOutput as RuleOutput,
         Stream.EventEnqueuedUtcTime AS [Time]
    FROM IoTTelemetryStream Stream
    JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
    WHERE
         Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature
     
    UNION ALL
     
    SELECT
         Stream.IoTHub.ConnectionDeviceId AS DeviceId,
         'Humidity' as ReadingType,
         Stream.Humidity as Reading,
         Ref.Humidity as Threshold,
         Ref.HumidityRuleOutput as RuleOutput,
         Stream.EventEnqueuedUtcTime AS [Time]
    FROM IoTTelemetryStream Stream
    JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
    WHERE
         Ref.Humidity IS NOT null AND Stream.Humidity > Ref.Humidity
     
    UNION ALL
     
    SELECT
         Stream.IoTHub.ConnectionDeviceId AS DeviceId,
         'ExternalTemperature' as ReadingType,
         Stream.ExternalTemperature as Reading,
         Ref.ExternalTemperature as Threshold,
         Ref.ExternalTemperatureRuleOutput as RuleOutput,
         Stream.EventEnqueuedUtcTime AS [Time]
    FROM IoTTelemetryStream Stream
    JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
    WHERE
         Ref.ExternalTemperature IS NOT null AND Stream.ExternalTemperature > Ref.ExternalTemperature
    )
     
    SELECT *
    INTO DeviceRulesMonitoring
    FROM AlarmsData
     
    SELECT *
    INTO DeviceRulesHub
    FROM AlarmsData
    ```

5. **[保存]** をクリックして、更新した規則クエリを変更します。

6. **[Start (開始)]** をクリックして、Stream Analytics ジョブの実行を再開します。

## <a name="add-your-new-rule-in-the-dashboard"></a>ダッシュ ボードに新しい規則を追加する

**ExternalTemperature** 規則をソリューション ダッシュボードのデバイスに追加できるようになりました。

1. ソリューション ポータルに移動します。

2. **[デバイス]** パネルに移動します。

3. **ExternalTemperature** テレメトリを送信する、作成済みのカスタム デバイスを見つけて、**[デバイスの詳細]** パネルで **[ルールの追加]** をクリックします。

4. **[Data Field (データ フィールド)]**で **[ExternalTemperature]** を選択します。

5. **[Threshold (しきい値)]** を 56 に設定します。 次に、**[規則の保存と表示]** をクリックします。

6. ダッシュボードに戻り、アラーム履歴を表示します。

7. 開いたままにしておいたコンソール ウィンドウで Node.js コンソール アプリを起動して、**ExternalTemperature** テレメトリ データの送信を開始します。

8. 新しい規則がトリガーされると、**[アラーム履歴]** テーブルに新しいアラームが表示されます。
 
## <a name="additional-information"></a>追加情報

演算子の変更 **>** はかなり複雑で、このチュートリアルで説明する手順の範囲を超えています。 Stream Analytics ジョブを変更して任意の演算子を使用することはできますが、その演算子をソリューション ポータルに反映するのはそれよりもずっと複雑な作業になります。 

## <a name="next-steps"></a>次のステップ
カスタム規則を作成する方法がわかったところで、構成済みソリューションについての次の詳細もご覧ください。

- [ロジック アプリを Azure IoT Suite リモート監視構成済みソリューションに接続する][lnk-logic-app]
- [リモート監視構成済みソリューションのデバイス情報メタデータ][lnk-devinfo]

[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
[lnk-builtin-rule]: iot-suite-getstarted-preconfigured-solutions.md#view-alarms
[lnk-dynamic-telemetry]: iot-suite-dynamic-telemetry.md
[lnk-logic-app]: iot-suite-logic-apps-tutorial.md
