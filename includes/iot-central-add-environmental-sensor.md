---
title: インクルード ファイル
description: インクルード ファイル
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 07/07/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: b32465091f82fec0aeae288ee9bfd5540bfe8b9d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87002048"
---
## <a name="create-a-device-template"></a>デバイス テンプレートを作成する

ローカル コンピューターに `environmental-sensor` という名前のフォルダーを作成します。

[環境センサー機能モデル](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/9004219bff1e958b7cd6ff2a52209f4b7ae19396/samples/EnvironmentalSensorInline.capabilitymodel.json)の JSON ファイルをダウンロードして `environmental-sensor` フォルダーに保存します。

ダウンロードした `EnvironmentalSensorInline.capabilitymodel.json` ファイル内で 2 か所出現する `{YOUR_COMPANY_NAME_HERE}` を、テキスト エディターを使用して実際の会社名に置き換えます。 英数字 (a から z、A から Z、0 から 9) とアンダースコアのみを使用してください。

お使いの Azure IoT Central アプリケーションで、`EnvironmentalSensorInline.capabilitymodel.json` デバイス機能モデル ファイルをインポートして、"*環境センサー*" というデバイス テンプレートを作成します。

![デバイス機能モデルがインポートされたデバイス テンプレート](./media/iot-central-add-environmental-sensor/device-template.png)

このデバイス機能モデルには、標準の **[デバイス情報]** インターフェイスとカスタムの **[環境センサー]** インターフェイスという 2 つのインターフェイスが含まれています。 **[環境センサー]** インターフェイスには、次の機能が定義されています。

| Type | 表示名 | 説明 |
| ---- | ------------ | ----------- |
| プロパティ | デバイス状態     | デバイスの状態。 オンラインとオフラインの 2 つの状態があります。 |
| プロパティ (書き込み可能) | Customer Name    | 現在デバイスを操作しているユーザーの名前。 |
| プロパティ (書き込み可能) | Brightness Level (明るさのレベル) | デバイスのライトに使用する明るさのレベル。 1 (高)、2 (中)、3 (低) を指定できます。 |
| テレメトリ | 気温 | デバイスによって検出された現在の温度。 |
| テレメトリ | 湿度    | デバイスによって検出された現在の湿度。 |
| コマンド | blink          | 指定された時間間隔でデバイスの LED の点滅を開始します。 |
| コマンド | turnon         | デバイスの LED をオンにします。 |
| コマンド | turnoff        | デバイスの LED をオフにします。 |
| コマンド | rundiagnostics | この非同期コマンドは、デバイスで実行される診断を開始します。 |

IoT Central アプリケーションにおける **[デバイスの状態]** プロパティの表示方法をカスタマイズするには、デバイス テンプレートの **[カスタマイズ]** を選択します。 **[デバイスの状態]** エントリを展開し、 **[True の名前]** として「_Online_」、 **[False の名前]** として「_Offline_」を入力します。 変更を保存します。

![デバイス テンプレートをカスタマイズする](./media/iot-central-add-environmental-sensor/customize-template.png)

## <a name="create-views"></a>ビューの作成

IoT Central アプリケーションに接続されたデバイスは、ビューを使って操作できます。 たとえば、テレメトリを表示するビュー、プロパティを表示するビュー、書き込み可能なプロパティやクラウドのプロパティを編集できるビューが考えられます。 ビューは、デバイス テンプレートの構成要素です。

**[環境センサー]** デバイス テンプレートに既定のビューを追加するには、お使いのデバイス テンプレートに移動し、 **[ビュー]** を選択して、 **[既定のビューの生成]** タイルを選択します。 **[概要]** と **[About]\(情報\)** が **[オン]** になっていることを確認してから、 **[既定のダッシュボード ビューの生成]** を選択します。 これでテンプレートには、既定のビューが 2 つ定義されました。

**[環境センサー]** インターフェイスには、 **[Customer Name]\(カスタマー名\)** と **[Brightness Level]\(明るさのレベル\)** という、書き込み可能なプロパティが 2 つあります。 これらのプロパティの編集に使用できるビューを作成するには、次の手順に従います。

1. **[ビュー]** を選択し、 **[デバイスとクラウドのデータの編集]** タイルを選択します。

1. フォーム名として「_Properties_」を入力します。

1. **[Brightness Level]\(明るさのレベル\)** および **[Customer Name]\(カスタマー名\)** プロパティを選択します。 次に、 **[セクションの追加]** を選択します。

1. 変更を保存します。

![プロパティを編集できるビューを追加する](./media/iot-central-add-environmental-sensor/properties-view.png)

## <a name="publish-the-template"></a>テンプレートを発行する

**[環境センサー]** デバイス テンプレートを使用するデバイスを追加する前に、それを発行しておく必要があります。

デバイス テンプレートで **[発行]** を選択します。 **[このデバイス テンプレートのアプリケーションへの発行]** パネルで、 **[発行]** を選択します。

テンプレートを使用する準備が整っていることを確認するために、IoT Central アプリケーションの **[デバイス]** ページに移動します。 発行済みデバイスの一覧がアプリケーションの **[デバイス]** セクションに表示されます。

![[デバイス] ページの発行済みテンプレート](./media/iot-central-add-environmental-sensor/published-templates.png)

## <a name="add-a-real-device"></a>実デバイスの追加

Azure IoT Central アプリケーションで、前のセクションで作成したデバイス テンプレートに実デバイスを追加します。

1. **[デバイス]** ページで、 **[環境センサー]** デバイス テンプレートを選択します。

1. **[+新規]** を選択します。

1. **[Create a new device]\(新しいデバイスの作成\)** ダイアログで、 **[環境センサー]** がテンプレート型であり、 **[Simulate this device?]\(このデバイスをシミュレートしますか?\)** が **[いいえ]** に設定されていることを確認します。

1. **[作成]** を選択します。

デバイス名をクリックし、 **[接続]** を選択します。 **[デバイスの接続]** ページで、デバイスの接続情報を書き留めておきます ( **[ID スコープ]** 、 **[デバイス ID]** 、 **[プライマリ キー]** )。 デバイス コードを作成する際に、これらの値が必要になります。

![デバイスの接続情報](./media/iot-central-add-environmental-sensor/device-connection.png)
