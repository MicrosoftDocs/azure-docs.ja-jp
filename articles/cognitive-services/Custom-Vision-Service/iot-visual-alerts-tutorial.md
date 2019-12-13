---
title: チュートリアル:IoT Visual Alerts サンプル
titleSuffix: Azure Cognitive Services
description: このチュートリアルでは、IoT デバイスで Custom Vision を使用して、カメラのビデオ フィードから視覚的な状態を認識し、報告します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 9f3802ada79ee87d1a04634f7caac3b1b4286dce
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978034"
---
# <a name="tutorial-use-custom-vision-with-an-iot-device-to-report-visual-states"></a>チュートリアル:IoT デバイスで Custom Vision を使用して視覚的な状態を報告する

このサンプル アプリでは、Custom Vision を使用して、カメラでデバイスをトレーニングして視覚的な状態を検出する方法を示します。 この検出シナリオは、Custom Vision サービスからエクスポートされた ONNX モデルを使用して、IoT デバイスで実行できます。

視覚的な状態は、誰もいない部屋あるいは人がいる部屋、または空の私道やトラックが通っている私道などの画像の内容を表します。 次の図では、カメラの前にバナナまたはりんごが配置されているかどうかをアプリが検出しています。

![カメラの前にあるフルーツにラベル付けされる UI のアニメーション](./media/iot-visual-alerts-tutorial/scoring.gif)

ここでは、次の操作方法について説明します。
> [!div class="checklist"]
> * 独自の Custom Vision と IoT Hub リソースを使用するようにサンプル アプリを構成します。
> * アプリを使用して、Custom Vision プロジェクトをトレーニングします。
> * アプリを使用して、新しいイメージをリアルタイムでスコア付けし、結果を Azure に送信します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。 

## <a name="prerequisites"></a>前提条件

* [!INCLUDE [create-resources](includes/create-resources.md)]
    > [!IMPORTANT]
    > このプロジェクトではモデルを後で ONNX にエクスポートするため、**コンパクト**な画像分類プロジェクトである必要があります。
