---
title: 接続とクエリ - 単一サーバー MySQL
description: Azure My SQL Database 単一サーバーに接続してクエリを実行する方法を示すクイックスタートへのリンク。
services: mysql
ms.service: mysql
ms.topic: how-to
author: mksuni
ms.author: sumuth
ms.date: 09/22/2020
ms.openlocfilehash: 0414aaad5a1cf6edb9c2152eed70f8753946cca1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92546434"
---
# <a name="connect-and-query-overview-for-azure-database-for-mysql--single-server"></a>Azure Database for MySQL - 単一サーバーへの接続とクエリの概要

次のドキュメントは、Azure Database for MySQL 単一サーバーに接続してクエリを実行する例を紹介したリンク集です。 このガイドには、以下のサポートされている言語でサーバーに接続するために使用できる TLS の推奨事項とライブラリも含まれています。

## <a name="quickstarts"></a>クイックスタート

| クイック スタート | 説明 |
|---|---|
|[MySQL Workbench](connect-workbench.md)|このクイックスタートでは、MySQL Workbench クライアントを使用してデータベースに接続する方法を示します。 その後、MySQL ステートメントを使用して、データベース内のデータの照会、挿入、更新、削除を実行できます。|
|[Azure Cloud Shell](./quickstart-create-mysql-server-database-using-azure-cli.md#connect-to-azure-database-for-mysql-server-using-mysql-command-line-client)|この記事では、[Azure Cloud Shell](../cloud-shell/overview.md) で **mysql.exe** を実行してサーバーに接続し、ステートメントを実行してデータベース内のデータを照会、挿入、更新、削除する方法について説明します。|
|[Visual Studio での MySQL](https://www.mysql.com/why-mysql/windows/visualstudio)|Visual Studio で MySQL を使用して、MySQL サーバーに接続できます。 Visual Studio 用の MySQL はサーバー エクスプローラーに直接統合されており、新しい接続を簡単にセットアップして、データベース オブジェクトを操作できます。|
|[PHP](connect-php.md)|このクイックスタートでは、PHP を使用してプログラムを作成してデータベースに接続し、MySQL ステートメントを使用してデータに対してクエリを実行する方法について説明します。|
|[Java](connect-java.md)|このクイックスタートでは、Java を使用してデータベースに接続した後、MySQL ステートメントを使用してデータに対してクエリを実行する方法について説明します。|
|[Node.js](connect-nodejs.md)|このクイックスタートでは、Node.js を使用してプログラムを作成してデータベースに接続し、MySQL ステートメントを使用してデータに対してクエリを実行する方法について説明します。|
|[.NET (C#)](connect-csharp.md)|このクイックスタートでは、.NET (C#) を使用して C# プログラムを作成してデータベースに接続し、MySQL ステートメントを使用してデータに対してクエリを実行する方法について説明します。|
|[Go](connect-go.md)|このクイック スタートでは、Go を使用してデータベースに接続する方法を紹介します。 Transact-SQL ステートメントを使用してデータを照会および変更する方法についても説明します。|
|[Python](connect-python.md)|このクイックスタートでは、Python を使用してデータベースに接続し、MySQL ステートメントを使用してデータに対してクエリを実行する方法について説明します。 |
|[Ruby](connect-ruby.md)|このクイックスタートでは、Ruby を使用してプログラムを作成してデータベースに接続し、MySQL ステートメントを使用してデータに対してクエリを実行する方法について説明します。|
|[C++](connect-cpp.md)|このクイックスタートでは、C++ を使用してプログラムを作成してデータベースに接続し、データのクエリを実行する方法について説明します。|

## <a name="tls-considerations-for-database-connectivity"></a>データベース接続に関する TLS の考慮事項

トランスポート層セキュリティ (TLS) は、Azure Database for MySQL 内のデータベースへの接続のために Microsoft で提供またはサポートされているすべてのドライバーで使用されています。 特別な構成は必要ありませんが、新しく作成されたサーバーには TLS 1.2 が強制されます。 TLS 1.0 と 1.1 を使用している場合は、サーバーの TLS バージョンを更新することを推奨します。 [TLS の構成方法](howto-tls-configurations.md)に関するページを参照してください。

## <a name="libraries"></a>ライブラリ

Azure Database for MySQL では、MySQL データベースの世界で最も人気のある Community Edition を使用しています。 そのため、さまざまな種類のプログラミング言語とドライバーと互換性があります。 目標は、MySQL ドライバーの 3 つの最新バージョンと、常に MySQL ドライバーの機能と使いやすさを向上させ続けているオープン ソース コミュニティの作成者の努力をサポートすることです。

Azure Database for MySQL 単一サーバーと互換性のある[ドライバー](concepts-compatibility.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

- [ダンプと復元を使用したデータの移行](concepts-migrate-dump-restore.md)
- [インポートおよびエクスポートを使用したデータの移行](concepts-migrate-import-export.md)