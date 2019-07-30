---
title: データ読み込みのベスト プラクティス - Azure SQL Data Warehouse | Microsoft Docs
description: Azure SQL Data Warehouse へのデータの読み込みに関する推奨事項とパフォーマンスの最適化。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 05/31/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seoapril2019
ms.openlocfilehash: bb170b53946a014d4aa69ce628c2e4bef7459b93
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595580"
---
# <a name="best-practices-for-loading-data-into-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse へのデータ読み込みのベスト プラクティス

Azure SQL Data Warehouse へのデータの読み込みに関する推奨事項とパフォーマンスの最適化。

## <a name="preparing-data-in-azure-storage"></a>Azure Storage のデータの準備

待ち時間を最小限に抑えるには、ストレージ層とデータ ウェアハウスを併置します。

ORC ファイル形式でデータをエクスポートすると、大きなテキスト列がある場合に、Java のメモリ不足エラーが発生することがあります。 この制限を回避するには、列のサブセットのみをエクスポートします。

PolyBase には、1,000,000 バイトを超えるデータを含む行を読み込むことができません。 Azure BLOB ストレージまたは Azure Data Lake Store のテキスト ファイルにデータを保存する場合、データは 1,000,000 バイトよりも少なくする必要があります。 テーブル スキーマには関係なく、このバイト制限はあります。

すべてのファイル形式は、異なるパフォーマンス特性を持っています。 最速で読み込むには、圧縮されたテキスト区切りファイルを使用します。 UTF-8 と UTF-16 のパフォーマンスの違いはごくわずかです。

大きな圧縮ファイルは小さな圧縮ファイルに分割します。

## <a name="running-loads-with-enough-compute"></a>十分なコンピューティング リソースで読み込みを実行する

読み込み速度を最速にするには、一度に 1 つの読み込みジョブのみを実行します。 それが実行できない場合は、同時に実行する読み込み数を最小限に抑えます。 大規模な読み込みジョブを実行する場合は、読み込み前にデータ ウェアハウスをスケール アップすることを検討してください。

適切なコンピューティング リソースで読み込みを実行するには、読み込みを実行するように指定された読み込みユーザーを作成します。 特定のリソース クラスに各読み込みユーザーを割り当てます。 読み込みを実行するには、いずれかの読み込みユーザーとしてサインインし、読み込みを実行します。 読み込みは、ユーザーのリソース クラスで実行されます。  この方法は、現在のリソース クラスのニーズに合わせてユーザーのリソース クラスを変更しようとするより簡単です。

### <a name="example-of-creating-a-loading-user"></a>読み込みユーザーの作成の例

この例では、staticrc20 リソース クラスの読み込みユーザーを作成します。 まず、**マスターに接続**し、ログインを作成します。

```sql
   -- Connect to master
   CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
```

データ ウェアハウスに接続し、ユーザーを作成します。 次のコードは、mySampleDataWarehouse という名前のデータベースに接続していることを前提としています。 LoaderRC20 という名前のユーザーを作成し、そのユーザーにデータベースに対するアクセス許可の制御を与える方法を示します。 その後、ユーザーを staticrc20 データベース ロールのメンバーとして追加します。  

```sql
   -- Connect to the database
   CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
   GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
   EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
```

staticRC20 リソース クラスのリソースで読み込みを実行するには、LoaderRC20 としてサインインし、読み込みを実行します。

動的リソース クラスではなく、静的リソース クラスで読み込みを実行します。 静的リソース クラスを使用すると、[Data Warehouse ユニット](what-is-a-data-warehouse-unit-dwu-cdwu.md)に関係なく、必ず同じリソースが使用されます。 動的リソース クラスを使用すると、サービス レベルによってリソースが変わります。 動的クラスの場合、サービス レベルが低いと、おそらく読み込みユーザー用により大きなリソース クラスを使用する必要があります。

## <a name="allowing-multiple-users-to-load"></a>複数のユーザーが読み込みを実行できるようにする

多くの場合、複数のユーザーがデータ ウェアハウスにデータを読み込むことができるようにするニーズがあります。 [CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) を使用して読み込むには、データベースの CONTROL アクセス許可が必要です。  CONTROL アクセス許可は、すべてのスキーマに対する制御アクセス権を付与します。 読み込みを実行するすべてのユーザーに、すべてのスキーマに対する制御アクセス権を付与したくない場合があります。 アクセス許可を制限するには、DENY CONTROL ステートメントを使用します。

たとえば、部門 A に schema_A、部門 B に schema_B というデータベース スキーマがあるとします。データベース ユーザーの user_A と user_B を、部門 A と B のそれぞれで読み込みを行う PolyBase のユーザーにします。 両方のユーザーには CONTROL データベースのアクセス許可が付与されています。 スキーマ A と B の作成者はここで、次のように DENY を使用してスキーマをロックダウンします。

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

user_A と user_B は、他の部門のスキーマからロックアウトされるようになりました。

## <a name="loading-to-a-staging-table"></a>ステージング テーブルに読み込む

データをデータ ウェアハウス テーブルに移行する際に最速の読み込み速度を達成するには、データを 1 つのステージング テーブルに読み込みます。  ステージング テーブルをヒープとして定義し、分散オプションにラウンドロビンを使用します。 

