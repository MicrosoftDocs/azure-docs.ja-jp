---
title: Azure Cosmos DB の一般的なユース ケースとシナリオ
description: Azure Cosmos DB の上位 5 つのユース ケースとして、ユーザーが生成したコンテンツ、イベントのログ記録、カタログ データ、ユーザー設定のデータ、およびモノのインターネット (IoT) について説明します。
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/07/2017
ms.openlocfilehash: 935c6ff03485c2b8e4d3e2f3df51c740ec1e8371
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54043296"
---
# <a name="common-azure-cosmos-db-use-cases"></a>Azure Cosmos DB の一般的なユース ケース
この記事では、Azure Cosmos DB のいくつかの一般的なユースケースの概要について説明します。  この記事に記載されている推奨事項は、Cosmos DB を使用してアプリケーションを開発する際の出発点として利用できます。   

この記事を読むと、次の質問に回答できるようになります。 

* Azure Cosmos DB の一般的なユースケースとはどのようなものですか?
* 小売アプリケーションで Azure Cosmos DB を使用する利点は何ですか?
* モノのインターネット (IoT) システムのデータ ストアとして Azure Cosmos DB を使用する利点は何ですか?
* Web およびモバイル アプリケーションで Azure Cosmos DB を使用する利点は何ですか?

## <a name="introduction"></a>はじめに
[Azure Cosmos DB](../cosmos-db/introduction.md) は、Microsoft のグローバルに分散されたデータベース サービスです。 このサービスは、顧客が任意の数の地理的リージョン間でスループットとストレージを弾力的に (かつ独立して) スケーリングできるように設計されています。 Azure Cosmos DB は、スループット、待機時間、可用性、整合性を含む包括的な[サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/cosmos-db/)を提供するための、今日の市場で最初のグローバルに分散されたデータベース サービスです。 

