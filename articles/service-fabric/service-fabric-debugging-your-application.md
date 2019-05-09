---
title: Visual Studio でアプリケーションをデバッグする | Microsoft Docs
description: Visual Studio とローカル開発クラスターを使用してサービスを開発、デバッグし、サービスの信頼性とパフォーマンスを改善します。
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: cb888532-bcdb-4e47-95e4-bfbb1f644da4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 9fbd9b8e298713dad022196989027f9e43ce806d
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667737"
---
# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>Visual Studio による Service Fabric アプリケーションのデバッグ
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
>


## <a name="debug-a-local-service-fabric-application"></a>ローカルの Service Fabric アプリケーションをデバッグする
ローカル コンピューターの開発クラスターで Azure Service Fabric アプリケーションをデプロイしデバッグすることにより、時間と費用を節約できます。 Visual Studio 2017 または Visual Studio 2015 では、アプリケーションをローカル クラスターにデプロイし、アプリケーションのすべてのインスタンスにデバッガーを自動的に接続できます。デバッガーを接続するためには Visual Studio が管理者として実行されている必要があります。

1. 「 [Service Fabric 開発環境の設定](service-fabric-get-started.md)」にある手順に従って、ローカル開発クラスターを開始してください。
2. **F5** キーを押すか、**[デバッグ]** > **[デバッグの開始]** の順にクリックします
   
    ![アプリケーションのデバッグを開始する][startdebugging]
3. コードにブレークポイントを設定し、 **[デバッグ]** メニューのコマンドをクリックしてアプリケーションをステップ実行します。
   
   > [!NOTE]
   > Visual Studio は、アプリケーションのすべてのインスタンスにアタッチします。 コードのステップ実行中に、ブレークポイントに複数のプロセスがヒットして、同時セッションになる場合があります。 ヒットしたブレークポイントを無効にし、ブレークポイントをスレッド ID の条件付きにするか、診断イベントを使用します。
   > 
   > 
4. **[診断イベント]** ウィンドウが自動的に開き、診断イベントをリアルタイムで表示します。
   
    ![リアルタイムに診断イベントを表示する][diagnosticevents]
5. **[診断イベント]** ウィンドウは、Cloud Explorer で開くこともできます。  **[Service Fabric]** の下で任意のノードを右クリックし、**[ストリーミング トレースを表示する]** を選択します。
   
    ![診断イベント ウィンドウを開く][viewdiagnosticevents]
   
    特定のサービスまたはアプリケーションにトレースをフィルター処理する場合は、そのサービスまたはアプリケーションでストリーミング トレースを有効にするだけで済みます。
