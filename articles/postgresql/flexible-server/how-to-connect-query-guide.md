---
title: 接続とクエリ - フレキシブル サーバー PostgreSQL
description: Azure Database for PostgreSQL フレキシブル サーバーに接続してクエリを実行する方法を示すクイックスタートへのリンク。
services: postgresql
ms.service: postgresql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 12/08/2020
ms.openlocfilehash: ee3b1f7db8bdafb1233b32579e032e8c864c37a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97364532"
---
# <a name="connect-and-query-overview-for-azure-database-for-postgresql--flexible-server"></a>Azure Database for PostgreSQL フレキシブル サーバーの接続とクエリの概要

次のドキュメントは、Azure Database for PostgreSQL Single Server に接続してクエリを実行する例を紹介したリンク集です。 このガイドには、以下のサポートされている言語でサーバーに接続するために使用できる TLS の推奨事項と拡張機能も含まれています。

>[!IMPORTANT]
> Azure Database for PostgreSQL フレキシブル サーバーは **プレビュー** 段階です。

## <a name="quickstarts"></a>クイックスタート

| クイック スタート | 説明 |
|---|---|
|[pgadmin](https://www.pgadmin.org/)|pgadmin を使用してサーバーに接続すると、データベース オブジェクトの作成、保守、および使用が簡単になります。|
|[Azure Cloud Shell の psql](./quickstart-create-server-cli.md#connect-using-postgresql-command-line-client)|この記事では、[Azure Cloud Shell](../../cloud-shell/overview.md) で [**psql**](https://www.postgresql.org/docs/current/static/app-psql.html) を実行してサーバーに接続し、ステートメントを実行してデータベース内のデータを照会、挿入、更新、削除する方法について説明します。開発環境にインストールされている場合は、**psql** を実行できます。|
|[Python](connect-python.md)|このクイック スタートでは、Python を使ってデータベースに接続した後、データベース オブジェクトを使ってデータに対してクエリを実行する方法について説明します。 |
|[Django と App Service](tutorial-django-app-service-postgres.md)|このチュートリアルでは、Ruby を使ってデータベースに接続するプログラムを作成し、データベース オブジェクトを使ってデータに対してクエリを実行する方法について説明します。|

## <a name="tls-considerations-for-database-connectivity"></a>データベース接続に関する TLS の考慮事項

トランスポート層セキュリティ (TLS) は、Azure Database for PostgreSQL 内のデータベースへの接続のために Microsoft で提供またはサポートされているすべてのドライバーで使用されています。 特別な構成は必要ありませんが、新しく作成されたサーバーには TLS 1.2 が強制されます。 TLS 1.0 と 1.1 を使用している場合は、サーバーの TLS バージョンを更新することを推奨します。 [TLS の構成方法](how-to-connect-tls-ssl.md)に関するページを参照してください

## <a name="postgresql-extensions"></a>PostgreSQL 拡張機能

PostgreSQL では拡張機能を使用してデータベースの機能を拡張することができます。 拡張機能により、関連する複数の SQL オブジェクトを単一のパッケージにまとめて、単一のコマンドでデータベースに対する読み込みや削除を行うことができます。 データベースに読み込まれた後、拡張機能は組み込み機能と同じように機能します。

- [Postgres 12 の拡張機能](./concepts-extensions.md#postgres-12-extensions)
- [Postgres 11 の拡張機能](./concepts-extensions.md#postgres-11-extensions)
- [dblink および postgres_fdw](./concepts-extensions.md#dblink-and-postgres_fdw)
- [pg_prewarm](./concepts-extensions.md#pg_prewarm)
- [pg_stat_statements](./concepts-extensions.md#pg_stat_statements)

詳細については、[フレキシブル サーバーで PostgreSQL 拡張機能を使用する方法](concepts-extensions.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

- [ダンプと復元を使用したデータの移行](../howto-migrate-using-dump-and-restore.md)
- [インポートおよびエクスポートを使用したデータの移行](../howto-migrate-using-export-and-import.md)
