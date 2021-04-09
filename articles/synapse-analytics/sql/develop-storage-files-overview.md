---
title: サーバーレス SQL プールでストレージ上のファイルにアクセスする
description: Azure Synapse Analytics のサーバーレス SQL プールを使用してストレージ ファイルにクエリを実行する方法について説明します。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/19/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 679df881e928825496a03b2fef7c9374d61593f7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199981"
---
# <a name="access-external-storage-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics のサーバーレス SQL プールを使用して外部ストレージにアクセスする

この記事では、ユーザーがサーバーレス SQL プールで、Azure Storage に格納されているファイルからデータを読み取る方法について説明します。 ユーザーは、ストレージにアクセスするための次のオプションを使用できます。

- [OPENROWSET](develop-openrowset.md) 関数。Azure Storage 内のファイルに対するアドホック クエリを有効にします。
- [外部テーブル](develop-tables-external-tables.md)。外部ファイル セット上に構築された定義済みのデータ構造です。

ユーザーは、Azure AD パススルー認証 (Azure AD プリンシパルの既定値) や SAS 認証 (SQL プリンシパルの既定値) などの[さまざまな認証方法](develop-storage-files-storage-access-control.md)を使用できます。

## <a name="query-files-using-openrowset"></a>OPENROWSET を使用してファイルに対してクエリを実行する

OPENROWSET を使用すると、ユーザーはストレージにアクセスできる場合に、Azure Storage 上の外部ファイルに対してクエリを実行できます。 サーバーレス SQL プールに接続されているユーザーは、次のクエリを使用して、Azure Storage 上のファイルの内容を読み取る必要があります。

```sql
SELECT * FROM
 OPENROWSET(BULK 'https://<storage_account>.dfs.core.windows.net/<container>/<path>/*.parquet', format= 'parquet') as rows
```

ユーザーは、次のアクセス ルールを使用してストレージにアクセスできます。

- Azure AD ユーザー - `OPENROWSET` は、呼び出し元の Azure AD ID を使用して、Azure Storage にアクセスするか、匿名アクセスでストレージにアクセスします。
- SQL ユーザー - `OPENROWSET` は、匿名アクセスでストレージにアクセスするか、ワークスペースのマネージド ID または SAS トークンを使用して偽装することができます。

### <a name="impersonation"></a>[偽装](#tab/impersonation)

SQL プリンシパルでは、OPENROWSET を使用して、ワークスペースのマネージド ID または SAS トークンで保護されたファイルに対して、直接クエリを実行することもできます。 SQL ユーザーがこの関数を実行する場合、`ALTER ANY CREDENTIAL` 権限を持つパワー ユーザーは、(ストレージ名とコンテナーを使用して) 関数内の URL に一致するサーバースコープの資格情報を作成し、この資格情報に対する REFERENCES 権限を OPENROWSET 関数の呼び出し元に付与する必要があります。

```sql
EXECUTE AS somepoweruser

CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sas token';

GRANT REFERENCES ON CREDENTIAL::[https://<storage_account>.dfs.core.windows.net/<container>] TO sqluser
```

URL に一致するサーバーレベルの資格情報がない場合、または SQL ユーザーがこの資格情報に対する参照権限を持っていない場合は、エラーが返されます。 SQL プリンシパルでは、何かの Azure AD ID を使用して偽装することはできません。

### <a name="direct-access"></a>[直接アクセス](#tab/direct-access)

特別な設定を行わなくても、Azure AD ユーザーはその ID を使用してファイルにアクセスすることができます。
匿名アクセスを許可する Azure ストレージには、すべてのユーザーがアクセスできます (特別な設定は必要ありません)。

---

> [!NOTE]
> このバージョンの OPENROWSET は、既定の認証を使用して迅速かつ簡単にデータを探索できるように設計されています。 偽装またはマネージド ID を活用するには、次のセクションで説明されているように OPENROWSET を DATASOURCE と共に使用します。

## <a name="query-data-sources-using-openrowset"></a>OPENROWSET を使用してデータ ソースのクエリを実行する

OPENROWSET を使用すると、ユーザーは外部データ ソースに配置されたファイルに対してクエリを実行できます。

```sql
SELECT * FROM
 OPENROWSET(BULK 'file/path/*.parquet',
 DATASOURCE = MyAzureInvoices,
 FORMAT= 'parquet') as rows
```

