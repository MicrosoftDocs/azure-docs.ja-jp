---
title: "Azure DocumentDB の一般的なユース ケースと NoSQL シナリオ | Microsoft Docs"
description: "DocumentDB の上位 5 つのユース ケースとして、ユーザーが生成したコンテンツ、イベントのログ記録、カタログ データ、ユーザー設定のデータ、およびモノのインターネット (IoT) について説明します。"
services: documentdb
author: h0n
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: eca68a58-1a8c-4851-8cf8-6e4d2b889905
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: hawong
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 9a0ba106b4c2aded8aaac673dfba3610bd4ca0fb
ms.lasthandoff: 03/24/2017


---

# <a name="common-documentdb-use-cases"></a>DocumentDB の一般的なユース ケース
この記事では、DocumentDB の一般的なユース ケースの概要について説明します。  この記事に記載されている推奨事項は、DocumentDB を使用してアプリケーションを開発する際の出発点として利用できます。   

この記事を読むと、次の質問に回答できるようになります。 

* DocumentDB の一般的なユース ケースにはどのようなものがあるか。
* 小売アプリケーションで DocumentDB を使用する利点は何か。
* モノのインターネット (IoT) システムのデータ ストアとして DocumentDB を使用する利点は何か。
* Web アプリケーションやモバイル アプリケーションで DocumentDB を使用する利点は何か。

## <a name="common-use-cases-for-documentdb"></a>DocumentDB の一般的なユース ケース
Azure DocumentDB は、さまざまなアプリケーションやユース ケースで使用されている汎用の NoSQL データベースです。 ミリ秒レベルの高速な応答時間や、迅速なスケールのニーズがあるアプリケーションに最適です。 ハイパフォーマンス アプリケーションに特に適した DocumentDB。その特長をいくつかご紹介します。

* 高可用性とスケーラビリティのため、ネイティブでデータをパーティション分割します。
* ミリ秒レベルで応答する高速な SSD ベースのストレージを備えています。
* 最終的、セッション、有界整合性制約などの整合性レベルのサポートにより、優れた費用性能比を実現します。 
* ストレージとスループットを個別に測定する柔軟な価格モデルが用意されています。
* 予約済みスループット モデルでは、基になるハードウェアの CPU、メモリ、IOP の代わりに、読み取り/書き込みの数を考慮すればよいようになっています。
* 1 日あたりおよそ数十億にも上る大量の要求にまでスケールアップできるよう設計されています。

このように、DocumentDB は大量の読み取り/書き込みを迅速に処理する必要がある Web アプリケーション、モバイル アプリケーション、ゲーム アプリケーション、IoT アプリケーションに有用です。 

## <a name="iot-and-telematics"></a>IoT とテレマティックス
IoT のユース ケースでは、データの取り込み、処理、および格納の方法にいくつかの共通パターンがあります。  まず、これらのシステムでは、さまざまなロケールのデバイス センサーから大量のデータを取り込む必要があります。 次に、これらのシステムではストリーミング データを処理および分析して、インサイトをリアルタイムで導き出しています。 その後、データはバッチ分析用にコールド ストレージにアーカイブされます。 Microsoft Azure では、Azure Event Hubs、Azure DocumentDB、Azure Stream Analytics、Azure Notification Hub、Azure Machine Learning、Azure HDInsight、PowerBI など、IoT のユース ケースに適用できる豊富なサービスを提供しています。 

![Azure DocumentDB IoT リファレンス アーキテクチャ](./media/documentdb-use-cases/documentdb-iot.png)

