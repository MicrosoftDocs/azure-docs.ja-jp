---
title: リモート監視ソリューションを (Visual Studio IDE 経由で) ローカルにデプロイする - Azure | Microsoft Docs
description: この攻略ガイドでは、リモート監視ソリューション アクセラレータをテストおよび開発のために Visual Studio を使用してローカル コンピューターにデプロイする方法を示します。
author: avneet723
manager: hegate
ms.author: avneet723
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: fdb9b6c09e81ce29d2587fb0faca1aa80d28ea38
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58309981"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio"></a>リモート監視ソリューション アクセラレータをローカルでデプロイする - Visual Studio

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

この記事では、リモート監視ソリューション アクセラレータをテストおよび開発のためにローカル コンピューターにデプロイする方法を示します。 また、Visual Studio でマイクロ サービスを実行する方法も示します。 ローカルのマイクロサービス デプロイで使用するクラウド サービスは、ローカルのマイクロサービス デプロイで使用するクラウド サービスは、クラウド内の IoT Hub、Cosmos DB、Azure Stream Analytics、および Azure Time Series Insights サービスです。

リモート監視ソリューション アクセラレータをローカル コンピューター上の Docker で実行する場合、[リモート監視ソリューション アクセラレータをローカルでデプロイする - Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md) に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

リモート監視ソリューション アクセラレータによって使用される Azure サービスをデプロイするには、アクティブな Azure サブスクリプションが必要です。

アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。

### <a name="machine-setup"></a>コンピューターのセットアップ

ローカル デプロイを完了するには、ローカル開発マシンにインストールされた次のツールが必要です。

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/) - このソフトウェアは、スクリプトが Azure リソースを作成するために使用する PCS CLI の前提条件です。 Node.js v10 は使用しないでください。

> [!NOTE]
> Visual Studio は Windows と Mac で利用できます。

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>マイクロサービスを実行する

このセクションでは、リモート監視マイクロサービスを実行します。 Web UI をネイティブで実行し、Docker でデバイス シミュレーション サービスを実行し、Visual Studio でマイクロサービスを実行します。

### <a name="run-the-device-simulation-service"></a>デバイス シミュレーション サービスを実行する

新しいコマンド プロンプト ウィンドウを開いて、前のセクションで **start.cmd** スクリプトによって設定された環境変数にアクセスできることを確認します。

デバイス シミュレーション サービス用の Docker コンテナーを起動するには、次のコマンドを実行します。 このサービスは、リモート監視ソリューション用のデバイスをシミュレートします。

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>ローカル コンピューター上のその他すべてのマイクロサービスをデプロイする

次の手順では、Visual Studio 2017 のリモート監視マイクロサービスを実行する方法を示します。

1. Visual Studio 2017 を起動します
1. リポジトリのローカル コピー内の **services** フォルダーで、**remote-monitoring.sln** ソリューションを開きます。
1. **ソリューション エクスプローラー**でソリューションを右クリックして、**[プロパティ]** をクリックします。
1. **[共通プロパティ] > [スタートアップ プロジェクト]** を選択します。
1. **[マルチ スタートアップ プロジェクト]** を選択し、以下のプロジェクトで **[アクション]** を **[開始]** に設定します。
    * WebService (asa-manager\WebService)
    * WebService (auth\WebService)
    * WebService (config\WebService)
    * WebService (device-telemetry\WebService)
    * WebService (iothub-manager\WebService)
    * WebService (storage-adapter\WebService)
1. **[OK]** をクリックして変更を保存します。
1. **[デバッグ] > [デバッグの開始]** をクリックして、ローカル コンピューター上で Web サービスをビルドして実行します。

Web サービスごとにコマンド プロンプトと Web ブラウザー ウィンドウが開きます。 コマンド プロンプトには実行中のサービスの出力が表示され、ブラウザー ウィンドウでは状態を監視することができます。 コマンド プロンプトや Web ページを閉じると Web サービスが停止するため、閉じないでください。

### <a name="start-the-stream-analytics-job"></a>Stream Analytics ジョブの開始

Stream Analytics ジョブを開始するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) に移動します。
1. ソリューション用に作成された**リソース グループ**に移動します。 このリソース グループの名前は、**start.cmd** スクリプトを実行したときにソリューション用に選択した名前です**。
1. リソースの一覧で **[Stream Analytics ジョブ]** をクリックします。
1. Stream Analytics ジョブの **[概要]** ページで、**[開始]** ボタンをクリックします。 次に、**[開始]** をクリックしてジョブをすぐに開始します。

### <a name="run-the-web-ui"></a>Web UI を実行する

この手順では、Web UI を開始します。 新しいコマンド プロンプト ウィンドウを開いて、**start.cmd** スクリプトによって設定された環境変数にアクセスできることを確認します。 リポジトリのローカル コピーにある **webui** フォルダーに移動し、次のコマンドを実行します。

```cmd
npm install
npm start
```

起動が完了すると、ブラウザーに **http:\//localhost:3000/dashboard** ページが表示されます。 このページには、エラーが存在する可能性があります。 エラーがない状態でアプリケーションを表示するには、次の手順を実行してください。

### <a name="configure-and-run-nginx"></a>NGINX を構成および実行する

ローカル コンピューターで実行されているマイクロサービスと Web アプリケーションをリンクするリバース プロキシ サーバーを設定します。

* リポジトリのローカル コピーにある **webui\scripts\localhost** フォルダーから **nginx.conf** ファイルを **nginx\conf** インストール ディレクトリにコピーします。
* **nginx** を実行します。

**nginx** の実行の詳細については、[nginx for Windows](https://nginx.org/en/docs/windows.html) を参照してください。

### <a name="connect-to-the-dashboard"></a>ダッシュボードに接続する

リモート監視ソリューションのダッシュボードにアクセスするには、ブラウザーで http:\//localhost:9000 に移動します。

## <a name="clean-up"></a>クリーンアップ

不必要な課金を避けるために、テストを完了したら、Azure サブスクリプションからクラウド サービスを削除します。 サービスを削除するには、[Azure portal](https://ms.portal.azure.com) に移動し、**start.cmd** スクリプトが作成したリソース グループを削除します。

また、GitHub からソース コードを複製するときに作成されたリモート監視リポジトリのローカル コピーを削除することもできます。

## <a name="next-steps"></a>次の手順

これで、リモート監視ソリューションのデプロイが完了しました。次の手順は[ソリューション ダッシュボードの機能を確認](quickstart-remote-monitoring-deploy.md)することです。
