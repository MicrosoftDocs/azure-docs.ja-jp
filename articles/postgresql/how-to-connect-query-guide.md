---
title: 接続とクエリ - Single Server PostgreSQL
description: Azure Database for PostgreSQL 単一サーバーに接続してクエリを実行する方法を示すクイックスタートへのリンク。
services: postgresql
ms.service: postgresql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 09/21/2020
ms.openlocfilehash: 413f5fc1f6579102b62042c1470816470c2e1449
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92546553"
---
# <a name="connect-and-query-overview-for-azure-database-for-postgresql--single-server"></a>Azure Database for PostgreSQL - Single Server への接続とクエリの概要

次のドキュメントは、Azure Database for PostgreSQL Single Server に接続してクエリを実行する例を紹介したリンク集です。 このガイドには、以下のサポートされている言語でサーバーに接続するために使用できる TLS の推奨事項と拡張機能も含まれています。

## <a name="quickstarts"></a>クイックスタート

| クイック スタート | 説明 |
|---|---|
|[pgadmin](https://www.pgadmin.org/)|pgadmin を使用してサーバーに接続すると、データベース オブジェクトの作成、保守、および使用が簡単になります。|
|[Azure Cloud Shell の psql](quickstart-create-server-database-azure-cli.md#connect-to-the-azure-database-for-postgresql-server-by-using-psql)|この記事では、[Azure Cloud Shell](../cloud-shell/overview.md) で [**psql**](https://www.postgresql.org/docs/current/static/app-psql.html) を実行してサーバーに接続し、ステートメントを実行してデータベース内のデータを照会、挿入、更新、削除する方法について説明します。開発環境にインストールされている場合は、**psql** を実行できます。|
|[PostgreSQL と VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)|Azure Databases extension for VS Code (プレビュー) を使用すると、scrapbooks と高度な Intellisense を使用して、ローカルとクラウドの両方で PostgreSQL サーバーを参照し、クエリを実行できます。 |
|[PHP](connect-php.md)|このクイック スタートでは、PHP を使ってプログラムを作成してデータベースに接続し、データベース オブジェクトを使ってデータに対してクエリを実行する方法について説明します。|
|[Java](connect-java.md)|このクイック スタートでは、Java を使ってデータベースに接続した後、データベース オブジェクトを使ってデータに対してクエリを実行する方法について説明します。|
|[Node.js](connect-nodejs.md)|このクイック スタートでは、Node.js を使ってプログラムを作成してデータベースに接続し、データベース オブジェクトを使ってデータに対してクエリを実行する方法について説明します。|
|[.NET (C#)](connect-csharp.md)|このクイック スタートでは、.NET (C#) を使って C# プログラムを作成してデータベースに接続し、データベース オブジェクトを使ってデータに対してクエリを実行する方法について説明します。|
|[Go](connect-go.md)|このクイック スタートでは、Go を使用してデータベースに接続する方法を紹介します。 Transact-SQL ステートメントを使用してデータを照会および変更する方法についても説明します。|
|[Python](connect-python.md)|このクイック スタートでは、Python を使ってデータベースに接続した後、データベース オブジェクトを使ってデータに対してクエリを実行する方法について説明します。 |
|[Ruby](connect-ruby.md)|このクイック スタートでは、Ruby を使ってプログラムを作成してデータベースに接続し、データベース オブジェクトを使ってデータに対してクエリを実行する方法について説明します。|

## <a name="tls-considerations-for-database-connectivity"></a>データベース接続に関する TLS の考慮事項

トランスポート層セキュリティ (TLS) は、Azure Database for PostgreSQL 内のデータベースへの接続のために Microsoft で提供またはサポートされているすべてのドライバーで使用されています。 特別な構成は必要ありませんが、新しく作成されたサーバーには TLS 1.2 が強制されます。 TLS 1.0 と 1.1 を使用している場合は、サーバーの TLS バージョンを更新することを推奨します。 [TLS の構成方法](howto-tls-configurations.md)に関するページを参照してください

## <a name="postgresql-extensions"></a>PostgreSQL 拡張機能

PostgreSQL では拡張機能を使用してデータベースの機能を拡張することができます。 拡張機能により、関連する複数の SQL オブジェクトを単一のパッケージにまとめて、単一のコマンドでデータベースに対する読み込みや削除を行うことができます。 データベースに読み込まれた後、拡張機能は組み込み機能と同じように機能します。

- [Postgres 11 の拡張機能](./concepts-extensions.md#postgres-11-extensions)
- [Postgres 10 の拡張機能](./concepts-extensions.md#postgres-10-extensions)
- [Postgres 9.6 の拡張機能](./concepts-extensions.md#postgres-96-extensions)
- [Postgres 9.5 の拡張機能](./concepts-extensions.md#postgres-95-extensions)

詳細については、[単一サーバーで PostgreSQL 拡張機能を使用する方法](concepts-extensions.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

- [ダンプと復元を使用したデータの移行](howto-migrate-using-dump-and-restore.md)
- [インポートおよびエクスポートを使用したデータの移行](howto-migrate-using-export-and-import.md)
