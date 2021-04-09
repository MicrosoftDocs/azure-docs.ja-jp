---
title: Azure Functions を使用してドアが開いたときに電子メールを送信する
description: 磁気センサーを監視してドアが開いたことを検出し、Azure Functions を使用して電子メール通知を送信します。
author: liydu
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/19/2018
ms.author: liydu
ms.openlocfilehash: e3649ac786c07459695a1e6ca6ad52620e23d59f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92148797"
---
# <a name="door-monitor----using-azure-functions-and-sendgrid-send-email-when-a-door-is-opened"></a>ドア モニター -- Azure Functions と SendGrid を使用して、ドアが開いたときに電子メールを送信する           

MXChip IoT DevKit には、磁気センサーが組み込まれています。 このプロジェクトでは、近くの強力な磁場の有無を検出します。ここでは、小型の永久磁石から発生する磁場を検出します。

## <a name="what-you-learn"></a>学習内容

このプロジェクトでは、以下を学習します。
- MXChip IoT DevKit の磁気センサーを使用して、近くの磁石の動きを検出する方法。
- SendGrid サービスを使用して、電子メール アドレスに通知を送信する方法。

> [!NOTE]
> このプロジェクトを実際に利用するには、次のタスクを実行します。
> - ドアの端に磁石を取り付けます。
> - 磁石の近くのドア枠に DevKit を取り付けます。 ドアが開くか閉じるとセンサーが起動し、そのイベントの発生を示す電子メール通知が送信されます。

## <a name="what-you-need"></a>必要なもの

[ファースト ステップ ガイド](iot-hub-arduino-iot-devkit-az3166-get-started.md)に従って以下のことを行います。

* DevKit をWi-Fi に接続
* 開発環境の準備

有効な Azure サブスクリプション 持っていない場合は、次のいずれかの方法で登録できます。

