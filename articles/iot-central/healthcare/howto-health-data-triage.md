---
title: Azure IoT Central を使用して健康データ トリアージ ダッシュボードを作成する | Microsoft Docs
description: Azure IoT Central アプリケーション テンプレートを使用して、健康データ トリアージ ダッシュボードを構築する方法について学習します。
author: philmea
ms.author: philmea
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: ed06aef4d494fbdce5a07c5bc50bad9737ba5433
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86497048"
---
# <a name="tutorial-build-a-power-bi-provider-dashboard"></a>チュートリアル:Power BI プロバイダー ダッシュボードを構築する



患者の継続的な監視ソリューションを構築するときは、病院看護チームが患者のデータを視覚化するためのダッシュボードも作成できます。 このチュートリアルでは、IoT Central の患者の継続的なモニタリング アプリケーション テンプレートから Power BI のリアルタイム ストリーミング ダッシュボードを作成する方法を説明します。 リアルタイム データへのアクセスが不要なユースケースでは、デプロイ プロセスが簡略化された [IoT Central Power BI ダッシュボード](../core/howto-connect-powerbi.md)を使用できます。 

>[!div class="mx-imgBorder"]
>![ダッシュボードの GIF](media/dashboard-gif-3.gif)

基本的なアーキテクチャは、次のような構造になっています。

>[!div class="mx-imgBorder"] 
>![プロバイダー トリアージ ダッシュボード](media/dashboard-architecture.png)

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure IoT Central から Azure Event Hubs にデータをエクスポートする
> * Power BI ストリーミング データセットをセットアップする
> * ロジック アプリを Azure Event Hubs に接続する
> * ロジック アプリから Power BI にデータをストリーミングする
> * 患者のバイタル値用のリアルタイム ダッシュボードを作成する

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* Azure IoT Central の患者の継続的なモニタリング アプリケーション テンプレート。 まだない場合は、[アプリケーション テンプレートの展開](overview-iot-central-healthcare.md)に関する記事の手順に従って展開できます。

* Azure [Event Hubs 名前空間とイベント ハブ](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)。

