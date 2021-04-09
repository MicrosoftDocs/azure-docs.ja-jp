---
title: Azure Functions を使用して Twitter メッセージを取得する | Microsoft Docs
description: モーション センサーを使用して振動を検出し、Azure Functions を使用して指定したハッシュタグ付きのランダム ツイートを検索します。
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/07/2018
ms.author: liydu
ms.custom: devx-track-csharp
ms.openlocfilehash: af1685f6455c0642800cba7dd604fcc836bcd7a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92147897"
---
# <a name="shake-shake-for-a-tweet----retrieve-a-twitter-message-with-azure-functions"></a>シェイク、シェイクしてツイート -- Azure Functions を使用して Twitter メッセージを取得する

このプロジェクトでは、Azure Functions を使用してイベントをトリガーするモーション センサーの使用方法を学習します。 このアプリは、Arduino スケッチで構成する # (ハッシュタグ) を使用して、ランダムなツイートを取得します。 ツイートは、DevKit 画面に表示されます。

## <a name="what-you-need"></a>必要なもの

[ファースト ステップ ガイド](./iot-hub-arduino-iot-devkit-az3166-get-started.md)に従って以下のことを行います。

* DevKit を Wi-Fi に接続する。
* 開発環境を準備する。

有効な Azure サブスクリプション ない場合は、次のいずれかの方法を使用して登録できます。

