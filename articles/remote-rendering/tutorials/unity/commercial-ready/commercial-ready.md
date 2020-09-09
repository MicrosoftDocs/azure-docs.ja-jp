---
title: 商用の Azure Remote Rendering アプリケーションの作成
description: Azure Remote Rendering を使用して商用対応アプリケーションを作成するための戦略と考慮事項
author: FlorianBorn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.openlocfilehash: 0dad78ad76a870ea9f1db28a3cb5ccace5cd804f
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510931"
---
# <a name="tutorial-creating-a-commercial-ready-azure-remote-rendering-application"></a>チュートリアル:商用の Azure Remote Rendering アプリケーションの作成

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
>
> * 商用アプリケーションのセッション管理
> * 課金のためのセッション追跡
> * セッションの読み込み時間に関するユーザー エクスペリエンスの最適化
> * ネットワーク待ち時間に関する考慮事項

## <a name="prerequisites"></a>前提条件

* このチュートリアルは、「[チュートリアル: Azure Remote Rendering とモデル ストレージのセキュリティ保護](../security/security.md)」に基づいて作成されています。

## <a name="introduction-to-commercial-readiness"></a>商用対応の概要

Azure Remote Rendering を使用すると、複合現実の可能性が広がります。 ソリューションに基本的な要素を統合したら、ソリューションの安全性とスケーラビリティを確保し、価値を提供できるようにするために、いくつかの追加の考慮事項について検討する必要があります。

このモジュールでは、商用アプリケーションについて考慮する必要があるいくつかの追加機能を紹介します。

システム全体のアーキテクチャに関するベスト プラクティスの概要については、以下を参照してください。