6. 診断イベントは自動的に生成される **ServiceEventSource.cs** でも確認でき、アプリケーション コードから呼び出されます。
   
    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```
7. **[診断イベント]** ウィンドウは、フィルター処理、一時停止、およびリアルタイムのイベント検査をサポートしています。  このフィルターは、イベント メッセージとその内容の単純な文字列検索です。
   
    ![フィルター処理、一時停止と再開、またはリアルタイムのイベント検査][diagnosticeventsactions]
8. サービスのデバッグは、その他のアプリケーションのデバッグと似ています。 通常 Visual Studio で簡単なデバッグ用のブレークポイントを設定できます。 Reliable Collections は複数のノード間で複製されますが、IEnumerable は実装されます。 これは、デバッグ中に Visual Studio の結果ビューを使用して内部保存されているものを確認できることを意味します。 コードの任意の場所にブレークポイントを設定するだけです。
   
    ![アプリケーションのデバッグを開始する][breakpoint]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>リモートの Service Fabric アプリケーションをデバッグする
Service Fabric アプリケーションを Azure の Service Fabric クラスターで実行している場合は、Visual Studio から直接これらのアプリケーションをリモートでデバッグできます。

> [!NOTE]
> この機能には [Service Fabric SDK 2.0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) と [Azure SDK for .NET 2.9](https://azure.microsoft.com/downloads/) が必要です。    
> 
> 

<!-- -->
> [!WARNING]
> リモート デバッグは開発/テスト シナリオ向けであり、運用環境向けではありません。これは、実行中のアプリケーションに影響が生じるためです。
> 
> 

1. **Cloud Explorer** で目的のクラスターを右クリックし、**[デバッグの有効化]** を選択します。
   
    ![リモート デバッグの有効化][enableremotedebugging]
   
    これにより、必要なネットワーク構成のほか、クラスター ノードでリモート デバッグ拡張機能を有効にするプロセスが開始されます。
2. **Cloud Explorer** でクラスター ノードを右クリックし、**[デバッガーのアタッチ]** を選択します。
   
    ![[デバッガーの接続]][attachdebugger]
3. **[プロセスにアタッチ]** ダイアログ ボックスで、デバッグするプロセスを選択し、**[アタッチ]** をクリックします。
   
    ![プロセスの選択][chooseprocess]
   
    アタッチするプロセスの名前は、サービス プロジェクト アセンブリ名と同じ名前です。
   
    デバッガーはプロセスを実行するすべてのノードにアタッチされます。
   
   * ステートレス サービスをデバッグする場合は、全ノード上のサービスのすべてのインスタンスがデバッグ セッションに含まれます。
   * ステートフル サービスをデバッグする場合は、パーティションのプライマリ レプリカのみがアクティブになり、デバッガーによってキャッチされます。 デバッグ セッション中にプライマリ レプリカが移動した場合でも、そのレプリカの処理は引き続きデバッグ セッションに含まれます。
   * 関連するパーティションまたは特定のサービスのインスタンスのみをキャッチするために、特定のパーティションまたはインスタンスのみを中断する条件付きブレークポイントを設定できます。
     
     ![条件付きブレークポイント][conditionalbreakpoint]
     
     > [!NOTE]
     > 現在、サービスの実行可能ファイル名が同名の複数のインスタンスを持つ Service Fabric クラスターのデバッグはサポートされていません。
     > 
     > 
4. アプリケーションのデバッグが完了したら、**Cloud Explorer** でクラスターを右クリックし、**[デバッグの無効化]** を選択して、リモート デバッグ拡張機能を無効にできます。
   
    ![リモート デバッグの無効化][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>リモート クラスター ノードからのストリーミング トレース
リモート クラスター ノードから直接 Visual Studio にトレースをストリーミングすることもできます。 この機能を使用すると、Service Fabric クラスター ノードで生成された ETW トレース イベントをストリーミングできます。

> [!NOTE]
> この機能には [Service Fabric SDK 2.0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) と [Azure SDK for .NET 2.9](https://azure.microsoft.com/downloads/) が必要です。
> この機能は、Azure で実行するクラスターのみをサポートします。
> 
> 

<!-- -->
> [!WARNING]
> ストリーミング トレースは開発/テスト シナリオ向けであり、運用環境向けではありません。これは、実行中のアプリケーションに影響が生じるためです。
> 運用環境のシナリオでは、Azure Diagnostics を使用したイベント転送に頼る必要があります。
> 
> 

1. **Cloud Explorer** で目的のクラスターを右クリックし、**[ストリーミング トレースを有効にする]** を選択します。
   
    ![リモートのストリーミング トレースの有効化][enablestreamingtraces]
   
    これにより、必要なネットワーク構成のほか、クラスター ノードでストリーミング トレース拡張機能を有効にするプロセスが開始されます。
2. **Cloud Explorer** で **[ノード]** を展開し、ストリーミング トレースを実行するノードを右クリックし、**[ストリーミング トレースを表示する]** を選択します。
   
    ![リモートのストリーミング トレースの表示][viewremotestreamingtraces]
   
    トレースを表示するノードの数だけ手順 2. を繰り返します。 各ノード ストリームは専用のウィンドウに表示されます。
   
    これで、サービスと Service Fabric によって出力されるトレースを表示できるようになりました。 特定のアプリケーションのみを表示するようにイベントをフィルター処理する場合は、フィルターにアプリケーション名を入力します。
   
    ![ストリーミング トレースの表示][viewingstreamingtraces]
3. クラスターからのストリーミング トレースが完了したら、リモートのストリーミング トレースを無効化できます。このためには、**Cloud Explorer** でクラスターを右クリックし、**[ストリーミング トレースを無効にする]** を選択します。
   
    ![リモートのストリーミング トレースの無効化][disablestreamingtraces]

## <a name="next-steps"></a>次の手順
* [Service Fabric サービスのテスト](service-fabric-testability-overview.md)。
* [Visual Studio での Service Fabric アプリケーションの管理](service-fabric-manage-application-in-visual-studio.md)。

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png
[enableremotedebugging]: ./media/service-fabric-debugging-your-application/enableremotedebugging.png
[attachdebugger]: ./media/service-fabric-debugging-your-application/attachdebugger.png
[chooseprocess]: ./media/service-fabric-debugging-your-application/chooseprocess.png
[conditionalbreakpoint]: ./media/service-fabric-debugging-your-application/conditionalbreakpoint.png
[disableremotedebugging]: ./media/service-fabric-debugging-your-application/disableremotedebugging.png
[enablestreamingtraces]: ./media/service-fabric-debugging-your-application/enablestreamingtraces.png
[viewingstreamingtraces]: ./media/service-fabric-debugging-your-application/viewingstreamingtraces.png
[viewremotestreamingtraces]: ./media/service-fabric-debugging-your-application/viewremotestreamingtraces.png
[disablestreamingtraces]: ./media/service-fabric-debugging-your-application/disablestreamingtraces.png
