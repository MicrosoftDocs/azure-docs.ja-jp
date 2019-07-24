---
title: Azure Database for PostgreSQL のサポートされているバージョン
description: サポートされている Azure Database for PostgreSQL バージョンについて説明します。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: 5b06128979bf448a0b85084d5178d9291beb7691
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2018
ms.locfileid: "53542794"
---
# <a name="supported-postgresql-database-versions"></a>サポートされている PostgreSQL Database バージョン
マイクロソフトでは、Azure Database for PostgreSQL サービスでの PostgreSQL エンジンの n-2 バージョンのサポートを予定しています。 バージョンは、Azure の現在のメジャー バージョン (n) とその前の 2 つのメジャー バージョン (-2) です。

Azure Database for PostgreSQL では現在、次のバージョンがサポートされています。

## <a name="postgresql-version-105"></a>PostgreSQL バージョン 10.5
このマイナー バージョンの機能強化と修正の詳細については、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/10/static/release-10-5.html)を参照してください。

## <a name="postgresql-version-9610"></a>PostgreSQL バージョン 9.6.10
このマイナー バージョンの機能強化と修正の詳細については、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/9.6/static/release-9-6-10.html)を参照してください。

## <a name="postgresql-version-9514"></a>PostgreSQL バージョン 9.5.14
このマイナー バージョンの機能強化と修正については、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/9.5/static/release-9-5-14.html)を参照してください。

## <a name="managing-updates-and-upgrades"></a>更新プログラムとアップグレードの管理
Azure Database for PostgreSQL では、マイナー バージョンの修正プログラム適用については自動管理されます。 現在、メジャー バージョンのアップグレードはサポートされていません。 たとえば、PostgreSQL 9.5 から PostgreSQL 9.6 へのアップグレードはサポートされません。 次のメジャー バージョンにアップグレードする場合は、新しいエンジンのバージョンで作成されたサーバーにデータベースの[ダンプと復元](./howto-migrate-using-dump-and-restore.md)を作成します。

## <a name="next-steps"></a>次の手順
さまざまな PostgreSQL 拡張機能のサポートについては、[PostgreSQL 拡張機能](concepts-extensions.md)に関するページをご覧ください。
