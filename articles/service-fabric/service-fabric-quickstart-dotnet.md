---
title: "Azure に .NET Service Fabric アプリケーションを作成する | Microsoft Docs"
description: "Service Fabric のクイック スタート サンプルを使用して Azure 向けの .NET アプリケーションを作成します。"
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/09/2017
ms.author: mikhegn
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: d11b9af982112db8ba94b62110c18be843f1abb1
ms.contentlocale: ja-jp
ms.lasthandoff: 08/24/2017

---

# <a name="create-a-net-service-fabric-application-in-azure"></a>Azure に .NET Service Fabric アプリケーションを作成する
Azure Service Fabric は、スケーラブルで信頼性に優れたマイクロサービスとコンテナーのデプロイと管理を行うための分散システム プラットフォームです。 

このクイック スタートでは、初めての .NET アプリケーションを Service Fabric にデプロイする方法を紹介します。 最後まで読み進めていけば、ASP.NET Core Web フロントエンドからクラスター内のステートフルなバックエンド サービスに投票結果を保存するアプリケーションが完成します。

![アプリケーションのスクリーンショット](./media/service-fabric-quickstart-dotnet/application-screenshot.png)

このアプリケーションを通じて次の方法を説明します。
> [!div class="checklist"]
> * .NET と Service Fabric を使用してアプリケーションを作成する
> * ASP.NET Core を Web フロントエンドとして使用する
> * アプリケーション データをステートフル サービスに保存する
> * アプリケーションをローカルでデバッグする
> * アプリケーションを Azure のクラスターにデプロイする
> * 複数のノードにアプリケーションをスケールアウトする
> * アプリケーションのローリング アップグレードを実行する

