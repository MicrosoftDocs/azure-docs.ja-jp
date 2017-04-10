---
title: "JSON データベースである DocumentDB の概要 | Microsoft Docs"
description: "NoSQL JSON データベースである Azure DocumentDB について説明します。 このドキュメント データベースは、ビッグ データ、エラスティックな拡張性、および高可用性用に構築されています。"
keywords: "json データベース, ドキュメント データベース"
services: documentdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 686cdd2b-704a-4488-921e-8eefb70d5c63
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/14/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 4f6ae0c3c40a10f75b46c6e44b0aa9e8ce440b4d
ms.lasthandoff: 03/15/2017


---
# <a name="introduction-to-documentdb-a-nosql-json-database"></a>DocumentDB の概要: NoSQL JSON Database
## <a name="what-is-documentdb"></a>DocumentDB とは?
DocumentDB は、完全管理された NoSQL データベース サービスです。高速で確実なパフォーマンス、高可用性、エラスティック スケーリング、グローバル分散、開発の容易さを視野に入れて構築されています。 スキーマフリーの NoSQL データベースとして、DocumentDB は、豊富で使いやすい SQL クエリ機能を提供します。JSON データに対するレイテンシは一貫して低く、読み取りの 99% は 10 ミリ秒未満で行われ、書き込みの 99% は 15 ミリ秒未満で行われます。 このようなユニークな利点があるため、DocumentDB はシームレスなスケーリングとグローバル レプリケーションを必要とする Web、モバイル、ゲーム、IoT、その他の多くのアプリケーションに最適です。

## <a name="how-can-i-learn-about-documentdb"></a>DocumentDB についての学習方法
DocumentDB を簡単に理解して動作を確認するには、次の&3; つの手順に従います。 

