---
title: Visual Studio Code で .NET Core Azure Service Fabric アプリケーションを開発する | Microsoft Docs
description: この記事では、Visual Studio Code を使用して .NET Core Service Fabric アプリケーションをビルド、デプロイ、およびデバッグする方法について説明します。
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 60b634b0b927804249148737ee7a99c0e86dd7d6
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537776"
---
# <a name="develop-c-service-fabric-applications-with-visual-studio-code"></a>Visual Studio Code を使用して C# Service Fabric アプリケーションを開発する

[VS Code 用の Service Fabric Reliable Services 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services)を使用すると、Windows、Linux、および macOS オペレーティング システム上で .NET Core Service Fabric アプリケーションを簡単にビルドできます。

この記事では、Visual Studio Code を使用して .NET Core Service Fabric アプリケーションをビルド、デプロイ、およびデバッグする方法について説明します。

## <a name="prerequisites"></a>前提条件

この記事では、VS Code、VS Code 用の Service Fabric Reliable Services 拡張機能、および開発環境に必要な依存関係が既にインストールされていることを前提としています。 詳細については、[作業の開始](./service-fabric-get-started-vs-code.md#prerequisites)に関するセクションを参照してください。

## <a name="download-the-sample"></a>サンプルのダウンロード
この記事では、[Service Fabric .NET Core getting started サンプルの GitHub リポジトリ](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)の CounterService アプリケーションを使用しています。 

リポジトリを開発マシンに複製するには、ターミナル ウィンドウ (Windows ではコマンド ウィンドウ) から次のコマンドを実行します。

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started.git
```

## <a name="open-the-application-in-vs-code"></a>VS Code でアプリケーションを開く

### <a name="windows"></a>Windows
スタート メニューで VS Code アイコンを右クリックし、 **[管理者として実行]** を選択します。 サービスにデバッガーをアタッチするには、VS Code を管理者として実行する必要があります。

### <a name="linux"></a>Linux
ターミナルを使用して、ローカルでアプリケーションが複製されたディレクトリからパス /service-fabric-dotnet-core-getting-started/Services/CounterService に移動します。

以下のコマンドを実行して VS Code をルート ユーザーとして開き、デバッガーがサービスにアタッチできるようにします。
```
sudo code . --user-data-dir='.'
```

アプリケーションが VS Code ワークスペースに表示されます。

![ワークスペース内のカウンター サービス アプリケーション](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-application-in-workspace.png)

## <a name="build-the-application"></a>アプリケーションのビルド
1. (Ctrl + Shift + P キー) を押して VS Code で**コマンド パレット**を開きます。
2. **[Service Fabric:Build Application]\(Service Fabric: アプリケーションのビルド\)** コマンドを探して選択します。 ビルドの出力は統合ターミナルに送信されます。

   ![VS Code の [Build Application]\(アプリケーションのビルド\) コマンド](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>アプリケーションをローカル クラスターにデプロイする
アプリケーションをビルドしたら、ローカル クラスターにデプロイできます。 

1. **コマンド パレット**から **[Service Fabric:Deploy Application (Localhost)]\(Service Fabric: アプリケーションのデプロイ (Localhost)\)** コマンドを選択します。 インストール プロセスの出力は統合ターミナルに送信されます。

   ![VS Code の [Deploy Application]\(アプリケーションのデプロイ) コマンド](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-deploy-application.png)

4. デプロイが完了したら、ブラウザーを起動して Service Fabric Explorer (http:\//localhost:19080/Explorer) を開きます。 アプリケーションが実行中であることがわかります。 この処理には時間がかかる場合があります。 

   ![Service Fabric Explorer 内のカウンター サービス アプリケーション](./media/service-fabric-develop-csharp-applications-with-vs-code/sfx-verify-deploy.png)

4. アプリケーションが実行されていることを確認したら、ブラウザーを起動して http:\//localhost:31002 のページを開きます。 これはアプリケーションの Web フロントエンドです。 カウンター値は増えるので、ページを更新して最新の値を表示します。

   ![ブラウザーのカウンター サービス アプリケーション](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-running.png)

## <a name="publish-the-application-to-an-azure-service-fabric-cluster"></a>Azure Service Fabric クラスターにアプリケーションを発行する
ローカル クラスターにアプリケーションをデプロイするだけでなく、リモートの Azure Service Fabric クラスターにアプリケーションを発行することもできます。 

1. 上記の手順を使用してアプリケーションをビルドしたことを確認します。 生成された構成ファイル `Cloud.json` を、発行先となるリモート クラスターの詳細で更新します。

2. **コマンド パレット**から **Service Fabric: Publish Application\(Service Fabric: アプリケーションの発行\) コマンド**を選択します。 インストール プロセスの出力は統合ターミナルに送信されます。

   ![VS Code のアプリケーションの発行コマンド](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-publish-application.png)

3. デプロイが完了したら、ブラウザーを起動して Service Fabric Explorer を開きます (`https:<clusterurl>:19080/Explorer`)。 アプリケーションが実行中であることがわかります。 この処理には時間がかかる場合があります。 

## <a name="debug-the-application"></a>アプリケーションのデバッグ
VS Code でアプリケーションをデバッグする場合、ローカル クラスター上でアプリケーションを実行する必要があります。 ブレークポイントをコードに追加することができます。

ブレークポイントとデバッグを設定するには、次の手順を実行します。
1. エクスプローラーで */src/CounterServiceApplication/CounterService/CounterService.cs* ファイルを開き、`RunAsync` メソッド内の 62 行目にブレークポイントを設定します。
3. VS Code で**アクティビティ バー**の [デバッグ] アイコンをクリックして、デバッガー ビューを開きます。 デバッガー ビューの上部にある歯車アイコンをクリックし、ドロップダウン メニューから **[.NET Core]** を選択します。 launch.json ファイルが開きます。 このファイルを複製できます。 実行ボタン (緑色の矢印) の横にあるデバッグ設定メニューに構成の選択肢が表示されます。

   ![VS Code ワークスペースの [デバッグ] アイコン](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-icon-workspace.png)

2. デバッグの構成メニューから **[.NET Core Attach]\(.NET Core のアタッチ\)** を選択します。

   ![VS Code ワークスペースの [デバッグ] アイコン](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-start.png)

3. ブラウザーで Service Fabric Explorer (http:\//localhost:19080/Explorer) を開きます。 **[アプリケーション]** をクリックし、ドリルダウンして CounterService が実行されているプライマリ ノードを特定します。 以下の画像では、CounterService のプライマリ ノードはノード 0 です。

   ![CounterService のプライマリ ノード](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-primary-node.png)

4. VS Code で、 **[.NET Core Attach]\(.NET Core のアタッチ\)** デバッグ構成の横にある実行アイコン (緑の矢印) をクリックします。 プロセスの選択ダイアログで、手順 4 で特定したプライマリ ノード上で実行されている CounterService プロセスを選択します。

   ![プライマリ プロセス](./media/service-fabric-develop-csharp-applications-with-vs-code/select-process.png)

5. *CounterService.cs* ファイル内のブレークポイントにはすぐにヒットします。 ヒットしたら、ローカル変数の値を調べることができます。 VS Code の上部にある [デバッグ] ツールバーを使用して、実行の継続、行のステップ オーバー、メソッドへのステップ イン、現在のメソッドからのステップ アウトを行います。 

   ![値をデバッグする](./media/service-fabric-develop-csharp-applications-with-vs-code/breakpoint-hit.png)

6. デバッグ セッションを終了するには、VS Code の上部にある [デバッグ] ツールバーのプラグ アイコンをクリックします。
   
   ![デバッガーから切断する](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-bar-disconnect.png)
       
7. デバッグが完了したら、 **[Service Fabric:Remove Application]\(Service Fabric: アプリケーションの削除\)** コマンドを使用して、ローカル クラスターから CounterService アプリケーションを削除できます。 

## <a name="next-steps"></a>次の手順

* [VS Code を使用して Java Service Fabric アプリケーションを開発し、デバッグする](./service-fabric-develop-java-applications-with-vs-code.md)方法について学びます。



