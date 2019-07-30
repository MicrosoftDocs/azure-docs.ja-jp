---
title: Azure Cosmos DB のさまざまな API についてよく寄せられる質問
description: グローバル分散型のマルチモデル データベース サービスである Azure Cosmos DB についてよく寄せられる質問の回答が得られます。 容量、パフォーマンス レベル、スケーリングについて説明します。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 89c04a55138d57fd2ff37a96f2bc92b12d1780d9
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341227"
---
# <a name="frequently-asked-questions-about-different-apis-in-azure-cosmos-db"></a>Azure Cosmos DB のさまざまな API についてよく寄せられる質問

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Azure Cosmos DB の一般的なユース ケースを教えてください。

Azure Cosmos DB は、自動スケール、予測可能なパフォーマンス、ミリ秒レベルの高速応答時間、スキーマフリー データに対してクエリを実行できることが重要である、新しい Web、モバイル、ゲーム、IoT の各アプリケーションに適しています。 Azure Cosmos DB は迅速な開発に役立ち、アプリケーション データ モデルの継続的な反復をサポートします。 ユーザーが生成したコンテンツとデータを管理するアプリケーションは、[Azure Cosmos DB の一般的なユース ケース](use-cases.md)です。

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Azure Cosmos DB では、予測可能なパフォーマンスをどのようにして実現していますか?

[要求ユニット](request-units.md) (RU) とは、Azure Cosmos DB におけるスループットの単位です。 1 RU のスループットは、1 KB のドキュメントを取得するスループットに相当します。 Azure Cosmos DB におけるすべての操作 (読み取り、書き込み、SQL クエリ、ストアド プロシージャの実行など) には、操作を完了するために必要なスループットに基づいて明確な RU 値が設定されています。 CPU、IO、メモリや、これらがアプリケーションのスループットに及ぼす影響について考えるのではなく、RU という 1 つの単位を基にして考えることができます。

