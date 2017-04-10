---
title: "DocumentDB データベース質問 - よく寄せられる質問 | Microsoft Docs"
description: "JSON 用の NoSQL ドキュメント データベース サービスである Azure DocumentDB に関してよく寄せられる質問に対する回答を示します。 容量、パフォーマンス レベル、およびスケーリングに関するデータベース質問に回答します。"
keywords: "データベース質問, よく寄せられる質問, documentdb, azure, Microsoft azure"
services: documentdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: b68d1831-35f9-443d-a0ac-dad0c89f245b
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 8ebc1aa663f298d1f3f495523d85bda8777d5d29
ms.lasthandoff: 03/10/2017


---
# <a name="frequently-asked-questions-about-documentdb"></a>DocumentDB に関してよく寄せられる質問
## <a name="database-questions-about-microsoft-azure-documentdb-fundamentals"></a>Microsoft Azure DocumentDB の基礎に関するデータベース質問
### <a name="what-is-microsoft-azure-documentdb"></a>Microsoft Azure DocumentDB とは
Microsoft Azure DocumentDB は、超高速かつ世界規模の NoSQL ドキュメント データベースを提供する、サービスとしてのデータベースです。Microsoft Azure の強力な機能と守備範囲に支えられた管理プラットフォームを通じて、スキーマフリーのデータに対して高度なクエリを実行できるほか、構成可能で信頼性の高いパフォーマンスや、迅速な開発が実現できます。 DocumentDB は、予測可能なスループット、高い可用性、遅延時間の短縮、およびスキーマフリー データ モデルが重要な要件となる、Web、モバイル、ゲーム、IoT の各アプリケーションに適したソリューションです。 DocumentDB では、ネイティブ JSON データ モデルでスキーマの柔軟性と豊富なインデックス作成機能が提供され、統合 JavaScript でマルチドキュメント トランザクションがサポートされます。  

