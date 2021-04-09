---
title: チュートリアル - Azure IoT Central でビデオ分析用 IoT Edge インスタンスを作成する (Linux VM)
description: このチュートリアルでは、ビデオ分析 (物体とモーションの検出) アプリケーション テンプレートを使用して、ビデオ分析用 IoT Edge インスタンスを Linux VM に作成する方法について説明します。
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: 10ddbf3dde62380eb79af685ad41b22e4552cea1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99832625"
---
# <a name="tutorial-create-an-iot-edge-instance-for-video-analytics-linux-vm"></a>チュートリアル:ビデオ分析用の IoT Edge インスタンスを作成する (Linux VM)

Azure IoT Edge は、以下をデプロイして実行することによってクラウド インテリジェンスをローカルで実現するフル マネージド サービスです。

* カスタム ロジック
* Azure サービス
* 人工知能

IoT Edge では、これらのサービスがクロスプラットフォームの IoT デバイス上で直接実行されるため、クラウドであれオフラインであれ、IoT ソリューションを安全かつ大規模に実行することができます。

このチュートリアルでは、IoT Edge デバイスを Azure VM で用意する方法について説明します。 IoT Edge インスタンスは、Azure IoT Central のビデオ分析 (物体とモーションの検出) アプリケーション テンプレートで使用されるライブ ビデオ分析モジュールを実行します。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * Azure IoT Edge ランタイムがインストールされた Azure VM を作成する
> * ライブ ビデオ分析モジュールのホストとして IoT Central に接続する IoT Edge 環境を用意する

## <a name="prerequisites"></a>前提条件

開始する前に、これよりも前の [Azure IoT Central でのビデオ分析アプリケーションの作成](./tutorial-video-analytics-create-app-yolo-v3.md)に関するチュートリアルか、[Azure IoT Central でのビデオ分析の作成 (OpenVINO&trade;)](tutorial-video-analytics-create-app-openvino.md) に関するチュートリアルを完了しておく必要があります。

