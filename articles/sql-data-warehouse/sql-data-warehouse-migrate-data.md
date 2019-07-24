---
title: SQL Data Warehouse へのデータの移行 | Microsoft Docs
description: ソリューション開発のための Azure SQL Data Warehouse へのデータの移行に関するヒント
services: sql-data-warehouse
author: jrowlandjones
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: jrj
ms.reviewer: igorstan
ms.openlocfilehash: 6a2acf602252ee4319f9a5eccef53a25d8e2dd7f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58014268"
---
# <a name="migrate-your-data"></a>データの移行
さまざまなソースのデータを、さまざまなツールを使って SQL Data Warehouse に移動することができます。  この目的を果たすうえで、ADF コピー、SSIS、bcp はすべて使用できます。 ただし、データ量が増えると、データ移行プロセスを複数のステップに分割することを検討する必要が生じます。 これにより、パフォーマンスと復元性の両面で各ステップを最適化し、スムーズなデータ移行を実行できる可能性が高まります。

この記事では、まず、ADF コピー、SSIS、および bcp の単純な移行シナリオについて説明します。 次に、移行を最適化する方法についてもう少し詳細に検討していきます。

## <a name="azure-data-factory-adf-copy"></a>Azure Data Factory (ADF) コピー
[ADF コピー][ADF Copy]は [Azure Data Factory][Azure Data Factory] の一部として組み込まれています。 ADF コピーを使用して、ローカル ストレージに置かれているフラット ファイル、Azure BLOB ストレージに保持されているリモート フラット ファイル、または SQL Data Warehouse 内のディレクトリにデータをエクスポートできます。

データがフラット ファイルで始まる場合、SQL Data Warehouse へのロードを開始する前に、まず、Asure ストレージ BLOB にそのフラット ファイルを転送する必要があります。 データが Azure BLOB ストレージに転送されたら、[ADF コピー][ADF Copy]を使用するようにもう一度選択して、SQL Data Warehouse にデータをプッシュすることができます。

PolyBase には、データをロードするための非常に高いパフォーマンスのオプションも用意されています。 ただし、これは 1 つではなく、2 つのツールを使用するという意味です。 最高のパフォーマンスが必要であれば、PolyBase を使用してください。 1 つのツールのみを使用する場合は (および、データが大規模ではない場合は)、ADF を使用してください。

ADF を使用してデータをデータ ウェアハウスに読み込む方法については、[このチュートリアル](../data-factory/load-azure-sql-data-warehouse.md)を参照してください。

## <a name="integration-services"></a>Integration Services
Integration Services (SSIS) は、強力で柔軟な抽出、変換、ロード (ETL) ツールであり、複雑なワークフロー、データの変換、およびいくつかのデータ ロード オプションをサポートします。 SSIS を使用して単にデータを Azure に転送したり、より広範囲にわたる移行の一部として転送したりします。

> [!NOTE]
> SSIS は、ファイルにバイト オーダーをマークせずに UTF-8 にエクスポートできます。 これを構成するには、まず派生された列コンポーネントを使用して、65001 UTF-8 コード ページを使用するようにデータ フローで文字データを変換します。 列が変換されたら、ファイルのコード ページとして 65001 も選択されていることを確認し、フラット ファイルの宛先アダプターにデータを書き込みます。
> 
> 

SSIS は、SQL Server のデプロイメントに接続する場合と同様に、SQL Data Warehouse に接続します。 ただし、接続には ADO.NET 接続マネージャーを使用する必要があります。 また、"使用可能な場合は一括挿入を使用する" 設定を構成してスループットを最大化することを検討する必要もあります。 このプロパティについて詳しくは、「[ADO NET 変換先エディター][ADO.NET destination adapter]」をご覧ください

> [!NOTE]
> OLEDB を使用した Azure SQL Data Warehouse への接続はサポートされていません。
> 
> 

さらに、調整やネットワークの問題が原因で、パッケージが失敗する可能性は常にあります。 パッケージを設計するときは、失敗する前に完了していた作業をやり直さなくても済むように、失敗した時点でパッケージを再開できるように設計してください。

詳しくは、[SSIS のドキュメント][SSIS documentation]をご覧ください。

## <a name="bcp"></a>bcp
bcp は、フラット ファイル データのインポートとエクスポート用に設計されたコマンド ライン ユーティリティです。 データのエクスポート時にはいくつかの変換を実行できます。 単純な変換を実行するには、クエリを使用してデータを選択し、変換します。 エクスポートが完了したら、フラット ファイルをターゲットである SQL Data Warehouse のデータベースに直接ロードできます。

> [!NOTE]
> ソース システムのビューで、データ エクスポート中に使用される変換をカプセル化したほうがいい場合がよくあります。 これにより、ロジックが保持され、プロセスを反復できるようになります。
> 
> 