データベース質問および回答の詳細と、このサービスのデプロイおよび使用の手順については、 [DocumentDB ドキュメント ページ](https://azure.microsoft.com/documentation/services/documentdb/)を参照してください。

### <a name="what-kind-of-database-is-documentdb"></a>DocumentDB はどのような種類のデータベースですか?
DocumentDB は、JSON 形式でデータを格納する NoSQL ドキュメント指向のデータベースです。  DocumentDB では、高度な DocumentDB [SQL クエリ文法](documentdb-sql-query.md)で照会できる入れ子になった自立的データ構造がサポートされます。 DocumentDB では、[ストアド プロシージャ、トリガー、およびユーザー定義関数](documentdb-programming.md)を使用して、サーバー側 JavaScript のトランザクション処理を高いパフォーマンスで実行できます。 また、開発者によって調整可能な一貫性レベルを、関連付けられた [パフォーマンス レベル](documentdb-performance-levels.md)と共にサポートします。

### <a name="do-documentdb-databases-have-tables-like-a-relational-database-rdbms"></a>DocumentDB データベースはリレーショナル データベース (RDBMS) のようなテーブルを保持しますか?
いいえ、DocumentDB は JSON ドキュメントのコレクション内にデータを格納します。  DocumentDB のリソースの詳細については、「 [DocumentDB のリソース モデルと概念](documentdb-resources.md)」を参照してください。 DocumentDB のような NoSQL ソリューションとリレーショナル ソリューションの相違点の詳細については、「 [NoSQL 対 SQL](documentdb-nosql-vs-sql.md)」を参照してください。

### <a name="do-documentdb-databases-support-schema-free-data"></a>DocumentDB データベースはスキーマフリー データをサポートしますか?
はい、DocumentDB ではスキーマ定義またはヒントなしで、アプリケーションが任意の JSON ドキュメントを格納できます。 DocumentDB SQL クエリ インターフェイスを使用したクエリに、データをすぐに利用できます。   

### <a name="does-documentdb-support-acid-transactions"></a>DocumentDB は ACID トランザクションをサポートしますか?
はい、DocumentDB では、JavaScript ストアド プロシージャおよびトリガーとして表現されるクロスドキュメント トランザクションをサポートします。 トランザクションは、各コレクション内の単一のパーティションを対象とし、他の同時実行されるコードおよびユーザー要求から完全に分離された ACID セマンティクスで実行されます。  JavaScript アプリケーション コードのサーバー側実行により例外がスローされた場合は、トランザクション全体がロールバックされます。 トランザクションの詳細については、「 [プログラム データベース トランザクション](documentdb-programming.md#database-program-transactions)」を参照してください。

### <a name="what-are-the-typical-use-cases-for-documentdb"></a>DocumentDB の一般的な使用事例にはどのようなものがありますか?
DocumentDB は、自動スケール、予測可能なパフォーマンス、ミリ秒レベルの高速な応答時間、およびスキーマフリー データに対してクエリを実行できることが重要である新しい Web アプリケーション、モバイル アプリケーション、ゲーム、IoT アプリケーションに適しています。 DocumentDB は、迅速な開発に役立ち、アプリケーション データ モデルの継続的な反復をサポートします。 ユーザーが生成したコンテンツとデータを管理するアプリケーションは、 [DocumentDB の一般的な使用事例](documentdb-use-cases.md)です。  

### <a name="how-does-documentdb-offer-predictable-performance"></a>DocumentDB では、予測可能なパフォーマンスをどのようにして実現していますか?
DocumentDB ではスループットの単位として [要求ユニット](documentdb-request-units.md) (RU) を使用します。 1 RU は、1KB のドキュメントを取得するスループットに相当します。 DocumentDB におけるすべての操作 (読み取り、書き込み、SQL クエリ、ストアド プロシージャの実行など) には、操作を完了するのに必要なスループットに基づいて、明確な RU 値が設定されています。 CPU、IO、メモリや、これらがアプリケーションのスループットに及ぼす影響を考慮する代わりに、RU という&1; つの単位を基にして考えることができます。

DocumentDB の各コレクションを予約する際は、1 秒あたりのスループットを表す RU を単位としてプロビジョニングされたスループットを使用できます。 アプリケーションがどのようなサイズでも、個々の要求のベンチマークを実行して RU 値を測定し、すべての要求の要求ユニットの合計に対処できるようコレクションをプロビジョニングすることができます。 アプリケーションのニーズの進化に合わせて、コレクションのスループットをスケールアップしたりスケールダウンしたりすることもできます。 要求ユニットの詳細やコレクションのニーズを判断する方法については、「[スループットのニーズの推定](documentdb-request-units.md#estimating-throughput-needs)」を参照して、[スループット計算ツール](https://www.documentdb.com/capacityplanner)をお試しください。

### <a name="is-documentdb-hipaa-compliant"></a>DocumentDB HIPAA は準拠していますか?
はい、DocumentDB は HIPAA に準拠しています。 HIPAA は、個別に識別できる医療情報の使用、開示、および保護するための要件を確立しています。 詳細については、[Microsoft セキュリティ センター](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA)を参照してください。

### <a name="what-are-the-storage-limits-of-documentdb"></a>DocumentDB のストレージ制限とは何ですか?
DocumentDB でコレクションが格納できる合計データ量には、制限はありません。

### <a name="what-are-the-throughput-limits-of-documentdb"></a>DocumentDB のスループット制限とは何ですか?
十分な数のパーティション キーにワークロードをほぼ均等に分散できる場合は、DocumentDB でコレクションがサポートできるスループットの合計量には、制限はありません。

### <a name="how-much-does-microsoft-azure-documentdb-cost"></a>Microsoft Azure DocumentDB の料金はいくらですか?
詳細については、「[DocumentDB の価格](https://azure.microsoft.com/pricing/details/documentdb/)」ページを参照してください。 DocumentDB の利用料金は、プロビジョニング済みコレクションの数、コレクションがオンラインであった時間数、および各コレクションのプロビジョニング済みスループットによって決まります。

### <a name="is-there-a-free-account-available"></a>無料アカウントを使用できますか?
Azure を初めて使用する場合は、30 日間使用できる [Azure 無料アカウント](https://azure.microsoft.com/free/)にサインアップできます。すべての Azure サービスを試すには 200 ドルかかります。 Visual Studio サブスクリプションをお持ちの場合は、[1 か月あたり&150; ドルの無料 Azure クレジット](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)ですべての Azure サービスを使用できます。  

また、[Azure DocumentDB Emulator](documentdb-nosql-local-emulator.md) を使用すると、Azure サブスクリプションを作成せずに、ローカルでアプリケーションの開発とテストを行うことができます。 DocumentDB Emulator でのアプリケーションの動作に満足できたら、クラウドでの Azure DocumentDB アカウントの使用に切り替えることができます。

### <a name="how-can-i-get-additional-help-with-documentdb"></a>DocumentDB に関するその他のヘルプはどのようにして得られますか?
ヘルプが必要な場合は、[Stack Overflow](http://stackoverflow.com/questions/tagged/azure-documentdb) を利用するか、[askdocdb@microsoft.com](mailto:askdocdb@microsoft.com) にメールを送信して、DocumentDB エンジニアリング チームとの 1 対 1 のチャットをスケジュールしてください。 DocumentDB の最新のニュースと機能については、 [Twitter](https://twitter.com/DocumentDB)をフォローしてください。

## <a name="set-up-microsoft-azure-documentdb"></a>Microsoft Azure DocumentDB のセットアップ
### <a name="how-do-i-sign-up-for-microsoft-azure-documentdb"></a>Microsoft Azure DocumentDB にはどのようにサインアップしますか?
Microsoft Azure DocumentDB は、[Azure Portal][azure-portal]で利用可能です。  最初に、Microsoft Azure サブスクリプションにサインアップする必要があります。  Microsoft Azure サブスクリプションにサインアップした後、DocumentDB アカウントを Azure サブスクリプションに追加できます。 DocumentDB アカウントの追加方法については、「 [DocumentDB データベース アカウントの作成](documentdb-create-account.md)」を参照してください。   

### <a name="what-is-a-master-key"></a>マスター キーとは何ですか?
マスター キーは、アカウントのすべてのリソースにアクセスするためのセキュリティ トークンです。 キーを保持する個人には、データベース アカウント内のすべてのリソースへの読み取り/書き込みアクセスが許可されます。 マスター キーを配布するときには十分な注意が必要となります。 プライマリ マスター キーとセカンダリ マスター キーは、[Azure Portal][azure-portal]の **[キー]** ブレードで入手できます。 リソース キーの詳細については、「 [アクセス キーを表示、コピー、および再生成する](documentdb-manage-account.md#keys)」を参照してください。

### <a name="how-do-i-create-a-database"></a>どのようにしてデータベースを作成しますか?
データベースは、[DocumentDB データベース コレクションとデータベースの作成](documentdb-create-collection.md)に関するページで説明されているように [Azure Portal]() を使用するか、いずれかの [DocumentDB SDK](documentdb-sdk-dotnet.md) を使用するか、[REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) を使用して作成できます。  

### <a name="what-is-a-collection"></a>コレクションとは何ですか?
コレクションには、JSON ドキュメントのほか、関連する JavaScript アプリケーション ロジックが格納されます。 コレクションは課金対象のエンティティであり、その[コスト](documentdb-performance-levels.md)は、使用しているスループットとストレージによって決まります。 コレクションは、1 つまたは複数のパーティション/サーバーにまたがって存在でき、拡張性があるので、扱うことのできるストレージのボリュームやスループットには実質的に制限がありません。

コレクションも DocumentDB の課金エンティティです。 各コレクションは、プロビジョニング済みスループットと使用されたストレージ領域に基づいて時間単位で課金されます。 詳細については、「 [DocumentDB の価格](https://azure.microsoft.com/pricing/details/documentdb/)」を参照してください。  

### <a name="how-do-i-set-up-users-and-permissions"></a>どのようにしてユーザーおよびアクセス許可を設定しますか?
[DocumentDB SDK](documentdb-sdk-dotnet.md) または [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) を使用して、ユーザーおよびアクセス許可を作成できます。   

## <a name="database-questions-about-developing-against-microsoft-azure-documentdb"></a>Microsoft Azure DocumentDB に対する開発についてのデータベース質問
### <a name="how-to-do-i-start-developing-against-documentdb"></a>どのようにして DocumentDB に対する開発を開始しますか?
[SDK](documentdb-sdk-dotnet.md) が利用可能です。  開発者は、[RESTful HTTP API](https://msdn.microsoft.com/library/azure/dn781481.aspx) を使用して、さまざまなプラットフォームおよび言語から DocumentDB リソースとやり取りすることもできます。

GitHub では、[.NET](documentdb-dotnet-samples.md)、[Java](https://github.com/Azure/azure-documentdb-java)、[Node.js](documentdb-nodejs-samples.md)、[Python](documentdb-python-samples.md) 用の DocumentDB SDK のサンプルを入手できます。

### <a name="does-documentdb-support-sql"></a>DocumentDB は SQL をサポートしますか?
DocumentDB SQL クエリ言語は、SQL でサポートされるクエリ機能の強化版サブセットです。 DocumentDB の SQL クエリ言語は、JavaScript ベースのユーザー定義関数 (UDF) により高度な階層型の関係演算子と機能拡張を提供します。 JSON 文法では、JSON ドキュメントをツリー ノードとしてラベルが付けられたツリーとしてモデル化することができます。これは、DocumentDB の自動的なインデックス作成手法と DocumentDB の SQL クエリ方言の両方で使用されます。  SQL 文法の使用方法の詳細については、[DocumentDB のクエリ][query]に関する記事をご覧ください。

### <a name="does-documentdb-support-sql-aggregation-functions"></a>DocumentDB は SQL の集計関数をサポートしますか?
DocumentDB は、SQL 文法の集計関数 `COUNT`、`MIN`、`MAX`、`AVG`、`SUM` をサポートしており、任意のスケールで遅延時間を集計できます。 詳細については、「[集計関数](documentdb-sql-query.md#Aggregates)」をご覧ください。

### <a name="what-are-the-data-types-supported-by-documentdb"></a>DocumentDB でサポートされるデータ型は何ですか?
DocumentDB でサポートされるプリミティブ データ型は JSON と同じです。 JSON には、文字列、数値 (IEEE754 倍精度)、ブール値 (true、false、Null) で構成されるシンプルな型システムがあります。 DocumentDB は、Point、Polygon、および GeoJSON として表現された LineString の空間型をネイティブでサポートします。 { } 演算子で入れ子になったオブジェクトを作成し、[ ] 演算子で配列を作成することにより、さらに複雑なデータ型 (DateTime、Guid、Int64、Geometry など) を JSON と DocumentDB の両方で表現できます。

### <a name="how-does-documentdb-provide-concurrency"></a>DocumentDB はどのようにして同時実行を提供しますか?
DocumentDB では、HTTP エンティティ タグ (または ETag) によるオプティミスティック同時実行制御 (OCC) をサポートしています。 すべての DocumentDB リソースに ETag があり、ドキュメントが更新されるたびにサーバーで ETag が設定されます。 すべての応答メッセージに ETag ヘッダーと現在の値が含まれます。 Etag を If-Match ヘッダーと共に使用すると、サーバーでリソースを更新するかどうかを判定できるようになります。 If-Match 値は、チェック対象の ETag 値です。 対象の ETag 値がサーバーの ETag 値と一致する場合に、リソースが更新されます。 ETag が現在の値ではない場合、サーバーは操作を拒否して "HTTP 412 Precondition failure" 応答コードを返します。 この場合、クライアントでは、リソースを再フェッチしてリソースの現在の ETag 値を取得する必要があります。 また、ETag を If-None-Match ヘッダーと共に使用すると、リソースの再フェッチが必要かどうかを判定することができます。

.NET でオプティミスティック同時実行制御を使用するには、 [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) クラスを使用します。 .NET サンプルについては、GitHub にある DocumentManagement サンプルの [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) を参照してください。

### <a name="how-do-i-perform-transactions-in-documentdb"></a>どのようにして DocumentDB のトランザクションを実行しますか?
DocumentDB では、JavaScript ストアド プロシージャおよびトリガーを介して、統合された言語のトランザクションがサポートされます。 スクリプト内のすべてのデータベース操作は、単一パーティション コレクションの場合はコレクションを対象として、スナップショット分離下で実行されます。コレクションがパーティション分割されている場合は、コレクション内の同じパーティション キー値を持つドキュメントを対象として実行されます。 ドキュメント バージョン (ETag) のスナップショットは、トランザクションの開始時に取得され、スクリプトが成功された場合のみコミットされます。 JavaScript がエラーをスローした場合、トランザクションはロールバックされます。 詳細については、「[DocumentDB のサーバー側プログラミング](documentdb-programming.md)」を参照してください。

### <a name="how-can-i-bulk-insert-documents-into-documentdb"></a>どのようにして DocumentDB にドキュメントを一括挿入しますか?
DocumentDB にドキュメントを一括挿入するには&3; つの方法があります。

* データ移行ツール。[DocumentDB へのデータのインポート](documentdb-import-data.md)に関するページを参照してください。
* Azure Portal の Document Explorer。[ドキュメント エクスプローラーを使用したドキュメントの一括追加](documentdb-view-json-document-explorer.md#bulk-add-documents)に関するページを参照してください。
* ストアド プロシージャ。「[DocumentDB のサーバー側プログラミング](documentdb-programming.md)」を参照してください。

### <a name="does-documentdb-support-resource-link-caching"></a>DocumentDB はリソース リンク キャッシュをサポートしますか?
はい、DocumentDB は RESTful サービスであるため、リソース リンクは不変であり、キャッシュできます。 DocumentDB クライアントは、ドキュメントやコレクションなどリソースの読み取りに対して "If-None-Match" ヘッダーを指定でき、サーバー バージョンが変更された場合のみローカル コピーを更新できます。

### <a name="is-a-local-instance-of-documentdb-available"></a>DocumentDB のローカル インスタンスは使用できますか?
はい。 [Azure DocumentDB Emulator](documentdb-nosql-local-emulator.md) には、DocumentDB サービスの高忠実度エミュレーションが用意されています。 JSON ドキュメントの作成とクエリ、コレクションのプロビジョニングとスケーリング、ストアド プロシージャとトリガーの実行のサポートなど、Azure DocumentDB と同じ機能がサポートされています。 DocumentDB Emulator を使用してアプリケーションの開発およびテストを行い、DocumentDB の接続エンドポイントの構成を&1; つ変更するだけで、世界規模で Azure にデプロイすることができます。

## <a name="database-questions-about-developing-against-api-for-mongodb"></a>MongoDB 用 API に対する開発についてのデータベース質問
### <a name="what-is-documentdbs-api-for-mongodb"></a>DocumentDB の MongoDB 用 API とは何ですか?
Microsoft Azure DocumentDB の MongoDB 用 API は、Apache MongoDB API とドライバーによってサポートされる既存のコミュニティを使用して、アプリケーションがネイティブ DocumentDB データベース エンジンと簡単かつ透過的に通信できるようにする互換性レイヤーです。 開発者は既存の MongoDB ツール チェーンおよびスキルを使用して、DocumentDB の独自の機能を利用して、DocumentDB を活用するアプリケーションを構築できるようになりました。この独自の機能には、自動インデックス作成、バックアップ メンテナンス、利用料金に基づくサービス レベル アグリーメント (SLA) などがあります。

### <a name="how-to-do-i-connect-to-my-api-for-mongodb-database"></a>MongoDB 用 API データベースへの接続方法を教えてください。
DocumentDB の MongoDB 用 API に接続するための最も簡単な方法は、[Azure ポータル](https://portal.azure.com)を使用することです。 自身のアカウントに移動します。 アカウントの*左側のナビゲーション*で、*[クイック スタート]* をクリックします。 *クイック スタート*は、コード スニペットを取得して、データベースに接続するための最善の方法です。 

DocumentDB では、厳密なセキュリティ要件や基準が要求されます。 DocumentDB アカウントでは、*SSL* による認証と安全な通信が要求されます。このため、必ず TLSv1.2 を使用してください。

詳しくは、[MongoDB 用 API データベースへの接続](documentdb-connect-mongodb-account.md)に関する記事をご覧ください。

### <a name="are-there-additional-error-codes-for-an-api-for-mongodb-database"></a>MongoDB 用 API にはその他のエラー コードはありますか?
MongoDB 用 API には、一般的な MongoDB エラー コードのほかに、独自の特定のエラー コードがあります。


| エラー               | コード  | 説明  | 解決策  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | 使用された要求ユニットの合計数が、コレクションのプロビジョニング済み要求ユニット レートを超えたため調整されました。 | Azure Portal でのコレクションのスループットのスケーリングか、再試行を検討してください。 |
| ExceededMemoryLimit | 16501 | マルチ テナント サービスとして、操作がクライアントのメモリ配分を超えました。 | より制限の厳しいクエリ条件により操作のスコープを減らすか、[Azure ポータル](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)からサポートに問い合わせます。 <br><br>*例:  &nbsp;&nbsp;&nbsp;&nbsp;db.getCollection('users').aggregate([<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$match: {name: "Andy"}}, <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$sort: {age: -1}}<br>&nbsp;&nbsp;&nbsp;&nbsp;])*) |

[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md

