---
title: "OMS を使用した Azure Service Fabric イベント分析 | Microsoft Docs"
description: "Azure Service Fabric クラスターの監視と診断に OMS を使用したイベントの視覚化と分析について説明します。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/26/2017
ms.author: dekapur
ms.translationtype: HT
ms.sourcegitcommit: 760543dc3880cb0dbe14070055b528b94cffd36b
ms.openlocfilehash: 425c7a733a0a2383f01d2122e7155d3e3a9071be
ms.contentlocale: ja-jp
ms.lasthandoff: 08/10/2017

---

# <a name="event-analysis-and-visualization-with-oms"></a>OMS を使用したイベントの分析と視覚化

Operations Management Suite (OMS) は、クラウドでホストされるアプリケーションとサービスの監視と診断に役立つ管理サービスのコレクションです。 OMS および OMS が提供するサービスの詳細については、[OMS とは](../operations-management-suite/operations-management-suite-overview.md)を参照してください

## <a name="log-analytics-and-the-oms-workspace"></a>Log Analytics と OMS ワークスペース

Log Analytics は、Azure ストレージ テーブルまたはエージェントなどの管理対象リソースからデータを収集し、中央レポジトリ内で保守します。 それらのデータは分析、アラート、および視覚化、さらにエクスポートに使用できます。 Log Analytics は、イベント、パフォーマンス データ、またはその他のカスタム データをサポートします。

OMS が構成されているときは特定の *OMS ワークスペース*にアクセスして、ダッシュボード内でデータを照会したり視覚化したりできます。

Log Analytics がデータを受け取った後、OMS にはいくつかの*管理ソリューション*が用意されています。これらは受け取るデータを監視するために事前にパッケージ化されたソリューションで、いくつかのシナリオに合わせてカスタマイズされています。 *Service Fabric Analytics* ソリューションと*コンテナー* ソリューションが含まれており、これらは Service Fabric クラスターを使用する際の診断と監視に最も関連性の高いソリューションです。 調査してみる価値のあるソリューションが他にもいくつかあり、OMS ではカスタム ソリューションを作成することもできます。詳細については[こちら](../operations-management-suite/operations-management-suite-solutions.md)をご覧ください。 クラスターに使用するために選択する各ソリューションは、同じ OMS ワークスペース内に Log Analytics と一緒に構成されます。 ワークスペースでは、カスタム ダッシュボードを作成でき、データを視覚化したり、収集、処理、および分析するデータに変更を加えたりできます。

## <a name="setting-up-an-oms-workspace-with-the-service-fabric-solution"></a>Service Fabric ソリューションによる OMS ワークスペースの設定

OMS ワークスペースに Service Fabric ソリューションを含めることをお勧めします。プラットフォームおよびアプリケーション レベルからのさまざまなログ チャネルを表示する便利なダッシュボードが用意されており、Service Fabric 固有のログを照会することもできます。 ここで、比較的単純な Service Fabric ソリューションを示します。クラスター上に単一アプリケーションが展開されます。

![OMS SF ソリューション](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-solution.png)

OMS ワークスペースをプロビジョニングして構成する方法は 2 つあります。Resource Manager テンプレートを使用する方法と、Azure Marketplace から直接行う方法です。 クラスターを展開するときは前者を使用し、診断を有効にした状態でクラスターを既に展開している場合は後者を使用します。

### <a name="deploying-oms-using-a-resource-management-template"></a>Resource Management テンプレートを使用した OMS の展開

これはクラスター作成ステージで使用します。Resource Manager テンプレートを使用してクラスターを展開すると、テンプレートが新しい OMS ワークスペースも作成し、Service Fabric ソリューションをそこに追加し、適切なストレージ テーブルからデータを読み取るように構成できます。

>[!NOTE]
>この方法が機能するには、Azure ストレージ テーブルが存在し、OMS / Log Analytics がそこから情報を読み取るために、診断が有効になっている必要があります。