* [Azure アーキテクチャ センター](https://docs.microsoft.com/azure/architecture/)
* [Azure 開発者向けファースト ステップ ガイド](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide)

## <a name="analytics"></a>Analytics

分析ツールを統合すると、ソリューションの管理、追跡、および強化に役立ちます。

利用可能な分析リソースの包括的な一覧については、次を参照してください。

* [Azure Analytics サービス](https://azure.microsoft.com/product-categories/analytics/)

### <a name="tracking-usage-for-billing"></a>課金を目的とした利用状況の追跡

複数の内部チームや外部クライアントによる Azure Remote Rendering の使用量を追跡することは、特にマルチテナントの状況において重要な考慮事項となります。

これを実現するために、Azure ではリソースのタグ付けと呼ばれるサービスを提供しています。これにより、各クライアントに Azure Remote Rendering サービスの使用量が関連付けられます。

リソースの名前付けとタグ付けの詳細については、まず次を参照してください。

* [リソースの名前付けとタグ付けの意思決定ガイド](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

### <a name="diagnostics"></a>診断

Event Trace for Windows (ETW) やイベント トレース ログ (ETL) などの強力なツールを使用すると、アプリケーション内でのトレース イベントの生成が容易になり、商用ソリューションのデプロイで発生する可能性のあるネットワーク、コンテンツのインジェスト、セッション、アプリケーションなどの問題の診断に役立てることができます。

詳細については、以下を参照してください。

* [クライアント側のパフォーマンス トレースの作成](https://docs.microsoft.com/azure/remote-rendering/how-tos/performance-tracing)
* [Event Trace for Windows (ETW) データの収集方法](https://docs.microsoft.com/visualstudio/profiling/how-to-collect-event-tracing-for-windows-etw-data)
* [Windows デバイス ポータルの使用: ログ](https://docs.microsoft.com/windows/mixed-reality/using-the-windows-device-portal)

### <a name="usage-analysis"></a>利用状況の分析

Azure Application Insights は、ユーザーが Azure Remote Rendering アプリケーションをどのように使用しているかを把握するのに役立ちます。 アプリを更新するたびに、アプリがどの程度ユーザーに役立っているかを評価できます。また、それに応じてソリューションを強化できます。 この情報により、次の開発サイクルに関してデータに基づく意思決定を行うことができます。

詳細については、以下を参照してください。

* [Application Insights による利用状況分析](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview)

## <a name="fast-startup-time-strategies"></a>起動時間を短縮するための戦略

ユース ケースによっては、アプリケーションの起動から 3D モデルの表示までを、すばやく行うことが必要になる場合があります。 たとえば、重要な会議では、事前に稼働状態にしておくことが重要です。 また別の例として、CAD の 3D モデルのレビューでは、効率を高めるために CAD アプリケーションと複合現実の間で設計の反復処理を高速に行うことが重要です。

Azure Remote Rendering には前処理済みの 3D モデルが必要であり、現在 Azure では、セッションを作成し、モデルを読み込んでレンダリングするまでに数分かかります。 このプロセスを可能な限りシームレスかつ高速に行うには、3D モデルのデータと ARR セッションを事前に準備しておく必要があります。

ここに示す機能は、現在、Standard の Azure Remote Rendering には含まれていませんが、独自に実装することで起動時間を短縮できます。

### <a name="initiate-early"></a>早い段階での開始

起動時間を短縮するための最も簡単な解決策は、ユーザー ワークフローにおいてできるだけ早くセッションの作成と初期化に移ることです。 1 つの戦略は、ARR セッションが必要であることがわかった時点で、すぐにセッションを初期化することです。 これは多くの場合、ユーザーが Azure Remote Rendering で使用するために 3D モデルを Azure Blob Storage にアップロードし始めるタイミングに該当します。 この場合、セッションの作成と初期化を、3D モデルのアップロードと同時に開始することで、両方の作業ストリームを並列で実行できます。

このプロセスをさらに合理化するには、選択した Azure Blob Storage の入力および出力コンテナーが、Azure Remote Rendering セッションと同じリージョンのデータ センターにあるようにします。

### <a name="scheduling"></a>スケジュール設定

今後 Azure Remote Rendering が必要となることがわかっている場合は、Azure Remote Rendering セッションを開始する特定の日付と時刻をスケジュールすることができます。

このオプションは、Web ポータルを通じて提供されます。そこでユーザーは、3D モデルのアップロードと、後ほどモデルを表示する時刻のスケジュール設定の両方を行うことができます。 ここでは、[*Standard*](../../../reference/vm-sizes.md) または [*Premium*](../../../reference/vm-sizes.md) のレンダリングなど、他の設定を指定することもできます。 *Premium* レンダリングは、理想的なサイズを自動的に判断することが困難なアセットの組み合わせを表示したい場合や、その指定の時刻に使用可能な VM を Azure リージョンで確保する必要がある場合に適しています。

### <a name="session-pooling"></a>セッション プール

最も要求の厳しい状況では、1 つ以上のセッションが常に作成、初期化される、セッション プールというもう 1 つのオプションを使用できます。 これにより、要求元ユーザーがすぐに使用できるようにセッション プールが作成されます。 この方法の欠点は、VM が初期化されるとサービスの課金が開始されることです。 セッション プールを常に実行したままにしておくのはコスト効果が良くないかもしれませんが、分析に基づいてピーク時の負荷を予測したり、上記のスケジュール戦略と組み合わせてセッションが必要になるタイミングを予測し、それに応じてセッション プールを増減させたりすることもできます。

また、この戦略は、*Standard* と *Premium* のセッションの選択をより動的に最適化するのに役立ちます。たとえば、まず *Premium* で複雑なモデルを表示し、続いて *Standard* 内で動作できるモデルを表示するといったように、単一のユーザー セッションで 2 つの種類をすばやく切り替えることができるためです。 このようなユーザー セッションが長時間にわたって続く場合は、コストを大幅に節約できる可能性があります。

Azure Remote Rendering のセッションの詳細については、次を参照してください。

* [Remote Rendering のセッション](https://docs.microsoft.com/azure/remote-rendering/concepts/sessions)

## <a name="standard-vs-premium-server-size-routing-strategies"></a>Standard と Premium のサーバー サイズの選択戦略

*Standard* サーバー サイズと *Premium* サーバー サイズのどちらを作成するかという選択は、ユーザー エクスペリエンスとエンド ツー エンド システムを設計する際の課題となります。 *Premium* セッションのみを使用するのも 1 つの選択肢ですが、*Standard* セッションでは使用される Azure コンピューティング リソースがはるかに少ないため、*Premium* よりも低コストになります。 このため、可能な限り *Standard* セッションを使用し、必要な場合にのみ *Premium* を使用したいという強い動機が生まれます。

ここでは、セッションの選択を管理するというニーズに対応できるように、包括性の最も低いものから最も高いものまでいくつかのオプションを紹介します。

### <a name="use-only-standard-or-premium"></a>Standard と Premium のいずれかのみの使用

ニーズが *Standard* と *Premium* の間のしきい値を "*常に*" 下回ることが確かな場合は、容易に判断できます。 *Standard* を使用してください。 ただし、読み込まれたアセットが全体として *Standard* セッションには複雑すぎるために拒否される場合は、ユーザー エクスペリエンスへの影響が大きくなることに注意してください。

同様に、使用の大部分が *Standard* と *Premium* の間のしきい値を超えることが予想される場合、またはユース ケースにおいてコストが重要な要素ではない場合は、常に *Premium* を選択することによっても単純化できます。

### <a name="ask-the-user"></a>ユーザーへの質問

*Standard* と *Premium* の両方をサポートする場合、どちらの種類のセッションをインスタンス化するかを判断する最も簡単な方法は、どのような場合に 3D アセットの表示を選択するかをユーザーに質問することです。 このアプローチの難点は、表示する 3D アセットまたは複数のアセットの複雑さをユーザーが理解している必要があるということです。 そのような理由から、通常、この方法はお勧めできません。 ユーザーが誤って *Standard* を選択した場合、タイミング悪くユーザー エクスペリエンスが悪化してしまう可能性があります。

### <a name="analyze-the-3d-model"></a>3D モデルの分析

比較的簡単な別の方法は、選択した 3D アセットの複雑さを分析することです。 モデルの複雑さが *Standard* のしきい値を下回っている場合は、*Standard* セッションを開始します。それ以外の場合は、*Premium* セッションを開始します。 この場合の難点は、最終的に 1 つのセッションを使用して複数のモデルを表示したとき、そのうちのいくつかの複雑さが *Standard* セッションのしきい値を超えている可能性があることです。そのため、一連の異なる 3D アセットに対して同じセッションをシームレスに使用することができなくなります。

### <a name="automatic-switching"></a>自動切り替え

*Standard* および *Premium* セッションの自動切り替えは、セッション プールも含まれているシステム設計では非常に道理にかなった方法です。 この戦略により、リソース使用率をさらに最適化することができます。 表示するモデルをユーザーが読み込むと、その複雑さが判別され、適切なセッション サイズがセッション プール サービスに対して要求されます。

## <a name="working-with-networks"></a>ネットワークの操作

### <a name="diagnostics"></a>診断

Azure Remote Rendering には、低遅延の高速インターネット接続が必要です。 ユーザーのネットワークの品質は、エクスペリエンスの質に大きな影響を与える可能性があります。 クライアントがさまざまなネットワーク構成を使用している可能性があり、ごくたまにネットワーク待ち時間が長くなる場合は、診断ツールが重要です。  

一貫して高品質のエクスペリエンスを提供できるようにするには、サーバー側とクライアント側で分析ツールを Azure Remote Rendering アプリケーションに統合することをお勧めします。 こうすることで、クライアントで発生している可能性のあるネットワークの問題を診断して軽減するために必要な情報を手に入れることができます。

### <a name="client-network-configurations"></a>クライアントのネットワーク構成

さまざまなエンタープライズ環境にデプロイされる堅牢なコラボレーション ソリューションを開発するうえで最大の課題の 1 つは、クライアントで使用される可能性のある各種のネットワーク トポロジとエンタープライズ ファイアウォール構成に対応できるよう準備することです。

多くの企業では、LAN 内のすべてのピアツーピア トラフィックをブロックしています。 このため、自動 LAN 検出の簡素さと効率的な UX を活かして、複合現実アプリケーションの検出されたインスタンスすべての間でローカル共有セッションを確立するのが困難になります。

その他の潜在的な障害ポイントとしては、帯域幅を意図的にスロットルするように構成されたルーターと、大部分の TCP/IP ポートをブロックするファイアウォールがあります。

不慣れなネットワークで Azure Remote Rendering を使用する予定がある場合は、常に次の方法をお勧めします。

* ネットワークの準備状態を評価するためのミーティング前チェックリストを提供します。
* 該当するリージョンのデータ センターで要求を処理できることを確認します。
* 十分に時間をかけて問題を診断します。
* 高帯域幅のデータ プランを備えたモバイル ホットスポットをバックアップとして持ち込みます。

### <a name="end-to-end-bandwidth"></a>エンド ツー エンドの帯域幅

Azure Remote Rendering VM とエンド クライアントの間に存在する可能性があるネットワークの区間ごとに、帯域幅の能力を評価することが重要です。 Azure データ センターからクライアントの ISP までのネットワーク セグメントでは、ISP からクライアントまでの制限要因を超えている可能性があることに注意してください。 このような問題の診断には、BLOB のダウンロード速度テストを使用できます。

### <a name="bandwidth-competition"></a>帯域幅の競合

複合現実アプリケーションを設計する際は、アプリのさまざまな機能と Azure Remote Rendering との間で帯域幅の競合が発生する可能性があることに注意してください。 おそらく予想外であろうと思われる例としては、1 つの部屋にいる多くの参加者全員が、同時に ARR を使用して 3D アセットを表示しようとする場合が挙げられます。 ネットワーク データ フローの各区間には、すべての ARR データ ストリームを合わせた総計を転送できるだけの容量が必要です。

他の例としては、ビデオのストリーミング、バックグラウンドでの他の関連コンテンツの同時アップロード、音声チャット (特に、多数の参加者がいて、システムの中央にオーディオ ミキシング サーバーを配置する方式ではなく、分散型のピアツーピア方式を使用している場合) などが考えられます。

ネットワーク分析の詳細については、次を参照してください。

* [Azure Storage Blob のダウンロード速度テスト](https://www.azurespeed.com/Azure/Download)
* [Azure ネットワーク ラウンド トリップ待ち時間統計](https://docs.microsoft.com/azure/networking/azure-network-latency)
* [サーバー側のパフォーマンス トレース](https://docs.microsoft.com/azure/remote-rendering/overview/features/performance-queries)
* [クライアント側のパフォーマンス トレース](https://docs.microsoft.com/azure/remote-rendering/how-tos/performance-tracing)

## <a name="collaboration-considerations"></a>コラボレーションに関する考慮事項

Azure Remote Rendering の最も重要な用途の一部には、同時に同じ 3D エクスペリエンスを表示している複数の参加者の間でのコラボレーションが含まれています。 これらの共有セッションでは、各参加者が同じネットワーク上の同じ場所にいるかどうかに関係なく、それぞれに独自の Azure Remote Rendering セッションが必要であるということを認識するのが重要です。

これは、各参加者が異なる視点から同じエクスペリエンスを実際に見ているからです。これを実現するには、それらの各パースペクティブから同じ 3D アセットを同時にレンダリングする必要があります。

### <a name="multiple-azure-remote-rendering-sessions"></a>複数の Azure Remote Rendering セッション

Azure Remote Rendering を使用して共有エクスペリエンスをサポートする場合、ARR セッションを作成および管理するために配置するシステムでは、複数のセッションを開始できるように準備しておく必要があります。 参加者が地理的に分散している場合、これらのセッションは、異なる Azure データ センターで初期化する必要が生じる可能性があります。

お客様のシステムでは、現在 Azure Remote Rendering によってサポートされていない地理的リージョンや、Azure Remote Rendering VM インスタンスが現在利用できない地理的リージョンに、1 人以上の参加者がいる可能性に対応する必要もあります。

このドキュメントで説明しているセッション プールやその他の戦略と組み合わせると、このような複数の同時セッションの管理をさらに効率化することができます。

### <a name="azure-blob-storage-considerations"></a>Azure Blob Storage に関する考慮事項

すべての同時 ARR セッションでは、同じ SAS URI を参照することで、変換されたモデルを表示できるようになっています。 これにより、目的の 3D アセットを一度だけアップロードして変換したら、すべてのセッションでそれを共有することができます。 これは特に、参加者が同じ場所におり、(Azure Blob Storage が Azure Remote Rendering サーバーおよびユーザーとは異なるデータ センターに配置されているために、パフォーマンス上の懸念のない) 同じデータ センターを使用している場合に当てはまります。

通常、3D アセットが 1 つの表示セッション用にアップロードされてから設計レビュー セッションなどで破棄された場合、Azure Remote Rendering サーバーに対して Azure Blob Storage の地理的リージョンがどこであるかということは、あまり重要ではありません。

ただし、トレーニングのユース ケースなどで 3D アセットが繰り返し使用される場合は、Azure Remote Rendering の使用を計画している各リージョンのデータ センターの BLOB ストレージに、すぐに使える状態で 3D アセットを保持することをお勧めします。 これは、Azure Storage の冗長性を使用して自動化することができます。 この目的で CDN が使用されることもよくありますが、これはまだ Azure Remote Rendering のオプションではありません。

詳細情報:

* [複合現実での共有エクスペリエンス](https://docs.microsoft.com/windows/mixed-reality/shared-experiences-in-mixed-reality)
* [Azure Storage の冗長性](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

## <a name="managing-model-access"></a>モデル アクセスの管理

Azure Remote Rendering を最大限に活用するには、3D モデルを管理するためのエンドツーエンドのインフラストラクチャを慎重に検討する必要があります。

Azure Remote Rendering を使用する利点は、大規模な 3D アセットを表示する前に、複合現実デバイスに直接送信する必要がないことです。  さらに、Azure Remote Rendering で使用できるように 3D アセットをいったんアップロードして変換すると、任意の数のユーザーがその 3D モデルの 1 つのインスタンスを共有できるようになります。

### <a name="considerations-for-3d-model-access"></a>3D モデル アクセスに関する考慮事項

モデル アクセス戦略を決定する際に重要ないくつかの考慮事項を次に示します。

予想されるユース ケースに基づいて、ユーザーが表示する 3D アセットを選択できるようにする最適な場所または場所の組み合わせを決定します。 一般的な選択肢は次のとおりです。

* 複合現実エクスペリエンス内で直接
* コンパニオン Web ポータル経由
* コンパニオン デスクトップ アプリケーションまたはモバイル アプリケーションで

同じ 3D アセットを複数回アップロードする可能性のある使用パターンがユース ケースに含まれる場合、バックエンドでは、今後の複数回の選択に備えてモデルを 1 回のみ事前処理すればよいように、どのモデルが ARR で使用できるように変換済みであるかが追跡されます。 設計レビューの例としては、チームが元の共通 3D アセットにアクセスできる場合が挙げられます。 各チーム メンバーは、作業ストリームの特定の時点で ARR を使用してモデルをレビューする必要があります。 最初のビュー時にのみ、処理前の手順がトリガーされます。 後続のビューでは、関連付けられている処理後のファイルが SAS 出力コンテナー内で検索されます。

ユース ケースに応じて、同じセッションでまとめて表示する 3D アセットまたはアセットのグループごとに、適切な Azure Remote Rendering サーバーのサイズや、*Standard* と *Premium* のどちらを使用するかといった内容を決定し、保持できると便利です。  

### <a name="on-device-model-selection-list"></a>デバイス上のモデル選択リスト

トレーニング、タスク ガイダンス、マーケティング アプリケーションなどの多くのユース ケースでは、Azure Remote Rendering で一般的に表示される 3D アセットのセットが、かなりの程度静的である可能性があります。 このような場合では、キュレーションされた一連の 3D アセットを事前に変換し、キュレーションされたアセットの選択リストを設定するために必要な情報が格納されたデータベースを介して使用できるようにすることができます。 次に、複合現実アプリケーションでこのデータを取得することによって、選択メニューを設定できます。

これは、個々のユーザーまたはグループに固有のプライベート 3D アセットをアップロードする方法を提供することで、さらに一歩進めることができます。 次に、このプライベート アセットの一覧を、キュレーションされた共通アセットの一覧とユーザー エクスペリエンス内で組み合わせて、表示する 3D アセットを選択できるようにすることが可能です。

### <a name="on-device-onedrive-access"></a>デバイスから OneDrive へのアクセス

OneDrive ファイル ピッカーが Microsoft の混合現実デバイスにネイティブで組み込まれている場合、OneDrive からデバイス上の 3D アセットを選択できることは魅力的です。特に、異なる 3D モデルや変更済みの 3D モデルを読み込むユース ケースでは便利です。 このシナリオでは、ユーザーは、複合現実アプリケーション内の OneDrive ファイル ピッカーを使用して、1 つまたは複数の 3D アセットを選択します。 次に、3D アセットは SAS 入力コンテナーに移行されてから、SAS 出力コンテナーに変換され、ARR セッションにアタッチされます。 複合現実アプリケーションでは、すべてのビットを OneDrive からデバイスに移動して Azure Blob Storage に戻すのではなく、クラウドベースのプロセスを呼び出すことでこれらの手順を実行するのが理想的です。

このアプローチは、以前に表示されていた 3D アセット間の関連付けを保持することによってさらに一歩進めることができます。たとえば、OneDrive から同じモデルを再選択したときに、アプリケーションが変換処理をバイパスして、関連付けられている変換済み 3D アセットを SAS URI 経由で直接読み込めるようにすることが考えられます。

詳細情報:

* [Microsoft Power Automate テンプレートを使用した OneDrive から Azure Storage へのレプリケーション](https://flow.microsoft.com/galleries/public/templates/2f90b5d3-029b-4e2e-ad37-1c0fe6d187fe/when-a-file-is-uploaded-to-onedrive-copy-it-to-azure-storage-container/)
* [OneDrive ファイル ストレージ API の概要](https://docs.microsoft.com/graph/onedrive-concept-overview)

### <a name="direct-cad-access"></a>CAD への直接アクセス

複合現実の魅力的なユース ケースの 1 つに、進行中の CAD 作業の設計レビューがあります。 このシナリオでは、デスクトップから複合現実への最速での読み込みが鍵となります。 理想的なソリューションとしては、特定の CAD アプリケーション用のプラグインの開発があります。 これらのプラグインを使用して、読み込み、変換、および表示の各プロセスのあらゆる側面を直接管理します。

* 以下を実現する UX を提供します。
  * CAD アプリケーションを特定の複合現実デバイスとペアリングする (1 回限り)。
  * 選択したジオメトリをその複合現実デバイスで表示するように要求する。
* まだ実行されていない場合は、Azure Remote Rendering セッションを起動して、CAD ファイルをアップロードおよび変換しながら並列処理できるようにします
* Azure Remote Rendering でサポートされているいずれかの形式に CAD のジオメトリ データを正規化します
* 正規化されたデータを Azure Blob Storage 入力コンテナーに直接転送します
* モデル変換プロセスを開始します
* モデルの出力コンテナーの SAS URI を Azure Remote Rendering セッションにリンクします
* モデルが使用可能で表示の準備が整っていることをペアの混合現実アプリケーションに通知し、アプリケーションが出力コンテナーをセッションにアタッチできるように出力コンテナーの SAS URI を提供します。

より簡素ではあるもののわずかに非効率的なアプローチとしては、3D モデルをローカルのハード ドライブに保存するプロセスを自動化し、その保存したファイルを SAS 入力コンテナーに送信するプロセスを開始する方法もあります。

### <a name="azure-marketplace"></a>Azure Marketplace

エンタープライズ クライアントの多くは、セキュリティ上の理由から独自の Azure アカウントと資格情報を使用して Azure Stack をデプロイできることを求めています。 これを可能にするには、Azure Marketplace で Azure アプリケーション プランとして公開できるように、Azure マネージド アプリケーションのパッケージ化を検討します。

詳細情報:

* [Azure Marketplace](https://azure.microsoft.com/marketplace/)
* [チュートリアル:Azure マネージド アプリケーションを Marketplace で公開する](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/publish-marketplace-app)

### <a name="security"></a>セキュリティ

セキュリティを徹底的に強化するために、エンドツーエンドの Azure Remote Rendering ソリューションを構築することが重要です。 エンドツーエンド ソリューションの設計で考慮する必要があるセキュリティには、次のようなさまざまな側面があります。

* 認証の戦略
* アクセス管理 - グループ、ポリシー、およびアクセス許可
* マルチテナント
* データ ストレージと転送の暗号化
* 一時的に使用するトークン
* 分散型サービス拒否 (DDoS) 攻撃
* 脅威の検出
* VPN と安全なネットワーク
* ファイアウォール
* 証明書および秘密鍵の管理
* アプリケーションの脆弱性と侵害

認証については、できるだけ多くの ARR 認証とセッション管理を Azure Web サービスに移行することをお勧めします。 これにより、管理が容易になり、より安全なソリューションが実現します。

詳細情報:

* [Azure AD サービス認証](https://docs.microsoft.com/azure/spatial-anchors/concepts/authentication?tabs=csharp#azure-ad-service-authentication)
* [Azure でセキュリティ体制を強化する](https://azure.microsoft.com/overview/security/)
* [クラウドのセキュリティ](https://azure.microsoft.com/product-categories/security/)
