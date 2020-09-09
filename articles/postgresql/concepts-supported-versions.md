---
title: サポートされるバージョン - Azure Database for PostgreSQL - Single Server
description: Azure Database for PostgreSQL - Single Server のサポートされる Postgres メジャーおよびマイナー バージョンについて説明します。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/22/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 1640395b3a73116c27894a2b3f2b95b8bd5bb2eb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87084162"
---
# <a name="supported-postgresql-major-versions"></a>サポートされる PostgreSQL のメジャー バージョン
Microsoft では、Azure Database for PostgreSQL - Single Server での PostgreSQL エンジンの n-2 バージョンのサポートを予定しています。 バージョンは、Azure の現在のメジャー バージョン (n) とその前の 2 つのメジャー バージョン (-2) です。

Azure Database for PostgreSQL では現在、次のメジャー バージョンがサポートされています。

## <a name="postgresql-version-11"></a>PostgreSQL バージョン 11
現在のマイナー リリースは 11.6 です。 このマイナー リリースの機能強化と修正の詳細については、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/11/static/release-11-6.html)を参照してください。

## <a name="postgresql-version-10"></a>PostgreSQL バージョン 10
現在のマイナー リリースは 10.11 です。 このマイナー リリースの機能強化と修正の詳細については、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/10/static/release-10-11.html)を参照してください。

## <a name="postgresql-version-96"></a>PostgreSQL バージョン 9.6
現在のマイナー リリースは 9.6.16 です。 このマイナー リリースの機能強化と修正の詳細については、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/9.6/static/release-9-6-16.html)を参照してください。

## <a name="postgresql-version-95"></a>PostgreSQL バージョン 9.5
現在のマイナー リリースは 9.5.20 です。 このマイナー リリースの機能強化と修正については、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/9.5/static/release-9-5-20.html)を参照してください。

## <a name="managing-upgrades"></a>アップグレードの管理
PostgreSQL プロジェクトでは、報告されたバグを修正するためにマイナー リリースを定期的に発行しています。 Azure Database for PostgreSQL は、サービスの月次デプロイ中に、マイナー リリースのサーバーに自動的に修正を適用します。 

メジャー バージョンの自動インプレース アップグレードはサポートされていません。 次のメジャー バージョンにアップグレードするには、以下の手順を実行します。 
   * [pg_dump と pg_restore](./howto-migrate-using-dump-and-restore.md) を使用して、新しいエンジン バージョンで作成されたサーバーにデータベースを移動します。
   * または、[Azure Database Migration Service](..\dms\tutorial-azure-postgresql-to-azure-postgresql-online-portal.md) を使用して、PostgreSQL 10 から 11 にアップグレードすることもできます。

### <a name="version-syntax"></a>バージョンの構文
PostgreSQL バージョン 10 より前は、[PostgreSQL のバージョン管理ポリシー](https://www.postgresql.org/support/versioning/)では、1 番目 _または_ 2 番目の番号が増えることが _メジャー バージョン_ のアップグレードと見なされていました。 たとえば、9.5 から 9.6 への変更は、_メジャー_ バージョンのアップグレードと見なされていました。 バージョン 10 以降は、1 番目の番号の変更のみがメジャー バージョンのアップグレードと見なされます。 たとえば、10.0 から 10.1 への変更は、_マイナー_ リリースのアップグレードになります。 バージョン 10 から 11 への変更が、_メジャー_ バージョンのアップグレードになります。

## <a name="next-steps"></a>次のステップ
サポートされている PostgreSQL 拡張機能の詳細については、[拡張機能に関するドキュメント](concepts-extensions.md)を参照してください。
