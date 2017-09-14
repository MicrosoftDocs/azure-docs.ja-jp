---
title: "C# で Azure Service Fabric のリライアブル サービスを作成する"
description: "Visual Studio を使用して、Azure Service Fabric を基盤としたリライアブル サービス アプリケーションを作成、デプロイ、デバッグできます。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: c3655b7b-de78-4eac-99eb-012f8e042109
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/28/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: f93298e6483fd8c9dfda835964aeebd1a430af69
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---

# <a name="create-your-first-c-service-fabric-stateful-reliable-services-application"></a>C# で最初の Service Fabric ステートフル リライアブル サービス アプリケーションを作成する

Windows に初めての .NET 対応 Service Fabric アプリケーションをわずか数分でデプロイする方法について説明します。 完了すると、リライアブル サービス アプリケーションがデプロイされたローカル クラスターが稼働している状態になります。

## <a name="prerequisites"></a>前提条件

作業を開始する前に、 [開発環境がセットアップ](service-fabric-get-started.md)されていることを確認してください。 開発環境には、Service Fabric SDK と Visual Studio 2017 または 2015 のインストールが必要です。

## <a name="create-the-application"></a>アプリケーションを作成する

Visual Studio を**管理者**として起動します。

`CTRL` + `SHIFT` + `N` キーを押して、プロジェクトを作成します。

**[新しいプロジェクト]** ダイアログで、**[クラウド]、[Service Fabric アプリケーション]** の順に選択します。

アプリケーションに "**MyApplication**" という名前を付けて、**[OK]** をクリックします。

   
![Visual Studio の [新しいプロジェクト] ダイアログ][1]

次のダイアログで、任意の種類の Service Fabric アプリケーションを作成できます。 このクイックスタートでは、**[ステートフル サービス]** を選択します。

サービスに "**MyStatefulService**" という名前を付けて、**[OK]** をクリックします。

![Visual Studio の [新しいサービス] ダイアログ][2]


Visual Studio によって、アプリケーション プロジェクトとステートフル サービス プロジェクトが作成され、ソリューション エクスプ ローラーに表示されます。

![アプリケーションとステートフル サービスの作成後に表示されるソリューション エクスプ ローラー][3]

このアプリケーション プロジェクト (**MyApplication**) にコードが直接含まれることはありません。 代わりに、一連のサービス プロジェクトが参照されます。 さらに、アプリケーション プロジェクトには、他に次の 3 つの種類のコンテンツが含まれます。

* **発行プロファイル**  
さまざまな環境にデプロイするためのプロファイルです。

* **スクリプト**  
アプリケーションをデプロイ/アップグレードするための PowerShell スクリプトです。

* **アプリケーション定義**  
*ApplicationPackageRoot* に、アプリケーションの構成を定義する ApplicationManifest.xm ファイルが配置されています。 関連するアプリケーション パラメーター ファイルは *ApplicationParameters* に配置されています。このファイルを使用して、環境に合わせたパラメーターを指定できます。 環境へのデプロイ時、関連する発行プロファイルで指定されたアプリケーション パラメーター ファイルが Visual Studio によって選択されます。
    
サービス プロジェクトのコンテンツの概要については、「 [Service Fabric の Reliable Services の概要](service-fabric-reliable-services-quick-start.md)」を参照してください。

## <a name="deploy-and-debug-the-application"></a>アプリケーションをデプロイしデバッグする

これでアプリケーションの用意ができたので実行してみましょう。

Visual Studio で `F5` キーを押して、デバッグ用にアプリケーションをデプロイします。

>[!NOTE]
>初めてアプリケーションをローカルで実行してデプロイすると、Visual Studio によってデバッグ用にローカル クラスターが作成されます。 これには時間がかかる場合があります。 Visual Studio の出力ウィンドウにクラスターの作成状態が表示されます。

クラスターの準備が整うと、SDK に含まれているローカル クラスター システム トレイ マネージャー アプリケーションから通知が表示されます。
   
![ローカル クラスター システム トレイ通知][4]

アプリケーションが起動すると、Visual Studio によって**診断イベント ビューアー**が自動的に呼び出されます。ここでは、サービスからのトレース出力を確認することができます。
   
![診断イベント ビューアー][5]

このクイックスタートで使用したステートフル サービス テンプレートでは、**MyStatefulService.cs** の `RunAsync` メソッドでインクリメントされているカウンター値のみが表示されます。