大量データの取り込みは、待機時間が短く高スループットのデータ取り込みが可能な Azure Event Hubs で実行できます。 取り込んだデータを処理してリアルタイムのインサイトを得る必要がある場合は、Azure Stream Analytics にデータを投入してリアルタイムで分析できます。 DocumentDB にデータを読み込めば、アドホック クエリを実行できます。 データが DocumentDB に読み込まれれば、いつでもデータにクエリを実行できます。  DocumentDB 内のデータは、リアルタイム分析において参照データとして使用できます。 さらに、Pig、Hive、または Map/Reduce のジョブ用に DocumentDB データを HDInsight に接続し、データをさらに絞り込んで処理できます。  絞り込んだデータは DocumentDB に再度読み込んでレポート作成に使用できます。   

DocumentDB、Event Hubs、および Storm を使用した IoT ソリューションの例については、 [GitHub の hdinsight-storm-examples リポジトリ](https://github.com/hdinsight/hdinsight-storm-examples/)を参照してください。

IoT に対する Azure サービスの詳細については、[モノのインターネットの作成](http://www.microsoft.com/en-us/server-cloud/internet-of-things.aspx)に関するページを参照してください。 

## <a name="retail-and-marketing"></a>小売とマーケティング
DocumentDB は、カタログ データを格納するために、小売業界でよく使用されます。 カタログ データの使用シナリオでは、人、場所、製品などのエンティティの一連の属性を保存し、照会する必要があります。  カタログ データの例には、ユーザー アカウント、製品カタログ、IoT のデバイス レジストリ、部品表システムなどがあります。  これらのデータの属性は、アプリケーションの要件によって異なり、さらに時間の経過と共に変化する場合があります。  

自動車部品メーカーの製品カタログの例を考えてみましょう。 各部品には、すべて部品に共通の属性に加えて、それぞれに固有の属性があります。  また、特定の部品の属性は、翌年に新しいモデルがリリースされたときに変更されることがあります。  DocumentDB は JSON ドキュメント ストアとして柔軟なスキーマと階層データをサポートしているため、製品カタログ データの格納に適しています。

![Azure DocumentDB 小売カタログ リファレンス アーキテクチャ](./media/documentdb-use-cases/documentdb-retail-catalog.png)

 さらに、DocumentDB に格納されたデータは、Pig ジョブ、Hive ジョブ、または Map/Reduce ジョブによってビッグ データ分析を行うために、HDInsight と統合できます。 DocumentDB 用 Hadoop コネクタの詳細については、 [DocumentDB と HDInsight を使用した Hadoop ジョブの実行](documentdb-run-hadoop-with-hdinsight.md)に関するページを参照してください。

 ![Azure DocumentDB 小売注文リファレンス アーキテクチャ](./media/documentdb-use-cases/documentdb-retail-orders.png)
## <a name="gaming"></a>ゲーム
データベース層は、ゲーム アプリケーションの重要なコンポーネントです。 最近のゲームはモバイル/コンソール クライアントでグラフィック処理を行いますが、ゲーム内統計、ソーシャル メディア統合、スコアボードなどの個人向けにカスタマイズされたコンテンツの配信は、クラウドに依存しています。 多くの場合、ゲームでは魅力的なゲーム内エクスペリエンスを提供するために、読み取り/書き込みの待機時間を 1 ミリ秒にする必要があります。 ゲーム データベースは高速であることが必要であり、新しいゲームのリリース時や機能の更新時に、要求レートの急増に対処できる必要があります。

DocumentDB は、[Next Games](http://www.nextgames.com/) の「[The Walking Dead: No Man's Land](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/)」や「[Halo 5: Guardians](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/)」などのゲームで使用されています。 DocumentDB はゲーム開発者に次の利点をもたらします。

* パフォーマンスを柔軟にスケール アップまたはスケール ダウンできます。 これにより、同時にプレイしている数十から数百万ものプレイヤーのプロファイルや統計の更新を、1 回の API 呼び出しだけで処理することができます。
* ゲーム プレイ中に遅延が発生しないよう、ミリ秒レベルの読み取り/書き込みがサポートされています。
* 自動インデックス作成機能により、複数のさまざまなプロパティに対してリアルタイムでフィルター処理することができます。たとえば、プレーヤーの内部 ID、GameCenter、Facebook、Google ID、またはギルド メンバーシップに基づくクエリによって、プレーヤーを検索できます。 複雑なインデックスやシャーディング インフラストラクチャを構築する必要もありません。
* ゲーム内チャット メッセージ、ギルド メンバーシップ、ミッション完了、スコアボード、ソーシャル グラフなどのソーシャル機能は、柔軟なスキーマで簡単に実装できます。
* 管理されたサービスとしてのプラットフォーム (PaaS) である DocumentDB を使用すれば、セットアップと管理にかける労力を最小限に抑えながら、反復処理を迅速化し、製品化までの時間を短縮できます。

![Azure DocumentDB ゲーム リファレンス アーキテクチャ](./media/documentdb-use-cases/documentdb-gaming-architecture.png)

## <a name="web-and-mobile-applications"></a>Web アプリケーションとモバイル アプリケーション
DocumentDB は、通常、Web アプリケーションおよびモバイル アプリケーションで使用されます。特に、ソーシャル インタラクションのモデル化、サード パーティ サービスとの統合、豊富でカスタマイズされたエクスペリエンスの構築に適しています。 DocumentDB SDK を使用すると、一般的な [Xamarin フレームワーク](documentdb-mobile-apps-with-xamarin.md)を使って豊富な iOS および Android アプリケーションを構築できます。  

### <a name="social-applications"></a>ソーシャル アプリケーション
DocumentDB の一般的なユース ケースとして、Web アプリケーションやモバイル アプリケーション、特にソーシャル メディア アプリケーション向けにユーザーが生成したコンテンツ (UGC) の保存とクエリの実行があります。 UGC の例には、チャット セッション、ツイート、ブログの投稿、評価、コメントなどがあります。 ソーシャル メディア アプリケーション内の UGC は、多くの場合、厳格な構造による制約のない、自由形式のテキスト、プロパティ、タグ、およびリレーションシップが組み合わさったものです。 チャット、コメント、投稿などのコンテンツは DocumentDB に格納でき、変換や複雑なオブジェクト リレーショナル マッピング層は必要ありません。  開発者がアプリケーション コードを反復処理する際、要件に合うようにデータのプロパティを簡単に追加または変更できるため、迅速な開発が促進されます。  

サード パーティ ソーシャル ネットワークを統合するアプリケーションは、これらのネットワークから届くスキーマの変更に応答する必要があります。 DocumentDB では既定で自動的にデータにインデックスが設定されるため、データに対していつでもクエリを実行できます。 これにより、これらのアプリケーションでは、それぞれのニーズに応じて柔軟にプロジェクションを取得できます。

ソーシャル アプリケーションの多くが世界規模で実行されており、予測できない使用パターンが発生することがあります。 アプリケーション層は使用ニーズを満たすようにスケールするため、データ ストアを柔軟にスケールできることがきわめて重要です。  DocumentDB では、DocumentDB アカウントにデータのパーティションを追加することでスケールアウトできます。  また、複数のリージョンにわたって追加の DocumentDB アカウントを作成することもできます。 DocumentDB サービス リージョンの可用性については、「 [Azure のリージョン](https://azure.microsoft.com/regions/#services)」を参照してください。

![Azure DocumentDB Web アプリ リファレンス アーキテクチャ](./media/documentdb-use-cases/documentdb-web.png)

### <a name="personalization"></a>パーソナル化
今日では、最新のアプリケーションは複雑なビューとエクスペリエンスを備えています。 それらはたいてい動的で、ユーザーの設定や気分、ブランドのニーズに対応します。 そのため、アプリケーションは、UI 要素とエクスペリエンスをすばやく表示するために、個人用設定を効率的に取得できる必要があります。 

JSON は、軽量なうえに JavaScript で容易に解釈できるため、UI レイアウト データを表すのに効果的な形式です。 DocumentDB は、チューニング可能な一貫性レベルを提供し、高速な読み取りと待機時間の短い書き込みを実現します。 これにより、個人用設定を含む UI レイアウト データを DocumentDB に JSON ドキュメントとして格納すれば、これらのデータをネットワーク経由で効率よく取得することができます。

## <a name="next-steps"></a>次のステップ
DocumentDB の利用を開始するには、[アカウント](https://azure.microsoft.com/pricing/free-trial/)を作成し、Microsoft の[ラーニング パス](https://azure.microsoft.com/documentation/learning-paths/documentdb/)を使用して DocumentDB について学習し、必要な情報を確認してください。 

または、DocumentDB を使用しているお客様の詳細情報を確認したい場合は、次の顧客事例が提供されています。

* [Sulekha.com](https://customers.microsoft.com/en-US/story/sulekha-uses-azure-documentdb-to-connect-customers-and-businesses-across-india)。 Sulekha では、Azure DocumentDB を使用してインド各地で顧客と企業を結び付けています。
* [NewOrbit](https://customers.microsoft.com/en-US/story/neworbit-takes-flight-with-azure-documentdb)。 NewOrbit は、Azure DocumentDB を使用することで飛躍を遂げています。
* [Affinio](https://customers.microsoft.com/en-US/doclink/affinio-switches-from-aws-to-azure-documentdb-to-harness-social-data-at-scale)。 Affinio は、大規模なソーシャル データを処理するために、AWS から Azure DocumentDB に切り替えます。
* [Next Games](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/)。 Azure DocumentDB のサポートにより、「The Walking Dead: No Man's Land」がランキング 1 位に浮上しました。
* [Halo](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/)。 Azure DocumentDB を使用して「Halo 5」にソーシャル ゲームプレイを実装した方法をご紹介します。
* [Cortana Analytics ギャラリー](https://azure.microsoft.com/blog/cortana-analytics-gallery-a-scalable-community-site-built-on-azure-documentdb/)。 Cortana Analytics ギャラリーは、Azure DocumentDB 上に構築されたスケーラブルなコミュニティ サイトです。
* [Breeze](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18602)。 業界屈指のインテグレーターが柔軟なクラウド テクノロジを使用した多国籍企業のグローバル インサイトを数分で説明します。
* [News Republic](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18639)。 ニュースにインテリジェントを追加し、意識の高い市民に向けて情報を提供しています。 
* [SGS International](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18653)。 世界全体で一貫した色を提示するために、大手ブランドが SGS を頼っています。 その SGS は Azure を活用しています。
* [Telenor](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18608)。 世界的リーダーの Telenor では、クラウドを使用して、スタートアップ時のスピードを保って前進しています。 
* [XOMNI](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18667)。 未来の店舗は、スピーディな検索と容易なデータ フローを基盤として運営されます。
* [Nucleo](https://customers.microsoft.com/story/azure-based-software-platform-breaks-down-barriers-bet)。 Azure ベースのソフトウェア プラットフォームが、企業と顧客との間の壁を打ち壊します。
* [Weka](https://customers.microsoft.com/story/weka-smart-fridge-improves-vaccine-management-so-more-people-can-be-protected-against-diseases)。 Weka の Smart Fridge によってワクチン管理が向上し、より多くの人を感染症から守ることができるようになりました。
* [Orange Tribes](https://customers.microsoft.com/en-US/story/theres-more-to-that-food-app-than-meets-the-eye-or-the-mouth)。 そこには、目に映るフード アプリ以上のものがあります。
* [Real Madrid](https://customers.microsoft.com/en-US/story/real-madrid-brings-the-stadium-closer-to-450-million-f)。 Real Madrid は、Microsoft Cloud により、世界中から 4 億 5,000 万人近くのファンをスタジアムに動員しています。
* [Tuku](https://customers.microsoft.com/en-US/story/tuku-makes-car-buying-fun-with-help-from-azure-services)。 TUKU では、Azure サービスを利用して自動車を快適に購入できます。 

