---
title: Azure Database for PostgreSQL の制限事項
description: この記事では、Azure Database for PostgreSQL の制限 (接続数やストレージ エンジンのオプションなど) について説明します。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 1/22/2019
ms.openlocfilehash: 843107b8d251c2073ba9e02beacb16ab7615eca6
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2019
ms.locfileid: "54470734"
---
# <a name="limitations-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL の制限事項
次のセクションでは、データベース サービス容量と機能の制限について説明します。

## <a name="maximum-connections"></a>最大接続数
価格レベルと仮想コアごとの最大接続数は次のとおりです。 

|**価格レベル**| **仮想コア数**| **最大接続数** |
|---|---|---|
|Basic| 1| 50 |
|Basic| 2| 100 |
|汎用| 2| 150|
|汎用| 4| 250|
|汎用| 8| 480|
|汎用| 16| 950|
|汎用| 32| 1500|
|汎用| 64| 1900|
|メモリ最適化| 2| 300|
|メモリ最適化| 4| 500|
|メモリ最適化| 8| 960|
|メモリ最適化| 16| 1900|
|メモリ最適化| 32| 1900|

接続数が制限を超えると、次のエラーが表示される場合があります。
> FATAL:  sorry, too many clients already

Azure システムでは、Azure Database for PostgreSQL サーバーを監視する 5 つの接続が必要です。 

## <a name="functional-limitations"></a>機能制限
### <a name="scale-operations"></a>スケール操作
- Basic 価格レベルとの間の動的スケーリングは現在サポートされていません。
- 現在、サーバー ストレージを減らすことはできません。

### <a name="server-version-upgrades"></a>サーバー バージョンのアップグレード
- データベース エンジンのメジャー バージョン間での自動移行は現在サポートされていません。 次のメジャー バージョンにアップグレードする場合は、新しいエンジンのバージョンで作成されたサーバーに[ダンプを復元](./howto-migrate-using-dump-and-restore.md)します。

### <a name="vnet-service-endpoints"></a>VNet サービス エンドポイント
- VNet サービス エンドポイントは、汎用サーバーとメモリ最適化サーバーでのみサポートされています。

### <a name="restoring-a-server"></a>サーバーの復元
- PITR 機能を使うと、基になっているサーバーと同じ価格レベルの構成で新しいサーバーが作成されます。
- 復元中に作成される新しいサーバーには、元のサーバーに存在するファイアウォール規則はありません。 この新しいサーバー用のファイアウォール規則を個別に設定する必要があります。
- 削除されたサーバーへの復元はサポートされていません。

### <a name="utf-8-characters-on-windows"></a>Windows での UTF-8 文字
- 一部のシナリオにおいて、Windows 上のオープン ソース PostgreSQL では UTF-8 文字が完全にはサポートされません。これは、Azure Database for PostgreSQL に影響するためです。 詳細については、[PostgreSQL アーカイブ内の Bug #15476](https://www.postgresql-archive.org/BUG-15476-Problem-on-show-trgm-with-4-byte-UTF-8-characters-td6056677.html) のスレッドを参照してください。

## <a name="next-steps"></a>次の手順
- [各価格レベルで使用できる内容](concepts-pricing-tiers.md)について理解します
- [サポートされている PostgreSQL Database バージョン](concepts-supported-versions.md)について学習します
- [Azure Portal を使用して Azure Database for PostgreSQL でサーバーをバックアップして復元する方法](howto-restore-server-portal.md)を確認します