* また、Azure で [IoT Hub リソースを作成](https://ms.portal.azure.com/#create/Microsoft.IotHub)する必要があります。
* [Visual Studio 2015 またはそれ以降](https://www.visualstudio.com/downloads/)
* 必要に応じて、Windows 10 IoT Core バージョン 17763 以上を実行している IoT デバイス。 PC からアプリを直接実行することもできます。
   * Raspberry Pi 2 および 3 では、IoT ダッシュボード アプリから Windows 10 を直接設定できます。 DrangonBoard などの他のデバイスの場合、[eMMC の方法](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup#flashing-with-emmc-for-dragonboard-410c-other-qualcomm-devices)を使用してフラッシュする必要があります。 新しいデバイスのセットアップでヘルプが必要な場合は、Windows IoT ドキュメントの[「デバイスのセットアップ」](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup)を参照してください。

## <a name="about-the-visual-alerts-app"></a>Visual Alerts アプリについて

IoT Visual Alerts アプリは状況に応じて 4 つの異なる状態を切り替えて連続したループで実行されています。

* **モデルなし**:操作なしの状態です。 アプリは継続的に 1 秒間スリープ状態になり、カメラを検査します。
* **トレーニング イメージのキャプチャ中**:この状態では、アプリは画像をキャプチャし、ターゲットの Custom Vision プロジェクトにトレーニング イメージとしてアップロードします。 その後、アプリは 500 ミリ秒間スリープ状態になり、イメージのターゲット数が設定回数キャプチャされるまで操作を繰り返します。 次に、Custom Vision モデルのトレーニングをトリガーします。
* **トレーニング済みモデルの待機中**:この状態では、アプリは 1 秒ごとに Custom Vision API を呼び出して、ターゲット プロジェクトにトレーニング済みのイテレーションが含まれているかどうかを確認します。 トレーニング済みのイテレーションが検出されると、対応する ONNX モデルがローカル ファイルにダウンロードされ、**スコアリング**状態に切り替わります。
* **スコアリング**:この状態では、アプリはローカル ONNX モデルに対して Windows ML を使用してカメラから 1 つのフレームを評価します。 最終的なイメージの分類が画面に表示され、IoT Hub にメッセージとして送信されます。 アプリは、新しいイメージをスコアリングする前に 1 秒間スリープ状態になります。

## <a name="understand-the-code-structure"></a>コードの構造を理解する

このアプリの主な機能は、以下のファイルで処理されています。

| ファイル | 説明 |
|-------------|-------------|
| [MainPage.xaml](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml) | このファイルは、XAML ユーザー インターフェイスを定義します。 Web カメラ コントロールをホストし、ステータスの更新に使用されるラベルを含みます。|
| [MainPage.xaml.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml.cs) | このコードは、XAML UI の動作を制御します。 ステート マシンの処理コードが含まれています。|
| [CustomVision\CustomVisionServiceWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionServiceWrapper.cs) | このクラスは、Custom Vision Service との統合を処理するラッパーです。|
| [CustomVision\CustomVisionONNXModel.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionONNXModel.cs) | このクラスは、ONNX モデルとスコアリング イメージを読み込む Windows ML との統合を処理するラッパーです。|
| [IoTHub\IotHubWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/IoTHub/IotHubWrapper.cs) | このクラスは、スコアリング結果を Azure にアップロードする IoT Hub との統合を処理するラッパーです。|

## <a name="set-up-the-visual-alerts-app"></a>Visual Alerts アプリの設定

次のステップに従って、お使いの PC または IoT デバイスで実行されている IoT Visual Alerts アプリを取得します。

1. GitHub で [IoTVisualAlerts サンプル](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts)を複製またはダウンロードします。
1. Visual Studio で、ソリューション _IoTVisualAlerts.sln_ を開きます
1. Custom Vision プロジェクトを次のように統合します。
    1. _CustomVision\CustomVisionServiceWrapper.cs_ スクリプトで、トレーニング キーを使用して `ApiKey` 変数を更新します。
    1. 次に、キーに関連付けられているエンドポイント URL を使用して、`Endpoint` 変数を更新します。
    1. `targetCVSProjectGuid` 変数を、使用する Custom Vision プロジェクトに対応する ID に更新します。 
1. IoT Hub リソースを次のように設定します。
    1. _IoTHub\IotHubWrapper.cs_ スクリプトで、`s_connectionString` 変数をデバイスの適切な接続文字列に更新します。 
    1. Azure portal で IoT Hub インスタンスを読み込み、 **[エクスプローラー]** の下にある **[IoT デバイス]** をクリックしてターゲット デバイスを選択し (または必要に応じて作成し)、 **[プライマリ接続文字列]** から接続文字列を指定します。 文字列には、`{your iot hub name}.azure-devices.net;DeviceId={your device id};SharedAccessKey={your access key}` の形式の IoT Hub 名、デバイス ID、共有アクセスキーが含まれます。

## <a name="run-the-app"></a>アプリの実行

PC でアプリを実行している場合は、Visual Studio ではターゲット デバイスの **[ローカル コンピューター]** を選択し、ターゲット プラットフォームでは **x64** または **x86** を選択します。 その後、F5 を押して、プログラムを実行します。 アプリが起動し、カメラからのライブ フィードとステータス メッセージが表示されます。

ARM プロセッサを搭載した IoT デバイスにデプロイする場合は、ターゲット プラットフォームとして **ARM** を選択し、ターゲット デバイスとして**リモート マシン**を選択する必要があります。 メッセージが表示されたら、デバイスの IP アドレスを指定します (PC と同じネットワーク上にある必要があります)。 デバイスを起動してネットワークに接続すると、Windows IoT の既定アプリから IP アドレスを取得できます。 F5 を押して、プログラムを実行します。

アプリの初回実行時は、視覚的な状態に関する情報は表示されません。 使用可能なモデルがないことを示すステータス メッセージが表示されます。 

## <a name="capture-training-images"></a>トレーニング イメージをキャプチャする

モデルを設定するには、アプリを**トレーニング イメージのキャプチャ中**状態にする必要があります。 次のいずれかのステップを使用します。
* PC でアプリを実行している場合は、UI の右上隅にあるボタンを使用します。
* アプリを IoT デバイスで実行している場合は、IoT Hub を通してデバイスで `EnterLearningMode` メソッドを呼び出します。 これは、Azure portal の [IoT Hub] メニューのデバイス エントリ、または [IoT Hub Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) などのツールを使用して呼び出すことができます。
 
アプリの状態が**トレーニング イメージのキャプチャ中**になると、目標のイメージ数に達するまで、1 秒ごとに 2 枚のイメージがキャプチャされます。 既定のイメージ数は 30 ですが、設定したい数を引数として `EnterLearningMode` IoT Hub メソッドに渡すことによって、このパラメーターを設定できます。 

アプリがイメージをキャプチャしている間、検出する視覚的な状態の種類 (たとえば、空の部屋、人がいる部屋、空の机、おもちゃのトラックが乗っている机など) にカメラを向ける必要があります。

## <a name="train-the-custom-vision-model"></a>Custom Vision モデルをトレーニングする

アプリでイメージのキャプチャが完了すると、イメージがアップロードされ、**トレーニング済みモデルの待機中**状態に切り替わります。 切り替わったら [Custom Vision ポータル](https://www.customvision.ai/)にアクセスし、新しいトレーニング イメージに基づいてモデルを作成する必要があります。 以下のアニメーションはこのプロセスの例を示したものです。

![アニメーション: 複数のバナナの画像にタグ付けする](./media/iot-visual-alerts-tutorial/labeling.gif)

独自のシナリオでこのプロセスを繰り返すには、次のステップを実行します。

1. [Custom Vision ポータル](http://customvision.ai)にサインインします。
1. アプリがアップロードしたすべてのトレーニング イメージが含まれているターゲット プロジェクトを見つけます。
1. 識別する視覚的な状態ごとに適切なイメージを選択し、タグを手動で適用します。
    * たとえば、空の部屋と人がいる部屋を区別することが目的である場合は、人が含まれている 5 枚以上の画像を新しいクラス (**人**) としてタグ付けし、人がいない 5 枚以上の写真を**負**のタグとしてタグ付けすることをお勧めします。 これにより、モデルは 2 つの状態を区別できます。
    * もう 1 つの例として、棚がどの程度いっぱいであるかを推定したい場合は、**EmptyShelf**、**PartiallyFullShelf**、**FullShelf** のようなタグを使用します。
1. 完了したら、 **[トレーニングする]** ボタンをクリックします。
1. トレーニングが完了すると、アプリがトレーニング済みのイテレーションが使用可能であることを検出します。 トレーニング済みのモデルを ONNX にエクスポートし、デバイスにダウンロードするプロセスがアプリで開始されます。

## <a name="use-the-trained-model"></a>トレーニング済みのモデルを使用する

トレーニング済みのモデルがアプリによってダウンロードされると**スコアリング**状態に切り替わり、連続したループでカメラからのイメージのスコアリングが開始されます。

アプリでは、キャプチャされたイメージごとに、上位のタグが画面に表示されます。 視覚的な状態が認識できない場合は **[一致なし]** と表示されます。 また、アプリはこれらのメッセージを IoT Hub に送信し、クラスが検出された場合、メッセージには、ラベル、信頼スコア、および `detectedClassAlert` というプロパティが含まれます。このプロパティは、プロパティに基づく高速メッセージ ルーティングを行うために必要な IoT Hub クライアントで使用できます。

さらに、このサンプルでは、[Sense HAT ライブラリ](https://github.com/emmellsoft/RPi.SenseHat)を使用して Raspberry Pi 上で Sense HAT unit がいつ実行されているかを検出します。これにより、クラスが検出されるたびにすべてのディスプレイ ライトを赤に設定して、検出されていない間は空白に設定する出力ディスプレイとして使用できます。

## <a name="reuse-the-app"></a>アプリを再利用する

アプリを元の状態にリセットする場合は、UI の右上隅にあるボタンをクリックするか、IoT Hub を使用して `DeleteCurrentModel` メソッドを呼び出します。

右上の UI ボタンをクリックするか、`EnterLearningMode` メソッドを再度呼び出すことで、いつでもトレーニング イメージのアップロード ステップを繰り返すことができます。

アプリをデバイスで実行していて、IP アドレスを再度取得する必要がある場合 (たとえば、[Windows IoT Remote Client](https://www.microsoft.com/p/windows-iot-remote-client/9nblggh5mnxz#activetab=pivot:overviewtab) を使用してリモート接続を確立する場合)、IoT Hub を使用して `GetIpAddress` メソッドを呼び出すことができます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Custom Vision プロジェクトを保持する必要がなくなった場合は削除します。 [Custom Vision Web サイト](https://customvision.ai)で、 **[Projects]\(プロジェクト\)** に移動し、新しいプロジェクトのごみ箱アイコンを選択します。

!["My New Project" とごみ箱アイコンが表示されたパネルのスクリーンショット](./media/csharp-tutorial/delete_project.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、IoT デバイスで視覚的な状態に関する情報を検出し、結果を IoT Hub に送信するアプリケーションを設定して実行しました。 次の手順として、ソース コードをさらに調査するか、以下の推薦される変更を行います。

> [!div class="nextstepaction"]
> [IoTVisualAlerts サンプル (GitHub)](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts)

* IoT Hub メソッドを追加して、アプリを**トレーニング済みモデルの待機中**状態に直接切り替えます。 これにより、デバイス以外でキャプチャされたイメージを使用してモデルをトレーニングし、新しいモデルをコマンドを通じてデバイスにプッシュすることができます。
* Power BI ダッシュボードを作成し、サンプルによって送信された IoT Hub アラートを視覚化するには、[リアルタイム センサー データを視覚化する](https://docs.microsoft.com/azure/iot-hub/iot-hub-live-data-visualization-in-power-bi)方法に関するチュートリアルに従います。
* 視覚的な状態が検出されたときに IoT Hub アラートに応答するロジック アプリを作成するには、[IoT リモート監視](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps)に関するチュートリアルに従います。
