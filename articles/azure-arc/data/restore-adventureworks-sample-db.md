---
title: AdventureWorks サンプル データベースを SQL Managed Instance に復元する
description: AdventureWorks サンプル データベースを SQL Managed Instance に復元する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: d238a889648e789087a0803f6b50288318462c7b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "91629065"
---
# <a name="restore-the-adventureworks-sample-database-into-sql-managed-instance---azure-arc"></a>AdventureWorks サンプル データベースを SQL Managed Instance に復元する - Azure Arc

[AdventureWorks](/sql/samples/adventureworks-install-configure?view=sql-server-ver15&tabs=tsql&preserve-view=true) は、チュートリアルや例でよく使用される OLTP データベースを含むサンプル データベースです。 これは、[SQL Server サンプル GitHub リポジトリ](https://github.com/microsoft/sql-server-samples/tree/master/samples/databases)の一部として、Microsoft によって提供され、管理されています。

このドキュメントでは、AdventureWorks サンプル データベースを SQL Managed Instance - Azure Arc に復元するための簡単なプロセスを説明します。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="download-the-adventureworks-backup-file"></a>AdventureWorks のバックアップ ファイルをダウンロードする

AdventureWorks バックアップ (.bak) ファイルを SQL Managed Instance コンテナーにダウンロードします。 この例では、`kubectl exec` コマンドを使用して、SQL Managed Instance コンテナーの内部でコマンドをリモートで実行し、.bak ファイルをコンテナーにダウンロードします。 SQL Managed Instance コンテナーの内部になるようにプルする他のデータベース バックアップ ファイルがある場合、`wget` によってアクセス可能な任意の場所からこのファイルをダウンロードします。 それを SQL Managed Instance コンテナーの内部に入れると、標準の `RESTORE DATABASE` T-SQL を使用して簡単に復元できます。

次のようなコマンドを実行して、.bak ファイルをダウンロードします。実行する前に、ポッド名と名前空間名の値を置換します。
> [!NOTE]
>  GitHub からファイルをダウンロードするには、コンテナーが 443 経由でインターネットに接続されている必要があります

```console
kubectl exec <SQL pod name> -n <namespace name> -c arc-sqlmi -- wget https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2019.bak -O /var/opt/mssql/data/AdventureWorks2019.bak
```

例

```console
kubectl exec sqltest1-0 -n arc -c arc-sqlmi -- wget https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2019.bak -O /var/opt/mssql/data/AdventureWorks2019.bak
```

## <a name="restore-the-adventureworks-database"></a>AdventureWorks データベースを復元する

同様に、`kubectl` exec コマンドを実行して、SQL Managed Instance コンテナーに含まれている `sqlcmd` CLI ツールを使用し、T-SQL コマンドを実行してデータベースを復元 (RESTORE DATABASE) できます。

次のようなコマンドを実行して、データベースを復元します。 それを実行する前に、ポッド名、パスワード、および名前空間名の値を置き換えます。

```console
kubectl exec <SQL pod name> -n <namespace name> -c arc-sqlmi -- /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P <password> -Q "RESTORE DATABASE AdventureWorks2019 FROM  DISK = N'/var/opt/mssql/data/AdventureWorks2019.bak' WITH MOVE 'AdventureWorks2017' TO '/var/opt/mssql/data/AdventureWorks2019.mdf', MOVE 'AdventureWorks2017_Log' TO '/var/opt/mssql/data/AdventureWorks2019_Log.ldf'"
```
例

```console
kubectl exec sqltest1-0 -n arc -- -c arc-sqlmi /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P MyPassword! -Q "RESTORE DATABASE AdventureWorks2019 FROM  DISK = N'/var/opt/mssql/data/AdventureWorks2019.bak' WITH MOVE 'AdventureWorks2017' TO '/var/opt/mssql/data/AdventureWorks2019.mdf', MOVE 'AdventureWorks2017_Log' TO '/var/opt/mssql/data/AdventureWorks2019_Log.ldf'"
```