bcp の利点は次のとおりです。

* 簡単さ。 bcp コマンドは簡単にビルドおよび実行できます
* 再起動可能なロード プロセス。 エクスポートした後、ロードは何回でも実行できます

bcp の制限は次のとおりです。

* bcp は表形式のフラット ファイルのみで動作します。 xml や JSON などのファイルでは機能しません
* データ変換機能はエクスポート段階のみに限定されており、本質的に単純です
* インターネット経由でデータをロードする場合、bcp は堅牢な構成になりません。 ネットワークが不安定になると、ロードでエラーが発生する可能性があります。
* bcp はロードの前に対象のデータベースに存在していたスキーマに依存します

詳しくは、「[bcp を使用した SQL Data Warehouse へのデータのロード][Use bcp to load data into SQL Data Warehouse]」をご覧ください。

## <a name="optimizing-data-migration"></a>データの移行の最適化
SQLDW データ移行処理は、事実上、次の 3 つの別個のステップに分割できます。

1. ソース データのエクスポート
2. Azure へのデータの転送
3. 対象の SQLDW データベースへのロード

各ステップを個別に最適化して、各ステップでパフォーマンスを最大化する、堅牢で再開可能な回復力のある移行プロセスを作成できます。

## <a name="optimizing-data-load"></a>データ ロードの最適化
ここで振り返ってみると、最短でデータをロードする方法は、PolyBase を使用することであることがわかります。 PolyBase ロード プロセスの最適化では、先行するステップで前提条件が生じることを事前に理解しておいてください。 次に例を示します。

1. データ ファイルのエンコード
2. データ ファイルの形式
3. データ ファイルの場所

### <a name="encoding"></a>エンコード
PolyBase では、データ ファイルは UTF-8 または UTF-16FE 形式でエンコードされている必要があります。 



### <a name="format-of-data-files"></a>データ ファイルの形式
PolyBase では、固定行ターミネータとして \n または改行を使用する必要があります。 データ ファイルは、この標準に準拠する必要があります。 文字列または列のターミネータに関する制限はありません。

PolyBase で外部テーブルの一部として、ファイル内のすべての列を定義する必要があります。 エクスポートされたすべての列が必要であり、型が必須の標準に準拠していることを確認します。

サポートされるデータ型の詳細については、前述の「[スキーマの移行]」に関する記事を参照してください。

### <a name="location-of-data-files"></a>データ ファイルの場所
SQL Data Warehouse は、PolyBase を使用して Azure BLOB ストレージのみからデータをロードします。 このため、データは最初に BLOB ストレージに転送されている必要があります。

## <a name="optimizing-data-transfer"></a>データ転送の最適化
データ移行で最も時間を要する部分の 1 つは、Azure へのデータの転送です。 ネットワーク帯域幅が問題になり得るというだけでなく、ネットワークの信頼性によっても進捗が大幅に妨げられる可能性があります。 既定では、インターネットを介して Azure にデータが移行されるため、転送エラーが発生する可能性は十分にあります。 ただし、これらのエラーによって、データの全体または一部を再送信する必要が生じる場合があります。

幸いなことに、この処理の速度と回復性を向上させるいくつかのオプションがあります。

### <a name="expressrouteexpressroute"></a>[ExpressRoute][ExpressRoute]
[ExpressRoute][ExpressRoute] を使用して転送速度を上げることを検討できます。 [ExpressRoute][ExpressRoute] では Azure への確立されたプライベート接続が提供されるため、パブリック インターネット経由では接続されません。 これは、必須の手順ではありません。 ただし、オンプレミスまたは共用施設からデータを Azure にプッシュするときのスループットが改善されます。

[ExpressRoute][ExpressRoute] を使用する利点は次のとおりです。

1. 信頼性が向上する
2. ネットワーク速度が高まる
3. ネットワーク待ち時間が短縮される
4. ネットワーク セキュリティが高まる

[ExpressRoute][ExpressRoute] は移行だけではなく、さまざまな状況で役に立ちます。

ご興味がおありでしたら、 詳細や価格については、[ExpressRoute に関するドキュメント][ExpressRoute documentation]をご覧ください。

### <a name="azure-import-and-export-service"></a>Azure Import/Export サービス
Azure Import/Export サービスは、大規模なデータ (GB (ギガバイト) 単位) から巨大なデータ (TB (テラバイト) 単位) を Azure に転送するために設計されたデータ転送プロセスです。 これには、データをディスクに書き込む作業や、それらを Azure データ センターに送付する作業も含まれています。 ディスクの内容は、自動的に Azure Storage BLOB にロードされます。

インポートおよびエクスポート処理の概要は次のとおりです。

