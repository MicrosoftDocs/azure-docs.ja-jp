---
title: リモート監視ソリューションを (IntelliJ IDE 経由で) ローカルにデプロイする - Azure | Microsoft Docs
description: この攻略ガイドでは、テストおよび開発のために IntelliJ を使用してリモート監視ソリューション アクセラレータをローカル コンピューターにデプロイする方法を示します。
author: v-krghan
manager: dominicbetts
ms.author: v-krghan
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: 2f3c11763bb2f406caf9d33275fc29b0d140da9a
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "70743308"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>リモート監視ソリューション アクセラレータのローカルでのデプロイ - IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

この記事では、リモート監視ソリューション アクセラレータをテストおよび開発のためにローカル コンピューターにデプロイする方法を示します。 IntelliJ でマイクロサービスを実行する方法を学習できます。 ローカルのマイクロサービス デプロイで使用するクラウド サービスは、IoT Hub、Azure Cosmos DB、Azure Stream Analytics、Azure Time Series Insights です。

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
* [IntelliJ Scala プラグイン](https://plugins.jetbrains.com/plugin/1347-scala)
* [IntelliJ SBT プラグイン](https://plugins.jetbrains.com/plugin/5007-sbt)
* [IntelliJ SBT Executor プラグイン](https://plugins.jetbrains.com/plugin/7247-sbt-executor)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/)

Node.js v8 は、スクリプトで Azure リソースを作成するために使用する PCS CLI の前提条件です。 Node.js v10 は使用しないでください。

> [!NOTE]
> IntelliJ IDE は Windows と Mac で利用できます。

## <a name="download-the-source-code"></a>ソース コードをダウンロードする

リモート監視ソース コード リポジトリには、マイクロサービスの Docker イメージを実行するために必要なソース コードと Docker 構成ファイルが含まれています。

複製してローカル バージョンのリポジトリを作成するには、コマンドライン環境を使用してローカル コンピューター上の適切なフォルダーに移動します。 その後、次のいずれかのコマンド セットを実行して、Java リポジトリを複製します。

* Java マイクロサービスの実装の最新バージョンをダウンロードするには、次のコマンドを実行します。

  ```cmd/sh
  git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git
  ```

* 最新のサブモジュールを取得するには、次のコマンドを実行します。

   ```cmd/sh
   cd azure-iot-pcs-remote-monitoring-java
   git submodule foreach git pull origin master
    ```

> [!NOTE]
> これらのコマンドでは、マイクロサービスのローカル実行に使用するスクリプトだけでなく、すべてのマイクロサービスのソース コードがダウンロードされます。 Docker でマイクロサービスを実行する場合、このソース コードは必要ありません。 ただし、後でソリューション アクセラレータを変更して変更内容をローカルでテストする予定がある場合は、このソース コードが役立ちます。

## <a name="deploy-the-azure-services"></a>Azure サービスをデプロイする

この記事ではマイクロサービスをローカルに実行する方法を示しますが、これらはクラウド内で実行されている Azure サービスに依存します。 Azure サービスをデプロイするには、次のスクリプトを使用します。 このスクリプトの例では、Windows マシンで Java リポジトリを使用していることを前提としています。 別の環境で作業している場合は、パス、ファイル拡張子、およびパスの区切り記号を適切に調整してください。

### <a name="create-new-azure-resources"></a>新しい Azure リソースを作成する

必要な Azure リソースをまだ作成していない場合は、次の手順のようにします。

1. コマンドライン環境で、複製コピーしたリポジトリ内の **\services\scripts\local\launch** フォルダーに移動します。

1. 次のコマンドを実行して **pcs** CLI ツールをインストールし、Azure アカウントにサインインします。

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. **start.cmd** スクリプトを実行します。 このスクリプトでは、次の情報を入力するよう求められます。

   * ソリューション名。
   * 使用する Azure サブスクリプション。
   * 使用する Azure データセンターの場所。

   このスクリプトにより、指定したソリューション名のリソース グループが Azure に作成されます。 このリソース グループには、ソリューション アクセラレータが使用する Azure リソースが含まれます。 このリソース グループは、該当するリソースが不要になったら削除できます。

   また、このスクリプトにより、ローカル コンピューターに一連の環境変数も追加されます。 各変数名には、**PCS** というプレフィックスが付いています。 これらの環境変数では、リモート監視で Azure Key Vault リソースからその構成値を読み取れるようにするための詳細が提供されます。

   > [!TIP]
   > このスクリプトが完了すると、環境変数は、 **\<実際のホーム フォルダー\>\\.pcs\\\<ソリューション名\>.env** という名前のファイルに保存されます。 これらは、将来のソリューション アクセラレータのデプロイに使用できます。 **docker-compose** を実行すると、ローカル コンピューターで設定した環境変数が、**services\\scripts\\local\\.env** ファイル内の値をオーバーライドすることに注意してください。

1. コマンドライン環境を閉じます。

### <a name="use-existing-azure-resources"></a>既存の Azure リソースを使用する

必要な Azure リソースを既に作成済みの場合は、ローカル コンピューターで対応する環境変数を設定します。
* **PCS_KEYVAULT_NAME**: Key Vault リソースの名前。
* **PCS_AAD_APPID**: Azure Active Directory (Azure AD) アプリケーションの ID。
* **PCS_AAD_APPSECRET**: Azure AD アプリケーションのシークレット。

構成値はこの Key Vault リソースから読み取られます。 これらの環境変数は、デプロイから **\<実際のホーム フォルダー\>\\.pcs\\\<ソリューション名\>.env** ファイルに保存できます。 **docker-compose** を実行すると、ローカル コンピューターに設定した環境変数が、**services\\scripts\\local\\.env** ファイル内の値をオーバーライドすることに注意してください。

マイクロサービスで必要な構成の一部は、初回デプロイ時に作成された Key Vault のインスタンスに格納されます。 このキー コンテナー内の対応する変数は、必要に応じて変更する必要があります。

## <a name="run-the-microservices"></a>マイクロサービスを実行する

このセクションでは、リモート監視マイクロサービスを実行します。 以下を実行します。

* ネイティブで Web UI。
* Docker で Azure IoT デバイス シミュレーション、認証、Azure Stream Analytics Manager サービス。
* IntelliJ でマイクロサービス。

### <a name="run-the-device-simulation-service"></a>デバイス シミュレーション サービスを実行する

新しいコマンド プロンプト ウィンドウを開きます。 前のセクションで **start.cmd** スクリプトによって設定された環境変数にアクセスできることを確認します。

次のコマンドを実行して、デバイス シミュレーション サービス用の Docker コンテナーを開きます。 このサービスは、リモート監視ソリューション用のデバイスをシミュレートします。

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>認証サービスを実行する

新しいコマンド プロンプト ウィンドウを開き、次のコマンドを実行して認証サービス用の Docker コンテナーを開きます。 このサービスを使用することで、Azure IoT ソリューションにアクセスする権限を持つユーザーを管理できます。

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-stream-analytics-manager-service"></a>Stream Analytics Manager サービスを実行する

新しいコマンド プロンプト ウィンドウを開き、次のコマンドを実行して Stream Analytics Manager サービス用の Docker コンテナーを開きます。 このサービスを使用すると、Stream Analytics ジョブを管理できます。 このような管理には、ジョブ構成の設定やジョブの状態の開始、停止、監視が含まれます。

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-your-local-machine"></a>ローカル コンピューター上にその他すべてのマイクロサービスをデプロイする

次の手順では、IntelliJ でリモート監視マイクロサービスを実行する方法を示します。

#### <a name="import-a-project"></a>プロジェクトをインポートする

1. IntelliJ IDE を開きます。
1. **[Import Project]\(プロジェクトのインポート\)** を選択します。
1. **azure-iot-pcs-remote-monitoring-java\services\build.sbt** を選択します。

#### <a name="create-run-configurations"></a>実行構成を作成する

1. **[Run]\(実行\)**  >  **[Edit Configurations]\(構成の編集\)** の順に選択します。
1. **[Add New Configuration]\(新しい構成の追加\)**  >  **[sbt task]\(sbt タスク\)** の順に選択します。
1. **[Name]\(名前\)** を入力し、 **[Tasks]\(タスク\)** に「**run**」と入力します。
1. 実行するサービスに基づいて **[Working Directory]\(作業ディレクトリ\)** を選択します。
1. **[Apply]\(適用\)**  >  **[OK]** の順に選択して選択内容を保存します。
1. 次の Web サービスについて実行構成を作成します。
    * WebService (services\config)
    * WebService (services\device-telemetry)
    * WebService (services\iothub-manager)
    * WebService (services\storage-adapter)

例として、次の図では、サービスの構成を追加する方法を示しています。

[![IntelliJ IDE の [Run/Debug Configurations]\(実行/デバッグ構成\) ウィンドウのスクリーンショット。左側のウィンドウの sbt タスク リストで強調表示された [storageAdapter] オプションと、右側のウィンドウの [Name]\(名前\)、[Tasks]\(タスク\)、[Working directory]\(作業ディレクトリ\)、[VM parameters]\(VM パラメーター\) の各ボックスの入力内容を示しています。](./media/deploy-locally-intellij/run-configurations.png)](./media/deploy-locally-intellij/run-configurations.png#lightbox)

#### <a name="create-a-compound-configuration"></a>複合構成を作成する

1. すべてのサービスをまとめて実行するには、 **[Add new Configuration]\(新しい構成の追加\)**  >  **[Compound]\(複合\)** の順に選択します。
1. **[Name]\(名前\)** を入力し、 **[add sbt tasks]\(sbt タスクの追加\)** を選択します。
1. **[Apply]\(適用\)**  >  **[OK]** の順に選択して選択内容を保存します。

例として、次の図では、すべての sbt タスクを 1 つの構成に追加する方法を示しています。

[![IntelliJ IDE の [Run/Debug Configurations]\(実行/デバッグ構成\) ウィンドウのスクリーンショット。左側のウィンドウの [Compound]\(複合\) の一覧で強調表示された [AllServices] オプションと、右側のウィンドウで強調された [sbt Task 'deviceTelemetry']\(sbt タスク 'deviceTelemetry'\) オプションを示しています。](./media/deploy-locally-intellij/all-services.png)](./media/deploy-locally-intellij/all-services.png#lightbox)

**[Run]\(実行\)** を選択して、ローカル コンピューター上で Web サービスをビルドして実行します。

Web サービスごとにコマンド プロンプト ウィンドウと Web ブラウザー ウィンドウが開きます。 コマンド プロンプトに、実行中のサービスからの出力が表示されます。 ブラウザー ウィンドウでは、状態を監視することができます。 コマンド プロンプト ウィンドウまたは Web ページは閉じないでください。そのような操作を行うと Web サービスが停止します。

サービスの状態にアクセスするには、次の URL に移動します。

* IoT-Hub Manager: [http://localhost:9002/v1/status](http://localhost:9002/v1/status)
* デバイス テレメトリ: [http://localhost:9004/v1/status](http://localhost:9004/v1/status)
* config: [http://localhost:9005/v1/status](http://localhost:9005/v1/status)
* storage-adapter: [http://localhost:9022/v1/status](http://localhost:9022/v1/status)

### <a name="start-the-stream-analytics-job"></a>Stream Analytics ジョブの開始

Stream Analytics ジョブを開始するには、次の手順に従います。

1. [Azure ポータル](https://portal.azure.com)にアクセスします。
1. ソリューション用に作成された**リソース グループ**に移動します。 このリソース グループの名前は、**start.cmd** スクリプトを実行したときにソリューション用に選択した名前です。
1. リソースの一覧で **[Stream Analytics ジョブ]** を選択します。
1. Stream Analytics ジョブの **[概要]** ページで、 **[開始]** ボタンを選択し、 **[開始]** を選択してジョブを開始します。

### <a name="run-the-web-ui"></a>Web UI を実行する

この手順では、Web UI を開始します。 新しいコマンド プロンプト ウィンドウを開きます。 **start.cmd** スクリプトによって設定された環境変数にアクセスできることを確認します。 リポジトリのローカル コピー内の **webui** フォルダーに移動し、次のコマンドを実行します。

```cmd
npm install
npm start
```

**start** コマンドが完了すると、ブラウザーには、[http://localhost:3000/dashboard](http://localhost:3000/dashboard) というアドレスのページが表示されます。 このページには、エラーが存在する可能性があります。 エラーがない状態でアプリケーションを表示するには、次の手順を実行してください。

### <a name="configure-and-run-nginx"></a>Nginx を構成および実行する

ローカル コンピューターで実行されているマイクロサービスに Web アプリケーションをリンクさせるリバース プロキシ サーバーを設定します。

1. リポジトリのローカル コピー内にある **webui\scripts\localhost** フォルダーから **nginx.conf** ファイルを **nginx\conf** インストール ディレクトリにコピーします。
1. Nginx を実行します。

Nginx の実行の詳細については、「[nginx for Windows](https://nginx.org/en/docs/windows.html)」を参照してください。

### <a name="connect-to-the-dashboard"></a>ダッシュボードに接続する

リモート監視ソリューションのダッシュボードにアクセスするには、ブラウザーで http://localhost:9000 に移動します。

## <a name="clean-up"></a>クリーンアップ

不必要な課金を避けるために、テストが完了したら、お使いの Azure サブスクリプションからクラウド サービスを削除してください。 サービスを削除するには、[Azure portal](https://ms.portal.azure.com) に移動し、**start.cmd** スクリプトによって作成されたリソース グループを削除します。

また、GitHub からソース コードを複製したときに作成されたリモート監視リポジトリのローカル コピーも削除してかまいません。

## <a name="next-steps"></a>次の手順

これで、リモート監視ソリューションのデプロイが完了しました。次の手順は[ソリューション ダッシュボードの機能を確認](quickstart-remote-monitoring-deploy.md)することです。
