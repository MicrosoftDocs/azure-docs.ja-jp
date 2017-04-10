---
title: "動的テレメトリの使用 | Microsoft Docs"
description: "事前構成済みの Azure IoT Suite リモート監視ソリューションで動的テレメトリを使用する方法については、このチュートリアルを参照してください。"
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
ms.date: 02/09/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 37a1653ca058c60a39df95f646127bd9e7fdd556
ms.openlocfilehash: 7fe03bcb918997971208554d030264d67bedb1ff
ms.lasthandoff: 02/09/2017


---
# <a name="use-dynamic-telemetry-with-the-remote-monitoring-preconfigured-solution"></a>事前構成済みのリモート監視ソリューションによる動的テレメトリの使用
## <a name="introduction"></a>はじめに
事前構成済みのリモート監視ソリューションに送信されたテレメトリは、動的テレメトリによって視覚化できます。 事前構成済みのソリューションと一緒にデプロイされたシミュレーション対象デバイスからは、温度と湿度のテレメトリが送信されます。このテレメトリをダッシュボードで視覚化することができます。 既存のシミュレーション対象デバイスをカスタマイズする場合や、新しいシミュレーション対象デバイスを作成する場合、または事前構成済みのソリューションに物理デバイスを接続する場合は、他にも外部温度や RPM、風速などのテレメトリ値を送信することができます。 そのうえでこうした追加のテレメトリをダッシュボードで視覚化できます。

このチュートリアルでは、変更を加えやすい単純な Node.js のシミュレーション対象デバイスを使用して、動的テレメトリの実験を行います。

このチュートリアルを完了するには、以下が必要になります。

* 有効な Azure サブスクリプション アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、[Azure の無料試用版サイト][lnk_free_trial]をご覧ください。
* [Node.js][lnk-node] バージョン 0.12.x 以降。

このチュートリアルに使用するオペレーティング システムは、Windows や Linux など、Node.js がインストールできれば何でもかまいません。

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

[!INCLUDE [iot-suite-send-external-temperature](../../includes/iot-suite-send-external-temperature.md)]

## <a name="add-a-telemetry-type"></a>テレメトリ タイプの追加
次の手順では、Node.js のシミュレーション対象デバイスによって生成されるテレメトリを、新しい値に差し替えます。

1. コマンド プロンプトまたはシェルで **Ctrl + C** キーを押し、Node.js のシミュレーション対象デバイスを停止します。
2. 既存の温度、湿度、外部温度のテレメトリに使用されるベース データ値は、remote_monitoring.js ファイルで確認できます。 次のように、 **rpm** に使用するベース データ値を追加します。
   
    ```
    // Sensors data
    var temperature = 50;
    var humidity = 50;
    var externalTemperature = 55;
    var rpm = 200;
    ```
3. Node.js のシミュレーション対象デバイスは、remote_monitoring.js ファイルの **generateRandomIncrement** 関数を使用して、ベース データ値にランダムな増分値を加算します。 既にある無作為化の処理に続けて次のコード行を追加し、 **rpm** の値を無作為化します。
   
    ```
    temperature += generateRandomIncrement();
    externalTemperature += generateRandomIncrement();
    humidity += generateRandomIncrement();
    rpm += generateRandomIncrement();
    ```
4. デバイスから IoT Hub に送信される JSON ペイロードに新しい rpm 値を追加します。
   
    ```
    var data = JSON.stringify({
      'DeviceID': deviceId,
      'Temperature': temperature,
      'Humidity': humidity,
      'ExternalTemperature': externalTemperature,
      'RPM': rpm
    });
    ```
5. 次のコマンドを使用して、Node.js のシミュレーション対象デバイスを実行します。
   
    ```
    node remote_monitoring.js
    ```
6. ダッシュボードのグラフに表示される新しい RPM テレメトリ タイプを観察します。

![Add RPM to the dashboard][image3]

> [!NOTE]
> 変更がすぐに反映されない場合は、ダッシュボードの **[デバイス]** ページで一度 Node.js デバイスを無効にしてから有効にしてください。
> 
> 

