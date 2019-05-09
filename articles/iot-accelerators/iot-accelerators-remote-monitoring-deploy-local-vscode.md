---
title: リモート監視ソリューションをローカルにデプロイする (Visual Studio Code) - Azure | Microsoft Docs
description: この攻略ガイドでは、リモート監視ソリューション アクセラレータをテストおよび開発のために Visual Studio Code を使用してローカル コンピューターにデプロイする方法を示します。
author: avneet723
manager: hegate
ms.author: avneet723
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 946f815cecea4cc172fac35c0b260d795317e6e1
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316220"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio-code"></a>リモート監視ソリューション アクセラレータをローカルでデプロイする - Visual Studio Code

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

この記事では、リモート監視ソリューション アクセラレータをテストおよび開発のためにローカル コンピューターにデプロイする方法を示します。 また、Visual Studio Code でマイクロ サービスを実行する方法も示します。 ローカルのマイクロサービス デプロイで使用するクラウド サービスは、IoT Hub、Cosmos DB、Azure Stream Analytics、Azure Time Series Insights です。

## <a name="prerequisites"></a>前提条件

リモート監視ソリューション アクセラレータによって使用される Azure サービスをデプロイするには、アクティブな Azure サブスクリプションが必要です。

アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。

### <a name="machine-setup"></a>コンピューターのセットアップ

ローカル デプロイを完了するには、ローカル開発マシンにインストールされた次のツールが必要です。

* [Git](https://git-scm.com/)
* [.NET Core](https://dotnet.microsoft.com/download)
* [Docker](https://www.docker.com)
* [Nginx](https://nginx.org/en/download.html)
* [Visual Studio Code](https://code.visualstudio.com/)
* [VS Code の C# 拡張機能](https://code.visualstudio.com/docs/languages/csharp)
* [Node.js v8](https://nodejs.org/) - このソフトウェアは、スクリプトが Azure リソースを作成するために使用する PCS CLI の前提条件です。 Node.js v10 は使用しないでください。

> [!NOTE]
> Visual Studio Code は、Windows、Mac、Ubuntu で利用できます。

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>マイクロサービスを実行する

このセクションでは、リモート監視マイクロサービスを実行します。 Web UI をネイティブで実行し、Docker でデバイス シミュレーション サービスを実行し、Visual Studio Code でマイクロサービスを実行します。

### <a name="build-the-code"></a>コードのビルド

コマンド プロンプトで azure-iot-pcs-remote-monitoring-dotnet\services に移動し、次のコマンドを実行してコードをビルドします。

```cmd
dotnet restore
dotnet build -c Release
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>ローカル コンピューター上のその他すべてのマイクロサービスをデプロイする

次の手順では、Visual Studio 2017 のリモート監視マイクロサービスを実行する方法を示します。

1. Visual Studio Code を起動します。
1. VS Code でローカル コピーから **azure-iot-pcs-remote-monitoring-dotnet** モジュールを開きます。
1. scripts\local\launch\idesettings\vscode から **launch.json** ファイルと **tasks.json** ファイルをコピーします。\. 新しいフォルダー **azure-iot-pcs-remote-monitoring-dotnet\.vscode** を作成して、そこにファイルを貼り付けます。
1. VS Code で [デバッグ] パネルを開き、**[Run all microservices]\(すべてのマイクロサービスを実行\)** 構成を実行します。 この構成は、デバイス シミュレーション マイクロサービスを Docker で実行し、他のマイクロサービスをデバッガーで実行します。

たとえば**デバッグ コンソール**では、**Auth** サービスの出力が次のように表示されます。

[![ローカル Auth サービスのデプロイ](./media/deploy-locally-vscode/auth-debug-results-inline.png)](./media/deploy-locally-vscode/auth-debug-results-expanded.png#lightbox)

### <a name="run-the-web-ui"></a>Web UI を実行する

この手順では、Web UI を開始します。 ローカル コピーの **azure-iot-pcs-remote-monitoring-dotnet\webui** フォルダーに移動し、次のコマンドを実行します。

```cmd
npm install
npm start
```

起動が完了すると、ブラウザーに **http:\//localhost:3000/dashboard** ページが表示されます。 このページには、エラーが存在する可能性があります。 エラーがない状態でアプリケーションを表示するには、次の手順を実行してください。

### <a name="configure-and-run-nginx"></a>NGINX を構成および実行する

ローカル コンピューターで実行されているマイクロサービスと Web アプリケーションをリンクするリバース プロキシ サーバーを設定します。

* **webui\scripts\localhost** フォルダーから **nginx.conf** ファイルを **nginx\conf** インストール ディレクトリにコピーします。
* **nginx** を実行します。

**nginx** の実行の詳細については、[nginx for Windows](https://nginx.org/en/docs/windows.html) を参照してください。

### <a name="connect-to-the-dashboard"></a>ダッシュボードに接続する

リモート監視ソリューションのダッシュボードにアクセスするには、ブラウザーで http:\//localhost:9000 に移動します。

## <a name="clean-up"></a>クリーンアップ

不必要な課金を避けるために、テストを完了したら、Azure サブスクリプションからクラウド サービスを削除します。 サービスを削除するには、[Azure portal](https://ms.portal.azure.com) に移動し、**start.cmd** スクリプトが作成したリソース グループを削除します。

また、GitHub からソース コードを複製するときに作成されたリモート監視リポジトリのローカル コピーを削除することもできます。

## <a name="next-steps"></a>次の手順

これで、リモート監視ソリューションのデプロイが完了しました。次の手順は[ソリューション ダッシュボードの機能を確認](quickstart-remote-monitoring-deploy.md)することです。
