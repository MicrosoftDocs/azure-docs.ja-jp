---
title: "Azure Cosmos DB についてよく寄せられる質問 | Microsoft Docs"
description: "グローバル分散型のマルチモデル データベース サービスである Azure Cosmos DB についてよく寄せられる質問の回答が得られます。 容量、パフォーマンス レベル、スケーリングについて説明します。"
keywords: "データベース質問, よく寄せられる質問, documentdb, azure, Microsoft azure"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: b68d1831-35f9-443d-a0ac-dad0c89f245b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2017
ms.author: mimig
ms.openlocfilehash: e8ac4bef5af7a98857278b82304bb58f1ac6e342
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-faq"></a>Azure Cosmos DB の FAQ
## <a name="azure-cosmos-db-fundamentals"></a>Azure Cosmos DB の基礎
### <a name="what-is-azure-cosmos-db"></a>Azure Cosmos DB とは何ですか?
Azure Cosmos DB は、グローバルにレプリケートされたマルチモデル データベース サービスです。スキーマフリーのデータに対する豊富なクエリを提供し、構成可能で信頼性の高いパフォーマンスと迅速な開発を実現します。 これらはすべて、Microsoft Azure の強力な機能と守備範囲に支えられた管理プラットフォームを通じて実現されます。 

Azure Cosmos DB は、予測可能なスループット、高可用性、低待機時間、スキーマフリー データ モデルが重要な要件となる、Web、モバイル、ゲーム、IoT の各アプリケーションに最適なソリューションです。 スキーマの柔軟性と豊富なインデックス作成機能を備え、統合 JavaScript によるマルチドキュメント トランザクションをサポートします。 

