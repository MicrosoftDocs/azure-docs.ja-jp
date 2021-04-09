---
title: Azure Cosmos DB のさまざまな API についてよく寄せられる質問
description: グローバル分散型のマルチモデル データベース サービスである Azure Cosmos DB についてよく寄せられる質問の回答が得られます。 容量、パフォーマンス レベル、スケーリングについて説明します。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 81c959467c4bb3700120917cf150773fb18e866d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359831"
---
# <a name="frequently-asked-questions-about-different-apis-in-azure-cosmos-db"></a>Azure Cosmos DB のさまざまな API についてよく寄せられる質問
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Azure Cosmos DB の一般的なユース ケースを教えてください。

Azure Cosmos DB は、自動スケール、予測可能なパフォーマンス、ミリ秒レベルの高速応答時間、スキーマフリー データに対してクエリを実行できることが重要である、新しい Web、モバイル、ゲーム、IoT の各アプリケーションに適しています。 Azure Cosmos DB は迅速な開発に役立ち、アプリケーション データ モデルの継続的な反復をサポートします。 ユーザーが生成したコンテンツとデータを管理するアプリケーションは、[Azure Cosmos DB の一般的なユース ケース](use-cases.md)です。

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Azure Cosmos DB では、予測可能なパフォーマンスをどのようにして実現していますか?

[要求ユニット](request-units.md) (RU) とは、Azure Cosmos DB におけるスループットの単位です。 1 RU のスループットは、1 KB のドキュメントを取得するスループットに相当します。 Azure Cosmos DB におけるすべての操作 (読み取り、書き込み、SQL クエリ、ストアド プロシージャの実行など) には、操作を完了するために必要なスループットに基づいて明確な RU 値が設定されています。 CPU、IO、メモリや、これらがアプリケーションのスループットに及ぼす影響について考えるのではなく、RU という 1 つの単位を基にして考えることができます。

