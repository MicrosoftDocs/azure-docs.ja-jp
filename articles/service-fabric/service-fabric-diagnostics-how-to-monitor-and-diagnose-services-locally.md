---
title: Windows で Azure Service Fabric アプリをデバッグする
description: ローカルの開発用コンピューターで Microsoft Azure Service Fabric を使用して作成されたサービスを監視および診断する方法について説明します。
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 58319b47c78a85b4f06c2c834db20f6c42cc1939
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "86247423"
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>ローカル コンピューターの開発のセットアップでのサービスの監視と診断
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
> 
> 

監視、検出、診断、トラブルシューティングにより、ユーザー エクスペリエンスの中断を最小限に抑えてサービスを続行できます。 監視と診断は実際にデプロイされる運用環境で不可欠ですが、実際にセットアップに移るときに動作するように、サービスを開発している間に同様のモデルを採用するかどうかで、その有効性が左右されます。 Service Fabric により、サービスの開発者は、1 台のコンピューターのローカルの開発のセットアップと実際の運用クラスターのセットアップの両方でシームレスに操作できる診断を簡単に実装できます。

## <a name="event-tracing-for-windows"></a>Windows イベント トレーシング
[Event Tracing for Windows](/windows/win32/etw/event-tracing-portal) (ETW) では、Service Fabric のメッセージをトレースするための推奨されるテクノロジです。 ETW を使用する利点は次のとおりです。

* **ETW は、高速です。** コードの実行時間にほとんど影響を与えない追跡テクノロジとして構成されています。
* **ETW トレースは、ローカル開発環境から実際のクラスターのセットアップに至るまでシームレスに機能します。** つまり、コードを実際のクラスターにデプロイする準備ができたら、トレース コードを書き直す必要はありません。
* **Service Fabric のシステム コードも内部のトレースに ETW を使用します。** これを使用すると、アプリケーションのトレースと Service Fabric のシステム トレースを交互に配置した状態で表示できます。 また、アプリケーションのコードと基盤となるシステムのイベントの間の順序と相互関係が理解しやすくなります。
* **ETW イベントを表示する Service Fabric のVisual Studio ツールが内蔵でサポートされます。** ETW イベントは、Service Fabric で Visual Studio が正しく構成されていると、Visual Studio の診断イベント ビューで表示されます。 

## <a name="view-service-fabric-system-events-in-visual-studio"></a>Visual Studio で Service Fabric のシステム イベントを表示する
Service Fabric は ETW イベントを出力して、アプリケーションの開発者が、プラットフォームで起こっていることを理解できるようにします。 まだ実行していない場合は、 [Visual Studio での初めてのアプリケーション作成](service-fabric-tutorial-create-dotnet-app.md)に関するページの手順に従ってください。 この情報は、アプリケーションを起動して実行し、診断イベント ビューアーにトレース メッセージを表示するのに役立ちます。

1. 診断イベント ウィンドウが自動的に表示されない場合は、Visual Studio の **[表示]** タブに移動し、 **[その他のウィンドウ]** 、 **[診断イベント ビューアー]** の順に選択します。
2. 各イベントには、イベントの送信元のノード、アプリケーション、およびサービスを示す標準的なメタデータ情報があります。 イベント ウィンドウの上部にある **[イベントのフィルター処理]** ボックスを使用して、イベントの一覧をフィルター処理することもできます。 たとえば、 **[ノード名]** または **[サービス名]** でフィルター処理することができます。 また、イベントの詳細を確認する際には、イベント ウィンドウの上部にある **[一時停止]** ボタンを使用して一時停止すると、イベントが失われることなく後で再開できます。
   
   ![Visual Studio の診断イベント ビューアー](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

## <a name="add-your-own-custom-traces-to-the-application-code"></a>アプリケーション コードの独自のカスタム トレースを追加する
Service Fabric の Visual Studio プロジェクト テンプレートには、サンプル コードが含まれています。 コードは、Visual Studio ETW ビューアーに表示されるカスタム アプリケーション コードの ETW トレースと Service Fabric からのシステム トレースを追加する方法を示しています。 このメソッドの利点は、メタデータがトレースに自動的に追加され、Visual Studio の診断イベント ビューアーがそれらを表示するよう既に構成されていることです。

**サービス テンプレート** から作成されるプロジェクト (ステートレスまたはステートフル) の場合は、単に `RunAsync` の実装を検索します:

1. `ServiceEventSource.Current.ServiceMessage` in the `RunAsync` を呼び出すと、アプリケーション コードからのカスタムの ETW トレースの例が表示されます。
2. **ServiceEventSource.cs** ファイルには、パフォーマンス上の理由により頻度の高いイベントに使用する必要がある、`ServiceEventSource.ServiceMessage` メソッドのオーバーロードが見つかります。

**アクター テンプレート** から作成されるプロジェクトの場合 (ステートレスまたはステートフル):

1. **"ProjectName".cs** ファイルを開きます。*ProjectName* は、Visual Studio プロジェクト用に選択した名前です。  
2. *DoWorkAsync* メソッドでコード `ActorEventSource.Current.ActorMessage(this, "Doing Work");` を検索します。  これは、アプリケーション コードからのカスタムの ETW トレースの例です。  
3. **ActorEventSource.cs** ファイルには、パフォーマンス上の理由により頻度の高いイベントに使用する必要がある、`ActorEventSource.ActorMessage` メソッドのオーバーロードが見つかります。

カスタムの ETW トレースをサービスのコードに追加した後で、もう一度アプリケーションをビルド、デプロイ、および実行して、診断イベント ビューアーでイベントを表示することができます。 **F5** キーを使用してアプリケーションをデバッグすると、診断イベント ビューアーが自動的に開きます。

## <a name="next-steps"></a>次のステップ
ローカルの診断のために上記でアプリケーションに追加したのと同じトレース コードは、Azure のクラスターでアプリケーションを実行したときにこれらのイベントを表示するために使用できるツールでも動作します。 ツールの各オプションや、その設定方法について説明した記事を参照してください。

* [Azure Diagnostics でログを収集する方法](./service-fabric-diagnostics-event-aggregation-wad.md)
* [EventFlow を使用したイベントの集計と収集](service-fabric-diagnostics-event-aggregation-eventflow.md)
