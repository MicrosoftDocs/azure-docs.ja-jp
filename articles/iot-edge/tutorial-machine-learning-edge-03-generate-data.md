---
title: シミュレートされたデバイス データを生成する - Azure IoT Edge での Machine Learning | Microsoft Docs
description: 後で機械学習モデルをトレーニングするために使用できるシミュレートされたテレメトリを生成する仮想デバイスを作成します。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 666172e3685b923ca0d0e5fa02878341fcd0a216
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543869"
---
# <a name="tutorial-generate-simulated-device-data"></a>チュートリアル:シミュレートされたデバイスのデータを生成する

> [!NOTE]
> この記事は、IoT Edge 上で Azure Machine Learning を使用するためのチュートリアルのシリーズの一部です。 この記事に直接アクセスしている場合は、最適な結果を得るために、シリーズの[最初の記事](tutorial-machine-learning-edge-01-intro.md) から始めることをお勧めします。

この記事では、機械学習のトレーニング データを使用して、IoT Hub にテレメトリを送信するデバイスをシミュレートします。 概要で説明したように、このエンド ツー エンドのチュートリアルでは[ターボファン エンジンの劣化シミュレーション データ セット](https://c3.nasa.gov/dashlink/resources/139/)を使用して、トレーニングとテストのための 1 組の航空機エンジンのデータをシミュレートします。

付随する readme.txt から、次のことがわかっています。

* データは、複数の多変量時系列で構成されている
* 各データ セットは、トレーニングとテストのサブセットに分割される
* 各時系列は、別のエンジンのものである
* 各エンジンには、最初から程度の異なる初期摩耗と製造のばらつきがある

このチュートリアルでは、1 つのデータ セット (FD003) のトレーニング データ サブセットを使用します。

実際には、各エンジンは独立した IoT デバイスになります。 インターネットに接続されたターボファン エンジンの使用可能なコレクションがないと仮定しているため、これらのデバイスのためのソフトウェア スタンドインを構築します。

このシミュレーターは、IoT Hub API を使用して、仮想デバイスをプログラムで IoT Hub に登録する C# プログラムです。 次に、NASA で提供されたデータ サブセットのデバイスごとのデータを読み取り、シミュレートされた IoT デバイスを使用してそれを IoT ハブに送信します。 チュートリアルのこの部分のすべてのコードは、リポジトリの DeviceHarness ディレクトリで見つけることができます。

DeviceHarness プロジェクトは、次の 4 つのクラスから成る C# で記述された .NET Core プロジェクトです。

* **Program:** ユーザー入力の処理と全体的な調整を担当する実行のためのエントリ ポイント。
* **TrainingFileManager:** 選択されたデータ ファイルの読み取りと解析を担当します。
* **CycleData:** メッセージ形式に変換されたファイル内の 1 行のデータを表します。
* **TurbofanDevice:** 1 つのデバイス (時系列) に対応する IoT デバイスのデータ内への作成と、そのデータの IoT デバイス経由での IoT Hub への送信を担当します。

この記事で説明されているタスクは、完了するまでに約 20 分かかります。

この手順に相当する実際の作業は、デバイス開発者やクラウド開発者によって実行される可能性があります。

## <a name="configure-visual-studio-code-and-build-deviceharness-project"></a>Visual Studio Code を構成して DeviceHarness プロジェクトをビルドする

1. 前の記事に示されているように、仮想マシンへのリモート デスクトップ セッションを開きます。

1. Visual Studio Code を開きます。

1. Visual Studio Code で、 **[ファイル]**  >  **[フォルダーを開く]** の順に選択します。

1. **[フォルダー]** テキスト ボックスに「`C:\source\IoTEdgeAndMlSample\DeviceHarness`」と入力し、 **[フォルダーの選択]** ボタンをクリックします。

   出力ウィンドウに OmniSharp のエラーが表示された場合は、C# 拡張機能をアンインストールし、VS Code を閉じて再度開き、C# 拡張機能をインストールしてからウィンドウを再度読み込む必要があります。

1. このコンピューター上で初めて拡張機能を使用しているため、一部の拡張機能が更新され、それらの依存関係がインストールされます。 拡張機能を更新するよう求められることがあります。 その場合は、 **[ウィンドウの再読み込み]** を選択します。

1. DeviceHarness に必要な資産を追加するよう求められます。 **[はい]** を選択して追加します。

   * この通知が表示されるまでに数秒かかることがあります。
   * この通知を見逃した場合は、右下隅にある [ベル] アイコンを確認します。

   ![VS Code 拡張機能のポップアップ](media/tutorial-machine-learning-edge-03-generate-data/add-required-assets.png)

1. **[復元]** を選択して、パッケージの依存関係を復元します。

   ![VS Code の復元のプロンプト](media/tutorial-machine-learning-edge-03-generate-data/restore-package-dependencies.png)

1. ビルド (`Ctrl + Shift + B` または **[ターミナル]**  >  **[ビルド タスクの実行]** ) をトリガーすることによって、環境が正しく設定されていることを検証します。

1. 実行するビルド タスクを選択するよう求められます。 **[Build]\(ビルド\)** を選択します。

1. ビルドが実行され、成功メッセージが出力されます。

   ![ビルドの成功を示す出力メッセージ](media/tutorial-machine-learning-edge-03-generate-data/build-success.png)

1. **[ターミナル]**  >  **[Configure Default Build Task...] (既定のビルド タスクの構成...)** の順に選択し、プロンプトから **[ビルド]** を選択することによって、このビルドを既定のビルド タスクにすることができます。

## <a name="connect-to-iot-hub-and-run-deviceharness"></a>IoT ハブに接続して DeviceHarness を実行する

これでプロジェクトがビルドされたので、IoT ハブに接続して接続文字列にアクセスし、データ生成の進行状況を監視します。

### <a name="sign-in-to-azure-in-visual-studio-code"></a>Visual Studio Code で Azure にサインインする

1. Visual Studio Code でコマンド パレット (`Ctrl + Shift + P` または **[表示]**  >  **[Command Palette...] (コマンド パレット...)** ) を開くことによって、Azure サブスクリプションにサインインします。

1. プロンプトで、 **[Azure: サインイン]** を検索して選択します。

1. ブラウザー ウィンドウが開き、資格情報を入力するよう求められます。 成功ページにリダイレクトされたら、ブラウザーを閉じることができます。

### <a name="connect-to-your-iot-hub-and-retrieve-hub-connection-string"></a>IoT ハブに接続してハブ接続文字列を取得する

1. Visual Studio Code エクスプローラーの一番下のセクションで、 **[Azure IoT Hub デバイス]** フレームを選択して展開します。

1. 展開されたフレームで、 **[IoT Hub の選択]** をクリックします。

1. メッセージが表示されたら、Azure サブスクリプション、IoT ハブの順に選択します。

1. **[Azure IoT Hub デバイス]** フレームをクリックし、 **[...]** をクリックして他のアクションを表示します。 **[Copy IoT Hub connection string] (IoT Hub 接続文字列のコピー)** を選択します。

   ![[Copy IoT Hub connection string] (IoT Hub 接続文字列のコピー)](media/tutorial-machine-learning-edge-03-generate-data/copy-hub-connection-string.png)

### <a name="run-the-deviceharness-project"></a>DeviceHarness プロジェクトを実行する

1. **[表示]**  >  **[ターミナル]** の順に選択して Visual Studio Code ターミナルを開きます。

   プロンプトが表示されない場合は、Enter キーを押します。

1. ターミナルで「`dotnet run`」と入力します。

1. IoT Hub 接続文字列の入力を求められたら、前のセクションでコピーした接続文字列を貼り付けます。

1. **[Azure IoT Hub デバイス]** フレームで、更新ボタンをクリックします。

   ![IoT Hub デバイス一覧を更新する](media/tutorial-machine-learning-edge-03-generate-data/refresh-hub-device-list.png)

1. デバイスが IoT Hub に追加されていること、およびデバイスが緑色で表示される場合はそのデバイス経由でデータが送信されていることを示すことに注意してください。

1. いずれかのデバイスを右クリックし、 **[組み込みイベント エンドポイントの監視を開始する]** を選択することによって、ハブに送信されているメッセージを表示できます。 これらのメッセージは、Visual Studio Code の出力ウィンドウに表示されます。

1. 監視を停止するには、**Azure IoT Hub Toolkit** の出力ウィンドウをクリックし、 **[Stop Monitoring Built-in Event Endpoint] (組み込みイベント エンドポイントの監視を停止する)** を選択します。

1. アプリケーションを完了するまで実行します。これには数分かかります。

## <a name="check-iot-hub-for-activity"></a>IoT ハブのアクティビティを確認する

DeviceHarness によって送信されたデータは IoT ハブに転送されました。 そのデータがハブに到達したことは、Azure portal を使用して容易に確認できます。

1. [Azure portal](https://portal.azure.com/) を開き、IoT ハブに移動します。

1. 概要ページに、そのデータがハブに送信されたことが表示されます。  

   ![IoT ハブでデバイスからクラウドへのメッセージを表示する](media/tutorial-machine-learning-edge-03-generate-data/iot-hub-usage.png)

## <a name="validate-data-in-azure-storage"></a>Azure Storage 内のデータを検証する

先に IoT ハブに送信したデータは、前の記事で作成したストレージ コンテナーにルーティングされました。 ストレージ アカウント内のデータを見てみましょう。

1. Azure Portal のストレージ アカウントに移動します。

1. ストレージ アカウント ナビゲーターから、 **[Storage Explorer (preview)] (ストレージ エクスプローラー (プレビュー))** を選択します。

1. ストレージ エクスプローラーで、 **[BLOB コンテナー]** 、 **[devicedata]** の順に選択します。

1. コンテンツ ウィンドウで、フォルダーの IoT ハブの名前、年、月、日、時間の順にクリックします。 データが書き込まれた分を表すいくつかのフォルダーが表示されます。

   ![BLOB ストレージ内のフォルダーを表示する](media/tutorial-machine-learning-edge-03-generate-data/confirm-data-storage-results.png)

1. これらのフォルダーのうちの 1 つをクリックすると、パーティションに対応する **00** や **01** というラベルが付いたデータ ファイルが見つかります。

1. ファイルは [Avro](https://avro.apache.org/) 形式で書き込まれますが、これらのファイルのうちの 1 つをダブルクリックすると、別のブラウザー タブが開き、データが部分的に表示されます。 代わりにプログラムでファイルを開くよう求められた場合は、VS Code を選択できます。それにより、データが正しく表示されます。

1. 現時点ではデータを読み取ったり、解釈したりしようとする必要はありません。それは、次の記事で行う予定です。

## <a name="next-steps"></a>次の手順

この記事では、.NET Core プロジェクトを使用して一連の仮想デバイスを作成し、これらのデバイス経由で IoT Hub を通して Azure Storage コンテナーにデータを送信しました。 このプロジェクトは、物理デバイスが、センサー読み取り、動作設定、障害のシグナルやモードなどを含むデータを IoT Hub に、さらには選別されたストレージに送信する実際のシナリオをシミュレートします。 十分なデータが収集されたら、それを使用して、デバイスの残存耐用年数 (RUL) を予測するモデルをトレーニングします。それは、次の記事で示す予定です。

このデータを使用して機械学習モデルをトレーニングするには、次の記事に進んでください。

> [!div class="nextstepaction"]
> [Azure Machine Learning モデルをトレーニングしてデプロイする](tutorial-machine-learning-edge-04-train-model.md)
