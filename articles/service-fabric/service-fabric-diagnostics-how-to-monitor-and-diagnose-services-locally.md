---
title: "Windows で Azure マイクロサービスをデバッグする | Microsoft Docs"
description: "ローカルの開発用コンピューターで Microsoft Azure Service Fabric を使用して作成されたサービスを監視および診断する方法について説明します。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: edcc0631-ed2d-45a3-851d-2c4fa0f4a326
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2017
ms.author: dekapur
translationtype: Human Translation
ms.sourcegitcommit: cf8f717d5343ae27faefdc10f81b4feaccaa53b9
ms.openlocfilehash: 5421cf66449892bb7bbc46cd8727a0642b7d66f3
ms.lasthandoff: 01/24/2017


---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>ローカル コンピューターの開発のセットアップでのサービスの監視と診断
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
> 
> 

監視、検出、診断、トラブルシューティングにより、ユーザー エクスペリエンスの中断を最小限に抑えてサービスを続行できます。 監視と診断は実際にデプロイされる運用環境で不可欠ですが、実際にセットアップに移るときに動作するように、サービスを開発している間に同様のモデルを採用するかどうかで、その有効性が左右されます。 Service Fabric により、サービスの開発者は、1 台のコンピューターのローカルの開発のセットアップと実際の運用クラスターのセットアップの両方でシームレスに操作できる診断を簡単に実装できます。

## <a name="the-benefits-of-event-tracing-for-windows"></a>Windows イベント トレーシングの利点
[Event Tracing for Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) では、Service Fabric のメッセージをトレースするための推奨されるテクノロジです。 その理由は、次のとおりです。

* **ETW は、高速です。** コードの実行時間にほとんど影響を与えない追跡テクノロジとして構成されています。
* **ETW トレースは、ローカル開発環境から実際のクラスターのセットアップに至るまでシームレスに機能します。** つまり、コードを実際のクラスターにデプロイする準備ができたら、トレース コードを書き直す必要はありません。
* **Service Fabric のシステム コードも内部のトレースに ETW を使用します。** これを使用すると、アプリケーションのトレースと Service Fabric のシステム トレースを交互に配置した状態で表示できます。 また、アプリケーションのコードと基盤となるシステムのイベントの間の順序と相互関係が理解しやすくなります。
* **ETW イベントを表示する Service Fabric のVisual Studio ツールが内蔵でサポートされます。**

## <a name="view-service-fabric-system-events-in-visual-studio"></a>Visual Studio で Service Fabric のシステム イベントを表示する
Service Fabric は ETW イベントを出力して、アプリケーションの開発者が、プラットフォームで起こっていることを理解できるようにします。 まだ実行していない場合は、 [Visual Studio での初めてのアプリケーション作成](service-fabric-create-your-first-application-in-visual-studio.md)に関するページの手順に従ってください。 この情報は、アプリケーションを起動して実行し、診断イベント ビューアーにトレース メッセージを表示するのに役立ちます。

1. 診断イベント ウィンドウが自動的に表示されない場合は、Visual Studio の **[表示]** タブに移動し、**[その他のウィンドウ]**、**[診断イベント ビューアー]** の順に選択します。
2. 各イベントには、イベントの送信元のノード、アプリケーション、およびサービスを示す標準的なメタデータ情報があります。 イベント ウィンドウの上部にある **[イベントのフィルター処理]** ボックスを使用して、イベントの一覧をフィルター処理することもできます。 たとえば、**[ノード名]** または **[サービス名]** でフィルター処理することができます。 また、イベントの詳細を確認する際には、イベント ウィンドウの上部にある **[一時停止]** ボタンを使用して一時停止すると、イベントが失われることなく後で再開できます。
   
   ![Visual Studio の診断イベント ビューアー](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

## <a name="add-your-own-custom-traces-to-the-application-code"></a>アプリケーション コードの独自のカスタム トレースを追加する
Service Fabric の Visual Studio プロジェクト テンプレートには、サンプル コードが含まれています。 コードは、Visual Studio ETW ビューアーに表示されるカスタム アプリケーション コードの ETW トレースと Service Fabric からのシステム トレースを追加する方法を示しています。 このメソッドの利点は、メタデータがトレースに自動的に追加され、Visual Studio の診断イベント ビューアーがそれらを表示するよう既に構成されていることです。

**サービス テンプレート**から作成されるプロジェクト (ステートレスまたはステートフル) の場合は、単に `RunAsync` の実装を検索します:

1. `ServiceEventSource.Current.ServiceMessage` in the `RunAsync` を呼び出すと、アプリケーション コードからのカスタムの ETW トレースの例が表示されます。
2. **ServiceEventSource.cs** ファイルには、パフォーマンス上の理由により頻度の高いイベントに使用する必要がある、`ServiceEventSource.ServiceMessage` メソッドのオーバーロードが見つかります。

**アクター テンプレート** から作成されるプロジェクトの場合 (ステートレスまたはステートフル):

1. **"ProjectName".cs** ファイルを開きます。*ProjectName* は、Visual Studio プロジェクト用に選択した名前です。  
2. *DoWorkAsync* メソッドでコード `ActorEventSource.Current.ActorMessage(this, "Doing Work");` を検索します。  これは、アプリケーション コードからのカスタムの ETW トレースの例です。  
3. **ActorEventSource.cs** ファイルには、パフォーマンス上の理由により頻度の高いイベントに使用する必要がある、`ActorEventSource.ActorMessage` メソッドのオーバーロードが見つかります。

カスタムの ETW トレースをサービスのコードに追加した後で、もう一度アプリケーションをビルド、デプロイ、および実行して、診断イベント ビューアーでイベントを表示することができます。 **F5**キーを使用してアプリケーションをデバッグすると、診断イベント ビューアーが自動的に開きます。

## <a name="next-steps"></a>次のステップ
ローカルの診断のために上記でアプリケーションに追加したのと同じトレース コードは、Azure のクラスターでアプリケーションを実行したときにこれらのイベントを表示するために使用できるツールでも動作します。 ツールの各オプションや、その設定方法について説明した記事を参照してください。

* [Azure 診断でログを収集する方法](service-fabric-diagnostics-how-to-setup-wad.md)
* [サービス プロセスからログを直接収集する](service-fabric-diagnostic-collect-logs-without-an-agent.md)


