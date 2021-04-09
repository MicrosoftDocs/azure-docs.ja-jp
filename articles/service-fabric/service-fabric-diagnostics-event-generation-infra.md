---
title: Azure Service Fabric のプラットフォーム レベルの監視
description: Azure Service Fabric クラスターの監視と診断に使用するプラットフォーム レベルのイベントとログについて説明します。
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: f9db0df9370197190c11b740f1fd1af3fb69f19c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100581405"
---
# <a name="monitoring-the-cluster"></a>クラスターの監視

ハードウェアとクラスターが予想どおりに動作しているかどうかを確認するために、クラスター レベルで監視することが重要です。 Service Fabric では、ハードウェアの障害時にもアプリケーションを実行し続けることができますが、エラーがアプリケーションと基になるインフラストラクチャのどちらで発生しているのかを診断する必要があります。 また、ハードウェアの追加や削除を決定する際に役立つ適切な容量計画を作成するために、クラスターを監視する必要があります。

Service Fabric は、EventStore や標準で提供されるさまざまなログ チャネルを通じて、構造化されたプラットフォーム イベントを [Service Fabric イベント](service-fabric-diagnostics-events.md)としていくつか公開しています。 

Windows では、Service Fabric イベントは、稼働およびデータのチャネルとメッセージング チャネルの選択に使用される、一連の関連する `logLevelKeywordFilters` を持つ単一の ETW プロバイダーから提供されます。これが、発信される Service Fabric イベントを必要に応じて取り出してフィルター処理する方法となっています。

* **稼働** Service Fabric とクラスターで実行される高度な操作。ノードの起動、新しいアプリケーションのデプロイ、アップグレードのロールバックなどのイベントが含まれます。すべてのイベント一覧については、[こちら](service-fabric-diagnostics-event-generation-operational.md)を参照してください。  

* **運用時 - 詳細**  
正常性レポートおよび負荷分散の判定。

運用チャネルには、ETW/Windows イベント ログ、[EventStore](service-fabric-diagnostics-eventstore.md) (Windows クラスターに対してバージョン 6.2 以降の Windows で利用可能) など、さまざまな方法でアクセスできます。 EventStore は、エンティティごとの単位 (クラスター、ノード、アプリケーション、サービス、パーティション、レプリカ、コンテナーを含むエンティティ) でクラスターのイベントへのアクセスをユーザーに提供し、REST API と Service Fabric クライアント ライブラリを介してそれらを公開します。 開発/テスト クラスターを監視して、運用クラスターの状態について特定時点の情報を取得するためには、EventStore を使用します。

* **データおよびメッセージング**  
メッセージング (現在は ReverseProxy のみ) やデータ パス (Reliable Services モデル) で生成された重要なログおよびイベント。

* **データおよびメッセージング - 詳細**  
クラスター内のデータおよびメッセージングからのすべての重要でないログを含む詳細チャンネル (このチャンネルには非常に大量のイベントが含まれます)。

これらに加えて、2 つの構造化された EventSource チャネルと、サポートを目的として収集するログが提供されています。

* [Reliable Services のイベント](service-fabric-reliable-services-diagnostics.md)  
プログラミング モデル固有のイベント。

* [Reliable Actors のイベント](service-fabric-reliable-actors-diagnostics.md)  
プログラミング モデル固有のイベントとパフォーマンス カウンター。

* サポート ログ  
サポートを提供するときに Microsoft でのみ使用される Service Fabric によって生成されたシステム ログ。

これらの各種チャネルは、推奨されるプラットフォーム レベルのほとんどのログに対応します。 プラットフォーム レベルのログを向上させるには、正常性モデルをよく理解することに努め、カスタム正常性レポートを追加し、カスタム **パフォーマンス カウンター** を追加することを検討し、サービスやアプリケーションがクラスターに及ぼす影響をリアルタイムで把握できるようにします。

