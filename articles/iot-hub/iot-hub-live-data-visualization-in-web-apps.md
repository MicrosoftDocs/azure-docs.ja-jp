---
title: Web アプリでの IoT Hub データのリアルタイム データの視覚化
description: Web アプリを使用して、センサーから収集されて IoT Hub に送信された気温と湿度のデータを視覚化します。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
- 'Role: Data Analytics'
- devx-track-azurecli
ms.openlocfilehash: 53b5add7526b0c20487e8fe3adb0b8ebe207a2ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102200036"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>Web アプリで Azure IoT Hub からのリアルタイム センサー データを視覚化する

![エンド ツー エンド ダイアグラム](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>学習内容

このチュートリアルでは、お使いのローカル コンピューターで実行されている node.js Web アプリを使って、IoT ハブで受信されるリアルタイム センサー データを視覚化する方法について説明します。 Web アプリをローカルで実行した後は、必要に応じて、Azure App Service で Web アプリをホストする手順に従うことができます。 Power BI を使用して IoT ハブのデータを視覚化したい場合は、[Power BI を使用して Azure IoT Hub からのリアルタイム センサー データを視覚化する方法](iot-hub-live-data-visualization-in-power-bi.md)に関するページを参照してください。

## <a name="what-you-do"></a>作業内容

* Web アプリがセンサー データの読み取りに使用する IoT Hub にコンシューマー グループを追加する
* GitHub から Web アプリのコードをダウンロードする
* Web アプリのコードを調べる
* Web アプリに必要な IoT Hub の成果物を保持するために環境変数を構成する
* 開発マシンで Web アプリを実行する
* Web ページを開いて、IoT Hub からのリアルタイムの温度と湿度のデータを確認する
* (省略可能) Azure CLI を使用して Azure App Service で Web アプリをホストする

## <a name="what-you-need"></a>必要なもの

* [Raspberry Pi オンライン シミュレーター](iot-hub-raspberry-pi-web-simulator-get-started.md)のチュートリアルまたはいずれかのデバイス チュートリアル ([Node.js での Raspberry Pi](iot-hub-raspberry-pi-kit-node-get-started.md) に関するチュートリアルなど) が完了していること。 次の要件について取り上げられています。

  * 有効な Azure サブスクリプション
  * サブスクリプションの IoT Hub
  * IoT Hub にメッセージを送信するクライアント アプリケーション

* [Git のダウンロード](https://www.git-scm.com/downloads)

* この記事の手順では、Windows の開発マシンを想定しています。ただし、これらの手順は、Linux システムで好みのシェルを使って簡単に実行することができます。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="add-a-consumer-group-to-your-iot-hub"></a>IoT Hub へのコンシューマー グループの追加

[コンシューマー グループ](../event-hubs/event-hubs-features.md#event-consumers)は、イベント ストリームへの独立したビューを提供します。これにより、アプリと Azure サービスは、同じイベント ハブのエンドポイントからデータを別々に使用することができます。 このセクションでは、Web アプリがデータの読み取りに使用する IoT ハブの組み込みエンドポイントにコンシューマー グループを追加します。

IoT Hub の組み込みエンドポイントにコンシューマー グループを追加するには、次のコマンドを実行します。

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

選択した名前を書き留めておいてください。後でこのチュートリアルで必要になります。

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>IoT Hub のサービス接続文字列を取得する

IoT Hub は、いくつかの既定のアクセス ポリシーを使用して作成されます。 そのようなポリシーの 1 つとして **サービス** ポリシーがあります。これは、サービスが IoT Hub のエンドポイントを読み書きするのに十分なアクセス許可を提供します。 サービス ポリシーに準拠する IoT Hub の接続文字列を取得するには、次のコマンドを実行します。

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

接続文字列は次のようになります。

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

このサービス接続文字列を書き留めておいてください。後でこのチュートリアルで必要になります。

## <a name="download-the-web-app-from-github"></a>GitHub から Web アプリをダウンロードする

コマンド ウィンドウを開き、次のコマンドを入力して、サンプルを GitHub からダウンロードし、そのサンプルのディレクトリに移動します。

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>Web アプリのコードを調べる

web-apps-node-iot-hub-data-visualization ディレクトリから、お好みのエディターで Web アプリを開きます。 VS Code で表示されるファイル構造を以下に示します。

![Web アプリのファイル構造](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

少し時間を取って、次のファイルを調べます。

* **Server.js** は、Wb ソケットとイベント Hub のラッパー クラスを初期化するサービス側のスクリプトです。 これは、クラスが受信メッセージを Web ソケットにブロードキャストするために使用するイベント Hub ラッパー クラスへのコールバックを提供します。

* **Event-hub-reader.js** は、指定された接続文字列とコンシューマー グループを使用して IoT Hub の組み込みエンドポイントに接続するサービス側のスクリプトです。 これは、受信メッセージのメタデータから DeviceId と EnqueuedTimeUtc を抽出し、次に server.js によって登録されたコールバック メソッドを使用してメッセージをリレーします。

* **Chart-device-data.js** は、Web ソケットでリッスンし、各 DeviceId を追跡し、各デバイスの受信データの最後の 50 ポイントを格納するクライアント側のスクリプトです。 そしてそれは、選択したデバイス データをグラフ オブジェクトにバインドします。

* **Index.html** は、Web ページの UI レイアウトを処理し、クライアント側のロジックのために必要なスクリプトを参照します。

## <a name="configure-environment-variables-for-the-web-app"></a>Web アプリの環境変数を構成する

IoT Hub からデータを読み取るためには、Web アプリには、IoT Hub の接続文字列と、読み取りに使用するコンシューマー グループの名前が必要です。 これらの文字列は、server.js の次の行のプロセス環境から取得されます。

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

次のコマンドを使用して、コマンド ウィンドウで環境変数を設定します。 プレース ホルダーの値は、前に作成した、IoT Hub のサービス接続文字列とコンシューマー グループの名前に置き換えます。 文字列は引用符で囲まないでください。

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>Web アプリの実行

1. お使いのデバイスが実行中であり、データを送信していることを確認します。

2. コマンド ウィンドウで、次の行を実行して、参照されたパッケージをダウンロードしてインストールし、Web サイトを開始します。

   ```cmd
   npm install
   npm start
   ```

3. Web アプリが正常に IoT Hub に接続し、ポート 3000 でリッスンしていることを示す出力がコンソールに表示されます。

   ![コンソール上の開始済み Web アプリ](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>Web ページを開いて IoT Hub からのデータを表示する

`http://localhost:3000` を指定してブラウザーを開きます。

**[デバイスの選択]** 一覧からお使いのデバイスを選択し、デバイスによって IoT  Hub に送信された最新 50 個の温度と湿度を示すデータ ポイントの実行中のプロットを表示します。

![リアルタイムの温度と湿度を示す Web アプリのページ](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

Web アプリがブラウザー クライアントにブロードキャストしているメッセージを示す出力もコンソールに表示されるはずです。  

![コンソール上の Web アプリのブロードキャスト出力](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>App Service で Web アプリをホストする

[Azure App Service の Web Apps 機能](../app-service/overview.md) は、Web アプリをホストするためのサービスとしてのプラットフォーム (PAAS) を提供します。 Azure App Service でホストされている Web アプリは、継続的デプロイやパッケージ管理などの Azure とパートナーの DevOps ソリューションだけでなく、追加のセキュリティ、負荷分散、スケーラビリティなどの強力な Azure の機能の恩恵を受けることができます。 Azure App Service は、多くの一般的な言語で開発されて、Windows や Linux のインフラストラクチャにデプロイされた Web アプリをサポートします。

このセクションでは、App Service で Web アプリをプロビジョニングし、Azure CLI コマンドを使用してそれにコードをデプロイします。 [az webapp](/cli/azure/webapp) のドキュメントで、使用されているコマンドの詳細を確認できます。 始める前に、[IoT ハブにリソース グループを追加する](#add-a-consumer-group-to-your-iot-hub)、[IoT ハブのサービス接続文字列を取得する](#get-a-service-connection-string-for-your-iot-hub)、および [GitHub から Web アプリをダウンロードする](#download-the-web-app-from-github)の各手順を完了していることを確認してください。

1. [App Service プラン](../app-service/overview-hosting-plans.md)は、App Service でホストされているアプリを実行するための一連のコンピューティング リソースを定義します。 このチュートリアルでは、Developer/Free のレベルを使用して Web アプリをホストします。 Free レベルでは、Web アプリは、他のお客様のアプリを含めた他の App Service アプリとの共有 Windows リソースで実行されます。 Azure では、Web アプリを Linux コンピューティング リソースにデプロイする App Service プランも提供されています。 使用したい App Service プランが既にある場合は、この手順をスキップできます。

   Windows の Free レベルを使用して App Service プランを作成するには、次のコマンドを実行します。 IoT Hub が所属するものと同じリソース グループを使用します。 サービス プラン名には、大文字と小文字、数字、ハイフンを含めることができます。

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. 次に、App Service プランで Web アプリをプロビジョニングします。 `--deployment-local-git` パラメーターにより、お使いのローカル マシンで Git リポジトリから Web アプリのコードがアップロードされてデプロイされます。 Web アプリ名はグローバルに一意である必要があり、大文字と小文字の英字、数字、およびハイフンを使用できます。 使用している Node.js ランタイムのバージョンに応じて、`--runtime` パラメーターにノード バージョン 10.6 以降を指定してください。 `az webapp list-runtimes` コマンドを使用して、サポートされているランタイムの一覧を取得できます。

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --runtime "node|10.6" --deployment-local-git
   ```

3. 次に、IoT Hub 接続文字列とイベント Hub コンシューマー グループを指定する環境変数のアプリケーション設定を追加します。 `-settings` パラメーターで、個々の設定はスペースで区切られています。 このチュートリアルで前に作成した、IoT Hub のサービス接続文字列とコンシューマー グループを使用します。 値は引用符で囲まないでください。

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString="<your IoT hub connection string>"
   ```

4. Web アプリに対して Web ソケット プロトコルを有効にし、Web アプリが HTTPS 要求のみを受信する (HTTP 要求は HTTPS にリダイレクトされる) ように設定します。

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. コードを App Service にデプロイするには、[ユーザー レベルのデプロイ資格情報](../app-service/deploy-configure-credentials.md)を使用します。 ユーザー レベルのデプロイ資格情報は、Azure 資格情報とは異なり、Web アプリへの Git のローカルおよび FTP デプロイに使用されます。 それらは一度設定すると、お使いの Azure アカウント内のすべてのサブスクリプションのすべての App Service アプリで有効になります。 以前にユーザー レベルのデプロイ資格情報を設定している場合は、それらを使用できます。

   ユーザー レベルのデプロイ資格情報を以前に設定していない場合やパスワードを思い出せない場合は、次のコマンドを実行してください。 デプロイ ユーザー名は、Azure 内で一意である必要があり、ローカル Git プッシュに対して '\@' シンボルを含めることはできません。 プロンプトが表示されたら、新しいパスワードを入力して確認します。 パスワードは長さが 8 文字以上で、文字、数字、記号のうち 2 つを含む必要があります。

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. コードを App Service にプッシュするために使用する Git URL を取得します。

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. App Service の Web アプリの Git リポジトリを参照するリモートをクローンに追加します。 \<Git clone URL\> の場合、前の手順で返された URL を使用します。 コマンド ウィンドウで次のコマンドを実行します。

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. App Service にコードをデプロイするには、コマンド ウィンドウで次のコマンドを入力します。 資格情報の入力を求められたら、手順 5 で作成したユーザー レベルのデプロイ資格情報を入力します。 必ず、App Service リモートのメイン ブランチにプッシュしてください。

    ```cmd
    git push webapp main:main
    ```

9. コマンド ウィンドウで、デプロイの進行状況が更新されます。 デプロイが成功すると、次の出力のような行で終了します。

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  main -> main
    ```

10. 次のコマンドを実行して、Web アプリの状態のクエリを実行し、それが実行中であることを確認します。

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. ブラウザーで `https://<your web app name>.azurewebsites.net` にアクセスします。 Web アプリをローカルで実行したときに表示されたものと同様の Web ページが表示されます。 デバイスが実行中でデータを送信していれば、デバイスによって送信された最新の 50 個の温度と湿度を示す測定値の実行中のプロットが表示されるはずです。

## <a name="troubleshooting"></a>トラブルシューティング

このサンプルで問題が発生した場合は、次のセクションの手順を試してください。 まだ問題がある場合は、このトピックの下部でフィードバックを送信してください。

### <a name="client-issues"></a>クライアントの問題

* デバイスが一覧に表示されない場合や、グラフが描画されていない場合は、お使いのデバイスでデバイス コードが実行されていることを確認してください。

* ブラウザーで開発者ツールを開き (多くのブラウザーでは、F12 キーで開きます)、コンソールを見つけます。 そこに警告やエラーが表示されていないか確認します。

* クライアント側のスクリプトは /js/chat-device-data.js でデバッグできます。

### <a name="local-website-issues"></a>ローカル Web サイトの問題

* コンソールの出力についてノードを起動したウィンドウの出力を確認します。

* サーバー コード (具体的には server.js と /scripts/event-hub-reader.js) をデバッグします。

### <a name="azure-app-service-issues"></a>Azure App Service の問題

* Azure portal で、Web アプリに移動します。 左側のウィンドウの **[監視]** から **[App Service ログ]** を選択します。 **[アプリケーションのログ記録 (ファイル システム)]** を有効にし、 **[レベル]** を [エラー] に設定して、 **[保存]** を選択します。 次に、( **[監視]** の下の) **[ログ ストリーム]** を開きます。

* Azure portal の Web アプリから、 **[開発ツール]** の下で **[コンソール]** を選択し、`node -v` と `npm -v` を使用してノードと npm のバージョンを確認します。

* パッケージが見つからないというエラーが表示される場合は、手順が正しく実行されていない可能性があります。 サイトが (`git push` を使用して) デプロイされると、アプリ サービスは `npm install` を実行します。これは、構成したノードの現在のバージョンに基づいて行われます。 後になってそれが構成で変更される場合は、コードに意味のない変更を行って、再度プッシュする必要があります。

## <a name="next-steps"></a>次のステップ

Web アプリを使用して、IoT Hub からのリアルタイム センサー データを視覚化することができました。

Azure IoT Hub からのデータを視覚化するもう 1 つの方法については、「[Power BI を使用して Azure IoT Hub からのリアルタイム センサー データを視覚化する](iot-hub-live-data-visualization-in-power-bi.md)」を参照してください。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
