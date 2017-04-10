---
title: "初めての Azure マイクロサービス アプリケーションの作成 | Microsoft Docs"
description: "Visual Studio を使用して Service Fabric アプリケーションを作成、デプロイ、デバッグする"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: c3655b7b-de78-4eac-99eb-012f8e042109
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/07/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 296f02dd7deb22fd4ca15478b7f90a7688b4304a
ms.lasthandoff: 03/08/2017


---
# <a name="create-your-first-azure-service-fabric-application"></a>最初の Azure Service Fabric アプリケーションを作成する
> [!div class="op_single_selector"]
> * [C# - Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java - Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
> 
> 

Service Fabric SDK には、Service Fabric アプリケーションの作成、デプロイ、およびデバッグのためのテンプレートとツールを提供する Visual Studio 用アドインが含まれます。 このトピックでは、Visual Studio 2017 または Visual Studio 2015 で初めてのアプリケーションを作成するプロセスについて説明します。

## <a name="prerequisites"></a>前提条件
作業を開始する前に、 [開発環境がセットアップ](service-fabric-get-started.md)されていることを確認してください。

## <a name="video-walkthrough"></a>ビデオ チュートリアル
次のビデオでは、このチュートリアルの手順について説明します。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Creating-your-first-Service-Fabric-application-in-Visual-Studio/player]
> 
> 

## <a name="create-the-application"></a>アプリケーションを作成する
Service Fabric のアプリケーションには、アプリケーションの機能を提供する際にそれぞれ特定の役割を果たすサービスを&1; つ以上含めることができます。 新しいプロジェクト ウィザードを使用して、アプリケーション プロジェクトと共に最初のサービス プロジェクトを作成します。 後で必要に応じてサービスを追加することもできます。

1. Visual Studio を管理者として起動します。
2. **[ファイル]、[新しいプロジェクト]、[クラウド]、[Service Fabric アプリケーション]** の順にクリックします。
3. アプリケーションに名前を付けて、 **[OK]**をクリックします。
   
    ![Visual Studio の [新しいプロジェクト] ダイアログ][1]
4. 次のページで、アプリケーションに最初に追加するサービスの種類として **[ステートフル]** を選択します。 それに名前を付けて、 **[OK]**をクリックします。
   
    ![Visual Studio の [新しいサービス] ダイアログ][2]
   
   > [!NOTE]
   > オプションの詳細については、「 [Service Fabric プログラミング モデルの概要](service-fabric-choose-framework.md)」を参照してください。
   > 
   > 
   
    Visual Studio によって、アプリケーション プロジェクトとステートフル サービス プロジェクトが作成され、ソリューション エクスプ ローラーに表示されます。
   
    ![アプリケーションとステートフル サービスの作成後に表示されるソリューション エクスプ ローラー][3]
   
    アプリケーション プロジェクトでは、コードは直接含まれません。 代わりに、一連のサービス プロジェクトが参照されます。 さらに、アプリケーション プロジェクトには、他に次の&3; つの種類のコンテンツが含まれます。
   
   * **発行プロファイル**: さまざまな環境に合わせてツールの基本設定を管理するために使用します。
   * **スクリプト**: アプリケーションをデプロイ/アップグレードするための PowerShell スクリプトが含まれます。 このスクリプトが Visual Studio によってバックグラウンドで使用されます。 このスクリプトをコマンド ラインから直接呼び出すこともできます。
   * **アプリケーション定義**: *ApplicationPackageRoot* にアプリケーション マニフェストが含まれます。 関連するアプリケーション パラメーター ファイルは *ApplicationParameters* に格納され、ここでアプリケーションが定義されます。特定の環境向けに細かくアプリケーションを構成することができます。
     
     サービス プロジェクトのコンテンツの概要については、「 [Service Fabric の Reliable Services の概要](service-fabric-reliable-services-quick-start.md)」を参照してください。

## <a name="deploy-and-debug-the-application"></a>アプリケーションをデプロイしデバッグする
これでアプリケーションの用意ができたので実行してみましょう。

1. Visual Studio で F5 キーを押して、デバッグ用にアプリケーションをデプロイします。
   
   > [!NOTE]
   > デプロイにはしばらく時間がかかります。初めての場合、Visual Studio によって開発用のローカル クラスターが作成されるためです。 ローカル クラスターでは、複数のコンピューターから成るクラスターで使用するのと同じプラットフォーム コードを&1; 台のコンピューター上でのみ実行します。 Visual Studio の出力ウィンドウにクラスターの作成状態が表示されます。
   > 
   > 
   
    クラスターの準備が整うと、SDK に含まれているローカル クラスター システム トレイ マネージャー アプリケーションから通知が表示されます。
   
    ![ローカル クラスター システム トレイ通知][4]
2. アプリケーションが起動すると、Visual Studio によって診断イベント ビューアーが自動的に呼び出されます。ここでは、サービスからトレース出力を確認することができます。
   
    ![診断イベント ビューアー][5]
   
    ステートフル サービス テンプレートの場合、表示されるメッセージに含まれるのは、MyStatefulService.cs の `RunAsync` メソッド内でインクリメントされているカウンター値のみです。
3. コードが実行されているノードなど、詳細を確認するには、イベントのいずれかを展開します。 次の場合、_Node_2 になっていますが、使用するコンピューターによって異なる可能性があります。
   
    ![診断イベント ビューアーの詳細][6]
   
    ローカル クラスターには、1 台のコンピューターでホストされている&5; つのノードが含まれています。 ノードがそれぞれ異なるコンピューター上にある&5; ノード クラスターを模倣します。 Visual Studio デバッガーの実行中にコンピューターの損失が発生するケースをシミュレートするために、ローカル クラスター上のノードのいずれかをダウンさせてみましょう。
   
   > [!NOTE]
   > プロジェクト テンプレートによって生成されたアプリケーション診断イベントでは、付属の `ServiceEventSource` クラスを使用します。 詳細については、 [サービスをローカルで監視および診断する方法](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)に関するページを参照してください。
   > 
   > 
