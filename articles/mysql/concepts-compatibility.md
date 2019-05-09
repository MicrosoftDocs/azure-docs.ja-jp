---
title: Azure Database for MySQL ドライバーと管理ツールの互換性
description: この記事では、Azure Database for MySQL との、MySQL ドライバーと管理ツールの互換性について説明します。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 03/19/2019
ms.openlocfilehash: 05f48145973777052590f8d10e1a2ce1fd22ec7a
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258107"
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>Azure Database for MySQL に対する MySQL ドライバーと管理ツールの互換性
この記事では、Azure Database for MySQL との、ドライバーと管理ツールの互換性について説明します。

## <a name="mysql-drivers"></a>MySQL ドライバー
Azure Database for MySQL では、MySQL データベースの世界で最も人気のある Community Edition を使用しています。 そのため、さまざまな種類のプログラミング言語とドライバーと互換性があります。 目標は、MySQL ドライバーの 3 つの最新バージョンと、常に MySQL ドライバーの機能と使いやすさを向上させ続けているオープン ソース コミュニティの作成者の努力をサポートすることです。 テストによって Azure Database for MySQL 5.6 および 5.7 と互換性のあることがわかっているドライバーの一覧を次の表に示します。

| **ドライバー** | **リンク** | **互換性のあるバージョン** | **互換性のないバージョン** | **メモ** |
| :-------- | :------------------------ | :----------- | :---------------------- | :--------------------------------------- |
| PHP | https://secure.php.net/downloads.php | 5.5、5.6、7.x | 5.3 | SSL MySQLi との PHP 7.0 接続では、接続文字列に MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT を追加します。 <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> PDO の設定: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` オプションを false に設定します。|
| .NET | [GitHub 上の MySqlConnector](https://github.com/mysql-net/MySqlConnector) <br> [Nuget のインストール パッケージ](https://www.nuget.org/packages/MySqlConnector/) | 0.27 以降 | 0.26.5 以前 | |
| MySQL コネクタ/NET | [MySQL コネクタ/NET](https://github.com/mysql/mysql-connector-net) | 8.0、7.0、6.10 |  | 一部の非 UTF8 Windows システムでは、エンコードのバグが原因で、接続できない場合があります。 |
| Nodejs |  [GitHub 上の MySQLjs](https://github.com/mysqljs/mysql/) <br> NPM のインストール パッケージ:<br> NPM から `npm install mysql` を実行 | 2.15 | 2.14.1 以前 | |
| GO | https://github.com/go-sql-driver/mysql/releases | 1.3、1.4 | 1.2 以前 | バージョン 1.3 では接続文字列で `allowNativePasswords=true` を使用します。 バージョン 1.4 には修正プログラムが含まれているため、`allowNativePasswords=true` は不要になりました。 |
| Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3、2.0、2.1、2.2 | 1.2.2 以前 | |
| Java | https://downloads.mariadb.org/connector-java/ | 2.1、2.0、1.6 | 1.5.5 以前 | |

## <a name="management-tools"></a>管理ツール
互換性の利点は、データベース管理ツールにも拡張されています。 データベース操作がユーザーのアクセス許可の範囲内で動作する限り、既存のツールは引き続き Azure Database for MySQL でも機能します。 テストによって Azure Database for MySQL 5.6 および 5.7 と互換性のあることがわかっている 3 つの一般的なデータベース管理ツールの一覧を次の表に示します。

|                                     | **MySQL Workbench 6.x 以降** | **Navicat 12** | **PHPMyAdmin 4.x 以降** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------|
| 作成、更新、読み取り、書き込み、削除 | X | X | X |
| SSL 接続 | X | X | X |
| SQL クエリのオート コンプリート | X | X |  |
| データのインポートとエクスポート | X | X | X |
| 複数の形式へのエクスポート | X | X | X |
| バックアップと復元 |  | X |  |
| サーバー パラメーターの表示 | X | X | X |
| クライアント接続の表示 | X | X | X |

## <a name="next-steps"></a>次の手順

- [Azure Database for MySQL への接続に関する問題のトラブルシューティング](howto-troubleshoot-common-connection-issues.md)