1. データを受け取る Azure BLOB ストレージ コンテナーを構成します
2. ローカル ストレージにデータをエクスポートします
3. Azure Import/Export ツールを使用して、データを 3.5 インチ SATA II/III ハード ディスク ドライブにコピーします。
4. Azure Import/Export ツールで生成されたジャーナル ファイルを指定して、Azure Import/Export サービスを使用し、インポート ジョブを作成します。
5. 指定された Azure データ センターにディスクを送付します
6. データが Azure BLOB ストレージ コンテナーに転送されます
7. PolyBase を使用して、SQLDW にデータをロードします

### <a name="azcopyazcopy-utility"></a>[AZCopy][AZCopy] ユーティリティ
[AZCopy][AZCopy] ユーティリティは、Azure Storage BLOB に転送されたデータを取得するための優れたツールです。 これは、小規模なデータ (MB 単位) から非常に大規模なデータ (GB 単位) の転送用に設計されています。 [AZCopy] は Azure にデータを転送するときに、適切な回復力のあるスループットが提供されるようにも設計されているため、データ転送に最適です。 一度転送したら、PolyBase を使用して SQL Data Warehouse にデータをロードできます。 プロセスの実行タスクを使用して、SSIS パッケージに AZCopy を組み込むこともできます。

AZCopy を使用するには、最初にダウンロードしてインストールする必要があります。 [製品バージョン][production version]と[プレビュー バージョン][preview version]を使用できます。

ファイル システムからファイルをアップロードするには、次のようなコマンドが必要です。

```
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt
```

大まかなプロセスは、次のようになります。

1. データを受け取る Azure Storage BLOB コンテナーを構成します
2. ローカル ストレージにデータをエクスポートします
3. Azure BLOB ストレージ コンテナーにデータを AZCopy します
4. PolyBase を使用して SQL Data Warehouse にデータをロードします

完全なドキュメントは次から入手できます。[AZCopy][AZCopy]。

## <a name="optimizing-data-export"></a>データ エクスポートの最適化
PolyBase によって生じる要件にエクスポートが準拠していることを確認するほかに、データのエクスポートを最適化してプロセスをさらに改善することもできます。



### <a name="data-compression"></a>データ圧縮
PolyBase は、gzip 圧縮データを読み取ることができます。 gzip ファイルにデータを圧縮できる場合は、ネットワーク経由でプッシュされるデータ量が最小限に抑えられます。

### <a name="multiple-files"></a>複数のファイル
容量の大きいテーブルを複数のファイルに分割することは、エクスポートの高速化に役立つだけでなく、転送を再始動したり、Azure BLOB ストレージに保存した後にデータ全体を管理したりするのにも役立ちます。 PolyBase が持つ多数の便利な機能の 1 つとして、フォルダー内のすべてのファイルを読み取り、それを 1 つのテーブルとして処理する機能があります。 このため、各テーブルのファイルを独自のフォルダーに分離することをお勧めします。

PolyBase では、「再帰的なフォルダー トラバーサル」と呼ばれる機能もサポートされています。 この機能を使用して、エクスポートされたデータの編成をさらに拡張し、データ管理を改善できます。

PolyBase を使用したデータ ロードについて詳しくは、「[PolyBase を使用したデータのロード][Use PolyBase to load data into SQL Data Warehouse]」をご覧ください。

## <a name="next-steps"></a>次の手順
移行について詳しくは、「[SQL Data Warehouse へのソリューションの移行][Migrate your solution to SQL Data Warehouse]」をご覧ください。
開発に関するその他のヒントについては、[開発の概要][development overview]のページをご覧ください。

<!--Image references-->

<!--Article references-->
[AZCopy]: ../storage/common/storage-use-azcopy.md
[ADF Copy]: ../data-factory/load-azure-sql-data-warehouse.md 
[ADF Copy examples]: ../data-factory/quickstart-create-data-factory-dot-net.md
[development overview]: sql-data-warehouse-overview-develop.md
[スキーマの移行]: sql-data-warehouse-migrate-schema.md
[Migrate your solution to SQL Data Warehouse]: sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[Use bcp to load data into SQL Data Warehouse]: /sql/tools/bcp-utility
[Use PolyBase to load data into SQL Data Warehouse]: load-data-wideworldimportersdw.md


<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory]: https://azure.microsoft.com/services/data-factory/
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[ExpressRoute documentation]: https://azure.microsoft.com/documentation/services/expressroute/

[production version]: https://aka.ms/downloadazcopy/
[preview version]: https://aka.ms/downloadazcopypr/
[ADO.NET destination adapter]: https://msdn.microsoft.com/library/bb934041.aspx
[SSIS documentation]: https://msdn.microsoft.com/library/ms141026.aspx
