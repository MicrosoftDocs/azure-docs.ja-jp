---
title: 'Azure Cosmos DB: DocumentDB API | Microsoft Docs'
description: "SQL と JavaScript を使用して、Azure Cosmos DB に大量の JSON ドキュメントを短い待ち時間で格納し、クエリする方法を説明します。"
keywords: "json データベース, ドキュメント データベース"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 686cdd2b-704a-4488-921e-8eefb70d5c63
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/22/2017
ms.author: mimig
ms.openlocfilehash: 2cb4bd74ea973c8ff980d208a8c5f63a98ec1edd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-cosmos-db-documentdb-api"></a>Azure Cosmos DB の概要: DocumentDB API

[Azure Cosmos DB](introduction.md) は、ミッション クリティカルなアプリケーション向けの、Microsoft のグローバル分散マルチモデル データベース サービスです。 Azure Cosmos DB は、[ターン キー グローバル分散](distribute-data-globally.md)、[スループットとストレージの世界規模でのエラスティック スケーリング](partition-data.md)、99 パーセンタイルの 1 桁ミリ秒の待機時間、[明確に定義された 5 種類の整合性レベル](consistency-levels.md)を提供し、高可用性を保証します。これらはすべて[業界最高レベルの SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) によってサポートされています。 Azure Cosmos DB は、[データのインデックスを自動的に作成](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)します。スキーマとインデックスの管理に対処する必要はありません。 Azure Cosmos DB はマルチモデルであり、ドキュメント、キーと値、グラフ、列指向の各データ モデルをサポートします。 

![Azure DocumentDB API](./media/documentdb-introduction/cosmosdb-documentdb.png) 

DocumentDB API を使用すると、Azure Cosmos DB で豊富で使い慣れた [SQL クエリ機能](documentdb-sql-query.md)を利用できるようになるほか、スキーマレスの JSON データに対する待機時間が一貫して低くなります。 この記事では、Azure Cosmos DB の DocumentDB API の概要を説明します。また、Azure Cosmos DB を使用して大量の JSON データを格納し、数ミリ秒の待ち時間でデータを照会する方法、スキーマを簡単に進化させる方法についても説明します。 

## <a name="what-capabilities-and-key-features-does-azure-cosmos-db-offer"></a>Azure Cosmos DB の主な機能
DocumentDB API を使った場合の Azure Cosmos DB の主な機能とメリットは次のとおりです。

* **スループットとストレージのスケールを柔軟に調整:** アプリケーションのニーズに合わせて JSON データベースを簡単にスケールアップおよびスケールダウンできます。 データは SSD (Solid State Disk) に格納されるため、予測可能かつ低いレイテンシが期待できます。 Azure Cosmos DB は、JSON データを格納するための、コレクションと呼ばれるコンテナーをサポートします。コレクションは、ストレージのサイズとプロビジョニングするスループットを実質的に無制限に拡張することができます。 アプリケーションの成長に合わせて Azure Cosmos DB のスケールを臨機応変に拡張し、予測したとおりのパフォーマンスをシームレスに確保することができます。 


* **複数リージョンのレプリケーション**: Azure Cosmos DB は、Azure Cosmos DB アカウントに関連付けられているすべてのリージョンにデータを透過的にレプリケートします。これにより、整合性、可用性、パフォーマンスを所定のレベルで確保して、これらのトレードオフを実現しながら、データへのグローバル アクセスを必要とするアプリケーションを開発できます。 Azure Cosmos DB は、マルチホーミング API を使用した透過的なリージョン内フェールオーバーを提供します。また、スループットとストレージを世界規模で弾力的にスケールすることもできます。 詳細については、[Azure Cosmos DB を使用してデータをグローバルに分散させる方法](distribute-data-globally.md)に関するページを参照してください。

