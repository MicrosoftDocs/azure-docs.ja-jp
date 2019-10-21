---
title: クイック スタート:Azure Service Fabric で Java アプリを作成する
description: このクイック スタートでは、Service Fabric Reliable Services サンプル アプリケーションを使用して、Azure 用の Java アプリケーションを作成します。
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/29/2019
ms.author: suhuruli
ms.custom: mvc, devcenter, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 4ef0db5f72f5849942bb043261f1166cf7c046b1
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703294"
---
# <a name="quickstart--deploy-a-java-app-to-azure-service-fabric-on-linux"></a>クイック スタート:Azure Service Fabric on Linux に Java アプリをデプロイする

このクイックスタートでは、Linux 開発者マシンで Eclipse IDE を使用して、Azure Service Fabric に初めての Java アプリケーションをデプロイする方法を示します。 最後まで読み進めていけば、Java Web フロントエンドからクラスター内のステートフルなバックエンド サービスに投票結果を保存するアプリケーションが完成します。

Azure Service Fabric は、マイクロサービスとコンテナーのデプロイと管理を行うための分散システム プラットフォームです。

![Azure Service Fabric の投票サンプル](./media/service-fabric-quickstart-java/service-fabric-voting-sample.png)

このクイックスタートでは、次の方法について説明します。

* Service Fabric Java アプリケーションのツールとして Eclipse を使用する
* アプリケーションをローカル クラスターにデプロイする
* 複数のノードにアプリケーションをスケールアウトする

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、以下が必要です。

1. [Service Fabric SDK および Service Fabric コマンド ライン インターフェイス (CLI) をインストールする](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
2. [Git をインストールする](https://git-scm.com/)
3. [Eclipse をインストールする](https://www.eclipse.org/downloads/)
4. [Java 環境をセットアップ](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development)し、オプションの手順に従って Eclipse プラグインのインストールを確実に行う

## <a name="download-the-sample"></a>サンプルのダウンロード

コマンド ウィンドウで、次のコマンドを実行して、サンプル アプリのリポジトリをローカル コンピューターに複製します。

```git
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="run-the-application-locally"></a>ローカルでアプリケーションを実行する

1. 次のコマンドを実行して、ローカル クラスターを開始します。

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    ローカル クラスターの起動には、一定の時間がかかります。 クラスターが完全に起動されたことを確認するには、 **http://localhost:19080** で Service Fabric Explorer にアクセスします。 5 つの正常なノードは、ローカル クラスターが起動され、実行されていることを示します。

    ![Azure Service Fabric Explorer が正常なノードを表示する](./media/service-fabric-quickstart-java/service-fabric-explorer-healthy-nodes.png)

2. Eclipse を開きます。
3. **[File]\(ファイル\)**  >  **[Import]\(インポート\)**  >  **[Gradle]**  >  **[Existing Gradle Project]\(既存の Gradle プロジェクト\)** の順に選択してウィザードに従います。
4. **[Directory]\(ディレクトリ\)** を選択し、GitHub から複製した `service-fabric-java-quickstart` フォルダーから `Voting` ディレクトリを選択します。 **[完了]** を選択します。

    ![Gradle プロジェクトを Eclipse にインポートする](./media/service-fabric-quickstart-java/eclipse-import-gradle-project.png)

5. これで、Eclipse のパッケージ エクスプローラーに `Voting` プロジェクトが配置されました。
6. プロジェクトを右クリックして、 **[Service Fabric]** ドロップダウンの **[Publish Application]\(アプリケーションの発行\)** を選択します。 ターゲット プロファイルとして **[PublishProfiles/Local.json]** を選択し、 **[発行]** を選択します。

    ![Azure Service Fabric がローカル JSON を発行する](./media/service-fabric-quickstart-java/service-fabric-publish-local-json.png)

7. 使い慣れた Web ブラウザーを開き、`http://localhost:8080` に接続してアプリケーションにアクセスします。

    ![Azure Service Fabric のローカル ホスト](./media/service-fabric-quickstart-java/service-fabric-local-host.png)

これで一連の投票の選択肢を追加して投票を開始できます。 アプリケーションが実行され、データはすべて Service Fabric クラスターに保存されます。別途データベースを用意する必要はありません。

## <a name="scale-applications-and-services-in-a-cluster"></a>クラスター内のアプリケーションとサービスをスケールする

サービスは、その負荷の変化に対応するために、クラスターで簡単にスケールすることができます。 サービスをスケールするには、クラスターで実行されるインスタンスの数を変更します。 サービスをスケーリングする方法は多数あり、たとえば Service Fabric CLI (sfctl) のスクリプトやコマンドを使用できます。 次の手順では、Service Fabric Explorer を使用します。

Service Fabric Explorer は、すべての Service Fabric クラスターで動作し、ブラウザーからクラスターの HTTP 管理ポート (19080) にアクセスして利用することができます (例: `http://localhost:19080`)。

Web フロントエンド サービスをスケーリングするには、以下を実行します。

1. クラスターで Service Fabric Explorer を開きます (例: `https://localhost:19080`)。
2. ツリービューで **fabric:/Voting/VotingWeb** ノードの横にある省略記号 ( **...** ) を選択し、 **[サービスのスケール]** を選択します。

    ![Azure Service Fabric でサービスのスケーリングを行う](./media/service-fabric-quickstart-java/service-fabric-scale-service.png)

    これで Web フロントエンド サービスのインスタンス数をスケールできる状態になりました。

3. 数値を **2** に変更し、 **[サービスのスケール]** を選択します。
4. ツリービューの **fabric:/Voting/VotingWeb** ノードを選択し、パーティション ノード (GUID で表されます) を展開します。

    ![Azure Service Fabric でスケーリングされたサービス](./media/service-fabric-quickstart-java/service-fabric-explorer-service-scaled.png)

    このサービスには 2 つのインスタンスがあることがわかります。また、ツリー ビューを見れば、それらのインスタンスがどのノードで実行されているかが確認できます。

この簡単な管理タスクを通じて、フロントエンド サービスでユーザー負荷を処理するためのリソースが 2 倍になりました。 実行するサービスの信頼性を高めるために、サービスのインスタンスを複数用意する必要はないことに注目してください。 サービスで障害が発生した場合、Service Fabric によって新しいサービス インスタンスがクラスターで実行されます。

## <a name="next-steps"></a>次の手順

このクイック スタートでは、次の方法について説明しました。

* Service Fabric Java アプリケーションのツールとして Eclipse を使用する
* Java アプリケーションをローカル クラスターにデプロイする
* 複数のノードにアプリケーションをスケールアウトする

Service Fabric で Java アプリを操作する方法を学習するには、Java アプリのチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Java アプリのデプロイ](./service-fabric-tutorial-create-java-app.md)