コードが実行されているノードなど、詳細を確認するには、イベントのいずれかを展開します。 この例では、\_Node\_2 になっていますが、使用するマシンによって異なる可能性があります。
   
![診断イベント ビューアーの詳細][6]

ローカル クラスターには、1 台のコンピューターでホストされている 5 つのノードが含まれています。 運用環境では、ホストされているノードは物理マシンまたは仮想マシン 1 台につき 1 つだけです。 Visual Studio デバッガーの実行中にコンピューターの損失が発生するケースをシミュレートするために、ローカル クラスター上のノードのいずれかをダウンさせてみましょう。

**[ソリューション エクスプローラー]** ウィンドウで、**MyStatefulService.cs** を開きます。 

`RunAsync` メソッドを見つけ、メソッドの 1 行目にブレークポイントを設定します。

![ステートフル サービスの RunAsync メソッド内のブレークポイント][7]

**Service Fabric Explorer** ツールを起動するために、**[ローカル クラスター マネージャー]** システム トレイ アプリケーションを右クリックし、**[ローカル クラスターの管理]** を選択します。

![ローカル クラスター マネージャーから Service Fabric Explorer を起動する][systray-launch-sfx]

[**Service Fabric Explorer**](service-fabric-visualizing-your-cluster.md) では、クラスターが視覚的に表現されます。 クラスターにデプロイされたアプリケーションのセットや、クラスターを構成する物理ノードのセットも表示されます。

左側のウィンドウで、**[クラスター]、[ノード]** の順に展開し、コードが実行されているノードを見つけます。

**[アクション]、[非アクティブにする (再起動)]** の順にクリックし、コンピューターの再起動をシミュレートします。

![Service Fabric Explorer でノードを停止する][sfx-stop-node]

すぐに、Visual Studio にブレークポイント ヒットが表示されます。これは、1 つのノードで行っていた計算が別のノードにシームレスにフェールオーバーするためです。


次に、診断イベント ビューアーに戻り、メッセージを確認します。 イベントが実際には別のノードから取得されている場合でも、カウンターが継続的にインクリメントされています。

![フェールオーバー後の診断イベント ビューアー][diagnostic-events-viewer-detail-post-failover]

## <a name="cleaning-up-the-local-cluster-optional"></a>ローカル クラスターをクリーンアップする (省略可能)

このローカル クラスターは実際のローカル クラスターなので注意してください。 デバッガーを停止すると、アプリケーション インスタンスが削除され、アプリケーションの種類の登録が解除されます。 ただし、クラスターは引き続きバックグラウンドで実行されます。 ローカル クラスターを停止する準備ができたら、次のいずれかのオプションを実行します。

### <a name="keep-application-and-trace-data"></a>アプリケーションとトレース データを保持する

クラスターをシャットダウンします。具体的には、**[ローカル クラスター マネージャー]** システム トレイ アプリケーションを右クリックし、**[ローカル クラスターの停止]** を選択します。

### <a name="delete-the-cluster-and-all-data"></a>クラスターとすべてのデータを削除する

クラスターを削除します。具体的には、**[ローカル クラスター マネージャー]** システム トレイ アプリケーションを右クリックし、**[ローカル クラスターの削除]** を選択します。 

このオプションを選択すると、次にアプリケーションを実行したときに、クラスターが再デプロイされます。 しばらくの間ローカル クラスターを使用しない場合や、リソースを解放する必要がある場合に、このオプションを選択してください。

## <a name="next-steps"></a>次のステップ
Reliable Services の詳細については、[こちら](service-fabric-reliable-services-introduction.md)を参照してください。
<!-- Image References -->

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog.png
[2]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
[3]: ./media/service-fabric-create-your-first-application-in-visual-studio/solution-explorer-stateful-service-template.png
[4]: ./media/service-fabric-create-your-first-application-in-visual-studio/local-cluster-manager-notification.png
[5]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer.png
[6]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail.png
[7]: ./media/service-fabric-create-your-first-application-in-visual-studio/runasync-breakpoint.png
[sfx-stop-node]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-deactivate-node.png
[systray-launch-sfx]: ./media/service-fabric-create-your-first-application-in-visual-studio/launch-sfx.png
[diagnostic-events-viewer-detail-post-failover]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail-post-failover.png
[sfe-delete-application]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-delete-application.png
[switch-cluster-mode]: ./media/service-fabric-create-your-first-application-in-visual-studio/switch-cluster-mode.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png