* [30 日間の無料試用版 Microsoft Azure アカウント](https://azure.microsoft.com/free/)をアクティブにします。
* MSDN または Visual Studio サブスクライバーの場合は、[Azure クレジット](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を要求します。

## <a name="deploy-the-sendgrid-service-in-azure"></a>Azure に SendGrid サービスをデプロイする

[SendGrid](https://sendgrid.com/) は、クラウド ベースの電子メール配信プラットフォームです。 電子メール通知は、このサービスを使用して送信されます。

> [!NOTE]
> SendGrid サービスを既にデプロイしている場合は、「[IoT Hub を Azure にデプロイする](#deploy-iot-hub-in-azure)」にすぐに進むことができます。

### <a name="sendgrid-deployment"></a>SendGrid のデプロイ

Azure サービスをプロビジョニングするには、 **[Azure へのデプロイ]** ボタンをクリックします。 このボタンを使用すると、オープン ソース プロジェクトを Microsoft Azure に短時間で簡単にデプロイできます。

下の **[Azure へのデプロイ]** ボタンをクリックします。 

[![Azure へのデプロイ](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2FSendGridDeploy%2Fazuredeploy.json)

まだ Azure アカウントにサインインしていない場合は、ここでサインインします。 

SendGrid のサインアップ フォームが表示されます。

![SendGrid のデプロイ](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-deploy.png)

サインアップ フォームに入力します。

   * **[リソース グループ]** :SendGrid サービスをホストするリソース グループを作成するか、既存のリソース グループを使用します。 [リソース グループを使用した Azure リソースの管理](../azure-resource-manager/management/manage-resource-groups-portal.md)に関する記事をご覧ください。

   * **Name**:SendGrid サービスの名前。 他のサービスとは異なる一意の名前を選択します。

   * **パスワード**:このサービスにはパスワードが必要です。このプロジェクト内で他に使用されることはありません。

   * **電子メール**: SendGrid サービスは、この電子メール アドレスに確認を送信します。

このアプリケーションを後で見つけやすくするために、 **[ダッシュボードにピン留めする]** オプションをオンにし、 **[購入]** をクリックしてサインイン フォームを送信します。
 
### <a name="sendgrid-api-key-creation"></a>SendGrid API キーの作成

デプロイが完了したら、それをクリックし、 **[管理]** ボタンをクリックします。 SendGrid アカウント ページが表示されます。ここで、電子メール アドレスを確認する必要があります。

![SendGrid の管理](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-manage.png)

SendGrid ページで、 **[設定]**  >  **[API キー]**  >  **[API キーの作成]** をクリックします。

![SendGrid: API の作成 (その 1)](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-first.png)

**[API キーの作成]** ページで、 **[API キー名]** を入力し、 **[作成と表示]** をクリックします。

![SendGrid: API の作成 (その 2)](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-second.png)

API キーは 1 回だけ表示されます。 次の手順で使用するため、コピーして安全に保存してください。

## <a name="deploy-iot-hub-in-azure"></a>IoT Hub を Azure にデプロイする

次の手順で、その他の Azure IoT 関連サービスをプロビジョニングし、このプロジェクト用の Azure Functions をデプロイします。

下の **[Azure へのデプロイ]** ボタンをクリックします。 

[![Azure へのデプロイ](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2Fazuredeploy.json)

サインアップ フォームが表示されます。

![Iot Hub のデプロイ](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/iot-hub-deploy.png)

サインアップ フォームの各フィールドに情報を入力します。

   * **[リソース グループ]** :SendGrid サービスをホストするリソース グループを作成するか、既存のリソース グループを使用します。 [リソース グループを使用した Azure リソースの管理](../azure-resource-manager/management/manage-resource-groups-portal.md)に関する記事をご覧ください。

   * **Iot Hub 名**:IoT Hub の名前。 他のサービスとは異なる一意の名前を選択します。

   * **Iot Hub SKU**:F1 は無料です (サブスクリプションあたり 1 つに制限されています)。 詳しい料金情報については、[価格に関するページ](https://azure.microsoft.com/pricing/details/iot-hub/)をご覧ください。

   * **送信者アドレス**:このフィールドは、SendGrid サービスを設定するときに使用したのと同じ電子メール アドレスにする必要があります。

このアプリケーションを後で見つけやすくするために、 **[ダッシュボードにピン留めする]** オプションをオンにし、次の手順に進む準備ができたら、 **[購入]** をクリックします。
 
## <a name="build-and-upload-the-code"></a>コードをビルドしてアップロードする

次に、VS Code でサンプル コードを読み込み、必要な Azure サービスをプロビジョニングします。

### <a name="start-vs-code"></a>VS Code の起動

- DevKit がお使いのコンピューターに接続されて **いない** ことを確認します。
- VS Code を起動します。
- DevKit をコンピューターに接続します。

> [!NOTE]
> VS Code の起動時に、Arduino IDE または関連するボード パッケージが見つからないというエラー メッセージが表示される場合があります。 このエラーが表示された場合は、VS Code を閉じてから Arduino IDE を再度起動します。これにより、VS Code が Arduino IDE パスを正しく見つけることができます。

### <a name="open-arduino-examples-folder"></a>[Arduino Examples]\(Arduino の例\) フォルダーを開く

左側の **[ARDUINO EXAMPLES]** セクションを展開し、 **[Examples for MXCHIP AZ3166] > [AzureIoT]** を参照し、 **[DoorMonitor]** を選択します。 このアクションによって、プロジェクト フォルダーを含む新しい VS Code ウィンドウが開きます。

![mini-solution-examples](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-examples.png)

サンプル アプリは、コマンド パレットから開くこともできます。 `Ctrl+Shift+P` キー (macOS: `Cmd+Shift+P` キー) を使用してコマンド パレットを開き、「**Arduino**」と入力します。次に、 **[Arduino:Examples]\(Arduino: 例\)** を見つけて選択します。

### <a name="provision-azure-services"></a>Azure サービスのプロビジョニング

ソリューション ウィンドウで、クラウド プロビジョニング タスクを実行します。
- `Ctrl+P` を押します (macOS の場合: `Cmd+P`)。
- 表示されたテキスト ボックスに `task cloud-provision` と入力します。

VS Code ターミナルでは、対話型コマンド ラインを使用して、必要な Azure サービスをプロビジョニングできます。 「[IoT Hub を Azure にデプロイする](#deploy-iot-hub-in-azure)」でプロビジョニングしたとの同じ項目を一覧からすべて選択します。

![クラウド プロビジョニング](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/cloud-provision.png)

> [!NOTE]
> Azure にサインインしようとしているときに、読み込み状態でページがハングした場合は、[IoT DevKit FAQ の「Page hangs when log in Azure (Azure へのログイン時にページがハングする)」](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure)を参照して問題を解決してください。 

### <a name="build-and-upload-the-device-code"></a>デバイス コードのビルドとアップロード

次に、デバイス用のコードをアップロードします。

#### <a name="windows"></a>Windows

1. `Ctrl+P` キーを使用して `task device-upload` を実行します。

2. ターミナルによって、構成モードを開始するよう求められます。 これを行うには、A ボタンを押しながら、リセット ボタンを押して離します。 DevKit の識別番号と、"*構成*" という単語が画面に表示されます。

#### <a name="macos"></a>macOS

1. DevKit を構成モードに配置します。A ボタンを押しながら、リセット ボタンを押して離します。 画面に "構成" が表示されます。

2. `Cmd+P` をクリックして `task device-upload` を実行します。

#### <a name="verify-upload-and-run-the-sample-app"></a>サンプル アプリの検証、アップロード、実行

「[Azure サービスのプロビジョニング](#provision-azure-services)」で取得した接続文字列が設定されると、 

VS Code によって、Arduino スケッチの検証と DevKit へのアップロードが開始されます。

![Arduino スケッチを確認してアップロードする Visual Studio Code を示すスクリーンショット。](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

DevKit が再起動され、コードの実行が開始されます。

> [!NOTE]
> "エラー:AZ3166:不明なパッケージ" というエラー メッセージが表示される場合があります。 このエラーは、ボード パッケージ インデックスが正しく更新されない場合に発生します。 このエラーを解決するには、[IoT DevKit FAQ の「Development (開発)」セクション](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development)を参照してください。

## <a name="test-the-project"></a>プロジェクトのテスト

プログラムは、DevKit が安定した磁場の中にあるときに、まず初期化を実行します。

初期化が終わると、画面に `Door closed` が表示されます。 磁場が変化すると、状態が `Door opened` に変わります。 ドアの状態が変化するたびに、電子メール通知を受信します (これらの電子メール メッセージが受信されるまで、最大 5 分かかる場合があります)。

![磁石がセンサーの近くにあるとき:Door Closed](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-closed.jpg "磁石がセンサーの近くにあるとき: Door Closed")

![磁石がセンサーから離れたとき:Door Opened](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-opened.jpg "磁石がセンサーから離れたとき: Door Opened")

## <a name="problems-and-feedback"></a>問題とフィードバック

問題が発生した場合は、[IoT DevKit の FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) に関するページを参照するか、以下のチャネルを使用してお問い合わせください。

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>次のステップ

ここでは、DevKit デバイスを Azure IoT リモート監視ソリューション アクセラレータに接続し、SendGrid サービスを使用して電子メールを送信する方法について学習しました。 推奨される次のステップは以下のとおりです。[Azure IoT リモート監視ソリューション アクセラレータの概要](/azure/iot-suite/)