## <a name="customize-the-dashboard-display"></a>ダッシュボード表示のカスタマイズ
**Device-Info** メッセージには、デバイスから IoT Hub に送信できるテレメトリについてのメタデータを含めることができます。 デバイスから送信されるテレメトリのタイプをこのメタデータで指定することができます。 remote_monitoring.js ファイルの **deviceMetaData** の値に変更を加えます。**Commands** の定義に続けて **Telemetry** の定義を追加してください。 次のコード スニペットは、**Commands** の定義を示しています (**Commands** の定義の後、忘れずに `,` を追加してください)。

```
'Commands': [{
  'Name': 'SetTemperature',
  'Parameters': [{
    'Name': 'Temperature',
    'Type': 'double'
  }]
},
{
  'Name': 'SetHumidity',
  'Parameters': [{
    'Name': 'Humidity',
    'Type': 'double'
  }]
}],
'Telemetry': [{
  'Name': 'Temperature',
  'Type': 'double'
},
{
  'Name': 'Humidity',
  'Type': 'double'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double'
}]
```

> [!NOTE]
> リモート監視ソリューションでは、メタデータの定義とテレメトリ ストリーム内のデータとを比較する際、大文字と小文字が区別されません。
> 
> 

前のコード スニペットに示すように **Telemetry** の定義を追加しても、ダッシュボードの動作は変わりません。 一方、ダッシュボードの表示は、メタデータに **DisplayName** 属性を追加することによってカスタマイズすることもできます。 **Telemetry** のメタデータ定義を以下のスニペットに示すように更新します。

```
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double',
  'DisplayName': 'Outdoor Temperature (C*)'
}
]
```

次のスクリーンショットは、ダッシュボードに表示されるグラフの凡例が、この変更によってどのように変わるかを示しています。

![Customize the chart legend][image4]

> [!NOTE]
> 変更がすぐに反映されない場合は、ダッシュボードの **[デバイス]** ページで一度 Node.js デバイスを無効にしてから有効にしてください。
> 
> 

## <a name="filter-the-telemetry-types"></a>テレメトリの種類のフィルタリング
既定では、テレメトリのストリームに含まれるすべてのデータ系列がダッシュボード上のグラフに表示されます。 **Device-Info** のメタデータを使用すると、特定の種類のテレメトリがグラフに表示されないようにすることができます。 

温度と湿度のテレメトリだけがグラフに表示されるようにするには、**Device-Info** の **Telemetry** メタデータから **ExternalTemperature** をコメントアウトします。

```
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
//{
//  'Name': 'ExternalTemperature',
//  'Type': 'double',
//  'DisplayName': 'Outdoor Temperature (C*)'
//}
]
```

**Outdoor Temperature (屋外の温度)** がグラフに表示されなくなります。

![Filter the telemetry on the dashboard][image5]

この変更は、グラフの表示のみに影響します。 **ExternalTemperature** データの値は保存されており、バックエンド処理に利用することができます。

> [!NOTE]
> 変更がすぐに反映されない場合は、ダッシュボードの **[デバイス]** ページで一度 Node.js デバイスを無効にしてから有効にしてください。
> 
> 

## <a name="handle-errors"></a>エラーを処理する
データ ストリームがグラフに表示されるためには、**Device-Info** メタデータにおける対応する **Type** がテレメトリの値のデータ型と一致する必要があります。 たとえば湿度データの **Type** が、メタデータで **int** と指定されているとき、テレメトリ ストリームに検出されたデータが **double** である場合、その湿度テレメトリはグラフに表示されません。 それでも **Humidity** の値は保存されており、バックエンド処理に利用することができます。

## <a name="next-steps"></a>次のステップ
動的なテレメトリを使用する方法を確認したので、構成済みのソリューションでデバイス情報を使用する方法について「[リモート監視構成済みソリューションのデバイス情報メタデータ][lnk-devinfo]」からさらに学ぶことができます。

[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[image3]: media/iot-suite-dynamic-telemetry/image3.png
[image4]: media/iot-suite-dynamic-telemetry/image4.png
[image5]: media/iot-suite-dynamic-telemetry/image5.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org