各 Azure Cosmos コンテナーは、1 秒あたりのスループットを表す RU を単位として、プロビジョニング スループットを使用して構成できます。 あらゆる規模のアプリケーションで、個々の要求のベンチマークを実行して RU 値を測定し、すべての要求の要求ユニットの合計に対処できるようにコンテナーをプロビジョニングできます。 アプリケーションのニーズの進化に合わせて、コンテナーのスループットをスケールアップまたはスケールダウンすることもできます。 要求ユニットの詳細とコンテナーのニーズを判断する方法については、[スループット計算ツール](https://www.documentdb.com/capacityplanner)をお試しください。

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Azure Cosmos DB は、キー/値、多桁式、ドキュメント、グラフなどのさまざまなデータ モデルをどのようにサポートしていますか?

キー/値 (テーブル)、多桁式、ドキュメント、およびグラフ データ モデルは、Azure Cosmos DB のベースである ARS (アトム、レコード、およびシーケンス) 設計のため、すべてネイティブにサポートされています。 アトム、レコード、およびシーケンスは、さまざまなデータ モデルに容易にマップしたり投影したりできます。 モデルのサブセット用の API は今すぐ使用でき (SQL、MongoDB、Table、Gremlin)、追加のデータ モデルに固有のその他の API は将来使用可能になります。

Azure Cosmos DB は、開発者にスキーマやセカンダリ インデックスを要求することなく、取り込んだすべてのデータを自動的にインデックス作成できるスキーマ独立型インデックス作成エンジンを備えています。 このエンジンは、インデックスおよびクエリ処理サブシステムから記憶域のレイアウトを分離する一連の論理インデックス レイアウト (転置、多桁式、ツリー) に依存しています。 さらに Cosmos DB は、一連のワイヤ プロトコルと API を拡張可能な方法でサポートし、それらをコア データ モデル (1) と論理インデックス レイアウト (2) に効率的に変換することによって複数のデータ モデルをネイティブにサポートできるようにする独自の能力も備えています。

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Azure Cosmos DB は HIPAA に準拠していますか?

はい。Azure Cosmos DB は HIPAA に準拠しています。 HIPAA は、個別に識別できる医療情報の使用、開示、および保護するための要件を確立しています。 詳細については、[Microsoft セキュリティ センター](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA)を参照してください。

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

* [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb)。 Stack Overflow は、プログラミングに関する質問に最適です。 質問が[的を得ており](https://stackoverflow.com/help/on-topic)、かつ[その質問を明確で回答可能なものにするようにできるだけ多くの詳細情報が含まれている](https://stackoverflow.com/help/how-to-ask)ことを確認してください。

新機能を要求するには、[User voice](https://feedback.azure.com/forums/263030-azure-cosmos-db) で新しい要求を作成します。

アカウントを使用して問題を修正するには、Azure Portal で[サポート要求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を提出します。

その他の質問は、[askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) でチームに送信できます。ただし、これはテクニカル サポートの別名ではありません。

## <a id="try-cosmos-db"></a>Azure Cosmos DB サブスクリプションを試す

現在、一定期間に限り、サブスクリプション不要で、課金も契約もなしで Azure Cosmos DB をご利用いただけます。 Try Azure Cosmos DB サブスクリプションにサインアップするには、「[Azure Cosmos DB を無料で試す](https://azure.microsoft.com/try/cosmosdb/)」にアクセスしてください。 このサブスクリプションは [Azure 無料試用版](https://azure.microsoft.com/free/)とは別のものであり、Azure 無料試用版または Azure 有料サブスクリプションと併用できます。

Try Azure Cosmos DB サブスクリプションは、Azure Portal で、ユーザー ID に関連付けられた他のサブスクリプションの横に表示されます。

Try Azure Cosmos DB サブスクリプションには、次の条件が適用されます。

* SQL、Gremlin API、Table アカウントのサブスクリプションあたり 1 つの[プロビジョニング スループットのコンテナー](./set-throughput.md#set-throughput-on-a-container)。
* MongoDB アカウントのサブスクリプションあたり最大 3 つの[プロビジョニング スループットのコレクション](./set-throughput.md#set-throughput-on-a-container)。
* サブスクリプションあたり 1 つの[プロビジョニング スループットのデータベース](./set-throughput.md#set-throughput-on-a-database)。 プロビジョニング スループットのデータベースには、任意の数のコンテナーを含めることができます。
* 10 GB のストレージ容量。
* グローバルなレプリケーションは、米国中部、北ヨーロッパ、東南アジアという [Azure リージョン](https://azure.microsoft.com/regions/)で利用可能です
* コンテナー レベルでプロビジョニングされている場合、最大 5 K RU/s のスループット。
* データベース レベルでプロビジョニングされている場合、最大 20 K RU/s のスループット。
* サブスクリプションの有効期限は 30 日後に切れます。これは合計で最大 31 日まで延長できます。
* Try Azure Cosmos DB アカウントでは Azure サポート チケットを作成できません。ただし、既存のサポート プランをご利用のサブスクライバーにはサポートが提供されます。

## <a name="set-up-azure-cosmos-db"></a>Azure Cosmos DB の設定

### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Azure Cosmos DB にサインアップするにはどうすればよいですか?

Azure Cosmos DB は Azure Portal で利用できます。 まず、Azure サブスクリプションにサインアップします。 サインアップ後、Azure サブスクリプションに Azure Cosmos DB アカウントを追加できます。

### <a name="what-is-a-master-key"></a>マスター キーとは何ですか?

マスター キーは、アカウントのすべてのリソースにアクセスするためのセキュリティ トークンです。 キーを保持する個人には、データベース アカウント内のすべてのリソースへの読み取り/書き込みアクセスが許可されます。 マスター キーを配布するときには十分な注意が必要です。 プライマリ マスター キーとセカンダリ マスター キーは、[Azure Portal][azure-portal] の **[キー]** ブレードで入手できます。 リソース キーの詳細については、「 [アクセス キーを表示、コピー、および再生成する](manage-with-cli.md#list-account-keys)」を参照してください。

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>PreferredLocations として設定できるリージョンを教えてください。

PreferredLocations 値は、Cosmos DB を利用できる Azure リージョンのいずれかに設定できます。 利用可能なリージョンの一覧については、「[Azure リージョン](https://azure.microsoft.com/regions/)」をご覧ください。

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Azure のデータ センター経由で世界中にデータを配布するときに注意すべきことはありますか?

Azure Cosmos DB は、「[Azure リージョン](https://azure.microsoft.com/regions/)」ページに記載されているすべての Azure リージョンにわたって存在します。 Azure Cosmos DB はコア サービスであるため、すべての新しいデータ センターに Azure Cosmos DB が存在することになります。

リージョンを設定するときは、Azure Cosmos DB では主権のあるクラウドと政府機関のクラウドが重視されることに注意してください。 つまり、[独立リージョン](https://azure.microsoft.com/global-infrastructure/)にアカウントを作成した場合、その[独立リージョン](https://azure.microsoft.com/global-infrastructure/)の外部にレプリケートすることはできません。 同様に、外部のアカウントから他の独立した場所へのレプリケーションを有効にすることはできません。

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>コンテナー レベルのスループットのプロビジョニングからデータベース レベルのスループットのプロビジョニングに切り替えられますか? またはその逆はどうですか

コンテナー レベルとデータベース レベルのスループットのプロビジョニングは別個のサービスであり、これらのサービス間で切り替えるには移行元から移行先へのデータの移行が必要になります。 つまり、新しいデータベースまたは新しいコレクションを作成した後、[Bulk Executor ライブラリ](bulk-executor-overview.md)または [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) を使用してデータを移行する必要があります。

### <a name="does-azure-cosmosdb-support-time-series-analysis"></a>Azure CosmosDB では、時系列分析がサポートされていますか。

はい。Azure CosmosDB では、時系列分析がサポートされています。時系列パターンのサンプルについては、[こちら](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/samples/Patterns)をご覧ください。 このサンプルでは、変更フィードを利用し、時系列データを集計したビューを構築する方法をご確認いただけます。 Spark Streaming やその他のストリーム データ プロセッサを使用することでこの手法を拡張できます。

## <a name="sql-api"></a>SQL API

### <a name="how-do-i-start-developing-against-the-sql-api"></a>SQL API に対する開発を開始するにはどうすればよいですか?

まず、Azure サブスクリプションにサインアップする必要があります。 Azure サブスクリプションにサインアップしたら、SQL API コンテナーを Azure サブスクリプションに追加できます。 Azure Cosmos DB アカウントを追加する手順については、[Azure Cosmos DB データベース アカウントの作成](create-sql-api-dotnet.md#create-account)に関するセクションをご覧ください。

[SDK](sql-api-sdk-dotnet.md) が利用可能です。 開発者は、[RESTful HTTP API](/rest/api/cosmos-db/) を使用して、さまざまなプラットフォームや言語で Azure Cosmos DB リソースを操作することもできます。

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>開発作業を速やかに開始するために、既製のサンプルを利用できますか?

SQL API の [.NET](sql-api-dotnet-samples.md)、[Java](https://github.com/Azure/azure-documentdb-java)、[Node.js](sql-api-nodejs-samples.md)、[Python](sql-api-python-samples.md) の各 SDK のサンプルを GitHub で入手できます。

### <a name="does-the-sql-api-database-support-schema-free-data"></a>SQL API データベースはスキーマフリー データをサポートしていますか?

はい。SQL API では、スキーマ定義やヒントを必要とせずに、アプリケーションが任意の JSON ドキュメントを格納できます。 データは、Azure Cosmos DB SQL クエリ インターフェイスを使用してクエリにすぐに使用できます。

### <a name="does-the-sql-api-support-acid-transactions"></a>SQL API は ACID トランザクションをサポートしていますか?

はい。SQL API では、JavaScript のストアド プロシージャとトリガーとして表現されるクロス ドキュメント トランザクションをサポートしています。 トランザクションは、各コンテナー内の単一のパーティションを対象とし、他の同時実行されるコードおよびユーザー要求から "完全に" 分離された ACID セマンティクスで実行されます。 JavaScript アプリケーション コードのサーバー側実行により例外がスローされた場合は、トランザクション全体がロールバックされます。 

### <a name="what-is-a-container"></a>コンテナーとは何ですか?

コンテナーとは、ドキュメントと、関連する JavaScript アプリケーション ロジックのグループです。 コンテナーは課金対象のエンティティであり、その[コスト](performance-levels.md)は、スループットと使用しているストレージによって決まります。 コンテナーは、1 つ以上のパーティションまたはサーバーにまたがって存在することができ、拡張性があるので、対応できるストレージまたはスループットの量には実質的に制限はありません。

* SQL API アカウントと Cosmos DB の MongoDB 用 API アカウントの場合、コンテナーはコレクションにマップされます。
* Cassandra および Table API アカウントの場合、コンテナーはテーブルにマップされます。
* Gremlin API アカウントの場合、コンテナーはグラフにマップされます。

コンテナーは、Azure Cosmos DB の課金エンティティでもあります。 各コンテナーは、プロビジョニング済みスループットと使用されたストレージ領域に基づいて時間単位で課金されます。 詳細については、「[Azure Cosmos DB の価格](https://azure.microsoft.com/pricing/details/cosmos-db/)」を参照してください。

### <a name="how-do-i-create-a-database"></a>どのようにしてデータベースを作成しますか?

データベースは、[Azure Portal](https://portal.azure.com) (「[コレクションの追加](create-sql-api-dotnet.md#create-collection-database)」を参照)、[Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) のいずれか、または [REST API](/rest/api/cosmos-db/) を使用して作成できます。

### <a name="how-do-i-set-up-users-and-permissions"></a>どのようにしてユーザーおよびアクセス許可を設定しますか?

ユーザーとアクセス許可は、[Cosmos DB API SDK](sql-api-sdk-dotnet.md) または [REST API](/rest/api/cosmos-db/) のいずれかを使用して作成できます。

### <a name="does-the-sql-api-support-sql"></a>SQL API は SQL をサポートしていますか?

SQL API アカウントでサポートされる SQL クエリ言語は、SQL Server でサポートされるクエリ機能の強化版サブセットです。 Azure Cosmos DB の SQL クエリ言語は、高度な階層型の関係演算子と、JavaScript ベースのユーザー定義関数 (UDF) による機能拡張を提供します。 JSON 文法では、ラベル付きノードが配置されたツリーとして JSON ドキュメントをモデル化できます。これは、Azure Cosmos DB の自動インデックス作成手法と Azure Cosmos DB の SQL クエリ言語の両方で使用されます。 SQL 文法の使用方法については、[SQL クエリ][query] に関する記事をご覧ください。

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>SQL API は SQL 集計関数をサポートしていますか?

SQL API は、SQL 文法の `COUNT`、`MIN`、`MAX`、`AVG`、`SUM` の各集計関数を使用した、あらゆるスケールでの低待機時間の集計をサポートしています。 詳細については、「[集計関数](sql-query-aggregates.md)」をご覧ください。

### <a name="how-does-the-sql-api-provide-concurrency"></a>SQL API はどのようにして同時実行を提供しますか?

SQL API は、HTTP エンティティ タグ (ETag) によるオプティミスティック コンカレンシー (OCC) をサポートしています。 すべての SQL API リソースに ETag があり、ドキュメントが更新されるたびにサーバーで ETag が設定されます。 すべての応答メッセージに ETag ヘッダーと現在の値が含まれます。 ETag を If-Match ヘッダーと共に使用することで、サーバーはリソースを更新する必要があるかどうかを判断できるようになります。 If-Match 値は、チェック対象の ETag 値です。 対象の ETag 値がサーバーの ETag 値と一致する場合に、リソースが更新されます。 ETag が最新ではない場合、サーバーは操作を拒否して "HTTP 412 Precondition failure" 応答コードを返します。 この場合、クライアントは、リソースを再フェッチしてリソースの最新の ETag 値を取得します。 また、ETag を If-None-Match ヘッダーと共に使用すると、リソースの再フェッチが必要かどうかを判断できます。

.NET でオプティミスティック コンカレンシーを使用するには、 [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) クラスを使用します。 .NET サンプルについては、GitHub にある DocumentManagement サンプルの [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) を参照してください。

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>どのようにして SQL API のトランザクションを実行しますか?

SQL API は、JavaScript のストアド プロシージャとトリガーによる、統合された言語のトランザクションをサポートしています。 スクリプト内のすべてのデータベース操作は、スナップショット分離下で実行されます。 単一パーティション コレクションの場合は、コレクションが実行対象となります。 コレクションがパーティション分割されている場合は、コレクション内の同じパーティション キー値を持つドキュメントが実行対象となります。 ドキュメント バージョン (ETag) のスナップショットは、トランザクションの開始時に取得され、スクリプトが成功された場合のみコミットされます。 JavaScript がエラーをスローした場合、トランザクションはロールバックされます。 詳細については、「[Azure Cosmos DB のサーバー側プログラミング](stored-procedures-triggers-udfs.md)」を参照してください。

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>ドキュメントを Cosmos DB に一括挿入するにはどうすればよいですか?

Azure Cosmos DB へのドキュメントの一括挿入は、次のいずれかの方法で実行できます。

* 一括実行ツール。[一括実行 .NET ライブラリの使用](bulk-executor-dot-net.md)および[一括実行 Java ライブラリの使用](bulk-executor-java.md)に関する記事で説明されています。
* データ移行ツール。[Azure Cosmos DB 用のデータベース移行ツール](import-data.md)に関する記事で説明されています。
* ストアド プロシージャ。[Azure Cosmos DB のサーバー側 JavaScript プログラミング](stored-procedures-triggers-udfs.md)に関する記事で説明されています。

### <a name="does-the-sql-api-support-resource-link-caching"></a>SQL API はリソース リンク キャッシュをサポートしていますか?

はい。Azure Cosmos DB は RESTful サービスであるため、リソース リンクは不変であり、キャッシュできます。 SQL API クライアントは、ドキュメントやコレクションなどのリソースに対する読み取りに "If-None-Match" ヘッダーを指定し、サーバー バージョンが変更されたらローカル コピーを更新できます。

### <a name="is-a-local-instance-of-sql-api-available"></a>SQL API のローカル インスタンスは使用できますか?

はい。 [Azure Cosmos DB Emulator](local-emulator.md) には、Cosmos DB サービスの高忠実度エミュレーションが用意されています。 JSON ドキュメントの作成とクエリ、コレクションのプロビジョニングとスケーリング、ストアド プロシージャとトリガーの実行のサポートなど、Azure Cosmos DB と同じ機能がサポートされています。 Azure Cosmos DB Emulator を使用してアプリケーションの開発とテストを行い、Azure Cosmos DB の接続エンドポイントの構成を 1 つ変更するだけで、世界規模で Azure にデプロイできます。

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>ポータルのデータ エクスプローラーから表示したとき、ドキュメント内の長い浮動小数点値が丸められるのはなぜですか?

これは、JavaScript の制限です。 JavaScript では IEEE 754 で指定されている倍精度浮動小数点形式の値が使用されるため、安全に保持できるのは -(2<sup>53</sup> - 1) から 2<sup>53</sup> - 1 (つまり 9007199254740991) の範囲の値だけです。

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>アクセス許可はオブジェクト階層のどこで許可されますか?

ResourceTokens の使用によるアクセス許可の作成は、コンテナー レベルとその子孫 (ドキュメントや添付ファイルなど) で許可されます。 これは、データベースまたはアカウント レベルでのアクセス許可の作成は、現時点では許可されないことを示唆しています。

## <a name="azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB の MongoDB 用 API

### <a name="what-is-the-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB の MongoDB 用 API とは何ですか?

Azure Cosmos DB の MongoDB 用 API は、コミュニティでサポートされる MongoDB 用の既存の SDK とドライバーを使用して、アプリケーションがネイティブの Azure Cosmos DB データベース エンジンと簡単かつ透過的に通信できるようにするワイヤ プロトコル互換レイヤーです。 開発者は、既存の MongoDB ツールチェーンとスキルを使って、Azure Cosmos DB を活用するアプリケーションを構築できるようになりました。 これにより、マルチマスター レプリケーションによるグローバル配信、自動インデックス作成、バックアップ メンテナンス、利用料金に基づくサービス レベル アグリーメント (SLA) など、Azure Cosmos DB の独自の機能によるメリットが得られます。

### <a name="how-do-i-connect-to-my-database"></a>データベースに接続する方法は?

Azure Cosmos DB の MongoDB 用 API で Cosmo データベースに接続する最も簡単な方法は、[Azure Portal](https://portal.azure.com) を使用することです。 アカウントに移動し、左側のナビゲーション メニューで **[クイック スタート]** をクリックします。 クイック スタートは、コード スニペットを取得してデータベースに接続するための最善の方法です。

Azure Cosmos DB では、厳密なセキュリティ要件と基準が適用されます。 Azure Cosmos DB アカウントでは、SSL による認証とセキュリティで保護された通信が要求されるので、必ず TLSv1.2 を使用してください。

詳細については、[Azure Cosmos DB の MongoDB 用 API で Cosmo データベースに接続する](connect-mongodb-account.md)方法に関するページを参照してください。

### <a name="are-there-additional-error-codes-that-i-need-to-deal-with-while-using-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB の MongoDB 用 API を使用するとき、対処しなければならないエラー コードは他にもありますか。

Azure Cosmos DB の MongoDB 用 API には、一般的な MongoDB エラー コードのほかに、次のような独自のエラー コードがあります。

| Error               | コード  | 説明  | 解決策  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | 使用された要求ユニットの合計数が、コレクションのプロビジョニング済みの要求ユニット レートを超えたために調整されました。 | Azure Portal からコンテナーまたはコンテナーのセットに割り当てられているスループットをスケーリングするか、再試行することを検討してください。 |
| ExceededMemoryLimit | 16501 | マルチテナント サービスとしての操作が、クライアントのメモリ配分を超えました。 | より制限の厳しいクエリ条件によって操作のスコープを減らすか、[Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) からサポートに連絡してください。 <br><br>例: <em>&nbsp;&nbsp;&nbsp;&nbsp;db.getCollection('users').aggregate([<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$match: {name:"Andy"}}, <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$sort: {age: -1}}<br>&nbsp;&nbsp;&nbsp;&nbsp;])</em>) |

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB 向けの Simba ドライバーは Azure Cosmos DB の MongoDB 用 API で使用できますか。

はい。Simba の Mongo ODBC ドライバーは Azure Cosmos DB の MongoDB 用 API で使用できます。

## <a id="table"></a>Table API

### <a name="how-can-i-use-the-table-api-offering"></a>Table API を使うにはどうすればよいですか?

Azure Cosmos DB Table API は [Azure Portal][azure-portal] で利用できます。 まず、Azure サブスクリプションにサインアップする必要があります。 サインアップしたら、Azure サブスクリプションに Azure Cosmos DB Table API アカウントを追加し、アカウントにテーブルを追加できます。

サポートされている言語および関連するクイックスタートは、「[Azure Cosmos DB の概要: Table API](table-introduction.md)」で確認できます。

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>Table API を使うには新しい SDK が必要ですか?

いいえ、既存のストレージ SDK でまだ動作するはずです。 ただし、最善のサポートと、多くの場合に優れたパフォーマンスを得るために、常に最新の SDK を使うことをお勧めします。 使うことができる言語の一覧については、「[Azure Cosmos DB の概要: Table API](table-introduction.md)」をご覧ください。

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Table API と Azure Table Storage の動作はどのような点が異なりますか?

これまで Azure Table Storage を使っていたユーザーが Azure Cosmos DB Table API でテーブルを作成する場合、知っておく必要のある動作の違いがいくつかあります。

* Azure Cosmos DB Table API ではパフォーマンスを保証するために予約容量モデルが使われていますが、これは、テーブルが作成されると直ちに、たとえ容量が使われていない場合でも、容量に対して課金されることを意味します。 Azure Table Storage では、使用された容量に対してのみ課金されます。 Table API では 99 パーセンタイルで 10 ミリ秒の読み取り SLA と 15 ミリ秒の書き込み SLA が提供されるのに対し、Azure Table Storage で提供される SLA が 10 秒であるのは、このためです。 ただし、その結果として、Table API のテーブルでは、要求が含まれない空のテーブルであっても、Azure Cosmos DB が提供する SLA でテーブルへの要求を処理できる容量を確保するためにコストがかかります。
* Table API によって返されるクエリ結果は、Azure Table Storage のようなパーティション キー/行キーの順序にはなりません。
* 行キーに許される最大長は 255 バイトです。
* バッチの最大容量は 2 MB のみです。
* CORS は現在サポートされていません。
* Azure Table Storage ではテーブル名は大文字小文字が区別されませんが、Azure Cosmos DB Table API では区別されます。
* 現在、バイナリ フィールドなどのエンコード情報に対する Azure Cosmos DB の内部形式の一部は、それほど効率的ではありません。 そのため、データ サイズの予期しない制限が発生する可能性があります。 たとえば、現在、エンコードによってデータ サイズが増加するため、フル 1 メガのテーブル エンティティを使用してバイナリ データを格納することはできません。
* エンティティ プロパティ名 "ID" は現在サポートされていません。
* TableQuery TakeCount は 1000 に制限されません。

REST API に関しては、Azure Cosmos DB Table API によってサポートされないエンドポイント/クエリ オプションがいくつかあります。

| REST メソッド | REST エンドポイント/クエリ オプション | ドキュメントの URL | 説明 |
| ------------| ------------- | ---------- | ----------- |
| GET、PUT | /?restype=service@comp=properties| 「[Set Table Service Properties](https://docs.microsoft.com/rest/api/storageservices/set-table-service-properties)」(Table Service のプロパティを設定する) および「[Get Table Service Properties](https://docs.microsoft.com/rest/api/storageservices/get-table-service-properties)」(Table Service のプロパティを取得する) | このエンドポイントは、CORS ルールの設定、ストレージ分析の構成、ログ記録の設定に使われます。 CORS は現在サポートされておらず、Azure Cosmos DB での分析とログ記録の処理は Azure Storage Table とは異なります。 |
| OPTIONS | /\<table-resource-name> | 「[Pre-flight CORS table request](https://docs.microsoft.com/rest/api/storageservices/preflight-table-request)」(プレフライト CORS テーブル要求) | これは、Azure Cosmos DB が現在サポートしていない CORS の一部です。 |
| GET | /?restype=service@comp=stats | 「[Get Table Service Stats](https://docs.microsoft.com/rest/api/storageservices/get-table-service-stats)」(Table Service の統計情報を取得する) | プライマリとセカンダリの間でデータがレプリケートされる速度の情報を提供します。 Cosmos DB ではレプリケーションは書き込みの一部なので、これは必要ありません。 |
| GET、PUT | /mytable?comp=acl | 「[Get Table ACL](https://docs.microsoft.com/rest/api/storageservices/get-table-acl)」(テーブルの ACL を取得する) および[Set Table ACL](https://docs.microsoft.com/rest/api/storageservices/set-table-acl)」(テーブルの ACL を設定する) | Shared Access Signature (SAS) の管理に使われる保存されたアクセス ポリシーを取得および設定します。 SAS はサポートされていますが、設定と管理の方法は異なります。 |

さらに、Azure Cosmos DB Table API は JSON 形式のみをサポートし、ATOM はサポートしません。

Azure Cosmos DB は Shared Access Signature (SAS) をサポートしていますが、一部のポリシーはサポートされていません。具体的には、新しいテーブルを作成する権限などの管理操作に関連するものです。

特に .NET SDK には、Azure Cosmos DB が現在サポートしていないクラスとメソッドがいくつかあります。

| クラス | サポートされていないメソッド |
|-------|-------- |
| CloudTableClient | \*ServiceProperties* |
|                  | \*ServiceStats* |
| CloudTable | SetPermissions* |
|            | GetPermissions* |
| TableServiceContext | * (このクラスは非推奨です) |
| TableServiceEntity | " " |
| TableServiceExtensions | " " |
| TableServiceQuery | " " |

これらの相違点のいずれかがプロジェクトで問題になる場合は、[askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) でお知らせください。

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>SDK やバグに関するフィードバックを提供するにはどうすればよいですか?

次のいずれかの方法でフィードバックをお寄せください。

* [ユーザーの声](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb)。 Stack Overflow は、プログラミングに関する質問に最適です。 質問が[的を得ており](https://stackoverflow.com/help/on-topic)、かつ[その質問を明確で回答可能なものにするようにできるだけ多くの詳細情報が含まれている](https://stackoverflow.com/help/how-to-ask)ことを確認してください。

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Table API に接続するときに使う必要がある接続文字列を教えてください。

接続文字列は次のとおりです。

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```

接続文字列は、Azure Portal の [接続文字列] ページから取得できます。

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Table API の .NET SDK で、要求オプションの構成設定をオーバーライドするにはどうすればよいですか?

一部の設定は CreateCloudTableClient メソッドで処理され、他の設定はクライアント アプリケーションの appSettings セクションの app.config で処理されます。 構成設定については、「[Azure Cosmos DB の機能](tutorial-develop-table-dotnet.md)」をご覧ください。

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>既存の Azure Table Storage SDK を使っている顧客に関する変更はありますか?

なし。 既存の Azure Table Storage SDK を使用する既存または新規のお客様を対象とする変更はありません。

### <a name="how-do-i-view-table-data-thats-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Table API で使うために、Azure Cosmos DB に格納されているテーブル データを表示するにはどうすればよいですか?

Azure Portal を使用してデータを参照できます。 また、Table API コードまたは次の回答で説明するツールを使うこともできます。

### <a name="which-tools-work-with-the-table-api"></a>Table API で動作するのはどのツールですか?

[Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) を使うことができます。

以前に指定した形式の接続文字列を取得する柔軟性を備えたツールは、新しい Table API に対応できます。 テーブル ツールの一覧については、「[Azure Storage クライアント ツール](../storage/common/storage-explorers.md)」をご覧ください。

### <a name="is-the-concurrency-on-operations-controlled"></a>操作の同時実行は制御されますか?

はい。オプティミスティック コンカレンシーは、ETag メカニズムを使用して提供されます。

### <a name="is-the-odata-query-model-supported-for-entities"></a>エンティティの OData クエリ モデルはサポートされていますか?

はい。Table API は、OData クエリと LINQ クエリをサポートしています。

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>同じアプリケーションで Azure Table Storage と Azure Cosmos DB Table API に同時に接続できますか?

はい。同時に接続するには、CloudTableClient の 2 つのインスタンスを作成し、各インスタンスで接続文字列を使用して独自の URI を参照します。

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>既存の Azure Table Storage アプリケーションをこのサービスに移行するにはどうすればよいですか?

[AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) と [Azure Cosmos DB データ移行ツール](import-data.md)の両方がサポートされています。

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>たとえば、最初は *n* GB のデータが時間の経過と共に 1 TB に増加した場合、このサービスではストレージ サイズはどのように拡張されるのですか?

Azure Cosmos DB は、水平スケーリングを使用して無制限のストレージを提供するように設計されています。 このサービスは、ストレージを監視し、ストレージを効率的に増やすことができます。

### <a name="how-do-i-monitor-the-table-api-offering"></a>Table API を監視するにはどうすればよいですか?

Table API の **[メトリック]** ウィンドウを使って、要求とストレージ使用量を監視できます。

### <a name="how-do-i-calculate-the-throughput-i-require"></a>必要なスループットを計算するにはどうすればよいですか?

容量見積もりツールを使用して、操作に必要な TableThroughput を計算できます。 詳細については、「[Estimate Request Units and Data Storage (要求ユニットとデータ ストレージの見積もり)](https://www.documentdb.com/capacityplanner)」をご覧ください。 一般に、エンティティを JSON として示し、操作数を指定します。

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>エミュレーターで Table API SDK をローカルに使うことができますか?

現時点ではありません。

### <a name="can-my-existing-application-work-with-the-table-api"></a>既存のアプリケーションを Table API で動作させることはできますか?

はい。同じ API がサポートされています。

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-dont-want-to-use-the-table-api-features"></a>Table API の機能を使わない場合、既存の Azure Table Storage アプリケーションを SDK に移行する必要はありますか?

いいえ。中断することなく、Azure Table Storage 資産を作成したり、既存の Azure Table Storage 資産を使用したりできます。 ただし、Table API を使わない場合、自動インデックス、追加の整合性オプション、またはグローバル分散によるメリットは得られません。

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-more-than-one-region-of-azure"></a>Table API で、Azure の複数のリージョン間でのデータのレプリケーションを追加するにはどうすればよいですか?

Azure Cosmos DB ポータルの[グローバル レプリケーション設定](tutorial-global-distribution-sql-api.md#portal)を使用して、アプリケーションに適したリージョンを追加できます。 グローバル分散型のアプリケーションを開発するには、低待機時間の読み取りを実現するために、PreferredLocation 情報をローカル リージョンに設定したアプリケーションを追加する必要があります。

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Table API でアカウントのプライマリ書き込みリージョンを変更するにはどうすればよいですか?

Azure Cosmos DB のグローバル レプリケーション ポータル ウィンドウを使用してリージョンを追加し、必要なリージョンにフェールオーバーできます。 手順については、[複数リージョンの Azure Cosmos DB アカウントを使用した開発](high-availability.md)に関する記事をご覧ください。

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>データを分散するときに低待機時間を実現するために、優先読み取りリージョンを構成するにはどうすればよいですか?

ローカルの場所から読み取りを実行できるようにするには、app.config ファイルの PreferredLocation キーを使用します。 既存のアプリケーションの場合、LocationMode が設定されていると、Table API はエラーをスローします。 Table API は app.config ファイルからこの情報を取得するので、該当のコードを削除してください。 

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>Table API の整合レベルについてどのように考えればよいですか?

Azure Cosmos DB では、整合性、可用性、待機時間の最適なトレードオフを提供します。 Azure Cosmos DB には 5 つの整合性レベルが用意されているので、Table API 開発者は、データを照会するときにテーブル レベルで最適な整合性モデルを選び、個々の要求を行うことができます。 クライアントは、接続時に整合性レベルを指定できます。 レベルは、CreateCloudTableClient の consistencyLevel 引数を使って変更できます。

Table API では、既定で有界整合性制約の整合性が適用され、"自身の書き込みの読み取り" によって低待機時間の読み取りを実現します。 詳細については、[整合性レベル](consistency-levels.md)に関する記事をご覧ください。

既定では、Azure Standard Table はリージョン内では厳密な整合性を提供し、セカンダリの場所では最終的な整合性を提供します。

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>Azure Cosmos DB Table API では、Azure Table Storage より多くの整合性レベルが提供されますか?

はい。Azure Cosmos DB の分散特性のメリットを得る方法については、「[一貫性レベル](consistency-levels.md)」をご覧ください。 整合性レベルは保証の対象となるため、安心して使用できます。

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>グローバル分散を有効にした場合、データのレプリケーションにどのくらいの時間がかかりますか?

Azure Cosmos DB では、データはローカル リージョンで永続的にコミットされ、わずか数ミリ秒ですぐに他のリージョンにプッシュされます。 このレプリケーションは、データ センターのラウンドトリップ時間 (RTT) にのみ依存します。 Azure Cosmos DB のグローバル分散機能の詳細については、[Azure Cosmos DB:Azure のグローバル分散データベース サービス](distribute-data-globally.md)に関する記事をご覧ください。

### <a name="can-the-read-request-consistency-level-be-changed"></a>読み取り要求の整合性レベルを変更することはできますか?

Azure Cosmos DB では、コンテナー レベル (テーブル) で整合性レベルを設定できます。 レベルを変更するには、.NET SDK を使って、app.config ファイルで TableConsistencyLevel キーの値を指定します。 指定できる値は、Strong、Bounded Staleness、Session、Consistent Prefix、Eventual です。 詳細については、「[Azure Cosmos DB の調整可能なデータの一貫性レベル](consistency-levels.md)」をご覧ください。 基本的な考え方として、要求の整合性レベルは、テーブルの設定よりも高いレベルに設定することはできません。 たとえば、テーブルの整合性レベルを Eventual (最終的) に設定し、要求の整合性レベルを Strong (厳密) に設定することはできません。

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>リージョンがダウンした場合、Table API はフェールオーバーをどのように処理しますか?

Table API は、Azure Cosmos DB のグローバルに分散されたプラットフォームを利用します。 アプリケーションがデータ センターのダウンタイムを許容できるようにするには、Azure Cosmos DB ポータルでアカウントのリージョンを少なくとももう 1 つ有効にします ([複数リージョンの Azure Cosmos DB アカウントを使用した開発](high-availability.md)に関する記事を参照)。 ポータルを使用してリージョンの優先順位を設定できます ([複数リージョンの Azure Cosmos DB アカウントを使用した開発](high-availability.md)に関する記事を参照)。

アカウントのリージョンを必要な数だけ追加し、フェールオーバーの優先順位を指定してフェールオーバー先を制御できます。 データベースを使用するには、そのリージョンでもアプリケーションを提供する必要があります。 そうすれば、ダウンタイムが発生しなくなります。 [最新の .NET クライアント SDK](table-sdk-dotnet.md) は自動回帰しますが、他の SDK はしません。 つまり、ダウンしているリージョンを検出すると、新しいリージョンに自動的にフェールオーバーできます。

### <a name="is-the-table-api-enabled-for-backups"></a>Table API ではバックアップは有効になっていますか?

はい。Table API は、バックアップに Azure Cosmos DB のプラットフォームを利用します。 バックアップは自動的に作成されます。 詳細については、[Azure Cosmos DB でのオンライン バックアップと復元](online-backup-and-restore.md)に関する記事をご覧ください。

### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>Table API では、既定でエンティティのすべての属性のインデックスが作成されますか?

はい。エンティティのすべての属性のインデックスが既定で作成されます。 詳細については、「[Azure Cosmos DB でのインデックス作成ポリシー](index-policy.md)」をご覧ください。

### <a name="does-this-mean-i-dont-have-to-create-more-than-one-index-to-satisfy-the-queries"></a>つまり、クエリを満たすために複数のインデックスを作成する必要はないということですか?

はい。Azure Cosmos DB Table API はすべての属性の自動インデックス作成機能を備えています。スキーマ定義は不要です。 この自動化により、開発者はインデックスの作成と管理ではなく、アプリケーションに注力できるようになります。 詳細については、「[Azure Cosmos DB でのインデックス作成ポリシー](index-policy.md)」をご覧ください。

### <a name="can-i-change-the-indexing-policy"></a>インデックス作成ポリシーは変更できますか?

はい。インデックス定義を提供することでインデックス作成ポリシーを変更できます。 設定を適切にエンコードし、エスケープする必要があります。

.NET SDK がない場合、インデックス作成ポリシーを設定できる唯一の方法は次のとおりです。ポータルの**データ エクスプローラー**で、変更する特定のテーブルに移動し、 **[Scale & Settings]\(スケールと設定\)** > [インデックス作成ポリシー] で必要な変更を行って、 **[保存]** を選びます。

.NET SDK からは、app.config ファイルで送信できます。

```JSON
{
  "indexingMode": "consistent",
  "automatic": true,
  "includedPaths": [
    {
      "path": "/somepath",
      "indexes": [
        {
          "kind": "Range",
          "dataType": "Number",
          "precision": -1
        },
        {
          "kind": "Range",
          "dataType": "String",
          "precision": -1
        }
      ]
    }
  ],
  "excludedPaths":
[
 {
      "path": "/anotherpath"
 }
]
}
```

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>プラットフォームとしての Azure Cosmos DB は、並べ替え、集計、階層などの多数の機能を備えているようですが、 これらの機能は Table API に追加される予定ですか?

Table API は Azure Table Storage と同じクエリ機能を提供します。 また、並べ替え、集計、地理空間クエリ、階層、さまざまな組み込み関数もサポートしています。 詳細については、[SQL クエリ](how-to-sql-query.md)に関する記事を参照してください。

### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>Table API の TableThroughput は、どのようなときに変更する必要がありますか?

次のいずれかの条件に該当する場合は、TableThroughput を変更してください。

* データの抽出、変換、読み込み (ETL) を実行している。または、短時間に大量のデータをアップロードする必要がある。
* バックエンドでコンテナーまたはコンテナーのセットのスループットを増やす必要がある (たとえば、使用されたスループットがプロビジョニング スループットを超えており、調整が行われている)。 詳細については、「[Azure Cosmos DB コンテナーのスループットの設定](set-throughput.md)」を参照してください。

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Table API のテーブルのスループットはスケールアップまたはスケールダウンできますか?

はい。スループットのスケーリングは、Azure Cosmos DB ポータルのスケール ウィンドウを使用して実行できます。 詳細については、[スループットの設定](set-throughput.md)に関する記事をご覧ください。

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>新しくプロビジョニングされたテーブルには、既定の TableThroughput が設定されるのですか?

はい。app.config で TableThroughput をオーバーライドしておらず、Azure Cosmos DB であらかじめ作成されているコンテナーを使用していない場合、スループットが 400 に設定されたテーブルが作成されます。

### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Azure Table Storage サービスの既存の顧客を対象とする価格の変更はありますか?

なし。 Azure Table Storage の既存のお客様を対象とする価格の変更はありません。

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Table API の料金はどのように計算されますか?

料金は、割り当てられた TableThroughput によって異なります。

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>Table API では、テーブルのレート制限にどのように対処すればよいですか?

要求レートが基になるコンテナーまたはコンテナーのセットのプロビジョニング スループットの容量を超えると、エラーが発生し、SDK では再試行ポリシーの適用によって呼び出しが再試行されます。

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Azure Cosmos DB の Table API サービスを利用するために、PartitionKey および RowKey とは別にスループットを選ぶ必要があるのはなぜですか?

Azure Cosmos DB では、app.config ファイルまたはポータルでコンテナーのスループットが指定されていない場合、既定のスループットが設定されます。

Azure Cosmos DB では、操作に上限を設定してパフォーマンスと待機時間を保証します。 この保証は、エンジンがテナントの操作にガバナンスを適用できる場合に可能になります。 TableThroughput を設定すると、プラットフォームでこの容量が予約され、操作が正常に完了することが保証されるので、保証されたスループットと待機時間が確保されます。

また、スループットの仕様により、スループットを弾力的に変更して、アプリケーションの季節性によるメリットを享受し、スループットのニーズを満たして、コストを削減できます。

### <a name="azure-table-storage-has-been-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-havent-performed-a-single-transaction-or-stored-anything-can-you-explain"></a>データの保存にしか料金を支払っておらず、クエリを実行することはほとんどないため、Azure Table Storage は安価でした。 Azure Cosmos DB Table API では、トランザクションを 1 つも実行していない場合や何も保存していない場合でも課金されているようです。 説明できますか。

Azure Cosmos DB は、可用性、待機時間、スループットが保証された、グローバル分散型の SLA ベースのシステムとして設計されています。 他のシステムのスループットとは異なり、Azure Cosmos DB でスループットを予約すると、そのスループットが保証されます。 Azure Cosmos DB には、セカンダリ インデックスやグローバル分散など、お客様から要望があった追加機能が用意されています。

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Azure Table Storage にデータを取り込んだときに、(パーティションがいっぱいであることを示す) "クォータが上限に達した" ことを通知するメッセージが表示されたことは一度もありませんでした。 Table API では、このメッセージが表示されました。 このサービスには制限があり、既存のアプリケーションを変更しなければならないのでしょうか?

Azure Cosmos DB は、待機時間、スループット、可用性、整合性を保証し、無制限のスケールを提供する SLA ベースのシステムです。 保証された Premium パフォーマンスを確保するために、データ サイズとインデックスが管理可能であり、スケーラブルであることを確認してください。 パーティション キーごとのエンティティ数または項目数に 10 GB の制限を設けているのは、検索やクエリの優れたパフォーマンスを確実に提供するためです。 すべての情報を 1 つのパーティションに格納し、そのパーティションに対してクエリを実行すると、ホット パーティションになります。Azure Storage でもアプリケーションが適切にスケールできるように、ホット パーティションが発生*しない*ようにすることをお勧めします。

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>Table API で PartitionKey と RowKey が必要なのはそのためですか?

はい。 Table API の外部からアクセスできる領域は、Azure Table Storage SDK のその領域とほぼ同じであるため、パーティション キーによってデータを効率的に分散させることができます。 行キーはそのパーティション内で一意です。 行キーが存在する必要があり、Standard SDK の場合と同様に、行キーを null にすることはできません。 RowKey の長さは 255 バイト、PartitionKey の長さは 1 KB です。

### <a name="what-are-the-error-messages-for-the-table-api"></a>Table API のエラー メッセージはどのようなものですか?

Azure Table Storage と Azure Cosmos DB Table API は同じ SDK を使っているので、ほとんどのエラーは同じです。

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Table API で多数のテーブルを次々に作成しようとすると調整が行われるのはなぜですか?

Azure Cosmos DB は、待機時間、スループット、可用性、整合性を保証する SLA ベースのシステムです。 Azure Cosmos DB はプロビジョニングされるシステムであるため、これらの要件を保証するためにリソースが予約されています。 テーブルを次々に作成すると、その作成ペースが検出され、調整が行われます。 テーブルの作成ペースを確認し、1 分あたり 5 つ未満に抑えることをお勧めします。 Table API はプロビジョニングされるシステムであることに注意してください。 プロビジョニングした時点から料金が発生します。

## <a name="gremlin-api"></a>Gremlin API

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>C#/.NET 開発の場合、Microsoft.Azure.Graphs パッケージまたは Gremlin.NET を使用するべきですか。

Azure Cosmos DB Gremlin API では、サービスのメイン コネクタとしてオープンソースのドライバーが活用されます。 そのため、[Apache Tinkerpop でサポートされているドライバー](https://tinkerpop.apache.org/)の使用をお勧めします。

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>グラフ データベースでクエリを実行するとき、RU/秒はどのように課金されますか。

グラフのオブジェクト、頂点、端はすべて、バックエンドで JSON ドキュメントとして表示されます。 1 つの Gremlin クエリで 1 つまたは複数のグラフ オブジェクトを一度に変更できるため、それに関連付けられるコストは、クエリで処理されるオブジェクトや端に直接関連します。 このプロセスは、その他すべての API に対して Azure Cosmos DB で使用されるプロセスと同じです。 詳細については、「[Azure Cosmos DB の要求ユニット](request-units.md)」をご覧ください。

RU 課金は、結果セットではなく、トラバーサルの作業データ セットに基づきます。 たとえば、結果として頂点を 1 つ得ることがクエリの目的であるが、その過程で他の複数のオブジェクトを走査する必要がある場合、結果として頂点を 1 つ計算するために必要なすべてのグラフ オブジェクトに基づいてコストが算出されます。

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>Azure Cosmos DB Gremlin API でグラフ データベースに与えられる最大スケールは何ですか。

Azure Cosmos DB では、[水平方向のパーティション分割](partition-data.md)を利用し、ストレージ要件とスループット要件の増加に自動的に対処します。 ワークロードの最大スループットと最大ストレージ容量は、指定されたコレクションに関連付けられているパーティションの数により決定されます。 しかしながら、Gremlin API コレクションには、大規模でも適切なパフォーマンスを得るために、一連のガイドラインが指定されています。 パーティション分割の詳細とベスト プラクティスについては、[Azure Cosmos DB でのパーティション分割](partition-data.md)に関する記事をご覧ください。

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>Gremlin ドライバーを利用したインジェクション攻撃はどのように防ぎますか。

ネイティブ Apache Tinkerpop Gremlin ドライバーのほとんどでは、クエリ実行にパラメーター ディクショナリを提供できます。 この方法のサンプルは、[Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) と [Gremlin-Javascript](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/master/app.js) にあります。

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>"Gremlin Query Compilation Error: Unable to find any method" (Gremlin クエリ コンパイル エラー:メソッドが見つかりません) エラーが表示されるのはなぜですか。

Azure Cosmos DB Gremlin API では、Gremlin セキュリティに定義されている機能サブセットが実装されます。 サポートされている手順と詳細については、「[Gremlin サポート](gremlin-support.md)」という記事を参照してください。

最良の回避策は、Gremlin の必須手順をサポートされている機能で書き直すことです。Gremlin の必須手順はすべて、Azure Cosmos DB でサポートされているためです。

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>"WebSocketException: The server returned status code '200' when status code '101' was expected" (WebSocketException: 状態コード '101' が予想されるとき、サーバーから '200' が返されました) エラーが表示されるのはなぜですか。

このエラーはおそらく、間違ったエンドポイントが使用されているときにスローされます。 このエラーを生成するエンドポイントには次のパターンがあります。

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

これは、ご利用のグラフ データベースのドキュメント エンドポイントです。  使用すべき正しいエンドポイントは Gremlin エンドポイントです。この形式は次のようになっています。

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>"RequestRateIsTooLarge" エラーが表示されるのはなぜですか。

このエラーは、秒あたりで割り当てられている要求ユニットではクエリに十分なサービスを提供できないことを意味します。 このエラーは通常、すべての頂点を取得するクエリを実行すると表示されます。

```
// Query example:
g.V()
```

このクエリでは、グラフからすべての頂点を取得するように試行されます。 そのため、このクエリのコストは、RU の観点からは、少なくとも頂点の数と等しくなります。 RU/秒の設定を調整してこのクエリに対処してください。

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>Gremlin ドライバーの接続が最後には切断されます。なぜですか。

Gremlin 接続は WebSocket 接続経由で行われます。 WebSocket 接続には有効時間が指定されませんが、Azure Cosmos DB Gremlin API では、アイドル時間が 30 分間続くと接続が終了します。

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>ネイティブ Gremlin ドライバーで Fluent API 呼び出しを使用できません。なぜですか。

Fluent API 呼び出しは、Azure Cosmos DB Gremlin API ではまだサポートされていません。 Fluent API 呼び出しにはバイトコード サポートと呼ばれている内部書式設定機能が必要ですが、これは現在のところ、Azure Cosmos DB Gremlin API ではサポートされていません。 同じ理由から、最新の Gremlin-JavaScript ドライバーも現在のところサポートされていません。

### <a name="how-can-i-evaluate-the-efficiency-of-my-gremlin-queries"></a>自分の Gremlin クエリの効率性を評価する方法は?

**executionProfile()** というプレビュー手順を利用し、クエリ実行プランを分析できます。 次の例のように、この手順は Gremlin クエリの最後に追加する必要があります。

**クエリの例**

```
g.V('mary').out('knows').executionProfile()
```

**出力例**

```json
[
  {
    "gremlin": "g.V('mary').out('knows').executionProfile()",
    "totalTime": 8,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 3,
        "annotations": {
          "percentTime": 37.5
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 62.5
        },
        "counts": {
          "resultCount": 0
        },
        "storeOps": [
          {
            "count": 0,
            "size": 0,
            "time": 0.6
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      }
    ]
  }
]
```

上記プロファイルの出力には、頂点オブジェクト、端オブジェクト、および作業データ セットのサイズの取得にかかった時間が表示されます。 これは Azure Cosmos DB クエリの標準コスト測定に関連します。

## <a id="cassandra"></a> Cassandra API

### <a name="what-is-the-protocol-version-supported-by-azure-cosmso-db-cassandra-api-is-there-a-plan-to-support-other-protocols"></a>Azure Cosmso DB の Cassandra API でサポートされているプロトコル バージョンは何ですか? 他のプロトコルをサポートする予定はありますか?

現在、Azure Cosmos DB 用の Apache Cassandra API は CQL バージョン 4 をサポートしています。 他のプロトコルのサポートについてフィードバックがある場合は、[UserVoice のフィードバック](https://feedback.azure.com/forums/263030-azure-cosmos-db)を投稿するか、メールを [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) に送信してください。

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>テーブルのスループットの選択が必須なのはなぜですか?

Azure Cosmos DB は、テーブルの作成元 (ポータルまたは CQL) に基づいてコンテナーの既定のスループットを設定します。
Azure Cosmos DB では、操作に上限を設定してパフォーマンスと待機時間を保証します。 この保証は、エンジンがテナントの操作にガバナンスを適用できる場合に可能になります。 スループットを設定すると、プラットフォームでこの容量が予約され、操作が正常に完了することが保証されるので、保証されたスループットと待機時間が確保されます。
アプリケーションの季節性を利用し、コストを削減するためにスループットを柔軟に変更することができます。

スループットの概念については、「[Azure Cosmos DB の要求ユニット](request-units.md)」を参照してください。 テーブルのスループットは、基になる物理パーティション全体で均等に分散されます。

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>CQL で作成したときのテーブルの既定の RU/秒を教えてください。 変更する必要がある場合はどうなりますか?

Azure Cosmos DB では、スループットの単位として 1 秒あたりの要求単位数 (RU/秒) が使われています。 CQL で作成したテーブルは 400 RU です。 RU はポータルから変更できます。

CQL

```
CREATE TABLE keyspaceName.tablename (user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=1200
```

.NET

```csharp
int provisionedThroughput = 400;
var simpleStatement = new SimpleStatement($"CREATE TABLE {keyspaceName}.{tableName} (user_id int PRIMARY KEY, lastname text)");
var outgoingPayload = new Dictionary<string, byte[]>();
outgoingPayload["cosmosdb_provisioned_throughput"] = Encoding.UTF8.GetBytes(provisionedThroughput.ToString());
simpleStatement.SetOutgoingPayload(outgoingPayload);
```

### <a name="what-happens-when-throughput-is-used-up"></a>スループットを使い切った場合はどうなりますか?

Azure Cosmos DB では、操作に上限を設定してパフォーマンスと待機時間を保証します。 この保証は、エンジンがテナントの操作にガバナンスを適用できる場合に可能になります。 これはスループットの設定に基づいて可能になります。スループットを設定すると、プラットフォームでこの容量が予約され、操作が正常に完了することが保証されるので、保証されたスループットと待機時間が確保されます。
この容量を超えると、容量を使い切ったことを示すオーバーロードのエラー メッセージ
"0x1001 Overloaded: the request cannot be processed because "Request Rate is large" "\(0x1001 オーバーロード: "要求レートが大きい" ため要求を処理できませんでした\) を受け取ります。 この段階で、この問題の原因となる操作とそのボリュームを確認することが重要です。 ポータルのメトリックを使用すると、プロビジョニングした容量を超えた容量の使用について理解できることがあります。 次に、基になるすべてのパーティションでほぼ均等に容量が使用されていることを確認する必要があります。 ほとんどのスループットが 1 つのパーティションに使用されている場合、ワークロードは均等ではありません。

複数のパーティション全体、または集計して、時間単位、日単位、7 日間単位で使用されたスループットを示すメトリックを使用できます。 詳細については、「[Azure Cosmos DB のメトリックを使用した監視とデバッグ](use-metrics.md)」を参照してください。

診断ログについては、「[Azure Cosmos DB 診断ログ](logging.md)」の記事を参照してください。

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>プライマリ キーは、Azure Cosmos DB のパーティション キーの概念と対応していますか?

はい。パーティション キーは、適切な場所にエンティティを配置するために使用されます。 Azure Cosmos DB では、物理パーティションに格納されている適切な論理パーティションを見つけるために使用されます。 パーティション分割の概念については、「[Azure Cosmos DB でのパーティション分割とスケーリング](partition-data.md)」の記事でわかりやすく説明されています。 ここで重要な点は、論理パーティションは現在の 10 GB の制限を超えないようにすることです。

### <a name="what-happens-when-i-get-a-quota-full-notification-indicating-that-a-partition-is-full"></a>パーティションが満杯であることを示す "クォータが上限に達した" 通知が表示された場合はどうなりますか?

Azure Cosmos DB は、待機時間、スループット、可用性、整合性を保証し、無制限のスケールを提供する SLA ベースのシステムです。 この無制限のストレージは、主要概念としてパーティション分割を使用する、データの水平スケールアウトに基づいてします。 パーティション分割の概念については、「[Azure Cosmos DB でのパーティション分割とスケーリング](partition-data.md)」の記事でわかりやすく説明されています。

論理パーティションあたりのエンティティ数または項目数に対する 10 GB の制限に従うことをお勧めします。 すべての情報を 1 つのパーティションに格納し、そのパーティションに対してクエリを実行すると、ホット パーティションになります。アプリケーションが適切にスケールできるように、ホット パーティションが発生*しない*ようにすることをお勧めします。 このエラーは、データが均等ではない場合、つまり、1 つのパーティション キーに大量の (10&nbsp;GB を超える) データ が割り当てられている場合にのみ発生する可能性があります。 ストレージ ポータルを使用して、データの分散を確認できます。 このエラーを解決するには、テーブルを作成し直し、より細分化されたプライマリ (パーティション キー) を選択し、データの分散を改善することをお勧めします。

### <a name="is-it-possible-to-use-cassandra-api-as-key-value-store-with-millions-or-billions-of-individual-partition-keys"></a>パーティション キーが数百万個または数十億個あるキー値ストアとして Cassandra API を使用することはできますか?

Azure Cosmos DB はストレージをスケールアウトすることで無制限のデータを格納できます。 これはスループットと関係がありません。 そのため、常に Cassandra API のみを使用し、適切なプライマリ/パーティション キーを指定してキー/値を格納および取得できます。 これらの個々のキーには独自の論理パーティションが割り当てられ、物理パーティションに問題なく配置されます。

### <a name="is-it-possible-to-create-more-than-one-table-with-apache-cassandra-api-of-azure-cosmos-db"></a>Azure Cosmos DB の Apache Cassandra API を使用して複数のテーブルを作成できますか?

はい、Apache Cassandra API を使用して複数のテーブルを作成できます。 これらの各テーブルは、スループットとストレージのユニットとして扱われます。

### <a name="is-it-possible-to-create-more-than-one-table-in-succession"></a>連続して複数のテーブルを作成することはできますか?

Azure Cosmos DB は、データ アクティビティとコントロール プレーン アクティビティ両方のリソース管理システムです。 コレクションやテーブルなどのコンテナーは、特定のスループット容量に対してプロビジョニングされるランタイム エンティティです。 これらのコンテナーの連続的な作成は、予期されるアクティビティではなく、調整されます。 テーブルを即時に削除/作成するテストを行う場合は、間隔を空けるようにしてください。

### <a name="what-is-maximum-number-of-tables-that-can-be-created"></a>最大何個のテーブルを作成できますか?

テーブル数には物理的な制限がありません。数十または数百単位の大量のテーブル (データの定常的な合計サイズが 10 TB を超える) を作成する必要がある場合は、[askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) まで電子メールでお問い合わせください。

### <a name="what-is-the-maximum--of-keyspace-that-we-can-create"></a>最大何個のキースペースを作成できますか?

キースペースはメタデータ コンテナーなので、キースペース数に物理的な制限はありません。何らかの理由で大量のキースペースがある場合は、[askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) まで電子メールでお問い合わせください。

### <a name="is-it-possible-to-bring-in-lot-of-data-after-starting-from-normal-table"></a>通常のテーブルから開始した後で、大量のデータを取り込むことはできますか?

ストレージ容量は自動的に管理され、取り込むデータが増えると拡大されます。 そのため、必要に応じて任意の量のデータを安心してインポートできます。ノードの管理やプロビジョニングなどは必要ありません。

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>Azure Cosmos DB 動作の Apache Casssandra API を構成するために、yaml ファイルの設定を提供することはできますか?

Azure Cosmos DB の Apache Cassandra API はプラットフォーム サービスです。 操作を実行するためのプロトコル レベルの互換性を提供します。 管理、監視、および構成の複雑な部分は隠されています。 開発者やユーザーは、可用性、廃棄標識、キー キャッシュ、行キャッシュ、ブルーム フィルター、他のさまざまな設定について心配する必要はありません。 Azure Cosmos DB の Apache Cassandra API では、構成と管理のオーバーヘッドなしで必要な読み取りと書き込みのパフォーマンスを提供することに焦点が当てられています。

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>Azure Cosmos DB の Apache Cassandra API は、ノード追加/クラスター状態/ノード状態コマンドをサポートしますか?

Apache Cassandra API は、容量計画およびスループットとストレージに対する柔軟性の要求への対応を容易にするプラットフォーム サービスです。 Azure Cosmos DB を使って必要なスループットをプロビジョニングします。 その後は、ノードの追加/削除や管理を心配せずに、1 日に何度でもスケールアップおよびスケールダウンできます。 つまり、ノードもクラスター管理ツールも使用する必要がありません。

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation-like-simplenetwork"></a>simple/network など、キースペースの作成のさまざまな構成設定についてはどうなりますか?

Azure Cosmos DB では、可用性と低待機時間のために、最初からグローバルな分散が提供されています。 レプリカやなどを設定する必要はありません。 すべての書き込みは、書き込み対象のすべてのリージョンで常に永続的にクォーラム コミットされ、パフォーマンスが保証されます。

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filter-caching-read-repair-change-gcgrace-compression-memtableflushperiod-and-more"></a>ブルーム フィルター、キャッシュ、読み取り修復の変更、gc_grace、圧縮 memtable_flush_period などのテーブル メタデータのさまざまな設定はどうなりますか?

Azure Cosmos DB が提供する読み取り/書き込みのパフォーマンスとスループットでは、構成設定を変更する必要はなく、誤って構成設定を操作することもありません。

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>Cassandra のテーブルでは Time to Live (TTL) がサポートされていますか?

はい。TTL がサポートされています。

### <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-earlier-with-various-tools-what-needs-to-be-monitored-now"></a>以前のさまざまなツールを使用して、ノード状態、レプリカ状態、gc、OS のパラメーターを監視することはできますか? 現在は何を監視する必要がありますか?

Azure Cosmos DB はプラットフォーム サービスであり、生産性を向上させ、インフラストラクチャの管理と監視の心配を取り除きます。 ポータルのメトリックで使用できるスループットのみに注意し、そのスループットの調整と増減が発生しているかどうかを確認する必要があります。
[SLA](monitor-accounts.md) を監視します。
[メトリック](use-metrics.md)と[診断ログ](logging.md)を使用します。

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>Azure Cosmos DB の Apache Cassandra API で動作するクライアント SDK はどれですか?

CQLv3 を使用する Apache Cassandra SDK のクライアント ドライバーがクライアント プログラムに使用されていました。 他のドライバーを使用している場合、または問題が発生している場合は、[askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) まで電子メールでお問い合わせください。

### <a name="is-composite-partition-key-supported"></a>複合パーティション キーはサポートされていますか?

はい。通常の構文を使って、複合パーティション キーを作成できます。

### <a name="can-i-use-stable-loader-for-data-loading"></a>データの読み込みに stable ローダーを使うことはできますか?

いいえ。stable ローダーはサポートされていません。

### <a name="can-an-on-premises-apache-cassandra-cluster-be-paired-with-azure-cosmos-dbs-cassandra-api"></a>オンプレミスの Apache Cassandra クラスターを Azure Cosmos DB の Cassandra API とペアにできますか?

現在、Azure Cosmos DB では、操作のオーバーヘッドがないクラウド環境に合わせてエクスペリエンスが最適化されています。 ペアリングが必要な場合は、シナリオの説明を添えて [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) まで電子メールでお問い合わせください。 現在、オンプレミスや別のクラウドの Cassandra クラスターと Cosomos DB の Cassandra API をペアリングする機能の提供に向けた作業を進めています。

### <a name="does-cassandra-api-provide-full-backups"></a>Cassandra API に完全バックアップ機能はありますか?

現在、Azure Cosmos DB では、すべての API について 4 時間間隔で 2 つの無料の完全バックアップが提供されています。 そのため、バックアップ スケジュールなどを設定する必要はありません。
リテンション期間と頻度を変更したい場合は、[askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) まで電子メールでお問い合わせいただくか、サポート ケースを作成してください。 バックアップ機能については、「[Azure Cosmos DB での自動オンライン バックアップと復元](online-backup-and-restore.md)」の記事を参照してください。

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>リージョンがダウンした場合、Cassandra API アカウントはフェールオーバーをどのように処理しますか?

Azure Cosmos DB の Cassandra API は、Azure Cosmos DB のグローバルに分散されたプラットフォームを利用します。 アプリケーションがデータ センターのダウンタイムを許容できるようにするには、Azure Cosmos DB ポータルでアカウントのリージョンを少なくとももう 1 つ有効にします ([複数リージョンの Azure Cosmos DB アカウントを使用した開発](high-availability.md)に関する記事を参照)。 ポータルを使用してリージョンの優先順位を設定できます ([複数リージョンの Azure Cosmos DB アカウントを使用した開発](high-availability.md)に関する記事を参照)。

アカウントのリージョンを必要な数だけ追加し、フェールオーバーの優先順位を指定してフェールオーバー先を制御できます。 データベースを使用するには、そのリージョンでもアプリケーションを提供する必要があります。 そうすれば、ダウンタイムが発生しなくなります。

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Apache Cassandra API では、既定でエンティティのすべての属性のインデックスが作成されますか?

Cassandra API では、特定の属性に対する選択的インデックスを作成できるように、セカンダリ インデックスをサポートする計画を進めています。 


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>エミュレーターで新しい Cassandra API SDK をローカルに使うことができますか?

はい、これはサポートされています。

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-change-feed-and-other-functionality-will-these-capabilities-be-added-to-the-cassandra-api"></a>プラットフォームとしての Azure Cosmos DB は、変更フィードなどの多数の機能を備えているようですが、 これらの機能が Cassandra API に追加される予定はありますか?

Apache Cassandra API には、Apache Cassandra と同じ CQL 機能が提供されています。 今後、多様な機能をサポートする実現可能性を調査する予定です。

### <a name="feature-x-of-regular-cassandra-api-isnt-working-as-today-where-can-the-feedback-be-provided"></a>通常の Cassandra API の機能 x が現在動作していませんが、どこでフィードバックを報告できますか?

フィードバックは [UserVoice のフィードバック](https://feedback.azure.com/forums/263030-azure-cosmos-db)でお寄せください。

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md
