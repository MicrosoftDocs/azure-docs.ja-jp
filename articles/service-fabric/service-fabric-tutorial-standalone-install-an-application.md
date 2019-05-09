---
title: スタンドアロン Service Fabric クラスターにアプリケーションをインストールするチュートリアル - Azure Service Fabric | Microsoft Docs
description: このチュートリアルでは、スタンドアロン Service Fabric クラスターにアプリケーションをインストールする方法について説明します。
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/11/2018
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 17bb5f5d8fe7ee407caf0ea5c34dc5380dbd79b0
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670313"
---
# <a name="tutorial-deploy-an-application-on-your-service-fabric-standalone-cluster"></a>チュートリアル:Service Fabric スタンドアロン クラスターにアプリケーションをデプロイする

Service Fabric で採用されている "すべての OS、すべてのクラウド" のアプローチの一環として、Service Fabric スタンドアロン クラスターには、独自の環境を選んでクラスターを作成する選択肢が用意されています。 このチュートリアル シリーズでは、AWS をホストとするスタンドアロン クラスターを作成し、そこにアプリケーションをデプロイします。

このチュートリアルは、シリーズの第 3 部です。  Microsoft が Service Fabric で採用している "すべての OS、すべてのクラウド" のアプローチの一環として、Service Fabric スタンドアロン クラスターには、独自の環境を選んでクラスターを作成する選択肢が用意されています。 このチュートリアルでは、このスタンドアロン クラスターをホストするために必要な AWS インフラストラクチャの作成方法を紹介します。

シリーズの第 3 部で学習する内容は次のとおりです。

> [!div class="checklist"]
> * サンプル アプリのダウンロード
> * クラスターのデプロイ

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* [Visual Studio 2017 をインストール](https://www.visualstudio.com/)し、**Azure 開発**ワークロードと **ASP.NET および Web 開発**ワークロードをインストールします。
* [Service Fabric SDK をインストール](service-fabric-get-started.md)します。

## <a name="download-the-voting-sample-application"></a>投票サンプル アプリケーションをダウンロードする

コマンド ウィンドウで、次のコマンドを実行して、サンプル アプリのリポジトリをローカル コンピューターに複製します。

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

### <a name="deploy-the-app-to-the-service-fabric-cluster"></a>アプリを Service Fabric クラスターにデプロイする

ダウンロードしたアプリケーションは、Visual Studio から直接クラスターにデプロイできます。

1. Visual Studio を開きます

2. **[ファイル]** > **[開く]** を選択します。

3. Git リポジトリの複製先フォルダーに移動し、Voting.sln を選択します。

4. ソリューション エクスプローラーで `Voting` アプリケーション プロジェクトを右クリックし、**[発行]** を選択します。

5. **[接続のエンドポイント]** のドロップダウンを選択し、クラスター内のいずれかのノードのパブリック DNS 名を入力します。  たとえば、`ec2-34-215-183-77.us-west-2.compute.amazonaws.com:19000` のように指定します。

6. 任意のブラウザーを開き、クラスター アドレスを入力します。これが接続エンドポイントとなります。このアプリのデプロイ先は、ポート 8080 とします (例: ec2-34-215-183-77.us-west-2.compute.amazonaws.com:8080)。

    ![クラスターからの API 応答](./media/service-fabric-tutorial-standalone-cluster/deployed-app.png)

## <a name="next-steps"></a>次の手順

シリーズの第 3 部では、アプリケーションをクラスターにデプロイする方法について説明しました。

> [!div class="checklist"]
> * サンプル アプリのダウンロード
> * クラスターのデプロイ

シリーズの第 4 部に進んで、クラスターをクリーンアップしてください。

> [!div class="nextstepaction"]
> [リソースをクリーンアップする](service-fabric-tutorial-standalone-clean-up.md)