## <a name="prerequisites"></a>前提条件
このクイック スタートを完了するには、以下が必要です。
1. [Visual Studio 2017 をインストール](https://www.visualstudio.com/)し、**Azure 開発**ワークロードと **ASP.NET および Web 開発**ワークロードをインストールする
2. [Git をインストールする](https://git-scm.com/)
3. [Microsoft Azure Service Fabric SDK をインストールする](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK)
4. 次のコマンドを実行して Visual Studio からローカル Service Fabric クラスターへのデプロイを有効にする
    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
    ```

## <a name="download-the-sample"></a>サンプルのダウンロード
コマンド ウィンドウで、次のコマンドを実行して、サンプル アプリのリポジトリをローカル コンピューターに複製します。
```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="run-the-application-locally"></a>ローカルでアプリケーションを実行する
スタート メニューで Visual Studio アイコンを右クリックし、**[管理者として実行]** を選択します。 サービスにデバッガーをアタッチするには、Visual Studio を管理者として実行する必要があります。

複製したリポジトリから Visual Studio ソリューション **Voting.sln** を開きます。

アプリケーションをデプロイするには、**F5** キーを押します。

> [!NOTE]
> 初めてアプリケーションを実行してデプロイすると、Visual Studio によってデバッグ用にローカル クラスターが作成されます。 この操作には、しばらく時間がかかる場合があります。 Visual Studio の出力ウィンドウにクラスターの作成状態が表示されます。

デプロイが完了したらブラウザーを起動し、`http://localhost:8080` ページ (アプリケーションの Web フロントエンド) を開きます。

![アプリケーション フロントエンド](./media/service-fabric-quickstart-dotnet/application-screenshot-new.png)

これで一連の投票の選択肢を追加して投票を開始できます。 アプリケーションが実行され、データはすべて Service Fabric クラスターに保存されます。別途データベースを用意する必要はありません。

## <a name="walk-through-the-voting-sample-application"></a>投票のサンプル アプリケーションの概要
この投票アプリケーションは次の 2 つのサービスから成ります。
- Web フロントエンド サービス (VotingWeb) - ASP.NET Core Web フロントエンド サービス。Web ページを表示すると共に、バックエンド サービスとやり取りするための Web API を公開します。
- バックエンド サービス (VotingData) - ASP.NET Core Web サービス。ディスク上に永続化された信頼性の高いディクショナリに投票結果を保存する API を公開します。

![アプリケーション ダイアグラム](./media/service-fabric-quickstart-dotnet/application-diagram.png)

アプリケーションで票を投じると、次のイベントが発生します。
1. JavaScript が Web フロントエンド サービスの Web API に HTTP PUT 要求として投票要求を送信します。

2. Web フロントエンド サービスがプロキシを使用して HTTP PUT 要求を検出し、バックエンド サービスに転送します。

3. バックエンド サービスが受信要求を受け取り、更新された結果を信頼性の高いディクショナリに保存すると、それがクラスター内の複数のノードにレプリケートされてディスク上に永続化されます。 アプリケーションのデータはすべてクラスターに保存されるため、データベースは必要ありません。

## <a name="debug-in-visual-studio"></a>Visual Studio でのデバッグ
Visual Studio でアプリケーションをデバッグするときは、ローカルの Service Fabric 開発クラスターを使用します。 デバッグのエクスペリエンスは実際のシナリオに合わせて調整することができます。 このアプリケーションでは、信頼性の高いディクショナリを使ってバックエンド サービスにデータを保存します。 既定では、デバッガーを停止すると、Visual Studio によってアプリケーションが削除されます。 アプリケーションが削除されると、バックエンド サービス内のデータも削除されます。 デバッグ セッションの終了後もデータを維持するには、Visual Studio の **Voting** プロジェクトのプロパティで、**[アプリケーション デバッグ モード]** を変更してください。

コードでどのような処理が実行されているのかを確認するには、次の手順に従います。
1. **VotesController.cs** ファイルを開き、Web API の **Put** メソッド (47 行目) にブレークポイントを設定します。このファイルは、Visual Studio のソリューション エクスプローラーで検索できます。

2. **VoteDataController.cs** ファイルを開き、この Web API の **Put** メソッド (50 行目) にブレークポイントを設定します。

3. ブラウザーに戻り、投票の選択肢をクリックするか、新しい選択肢を追加します。 Web フロントエンドの API コントローラーで 1 つ目のブレークポイントに到達します。
    - ここは、JavaScript がブラウザーからフロントエンド サービスの Web API コントローラーに要求を送信する部分です。
    
    ![投票フロントエンド サービスの追加](./media/service-fabric-quickstart-dotnet/addvote-frontend.png)

    - 最初にバックエンド サービスの ReverseProxy の URL を構築します **(1)**。
    - 次に HTTP PUT 要求を ReverseProxy に送信します **(2)**。
    - 最後にバックエンド サービスからの応答をクライアントに返します **(3)**。

4. **F5** キーを押して続行します。
    - 今度は、バックエンド サービスのブレークポイントに到達します。
    
    ![投票バックエンド サービスの追加](./media/service-fabric-quickstart-dotnet/addvote-backend.png)

    - メソッド **(1)** の先頭行では、`StateManager` を使用して信頼性の高いディクショナリ (`counts`) を取得または追加しています。
    - 信頼性の高いディクショナリ内の値とのすべてのやり取りにはトランザクションが必要です。この using ステートメント **(2)** によってトランザクションが作成されます。
    - その後、トランザクションで、投票の選択肢に関連したキーの値を更新し、操作をコミットします **(3)**。 コミット メソッドから制御が戻ると、ディクショナリ内のデータが更新され、クラスター内の他のノードにレプリケートされます。 これでデータが安全にクラスターに保存され、バックエンド サービスは、データの可用性を維持したまま他のノードにフェールオーバーすることができます。
5. **F5** キーを押して続行します。

デバッグ セッションを停止するには、**Shift + F5** キーを押します。

## <a name="deploy-the-application-to-azure"></a>Azure にアプリケーションをデプロイする
Azure 内のクラスターにアプリケーションをデプロイする場合、独自のクラスターを作成する方法と、パーティ クラスターを使用する方法とがあります。

パーティ クラスターは、Azure でホストされる無料の期間限定の Service Fabric クラスターであり、Service Fabric チームによって実行されます。このクラスターには、だれでもアプリケーションをデプロイして、プラットフォームについて学ぶことができます。 パーティ クラスターにアクセスするには、[こちらの手順を実行します](http://aka.ms/tryservicefabric)。 

独自クラスターの作成については、「[Azure で初めての Service Fabric クラスターを作成する](service-fabric-get-started-azure-cluster.md)」を参照してください。

> [!Note]
> Web フロントエンド サービスは、ポート 8080 で受信トラフィックをリッスンする構成になっています。 このポートがクラスターで開放されていることを確認してください。 パーティ クラスターを使用している場合、このポートは開放されています。
>

### <a name="deploy-the-application-using-visual-studio"></a>Visual Studio でアプリケーションをデプロイする
これでアプリケーションの準備ができたので、Visual Studio から直接クラスターにデプロイできます。

1. ソリューション エクスプローラーで **[Voting]** を右クリックして、**[発行]** を選択します。 [発行] ダイアログが表示されます。

    ![[発行] ダイアログ](./media/service-fabric-quickstart-dotnet/publish-app.png)

2. **[接続のエンドポイント]** フィールドにクラスターの接続エンドポイントを入力し、**[発行]** をクリックします。 パーティ クラスターにサインアップすると、ブラウザーに接続エンドポイントが提供されます  (例: `winh1x87d1d.westus.cloudapp.azure.com:19000`)。

3. ブラウザーを開き、クラスターのアドレスを入力します (例: `http://winh1x87d1d.westus.cloudapp.azure.com`)。 Azure のクラスターでアプリケーションが実行されていることがわかります。

![アプリケーション フロントエンド](./media/service-fabric-quickstart-dotnet/application-screenshot-new-azure.png)

## <a name="scale-applications-and-services-in-a-cluster"></a>クラスター内のアプリケーションとサービスをスケールする
Service Fabric サービスは、その負荷の変化に対応するために、クラスターで簡単にスケールすることができます。 サービスをスケールするには、クラスターで実行されるインスタンスの数を変更します。 サービスをスケールする方法は複数あり、PowerShell や Service Fabric CLI (sfctl) からスクリプトやコマンドを使用して行うことができます。 この例では、Service Fabric Explorer を使用します。

Service Fabric Explorer は、あらゆる Service Fabric クラスターで動作し、ブラウザーからクラスターの HTTP 管理ポート (19080) にアクセスして利用することができます (例: `http://winh1x87d1d.westus.cloudapp.azure.com:19080`)。

Web フロントエンド サービスをスケールするには、次の手順に従います。

1. クラスターで Service Fabric Explorer を開きます (例: `http://winh1x87d1d.westus.cloudapp.azure.com:19080`)。
2. ツリー ビューで **fabric:/Voting/VotingWeb** ノードの横にある省略記号 (3 つの点) をクリックし、**[Scale Service]\(サービスのスケール\)** を選択します。

    ![Service Fabric Explorer](./media/service-fabric-quickstart-dotnet/service-fabric-explorer-scale.png)

    これで Web フロントエンド サービスのインスタンス数をスケールできる状態になりました。

3. この数値を **2** に変更し、**[Scale Service]\(サービスのスケール\)** をクリックします。
4. ツリー ビューの **fabric:/Voting/VotingWeb** ノードをクリックし、パーティション ノード (GUID で表されます) を展開します。

    ![Service Fabric Explorer スケール サービス](./media/service-fabric-quickstart-dotnet/service-fabric-explorer-scaled-service.png)

    このサービスには 2 つのインスタンスがあることがわかります。また、ツリー ビューを見れば、それらのインスタンスがどのノードで実行されているかが確認できます。

たったこれだけの管理タスクにより、フロントエンド サービスでユーザー負荷を処理するためのリソースが 2 倍になりました。 実行するサービスの信頼性を高めるために、サービスのインスタンスを複数用意する必要はないことに注目してください。 サービスで障害が発生した場合、Service Fabric によって新しいサービス インスタンスがクラスターで実行されます。

## <a name="perform-a-rolling-application-upgrade"></a>アプリケーションのローリング アップグレードを実行する
アプリケーションに対して新しい更新プログラムをデプロイすると、その更新プログラムが Service Fabric によって安全にロールアウトされます。 ローリング アップグレードは、アップグレード中のダウンタイムが発生せず、また万一エラーが発生しても自動的にロールバックされます。

アプリケーションをアップグレードするには、次の手順に従います。

1. Visual Studio で **Index.cshtml** ファイルを開きます。このファイルは、Visual Studio のソリューション エクスプローラーで検索できます。
2. ページの見出しに何かテキストを追加して変更します。その例を次に示します。
    ```html
        <div class="col-xs-8 col-xs-offset-2 text-center">
            <h2>Service Fabric Voting Sample v2</h2>
        </div>
    ```
3. ファイルを保存します。
4. ソリューション エクスプローラーで **[Voting]** を右クリックして、**[発行]** を選択します。 [発行] ダイアログが表示されます。
5. **[マニフェストのバージョン]** ボタンをクリックしてサービスとアプリケーションのバージョンを変更します。
6. たとえば **VotingWebPkg** の **Code** 要素のバージョンを "2.0.0" に変更し、**[保存]** をクリックします。

    ![バージョンの変更ダイアログ](./media/service-fabric-quickstart-dotnet/change-version.png)
7. **[Service Fabric アプリケーションの発行]** ダイアログで、[アプリケーションをアップグレードする] チェック ボックスをオンにし、**[発行]** をクリックします。

    ![[発行] ダイアログのアップグレード設定](./media/service-fabric-quickstart-dotnet/upgrade-app.png)
8. ブラウザーを開いて、クラスターのアドレスにポート 19080 でアクセスします (例: `http://winh1x87d1d.westus.cloudapp.azure.com:19080`)。
9. ツリー ビューの **[Applications]\(アプリケーション\)** ノードをクリックし、右側のペインの **[Upgrades in Progress]\(進行中のアップグレード\)** をクリックします。 アップグレードが、クラスター内のアップグレード ドメインに展開されていくようすが表示されます。個々のドメインが正常であることを確認してから、次の手順に進んでください。
    ![Service Fabric Explorer のアップグレード ビュー](./media/service-fabric-quickstart-dotnet/upgrading.png)

    Service Fabric はアップグレードを安全に行うために、クラスター内の各ノードについて、サービスのアップグレード後、2 分間待ちます。 更新がすべて完了するまでに約 8 分かかります。

10. アップグレード中もアプリケーションを使い続けることができます。 クラスターで実行されているサービスのインスタンスは 2 つあるため、アップグレード後のアプリケーションによって処理される要求と、アップグレード前のアプリケーションによって処理される要求が混在する可能性があります。

## <a name="next-steps"></a>次のステップ
このクイック スタートでは、次の方法について説明しました。

> [!div class="checklist"]
> * .NET と Service Fabric を使用してアプリケーションを作成する
> * ASP.NET Core を Web フロントエンドとして使用する
> * アプリケーション データをステートフル サービスに保存する
> * アプリケーションをローカルでデバッグする
> * アプリケーションを Azure のクラスターにデプロイする
> * 複数のノードにアプリケーションをスケールアウトする
> * アプリケーションのローリング アップグレードを実行する

Service Fabric と .NET の詳細については、次のチュートリアルを参照してください。
> [!div class="nextstepaction"]
> [Service Fabric における .NET アプリケーション](service-fabric-tutorial-create-dotnet-app.md)
