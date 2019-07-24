---
title: Azure Functions と Cognitive Services を使用した IoT DevKit 翻訳ツールの作成 | Microsoft Docs
description: IoT DevKit のマイクを使用して音声メッセージを受信し、そのメッセージを英語のテキストに翻訳するために Azure Cognitive Services を使用します
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 12/19/2018
ms.author: liydu
ms.openlocfilehash: df7e7b426a8c85c8051d7f588c706a6f8811e183
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58085747"
---
# <a name="use-iot-devkit-az3166-with-azure-functions-and-cognitive-services-to-make-a-language-translator"></a>IoT DevKit AZ3166 と Azure Functions および Cognitive Services を使用して言語翻訳ツールを作成する

この記事では、[Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) を使用して、IoT DevKit をトランスレーターにする方法について説明します。 このトランスレーターでは、声を記録し、それを英語に翻訳して、DevKit の画面に表示します。

[MXChip IoT DevKit](https://aka.ms/iot-devkit) は、豊富な周辺機器とセンサーを備えたオールインワンの Arduino 互換ボードです。 Visual Studio Code で [Azure IoT Device Workbench](https://aka.ms/iot-workbench) または [Azure IoT Tools](https://aka.ms/azure-iot-tools) 拡張機能パックを使用して、これを開発することができます。 [プロジェクト カタログ](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)に、IoT ソリューションをプロトタイプ化するのに役立つサンプル アプリケーションが含まれています。

## <a name="before-you-begin"></a>開始する前に

このチュートリアルを完了するには、最初に次のタスクを行います。

* 「[IoT DevKit AZ3166 をクラウドの Azure IoT Hub に接続する](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)」の手順に従って、DevKit を準備します。

## <a name="create-azure-cognitive-service"></a>Azure Cognitive Service を作成する

1. Azure ポータルで、**[リソースの作成]** をクリックして、**Speech** を検索します。 フォームに入力して Speech サービスを作成します。
  ![Speech サービス](media/iot-hub-arduino-iot-devkit-az3166-translator/speech-service.png)

1. 先ほど作成した Speech サービスに移動して、**[キー]** セクションをクリックし、それにアクセスしている DevKit 用の **Key1** をコピーしてメモします。
  ![キーをコピーする](media/iot-hub-arduino-iot-devkit-az3166-translator/copy-keys.png)

## <a name="open-sample-project"></a>サンプル プロジェクトを開く

1. IoT DevKit がお使いのコンピューターに接続されて**いない**ことを確認します。 まず VS Code を起動し、DevKit をコンピューターに接続します。

1. `F1` をクリックしてコマンド パレットを開き、**[Azure IoT Device Workbench:Open Examples...]\(Azure IoT Device Workbench: 例を開く...\)** を入力して選択します。次に、**[IoT DevKit]** をボードとして選択します。

1. [IoT Workbench Examples]\(IoT Workbench の例\) ページで **[DevKit Translator]\(DevKit トランスレーター\)** を見つけて **[Open Sample]\(サンプルを開く\)** をクリックします。 次に、サンプル コードをダウンロードするための既定のパスを選択します。
  ![サンプルを開く](media/iot-hub-arduino-iot-devkit-az3166-translator/open-sample.png)

## <a name="use-speech-service-with-azure-functions"></a>Azure Functions で Speech サービスを使用する

1. VS Code で、`F1` をクリックし、**[Azure IoT Device Workbench: Azure サービスのプロビジョニング...]** を入力して選択します。![Azure サービスをプロビジョニングする](media/iot-hub-arduino-iot-devkit-az3166-translator/provision.png)

1. 手順に従って、Azure IoT Hub と Azure Functions のプロビジョニングを完了します。
   ![プロビジョニング手順](media/iot-hub-arduino-iot-devkit-az3166-translator/provision-steps.png)

   作成した Azure IoT Hub のデバイス名をメモします。

1. `Functions\DevKitTranslatorFunction.cs` を開き、メモしたデバイス名と音声サービスのキーで次のコード行を更新します。
   ```csharp
   // Subscription Key of Speech Service
   const string speechSubscriptionKey = "";

   // Region of the speech service, see https://docs.microsoft.com/azure/cognitive-services/speech-service/regions for more details.
   const string speechServiceRegion = "";

   // Device ID
   const string deviceName = "";
   ```

1. `F1` をクリックし、**[Azure IoT Device Workbench:Azure へのデプロイ...]** を入力して選択します。VS Code で再デプロイの確認が求められたら、**[はい]** をクリックします。
   ![デプロイの警告](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-warning.png)

1. デプロイが成功したことを確認します。
   ![デプロイの成功](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-success.png)

1. Azure ポータルで、**[Functions アプリ]** セクションに移動し、作成された Azure 関数アプリを見つけます。 `devkit_translator` をクリックします。次に、**[</> 関数の URL の取得]** をクリックして URL をコピーします。
   ![関数の URL をコピーする](media/iot-hub-arduino-iot-devkit-az3166-translator/get-function-url.png)

1. URL を `azure_config.h` ファイルに貼り付けます。
   ![Azure の構成](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-config.png)

   > [!NOTE]
   > 関数アプリが正しく動作しない場合は、こちらの [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function) に関するセクションを確認して問題を解決してください。

## <a name="build-and-upload-device-code"></a>デバイス コードをビルドしてアップロードする

1. 次のようにして、DevKit を**構成モード**に切り替えます。
   * **A** ボタンを押しながら、
   * **[リセット]** ボタンを押して離します。

   画面に、DevKit の ID と**構成**が表示されます。

   ![DevKit 構成モード](media/iot-hub-arduino-iot-devkit-az3166-translator/devkit-configuration-mode.png)

1. `F1` をクリックし、**[Azure IoT Device Workbench: デバイス設定の構成...] > [Config Device Connection String]\(デバイス接続文字列の構成\)** を入力して選択します。 **[Select IoT Hub Device Connection String]\(デバイス接続文字列の選択\)** を選び、DevKit に構成します。
   ![接続文字列を構成する](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string.png)

1. 成功すると、通知が表示されます。
   ![接続文字列の構成の成功](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string-success.png)

1. `F1` をもう一度クリックし、**[Azure IoT Device Workbench: Upload Device Code]\(デバイス コードのアップロード\)** を入力して選択します。 これにより、コンパイルと DevKit へのコードのアップロードが開始されます。
   ![デバイスのアップロード](media/iot-hub-arduino-iot-devkit-az3166-translator/device-upload.png)

## <a name="test-the-project"></a>プロジェクトのテスト

アプリの初期化が終わったら、DevKit の画面の指示に従います。 既定のソース言語は中国語です。

別の言語を選択するには、次の手順に従います。

1. A ボタン を押してセットアップ モードにします。

2. B ボタンを押すと、サポートされているすべてのソース言語をスクロールできます。

3. A ボタンを押して、ソース言語の選択を確定します。

4. B ボタンを押しながら話した後、B ボタンを放すと、翻訳が開始されます。

5. 翻訳されたテキスト (英語) が画面に表示されます。

![スクロールして言語を選択します](media/iot-hub-arduino-iot-devkit-az3166-translator/select-language.jpg)

![翻訳結果](media/iot-hub-arduino-iot-devkit-az3166-translator/translation-result.jpg)

翻訳結果画面では、次の操作を実行できます。

- A ボタンを押し、B ボタンを押してスクロールし、ソース言語を選択します。

- B ボタンを押して話します。 音声を送信し、翻訳テキストを表示するには、B ボタンを放します。

## <a name="how-it-works"></a>動作のしくみ

![mini-solution-voice-to-tweet-diagram](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

IoT DevKit では音声を記録し、HTTP 要求をポストして Azure Functions をトリガーします。 Azure Functions は、 Cognitive Services の音声翻訳 API を呼び出して翻訳を実行します。 Azure Functions は翻訳テキストを取得すると、デバイスに C2D メッセージを送信します。 その後、画面に翻訳テキストが表示されます。

## <a name="problems-and-feedback"></a>問題とフィードバック

問題が発生した場合は、[IoT DevKit の FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) に関するページを参照するか、以下のチャネルを使用して Microsoft までお問い合わせください。

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>次の手順

Azure Functions と Cognitive Services を使用して IoT DevKit を翻訳ツールとして使用する方法について学習しました。 この使い方で学習した内容は次のとおりです。

> [!div class="checklist"]
> * Visual Studio Code タスクを使用してクラウド プロビジョニングを自動化する
> * Azure IoT デバイス接続文字列を構成する
> * Azure 関数のデプロイ
> * 音声メッセージ翻訳をテストする

他のチュートリアルに進んで、次の方法を学習してください。

> [!div class="nextstepaction"]
> [IoT DevKit AZ3166 を Azure IoT リモート監視ソリューション アクセラレータに接続する](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring)