* [30 日間の無料試用版 Microsoft Azure アカウント](https://azure.microsoft.com/free/)をアクティブにする
* MSDN または Visual Studio サブスクライバーの場合は、[Azure クレジット](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を要求する

## <a name="open-the-project-folder"></a>プロジェクト フォルダーを開く

プロジェクト フォルダーを開くことから開始します。 

### <a name="start-vs-code"></a>VS Code の起動

* DevKit がお使いのコンピューターに接続されていることを確認します。

* VS Code を起動します。

* DevKit をコンピューターに接続します。

   > [!NOTE]
   > VS Code を起動するときに、Arduino IDE または関連するボード パッケージが見つからないというエラー メッセージが表示される場合があります。 このエラーが発生した場合は、VS Code を閉じて、Arduino IDE をもう一度起動します。 VS Code によって、今度は Arduino IDE のパスが正しく検索されます。

### <a name="open-the-arduino-examples-folder"></a>[Arduino Examples]\(Arduino の例\) フォルダーを開く

左側の **[Arduino Examples]\(Arduino の例\)** セクションを展開し、 **[Examples for MXCHIP AZ3166] > [AzureIoT]** を参照して、 **[ShakeShake]** を選択します。 プロジェクト フォルダーを表示する、新しい VS Code ウィンドウが開きます。 MXCHIP AZ3166 セクションが表示されない場合は、デバイスが正しく接続されていることを確認し、Visual Studio Code を再起動します。  
![mini-solution-examples](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/vscode_examples.png)

コマンド パレットから同じプロジェクトを開くこともできます。 `Ctrl+Shift+P` キー (macOS: `Cmd+Shift+P` キー) を使用してコマンド パレットを開き、「**Arduino**」と入力します。次に、 **[Arduino:Examples]\(Arduino: 例\)** を見つけて選択します。

## <a name="provision-azure-services"></a>Azure サービスのプロビジョニング

ソリューション ウィンドウで、「`task cloud-provision`」と入力し、`Ctrl+P` キー (macOS: `Cmd+P` キー) を使用してタスクを実行します。

VS Code ターミナルでは、対話型コマンド ラインを使用して、必要な Azure サービスをプロビジョニングできます。

![Visual Studio Code ターミナルの対話型コマンド ラインを示すスクリーンショット。](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-provision.png)

> [!NOTE]
> Azure にサインインしようとしているときに、読み込み状態で、ページがハングした場合は、[IoT DevKit の FAQ にある "ログイン ページのハング" の手順](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure)を参照してください。
 
## <a name="modify-the-hashtag"></a># (ハッシュタグ) の変更

`ShakeShake.ino` を開き、次のコード行を探します。

```cpp
static const char* iot_event = "{\"topic\":\"iot\"}";
```

中かっこ内の文字列 `iot` を、お好みのハッシュタグに置き換えます。 その後、DevKit によって、この手順で指定したハッシュタグを含むランダム ツイートが取得されます。

## <a name="deploy-azure-functions"></a>Azure Functions のデプロイ

`Ctrl+P` (macOS: `Cmd+P`) を使用して `task cloud-deploy` を実行し、Azure Functions コードのデプロイを開始します。

![task cloud-deploy を実行して Azure Functions コードをデプロイできる Visual Studio Code を示すスクリーンショット。](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-deploy.png)

> [!NOTE]
> 場合によっては、Azure Functions が正しく動作しない可能性があります。 この問題が発生したときに解決するには、[IoT DevKit の FAQ の "コンパイル エラー" に関するセクション](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#compilation-error-for-azure-function)を確認してください。

## <a name="build-and-upload-the-device-code"></a>デバイス コードのビルドとアップロード

次に、デバイス コードをビルドしてアップロードします。

### <a name="windows"></a>Windows

1. `Ctrl+P` キーを使用して `task device-upload` を実行します。

2. ターミナルによって、構成モードを開始するよう求められます。 そのためには次を行います。

   * A ボタンを押しながら

   * リセット ボタンを押して離します。

3. 画面に、DevKit の ID と "構成" が表示されます。

### <a name="macos"></a>macOS

1. DevKit を構成モードに配置します。

   A ボタンを押しながら、リセット ボタンを押して離します。 画面に "構成" が表示されます。

2. `Cmd+P` を使用して `task device-upload` を実行し、`task cloud-provision` ステップから取得した接続文字列を設定します。

### <a name="verify-upload-and-run"></a>確認、アップロード、および実行

これで接続文字列が設定され、これによってアプリが確認され、アップロードおよび実行されます。 

1. VS Code で Arduino スケッチの確認が開始され、DevKit にアップロードされます。

   ![Arduino スケッチを確認してアップロードする Visual Studio Code を示すスクリーンショット。](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)

2. DevKit が再起動され、コードの実行が開始されます。

"エラー: AZ3166:不明なパッケージ" というエラー メッセージが表示される場合があります。 このエラーは、ボード パッケージ インデックスが正しく更新されない場合に発生します。 この問題を解決するには、[IoT DevKit の FAQ の "不明なパッケージ" エラー](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development)を確認してください。

## <a name="test-the-project"></a>プロジェクトのテスト

アプリの初期化後に、リリース ボタン A をクリックしてから、DevKit ボードをそっとシェイクします。 このアクションにより、前に指定したハッシュタグを含むランダム ツイートが取得されます。 数秒で、DevKit 画面にツイートが表示されます。

### <a name="arduino-application-initializing"></a>Arduino アプリケーションの初期化中...

![Arduino-application-initializing](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-1.png)

### <a name="press-a-to-shake"></a>A を押してシェイクする...

![Press-A-to-shake](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-2.png)

### <a name="ready-to-shake"></a>シェイクする準備が完了...

![Ready-to-shake](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-3.png)

### <a name="processing"></a>処理中...

![処理中](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-4.png)

### <a name="press-b-to-read"></a>B を押して読み取り...

![Press-B-to-read](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-5.png)

### <a name="display-a-random-tweet"></a>ランダム ツイートを表示...

![Display-a-random-tweet](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-6.png)

- A ボタンをもう一度押してから、新しいツイートのためにシェイクします。
- ボタン B を押して、そのツイートの残りの部分をスクロールします。

## <a name="how-it-works"></a>しくみ

![Azure IoT Hub にイベントを送信するモバイル デバイスを示す図。このイベントが Azure Functions アプリをトリガーしてツイートを要求し、そのツイートがアプリに返送され、ハブに転送されて、モバイル デバイスに転送されます。](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/diagram.png)

Arduino スケッチは、Azure IoT Hub にイベントを送信します。 このイベントは、Azure Functions アプリをトリガーします。 Azure Functions アプリには、Twitter の API に接続して、ツイートを取得するためのロジックが含まれています。 そしてツイート テキストを C2D (cloud-to-device) メッセージにラップし、デバイスに返送します。

## <a name="optional-use-your-own-twitter-bearer-token"></a>省略可能:自分の Twitter ベアラー トークンを使用する

テスト目的のため、このサンプル プロジェクトでは、事前に構成されている Twitter ベアラー トークンを使用しています。 しかし、すべての Twitter アカウントには[レート制限](https://dev.twitter.com/rest/reference/get/search/tweets)があります。 独自のトークンの使用を検討する場合は、次の手順に従います。

1. [Twitter の開発者ポータル](https://dev.twitter.com/)に移動して、新しい Twitter アプリを登録します。

2. ご自分のアプリの[コンシューマー キーとコンシューマー シークレットを取得](https://support.yapsody.com/hc/en-us/articles/360003291573-How-do-I-get-a-Twitter-Consumer-Key-and-Consumer-Secret-key-)します。

3. [何らかのユーティリティ](https://gearside.com/nebula/utilities/twitter-bearer-token-generator/)を使用して、これら 2 つのキーから Twitter ベアラー トークンを生成します。

4. [Azure portal](https://portal.azure.com/){:target="_blank"} で、 **[リソース グループ]** に移動して、"Shake, Shake" プロジェクトの Azure Functions (タイプ: App Service) を見つけます。 名前には、常に 'shake...' 文字列が含まれます。

   ![プロジェクトのアプリ サービスを示す Azure portal のスクリーンショット。](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function.png)

5. 独自のトークンを使用して、 **[関数] > [shakeshake-cs]** 内で `run.csx` のコードを更新します。

   ```csharp
   string authHeader = "Bearer " + "[your own token]";
   ```
  
   ![トークンを入力できる、関数の C# コードを示すスクリーンショット。](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/twitter-token.png)

6. ファイルを保存して **[実行]** をクリックします。

## <a name="problems-and-feedback"></a>問題とフィードバック

問題をトラブルシューティングする方法、またはフィードバックを提供する方法。 

### <a name="problems"></a>問題が発生した場合

すべてのステップは正常に実行されているのに、画面に 'No Tweets' (ツイートなし) と表示される場合に発生する問題。 この状況は通常、サンプルを初めてデプロイして実行したときに発生します。これは、関数アプリでアプリをコールド スタートするには、数秒から 1 分程度必要なためです。 

または、コードを実行するときに、アプリの再起動が発生するいくつかの停止があります。 このような状況が発生すると、デバイス アプリがツイートをフェッチするためにタイムアウトになる場合があります。 この場合、この問題を解決するために、次のいずれかまたは両方の方法を試すことができます。

1. DevKit でリセット ボタンをクリックして、デバイス アプリをもう一度実行します。

2. [Azure portal](https://portal.azure.com/) で、作成した Azure Functions アプリを検索して再起動します。

   ![Azure Functions アプリと [再起動] ボタンが表示されている Azure portal を示すスクリーンショット。](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function-restart.png)

その他の問題が発生した場合は、[IoT DevKit の FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) に関するページを参照するか、以下のチャネルを使用して Microsoft までお問い合わせください。

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>次のステップ

ここでは、DevKit デバイスを Azure IoT リモート監視ソリューション アクセラレータに接続して、ツイートを取得する方法を説明しました。推奨する次の手順は、以下のとおりです。

* [Azure IoT リモート監視ソリューション アクセラレータの概要](/azure/iot-suite/)