これらのログを利用するために、Azure portal でクラスターの作成中に [診断] を有効にしておくことを強くお勧めします。 診断をオンにすることで、クラスターをデプロイしたときに、Microsoft Azure Diagnostics が、稼働、Reliable Services、Reliable Actors の各チャネルを認識し、データを保存できるようになります。詳細については、「[Windows Azure Diagnostics を使用したイベントの集計と収集](service-fabric-diagnostics-event-aggregation-wad.md)」を参照してください。

## <a name="azure-service-fabric-health-and-load-reporting"></a>Azure Service Fabric の正常性と負荷のレポート

Service Fabric には、次の記事で詳述する独自の正常性モデルがあります。

- [Service Fabric 正常性監視の概要](service-fabric-health-introduction.md)
- [サービス正常性のレポートとチェック](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Service Fabric のカスタム正常性レポートの追加](service-fabric-report-health.md)
- [Service Fabric の正常性レポートの確認](service-fabric-view-entities-aggregated-health.md)

正常性の監視は、サービス運用のさまざまな側面 (特にアプリケーションのアップグレード時) に重要です。 サービスの各アップグレード ドメインがアップグレードされた後、デプロイが次のアップグレード ドメインに移る前に、現在のアップグレード ドメインが正常性チェックに合格する必要があります。 正常な状態を実現できない場合、デプロイがロールバックされ、アプリケーションは既知の正常な状態に維持されます。 サービスがロールバックされる前に一部の顧客が影響を受ける場合もありますが、ほとんどの顧客では問題が発生することはありません。 また、人間のオペレーターによる操作を待たなくても、比較的速やかに問題が解決されます。 コードに組み込まれている正常性チェックが増えるほど、デプロイの問題に対するサービスの耐性が向上します。

サービスの正常性のもう 1 つの側面は、サービスからのメトリックのレポートです。 メトリックはリソース使用量のバランスを取るために使用されるため、Service Fabric において重要です。 また、メトリックはシステムの正常性を示すインジケーターにもなります。 たとえば、多数のサービスを使用するアプリケーションがあり、各インスタンスから 1 秒あたりの要求数 (RPS) メトリックが報告されているとします。 あるサービスが別のサービスよりも多くのリソースを使用している場合、Service Fabric はクラスター内でサービス インスタンスを移動させ、リソース使用率を均等に維持することを試みます。 リソース使用率のしくみの詳細については、「[Service Fabric のリソース使用量と負荷をメトリックで管理する](service-fabric-cluster-resource-manager-metrics.md)」をご覧ください。

メトリックにより、サービスの実行状況に関する知見も得られます。 長期間にわたり、メトリックを使用して、サービスが求められているパラメーターの範囲内で動作していることを確認することもできます。 たとえば、月曜日の午前 9 時の時点での平均 RPS が 1,000 であることを傾向が示している場合、RPS が 500 を下回るか、1,500 を超えたら通知する正常性レポートを設定できます。 すべてがまったく問題ない場合もありますが、顧客の優れたエクスペリエンスを確保するうえで、正常性レポートは一見の価値があります。 サービスでは、正常性チェックのために報告対象にすることができ、クラスターのリソースのバランスには影響を与えない一連のメトリックを定義できます。 これを行うには、メトリックの重みを 0 に設定します。 どのメトリックも最初は重みを 0 にし、メトリックの重み付けがクラスターのリソースのバランスに及ぼす影響を確実に理解できるまで重みを増やさないようにすることをお勧めします。

> [!TIP]
> 重み付けされたメトリックを使いすぎないようにしてください。 バランスを取るためにサービス インスタンスが移動されている理由を理解しにくくなる可能性があります。 少数のメトリックでも十分に役立ちます。

アプリケーションの正常性とパフォーマンスを示すことができる情報が、メトリックと正常性レポートの候補となります。 **CPU パフォーマンス カウンターではノードの使用状況がわかりますが、1 つのノードで複数のサービスが実行されている場合があるため、特定のサービスが正常かどうかを示すわけではありません。** しかし、RPS、処理された項目数、要求の待機時間などのメトリックは、いずれも特定のサービスの正常性を示すことができます。