さらに、Azure サブスクリプションが必要となります。 Azure サブスクリプションがない場合は、[Azure サインアップ ページ](https://aka.ms/createazuresubscription)で無料で作成できます。

## <a name="deploy-azure-iot-edge"></a>Azure IoT Edge をデプロイする

最新の IoT Edge ランタイムとライブ ビデオ分析モジュールがインストールされた Azure VM を作成するために、次のボタンを選択します。

[![iotedge-vm-deploy の [Azure に配置する] ボタン](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Flive-video-analytics%2Fmaster%2Fref-apps%2Flva-edge-iot-central-gateway%2Fvm_deploy%2FedgeModuleVMDeploy.json)

次の表の情報を使用して **[カスタム デプロイ]** フォームを完成させます。

| フィールド | 値 |
| ----- | ----- |
| サブスクリプション | Azure サブスクリプションを選択します。 |
| Resource group | *lva-rg* - 前のチュートリアルで作成したリソース グループ。 |
| リージョン       | *米国東部* |
| DNS ラベル プレフィックス | VM の一意の DNS プレフィックスを選択します。 すべて英字である必要があり、数字や特殊文字は使用できません。 |
| 管理ユーザー名 | *AzureUser* |
| 管理パスワード | パスワードを入力します。 このパスワードは後で使用します。*scratchpad.txt* ファイルに書き留めてください。 |
| スコープ ID | 前のチュートリアルでゲートウェイ デバイスを追加する際に *scratchpad.txt* ファイルに書き留めた **スコープ ID**。 |
| デバイス ID | *gateway-001* - 前のチュートリアルで作成したゲートウェイ デバイス。 |
| デバイス キー | 前のチュートリアルでゲートウェイ デバイスを追加する際に *scratchpad.txt* ファイルに書き留めた **デバイスの主キー**。 |
| IoT Central アプリ ホスト | 前のチュートリアルで *scratchpad.txt* ファイルに書き留めた **アプリケーション URL** (例: *traders.azureiotcentral.com*)。 |
| IoT Central アプリ API トークン | 前のチュートリアルで *scratchpad.txt* ファイルに書き留めた **オペレーター API トークン**。 |
| IoT Central デバイス プロビジョニング キー | 前のチュートリアルで *scratchpad.txt* ファイルに書き留めた **SAS-IoT-Devices グループの主キー**。 |
| VM サイズ | *Standard_DS1_v2* |
| Ubuntu OS バージョン | *18.04-LTS* |
| 場所 | *[resourceGroup().location]* |

**[Review + create]\(レビュー + 作成\)** を選択します。 検証が完了したら、 **[作成]** を選択します。 通常、デプロイが完了するまでには 3 分ほどかかります。 デプロイが完了したら、Azure portal で **lva-rg** リソース グループに移動します。

## <a name="ensure-the-iot-edge-runtime-loads-the-modules"></a>IoT Edge ランタイムにモジュールが読み込まれていることを確認する

Azure portal で、**lva-rg** リソース グループに移動して仮想マシンを選択します。 次に、 **[サポート + トラブルシューティング]** セクションで、 **[シリアル コンソール]** を選択します。

**Enter** キーを押して `login:` プロンプトを表示します。 ユーザー名には *AzureUser* を使用します。パスワードには、VM の作成時に選んだパスワードを使用してください。

次のコマンドを実行して、IoT Edge ランタイムのバージョンを確認します。 この記事の執筆時点では、バージョンは 1.0.9 です。

```bash
sudo iotedge --version
```

コマンドを使用して IoT Edge モジュールを一覧表示します。

```bash
sudo iotedge list
```

このデプロイでは、次の 5 つの IoT Edge モジュールが実行対象として構成されています。

* LvaEdgeGatewayModule
* edgeAgent
* edgeHub
* lvaEdge
* lvaYolov3

このデプロイでは、ライブ ビデオ分析に必要なモジュールを使用してカスタム IoT Edge 環境を作成しました。 既定の **config.yaml** は、IoT デバイス プロビジョニング サービスを使用して IoT Edge ランタイムが IoT Central に接続するように更新されています。 また、このデプロイでは、**state.json** というファイルが **/data/storage** フォルダーに作成されています。追加の構成データをモジュールに提供するためのものです。 詳細については、チュートリアル「[ビデオ分析用の IoT Edge インスタンスを作成する (Intel NUC)](./tutorial-video-analytics-iot-edge-nuc.md)」を参照してください。

IoT Edge デバイスをトラブルシューティングする方法については、「[IoT Edge デバイスのトラブルシューティング](../../iot-edge/troubleshoot.md)」を参照してください。

## <a name="use-the-rtsp-simulator"></a>RTSP シミュレーターを使用する

ビデオ分析アプリケーション テンプレートでは、IoT Edge デバイスに接続する実際のカメラ デバイスがない場合、シミュレートされた 2 つのカメラ デバイスを使用できます。 このセクションでは、シミュレートされたビデオ ストリームを IoT Edge デバイスで使用する方法について説明します。

これらの手順では、[Live555 Media Server](http://www.live555.com/mediaServer/) を RTSP シミュレーターとして Docker コンテナーで使用しています。

> [!NOTE]
> このリポジトリのサードパーティ製ソフトウェアについて言及していますが、これはあくまで情報提供および利便性を目的としたものです。 サードパーティ製ソフトウェアに関して、Microsoft は公式にサポートしているわけではなく、また権利を提供するものでもありません。 詳細については、[Live555 Media Server](http://www.live555.com/mediaServer/) に関するページを参照してください。

IoT Edge VM で次のコマンドを使用して、Docker コンテナーの **rtspvideo** ユーティリティを実行します。 Docker コンテナーによってバックグラウンド RTSP ストリームが作成されます。

```bash
sudo docker run -d --name live555 --rm -p 554:554 mcr.microsoft.com/lva-utilities/rtspsim-live555:1.2
```

次のコマンドを使用して、Docker コンテナーを一覧表示します。

```bash
sudo docker ps
```

一覧には、**live555** というコンテナーが含まれています。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

アプリケーションを使い終えたら、次の手順で、作成したリソースをすべて削除することができます。

1. IoT Central アプリケーションの **[管理]** セクションの **[お客様のアプリケーション]** ページに移動します。 次に、 **[削除]** を選択します。
1. Azure portal で、**lva-rg** リソース グループを削除します。
1. ローカル コンピューターで、**amp-viewer** Docker コンテナーを停止します。

## <a name="next-steps"></a>次のステップ

Azure 上で実行されている Linux VM に、IoT Edge ランタイム、LVA モジュール、Live555 シミュレーション ストリームをデプロイしました。

カメラを管理する方法については、次のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [ビデオ分析 (物体とモーションの検出) アプリケーションを監視、管理する](./tutorial-video-analytics-manage.md)