* イベント ハブにアクセスするためのロジック アプリ。 Azure Event Hubs トリガーを使用してロジック アプリを起動するには、[空のロジック アプリ](https://docs.microsoft.com/azure/logic-apps/quickstart-create-first-logic-app-workflow)が必要です。

* Power BI サービス アカウント。 まだない場合は、[Power BI サービスの無料試用版アカウントを作成する](https://app.powerbi.com/)ことができます。 これまで Power BI を使ったことがない場合は、[Power BI の使用開始](https://docs.microsoft.com/power-bi/service-get-started)に関する記事を参照してください。

## <a name="set-up-a-continuous-data-export-to-azure-event-hubs"></a>Azure Event Hubs への継続的データ エクスポートをセットアップする
最初に、Azure IoT Central アプリ テンプレートからサブスクリプションの Azure Event Hub への継続的データ エクスポートを設定する必要があります。 これは、[Event Hubs へのエクスポート](https://docs.microsoft.com/azure/iot-central/core/howto-export-data)に関する Azure IoT Central チュートリアルの手順に従って行うことができます。 このチュートリアルでは、テレメトリ用のエクスポートのみが必要です。

## <a name="create-a-power-bi-streaming-dataset"></a>Power BI ストリーミング データセットを作成する

1. Power BI アカウントにサインインします。

2. 任意のワークスペースで、ツール バーの右上隅にある **[+ 作成]** ボタンを選択して、新しいストリーミング データセットを作成します。 ダッシュボードに表示する患者ごとに、個別のデータセットを作成する必要があります。

    >[!div class="mx-imgBorder"] 
    >![ストリーミング データセットを作成する](media/create-streaming-dataset.png)

3. データセットのソースとして **[API]** を選択します。

4. データセットの**名前** (たとえば患者の名前) を入力し、ストリームから値を入力します。 患者の継続的なモニタリング アプリケーション テンプレートでシミュレートされたデバイスからの値に基づいて、次の例を確認できます。 例には 2 人の患者がいます。

    * Teddy Silvers のデータは Smart Knee Brace からのものです
    * Yesenia Sanford のデータは Smart Vitals Patch からのものです

    >[!div class="mx-imgBorder"] 
    >![データセットの値を入力する](media/enter-dataset-values.png)

Power BI でのデータセットのストリーミングについて詳しくは、「[Power BI のリアルタイム ストリーミング](https://docs.microsoft.com/power-bi/service-real-time-streaming)」をご覧ください。

## <a name="connect-your-logic-app-to-azure-event-hubs"></a>ロジック アプリを Azure Event Hubs に接続する
ロジック アプリを Azure Event Hubs に接続するには、[Azure Event Hubs と Azure Logic Apps でのイベントの送信](https://docs.microsoft.com/azure/connectors/connectors-create-api-azure-event-hubs#add-event-hubs-action)に関するドキュメントに記載されている手順に従ってください。 いくつかの推奨されるパラメーターを次に示します。

|パラメーター|値|
|---|---|
|Content type|application/json|
|Interval|3|
|頻度|秒|

このステップの最後では、ロジック アプリ デザイナーは次のようになります。

>[!div class="mx-imgBorder"] 
>![Event Hubs に接続した Logic Apps](media/eh-logic-app.png)

## <a name="stream-data-to-power-bi-from-your-logic-app"></a>ロジック アプリから Power BI にデータをストリーミングする
次のステップでは、イベント ハブからのデータを解析して、前に作成した Power BI データセットにストリーミングします。

1. これを行う前に、デバイスからイベント ハブに送信される JSON ペイロードを理解しておく必要があります。 そのためには、この[サンプル スキーマ](https://docs.microsoft.com/azure/iot-central/core/howto-export-data#telemetry)を確認し、自分のスキーマに合わせて変更するか、[Service Bus エクスプローラー](https://github.com/paolosalvatori/ServiceBusExplorer)を使用してメッセージを調べます。 患者の継続的なモニタリング アプリケーションを使用している場合、メッセージは次のようになります。

**Smart Vitals Patch テレメトリ**

```json
{
  "HeartRate": 80,
  "RespiratoryRate": 12,
  "HeartRateVariability": 64,
  "BodyTemperature": 99.08839032397609,
  "BloodPressure": {
    "Systolic": 23,
    "Diastolic": 34
  },
  "Activity": "walking"
}
```

**Smart Knee Brace テレメトリ**

```json
{
  "Acceleration": {
    "x": 72.73510947763711,
    "y": 72.73510947763711,
    "z": 72.73510947763711
  },
  "RangeOfMotion": 123,
  "KneeBend": 3
}
```

**Properties**

```json
{
  "iothub-connection-device-id": "1qsi9p8t5l2",
  "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",  \"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
  "iothub-connection-auth-generation-id": "637063718586331040",
  "iothub-enqueuedtime": 1571681440990,
  "iothub-message-source": "Telemetry",
  "iothub-interface-name": "Patient_health_data_3bk",
  "x-opt-sequence-number": 7,
  "x-opt-offset": "3672",
  "x-opt-enqueued-time": 1571681441317
}
```

2. JSON ペイロードの調査が済んだら、ロジック アプリ デザイナーに戻り、 **[+ 新しいステップ]** を選択します。 **[変数を初期化する]** を探し、次のステップとして追加し、以下のパラメーターを入力します。

    |パラメーター|値|
    |---|---|
    |名前|Interface Name|
    |Type|String|

    **[保存]** をクリックします。 

3. **Body** という名前で **String** 型の別の変数を追加します。 ロジック アプリには、次のアクションが追加されます。

    >[!div class="mx-imgBorder"]
    >![変数を初期化する](media/initialize-string-variables.png)
    
4. **[+ 新しいステップ]** を選択し、 **[JSON の解析]** アクションを追加します。 この名前を「**プロパティを解析する**」に変更します。 [Content]\(内容\) では、イベント ハブからの **[プロパティ]** を選択します。 下部にある **[サンプルのペイロードを使用してスキーマを生成する]** を選択し、上の [プロパティ] セクションからサンプル ペイロードを貼り付けます。

5. 次に、 **[変数の設定]** アクションを選択し、**Interface Name** 変数を解析された JSON プロパティの **iothub-interface-name** で更新します。

6. 次のアクションとして **[Split]\(分割\)** コントロールを追加し、**Interface Name** 変数を On パラメーターとして選択します。 これを使用して、データを適切なデータセットに絞り込みます。

7. Azure IoT Central アプリケーションで、 **[デバイス テンプレート]** ビューから、Smart Vitals Patch 健康データと Smart Knee Brace 健康データの Interface Name を見つけます。 各 Interface Name の **[Switch]\(切り替え\)** コントロールに対して 2 つの異なるケースを作成し、コントロールの名前を適切に変更します。 既定のケースは **[Terminate]\(終了する\)** コントロールを使用するように設定し、表示する状態を選択します。

    >[!div class="mx-imgBorder"] 
    >![分割コントロール](media/split-by-interface.png)

8. **Smart Vitals Patch** ケースに対しては、 **[JSON の解析]** アクションを追加します。 [Content]\(内容\) では、イベント ハブからの **[Content]\(内容\)** を選択します。 上の Smart Vitals Patch のサンプル ペイロードをコピーして貼り付け、スキーマを生成します。

9. **[変数の設定]** アクションを追加し、**Body** 変数をステップ 7 で解析された JSON の**本文**で更新します。

10. 次のアクションとして **[条件]** コントロールを追加し、条件を **Body**、 **[次の値を含む]** 、**HeartRate** に設定します。 これにより、Power BI データセットに設定する前に、Smart Vitals Patch からのデータのセットが適切であることが確認されます。 ステップ 7 から 9 は次のようになります。

    >[!div class="mx-imgBorder"] 
    >![Smart Vitals の条件を追加する](media/smart-vitals-pbi.png)

11. 条件の **True** ケースに対し、 **[データセットに行を追加します]** Power BI 機能を呼び出すアクションを追加します。 このためには Power BI にサインインする必要があります。 **False** ケースでは、 **[Terminate]\(終了する\)** コントロールを再度使用できます。

12. 適切な **[ワークスペース]** 、 **[データセット]** 、 **[テーブル]** を選択します。 Power BI でストリーミング データセットを作成するときに指定したパラメーターを、イベント ハブからの解析済み JSON 値にマップします。 入力したアクションは次のようになります。

    >[!div class="mx-imgBorder"] 
    >![Power BI に行を追加する](media/add-rows-yesenia.png)

13. **Smart Knee Brace** 切り替えケースには、ステップ 7 と同様に、 **[JSON の解析]** アクションを追加して内容を解析します。 次に、 **[データセットに行を追加します]** を使用して、Power BI の Teddy Silvers データセットを更新します。

    >[!div class="mx-imgBorder"] 
    >![Smart Vitals の条件を追加する](media/knee-brace-pbi.png)

14. **[保存]** を選択し、ロジック アプリを実行します。

## <a name="build-a-real-time-dashboard-for-patient-vitals"></a>患者のバイタル値用のリアルタイム ダッシュボードを作成する
ここで Power BI に戻り、 **[+ 作成]** を選択して、新しい**ダッシュボード**を作成します。 ダッシュボードに名前を付け、 **[作成]** を選択します。

上部のナビゲーション バーで省略記号を選択し、 **[+ タイルの追加]** を選択します。

>[!div class="mx-imgBorder"] 
>![ダッシュボードにタイルを追加する](media/add-tile.png)

追加するタイルの種類を選択し、必要に応じてアプリをカスタマイズします。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションを引き続き使用しない場合は、次の手順でリソースを削除します。

1. Azure portal では、作成したイベントハブと Logic Apps リソースを削除できます。

2. お使いの IoT Central アプリケーションで、[管理] タブに移動し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

* [患者の継続的なモニタリングのアーキテクチャのガイダンス](concept-continuous-patient-monitoring-architecture.md)を確認します。
