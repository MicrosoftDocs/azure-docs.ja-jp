---
title: リモート監視ソリューションのローカルでのデプロイ - Docker - Azure | Microsoft Docs
description: この攻略ガイドでは、リモート監視ソリューション アクセラレータをテストおよび開発のために Docker を使用してローカル コンピューターにデプロイする方法を示します。
author: avneet723
manager: hegate
ms.author: avneet723
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: c00e62e237fe263f54926c8e74fb6211a2e5a4e4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57993048"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---docker"></a>リモート監視ソリューション アクセラレータのローカルでのデプロイ - Docker

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

この記事では、リモート監視ソリューション アクセラレータをテストおよび開発のためにローカル コンピューターにデプロイする方法を示します。 また、ローカルの Docker コンテナーに、マイクロサービスをデプロイする方法も示します。 ローカルのマイクロサービス デプロイで使用するクラウド サービスは、ローカルのマイクロサービス デプロイで使用するクラウド サービスは、クラウド内の IoT Hub、Cosmos DB、Azure Stream Analytics、および Azure Time Series Insights サービスです。

リモート監視ソリューション アクセラレータをローカル コンピューター上の IDE で実行する場合は、「[Deploy the Remote Monitoring solution accelerator locally - Visual Studio](iot-accelerators-remote-monitoring-deploy-local.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

リモート監視ソリューション アクセラレータによって使用される Azure サービスをデプロイするには、アクティブな Azure サブスクリプションが必要です。

アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。

### <a name="machine-setup"></a>コンピューターのセットアップ

ローカル デプロイを完了するには、ローカル開発マシンにインストールされた次のツールが必要です。

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/) - マイクロサービスに対して変更を行う予定の場合。
* [Node.js v8](https://nodejs.org/) - このソフトウェアは、スクリプトが Azure リソースを作成するために使用する PCS CLI の前提条件です。 Node.js v10 は使用しないでください。

> [!NOTE]
> これらのツールは、Windows、Linux、iOS を含む多数のプラットフォームで使用できます。

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices-in-docker"></a>Docker 内のマイクロサービスを実行する

新しいコマンド プロンプトを開いて、**start.cmd** スクリプトによって設定された環境変数にアクセスできることを確認します。 Windows では、次のコマンドを実行して、環境変数が設定されていることを確認できます。

```cmd
set PCS
```

このコマンドは、**start.cmd** スクリプトによって設定されたすべての環境変数を表示します。

ローカル コンピューターで Docker が実行されていることを確認します。
> [!NOTE]
> Docker は、Windows で実行されている場合は [Linux コンテナー](https://docs.docker.com/docker-for-windows/)を実行している必要があります。

ローカルの Docker コンテナーで実行されているマイクロサービスは、Azure クラウド サービスにアクセスする必要があります。 次のコマンドを使用して、お使いの Docker 環境のインターネット接続をテストできます。このコマンドは、コンテナー内からインターネット アドレスを ping します。

```cmd/sh
docker run --rm -ti library/alpine ping google.com
```

ソリューション アクセラレータを実行するには、コマンドライン環境で **services\\scripts\\local** フォルダーに移動し、次のコマンドを実行します。

```cmd/sh
docker-compose up
```

> [!NOTE] 
> `docker-compose up` を実行する前に、Docker と[ローカル ドライブを共有している](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/issues/115)ことを確認します。

このコマンドを初めて実行すると、Docker は Docker ハブからマイクロサービス イメージをダウンロードして、コンテナーをローカルに構築します。 以降の実行では、Docker はコンテナーを直ちに実行します。

> [!TIP]
> マイクロソフトは、新しい機能を備えた新しい Docker イメージを頻繁に公開しています。 最新のものをプルする前に、次のコマンド セットを使用して、ローカルの Docker コンテナーと対応するイメージをクリーン アップすることができます。

```cmd/sh
docker list
docker rm <list_of_containers>
docker rmi <list_of_images>
```

個別のシェルを使用して、コンテナーからのログを表示できます。 最初に、`docker ps` コマンドを使用してコンテナー ID を見つけます。 次に、`docker logs {container-id} --tail 1000` を使用して、指定したコンテナーの最後の 1000 個のエントリを表示します。

### <a name="start-the-stream-analytics-job"></a>Stream Analytics ジョブの開始

Stream Analytics ジョブを開始するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) に移動します。
1. ソリューション用に作成された**リソース グループ**に移動します。 このリソース グループの名前は、**start.cmd** スクリプトを実行したときにソリューション用に選択した名前です。
1. リソースの一覧で **[Stream Analytics ジョブ]** をクリックします。
1. Stream Analytics ジョブの **[概要]** ページで、**[開始]** ボタンをクリックします。 次に、**[開始]** をクリックしてジョブをすぐに開始します。

### <a name="connect-to-the-dashboard"></a>ダッシュボードに接続する

リモート監視ソリューションのダッシュボードにアクセスするには、ブラウザーで `http://localhost:8080` に移動します。 Web UI とローカルのマイクロサービスを使用することができます。

## <a name="clean-up"></a>クリーンアップ

不必要な課金を避けるために、テストを完了したら、Azure サブスクリプションからクラウド サービスを削除します。 サービスを削除するには、[Azure portal](https://ms.portal.azure.com) に移動し、**start.cmd** スクリプトが作成したリソース グループを削除します。

Docker イメージを削除し、ローカル コンピューター上の領域を解放するには、`docker-compose down --rmi all` コマンドを使用します。 また、GitHub からソース コードを複製するときに作成されたリモート監視リポジトリのローカル コピーを削除することもできます。

## <a name="next-steps"></a>次の手順

これで、リモート監視ソリューションのデプロイが完了しました。次の手順は[ソリューション ダッシュボードの機能を確認](quickstart-remote-monitoring-deploy.md)することです。