* **馴染みのある SQL 構文を使用したアドホック クエリ:** 多種多様な JSON ドキュメントを格納し、馴染みのある SQL 構文を使用して照会できます。 Azure Cosmos DB では、同時実行性の高い、ロックのないログ構造のインデックス作成技術を利用して、すべてのドキュメントのコンテンツのインデックスを自動的に作成します。 そのため、スキーマのヒント、セカンダリ インデックス、ビューを指定せずに、豊富なリアルタイム クエリが可能となっています。 詳細については、[Azure Cosmos DB に対するクエリ](documentdb-sql-query.md)に関するページを参照してください。 
* **データベース内で JavaScript を実行**: 標準の JavaScript を使用し、ストアド プロシージャ、トリガー、ユーザー定義関数 (UDF) としてアプリケーション ロジックを表現することができるため、 アプリケーション スキーマとデータベース スキーマ間のミスマッチに悩まされることなく、アプリケーション ロジックでデータを扱うことができます。 DocumentDB API は、JavaScript アプリケーション ロジックを完全なトランザクションとしてデータベース エンジン内から直接実行できるようになっています。 JavaScript が深いレベルで統合されているため、INSERT、REPLACE、DELETE、SELECT の操作を分離されたトランザクションとして JavaScript プログラム内から実行することができます。 詳細については、「[DocumentDB のサーバー側プログラミング](programming.md)」を参照してください。

* **調整可能な整合性レベル:** 整合性とパフォーマンスの最適なトレードオフを実現するために、明確に定義された 5 つの整合性レベルの中から選択できます。 Azure Cosmos DB では、クエリと読み取り操作に関して、厳密、有界整合性制約、セッション、一貫性のあるプレフィックス、結果の 5 種類の整合性レベルを提供します。 きめ細かな一貫性レベルが明確に定義されていることによって、一貫性、可用性、待機時間の最適なトレードオフを検討することができます。 詳細については、[整合性レベルを使用して可用性とパフォーマンスを最大化する方法](consistency-levels.md)に関するページを参照してください。

* **完全管理**: データベースやコンピューター リソースを管理する手間がかかりません。 Microsoft Azure サービスは完全に管理されているため、仮想マシンの管理、ソフトウェアのデプロイと構成、スケールの管理、複雑なデータ層のアップグレードを手作業で行う必要はありません。 すべてのデータベースは自動的にバックアップされ、局地的障害から保護されます。 Azure Cosmos DB アカウントを簡単に追加し、必要に応じて容量をプロビジョニングできるので、データベースの運用と管理ではなく、アプリケーションに注力できます。 

* **設計に込められたオープンな環境:** 既存のスキルやツールをそのまま活かすことができます。 DocumentDB API に対するプログラミングは、シンプルで親しみやすく、新しいツールを導入する必要がないうえ、JSON や JavaScript のカスタム拡張機能への縛りもありません。 CRUD、クエリ、JavaScript 処理を含め、データベースのすべての機能には、単純な RESTful HTTP インターフェイスでアクセスすることができます。 DocumentDB API は、既にあるフォーマット、言語、標準を積極的に採用すると共に、それを基盤として価値の高いデータベース機能を提供しています。

* **自動インデックス作成:** 既定では、Azure Cosmos DB がデータベース内のすべてのドキュメントについて自動的にインデックスを作成するため、スキーマや、セカンダリ インデックスの作成は不要です。 すべてにはインデックスを作成したくない場合もあります。 その場合は、 [JSON ファイルでパスを除外](indexing-policies.md) することもできます。