このクエリを実行するユーザーは、ファイルにアクセスできる必要があります。 ユーザーがその [Azure AD ID](develop-storage-files-storage-access-control.md?tabs=user-identity) または[匿名アクセス](develop-storage-files-storage-access-control.md?tabs=public-access)を使用して直接ファイルにアクセスすることができない場合、[ワークスペースのマネージド ID](develop-storage-files-storage-access-control.md?tabs=managed-identity) または [SAS トークン](develop-storage-files-storage-access-control.md?tabs=shared-access-signature)を使用してユーザーを偽装する必要があります。

### <a name="impersonation"></a>[偽装](#tab/impersonation)

`DATABASE SCOPED CREDENTIAL` では、参照されるデータ ソース上のファイルへのアクセス方法 (現在は SAS およびマネージド ID) を指定します。 `CONTROL DATABASE` 権限を持つパワー ユーザーは、ストレージにアクセスするために使用される `DATABASE SCOPED CREDENTIAL`、および使用する必要がある資格情報とデータ ソースの URL を指定する `EXTERNAL DATA SOURCE` を作成する必要があります。

```sql
EXECUTE AS somepoweruser;

-- Create MASTER KEY if it doesn't exists in database
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'some very strong password';

CREATE DATABASE SCOPED CREDENTIAL AccessAzureInvoices
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&amp;sp=rwac&amp;se=2017-02-01T00:55:34Z&amp;st=201********' ;

CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>/' ,
 CREDENTIAL = AccessAzureInvoices) ;
```

呼び出し元には、OPENROWSET 関数を実行するための次のいずれかの権限が必要です。

- OPENROWSET を実行するための次のいずれかの権限:
  - `ADMINISTER BULK OPERATIONS` を使用すると、ログインで OPENROWSET 関数を実行できます。
  - `ADMINISTER DATABASE BULK OPERATIONS` を使用すると、データベース スコープ ユーザーは OPENROWSET 関数を実行できます。
- `EXTERNAL DATA SOURCE` で参照される資格情報に対する `REFERENCES DATABASE SCOPED CREDENTIAL`

### <a name="direct-access"></a>[直接アクセス](#tab/direct-access)

ユーザーは、パブリック アクセス ストレージを参照する CREDENTIAL なしの EXTERNAL DATA SOURCE を作成するか、Azure AD パススルー認証を使用することができます。

```sql
CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>') ;
```
---
## <a name="external-table"></a>EXTERNAL TABLE

テーブルを読み取る権限を持つユーザーは、Azure Storage のフォルダーとファイルのセット上に作成された EXTERNAL TABLE を使用して外部ファイルにアクセスできます。

