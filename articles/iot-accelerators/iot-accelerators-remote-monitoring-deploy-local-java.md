---
title: リモート監視ソリューションを (IntelliJ IDE 経由で) ローカルにデプロイする - Azure | Microsoft Docs
description: この攻略ガイドでは、リモート監視ソリューション アクセラレータをテストおよび開発のために IntelliJ を使用してローカル コンピューターにデプロイする方法を示します。
author: v-krghan
manager: dominicbetts
ms.author: v-krghan
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: ccdb3e16ea60cf85ae28e533e3b2d9f473cc90c8
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316390"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>リモート監視ソリューション アクセラレータのローカルでのデプロイ - IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

この記事では、リモート監視ソリューション アクセラレータをテストおよび開発のためにローカル コンピューターにデプロイする方法を示します。 また、IntelliJ でマイクロ サービスを実行する方法も示します。 ローカルのマイクロサービス デプロイで使用するクラウド サービスは、ローカルのマイクロサービス デプロイで使用するクラウド サービスは、クラウド内の IoT Hub、Cosmos DB、Azure Stream Analytics、および Azure Time Series Insights サービスです。

リモート監視ソリューション アクセラレータをローカル コンピューター上の Docker で実行する場合、[リモート監視ソリューション アクセラレータをローカルでデプロイする - Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md) に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

リモート監視ソリューション アクセラレータによって使用される Azure サービスをデプロイするには、アクティブな Azure サブスクリプションが必要です。

アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。

### <a name="machine-setup"></a>コンピューターのセットアップ

ローカル デプロイを完了するには、ローカル開発マシンにインストールされた次のツールが必要です。

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [IntelliJ コミュニティ エディション](https://www.jetbrains.com/idea/download/)
* [IntelliJ プラグイン Scala](https://plugins.jetbrains.com/plugin/1347-scala)
* [IntelliJ プラグイン SBT](https://plugins.jetbrains.com/plugin/5007-sbt)
* [IntelliJ プラグイン SBT Executor](https://plugins.jetbrains.com/plugin/7247-sbt-executor)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/) - このソフトウェアは、スクリプトが Azure リソースを作成するために使用する PCS CLI の前提条件です。 Node.js v10 は使用しないでください。

> [!NOTE]
> IntelliJ IDE は Windows と Mac で利用できます。

[!INCLUDE [iot-accelerators-local-setup-java](../../includes/iot-accelerators-local-setup-java.md)]

## <a name="run-the-microservices"></a>マイクロサービスを実行する

このセクションでは、リモート監視マイクロサービスを実行します。 Web UI をネイティブで実行し、Docker でデバイス シミュレーション サービス、認証サービス、ASA Manager サービスを実行し、IntelliJ でマイクロサービスを実行します。

### <a name="run-the-device-simulation-service"></a>デバイス シミュレーション サービスを実行する

新しいコマンド プロンプト ウィンドウを開いて、前のセクションで **start.cmd** スクリプトによって設定された環境変数にアクセスできることを確認します。

デバイス シミュレーション サービス用の Docker コンテナーを起動するには、次のコマンドを実行します。 このサービスは、リモート監視ソリューション用のデバイスをシミュレートします。

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>認証サービスを実行する

新しいコマンド プロンプト ウィンドウを開き、次のコマンドを実行して認証サービス用の Docker コンテナーを起動します。 このサービスを使用すると、Azure IoT ソリューションにアクセスする権限を持つユーザーを管理できます。

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-asa-manager-service"></a>ASA Manager サービスを実行する

新しいコマンド プロンプト ウィンドウを開き、次のコマンドを実行して ASA Manager サービス用の Docker コンテナーを起動します。 このサービスを使用すると、構成の設定、開始、停止、状態の監視など、Azure Stream Analytics (ASA) ジョブを管理できます。

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>ローカル コンピューター上のその他すべてのマイクロサービスをデプロイする

次の手順では、IntelliJ のリモート監視マイクロサービスを実行する方法を示します。

#### <a name="import-project"></a>プロジェクトのインポート

1. IntelliJ IDE を起動する
1. **[Import Project]\(プロジェクトのインポート\)** を選択し、**azure-iot-pcs-remote-monitoring-java\services\build.sbt** を選択します

#### <a name="create-run-configurations"></a>実行の構成を作成する

1. **[Run]\(実行\) > [Edit Configurations]\(構成の編集\)** の順に選択します
1. **[Add New Configuration]\(新しい構成の追加\) > [sbt task]\(sbt タスク\)** の順に選択します 
1. **[Name]\(名前\)** を入力し、実行として「**Tasks**」と入力します 
1. 実行するサービスに基づいて **[Working Directory]\(作業ディレクトリ\)** を選択します
1. **[Apply]\(適用\) > [OK]** の順にクリックして選択内容を保存します。
1. 次のサービスについて実行の構成を作成します。
    * WebService (services\config)
    * WebService (services\device-telemetry)
    * WebService (services\iothub-manager)
    * WebService (services\storage-adapter)

次の図は、サービスの構成を追加する例を示しています。

[![Add-Configuration](./media/deploy-locally-intelliJ/run-configurations.png)](./media/deploy-locally-intelliJ/run-configurations.png#lightbox)


#### <a name="create-compound-configuration"></a>複合構成を作成する

1. すべてのサービスを実行するには、**[Add new Configuration]\(新しい構成の追加\) > [Compound]\(複合\)** の順に選択します
1. **[Name]\(名前\)** を入力し、**sbt タスクを追加**します
1. **[Apply]\(適用\) > [OK]** の順にクリックして選択内容を保存します。

次の図は、すべての sbt タスクを 1 つの構成に追加する例を示しています。


[![Add-All-Services](./media/deploy-locally-intelliJ/all-services.png)](./media/deploy-locally-intelliJ/all-services.png#lightbox)



1. **[Run]\(実行\)** をクリックして、ローカル コンピューター上で Web サービスをビルドして実行します。

Web サービスごとにコマンド プロンプトと Web ブラウザー ウィンドウが開きます。 コマンド プロンプトには実行中のサービスの出力が表示され、ブラウザー ウィンドウでは状態を監視することができます。 コマンド プロンプトや Web ページを閉じると Web サービスが停止するため、閉じないでください。


サービスの状態にアクセスするには、以下の URL に移動します。
* IoT-Hub Manager [http://localhost:9002/v1/status](http://localhost:9002/v1/status)
* デバイス テレメトリ [http://localhost:9004/v1/status](http://localhost:9004/v1/status)
* config [http://localhost:9005/v1/status](http://localhost:9005/v1/status)
* storage-adapter [http://localhost:9022/v1/status](http://localhost:9022/v1/status)


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