データベース質問および回答の詳細と、このサービスのデプロイおよび使用の手順については、[Azure Cosmos DB ドキュメント ページ](https://azure.microsoft.com/documentation/services/cosmos-db/)をご覧ください。

### <a name="what-happened-to-documentdb"></a>DocumentDB はどうなりましたか?
DocumentDB API は、Azure Cosmos DB でサポートされている API の 1 つであり、データ モデルの 1 つです。 さらに、Azure Cosmos DB は、Graph API (プレビュー)、Table API (プレビュー)、MongoDB API もサポートしています。 詳細については、「[DocumentDB のお客様からの質問](#moving-to-cosmos-db)」をご覧ください。

### <a name="how-do-i-get-to-my-documentdb-account-in-the-azure-portal"></a>Azure Portal で DocumentDB アカウントにアクセスするにはどうすればよいですか?
Azure Portal で、左側のウィンドウの Azure Cosmos DB アイコンをクリックします。 DocumentDB アカウントを既に持っていた場合は、そのアカウントが Azure Cosmos DB アカウントになります。課金の変更はありません。

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Azure Cosmos DB の一般的なユース ケースを教えてください。
Azure Cosmos DB は、自動スケール、予測可能なパフォーマンス、ミリ秒レベルの高速応答時間、スキーマフリー データに対してクエリを実行できることが重要である、新しい Web、モバイル、ゲーム、IoT の各アプリケーションに適しています。 Azure Cosmos DB は迅速な開発に役立ち、アプリケーション データ モデルの継続的な反復をサポートします。 ユーザーが生成したコンテンツとデータを管理するアプリケーションは、[Azure Cosmos DB の一般的なユース ケース](use-cases.md)です。 

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Azure Cosmos DB では、予測可能なパフォーマンスをどのようにして実現していますか?
[要求ユニット](request-units.md) (RU) とは、Azure Cosmos DB におけるスループットの単位です。 1 RU のスループットは、1 KB のドキュメントを取得するスループットに相当します。 Azure Cosmos DB におけるすべての操作 (読み取り、書き込み、SQL クエリ、ストアド プロシージャの実行など) には、操作を完了するために必要なスループットに基づいて明確な RU 値が設定されています。 CPU、IO、メモリや、これらがアプリケーションのスループットに及ぼす影響について考えるのではなく、RU という 1 つの単位を基にして考えることができます。

各 Azure Cosmos DB コンテナーは、1 秒あたりのスループットを表す RU を単位として、プロビジョニング スループットを使用して予約できます。 あらゆる規模のアプリケーションで、個々の要求のベンチマークを実行して RU 値を測定し、すべての要求の要求ユニットの合計に対処できるようにコンテナーをプロビジョニングできます。 アプリケーションのニーズの進化に合わせて、コンテナーのスループットをスケールアップまたはスケールダウンすることもできます。 要求ユニットの詳細とコンテナーのニーズを判断する方法については、「[スループットのニーズの推定](request-units.md#estimating-throughput-needs)」を参照し、[スループット計算ツール](https://www.documentdb.com/capacityplanner)をお試しください。 ここで言う "*コンテナー*" とは、DocumentDB API のコレクション、Graph API のグラフ、MongoDB API のコレクション、Table API のテーブルを指します。 

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Azure Cosmos DB は、キー/値、多桁式、ドキュメント、グラフなどのさまざまなデータ モデルをどのようにサポートしていますか?

キー/値 (テーブル)、多桁式、ドキュメント、およびグラフ データ モデルは、Azure Cosmos DB のベースである ARS (アトム、レコード、およびシーケンス) 設計のため、すべてネイティブにサポートされています。 アトム、レコード、およびシーケンスは、さまざまなデータ モデルに容易にマップしたり投影したりできます。 モデルのサブセット用の API は今すぐ使用でき (DocumentDB、MongoDB、Table、および Graph API)、追加のデータ モデルに固有のその他の API は将来使用可能になります。

Azure Cosmos DB は、開発者にスキーマやセカンダリ インデックスを要求することなく、取り込んだすべてのデータを自動的にインデックス作成できるスキーマ独立型インデックス作成エンジンを備えています。 このエンジンは、インデックスおよびクエリ処理サブシステムから記憶域のレイアウトを分離する一連の論理インデックス レイアウト (転置、多桁式、ツリー) に依存しています。 Cosmos DB はまた、一連のワイヤ プロトコルおよび API を拡張可能な方法でサポートし、それらをコア データ モデル (1) と論理インデックス レイアウト (2) に効率的に変換することにより、一意に複数のデータ モデルをネイティブにサポートできるようになる機能も備えています。

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Azure Cosmos DB は HIPAA 準拠ですか?
はい。Azure Cosmos DB は HIPAA 準拠です。 HIPAA は、個別に識別できる医療情報の使用、開示、および保護するための要件を確立しています。 詳細については、[Microsoft セキュリティ センター](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA)を参照してください。

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Azure Cosmos DB のストレージの制限を教えてください。
Azure Cosmos DB でコンテナーが格納できるデータの合計量に制限はありません。

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Azure Cosmos DB のスループットの制限を教えてください。
Azure Cosmos DB でコンテナーがサポートできるスループットの総量に制限はありません。 基本的な考え方は、十分な数のパーティション キーにワークロードをほぼ均等に分散させることです。

### <a name="how-much-does-azure-cosmos-db-cost"></a>Azure Cosmos DB の料金はいくらですか?
詳細については、[Azure Cosmos DB の価格の詳細](https://azure.microsoft.com/pricing/details/cosmos-db/)に関するページをご覧ください。 Azure Cosmos DB の利用料金は、プロビジョニング済みコンテナーの数、コンテナーがオンラインであった時間数、各コンテナーのプロビジョニング スループットによって決まります。 ここで言う "*コンテナー*" とは、DocumentDB API のコレクション、Graph API のグラフ、MongoDB API のコレクション、Table API のテーブルを指します。 

### <a name="is-a-free-account-available"></a>無料アカウントはありますか?
はい。契約することなく、期間限定のアカウントに無料でサインアップできます。 サインアップするには、「[Azure Cosmos DB を無料で試す](https://azure.microsoft.com/try/cosmosdb/)」にアクセスするか、[Try Azure Cosmos DB に関する FAQ](#try-cosmos-db) セクションで詳細を確認してください。

Azure を初めて使用する場合は、[Azure 無料アカウント](https://azure.microsoft.com/free/)にサインアップできます。それにより、30 日間の使用権と、すべての Azure サービスを試すためのクレジットが与えられます。 Visual Studio サブスクリプションを持っている場合は、どの Azure サービスでも使用できる[無料の Azure クレジット](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)も与えられます。 

また、[Azure Cosmos DB Emulator](local-emulator.md) を使用すると、Azure サブスクリプションを作成しなくても、ローカルでのアプリケーションの開発とテストを無料で行うことができます。 Azure Cosmos DB Emulator でのアプリケーションの動作に満足できたら、クラウドでの Azure Cosmos DB アカウントの使用に切り替えることができます。

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Azure Cosmos DB に関するその他の支援を得るにはどうすればよいですか?
支援が必要な場合は、[Stack Overflow](http://stackoverflow.com/questions/tagged/azure-cosmosdb) または [MSDN フォーラム](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=AzureDocumentDB)を利用するか、[askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) にメールを送って、Azure Cosmos DB エンジニアリング チームとの 1 対 1 のチャットをスケジュールしてください。 

<a id="try-cosmos-db"></a>
## <a name="try-azure-cosmos-db-subscriptions"></a>Try Azure Cosmos DB サブスクリプション

現在、一定期間に限り、サブスクリプション不要で、課金も契約もなしで Azure Cosmos DB をご利用いただけます。 Try Azure Cosmos DB サブスクリプションにサインアップするには、「[Azure Cosmos DB を無料で試す](https://azure.microsoft.com/try/cosmosdb/)」にアクセスしてください。 このサブスクリプションは、[Azure 無料試用版](https://azure.microsoft.com/free/)とは別のもののため、Azure 無料試用版または Azure 有料サブスクリプションに加えて使用できます。 

Try Azure Cosmos DB サブスクリプションは、Azure Portal で、ユーザー ID に関連付けられた他のサブスクリプションの横に表示されます。 

Try Azure Cosmos DB サブスクリプションには、次の条件が適用されます。

* SQL (DocumentDB API)、Gremlin (Graph API)、Table アカウントのサブスクリプションあたり 1 つのコンテナー。
* MongoDB アカウントのサブスクリプションあたり最大 3 つのコレクション。
* 10 GB のストレージ容量。
* グローバルなレプリケーションは、米国中部、北ヨーロッパ、東南アジアという [Azure リージョン](https://azure.microsoft.com/regions/)で利用可能です。
* 最大スループット 5K RU/秒。
* サブスクリプションの有効期限は 24 時間後に切れます。これは、合計で最大 48 時間まで延長できます。
* Try Azure Cosmos DB アカウントでは Azure サポート チケットを作成できません。ただし、既存のサポート プランをご利用のサブスクライバーにはサポートが提供されます。 

## <a name="set-up-azure-cosmos-db"></a>Azure Cosmos DB の設定
### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Azure Cosmos DB にサインアップするにはどうすればよいですか?
Azure Cosmos DB は Azure Portal で利用できます。 まず、Azure サブスクリプションにサインアップします。 サインアップしたら、DocumentDB API、Graph API (プレビュー)、Table API (プレビュー)、または MongoDB API のアカウントを Azure サブスクリプションに追加できます。

### <a name="what-is-a-master-key"></a>マスター キーとは何ですか?
マスター キーは、アカウントのすべてのリソースにアクセスするためのセキュリティ トークンです。 キーを保持する個人には、データベース アカウント内のすべてのリソースへの読み取り/書き込みアクセスが許可されます。 マスター キーを配布するときには十分な注意が必要です。 プライマリ マスター キーとセカンダリ マスター キーは、[Azure Portal][azure-portal] の **[キー]** ブレードで入手できます。 リソース キーの詳細については、「 [アクセス キーを表示、コピー、および再生成する](manage-account.md#keys)」を参照してください。

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>PreferredLocations として設定できるリージョンを教えてください。 
PreferredLocations 値は、Cosmos DB を利用できる Azure リージョンのいずれかに設定できます。 利用可能なリージョンの一覧については、「[Azure リージョン](https://azure.microsoft.com/regions/)」をご覧ください。

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Azure のデータ センター経由で世界中にデータを配布するときに注意すべきことはありますか? 
Azure Cosmos DB は、「[Azure リージョン](https://azure.microsoft.com/regions/)」ページに記載されているすべての Azure リージョンにわたって存在します。 Azure Cosmos DB はコア サービスであるため、すべての新しいデータ センターに Azure Cosmos DB が存在することになります。 

リージョンを設定するときは、Azure Cosmos DB では主権のあるクラウドと政府機関のクラウドが重視されることに注意してください。 つまり、独立リージョンにアカウントを作成した場合、その独立リージョンの外部にレプリケートすることはできません。 同様に、外部のアカウントから他の独立した場所へのレプリケーションを有効にすることはできません。 

## <a name="develop-against-the-documentdb-api"></a>DocumentDB API に対する開発

### <a name="how-do-i-start-developing-against-the-documentdb-api"></a>DocumentDB API に対する開発を開始するにはどうすればよいですか?
Microsoft DocumentDB API は、[Azure Portal][azure-portal] で利用できます。 まず、Azure サブスクリプションにサインアップする必要があります。 Azure サブスクリプションにサインアップしたら、DocumentDB API コンテナーを Azure サブスクリプションに追加できます。 Azure Cosmos DB アカウントを追加する手順については、[Azure Cosmos DB データベース アカウントの作成](create-documentdb-dotnet.md#create-account)に関するセクションをご覧ください。 DocumentDB アカウントを既に持っていた場合は、そのアカウントが Azure Cosmos DB アカウントになります。 

[SDK](documentdb-sdk-dotnet.md) が利用可能です。 開発者は、[RESTful HTTP API](/rest/api/documentdb/) を使用して、さまざまなプラットフォームや言語で Azure Cosmos DB リソースを操作することもできます。

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>開発作業を速やかに開始するために、既製のサンプルを利用できますか?
DocumentDB API の [.NET](documentdb-dotnet-samples.md)、[Java](https://github.com/Azure/azure-documentdb-java)、[Node.js](documentdb-nodejs-samples.md)、[Python](documentdb-python-samples.md) の各 SDK のサンプルを GitHub で入手できます。


### <a name="does-the-documentdb-api-database-support-schema-free-data"></a>DocumentDB API データベースはスキーマフリー データをサポートしていますか?
はい。DocumentDB API では、スキーマ定義やヒントを必要とせずに、アプリケーションが任意の JSON ドキュメントを格納できます。 データは、Azure Cosmos DB SQL クエリ インターフェイスを使用してクエリにすぐに使用できます。  

### <a name="does-the-documentdb-api-support-acid-transactions"></a>DocumentDB API は ACID トランザクションをサポートしていますか?
はい。DocumentDB API では、JavaScript のストアド プロシージャとトリガーとして表現されるクロス ドキュメント トランザクションをサポートしています。 トランザクションは、各コレクション内の単一のパーティションを対象とし、他の同時実行されるコードおよびユーザー要求から "完全に" 分離された ACID セマンティクスで実行されます。 JavaScript アプリケーション コードのサーバー側実行により例外がスローされた場合は、トランザクション全体がロールバックされます。 トランザクションの詳細については、「 [プログラム データベース トランザクション](programming.md#database-program-transactions)」を参照してください。

### <a name="what-is-a-collection"></a>コレクションとは何ですか?
コレクションとは、ドキュメントと、関連する JavaScript アプリケーション ロジックのグループです。 コレクションは課金対象のエンティティであり、その[コスト](performance-levels.md)は、スループットと使用しているストレージによって決まります。 コレクションは、1 つ以上のパーティションまたはサーバーにまたがって存在することができ、拡張性があるので、対応できるストレージまたはスループットの量には実質的に制限はありません。

コレクションは、Azure Cosmos DB の課金エンティティでもあります。 各コレクションは、プロビジョニング済みスループットと使用されたストレージ領域に基づいて時間単位で課金されます。 詳細については、「[Azure Cosmos DB の価格](https://azure.microsoft.com/pricing/details/cosmos-db/)」を参照してください。 

### <a name="how-do-i-create-a-database"></a>どのようにしてデータベースを作成しますか?
データベースは、[Azure Portal](https://portal.azure.com) (「[コレクションの追加](create-documentdb-dotnet.md#create-collection)」を参照)、[Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) のいずれか、または [REST API](/rest/api/documentdb/) を使用して作成できます。 

### <a name="how-do-i-set-up-users-and-permissions"></a>どのようにしてユーザーおよびアクセス許可を設定しますか?
ユーザーとアクセス許可は、[Cosmos DB API SDK](documentdb-sdk-dotnet.md) または [REST API](/rest/api/documentdb/) のいずれかを使用して作成できます。  

### <a name="does-the-documentdb-api-support-sql"></a>DocumentDB API は SQL をサポートしていますか?
SQL クエリ言語は、SQL でサポートされるクエリ機能の強化版サブセットです。 Azure Cosmos DB の SQL クエリ言語は、高度な階層型の関係演算子と、JavaScript ベースのユーザー定義関数 (UDF) による機能拡張を提供します。 JSON 文法では、ラベル付きノードが配置されたツリーとして JSON ドキュメントをモデル化できます。これは、Azure Cosmos DB の自動インデックス作成手法と Azure Cosmos DB の SQL クエリ言語の両方で使用されます。 SQL 文法の使用方法については、[QueryDocumentDB][query] に関する記事をご覧ください。

### <a name="does-the-documentdb-api-support-sql-aggregation-functions"></a>DocumentDB API は SQL 集計関数をサポートしていますか?
DocumentDB API は、SQL 文法の `COUNT`、`MIN`、`MAX`、`AVG`、`SUM` の各集計関数を使用した、あらゆる規模での低待機時間の集計をサポートしています。 詳細については、「[集計関数](documentdb-sql-query.md#Aggregates)」をご覧ください。

### <a name="how-does-the-documentdb-api-provide-concurrency"></a>DocumentDB API はどのようにして同時実行を提供しますか?
DocumentDB API は、HTTP エンティティ タグ (ETag) によるオプティミスティック同時実行制御 (OCC) をサポートしています。 すべての DocumentDB API リソースに ETag があり、ドキュメントが更新されるたびにサーバーで ETag が設定されます。 すべての応答メッセージに ETag ヘッダーと現在の値が含まれます。 ETag を If-Match ヘッダーと共に使用することで、サーバーはリソースを更新する必要があるかどうかを判断できるようになります。 If-Match 値は、チェック対象の ETag 値です。 対象の ETag 値がサーバーの ETag 値と一致する場合に、リソースが更新されます。 ETag が最新ではない場合、サーバーは操作を拒否して "HTTP 412 Precondition failure" 応答コードを返します。 この場合、クライアントは、リソースを再フェッチしてリソースの最新の ETag 値を取得します。 また、ETag を If-None-Match ヘッダーと共に使用すると、リソースの再フェッチが必要かどうかを判断できます。

.NET でオプティミスティック同時実行制御を使用するには、 [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) クラスを使用します。 .NET サンプルについては、GitHub にある DocumentManagement サンプルの [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) を参照してください。

### <a name="how-do-i-perform-transactions-in-the-documentdb-api"></a>DocumentDB API でトランザクションを実行するにはどうすればよいですか?
DocumentDB API は、JavaScript のストアド プロシージャとトリガーによる、統合言語のトランザクションをサポートしています。 スクリプト内のすべてのデータベース操作は、スナップショット分離下で実行されます。 単一パーティション コレクションの場合は、コレクションが実行対象となります。 コレクションがパーティション分割されている場合は、コレクション内の同じパーティション キー値を持つドキュメントが実行対象となります。 ドキュメント バージョン (ETag) のスナップショットは、トランザクションの開始時に取得され、スクリプトが成功された場合のみコミットされます。 JavaScript がエラーをスローした場合、トランザクションはロールバックされます。 詳細については、「[Azure Cosmos DB のサーバー側プログラミング](programming.md)」を参照してください。

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>ドキュメントを Cosmos DB に一括挿入するにはどうすればよいですか?
Azure Cosmos DB へのドキュメントの一括挿入は、次のいずれかの方法で実行できます。

* データ移行ツール。[Azure Cosmos DB 用のデータベース移行ツール](import-data.md)に関する記事で説明されています。
* ストアド プロシージャ。[Azure Cosmos DB のサーバー側 JavaScript プログラミング](programming.md)に関する記事で説明されています。

### <a name="does-the-documentdb-api-support-resource-link-caching"></a>DocumentDB API はリソース リンク キャッシュをサポートしていますか?
はい。Azure Cosmos DB は RESTful サービスであるため、リソース リンクは不変であり、キャッシュできます。 DocumentDB API クライアントは、ドキュメントやコレクションなどのリソースに対する読み取りに "If-None-Match" ヘッダーを指定し、サーバー バージョンが変更されたらローカル コピーを更新できます。

### <a name="is-a-local-instance-of-documentdb-api-available"></a>DocumentDB API のローカル インスタンスは使用できますか?
はい。 [Azure Cosmos DB Emulator](local-emulator.md) には、Cosmos DB サービスの高忠実度エミュレーションが用意されています。 JSON ドキュメントの作成とクエリ、コレクションのプロビジョニングとスケーリング、ストアド プロシージャとトリガーの実行のサポートなど、Azure Cosmos DB と同じ機能がサポートされています。 Azure Cosmos DB Emulator を使用してアプリケーションの開発とテストを行い、Azure Cosmos DB の接続エンドポイントの構成を 1 つ変更するだけで、世界規模で Azure にデプロイできます。

## <a name="develop-against-the-api-for-mongodb"></a>MongoDB 用 API に対する開発
### <a name="what-is-the-azure-cosmos-db-api-for-mongodb"></a>Azure Cosmos DB の MongoDB 用 API とは何ですか?
Azure Cosmos DB の MongoDB 用 API は、コミュニティでサポートされる既存の Apache MongoDB API とドライバーを使用して、アプリケーションがネイティブの Azure Cosmos DB データベース エンジンと簡単かつ透過的に通信できるようにする互換性レイヤーです。 開発者は、既存の MongoDB ツール チェーンとスキルを使って、Azure Cosmos DB を活用するアプリケーションを構築できるようになりました。 これにより、自動インデックス作成、バックアップ メンテナンス、利用料金に基づくサービス レベル アグリーメント (SLA) など、Azure Cosmos DB の独自の機能によるメリットが得られます。

### <a name="how-do-i-connect-to-my-api-for-mongodb-database"></a>MongoDB 用 API データベースに接続するにはどうすればよいですか?
Azure Cosmos DB の MongoDB 用 API に接続する最も簡単な方法は、[Azure Portal](https://portal.azure.com) を使用することです。 アカウントに移動し、左側のナビゲーション メニューで **[クイック スタート]** をクリックします。 クイック スタートは、コード スニペットを取得してデータベースに接続するための最善の方法です。 

Azure Cosmos DB では、厳密なセキュリティ要件と基準が適用されます。 Azure Cosmos DB アカウントでは、SSL による認証とセキュリティで保護された通信が要求されるので、必ず TLSv1.2 を使用してください。

詳細については、[MongoDB 用 API データベースへの接続](connect-mongodb-account.md)に関する記事をご覧ください。

### <a name="are-there-additional-error-codes-for-an-api-for-mongodb-database"></a>MongoDB 用 API にはその他のエラー コードはありますか?
MongoDB 用 API には、一般的な MongoDB エラー コードのほかに、独自のエラー コードがあります。


| エラー               | コード  | 説明  | 解決策  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | 使用された要求ユニットの合計数が、コレクションのプロビジョニング済み要求ユニット レートを超えたため調整されました。 | Azure Portal でのコレクションのスループットのスケーリングか、再試行を検討してください。 |
| ExceededMemoryLimit | 16501 | マルチ テナント サービスとして、操作がクライアントのメモリ配分を超えました。 | より制限の厳しいクエリ条件によって操作のスコープを減らすか、[Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) からサポートに連絡してください。 <br><br>例: *&nbsp;&nbsp;&nbsp;&nbsp;db.getCollection('users').aggregate([<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$match: {name: "Andy"}}, <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$sort: {age: -1}}<br>&nbsp;&nbsp;&nbsp;&nbsp;])*) |

## <a name="develop-with-the-table-api-preview"></a>Table API (プレビュー) を使用した開発

### <a name="terms"></a>用語 
Azure Cosmos DB: Table API (プレビュー) は、テーブルをサポートするためにビルド 2017 で発表された、Azure Cosmos DB が提供する Premium サービスを指します。 

Standard Table SDK は、従来の Azure Storage Table SDK です。 

### <a name="how-can-i-use-the-new-table-api-preview-offering"></a>新しい Table API (プレビュー) を使用するにはどうすればよいですか? 
Azure Cosmos DB Table API は [Azure Portal][azure-portal] で利用できます。 まず、Azure サブスクリプションにサインアップする必要があります。 サインアップしたら、Azure サブスクリプションに Azure Cosmos DB Table API アカウントを追加し、アカウントにテーブルを追加できます。 

プレビュー期間は、.NET で [SDK](../cosmos-db/table-sdk-dotnet.md) を使用できます。まず、[Table API](../cosmos-db/create-table-dotnet.md) のクイック スタートを完了してください。

### <a name="do-i-need-a-new-sdk-to-use-the-table-api-preview"></a>Table API (プレビュー) を使用するには新しい SDK が必要ですか? 
はい。NuGet で [Windows Azure Storage Premium Table (プレビュー) SDK](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable) を入手できます。 追加情報については、[Azure Cosmos DB Table .NET API のダウンロードとリリース ノート](https://github.com/Microsoft/azure-docs-pr/cosmos-db/table-sdk-dotnet.md)のページをご覧ください。 

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>SDK やバグに関するフィードバックを提供するにはどうすればよいですか?
次のいずれかの方法でフィードバックをお寄せください。

* [Uservoice](https://feedback.azure.com/forums/599062-azure-cosmos-db-table-api)
* [MSDN フォーラム](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=AzureDocumentDB)
* [StackOverflow](http://stackoverflow.com/questions/tagged/azure-cosmosdb)

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api-preview"></a>Table API (プレビュー) に接続する際に使用する必要がある接続文字列を教えてください。
接続文字列は次のとおりです。
```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountNameFromDocumentDB>.documents.azure.com
```
接続文字列は、Azure Portal の [キー] ページから取得できます。 

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-new-table-api-preview"></a>新しい Table API (プレビュー) で、要求オプションの構成設定を上書きするにはどうすればよいですか?
構成設定については、「[Azure Cosmos DB の機能](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities)」をご覧ください。 設定を変更するには、クライアント アプリケーションの app.config の appSettings セクションに設定を追加します。

    <appSettings>
        <add key="TableConsistencyLevel" value="Eventual|Strong|Session|BoundedStaleness|ConsistentPrefix"/>
        <add key="TableThroughput" value="<PositiveIntegerValue"/>
        <add key="TableIndexingPolicy" value="<jsonindexdefn>"/>
        <add key="TableUseGatewayMode" value="True|False"/>
        <add key="TablePreferredLocations" value="Location1|Location2|Location3|Location4>"/>....
    </appSettings>


### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-standard-table-sdk"></a>既存の Standard Table SDK を使用する顧客を対象とする変更はありますか?
なし。 既存の Standard Table SDK を使用する既存または新規のお客様を対象とする変更はありません。 

### <a name="how-do-i-view-table-data-that-is-stored-in-azure-cosmos-db-for-use-with-the-table-api-review"></a>Table API (プレビュー) で使用するために、Azure Cosmos DB に格納されているテーブル データを表示するにはどうすればよいですか? 
Azure Portal を使用してデータを参照できます。 また、Table API (プレビュー) コードまたは次の回答で説明するツールを使用することもできます。 

### <a name="which-tools-work-with-the-table-api-preview"></a>Table API (プレビュー) で動作するのはどのツールですか? 
以前のバージョンの Azure Explorer (0.8.9) を使用できます。

以前に指定した形式の接続文字列を取得する柔軟性を備えたツールは、新しい Table API (プレビュー) に対応します。 テーブル ツールの一覧については、「[Azure Storage クライアント ツール](../storage/common/storage-explorers.md)」をご覧ください。 

### <a name="do-powershell-or-azure-cli-work-with-the-new-table-api-preview"></a>PowerShell または Azure CLI は新しい Table API (プレビュー) で動作しますか?
Table API (プレビュー) 用の PowerShell と Azure CLI のサポートを追加する予定です。 

### <a name="is-the-concurrency-on-operations-controlled"></a>操作の同時実行は制御されますか?
はい。オプティミスティック同時実行制御は、ETag メカニズムを使用して提供されます。 

### <a name="is-the-odata-query-model-supported-for-entities"></a>エンティティの OData クエリ モデルはサポートされていますか? 
はい。Table API (プレビュー) では、OData クエリと LINQ クエリをサポートしています。 

### <a name="can-i-connect-to-the-standard-azure-table-and-the-new-premium-table-api-preview-side-by-side-in-the-same-application"></a>同じアプリケーションで Standard Azure Table と新しい Premium Table API (プレビュー) に同時に接続できますか? 
はい。同時に接続するには、CloudTableClient の 2 つのインスタンスを作成し、各インスタンスで接続文字列を使用して独自の URI を参照します。

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-new-offering"></a>既存の Azure Table Storage アプリケーションをこの新しいサービスに移行するにはどうすればよいですか?
既存の Table Storage データで新しい Table API を利用する場合は、[askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) までご連絡ください。 

### <a name="what-is-the-roadmap-for-this-service-and-when-will-you-offer-other-standard-table-api-functionality"></a>このサービスのロードマップを教えてください。Standard Table API の他の機能はいつ提供されますか?
GA に向けて、SAS トークン、ServiceContext、統計、クライアント側暗号化、分析などの機能のサポートを追加する予定です。 [UserVoice](https://feedback.azure.com/forums/599062-azure-cosmos-db-table-api) でフィードバックをお寄せください。 

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>たとえば、最初は *n* GB のデータが時間の経過と共に 1 TB に増加した場合、このサービスではストレージ サイズはどのように拡張されるのですか? 
Azure Cosmos DB は、水平スケーリングを使用して無制限のストレージを提供するように設計されています。 このサービスは、ストレージを監視し、ストレージを効率的に増やすことができます。 

### <a name="how-do-i-monitor-the-table-api-preview-offering"></a>Table API (プレビュー) を監視するにはどうすればよいですか?
Table API (プレビュー) の**メトリック** ウィンドウを使用して、要求とストレージ使用量を監視できます。 

### <a name="how-do-i-calculate-the-throughput-i-require"></a>必要なスループットを計算するにはどうすればよいですか?
容量見積もりツールを使用して、操作に必要な TableThroughput を計算できます。 詳細については、「[Estimate Request Units and Data Storage (要求ユニットとデータ ストレージの見積もり)](https://www.documentdb.com/capacityplanner)」をご覧ください。 一般に、エンティティを JSON として表し、操作数を指定します。 

### <a name="can-i-use-the-new-table-api-preview-sdk-locally-with-the-emulator"></a>エミュレーターで新しい Table API (プレビュー) SDK をローカルで使用できますか?
新しい SDK を使用すると、ローカル エミュレーターで Table API (プレビュー) を使用できます。 新しいエミュレーターをダウンロードする場合は、「[Use the Azure Cosmos DB Emulator for local development and testing (ローカルの開発とテストでの Azure Cosmos DB Emulator の使用)](local-emulator.md)」をご覧ください。 app.config 内の StorageConnectionString 値が次のようになっている必要があります。

```
DefaultEndpointsProtocol=https;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=https://localhost:8081`. 
```

### <a name="can-my-existing-application-work-with-the-table-api-preview"></a>既存のアプリケーションを Table API (プレビュー) で動作させることはできますか? 
新しい Table API (プレビュー) の表面領域は、.NET API での作成、削除、更新、クエリの各操作で既存の Azure Standard Table SDK と互換性があります。 Table API (プレビュー) ではパーティション キーと行キーの両方が必要となるため、行キーがあることを確認してください。 また、このサービスの GA に向けて、SDK のサポートをさらに追加する予定です。

### <a name="do-i-need-to-migrate-my-existing-azure-table-based-applications-to-the-new-sdk-if-i-do-not-want-to-use-the-table-api-preview-features"></a>Table API (プレビュー) の機能を使用しない場合、Azure テーブル ベースの既存のアプリケーションを新しい SDK に移行する必要はありますか?
いいえ。中断することなく、Standard Table 資産を作成したり、既存の資産を使用したりできます。 ただし、新しい Table API (プレビュー) を使用しない場合、自動インデックス、追加の整合性オプション、またはグローバル分散によるメリットは得られません。 

### <a name="how-do-i-add-replication-of-the-data-in-the-premium-table-api-preview-across-multiple-regions-of-azure"></a>Premium Table API (プレビュー) で、Azure の複数のリージョン間でのデータのレプリケーションを追加するにはどうすればよいですか?
Azure Cosmos DB ポータルの[グローバル レプリケーション設定](tutorial-global-distribution-documentdb.md#portal)を使用して、アプリケーションに適したリージョンを追加できます。 グローバル分散型のアプリケーションを開発するには、低待機時間の読み取りを実現するために、PreferredLocation 情報をローカル リージョンに設定したアプリケーションを追加する必要があります。 

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-premium-table-api-preview"></a>Premium Table API (プレビュー) でアカウントのプライマリ書き込みリージョンを変更するにはどうすればよいですか?
Azure Cosmos DB のグローバル レプリケーション ポータル ウィンドウを使用してリージョンを追加し、必要なリージョンにフェールオーバーできます。 手順については、[複数リージョンの Azure Cosmos DB アカウントを使用した開発](regional-failover.md)に関する記事をご覧ください。 

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>データを分散するときに低待機時間を実現するために、優先読み取りリージョンを構成するにはどうすればよいですか? 
ローカルの場所から読み取りを実行できるようにするには、app.config ファイルの PreferredLocation キーを使用します。 既存のアプリケーションの場合、LocationMode が設定されていると、Table API (プレビュー) はエラーをスローします。 Premium Table API (プレビュー) は app.config ファイルからこの情報を取得するので、該当のコードを削除してください。 詳細については、「[Azure Cosmos DB の機能](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities)」をご覧ください。

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api-preview"></a>Table API (プレビュー) の整合レベルについてどのように考えればよいですか? 
Azure Cosmos DB では、整合性、可用性、待機時間の最適なトレードオフを提供します。 Azure Cosmos DB には 5 つの整合性レベルが用意されているので、Table API (プレビュー) 開発者は、データを照会するときにテーブル レベルで最適な整合性モデルを選択し、個々の要求を行うことができます。 クライアントは、接続時に整合性レベルを指定できます。 app.config で TableConsistencyLevel キーの値を設定することで、レベルを変更できます。 

Table API (プレビュー) では、既定でセッションの整合性が適用され、"自身の書き込みの読み取り" によって低待機時間の読み取りを実現します。 詳細については、[整合性レベル](consistency-levels.md)に関する記事をご覧ください。 

既定では、Azure Standard Table はリージョン内では厳密な整合性を提供し、セカンダリの場所では最終的な整合性を提供します。 

### <a name="does-azure-cosmos-db-offer-more-consistency-levels-than-standard-tables"></a>Azure Cosmos DB では、Standard Table よりも多くの整合性レベルが提供されるのですか?
はい。Azure Cosmos DB の分散特性のメリットを得る方法については、「[一貫性レベル](consistency-levels.md)」をご覧ください。 整合性レベルは保証の対象となるため、安心して使用できます。 詳細については、「[Azure Cosmos DB の機能](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities)」をご覧ください。

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>グローバル分散を有効にした場合、データのレプリケーションにどのくらいの時間がかかりますか?
データはローカル リージョンで永続的にコミットされ、ほんの数ミリ秒ですぐに他のリージョンにプッシュされます。 このレプリケーションは、データ センターのラウンドトリップ時間 (RTT) にのみ依存します。 Azure Cosmos DB のグローバル分散機能の詳細については、[Azure Cosmos DB: Azure のグローバル分散データベース サービス](distribute-data-globally.md)に関する記事をご覧ください。

### <a name="can-the-read-request-consistency-level-be-changed"></a>読み取り要求の整合性レベルを変更することはできますか?
Azure Cosmos DB では、コンテナー レベル (テーブル) で整合性レベルを設定できます。 レベルを変更するには、SDK を使用して、app.config ファイルで TableConsistencyLevel キーの値を指定します。 指定できる値は、Strong、Bounded Staleness、Session、Consistent Prefix、Eventual です。 詳細については、「[Azure Cosmos DB の調整可能なデータの一貫性レベル](consistency-levels.md)」をご覧ください。 基本的な考え方として、要求の整合性レベルは、テーブルの設定よりも高いレベルに設定することはできません。 たとえば、テーブルの整合性レベルを Eventual (最終的) に設定し、要求の整合性レベルを Strong (厳密) に設定することはできません。 

### <a name="how-does-the-premium-table-api-preview-account-handle-failover-if-a-region-goes-down"></a>リージョンがダウンした場合、Premium Table API (プレビュー) アカウントではフェールオーバーにどのように対処するのでしょうか? 
Premium Table API (プレビュー) では、Azure Cosmos DB のグローバル分散型プラットフォームを利用します。 アプリケーションがデータ センターのダウンタイムを許容できるようにするには、Azure Cosmos DB ポータルでアカウントのリージョンを少なくとももう 1 つ有効にします ([複数リージョンの Azure Cosmos DB アカウントを使用した開発](regional-failover.md)に関する記事を参照)。 ポータルを使用してリージョンの優先順位を設定できます ([複数リージョンの Azure Cosmos DB アカウントを使用した開発](regional-failover.md)に関する記事を参照)。 

アカウントのリージョンを必要な数だけ追加し、フェールオーバーの優先順位を指定してフェールオーバー先を制御できます。 データベースを使用するには、そのリージョンでもアプリケーションを提供する必要があります。 そうすれば、ダウンタイムが発生しなくなります。 クライアント SDK は自動回帰します。 つまり、ダウンしているリージョンを検出すると、新しいリージョンに自動的にフェールオーバーできます。

### <a name="is-the-premium-table-api-preview-enabled-for-backups"></a>Premium Table API (プレビュー) ではバックアップは有効になっていますか?
はい。Premium Table API (プレビュー) では、バックアップに Azure Cosmos DB のプラットフォームを利用します。 バックアップは自動的に作成されます。 詳細については、[Azure Cosmos DB でのオンライン バックアップと復元](online-backup-and-restore.md)に関する記事をご覧ください。

 
### <a name="does-the-table-api-preview-index-all-attributes-of-an-entity-by-default"></a>Table API (プレビュー) では、既定でエンティティのすべての属性のインデックスが作成されるのですか?
はい。エンティティのすべての属性のインデックスが既定で作成されます。 詳細については、[Azure Cosmos DB インデックス作成ポリシー](indexing-policies.md)に関する記事をご覧ください。 

### <a name="does-this-mean-i-do-not-have-to-create-multiple-indexes-to-satisfy-the-queries"></a>つまり、クエリを満たすために複数のインデックスを作成する必要はないということですか? 
はい。Azure Cosmos DB はすべての属性の自動インデックス作成機能を備えています。スキーマ定義は不要です。 この自動化により、開発者はインデックスの作成と管理ではなく、アプリケーションに注力できるようになります。 詳細については、[Azure Cosmos DB インデックス作成ポリシー](indexing-policies.md)に関する記事をご覧ください。

### <a name="can-i-change-the-indexing-policy"></a>インデックス作成ポリシーは変更できますか?
はい。インデックス定義を提供することでインデックス作成ポリシーを変更できます。 詳細については、「[Azure Cosmos DB の機能](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities)」をご覧ください。 設定を適切にエンコードし、エスケープする必要があります。 

app.config ファイルに JSON 形式の文字列で指定します。
```
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
プレビューでは、Table API は Azure Table Storage と同じクエリ機能を提供します。 また、並べ替え、集計、地理空間クエリ、階層、さまざまな組み込み関数もサポートしています。 将来のサービス更新プログラムで Table API の追加機能が提供される予定です。 詳細については、「[Azure Cosmos DB DocumentDB API の SQL クエリ](../documentdb/documentdb-sql-query.md)」を参照してください。
 
### <a name="when-should-i-change-tablethroughput-for-the-table-api-preview"></a>Table API (プレビュー) の TableThroughput は、どのようなときに変更する必要がありますか?
次のいずれかの条件に該当する場合は、TableThroughput を変更してください。
* データの抽出、変換、読み込み (ETL) を実行している。または、短時間に大量のデータをアップロードする必要がある。 
* バックエンドでコンテナーのスループットを増やす必要がある  (たとえば、使用されたスループットがプロビジョニング スループットを超えており、調整が行われている)。 詳細については、「[Azure Cosmos DB コンテナーのスループットの設定](set-throughput.md)」を参照してください。

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-preview-table"></a>Table API (プレビュー) のテーブルのスループットはスケールアップまたはスケールダウンできますか? 
はい。スループットのスケーリングは、Azure Cosmos DB ポータルのスケール ウィンドウを使用して実行できます。 詳細については、[スループットの設定](set-throughput.md)に関する記事をご覧ください。

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>新しくプロビジョニングされたテーブルには、既定の TableThroughput が設定されるのですか?
はい。app.config で TableThroughput を上書きしておらず、Azure Cosmos DB であらかじめ作成されているコンテナーを使用していない場合、スループットが 400 に設定されたテーブルが作成されます。
 
### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-standard-table-api"></a>Standard Table API の既存の顧客を対象とする価格の変更はありますか?
なし。 Standard Table API の既存のお客様を対象とする価格の変更はありません。 

### <a name="how-is-the-price-calculated-for-the-table-api-preview"></a>Table API (プレビュー) の料金はどのように計算されますか? 
料金は、割り当てられた TableThroughput によって異なります。 

### <a name="how-do-i-handle-any-throttling-on-the-tables-in-table-api-preview-offering"></a>Table API (プレビュー) では、テーブルの調整にどのように対処すればよいですか? 
要求レートが基になるコンテナーのプロビジョニング スループットの容量を超えると、エラーが発生し、SDK は再試行ポリシーを適用して呼び出しを再試行します。

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-premium-table-api-preview-offering-of-azure-cosmos-db"></a>Azure Cosmos DB の Premium Table API (プレビュー) を利用する際に、PartitionKey と RowKey 以外にスループットを選択する必要があるのはなぜですか?
Azure Cosmos DB では、app.config ファイルにコンテナーのスループットが指定されていない場合、既定のスループットが設定されます。 

Azure Cosmos DB では、操作に上限を設定してパフォーマンスと待機時間を保証します。 この保証は、エンジンがテナントの操作にガバナンスを適用できる場合に可能になります。 TableThroughput を設定すると、プラットフォームでこの容量が予約され、操作が正常に完了することが保証されるので、保証されたスループットと待機時間が確保されます。 

また、スループットの仕様により、スループットを弾力的に変更して、アプリケーションの季節性によるメリットを享受し、スループットのニーズを満たして、コストを削減できます。

### <a name="azure-storage-sdk-has-been-very-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-new-azure-cosmos-db-offering-seems-to-be-charging-me-even-though-i-have-not-performed-a-single-transaction-or-stored-anything-can-you-please-explain"></a>データの保存にしか料金を支払っておらず、クエリを実行することはほとんどないため、Azure Storage SDK は非常に安価でした。 新しい Azure Cosmos DB では、トランザクションを 1 つも実行していない場合や何も保存していない場合でも課金されているようです。 説明していただけますか?

Azure Cosmos DB は、可用性、待機時間、スループットが保証された、グローバル分散型の SLA ベースのシステムとして設計されています。 他のシステムのスループットとは異なり、Azure Cosmos DB でスループットを予約すると、そのスループットが保証されます。 Azure Cosmos DB には、セカンダリ インデックスやグローバル分散など、お客様から要望があった追加機能が用意されています。 プレビュー期間中はスループットに最適化されたモデルを提供していますが、最終的には、お客様のニーズを満たすためにストレージに最適化されたモデルを提供する予定です。 

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-table-storage-with-the-table-api-preview-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Table Storage にデータを取り込んだときに、(パーティションが満杯であることを示す) "クォータが上限に達した" ことを通知するメッセージが表示されたことは一度もありませんでした。 Table API (プレビュー) では、このメッセージが表示されました。 このサービスには制限があり、既存のアプリケーションを変更しなければならないのでしょうか?

Azure Cosmos DB は、待機時間、スループット、可用性、整合性を保証し、無制限のスケールを提供する SLA ベースのシステムです。 保証された Premium パフォーマンスを確保するために、データ サイズとインデックスが管理可能であり、スケーラブルであることを確認してください。 パーティション キーごとのエンティティ数または項目数に 10 GB の制限を設けているのは、検索やクエリの優れたパフォーマンスを確実に提供するためです。 すべての情報を 1 つのパーティションに格納し、そのパーティションに対してクエリを実行すると、ホット パーティションになります。Azure Storage でもアプリケーションが適切にスケールできるように、ホット パーティションが発生*しない*ようにすることをお勧めします。 

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-new-table-api-preview"></a>新しい Table API (プレビュー) で PartitionKey と RowKey が必要なのはそのためですか? 
はい。 Table API (プレビュー) の表面領域は Table Storage SDK の表面領域とほぼ同じであるため、パーティション キーによってデータを効率的に分散させることができます。 行キーはそのパーティション内で一意です。 行キーが存在する必要があり、Standard SDK の場合と同様に、行キーを null にすることはできません。 RowKey の長さは 255 バイト、PartitionKey の長さは 100 バイトです (間もなく 1 KB に増えます)。 

### <a name="what-are-the-error-messages-for-the-table-api-preview"></a>Table API (プレビュー) のエラー メッセージはどのようなものですか?
このプレビューは Standard Table と互換性があるため、ほとんどのエラーが Standard Table のエラーにマップされます。 

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api-preview"></a>Table API (プレビュー) で多数のテーブルを次々に作成しようとすると調整が行われるのはなぜですか?
Azure Cosmos DB は、待機時間、スループット、可用性、整合性を保証する SLA ベースのシステムです。 Azure Cosmos DB はプロビジョニングされるシステムであるため、これらの要件を保証するためにリソースが予約されています。 テーブルを次々に作成すると、その作成ペースが検出され、調整が行われます。 テーブルの作成ペースを確認し、1 分あたり 5 つ未満に抑えることをお勧めします。 Table API (プレビュー) はプロビジョニングされるシステムであることに注意してください。 プロビジョニングした時点から料金が発生します。 

## <a name="develop-against-the-graph-api-preview"></a>Graph API (プレビュー) に対する開発
### <a name="how-can-i-apply-the-functionality-of-graph-api-preview-to-azure-cosmos-db"></a>Graph API (プレビュー) の機能を Azure Cosmos DB に適用するにはどうすればよいですか?
拡張ライブラリを使用して、Graph API (プレビュー) の機能を適用できます。 このライブラリは Microsoft Azure Graphs と呼ばれ、NuGet で提供されます。 

### <a name="it-looks-like-you-support-the-gremlin-graph-traversal-language-do-you-plan-to-add-more-forms-of-query"></a>グラフ トラバーサル言語の Gremlin がサポートされているようですが、 クエリの他の形式を追加する予定はありますか?
はい。将来、クエリの他のメカニズムを追加する予定です。 

### <a name="how-can-i-use-the-new-graph-api-preview-offering"></a>新しい Graph API (プレビュー) を使用するにはどうすればよいですか? 
まず、[Graph API](../cosmos-db/create-graph-dotnet.md) のクイック スタートを完了してください。

<a id="moving-to-cosmos-db"></a>
## <a name="questions-from-documentdb-customers"></a>DocumentDB のお客様からの質問
### <a name="why-are-you-moving-to-azure-cosmos-db"></a>Azure Cosmos DB に移行するのはなぜですか? 

Azure Cosmos DB は、地理的な制約なく世界規模で分散できる次世代のクラウド データベースです。 DocumentDB をご利用のお客様には、Azure Cosmos DB が備えている革新的なシステムと機能が自動的に提供されます。

Azure Cosmos DB は、Microsoft 社内の大規模なアプリケーションを構築する際に開発者が直面する問題に対処するために、2010 年に "Project Florence" として運用が開始されました。 グローバル分散型のアプリケーションを構築する作業が問題となるのは Microsoft だけではないことから、Azure の開発者を対象に、2015 年にこのテクノロジの第 1 世代を Azure DocumentDB という形で提供するようになりました。 

そのときからずっと Microsoft は新しい機能を追加してきており、重要な新機能も導入しています。 その結果誕生したのが Azure Cosmos DB です。 今回 Azure Cosmos DB がリリースされたことを受けて、DocumentDB をご利用のお客様は、そのデータも含め、Azure Cosmos DB に自動的かつシームレスに移行します。 これらの機能は、核となるデータベース エンジンの領域のほか、グローバル分散、弾力性のあるスケーラビリティ、業界最先端の包括的な SLA に及んでいます。 具体的には、一般的なあらゆるデータ モデル、型システム、API を、Azure Cosmos DB の基になるデータ モデルに効率的にマップするために、Azure Cosmos DB データベース エンジンを進化させました。 

この取り組みの成果として、開発者向けに [Gremlin](../cosmos-db/graph-introduction.md) と [Table Storage API](../cosmos-db/table-introduction.md) が新たにサポートされるようになりました。 そして、これはまだ始まりにすぎません。 世界規模でパフォーマンスとストレージをさらに進歩させながら、他の一般的な API や新しいデータ モデルを徐々に追加していく予定です。 

重要なことですが、DocumentDB の [SQL Dialect](../documentdb/documentdb-sql-query.md) は、基になる Azure Cosmos DB でサポートされる多数の API の 1 つにすぎません。 Azure Cosmos DB のように、完全に管理されたサービスを使用する開発者にとって、そのサービスとの唯一のインターフェイスは、サービスによって公開されている API です。 DocumentDB を既に利用されているお客様の場合、実際には何も変わりません。 Azure Cosmos DB では、DocumentDB で提供されるものとまったく同じ SQL API を利用できます。 現在 (そして今後も)、以前は利用できなかった他の機能を利用できます。 

Microsoft のたゆみない作業のもう 1 つの成果は、スループットとストレージを世界規模で伸縮自在にスケールする素地です。 Microsoft はグローバル分散サブシステムに対し、いくつかの根本的な機能強化を実施しました。 多くの開発者向け機能の 1 つに、"一貫性のあるプレフィックス" 整合性モデルがあります。これで、明確に定義された整合性モデルは全部で 5 つになります。 ほかにも多くの魅力的な機能があり、完成の域に達した時点でリリースしていく予定です。 

### <a name="what-do-i-need-to-do-to-ensure-that-my-documentdb-resources-continue-to-run-on-azure-cosmos-db"></a>DocumentDB リソースを Azure Cosmos DB で引き続き実行できるようにするには何をすればよいですか?

お客様は何も変更する必要はありません。 ご利用の DocumentDB リソースは既に Azure Cosmos DB リソースになっています。また、この移行時にサービスの中断は発生していません。

### <a name="what-changes-do-i-need-to-make-for-my-app-to-work-with-azure-cosmos-db"></a>Azure Cosmos DB と連動させるために、既存のアプリにはどのような変更を加えればよいですか?

何も変更を加える必要はありません。 クラスや名前空間、NuGet パッケージの名前は変わっていません。 日頃から Microsoft が推奨しているように、最新の機能と改良点を最大限に活かすために SDK は最新の状態に保ってください。 

### <a name="whats-changed-in-the-azure-portal"></a>Azure Portal に変更はありますか?

DocumentDB は、Azure サービスとしてポータルに表示されなくなります。 次の図に示すように、代わりに新しい Azure Cosmos DB アイコンが表示されます。 既存のコレクションはすべてこれまでと同様に利用できます。また、スループットのスケーリング、整合性レベルの変更、SLA の監視も引き続き行うことができます。 データ エクスプローラー (プレビュー) の機能が強化されています。 次の図に示すように、ドキュメントの表示と編集、クエリの作成と実行、ストアド プロシージャ、トリガー、UDF の操作を 1 つのページから実行できるようになりました。 

![Azure Cosmos DB コレクション ブレード](./media/faq/cosmos-db-data-explorer.png)

### <a name="are-there-changes-to-pricing"></a>料金に変更はありますか?

いいえ。Azure Cosmos DB でアプリを実行するコストは以前と同じです。

### <a name="are-there-changes-to-the-slas"></a>SLA に変更はありますか?

いいえ。可用性、一貫性、待ち時間、スループットに関する SLA は未変更のままポータルに表示されています。 詳細については、「[Azure Cosmos DB の SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/)」をご覧ください。
   
![to-do アプリとサンプル データ](./media/faq/azure-cosmosdb-portal-metrics-slas.png)


[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md