[外部テーブルを作成するための権限](/sql/t-sql/statements/create-external-table-transact-sql?preserve-view=true&view=sql-server-ver15#permissions) (CREATE TABLE、ALTER ANY CREDENTIAL、REFERENCES DATABASE SCOPED CREDENTIAL など) を持つユーザーは、次のスクリプトを使用して、Azure Storage データソース上にテーブルを作成できます。

```sql
CREATE EXTERNAL TABLE [dbo].[DimProductexternal]
( ProductKey int, ProductLabel nvarchar, ProductName nvarchar )
WITH
(
LOCATION='/DimProduct/year=*/month=*' ,
DATA_SOURCE = AzureDataLakeStore ,
FILE_FORMAT = TextFileFormat
) ;
```

このテーブルからデータを読み取るユーザーは、ファイルにアクセスできる必要があります。 ユーザーがその [Azure AD ID](develop-storage-files-storage-access-control.md?tabs=user-identity) または[匿名アクセス](develop-storage-files-storage-access-control.md?tabs=public-access)を使用して直接ファイルにアクセスすることができない場合、[ワークスペースのマネージド ID](develop-storage-files-storage-access-control.md?tabs=managed-identity) または [SAS トークン](develop-storage-files-storage-access-control.md?tabs=shared-access-signature)を使用してユーザーを偽装する必要があります。

### <a name="impersonation"></a>[偽装](#tab/impersonation)

DATABASE SCOPED CREDENTIAL では、参照されるデータ ソース上のファイルへのアクセス方法を指定します。 CONTROL DATABASE 権限を持つユーザーは、ストレージにアクセスするために使用される DATABASE SCOPED CREDENTIAL、および使用する必要がある資格情報とデータ ソースの URL を指定する EXTERNAL DATA SOURCE を作成する必要があります。

```sql
EXECUTE AS somepoweruser;

-- Create MASTER KEY if it doesn't exists in database
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'some very strong password';

CREATE DATABASE SCOPED CREDENTIAL cred
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&sp=rwac&se=2017-02-01T00:55:34Z&st=201********' ;

CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>' ,
 CREDENTIAL = cred
 ) ;
```

### <a name="direct-access"></a>[直接アクセス](#tab/direct-access)

ユーザーは、パブリック アクセス ストレージを参照する CREDENTIAL なしの EXTERNAL DATA SOURCE を作成するか、Azure AD パススルー認証を使用することができます。

```sql
CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>') ;
```
---

### <a name="read-external-files-with-external-table"></a>EXTERNAL TABLE を使用して外部ファイルを読み取る

EXTERNAL TABLE では、標準的な SQL SELECT ステートメントを使用して、データ ソースを介して参照されるファイルからデータを読み取ることができます。

```sql
SELECT *
FROM dbo.DimProductsExternal
```

呼び出し元には、データを読み取るための次の権限が必要です。
- 外部テーブルに対する `SELECT` 権限
- `DATA SOURCE` に `CREDENTIAL` がある場合は、`REFERENCES DATABASE SCOPED CREDENTIAL` 権限

## <a name="permissions"></a>アクセス許可

次の表には、上に一覧表示されている操作に必要な権限が一覧表示されています。

| クエリ | 必要なアクセス許可|
| --- | --- |
| OPENROWSET(BULK) (データソースを指定しない) | `ADMINISTER BULK OPERATIONS`、`ADMINISTER DATABASE BULK OPERATIONS`、または SQL ログインには、SAS で保護されたストレージに対する REFERENCES CREDENTIAL::\<URL> が必要です |
| OPENROWSET (BULK) (資格情報なしのデータソースを指定) | `ADMINISTER BULK OPERATIONS` または `ADMINISTER DATABASE BULK OPERATIONS` |
| OPENROWSET (BULK) (資格情報ありのデータソースを指定) | `REFERENCES DATABASE SCOPED CREDENTIAL` と、`ADMINISTER BULK OPERATIONS` または `ADMINISTER DATABASE BULK OPERATIONS` のいずれか |
| CREATE EXTERNAL DATA SOURCE | `ALTER ANY EXTERNAL DATA SOURCE` および `REFERENCES DATABASE SCOPED CREDENTIAL` |
| CREATE EXTERNAL TABLE | `CREATE TABLE`、`ALTER ANY SCHEMA`、`ALTER ANY EXTERNAL FILE FORMAT`、および `ALTER ANY EXTERNAL DATA SOURCE` |
| SELECT FROM EXTERNAL TABLE | `SELECT TABLE` および `REFERENCES DATABASE SCOPED CREDENTIAL` |
| CETAS | テーブルを作成する場合 - `CREATE TABLE`、`ALTER ANY SCHEMA`、`ALTER ANY DATA SOURCE`、および `ALTER ANY EXTERNAL FILE FORMAT`。 データを読み取る場合: クエリのテーブル、ビューまたは関数ごとの `ADMINISTER BULK OPERATIONS`、`REFERENCES CREDENTIAL` または `SELECT TABLE` + ストレージに対する読み取りと書き込み権限 |

## <a name="next-steps"></a>次のステップ

これで、操作方法に関する次の記事に進む準備ができました。

- [ストレージ上のデータに対してクエリを実行する](query-data-storage.md)

- [CSV ファイルに対してクエリを実行する](query-single-csv-file.md)

- [Parquet ファイルに対してクエリを実行する](query-parquet-files.md)

- [JSON ファイルに対してクエリを実行する](query-json-files.md)

- [フォルダーと複数のファイルに対してクエリを実行する](query-folders-multiple-csv-files.md)

- [パーティション分割とメタデータ関数を使用する](query-specific-files.md)

- [入れ子になっている型に対してクエリを実行する](query-parquet-nested-types.md)

- [ビューの作成と使用](create-use-views.md)
