---
title: サポートされるバージョン - Azure Database for PostgreSQL - Single Server
description: Azure Database for PostgreSQL - Single Server のサポートされる Postgres メジャーおよびマイナー バージョンについて説明します。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/16/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 41662c5e4cc0ed9458f8b1b1279e2753daed789f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100518455"
---
# <a name="supported-postgresql-major-versions"></a>サポートされる PostgreSQL のメジャー バージョン

サポート ポリシーの詳細については、「[Azure Database for PostgreSQL のバージョン管理ポリシー](concepts-version-policy.md)」を参照してください。

Azure Database for PostgreSQL では現在、次のメジャー バージョンがサポートされています。

## <a name="postgresql-version-11"></a>PostgreSQL バージョン 11
現在のマイナー リリースは 11.6 です。 このマイナー リリースの機能強化と修正の詳細については、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/11/static/release-11-6.html)を参照してください。

## <a name="postgresql-version-10"></a>PostgreSQL バージョン 10
現在のマイナー リリースは 10.11 です。 このマイナー リリースの機能強化と修正の詳細については、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/10/static/release-10-11.html)を参照してください。

## <a name="postgresql-version-96"></a>PostgreSQL バージョン 9.6
現在のマイナー リリースは 9.6.16 です。 このマイナー リリースの機能強化と修正の詳細については、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/9.6/static/release-9-6-16.html)を参照してください。

## <a name="postgresql-version-95-retired"></a>PostgreSQL バージョン 9.5 (廃止)
Postgres コミュニティの[バージョン管理ポリシー](https://www.postgresql.org/support/versioning/)に従って、Azure Database for PostgreSQL では、2021 年 2 月 11 日に Postgres バージョン 9.5 が廃止されました。 詳細と制限については、「[Azure Database for PostgreSQL のバージョン管理ポリシー](concepts-version-policy.md)」をご覧ください。 このメジャー バージョンを実行している場合は、より新しいバージョンにアップグレードしてください (できるだけ早く PostgreSQL 11 にアップグレードすることをお勧めします)。

## <a name="managing-upgrades"></a>アップグレードの管理
PostgreSQL プロジェクトでは、報告されたバグを修正するためにマイナー リリースを定期的に発行しています。 Azure Database for PostgreSQL は、サービスの月次デプロイ中に、マイナー リリースのサーバーに自動的に修正を適用します。 

メジャー バージョンの自動インプレース アップグレードはサポートされていません。 より高いレベルのメジャー バージョンにアップグレードするには、以下の手順を実行します。 
   * [ダンプと復元を使用したメジャー バージョンのアップグレード](./how-to-upgrade-using-dump-and-restore.md)に関するページに記載されている方法のいずれかを使用します。
   * [pg_dump と pg_restore](./howto-migrate-using-dump-and-restore.md) を使用して、新しいエンジン バージョンで作成されたサーバーにデータベースを移動します。
   * [Azure Database Migration Service](..\dms\tutorial-azure-postgresql-to-azure-postgresql-online-portal.md) を使用して、オンライン アップグレードを実行します。

### <a name="version-syntax"></a>バージョンの構文
PostgreSQL バージョン 10 より前は、[PostgreSQL のバージョン管理ポリシー](https://www.postgresql.org/support/versioning/)では、1 番目 _または_ 2 番目の番号が増えることが _メジャー バージョン_ のアップグレードと見なされていました。 たとえば、9.5 から 9.6 への変更は、_メジャー_ バージョンのアップグレードと見なされていました。 バージョン 10 以降は、1 番目の番号の変更のみがメジャー バージョンのアップグレードと見なされます。 たとえば、10.0 から 10.1 への変更は、_マイナー_ リリースのアップグレードになります。 バージョン 10 から 11 への変更が、_メジャー_ バージョンのアップグレードになります。

## <a name="next-steps"></a>次のステップ
サポートされている PostgreSQL 拡張機能の詳細については、[拡張機能に関するドキュメント](concepts-extensions.md)を参照してください。