* **Change Feed サポート:** Change Feed からは、Azure Cosmos DB コレクション内の一連のドキュメントをその更新日時順に並べ替えて取得することができます。 このフィードを使ってデータに対する変更をリッスンし、更新時にデータのレプリケート、API 呼び出しのトリガー、ストリーミング処理などを行うことができます。 Change Feed は自動的に有効になります。また使い方は簡単です。Change Feed について詳しくは、[こちら](https://docs.microsoft.com/azure/cosmos-db/change-feed)をご覧ください。 

## <a name="data-management"></a>DocumentDB API を使ってデータを管理する方法
DocumentDB API は、明確に定義されたデータベース リソースを通じて JSON データを管理するのに役立ちます。 これらのリソースは、高可用性を確保するためにレプリケートされ、論理 URI によって一意にアドレス指定されます。 DocumentDB API のすべてのリソースには、HTTP ベースのシンプルで RESTful なプログラミング モデルを適用することができます。 


Azure Cosmos DB データベース アカウントは、Azure Cosmos DB にアクセスできる一意の名前空間です。 データベース アカウントを作成するには、事前に Azure サブスクリプションが必要です。このサブスクリプションで、多様な Azure サービスにアクセスできます。 

Azure Cosmos DB 内のリソースはいずれも、JSON ドキュメントとしてモデル化されて保存されます。 リソースはアイテム (メタデータを含んだ JSON ドキュメント) およびフィード (アイテムのコレクション) として管理されます。 一連のアイテムがそれぞれのフィード内に格納されます。

以下の図は、Azure Cosmos DB のリソース間の関係を示したものです。

![Azure Cosmos DB のリソース間の階層関係][1] 

データベース アカウントは、一連のデータベースから成ります。それぞれのデータベースには、複数のコレクションが含まれており、それぞれのコレクションに、ストアド プロシージャ、トリガー、UDF のほか、ドキュメントおよび関連する添付ファイルが含まれています。 また、データベースにはユーザーが関連付けられ、それぞれのユーザーには、他のさまざまなコレクション、ストアド プロシージャ、トリガー、UDF、ドキュメント、添付ファイルにアクセスするための一連のアクセス許可が関連付けられます。 データベース、ユーザー、アクセス許可、コレクションが、既知のスキーマを持ったシステム定義のリソースであるのに対し、ドキュメント、ストアド プロシージャ、トリガー、UDF、添付ファイルは、ユーザーが自由に定義できる JSON コンテンツを格納します。  

> [!NOTE]
> DocumentDB API は、過去に Azure DocumentDB サービスの一部として提供されていたものです。このため、Azure Resource Management REST API のほか、Azure DocumentDB または Azure Cosmos DB のリソース名を使用しているツールを使って作成したアカウントを引き続きプロビジョニング、監視、および管理できます。 Azure DocumentDB API について言及するときには、この 2 つの名前を同じ意味で使用しています。 

## <a name="develop"></a>DocumentDB API でアプリを開発する方法

Azure Cosmos DB が公開するリソースには、HTTP/HTTPS 要求機能を持つ任意の言語から REST API を呼び出すことでアクセスできます。 さらに、DocumentDB API にはいくつかの主要な言語のプログラミング ライブラリも用意されています。 アドレスのキャッシュ、例外管理、自動再試行のような細かい処理がクライアント ライブラリ側で行われるため、API の操作が多くの点で単純化されます。 ライブラリは、次の言語およびプラットフォーム用が現在提供されています。  

| ダウンロード | ドキュメント |
| --- | --- |
| [.NET SDK](http://go.microsoft.com/fwlink/?LinkID=402989) |[.NET ライブラリ](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet) |
| [Node.js SDK](http://go.microsoft.com/fwlink/?LinkID=402990) |[Node.js ライブラリ](http://azure.github.io/azure-documentdb-node/) |
| [Java SDK](http://go.microsoft.com/fwlink/?LinkID=402380) |[Java ライブラリ](/java/api/com.microsoft.azure.documentdb) |
| [JavaScript SDK](http://go.microsoft.com/fwlink/?LinkID=402991) |[JavaScript ライブラリ](http://azure.github.io/azure-documentdb-js/) |
| 該当なし |[サーバー側の JavaScript SDK](http://azure.github.io/azure-documentdb-js-server/) |
| [Python SDK](https://pypi.python.org/pypi/pydocumentdb) |[Python ライブラリ](http://azure.github.io/azure-documentdb-python/) |
| 該当なし | [MongoDB 用 API](mongodb-introduction.md)


[Azure Cosmos DB Emulator](local-emulator.md) を使用すると、DocumentDB API を使ったローカルでのアプリケーションの開発とテストが、Azure サブスクリプションを作成したりコストをかけたりせずに実施できます。 エミュレーターでのアプリケーションの動作に満足できたら、クラウドでの Azure Cosmos DB アカウントの使用に切り替えることができます。

DocumentDB API には、作成、読み取り、更新、削除という基本的な操作以外にも、JSON ドキュメントを検索するための多彩な SQL クエリ インターフェイスが備わっているほか、JavaScript のアプリケーション ロジックをサーバー側でトランザクション実行する機能がサポートされています。 クエリとスクリプトの実行インターフェイスは、REST API に加え、あらゆるプラットフォーム ライブラリから利用できます。 

### <a name="sql-query"></a>SQL クエリ
DocumentDB API では、JavaScript の型システムにマッチした SQL 言語と、リレーショナル クエリや階層クエリ、空間クエリに対応した式とを使用してドキュメントを照会することができます。 DocumentDB のクエリ言語は、JSON ドキュメントを照会するための、シンプルでありながら強力なインターフェイスとなっています。 この言語は、ANSI SQL の文法のサブセットをサポートしたうえで、深いレベルで JavaScript のオブジェクト、配列、オブジェクト生成、関数呼び出しとの統合が図られています。 DocumentDB API のクエリ モデルでは、スキーマやインデックスのヒントを開発者が明示的に指定する必要がありません。

カスタム アプリケーション ロジックへの対応は、ユーザー定義関数 (UDF) を DocumentDB API に登録して SQL クエリの中で参照するという、文法の拡張によって実現できます。 これらの UDF は JavaScript プログラムで記述し、データベース内から実行します。 

.NET の開発者にとっては、DocumentDB API [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.aspx) により LINQ クエリ プロバイダーが提供される点も見逃せません。 

### <a name="transactions-and-javascript-execution"></a>トランザクションと JavaScript の実行
DocumentDB API では、アプリケーション ロジックを JavaScript だけで、名前付きのプログラムとして記述できます。 これらのプログラムは、コレクションに登録され、特定のコレクション内のドキュメントに対してデータベース操作を発行できるようになっています。 JavaScript は、トリガー、ストアド プロシージャ、ユーザー定義関数のいずれかとして登録することができます。 トリガーとストアド プロシージャは、ドキュメントの作成、読み取り、更新、削除を実行できます。これに対し、ユーザー定義関数はクエリの一部として実行され、コレクションに対する書き込みアクセス権はありません。

Cosmos DB における JavaScript は、Transact-SQL の後継として、リレーショナル データベース システムによって裏付けられた概念に沿って実行がモデル化されています。 すべての JavaScript ロジックは、スナップショット分離機能を使用し、現在参加している ACID トランザクション内で実行されます。 その実行中に JavaScript で例外がスローされた場合、トランザクション全体が中止されます。

## <a name="are-there-any-online-courses-on-azure-cosmos-db"></a>Azure Cosmos DB にオンライン コースはありますか。

はい。Azure DocumentDB に [Microsoft Virtual Academy](https://mva.microsoft.com/en-US/training-courses/azure-documentdb-planetscale-nosql-16847) コースがあります。 

>[!VIDEO https://mva.microsoft.com/en-US/training-courses-embed/azure-documentdb-planetscale-nosql-16847]
>
>

## <a name="next-steps"></a>次のステップ
既に Azure アカウントをお持ちの場合は、 [クイック スタート](../cosmos-db/create-documentdb-dotnet.md)に従って Azure Cosmos DB の使用を開始できます。このクイック スタートでは、アカウントを作成して Cosmos DB を実際に使用する方法を説明しています。

[1]: ./media/documentdb-introduction/json-database-resources1.png