[これ](https://azure.microsoft.com/resources/templates/service-fabric-oms/)は、上記のアクションを実行する、使用可能なサンプル テンプレートです。必要に応じて変更できます。 より多くの選択性が必要な場合、プロセスのどこで OMS ワークスペースを設定するかに応じて異なるオプションを提供するテンプレートがいくつかあります。[Service Fabric および OMS テンプレート](https://azure.microsoft.com/resources/templates/?term=service+fabric+OMS)で見つかります。

### <a name="deploying-oms-using-through-azure-marketplace"></a>Azure Marketplace を使用した OMS の展開

クラスターを展開した後に OMS ワークスペースを追加する場合は、Azure Marketplace に進んで *"Service Fabric Analytics"* を探します。 以下のように「Monitoring + Management \(監視 + 管理)」カテゴリ内に、リソースが 1 つだけ表示されるはずです。

![Marketplace 内の OMS SF Analytics](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

**[Create] \(作成する)** をクリックすると OMS ワークスペースを求められます。 **[Select a workspace] \(ワークスペースの選択)** をクリックしてから、**[Create a new workspace] \(新しいワークスペースを作成する)** をクリックします。 必要なエントリを入力します。ここでの要件は、Service Fabric クラスターと OMS ワークスペースのサブスクリプションが同じであることだけです。 入力が検証されると、 OMS ワークスペースは数分で展開されます。 展開中は、Service Fabric ソリューション ブレードの作成は開いたままです。 *OMS ワークスペース*の下に同じワークスペースが表示されていることを確認し、下部で **[Create] \(作成する)** をクリックすると、Service Fabric ソリューションがワークスペースに追加されます。

## <a name="using-the-oms-agent"></a>OMS エージェントの使用

EventFlow と WAD を集計ソリューションとして使用することをお勧めします。診断と監視をモジュール方式で使用できます。 たとえば、EventFlow からの出力を変更する場合は、実際のインストルメンテーションを変更する必要はなく、構成ファイルを変更するだけで済みます。 ただし、OMS を使用することを決定していて、イベント分析のために OMS を引き続き使用することを希望する場合は (使用する唯一のプラットフォームである必要はなく、少なくともプラットフォームの 1 つであればいい)、 [OMS エージェント](../log-analytics/log-analytics-windows-agents.md)を設定してみることをお勧めします。 クラスターにコンテナーをデプロイするときは、次の説明のように OMS エージェントも使用する必要があります。

設定処理は比較的簡単です。エージェントを仮想マシン スケール セット拡張機能として Resource Manager テンプレートに追加するだけです。これで各ノードに確実にインストールされます。 Service Fabric ソリューション (前述の説明を参照) を使用して OMS ワークスペースをデプロイし、エージェントをノードに追加するサンプル Resource Manager テンプレートが、[Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) または [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux) を実行するクラスター用に用意されています。

これの利点は次のとおりです。

* パフォーマンス カウンターおよびメトリック面に関するデータが豊富
* クラスターから収集されるデータを構成したり、データに変更を加えたりするのが簡単です。アプリケーションやクラスターを再展開する必要はありません。エージェントの設定への変更は OMS ワークスペースから実行でき、エージェントは自動的にリセットされます。 特定のパフォーマンス カウンターを選択するように OMS エージェントを構成するには、ワークスペースの **[Home] \(ホーム)、[Settings] \(設定)、[Data] \(データ)、[Windows Performance Counters] \(Windows パフォーマンス カウンター)** の順に移動し、収集するデータを選択します
* データが速く表示される (データは OMS / Log Analytics によって選択される前に保存される必要があるが、それより速く表示される)
* コンテナーの監視はもっと簡単です。Docker ログ(stdout、stderror) と 統計 (コンテナーおよびノード レベルに関するパフォーマンス メトリック) を選択できます

ここでの主な考慮事項は、エージェントであるため、すべてのアプリケーションと共にクラスターに展開され、クラスター上のアプリケーションのパフォーマンスに最小限の影響があることです。

## <a name="monitoring-containers"></a>コンテナーの監視

コンテナーを Service Fabric クラスターに展開するときは、クラスターが OMS エージェントで設定され、監視と診断を有効にするためにコンテナー ソリューションが OMS ワークスペースに追加されていることが推奨されます。 ここで、ワークスペース内のコンテナー ソリューションを示します。

![基本的な OMS のダッシュボード](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

エージェントはいくつかのコンテナー固有ログの収集を有効にします。これらは、OMS 内で照会したり、視覚化されたパフォーマンス インジケーターに使用したりできます。 収集されるログの種類は次のとおりです。

* ContainerInventory: コンテナーの場所、名前、およびイメージに関する情報を表示
* ContainerImageInventory: ID やサイズなど、デプロイ済みのイメージに関する情報
* ContainerLog: 特定のエラー ログ、Docker ログ (stdout など)、およびその他のエントリ
* ContainerServiceLog: 実行されている Docker デーモン コマンド
* Perf: コンテナーの CPU、メモリ、ネットワーク トラフィック、ディスク I/O、およびホスト コンピューターからのカスタム メトリックなどのパフォーマンス カウンター

この記事では、クラスターを監視するようにコンテナーを設定するために必要な手順について説明します。 OMS のコンテナー ソリューションの詳細については、ソリューションの[ドキュメント](../log-analytics/log-analytics-containers.md)を参照してください。

ワークスペース内でコンテナー ソリューションを設定するときは、上記の手順に従って、 OMS エージェントをクラスターのノードに展開していることを確認してください。 クラスターが準備できたら、コンテナーを展開します。 コンテナー イメージがクラスターに初めてデプロイされる場合には、イメージのサイズによっては、ダウンロードに数分かかることに注意してください。

Azure Marketplace で*コンテナー*を検索し、 ([Monitoring + Management] \(監視 + 管理) カテゴリの下で) コンテナー リソースを作成します。

![コンテナー ソリューションの追加](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

作成手順では、OMS ワークスペースが要求されます。 上記のデプロイで作成したワークスペースを選択します。 この手順は、OMS ワークスペース内にコンテナー ソリューションを追加し、テンプレートでデプロイされた OMS エージェントによって自動的に検出されます。 エージェントはクラスター内のコンテナー プロセスでデータの収集を開始し、 15 分以内で、上記のダッシュボードのイメージのように、ソリューションがデータとともに表示されるはずです。


## <a name="next-steps"></a>次のステップ

必要に応じてワークスペースをカスタマイズするには、次の OMS ツールとオプションを調べてください。

* オンプレミス クラスター用に、OMS はデータを OMS に送信するために使用できるゲートウェイ (HTTP 転送プロキシ) を提供します。 詳細については、[インターネットにアクセスできないコンピューターを OMS ゲートウェイを使って OMS に接続する](../log-analytics/log-analytics-oms-gateway.md)を参照してください
* OMS を構成して、検出と診断に役立つ[自動アラート](../log-analytics/log-analytics-alerts.md)を設定する
* Log Analytic の一部として提供されている[ログ検索とクエリ](../log-analytics/log-analytics-log-searches.md)機能に詳しくなる