4. StatefulService から派生したサービス プロジェクト内のクラス (たとえば、MyStatefulService) を見つけ、 `RunAsync` メソッドの最初の行にブレークポイントを設定します。
   
    ![ステートフル サービスの RunAsync メソッド内のブレークポイント][7]
5. Service Fabric Explorer を起動するには、ローカル クラスター マネージャーのシステム トレイ アプリを右クリックし、**[ローカル クラスターの管理]** を選択します。
   
    ![ローカル クラスター マネージャーから Service Fabric Explorer を起動する][systray-launch-sfx]
   
    Service Fabric Explorer ではクラスターを視覚的に表現します。これには、クラスターにデプロイされた一連のアプリケーションや、クラスターを構成する一連の物理的なノードなども含まれます。 Service Fabric Explorer の詳細については、[クラスターの視覚化](service-fabric-visualizing-your-cluster.md)に関するページを参照してください。
6. 左側のウィンドウで、**[クラスター]、[ノード]** の順に展開し、コードが実行されているノードを見つけます。
7. **[アクション]、[非アクティブにする (再起動)]** の順にクリックし、コンピューターの再起動をシミュレートします。 または、左側のウィンドウのノードのリスト ビューからノードを非アクティブ化します。
   
    ![Service Fabric Explorer でノードを停止する][sfx-stop-node]
   
    すぐに、Visual Studio にブレークポイント ヒットが表示されます。これは、1 つのノードで行っていた計算が別のノードにシームレスにフェールオーバーするためです。
8. 診断イベント ビューアーに戻り、メッセージを確認します。 イベントが実際には別のノードから取得されている場合でも、カウンターが継続的にインクリメントされています。
   
    ![フェールオーバー後の診断イベント ビューアー][diagnostic-events-viewer-detail-post-failover]

## <a name="switch-cluster-mode"></a>クラスター モードを切り替える
既定では、ローカル開発クラスターは&5; ノード クラスターとして実行するように構成されています。これは、複数のノードにデプロイされているサービスをデバッグする場合に便利です。 ただし、アプリケーションを&5; ノードの開発クラスターにデプロイするには、少し時間がかかる場合があります。 アプリを&5; ノードでは実行せず、コードの変更をすばやく繰り返したい場合は、開発クラスターを&1; ノード モードに切り替えます。 ノードが 1 つのクラスターでコードを実行するには、システム トレイでローカル クラスター マネージャーを右クリックし、**[Switch Cluster Mode (クラスター モードの切り替え)]、[1 Node (1 ノード)]** の順に選択します。  

![クラスター モードを切り替える][switch-cluster-mode]

クラスター モードを変更すると、開発クラスターがリセットされ、そのクラスターでプロビジョニングまたは実行されているすべてのアプリケーションが削除されます。

また、PowerShell を使用してクラスター モードを変更することもできます。

1. 管理者として、新しい PowerShell ウィンドウを起動します。
2. SDK フォルダーからクラスター セットアップ スクリプトを実行します。
   
    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateOneNodeCluster
    ```
   
    クラスターのセットアップには、しばらく時間がかかります。 セットアップが完了すると、次のような出力が表示されます。
   
    ![クラスターのセットアップに関する出力][cluster-setup-success-1-node]

## <a name="cleaning-up"></a>クリーンアップしています
まとめに入る前に、ローカル クラスターが現実のものであることを思い出してください。 デバッガーを停止すると、アプリケーション インスタンスが削除され、アプリケーションの種類の登録が解除されます。 ただし、クラスターは引き続きバックグラウンドで実行されます。 クラスターは、いくつかの方法で管理することができます。

1. クラスターをシャットダウンしても、アプリケーションのデータとトレースは保持するという場合は、システム トレイ アプリで **[Stop Local Cluster (ローカル クラスターの停止)]** をクリックします。
2. クラスターを完全に削除するには、システム トレイ アプリで **[Remove Local Cluster (ローカル クラスターの削除)]** をクリックします。 このオプションを使用すると、次回 Visual Studio で F5 キーを押したときにも、デプロイが遅くなります。 しばらくの間、ローカル クラスターを使用しない場合や、リソースを解放する必要がある場合にのみ、クラスターを削除してください。

## <a name="next-steps"></a>次のステップ
* [Azure でのクラスター](service-fabric-cluster-creation-via-portal.md)または [Windows でのスタンドアロン クラスター](service-fabric-cluster-creation-for-windows-server.md)を作成する方法を学びます。
* [Reliable Services](service-fabric-reliable-services-quick-start.md) または [Reliable Actors](service-fabric-reliable-actors-get-started.md) プログラミング モデルを使用してサービスを作成してみます。
* [Windows コンテナー](service-fabric-deploy-container.md)または既存のアプリを[ゲスト実行可能ファイル](service-fabric-deploy-existing-app.md)としてデプロイしてみます。
* [Web サービス フロントエンド](service-fabric-add-a-web-frontend.md)を使用してインターネットにサービスを公開する方法を学びます。
* [ハンズオン ラボ](https://msdnshared.blob.core.windows.net/media/2016/07/SF-Lab-Part-I.docx) を参照し、ステートレス サービスの作成、監視と正常性レポートの構成、アプリケーションのアップグレードを実行します。
* [Service Fabric のサポート オプション](service-fabric-support.md)について学びます。

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

