---
title: "Azure Database for PostgreSQL の接続ライブラリ | Microsoft Docs"
description: "この記事では、アプリケーションをコーディングして PostgreSQL for Azure Database に接続およびクエリを実行するときに、開発者が使用できるさまざまなライブラリやドライバーについて説明します。"
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 06/15/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 7099e26fd749820ab6869c0262e1f97d993ab7b2
ms.contentlocale: ja-jp
ms.lasthandoff: 06/17/2017

---
# <a name="connection-libraries-for-azure-database-for-postgresql"></a>Azure Database for PostgreSQL の接続ライブラリ
このトピックには、アプリケーションをプログラミングして PostgreSQL for Azure Database に接続およびクエリを実行するときに開発者が使用できるライブラリやドライバーの一覧を示します。

## <a name="client-interfaces"></a>クライアント インターフェイス
PostgreSQL サーバーに接続するほとんどの言語クライアント ライブラリが外部プロジェクトです。このクライアント ライブラリは個別に配布され、 Windows、Linux、および Mac プラットフォームでサポートされます。 一般的なクライアント ドライバーの一部が一覧表示されます。

| **言語** | **クライアント インターフェイス** | **追加情報** | **ダウンロード** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | DB API 2.0 対応 | [ダウンロード](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://php.net/manual/en/book.pgsql.php) | データベース拡張機能 | [インストール](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Pg npm パッケージ](https://www.npmjs.com/package/pg) | 純粋な JavaScript 非ブロッキング クライアント | [インストール](https://www.npmjs.com/package/pg) |
| Java | [JDBC](http://jdbc.postgresql.org/) | Type 4 JDBC ドライバー | [ダウンロード](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Pg gem](https://deveiate.org/code/pg/) | Ruby インターフェイス | [ダウンロード](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Go | [Package pq](https://godoc.org/github.com/lib/pq) | 純粋な Go postgres ドライバー | [インストール](https://github.com/lib/pq/blob/master/README.md) |
| C\#/ .NET | [Npgsql](http://www.npgsql.org/) | ADO.NET データ プロバイダー | [ダウンロード](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | ODBC ドライバー | [ダウンロード](http://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | プライマリ C 言語インターフェイス | あり |
| C++ | [libpqxx](http://pqxx.org/) | 新しいスタイルの C++ インターフェイス | [ダウンロード](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>次のステップ
これらのクイックスタートを読み、次の言語を使用して Azure Database for PostgreSQL に接続およびクエリを実行する方法について確認します。

[Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [.NET (C#)](./connect-csharp.md)