各 Azure Cosmos コンテナーは、1 秒あたりのスループットを表す RU を単位として、プロビジョニング スループットを使用して構成できます。 あらゆる規模のアプリケーションで、個々の要求のベンチマークを実行して RU 値を測定し、すべての要求の要求ユニットの合計に対処できるようにコンテナーをプロビジョニングできます。 アプリケーションのニーズの進化に合わせて、コンテナーのスループットをスケールアップまたはスケールダウンすることもできます。 要求ユニットの詳細とコンテナーのニーズを判断する方法については、[スループット計算ツール](https://www.documentdb.com/capacityplanner)をお試しください。

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Azure Cosmos DB は、キー/値、多桁式、ドキュメント、グラフなどのさまざまなデータ モデルをどのようにサポートしていますか?

キー/値 (テーブル)、多桁式、ドキュメント、およびグラフ データ モデルは、Azure Cosmos DB のベースである ARS (アトム、レコード、およびシーケンス) 設計のため、すべてネイティブにサポートされています。 アトム、レコード、およびシーケンスは、さまざまなデータ モデルに容易にマップしたり投影したりできます。 モデルのサブセット用の API は今すぐ使用でき (SQL、MongoDB、Table、Gremlin)、追加のデータ モデルに固有のその他の API は将来使用可能になります。

Azure Cosmos DB は、開発者にスキーマやセカンダリ インデックスを要求することなく、取り込んだすべてのデータを自動的にインデックス作成できるスキーマ独立型インデックス作成エンジンを備えています。 このエンジンは、インデックスおよびクエリ処理サブシステムから記憶域のレイアウトを分離する一連の論理インデックス レイアウト (転置、多桁式、ツリー) に依存しています。 さらに Cosmos DB は、一連のワイヤ プロトコルと API を拡張可能な方法でサポートし、それらをコア データ モデル (1) と論理インデックス レイアウト (2) に効率的に変換することによって複数のデータ モデルをネイティブにサポートできるようにする独自の能力も備えています。

### <a name="can-i-use-multiple-apis-to-access-my-data"></a>複数の API を使用して自分のデータにアクセスできますか?

Azure Cosmos DB は、Microsoft によってグローバルに配布されるマルチモデル データベース サービスです。 マルチモデルでは、Azure Cosmos DB で複数の API と複数のデータ モデルがサポートされますが、API が異なると、ストレージやワイヤ プロトコルに使用されるデータ形式が異なります。 たとえば、SQL では JSON が使用され、MongoDB では BSON が使用され、Table では EDM が使用され、Cassandra では CQL が使用され、Gremlin では JSON 形式が使用されます。 そのため、特定のアカウントでデータにアクセスするときは、常に同じ API を使用することをお勧めします。

相互運用が可能な Gremlin と SQL API を除き、各 API は独立して動作します。

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Azure Cosmos DB は HIPAA に準拠していますか?

はい。Azure Cosmos DB は HIPAA に準拠しています。 HIPAA は、個別に識別できる医療情報の使用、開示、および保護するための要件を確立しています。 詳細については、[Microsoft トラスト センター](/compliance/regulatory/offering-hipaa-hitech)を参照してください。

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Azure Cosmos DB のストレージの制限を教えてください。

Azure Cosmos DB でコンテナーが格納できるデータの合計量に制限はありません。

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Azure Cosmos DB のスループットの制限を教えてください。

Azure Cosmos DB でコンテナーがサポートできるスループットの総量に制限はありません。 基本的な考え方は、十分な数のパーティション キーにワークロードをほぼ均等に分散させることです。

### <a name="are-direct-and-gateway-connectivity-modes-encrypted"></a>ダイレクトとゲートウェイの接続モードは暗号化されますか?

はい、どちらのモードも常に完全に暗号化されます。

### <a name="how-much-does-azure-cosmos-db-cost"></a>Azure Cosmos DB の料金はいくらですか?

詳細については、[Azure Cosmos DB の価格の詳細](https://azure.microsoft.com/pricing/details/cosmos-db/)に関するページをご覧ください。 Azure Cosmos DB の利用料金は、プロビジョニング済みコンテナーの数、コンテナーがオンラインであった時間数、各コンテナーのプロビジョニング スループットによって決まります。

### <a name="is-a-free-account-available"></a>無料アカウントはありますか?

はい。契約することなく、期間限定のアカウントに無料でサインアップできます。 サインアップするには、「[Azure Cosmos DB を無料で試す](https://azure.microsoft.com/try/cosmosdb/)」にアクセスするか、[Try Azure Cosmos DB に関する FAQ](#try-cosmos-db) セクションで詳細を確認してください。

Azure を初めてお使いの場合は、[Azure の無料アカウント](https://azure.microsoft.com/free/)にサインアップできます。このアカウントには、Azure のすべてのサービスを試すための試用期間 (30 日) とクレジットが与えられます。 Visual Studio サブスクリプションを持っている場合は、[無料の Azure クレジット](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を利用して、すべての Azure サービスを使うこともできます。

また、[Azure Cosmos DB Emulator](local-emulator.md) を使用すると、Azure サブスクリプションを作成しなくても、ローカルでのアプリケーションの開発とテストを無料で行うことができます。 Azure Cosmos DB Emulator でのアプリケーションの動作に満足できたら、クラウドでの Azure Cosmos DB アカウントの使用に切り替えることができます。

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Azure Cosmos DB に関するその他の支援を得るにはどうすればよいですか?

技術的な質問をするには、次の 2 つの質問および回答フォーラムのいずれかに投稿することができます。

* [Microsoft Q&A 質問ページ](/answers/topics/azure-cosmos-db.html)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb)。 Stack Overflow は、プログラミングに関する質問に最適です。 質問が[的を得ており](https://stackoverflow.com/help/on-topic)、かつ[その質問を明確で回答可能なものにするようにできるだけ多くの詳細情報が含まれている](https://stackoverflow.com/help/how-to-ask)ことを確認してください。

新機能を要求するには、[User voice](https://feedback.azure.com/forums/263030-azure-cosmos-db) で新しい要求を作成します。

アカウントを使用して問題を修正するには、Azure Portal で[サポート要求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を提出します。

## <a name="try-azure-cosmos-db-subscriptions"></a><a id="try-cosmos-db"></a>Azure Cosmos DB サブスクリプションを試す

現在、一定期間に限り、サブスクリプション不要で、課金も契約もなしで Azure Cosmos DB をご利用いただけます。 Try Azure Cosmos DB サブスクリプションにサインアップするには、「[Azure Cosmos DB を無料で試す](https://azure.microsoft.com/try/cosmosdb/)」にアクセスし、個人用 Microsoft アカウント (MSA) を使用してください。 このサブスクリプションは [Azure 無料試用版](https://azure.microsoft.com/free/)とは別のものであり、Azure 無料試用版または Azure 有料サブスクリプションと併用できます。

Try Azure Cosmos DB サブスクリプションは、Azure Portal で、ユーザー ID に関連付けられた他のサブスクリプションの横に表示されます。

Try Azure Cosmos DB サブスクリプションには、次の条件が適用されます。

* アカウント アクセスは、個人用 Microsoft アカウント (MSA) に許可することができます。 Azure Active Directory (Azure AD) アカウントや会社の Azure AD テナントに属するアカウントは使用しないでください。これらのアカウントには、アクセスの許可をブロックする制限が設定されている場合があります。
* SQL、Gremlin API、Table アカウントのサブスクリプションあたり 1 つの[プロビジョニング スループットのコンテナー](./set-throughput.md#set-throughput-on-a-container)。
* MongoDB アカウントのサブスクリプションあたり最大 3 つの[プロビジョニング スループットのコレクション](./set-throughput.md#set-throughput-on-a-container)。
* サブスクリプションあたり 1 つの[プロビジョニング スループットのデータベース](./set-throughput.md#set-throughput-on-a-database)。 プロビジョニング スループットのデータベースには、任意の数のコンテナーを含めることができます。
* 10 GB のストレージ容量。
* グローバルなレプリケーションは、米国中部、北ヨーロッパ、東南アジアという [Azure リージョン](https://azure.microsoft.com/regions/)で利用可能です
* コンテナー レベルでプロビジョニングされている場合、最大 5 K RU/s のスループット。
* データベース レベルでプロビジョニングされている場合、最大 20 K RU/s のスループット。
* サブスクリプションの有効期限は 30 日後に切れます。これは合計で最大 31 日まで延長できます。 有効期限が切れると、含まれる情報は削除されます。
* Try Azure Cosmos DB アカウントでは Azure サポート チケットを作成できません。ただし、既存のサポート プランをご利用のサブスクライバーにはサポートが提供されます。

## <a name="set-up-azure-cosmos-db"></a>Azure Cosmos DB の設定

### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Azure Cosmos DB にサインアップするにはどうすればよいですか?

Azure Cosmos DB は Azure Portal で利用できます。 まず、Azure サブスクリプションにサインアップします。 サインアップ後、Azure サブスクリプションに Azure Cosmos DB アカウントを追加できます。

### <a name="what-is-a-primary-key"></a>主キーとは

主キーは、アカウントのすべてのリソースにアクセスするためのセキュリティ トークンです。 キーを保持する個人には、データベース アカウント内のすべてのリソースへの読み取り/書き込みアクセスが許可されます。 主キーを配布するときには十分な注意が必要です。 プライマリ主キーとセカンダリ主キーは、[Azure portal][azure-portal] の **[キー]** ブレードで入手できます。 リソース キーの詳細については、「 [アクセス キーを表示、コピー、および再生成する](manage-with-cli.md#list-account-keys)」を参照してください。

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>PreferredLocations として設定できるリージョンを教えてください。

PreferredLocations 値は、Cosmos DB を利用できる Azure リージョンのいずれかに設定できます。 利用可能なリージョンの一覧については、「[Azure リージョン](https://azure.microsoft.com/regions/)」をご覧ください。

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Azure のデータ センター経由で世界中にデータを配布するときに注意すべきことはありますか?

Azure Cosmos DB は、「[Azure リージョン](https://azure.microsoft.com/regions/)」ページに記載されているすべての Azure リージョンにわたって存在します。 Azure Cosmos DB はコア サービスであるため、すべての新しいデータ センターに Azure Cosmos DB が存在することになります。

リージョンを設定するときは、Azure Cosmos DB では主権のあるクラウドと政府機関のクラウドが重視されることに注意してください。 つまり、[独立リージョン](https://azure.microsoft.com/global-infrastructure/)にアカウントを作成した場合、その[独立リージョン](https://azure.microsoft.com/global-infrastructure/)の外部にレプリケートすることはできません。 同様に、外部のアカウントから他の独立した場所へのレプリケーションを有効にすることはできません。

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>コンテナー レベルのスループットのプロビジョニングからデータベース レベルのスループットのプロビジョニングに切り替えられますか? またはその逆はどうですか

コンテナー レベルとデータベース レベルのスループットのプロビジョニングは別個のサービスであり、これらのサービス間で切り替えるには移行元から移行先へのデータの移行が必要になります。 つまり、新しいデータベースまたは新しいコンテナーを作成した後、[Bulk Executor ライブラリ](bulk-executor-overview.md)または [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) を使用してデータを移行する必要があります。

### <a name="does-azure-cosmosdb-support-time-series-analysis"></a>Azure CosmosDB では、時系列分析がサポートされていますか。

はい。Azure CosmosDB では、時系列分析がサポートされています。時系列パターンのサンプルについては、[こちら](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/samples/Patterns)をご覧ください。 このサンプルでは、変更フィードを利用し、時系列データを集計したビューを構築する方法をご確認いただけます。 Spark Streaming やその他のストリーム データ プロセッサを使用することでこの手法を拡張できます。

## <a name="what-are-the-azure-cosmos-db-service-quotas-and-throughput-limits"></a>Azure Cosmos DB にはどのようなサービス クォータとスループット制限がありますか?

詳細については、Azure Cosmos DB の[サービス クォータ](concepts-limits.md)と[コンテナーおよびデータベースごとのスループット制限](set-throughput.md#comparison-of-models)に関する記事を参照してください。

## <a name="frequently-asked-questions-about-sql-api"></a><a id="sql-api-faq"></a>SQL API についてよく寄せられる質問

### <a name="how-do-i-start-developing-against-the-sql-api"></a>SQL API に対する開発を開始するにはどうすればよいですか?

まず、Azure サブスクリプションにサインアップする必要があります。 Azure サブスクリプションにサインアップしたら、SQL API コンテナーを Azure サブスクリプションに追加できます。 Azure Cosmos DB アカウントを追加する手順については、「[Azure Cosmos アカウントを作成する](create-sql-api-dotnet.md#create-account)」を参照してください。

[SDK](sql-api-sdk-dotnet.md) が利用可能です。 開発者は、[RESTful HTTP API](/rest/api/cosmos-db/) を使用して、さまざまなプラットフォームや言語で Azure Cosmos DB リソースを操作することもできます。

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>開発作業を速やかに開始するために、既製のサンプルを利用できますか?

SQL API の [.NET](sql-api-dotnet-samples.md)、[Java](https://github.com/Azure/azure-documentdb-java)、[Node.js](sql-api-nodejs-samples.md)、[Python](sql-api-python-samples.md) の各 SDK のサンプルを GitHub で入手できます。

### <a name="does-the-sql-api-database-support-schema-free-data"></a>SQL API データベースはスキーマフリー データをサポートしていますか?

はい。SQL API では、スキーマ定義やヒントを必要とせずに、アプリケーションが任意の JSON ドキュメントを格納できます。 データは、Azure Cosmos DB SQL クエリ インターフェイスを使用してクエリにすぐに使用できます。

### <a name="does-the-sql-api-support-acid-transactions"></a>SQL API は ACID トランザクションをサポートしていますか?

はい。SQL API では、JavaScript のストアド プロシージャとトリガーとして表現されるクロス ドキュメント トランザクションをサポートしています。 トランザクションは、各コンテナー内の単一のパーティションを対象とし、他の同時実行されるコードおよびユーザー要求から "完全に" 分離された ACID セマンティクスで実行されます。 JavaScript アプリケーション コードのサーバー側実行により例外がスローされた場合は、トランザクション全体がロールバックされます。 

### <a name="what-is-a-container"></a>コンテナーとは何ですか?

コンテナーとは、ドキュメントと、関連する JavaScript アプリケーション ロジックのグループです。 コンテナーは課金対象のエンティティであり、その[コスト](performance-levels.md)は、スループットと使用しているストレージによって決まります。 コンテナーは、1 つ以上のパーティションまたはサーバーにまたがって存在することができ、拡張性があるので、対応できるストレージまたはスループットの量には実質的に制限はありません。

* SQL API では、リソースはコンテナーと呼ばれます。
* Cosmos DB の MongoDB 用 API アカウントの場合、コンテナーは Collection にマップされます。
* Cassandra および Table API アカウントの場合、コンテナーはテーブルにマップされます。
* Gremlin API アカウントの場合、コンテナーはグラフにマップされます。

コンテナーは、Azure Cosmos DB の課金エンティティでもあります。 各コンテナーは、プロビジョニング済みスループットと使用されたストレージ領域に基づいて時間単位で課金されます。 詳細については、「[Azure Cosmos DB の価格](https://azure.microsoft.com/pricing/details/cosmos-db/)」を参照してください。

### <a name="how-do-i-create-a-database"></a>どのようにしてデータベースを作成しますか?

データベースは、[Azure portal](https://portal.azure.com) (「[コンテナーの追加](create-sql-api-java.md#add-a-container)」を参照)、[Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) のいずれか、または [REST API](/rest/api/cosmos-db/) を使用して作成できます。

### <a name="how-do-i-set-up-users-and-permissions"></a>どのようにしてユーザーおよびアクセス許可を設定しますか?

ユーザーとアクセス許可は、[Cosmos DB API SDK](sql-api-sdk-dotnet.md) または [REST API](/rest/api/cosmos-db/) のいずれかを使用して作成できます。

### <a name="does-the-sql-api-support-sql"></a>SQL API は SQL をサポートしていますか?

SQL API アカウントでサポートされる SQL クエリ言語は、SQL Server でサポートされるクエリ機能の強化版サブセットです。 Azure Cosmos DB の SQL クエリ言語は、高度な階層型の関係演算子と、JavaScript ベースのユーザー定義関数 (UDF) による機能拡張を提供します。 JSON 文法では、ラベル付きノードが配置されたツリーとして JSON ドキュメントをモデル化できます。これは、Azure Cosmos DB の自動インデックス作成手法と Azure Cosmos DB の SQL クエリ言語の両方で使用されます。 SQL 文法の使用方法については、[SQL クエリ][query] に関する記事をご覧ください。

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>SQL API は SQL 集計関数をサポートしていますか?

SQL API は、SQL 文法の `COUNT`、`MIN`、`MAX`、`AVG`、`SUM` の各集計関数を使用した、あらゆるスケールでの低待機時間の集計をサポートしています。 詳細については、「[集計関数](sql-query-aggregate-functions.md)」をご覧ください。

### <a name="how-does-the-sql-api-provide-concurrency"></a>SQL API はどのようにしてコンカレンシーを提供しますか?

SQL API は、HTTP エンティティ タグ (ETag) によるオプティミスティック コンカレンシー (OCC) をサポートしています。 すべての SQL API リソースに ETag があり、ドキュメントが更新されるたびにサーバーで ETag が設定されます。 すべての応答メッセージに ETag ヘッダーと現在の値が含まれます。 ETag を If-Match ヘッダーと共に使用することで、サーバーはリソースを更新する必要があるかどうかを判断できるようになります。 If-Match 値は、チェック対象の ETag 値です。 対象の ETag 値がサーバーの ETag 値と一致する場合に、リソースが更新されます。 ETag が最新ではない場合、サーバーは操作を拒否して "HTTP 412 Precondition failure" 応答コードを返します。 この場合、クライアントは、リソースを再フェッチしてリソースの最新の ETag 値を取得します。 また、ETag を If-None-Match ヘッダーと共に使用すると、リソースの再フェッチが必要かどうかを判断できます。

.NET でオプティミスティック コンカレンシーを使用するには、 [AccessCondition](/dotnet/api/microsoft.azure.documents.client.accesscondition) クラスを使用します。 .NET サンプルについては、GitHub にある DocumentManagement サンプルの [Program.cs](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/DocumentManagement/Program.cs) を参照してください。

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>どのようにして SQL API のトランザクションを実行しますか?

SQL API は、JavaScript のストアド プロシージャとトリガーによる、統合された言語のトランザクションをサポートしています。 スクリプト内のすべてのデータベース操作は、スナップショット分離下で実行されます。 単一パーティション コンテナーの場合は、コンテナーが実行対象となります。 コンテナーがパーティション分割されている場合は、コンテナー内の同じパーティション キー値を持つドキュメントが実行対象となります。 ドキュメント バージョン (ETag) のスナップショットは、トランザクションの開始時に取得され、スクリプトが成功された場合のみコミットされます。 JavaScript がエラーをスローした場合、トランザクションはロールバックされます。 詳細については、「[Azure Cosmos DB のサーバー側プログラミング](stored-procedures-triggers-udfs.md)」を参照してください。

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>ドキュメントを Cosmos DB に一括挿入するにはどうすればよいですか?

Azure Cosmos DB へのドキュメントの一括挿入は、次のいずれかの方法で実行できます。

* 一括実行ツール。[一括実行 .NET ライブラリの使用](bulk-executor-dot-net.md)および[一括実行 Java ライブラリの使用](bulk-executor-java.md)に関する記事で説明されています。
* データ移行ツール。[Azure Cosmos DB 用のデータベース移行ツール](import-data.md)に関する記事で説明されています。
* ストアド プロシージャ。[Azure Cosmos DB のサーバー側 JavaScript プログラミング](stored-procedures-triggers-udfs.md)に関する記事で説明されています。

### <a name="does-the-sql-api-support-resource-link-caching"></a>SQL API はリソース リンク キャッシュをサポートしていますか?

はい。Azure Cosmos DB は RESTful サービスであるため、リソース リンクは不変であり、キャッシュできます。 SQL API クライアントでは、リソースのようなドキュメントやコンテナーに対する読み取りに "If-None-Match" ヘッダーを指定し、サーバー バージョンが変更されたらローカル コピーを更新できます。

### <a name="is-a-local-instance-of-sql-api-available"></a>SQL API のローカル インスタンスは使用できますか?

はい。 [Azure Cosmos DB Emulator](local-emulator.md) には、Cosmos DB サービスの高忠実度エミュレーションが用意されています。 JSON ドキュメントの作成とクエリ、コレクションのプロビジョニングとスケーリング、ストアド プロシージャとトリガーの実行のサポートなど、Azure Cosmos DB と同じ機能がサポートされています。 Azure Cosmos DB Emulator を使用してアプリケーションの開発とテストを行い、Azure Cosmos DB の接続エンドポイントの構成を 1 つ変更するだけで、世界規模で Azure にデプロイできます。

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>ポータルのデータ エクスプローラーから表示したとき、ドキュメント内の長い浮動小数点値が丸められるのはなぜですか?

これは、JavaScript の制限です。 JavaScript では IEEE 754 で指定されている倍精度浮動小数点形式の値が使用されるため、安全に保持できるのは -(2<sup>53</sup> - 1) から 2<sup>53</sup> - 1 (つまり 9007199254740991) の範囲の値だけです。

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>アクセス許可はオブジェクト階層のどこで許可されますか?

ResourceTokens の使用によるアクセス許可の作成は、コンテナー レベルとその子孫 (ドキュメントや添付ファイルなど) で許可されます。 これは、データベースまたはアカウント レベルでのアクセス許可の作成は、現時点では許可されないことを示唆しています。

[azure-portal]: https://portal.azure.com
[query]: ./sql-query-getting-started.md

## <a name="next-steps"></a>次のステップ

他の API に関してよく寄せられる質問については、以下を参照してください。

* [Azure Cosmos DB の MongoDB 用 API](mongodb-api-faq.md) についてよく寄せられる質問
* [Azure Cosmos DB での Gremlin API](gremlin-api-faq.md) についてよく寄せられる質問
* [Azure Cosmos DB での Cassandra API](cassandra-faq.md) についてよく寄せられる質問
* [Azure Cosmos DB での Table API](table-api-faq.md) についてよく寄せられる質問