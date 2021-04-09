---
title: チュートリアル - Azure IoT Central のビデオ分析 (物体とモーションの検出) アプリケーション テンプレートを使用してビデオを監視する
description: このチュートリアルでは、ビデオ分析 (物体とモーションの検出) アプリケーション テンプレートのダッシュボードを使用してカメラを管理し、ビデオを監視する方法について説明します。
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: fbfef094cd062e437f2a28369162de96631ef41b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99832608"
---
# <a name="tutorial-monitor-and-manage-a-video-analytics---object-and-motion-detection-application"></a>チュートリアル:ビデオ分析 (物体とモーションの検出) アプリケーションを監視、管理する

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * 物体とモーションの検出カメラを IoT Central アプリケーションに追加する。
> * ビデオ ストリームを管理して、関心のあるイベントが検出されたときにそれらを再生する。

## <a name="prerequisites"></a>前提条件

開始する前に、以下を完了しておく必要があります。

* これよりも前の [Azure IoT Central でのライブ ビデオ分析アプリケーションの作成](./tutorial-video-analytics-create-app-yolo-v3.md)に関するチュートリアルか、[Azure IoT Central でのビデオ分析の作成 (OpenVINO&trade;)](tutorial-video-analytics-create-app-openvino.md) に関するチュートリアルのいずれか一方。
* 既出の、[ライブ ビデオ分析用の IoT Edge インスタンスの作成 (Linux VM)](tutorial-video-analytics-iot-edge-vm.md) または[ライブ ビデオ分析用の IoT Edge インスタンスの作成 (Intel NUC)](tutorial-video-analytics-iot-edge-nuc.md) に関するチュートリアルのいずれか。