## <a name="service-fabric-support-logs"></a>Service Fabric のサポート ログ

Azure Service Fabric クラスターに関する支援を得るために Microsoft サポートに連絡する必要があるときは、ほとんどの場合、サポート ログが必要となります。 クラスターが Azure 内でホストされている場合は、クラスターの作成の一環として、サポート ログが自動的に構成され、収集されます。 ログは、クラスターのリソース グループにある専用のストレージ アカウントに保存されます。 このストレージ アカウントに固定名はありませんが、アカウント内に名前が *fabric* で始まる BLOB コンテナーとテーブルがあります。 スタンドアロン クラスターのログ収集の設定については、[スタンドアロン Azure Service Fabric クラスターの作成と管理](service-fabric-cluster-creation-for-windows-server.md)に関する記事および「[スタンドアロン Windows クラスターの構成設定](service-fabric-cluster-manifest.md)」をご覧ください。 スタンドアロン Service Fabric インスタンスの場合、ログをローカルのファイル共有に送信する必要があります。 これらのログは、サポートを受けるために **必須** となりますが、Microsoft カスタマー サポート チーム以外の人が使用するためのものではありません。

## <a name="measuring-performance"></a>パフォーマンスの測定

クラスターのパフォーマンスを測定すると、クラスターが負荷をどのように処理できるのかを理解しやすくなり、クラスターのスケーリングに関する意思決定が促進されます ([Azure](service-fabric-cluster-scale-in-out.md) または[オンプレミス](service-fabric-cluster-windows-server-add-remove-nodes.md)のクラスターのスケーリングの詳細を参照してください)。 また、パフォーマンス データは、今後ログを分析したときに、開発者またはアプリケーションやサービスによって実行された可能性のあるアクションと比較する際にも役立ちます。 

Service Fabric の使用時に収集されるパフォーマンス カウンターの一覧については、[Service Fabric のパフォーマンス カウンター](service-fabric-diagnostics-event-generation-perf.md)に関する記事をご覧ください。

クラスターのパフォーマンス データの収集は、次の 2 とおりの方法で設定できます。

* **エージェントの使用**  
エージェントは通常、収集可能なパフォーマンス メトリックの一覧を保持しており、ユーザーが収集または変更するメトリックを選択することは比較的簡単なプロセスであるため、これはマシンからパフォーマンスを収集するための推奨される方法です。 Azure Monitor ログを提供している Azure Monitor の詳細については、Service Fabric の [Azure Monitor ログの統合](service-fabric-diagnostics-event-analysis-oms.md)に関する記事と [Log Analytics エージェントの設定](../azure-monitor/agents/agent-windows.md)に関する記事を参照してください。Log Analytics エージェントは、クラスター VM やデプロイ済みのコンテナーのパフォーマンス データを取得できる監視エージェントです。

* **Azure Table Storage に対するパフォーマンス カウンター**  
パフォーマンス メトリックは、イベントと同じテーブル ストレージに送信することもできます。 この場合、クラスター内の VM から適切なパフォーマンス カウンターを取得するように Azure Diagnostics の構成を変更し、コンテナーをデプロイする場合は Azure Diagnostics が Docker の統計を取得できるようにします。 パフォーマンス カウンターの収集を設定する方法については、Service Fabric での [WAD のパフォーマンス カウンター](service-fabric-diagnostics-event-aggregation-wad.md)の構成に関する記事をご覧ください。

## <a name="next-steps"></a>次のステップ

* Service Fabric の [Azure Monitor ログの統合](service-fabric-diagnostics-event-analysis-oms.md)に関する記事を参照して、クラスター診断を収集し、カスタム クエリとアラートを作成します
* Service Fabric の組み込みの診断エクスペリエンスについては、[EventStore](service-fabric-diagnostics-eventstore.md) に関するページを参照してください
* Service Fabric の[一般的な診断シナリオ](service-fabric-diagnostics-common-scenarios.md)をいくつか紹介します