1. [DocumentDB について](https://azure.microsoft.com/documentation/videos/what-is-azure-documentdb/)説明する&2; 分間のビデオを視聴します。このビデオでは、DocumentDB を使用する利点が紹介されています。
2. [Azure での DocumentDB の作成に関する&3; 分間のビデオ](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/)を視聴します。このビデオでは、Azure Portal を使用して DocumentDB の使用を開始する方法が紹介されています。
3. [クエリのプレイ グラウンド](http://www.documentdb.com/sql/demo)にアクセスします。このページでは、DocumentDB で利用できる豊富なクエリ機能について理解するためのさまざまな手順を確認できます。 次に [サンドボックス] タブに移動して、独自のカスタム SQL クエリを実行し、DocumentDB を試してみます。

次に、この記事に戻ります。ここでは、より詳しく説明します。  

## <a name="what-capabilities-and-key-features-does-documentdb-offer"></a>DocumentDB では、どのような機能が提供されますか。
Azure DocumentDB が備えている主要な機能と利点は次のとおりです。

* **スループットとストレージのスケールを柔軟に調整:** アプリケーションのニーズに合わせて DocumentDB JSON データベースを簡単にスケールアップおよびスケールダウンできます。 データは SSD (Solid State Disk) に格納されるため、予測可能かつ低いレイテンシが期待できます。 DocumentDB は、JSON データを格納するための、コレクションと呼ばれるコンテナーをサポートします。コレクションは、ストレージのサイズとプロビジョニングするスループットを実質的に無制限に拡張することができます。 アプリケーションの成長に合わせて DocumentDB のスケールを臨機応変に拡張し、予測したとおりのパフォーマンスをシームレスに確保することができます。 
* **複数リージョンのレプリケーション**: DocumentDB は、DocumentDB アカウントに関連付けてあるすべてのリージョンにデータを透過的にレプリケートします。これにより、一貫性、可用性、パフォーマンスを所定のレベルで確保し、それらのトレードオフを実現しつつ、データへのグローバル アクセスが必要なアプリケーションを開発できます。 DocumentDB には、マルチホーミング API を使用した透過的な地域フェールオーバーのほか、スループットとストレージを世界規模で柔軟に拡張する機能が用意されています。 詳細については、「[DocumentDB を使用したデータのグローバル分散](documentdb-distribute-data-globally.md)」を参照してください。
* **馴染みのある SQL 構文を使用したアドホック クエリ:** 多種多様な JSON ドキュメントを DocumentDB に格納し、馴染みのある SQL 構文を使用して照会できます。 DocumentDB は、同時実行性の高い、ロックを用いないログ構造のインデックス作成技術を利用して、すべてのドキュメント コンテンツのインデックスを自動的に作成します。 そのため、スキーマのヒント、セカンダリ インデックス、ビューを指定せずに、豊富なリアルタイム クエリが可能となっています。 詳細については、「 [DocumentDB のクエリ](documentdb-sql-query.md)」を参照してください。 
* **データベース内で JavaScript を実行**: 標準の JavaScript を使用し、ストアド プロシージャ、トリガー、ユーザー定義関数 (UDF) としてアプリケーション ロジックを表現することができるため、 アプリケーション スキーマとデータベース スキーマ間のミスマッチに悩まされることなく、アプリケーション ロジックでデータを扱うことができます。 DocumentDB は、JavaScript アプリケーション ロジックを完全なトランザクションとしてデータベース エンジン内から直接実行できるようになっています。 JavaScript が深いレベルで統合されているため、INSERT、REPLACE、DELETE、SELECT の操作を分離されたトランザクションとして JavaScript プログラム内から実行することができます。 詳細については、「[DocumentDB のサーバー側プログラミング](documentdb-programming.md)」を参照してください。
* **一貫性レベルをチューニング可能:** 明確に定義された&4; つの一貫性レベルの中からいずれかのレベルを選択し、一貫性とパフォーマンス間のトレードオフを最適な形で行うことができます。 DocumentDB では、クエリと読み取り操作に関して、Strong、Bounded-Staleness、Session、Eventual の&4; 種類の一貫性レベルが利用できます。 きめ細かな一貫性レベルが明確に定義されていることによって、一貫性、可用性、待機時間の最適なトレードオフを検討することができます。 詳細については、「 [一貫性レベルを使用した DocumentDB の可用性とパフォーマンスの最大化](documentdb-consistency-levels.md)」を参照してください。
* **完全管理**: データベースやコンピューター リソースを管理する手間がかかりません。 Microsoft Azure サービスは完全に管理されているため、仮想マシンの管理、ソフトウェアのデプロイと構成、スケールの管理、複雑なデータ層のアップグレードを手作業で行う必要はありません。 すべてのデータベースは自動的にバックアップされ、局地的障害から保護されます。 DocumentDB アカウントは簡単に追加し、必要に応じて容量をプロビジョニングすることができます。データベースの運用と管理に煩わされることなく、アプリケーションに専念することが可能です。 
* **設計に込められたオープンな環境:** 既存のスキルやツールをそのまま活かすことができます。 DocumentDB に対するプログラミングは、シンプルで親しみやすく、新しいツールを導入する必要がないうえ、JSON や JavaScript のカスタム拡張機能への縛りもありません。 CRUD、クエリ、JavaScript 処理を含め、データベースのすべての機能には、単純な RESTful HTTP インターフェイスでアクセスすることができます。 DocumentDB は、既にあるフォーマット、言語、標準を積極的に採用すると共に、それを基盤として価値の高いデータベース機能を提供しています。
* **自動インデックス作成:** 既定では、DocumentDB がデータベース内のすべてのドキュメントについて自動的にインデックスを作成するため、スキーマや、セカンダリ インデックスの作成は不要です。 すべてにはインデックスを作成したくない場合もあります。 その場合は、 [JSON ファイルでパスを除外](documentdb-indexing-policies.md) することもできます。
* **MongoDB アプリとの互換性:** DocumentDB: MongoDB 用 API を使用すると、MongoDB 用に作成されたアプリのデータ ストアとして DocumentDB データベースを使用できます。 つまり、MongoDB データベースの既存のドライバーを使用することで、MongoDB 用に記述されたアプリケーションが DocumentDB と通信し、MongoDB データベースではなく DocumentDB データベースを使用できるようになりました。 多くの場合、接続文字列を変更するだけで、MongoDB から DocumentDB に使用を切り替えることができます。 詳細については、「[DocumentDB: MongoDB 用 API とは](documentdb-protocol-mongodb.md)」を参照してください。

## <a name="data-management"></a>DocumentDB はどのようにデータを管理しますか。
Azure DocumentDB では、明確に定義されたデータベース リソースによって JSON データが管理されます。 これらのリソースは、高可用性を確保するためにレプリケートされ、論理 URI によって一意にアドレス指定されます。 DocumentDB のすべてのリソースには、HTTP ベースのシンプルで RESTful なプログラミング モデルを適用することができます。 

DocumentDB データベース アカウントは、Azure DocumentDB にアクセスできる一意の名前空間です。 データベース アカウントを作成するには、事前に Azure サブスクリプションが必要です。このサブスクリプションで、多様な Azure サービスにアクセスできます。 

DocumentDB 内のリソースはいずれも、JSON ドキュメントとしてモデル化されて保存されます。 リソースはアイテム (メタデータを含んだ JSON ドキュメント) およびフィード (アイテムのコレクション) として管理されます。 一連のアイテムがそれぞれのフィード内に格納されます。

以下の図は、DocumentDB のリソース間の関係を示したものです。

![NoSQL JSON データベースである DocumentDB のリソース間の階層関係です][1] 

データベース アカウントは、一連のデータベースから成ります。それぞれのデータベースには、複数のコレクションが含まれており、それぞれのコレクションに、ストアド プロシージャ、トリガー、UDF のほか、ドキュメントおよび関連する添付ファイルが含まれています。 また、データベースにはユーザーが関連付けられ、それぞれのユーザーには、他のさまざまなコレクション、ストアド プロシージャ、トリガー、UDF、ドキュメント、添付ファイルにアクセスするための一連のアクセス許可が関連付けられます。 データベース、ユーザー、アクセス許可、コレクションが、既知のスキーマを持ったシステム定義のリソースであるのに対し、ドキュメント、ストアド プロシージャ、トリガー、UDF、添付ファイルは、ユーザーが自由に定義できる JSON コンテンツを格納します。  

## <a name="develop"></a> DocumentDB で、どのようにしてアプリを開発しますか。
Azure DocumentDB が公開するリソースには、HTTP/HTTPS 要求機能を持つ任意の言語から REST API を呼び出すことでアクセスできます。 さらに、DocumentDB にはいくつかの主要な言語のプログラミング ライブラリが用意されており、MongoDB API との互換性があります。 アドレスのキャッシュ、例外管理、自動再試行などに伴う細部の処理がクライアント ライブラリ側で行われるため、Azure DocumentDB の操作が多くの点で単純化されます。 ライブラリは、次の言語およびプラットフォーム用が現在提供されています。  

| ダウンロード | ドキュメント |
| --- | --- |
| [.NET SDK](http://go.microsoft.com/fwlink/?LinkID=402989) |[.NET ライブラリ](https://msdn.microsoft.com/library/azure/dn948556.aspx) |
| [Node.js SDK](http://go.microsoft.com/fwlink/?LinkID=402990) |[Node.js ライブラリ](http://azure.github.io/azure-documentdb-node/) |
| [Java SDK](http://go.microsoft.com/fwlink/?LinkID=402380) |[Java ライブラリ](http://azure.github.io/azure-documentdb-java/) |
| [JavaScript SDK](http://go.microsoft.com/fwlink/?LinkID=402991) |[JavaScript ライブラリ](http://azure.github.io/azure-documentdb-js/) |
| 該当なし |[サーバー側の JavaScript SDK](http://azure.github.io/azure-documentdb-js-server/) |
| [Python SDK](https://pypi.python.org/pypi/pydocumentdb) |[Python ライブラリ](http://azure.github.io/azure-documentdb-python/) |
| 該当なし | [MongoDB 用 API](documentdb-protocol-mongodb.md)

[Azure DocumentDB Emulator](documentdb-nosql-local-emulator.md) を使用すると、ローカルでのアプリケーションの開発とテストが、Azure サブスクリプションを作成したりコストをかけたりせずに実施できます。 DocumentDB Emulator でのアプリケーションの動作に満足できたら、クラウドでの Azure DocumentDB アカウントの使用に切り替えることができます。

DocumentDB には、作成、読み取り、更新、削除という基本的な操作以外にも、JSON ドキュメントを検索するための多彩な SQL クエリ インターフェイスが備わっているほか、JavaScript のアプリケーション ロジックをサーバー側でトランザクション実行する機能がサポートされています。 クエリとスクリプトの実行インターフェイスは、REST API に加え、あらゆるプラットフォーム ライブラリから利用できます。 

### <a name="sql-query"></a>SQL クエリ
Azure DocumentDB では、JavaScript の型システムにマッチした SQL 言語と、リレーショナル クエリや階層クエリ、空間クエリに対応した式とを使用してドキュメントを照会することができます。 DocumentDB のクエリ言語は、JSON ドキュメントを照会するための、シンプルでありながら強力なインターフェイスとなっています。 この言語は、ANSI SQL の文法のサブセットをサポートしたうえで、深いレベルで JavaScript のオブジェクト、配列、オブジェクト生成、関数呼び出しとの統合が図られています。 DocumentDB のクエリ モデルでは、スキーマやインデックスのヒントを開発者が明示的に指定する必要がありません。

カスタム アプリケーション ロジックへの対応は、ユーザー定義関数 (UDF) を DocumentDB に登録して SQL クエリの中で参照するという、文法の拡張によって実現できます。 これらの UDF は JavaScript プログラムで記述し、データベース内から実行します。 

.NET の開発者にとっては、 [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.aspx)に含まれている LINQ クエリ プロバイダーが提供される点も見逃せません。 

### <a name="transactions-and-javascript-execution"></a>トランザクションと JavaScript の実行
DocumentDB では、アプリケーション ロジックを JavaScript だけで、名前付きのプログラムとして記述できます。 これらのプログラムは、コレクションに登録され、特定のコレクション内のドキュメントに対してデータベース操作を発行できるようになっています。 JavaScript は、トリガー、ストアド プロシージャ、ユーザー定義関数のいずれかとして登録することができます。 トリガーとストアド プロシージャは、ドキュメントの作成、読み取り、更新、削除を実行できます。これに対し、ユーザー定義関数はクエリの一部として実行され、コレクションに対する書き込みアクセス権はありません。

DocumentDB における JavaScript は、Transact-SQL の後継として、リレーショナル データベース システムによって裏付けられた概念に沿って実行がモデル化されています。 すべての JavaScript ロジックは、スナップショット分離機能を使用し、現在参加している ACID トランザクション内で実行されます。 その実行中に JavaScript で例外がスローされた場合、トランザクション全体が中止されます。

## <a name="are-there-any-online-courses-on-documentdb"></a>DocumentDB にオンライン コースはありますか。

はい。Azure DocumentDB に [Microsoft Virtual Academy](https://mva.microsoft.com/en-US/training-courses/azure-documentdb-planetscale-nosql-16847) コースがあります。 

>[!VIDEO https://mva.microsoft.com/en-US/training-courses-embed/azure-documentdb-planetscale-nosql-16847]
>
>

## <a name="next-steps"></a>次のステップ
既に Azure アカウントをお持ちの場合は、 [DocumentDB データベース アカウントを作成する](documentdb-create-account.md)ことにより、[Azure Portal](https://portal.azure.com/#gallery/Microsoft.DocumentDB) で DocumentDB の使用を開始できます。

Azure アカウントをお持ちでない場合、 そのための方法は次のとおりです。

* [Azure 無料試用版](https://azure.microsoft.com/free/)にサインアップできます。これは 30 日間使用でき、すべての Azure サービスを試すには 200 ドルかかります。 
* MSDN サブスクリプションをお持ちの場合は、 [1 か月あたり&150; ドルの無料 Azure クレジット](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ですべての Azure サービスを使用できます。 
* [Azure DocumentDB Emulator](documentdb-nosql-local-emulator.md) をダウンロードして、ローカルでアプリケーションを開発できます。

詳細を確認する準備ができたら、 [ラーニング パス](https://azure.microsoft.com/documentation/learning-paths/documentdb/) のページにアクセスして、使用できるすべてのラーニング リソースを移動しながら確認します。 

[1]: ./media/documentdb-introduction/json-database-resources1.png