ビデオ ビューアー アプリケーションを実行するためには、ローカル コンピューターに [Docker](https://www.docker.com/products/docker-desktop) がインストールされている必要があります。

## <a name="add-an-object-detection-camera"></a>物体検出カメラを追加する

IoT Central アプリケーションで、以前作成した **LVA Gateway 001** デバイスに移動します。 その後、 **[コマンド]** タブを選択します。

**[Add Camera Request]\(カメラ要求の追加\)** コマンドには、パラメーターの値として次の表の値を使用します。 この表に示した値は、Azure VM でシミュレートされたカメラが使用されていることを前提としています。実際のカメラを使用している場合は、値を適宜調整してください。

| フィールド| 説明| 値の例|
|---------|---------|---------|
| Camera ID (カメラ ID)      | プロビジョニングの対象となるデバイス ID | camera-003 |
| Camera Name (カメラ名)    | フレンドリ名           | 物体検出カメラ |
| RTSP Url (RTSP URL)       | ストリームのアドレス   | RTSP://10.0.0.4:554/media/camera-300s.mkv|
| RTSP Username (RTSP ユーザー名)  |                         | user    |
| RTSP Password (RTSP パスワード)  |                         | password    |
| 検出の種類 | ドロップダウン                | オブジェクトの検出       |

**[実行]** を選択してカメラ デバイスを追加します。

:::image type="content" source="media/tutorial-video-analytics-manage/add-camera.png" alt-text="カメラを追加する":::

> [!NOTE]
> アプリケーションには、**LVA Edge Object Detector** デバイス テンプレートがあらかじめ存在します。

## <a name="add-a-motion-detection-camera-optional"></a>モーション検出カメラを追加する (省略可)

ご使用の IoT Edge ゲートウェイ デバイスに 2 台のカメラが接続されている場合は、前の手順を繰り返してモーション検出カメラをアプリケーションに追加します。 **[Camera ID]\(カメラ ID\)** 、 **[Camera Name]\(カメラ名\)** 、 **[RTSP Url]\(RTSP URL\)** の各パラメーターには、異なる値を使用してください。

## <a name="view-the-downstream-devices"></a>ダウンストリーム デバイスを表示する

先ほど追加したカメラ デバイスを表示するには、**LVA Gateway 001** デバイスの **[Downstream Devices]\(ダウンストリーム デバイス\)** タブを選択します。

:::image type="content" source="media/tutorial-video-analytics-manage/inspect-downstream.png" alt-text="検査":::

カメラ デバイスは、アプリケーションの **[デバイス]** ページの一覧にも表示されます。

## <a name="configure-and-manage-the-camera"></a>カメラを構成して管理する

**camera-003** デバイスに移動し、 **[管理]** タブを選択します。

既定値を使用してかまいませんが、デバイス プロパティをカスタマイズする必要がある場合は変更してください。

**カメラ設定**

| プロパティ | 説明 | 推奨値 |
|-|-|-|
| Video Playback Host (ビデオ再生ホスト) | Azure Media Player ビューアーのホスト | http://localhost:8094 |

**LVA の操作と診断**

| プロパティ | 説明 | 推奨値 |
|-|-|-|
| 自動開始 | LVA Gateway の再起動時に物体検出を開始します | オン |
| Debug Telemetry (デバッグ テレメトリ) | イベント トレース | 省略可能 |
|Inference Timeout (sec) (推論タイムアウト (秒))| 推論が停止したと判断するための基準として使用する時間 | 20 |

**AI 物体検出**

| プロパティ | 説明 | 推奨値 |
|-|-|-|
| Max Inference Capture Time (sec) (最大推論キャプチャ時間 (秒)) | 最大キャプチャ時間 | 15 |
| Detection Classes (検出クラス) | 検出タグのコンマ区切りの文字列。 詳細については、[サポートされているタグの一覧](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/yolov3-onnx/tags.txt)を参照してください。 | truck、bus |
| Confidence Threshold (信頼度のしきい値) | 物体検出が有効であるかどうかを判断するための適格性の割合 | 70 |
| Inference Frame Sample Rate (fps) (推論フレーム サンプル レート (fps)) | [ここに説明を追加してください] | 2 |

**[保存]** を選択します。

数秒後、各設定について、"**Accepted (受理されました)** " という確認メッセージが表示されます。

:::image type="content" source="media/tutorial-video-analytics-manage/object-detection.png" alt-text="物体検出":::

## <a name="start-lva-processing"></a>LVA 処理を開始する

**camera-003** デバイスに移動し、 **[コマンド]** タブを選択します。

**[Start LVA Processing]\(LVA 処理を開始する\)** コマンドを実行します。

コマンドが完了したら、コマンド履歴を見てエラーがないことを確認します。

:::image type="content" source="media/tutorial-video-analytics-manage/start-processing.png" alt-text="[Start LVA Processing]\(LVA 処理を開始する\) コマンド":::

## <a name="monitor-the-cameras"></a>カメラを監視する

**camera-003** デバイスに移動し、 **[ダッシュボード]** タブを選択します。

:::image type="content" source="media/tutorial-video-analytics-manage/camera-dashboard.png" alt-text="カメラ ダッシュボード":::

**[検出数]** タイルには、選択した検出クラスの物体ごとに、1 秒の検出間隔における平均検出数が表示されます。

**[Detection Classes]\(検出クラス\)** 円グラフには、各クラス タイプの検出の割合が表示されます。

**[Inference Event Video]\(推論イベント ビデオ\)** は、検出結果を含んだ Azure Media Services 資産へのリンク一覧です。 このリンクには、次のセクションで説明するホスト プレーヤーが使用されています。

## <a name="start-the-streaming-endpoint"></a>ストリーミング エンドポイントを開始する

記録されたビデオをクライアント Media Player アプリケーションでストリーム配信するには、Media Services エンドポイントを開始します。

* Azure portal で、**lva-rg** リソース グループに移動します。
* **ストリーミング エンドポイント** リソースを開きます。
* **[ストリーミング エンドポイントの詳細情報]** ページで **[開始]** を選択します。 エンドポイントを開始すると課金が開始されるという警告が表示されます。

## <a name="view-stored-video"></a>保存されたビデオを見る

カメラを監視して不審な画像に対応する時代は終わりました。 自動イベント タグ付けと、推論された検出によって保存されたビデオへの直接リンクを使用すれば、セキュリティ オペレーターは、関心のあるイベントをリストから見つけ出し、リンクをたどってそのビデオを見ることができます。

[AMP ビデオ プレーヤー](https://github.com/Azure/live-video-analytics/tree/master/utilities/amp-viewer)を使用して、Azure Media Services アカウントに格納されているビデオを表示することができます。

そのビデオは IoT Central アプリケーションによって Azure Media Services に保存されるので、そこからストリーム配信することができます。 Azure Media Services に保存されているビデオを再生するには、ビデオ プレーヤーが必要です。

ローカル コンピューターで **Docker** が実行されていることを確認してください。

ローカル コンピューター上の Docker コンテナーでビデオ プレーヤーを実行するには、コマンド プロンプトを開き、以下のコマンドを使用します。 コマンド内のプレースホルダーは、*scratchpad.txt* ファイルに事前に書き留めた値に置き換えてください。 Media Services アカウントのサービス プリンシパルを作成する際に `amsAadClientId`、`amsAadSecret`、`amsAadTenantId`、`amsSubscriptionId`、`amsAccountName` を書き留めました。

```bash
docker run -it --rm -e amsAadClientId="<FROM_AZURE_PORTAL>" -e amsAadSecret="<FROM_AZURE_PORTAL>" -e amsAadTenantId="<FROM_AZURE_PORTAL>" -e amsArmAadAudience="https://management.core.windows.net" -e amsArmEndpoint="https://management.azure.com" -e amsAadEndpoint="https://login.microsoftonline.com" -e amsSubscriptionId="<FROM_AZURE_PORTAL>" -e amsResourceGroup="<FROM_AZURE_PORTAL>" -e amsAccountName="<FROM_AZURE_PORTAL>" -p 8094:8094 mcr.microsoft.com/lva-utilities/amp-viewer:1.0-amd64
```

|Docker パラメーター | AMS API アクセス (JSON)|
|-|-|
|amsAadClientId| AadClientId|
|amsAadSecret| AadSecret |
|amsAadTenantId| AadTenantId |
|amsSubscriptionId| SubscriptionId|
|amsResourceGroup| ResourceGroup |
|amsAccountName| AccountName|

**camera-003** デバイスに移動し、 **[ダッシュボード]** タブを選択します。次に、 **[Inference Event Video]\(推論イベント ビデオ\)** タイルで、キャプチャされた物体検出のいずれかのハイパーリンクをクリックします。 ローカル ビデオ プレーヤーによって表示されたページにビデオが表示されます。

:::image type="content" source="media/tutorial-video-analytics-manage/video-snippet.png" alt-text="ビデオ スニペット":::

## <a name="change-the-simulated-devices-in-application-dashboards"></a>アプリケーション ダッシュボードのシミュレートされたデバイスを変更する

アプリケーション ダッシュボードには最初、IoT Central のシミュレートされたデバイスから生成されたテレメトリやプロパティが表示されています。 実際のカメラや Live555 シミュレーターから生成されたテレメトリが表示されるようにタイルを構成するには、これらの手順に従います。

1. **[(Sample) Real Camera Monitor]\((サンプル) 実際のカメラ モニター\)** アプリケーション ダッシュボードに移動します。
1. **[編集]** を選択します。
1. **[Note]\(メモ\)** タイルを選択して削除します。
1. ダッシュボードのタイトルを *[Real Camera Monitor]\(実際のカメラ モニター\)* に変更します。
1. **[Inference Count]\(推論数\)** タイルで、構成アイコンを選択します。
1. **[グラフの構成]** セクションで、**LVA Edge Object Detector** デバイス グループから実際のカメラを少なくとも 1 つ選択します。
1. `AI Inference Interface/Inference Count` テレメトリ フィールドを選択します。
1. **[Update]\(更新\)** を選択します。

1. 以上の手順を次のタイルについても行います。
    1. **[検出]** 円グラフ: `AI Inference Interface/Inference/entity/tag/value` テレメトリ タイプを使用します。
    1. **[推論]** : 最後の既知の値 `AI Inference Interface/Inference/entity/tag/value` を使用します。
    1. **[信頼度 %]** : 最後の既知の値 `AI Inference Interface/Inference/entity/tag/confidence` を使用します。
    1. **[スナップショット]** : 画像として表示される `AI Inference Interface/Inference Image` を使用します。
    1. **[Inference Event Video]\(推論イベント ビデオ\)** : リンクとして表示される `AI Inference Interface/Inference Event Video` を使用します。
1. **[保存]** を選択します。

**[Real Camera Monitor]\(実際のカメラ モニター\)** ダッシュボードに、実際のカメラ デバイスからの値が表示されます。

:::image type="content" source="media/tutorial-video-analytics-manage/update-real-cameras.png" alt-text="実際のカメラのアプリケーション ダッシュボード":::

## <a name="pause-processing"></a>処理を一時停止する

アプリケーションのライブ ビデオ分析処理を一時停止することができます。

1. アプリケーションで、物体検出カメラの **[コマンド]** ページに移動します。 次に、 **[Stop LVA Processing]\(LVA 処理を停止する\)** コマンドを実行します。

1. Media Services アカウントのストリーミング エンドポイントを停止します。
    * Azure portal で、**lva-rg** リソース グループに移動します。
    * **ストリーミング エンドポイント** リソースをクリックします。
    * **[ストリーミング エンドポイントの詳細情報]** ページで **[停止]** を選択します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

アプリケーションを使い終えたら、次の手順で、作成したリソースをすべて削除することができます。

1. IoT Central アプリケーションの **[管理]** セクションの **[お客様のアプリケーション]** ページに移動します。 次に、 **[削除]** を選択します。
1. Azure portal で、**lva-rg** リソース グループを削除します。
1. ローカル コンピューターで、**amp-viewer** Docker コンテナーを停止します。

## <a name="next-steps"></a>次のステップ

以上、IoT Central アプリケーションにカメラを追加し、物体とモーションを検出するための構成を行う方法について説明しました。

IoT Edge モジュールのソース コードをカスタマイズする方法を学習するには:

> [!div class="nextstepaction"]
> [ライブ ビデオ分析ゲートウェイ モジュールの変更とビルド](./tutorial-video-analytics-build-module.md)