Azure Cosmos DB は、さまざまなアプリケーションやユース ケースで使用されている、グローバルに分散されたマルチモデル データベースです。 ミリ秒レベルの高速な応答時間を必要とし、迅速かつグローバルにスケーリングする必要がある [サーバーレス](https://azure.com/serverless) アプリケーションに最適です。 複数のデータ モデル (キー値、ドキュメント、グラフ、多桁式) および [Azure Cosmos DB の MongoDB 用 API](mongodb-introduction.md)、[SQL API](documentdb-introduction.md)、[Gremlin API](graph-introduction.md)、[Table API](table-introduction.md) を含むデータ アクセス用の多くの API を、ネイティブおよび拡張可能な方法でサポートします。 

Azure Cosmos DB を、グローバルな展開を視野に入れた高性能アプリケーションに最適なものにしている属性のいくつかを次に示します。

* Azure Cosmos DB は、高可用性とスケーラビリティのためにデータをネイティブにパーティション分割します。 Azure Cosmos DB は、すべての単一リージョン アカウントと緩やかな一貫性のすべてのマルチリージョン アカウントにおける可用性、スループット、低待機時間、一貫性に対して 99.99% の保証を提供し、すべての複数リージョン データベース アカウントに対して 99.999% の 読み取り可用性を提供します。
* Azure Cosmos DB には、待機時間の短いミリ秒レベルの応答時間を備えた、SSD でバックアップされたストレージが含まれています。
* Azure Cosmos DB の結果、一貫性のあるプレフィックス、セッション、有界整合性制約などの整合性レベルのサポートにより、完全な柔軟性と優れた費用性能比が可能になります。 整合性のレベルで Azure Cosmos DB ほど高い柔軟性を提供するデータベース サービスは存在しません。 
* Azure Cosmos DB には、ストレージとスループットを個別に測定する、データを考慮した柔軟な価格モデルが用意されています。
* Azure Cosmos DB の予約済みスループット モデルでは、基になるハードウェアの CPU/メモリ/IOP ではなく、読み取り/書き込みの数の点から考慮することができます。
* Azure Cosmos DB の設計により、1 日あたり数兆個レベルの要求という大量の要求まで拡張できます。

このように、DocumentDB は大量の読み取り/書き込みを迅速に処理する必要がある Web アプリケーション、モバイル アプリケーション、ゲーム アプリケーション、IoT アプリケーションに有用です。

## <a name="iot-and-telematics"></a>IoT とテレマティックス
IoT のユース ケースでは、データの取り込み、処理、および格納の方法にいくつかの共通パターンがあります。  まず、これらのシステムでは、さまざまなロケールのデバイス センサーから大量のデータを取り込む必要があります。 次に、これらのシステムではストリーミング データを処理および分析して、インサイトをリアルタイムで導き出しています。 その後、データはバッチ分析用にコールド ストレージにアーカイブされます。 Microsoft Azure では、Azure Cosmos DB、Azure Event Hub、Azure Stream Analytics、Azure Notification Hub、Azure Machine Learning、Azure HDInsight、PowerBI など、IoT のユース ケースに適用できる豊富なサービスを提供しています。 

![Azure Cosmos DB IoT リファレンス アーキテクチャ](./media/use-cases/iot.png)

大量データの取り込みは、待機時間が短く高スループットのデータ取り込みが可能な Azure Event Hubs で実行できます。 取り込んだデータを処理してリアルタイムのインサイトを得る必要がある場合は、Azure Stream Analytics にデータを投入してリアルタイムで分析できます。 アドホック クエリのためにデータを Azure Cosmos DB に読み込むことができます。 データが Azure Cosmos DB に読み込まれたら、そのデータはクエリの準備ができています。 さらに、新しいデータと既存のデータへの変更は、Change Feed で読み取ることができます。 Change Feed は、Cosmos DB コンテナーへの変更を順次格納する永続的な追加専用ログです。 Azure Cosmos DB 内のすべてのデータ、またはデータへの変更のみを、リアルタイム分析に含まれる参照データとして使用できます。 さらに、Azure Cosmos DB データを Pig、Hive、または Map/Reduce ジョブ用に HDInsight に接続することによって、データをさらに絞り込んで処理できます。  絞り込まれたデータはその後、レポート作成のために元の Azure Cosmos DB に読み込まれます。   

Azure Cosmos DB、EventHubs、および Storm を使用したサンプルの IoT ソリューションについては、「[hdinsight-storm-examples repository on GitHub (GitHub 上の hdinsight-storm-examples リポジトリ)](https://github.com/hdinsight/hdinsight-storm-examples/)」を参照してください。

IoT に対する Azure サービスの詳細については、 [モノのインターネットの作成](https://www.microsoft.com/en-us/internet-of-things)に関するページを参照してください。 

## <a name="retail-and-marketing"></a>小売とマーケティング
Azure Cosmos DB は、Windows ストアおよび XBox Live を実行する Microsoft の独自の電子商取引プラットフォームで広く使用されています。 小売り業界ではカタログ データの格納や、順次処理パイプラインのイベント ソーシングにも使用できます。

カタログ データの使用シナリオでは、人、場所、製品などのエンティティの一連の属性を保存し、照会する必要があります。 カタログ データの例には、ユーザー アカウント、製品カタログ、IoT のデバイス レジストリ、部品表システムなどがあります。 これらのデータの属性は、アプリケーションの要件によって異なり、さらに時間の経過と共に変化する場合があります。

自動車部品メーカーの製品カタログの例を考えてみましょう。 各部品には、すべて部品に共通の属性に加えて、それぞれに固有の属性があります。 また、特定の部品の属性は、翌年に新しいモデルがリリースされたときに変更されることがあります。 Azure Cosmos DB は柔軟なスキーマと階層データをサポートしているため、製品カタログ データの格納に最適です。

![Azure Cosmos DB 小売カタログ リファレンス アーキテクチャ](./media/use-cases/product-catalog.png)

Azure Cosmos DB は、多くの場合、その [Change Feed](change-feed.md) 機能を使用するイベント駆動アーキテクチャのイベント ソーシングに使用されます。 Change Feed 機能は、Azure Cosmos DB 用に作られた、段階的かつ確実にインサートやアップデートを読み取る (注文イベントなど) 能力をダウンストリームのマイクロサービスに提供します。 この機能を利用すれば、状態の変化するイベントのメッセージ ブローカーとして永続的なイベント ストアを提供し、多数のマイクロサービス間で注文処理ワークフローを稼働することができます ([サーバーレス Azure Functions](https://azure.com/serverless) として実装可能)。

![Azure Cosmos DB 注文パイプライン リファレンス アーキテクチャ](./media/use-cases/event-sourcing.png)

さらに、Azure Cosmos DB に格納されたデータは、Apache Spark ジョブによるビッグ データ分析のために HDInsight と統合できます。 Azure Cosmos DB 用の Spark コネクタの詳細については、「[Run a Spark job with Cosmos DB and HDInsight (Cosmos DB と HDInsight を使用した Spark ジョブの実行)](spark-connector.md)」を参照してください。

## <a name="gaming"></a>Gaming
データベース層は、ゲーム アプリケーションの重要なコンポーネントです。 最近のゲームはモバイル/コンソール クライアントでグラフィック処理を行いますが、ゲーム内統計、ソーシャル メディア統合、スコアボードなどの個人向けにカスタマイズされたコンテンツの配信は、クラウドに依存しています。 多くの場合、ゲームでは魅力的なゲーム内エクスペリエンスを提供するために、読み取り/書き込みの待機時間を 1 ミリ秒にする必要があります。 ゲーム データベースは高速であることが必要であり、新しいゲームのリリース時や機能の更新時に、要求レートの急増に対処できる必要があります。

Azure Cosmos DB は、[The Walking Dead: No Man's Land](https://azure.microsoft.com/blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) ([Next Games](https://www.nextgames.com/)) や [Halo 5: Guardians](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/) などのゲームで使用されています。 Azure Cosmos DB は、ゲーム開発者に次の利点を提供します。

* Azure Cosmos DB では、パフォーマンスを柔軟にスケールアップまたはスケールダウンできます。 これにより、同時にプレイしている数十から数百万ものプレイヤーのプロファイルや統計の更新を、1 回の API 呼び出しだけで処理することができます。
* Azure Cosmos DB は、ゲーム プレイ中の遅延の回避に役立つように、ミリ秒の読み取り/書き込みをサポートしています。
* Azure Cosmos DB の自動インデックス作成により、複数のさまざまなプロパティに対してリアルタイムにフィルター処理できます。たとえば、内部のプレーヤー ID や GameCenter、Facebook、Google ID によってプレーヤーを見つけたり、ギルド内のプレーヤー メンバーシップに基づいてクエリを実行したりできます。 複雑なインデックスやシャーディング インフラストラクチャを構築する必要もありません。
* ゲーム内チャット メッセージ、ギルド メンバーシップ、ミッション完了、スコアボード、ソーシャル グラフなどのソーシャル機能は、柔軟なスキーマで簡単に実装できます。
* Azure Cosmos DB は、管理されたサービスとしてのプラットフォーム (PaaS) として、迅速な繰り返しを可能にして製品化までの時間を短縮するために必要なセットアップや管理作業が最小限に抑えられます。

![Azure Cosmos DB ゲーム リファレンス アーキテクチャ](./media/use-cases/gaming.png)

## <a name="web-and-mobile-applications"></a>Web アプリケーションとモバイル アプリケーション
Azure Cosmos DB は一般に Web およびモバイル アプリケーション内で使用され、ソーシャル インタラクションのモデル化、サード パーティ サービスとの統合、および豊富な個人別のエクスペリエンスの構築に適しています。 Cosmos DB SDK を使用すると、一般的な [Xamarin フレームワーク](mobile-apps-with-xamarin.md)を使って豊富な iOS および Android アプリケーションを構築できます。  

### <a name="social-applications"></a>ソーシャル アプリケーション
Azure Cosmos DB の一般的なユース ケースは、Web、モバイル、ソーシャル メディア アプリケーションにユーザーが生成したコンテンツ (UGC) の格納およびクエリの実行です。 UGC の例には、チャット セッション、ツイート、ブログの投稿、評価、コメントなどがあります。 ソーシャル メディア アプリケーション内の UGC は、多くの場合、厳格な構造による制約のない、自由形式のテキスト、プロパティ、タグ、およびリレーションシップが組み合わさったものです。 チャット、コメント、投稿などのコンテンツは Cosmos DB に格納でき、変換や複雑なオブジェクト リレーショナル マッピング層は必要ありません。  開発者がアプリケーション コードを反復処理する際、要件に合うようにデータのプロパティを簡単に追加または変更できるため、迅速な開発が促進されます。  

サード パーティ ソーシャル ネットワークを統合するアプリケーションは、これらのネットワークから届くスキーマの変更に応答する必要があります。 Cosmos DB では既定で自動的にデータにインデックスが設定されるため、データに対していつでもクエリを実行できます。 これにより、これらのアプリケーションでは、それぞれのニーズに応じて柔軟にプロジェクションを取得できます。

ソーシャル アプリケーションの多くが世界規模で実行されており、予測できない使用パターンが発生することがあります。 アプリケーション層は使用ニーズを満たすようにスケールするため、データ ストアを柔軟にスケールできることがきわめて重要です。  Cosmos DB では、Cosmos DB アカウントにデータのパーティションを追加することでスケールアウトできます。  また、複数のリージョンにわたって追加の Cosmos DB アカウントを作成することもできます。 Cosmos DB サービス リージョンの可用性については、「[Azure のリージョン](https://azure.microsoft.com/regions/#services)」を参照してください。

![Azure Cosmos DB Web アプリ リファレンス アーキテクチャ](./media/use-cases/apps-with-global-reach.png)

### <a name="personalization"></a>パーソナル化
今日では、最新のアプリケーションは複雑なビューとエクスペリエンスを備えています。 それらはたいてい動的で、ユーザーの設定や気分、ブランドのニーズに対応します。 そのため、アプリケーションは、UI 要素とエクスペリエンスをすばやく表示するために、個人用設定を効率的に取得できる必要があります。 

Cosmos DB でサポートされる形式である JSON は、軽量なうえに JavaScript で容易に解釈できるため、UI レイアウト データを表すのに効果的な形式です。 Cosmos DB は、チューニング可能な一貫性レベルを提供し、高速な読み取りと待機時間の短い書き込みを実現します。 これにより、個人用設定を含む UI レイアウト データを Cosmos DB に JSON ドキュメントとして格納すれば、これらのデータをネットワーク経由で効率よく取得することができます。

![Azure Cosmos DB Web アプリ リファレンス アーキテクチャ](./media/use-cases/personalization.png)

## <a name="next-steps"></a>次の手順
Azure Cosmos DB の使用を開始するには、[クイック スタート](create-sql-api-dotnet.md)をご覧ください。このクイック スタートでは、アカウントを作成して Cosmos DB を実際に使用する方法を説明しています。 

または、Cosmos DB を使用しているお客様の詳細情報を確認したい場合は、次の顧客事例が提供されています。

* [Jet.com](https://jet.com)。 電子商取引の挑戦者はトップを見据えて、Microsoft のクラウド上で運用を行い、世界規模で Cosmos DB を活用しています。
* [Asos.com](https://www.asos.com/)。 Asos.com は、英国のファッションおよび美容用品のオンライン ストアです。 主に若者をターゲットとし、Asos は 850 以上のブランドおよび、独自のラインナップの衣類やアクセサリーを販売しています。
* [トヨタ](https://www.toyota.com/)。 トヨタ自動車は日本の自動車メーカーです。 トヨタでは、グローバル IoT アプリに Cosmos DB を活用しています。
* [Citrix](https://customers.microsoft.com/story/citrix)。 Citrix では、Azure Service Fabric および Azure Cosmos DB を使用してシングル サインオン ソリューションを開発しています。
* [TEXA](https://customers.microsoft.com/story/texaspa) TEXA の革新的な車両所有者向け IoT ソリューションは、時間、費用、ガソリンを節約し、ひいては生活全般の負担を軽減します。
* [Domino's Pizza](https://www.dominos.com)。 Domino's Pizza Inc. は、アメリカのピザ レストラン チェーンです。
* [Johnson Controls](https://www.johnsoncontrols.com)。 Johnson Controls は、150 か国以上で幅広い顧客にサービスを提供する、グローバルな総合テクノロジーおよび多業種のリーダーです。
* [Microsoft Windows、ユニバーサル ストア、Azure IoT Hub、Xbox Live、その他のインターネット規模のサービス](https://azure.microsoft.com/blog/how-azure-documentdb-planet-scale-nosql-helps-run-microsoft-s-own-businesses/)。 Microsoft が Azure Cosmos DB を使用して大規模でスケーラブルなサービスを構築する方法。
* [Microsoft のデータと分析チーム](https://customers.microsoft.com/story/microsoftdataandanalytics)。 マイクロソフトのデータと分析チームは、Azure Cosmos DB を使用して、地球規模のビッグデータの収集を実現しました。
* [Sulekha.com](https://customers.microsoft.com/story/sulekha-uses-azure-documentdb-to-connect-customers-and-businesses-across-india)。 Sulekha では、Azure Cosmos DB を使用してインド各地で顧客と企業を結び付けています。
* [NewOrbit](https://customers.microsoft.com/story/neworbit-takes-flight-with-azure-documentdb)。 NewOrbit は、Azure Cosmos DB を使用することで飛躍を遂げています。
* [Affinio](https://customers.microsoft.com/doclink/affinio-switches-from-aws-to-azure-documentdb-to-harness-social-data-at-scale)。 Affinio は、大規模なソーシャル データを処理するために、AWS から Azure Cosmos DB に切り替えます。
* [Next Games](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/)。 「The Walking Dead: No Man's Land」は、Azure Cosmos DB のサポートによりランキング 1 位に浮上しました。
* [Halo](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/)。 Azure Cosmos DB を使用して「Halo 5」にソーシャル ゲームプレイを実装した方法をご紹介します。
* [Cortana Analytics ギャラリー](https://azure.microsoft.com/blog/cortana-analytics-gallery-a-scalable-community-site-built-on-azure-documentdb/)。 Cortana Analytics ギャラリーは、Azure Cosmos DB 上に構築されたスケーラブルなコミュニティ サイトです。
* [Breeze](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18602)。 業界屈指のインテグレーターが柔軟なクラウド テクノロジを使用した多国籍企業のグローバル インサイトを数分で説明します。
* [News Republic](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18639)。 ニュースにインテリジェントを追加し、意識の高い市民に向けて情報を提供しています。 
* [SGS International](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18653)。 世界全体で一貫した色を提示するために、大手ブランドが SGS を頼っています。 その SGS は Azure を活用しています。
* [Telenor](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18608)。 世界的リーダーの Telenor では、クラウドを使用して、スタートアップ時のスピードを保って前進しています。 
* [XOMNI](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18667)。 未来の店舗は、スピーディな検索と容易なデータ フローを基盤として運営されます。
* [Nucleo](https://customers.microsoft.com/story/azure-based-software-platform-breaks-down-barriers-bet)。 Azure ベースのソフトウェア プラットフォームが、企業と顧客との間の壁を打ち壊します。
* [Weka](https://customers.microsoft.com/story/weka-smart-fridge-improves-vaccine-management-so-more-people-can-be-protected-against-diseases)。 Weka の Smart Fridge によってワクチン管理が向上し、より多くの人を感染症から守ることができるようになりました。
* [Orange Tribes](https://customers.microsoft.com/story/theres-more-to-that-food-app-than-meets-the-eye-or-the-mouth)。 そこには、目に映るフード アプリ以上のものがあります。
* [Real Madrid](https://customers.microsoft.com/story/real-madrid-brings-the-stadium-closer-to-450-million-f)。 Real Madrid は、Microsoft Cloud により、世界中から 4 億 5,000 万人近くのファンをスタジアムに動員しています。
* [Tuku](https://customers.microsoft.com/story/tuku-makes-car-buying-fun-with-help-from-azure-services)。 TUKU では、Azure サービスを利用して自動車を快適に購入できます。