通常、読み込みは 2 段階のプロセスです。まずステージング テーブルにデータを読み込み、運用データ ウェアハウス テーブルに挿入します。 運用テーブルでハッシュ分散を使用している場合、ハッシュ分散を使用してステージング テーブルを定義すると、読み込みと挿入の合計時間が速くなる可能性があります。 ステージング テーブルへの読み込みに時間はかかりますが、運用テーブルに行を挿入する 2 つ目の手順では、分散全体でデータの移動は発生しません。

## <a name="loading-to-a-columnstore-index"></a>列ストア インデックスを読み込む

列ストア インデックスは、高品質の行グループにデータを圧縮するために多くのメモリを必要とします。 最適な圧縮とインデックスの効率を得るには、列ストア インデックスで各行グループに最大 1,048,576 行を圧縮する必要があります。 メモリが不足気味である場合、列ストア インデックスは最大圧縮率を達成できないことがあります。 これが、クエリのパフォーマンスに影響します。 詳細については、[列ストア メモリの最適化](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)に関するページを参照してください。

- 最大圧縮率を達成するための十分なメモリを読み込みユーザーが確実に持つようにするには、中規模または大規模なリソース クラスのメンバーである読み込みユーザーを使用します。 
- 新しい行グループを完全に埋められるように、十分な行を読み込みます。 一括読み込みでは、1,048,576 行ごとに、1 つの完全な行グループとして列ストアに直接圧縮されます。 102,400 行未満の読み込みの場合、行はデルタストアに送信され、行は b ツリー インデックスに保持されます。 読み込みの行が少なすぎる場合は、すべての行がデルタストアに移動され、すぐには列ストア形式に圧縮されません。

## <a name="handling-loading-failures"></a>読み込みエラーを処理する

外部テーブルを使用した読み込みが、"*クエリは中止されました。外部ソースの読み取り中に最大拒否しきい値に達しました*" というエラーで失敗する場合があります。 このメッセージは、外部データにダーティなレコードが含まれていることを示します。 データ レコードは、列のデータの種類と数値が外部テーブルの列定義と一致しない場合、またはデータが指定された外部ファイルの形式に従っていない場合に「ダーティ」であるとみなされます。 

ダーティなレコードを修正するには、外部テーブルと外部ファイルの形式の定義が正しいこと、および外部データがこれらの定義に従っていることを確認します。 外部データ レコードのサブセットがダーティである場合は、CREATE EXTERNAL TABLE の中で拒否オプションを使用することで、クエリでこれらのレコードを拒否することを選択できます。

## <a name="inserting-data-into-a-production-table"></a>運用テーブルにデータを挿入する

[INSERT ステートメント](/sql/t-sql/statements/insert-transact-sql)で小さなテーブルに 1 回だけ読み込む場合や、検索を定期的に再読み込みする場合は、`INSERT INTO MyLookup VALUES (1, 'Type 1')` などのステートメントで十分です。  ただし、単一挿入は、一括読み込みを実行するほど効率的ではありません。 

一日に何千もの単一の挿入がある場合は、一括読み込みができるように、挿入をバッチ化します。  単一の挿入をファイルに追加する処理を開発し、定期的にファイルを読み込む別の処理を作成します。

## <a name="creating-statistics-after-the-load"></a>読み込み後に統計を作成する

クエリ パフォーマンスを向上させるには、最初に読み込んだ後またはデータに大きな変更が加えられた後に、すべてのテーブルのすべての列で統計を作成することが重要です。  手動で行うことも、[統計の自動作成](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics#automatic-creation-of-statistic)の有効化を通じて行うこともできます。

統計の詳細については、 [統計](sql-data-warehouse-tables-statistics.md)に関する記事を参照してください。 次の例では、Customer_Speed テーブルの 5 つの列に関する統計を手動で作成する方法を示しています。

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys"></a>ストレージ キーの切り替え

セキュリティのために BLOB ストレージへのアクセス キーを定期的に変更することをお勧めします。 BLOB ストレージ アカウントには 2 つのストレージ キーがあり、キーの切り替えに使用できます。

Azure Storage のアカウント キーを切り替えるには:

キーが変更されているストレージ アカウントごとに、[ALTER DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/alter-database-scoped-credential-transact-sql) を発行します。

例:

元のキーを作成します。

```sql
CREATE DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key1'
``` 

key 1 から key 2 にキーを交換します。

```sql
ALTER DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key2' 
```

基となる外部データ ソースに対するこの他の変更は不要です。

## <a name="next-steps"></a>次の手順

- PolyBase と、ELT (抽出、読み込み、および変換) 処理の設計の詳細については、[SQL Data Warehouse の ELT の設計](design-elt-data-loading.md)に関するページを参照してください。
- 読み込みのチュートリアルについては、「[PolyBase を使用して Azure Blob Storage から Azure SQL Data Warehouse にデータを読み込む](load-data-from-azure-blob-storage-using-polybase.md)」を参照してください。
- データの読み込みの監視については、「[DMV を利用してワークロードを監視する](sql-data-warehouse-manage-monitor.md)」を参照してください。



