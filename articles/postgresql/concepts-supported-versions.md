---
title: Azure Database for PostgreSQL のサポートされているバージョン - Single Server
description: Azure Database for PostgreSQL のサポートされているバージョン - Single Server について説明します。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/11/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: c328f0dc510c1b7d36b547e6560a292f98d72f6f
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448042"
---
# <a name="supported-postgresql-database-versions"></a>サポートされている PostgreSQL Database バージョン
Microsoft では、Azure Database for PostgreSQL - Single Server での PostgreSQL エンジンの n-2 バージョンのサポートを予定しています。 バージョンは、Azure の現在のメジャー バージョン (n) とその前の 2 つのメジャー バージョン (-2) です。

Azure Database for PostgreSQL では現在、次のバージョンがサポートされています。

## <a name="postgresql-version-112"></a>PostgreSQL バージョン 11.2
このマイナー バージョンの機能強化と修正の詳細については、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/11/static/release-11-2.html)を参照してください。

>[!NOTE]
> PostgreSQL バージョン 11 がプレビューで使用できます。 Azure portal を使用した作成のサポートが展開中ですが、お客様のリージョンではまだ利用できない場合があります。 どのリージョンでも [Azure CLI](quickstart-create-server-database-azure-cli.md) を使用して Postgres 11 サーバーを作成できます。 たとえば、「 `az postgres server create -g group -n server -u username -p password -l westeurope --sku-name GP_Gen5_2 --version 11` 」のように入力します。

## <a name="postgresql-version-107"></a>PostgreSQL バージョン 10.7
このマイナー バージョンの機能強化と修正の詳細については、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/10/static/release-10-7.html)を参照してください。

## <a name="postgresql-version-9612"></a>PostgreSQL バージョン 9.6.12
このマイナー バージョンの機能強化と修正の詳細については、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/9.6/static/release-9-6-12.html)を参照してください。

## <a name="postgresql-version-9516"></a>PostgreSQL バージョン 9.5.16
このマイナー バージョンの機能強化と修正については、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/9.5/static/release-9-5-16.html)を参照してください。

## <a name="managing-updates-and-upgrades"></a>更新プログラムとアップグレードの管理
Azure Database for PostgreSQL では、マイナー バージョンの修正プログラム適用については自動管理されます。 現在、メジャー バージョンのアップグレードはサポートされていません。 たとえば、PostgreSQL 9.5 から PostgreSQL 9.6 へのアップグレードはサポートされません。 次のメジャー バージョンにアップグレードする場合は、新しいエンジンのバージョンで作成されたサーバーにデータベースの[ダンプと復元](./howto-migrate-using-dump-and-restore.md)を作成します。

> PostgreSQL バージョン 10 より前は、[PostgreSQL のバージョン管理ポリシー](https://www.postgresql.org/support/versioning/)では、1 番目_または_ 2 番目の番号が増えることが_メジャー バージョン_のアップグレードと見なされました (たとえば、9.5 から 9.6 への変更は、_メジャー_ バージョンのアップグレードと見なされました)。
> バージョン 10 以降、1 番目の番号の変更のみメジャー バージョンのアップグレードと見なされます (たとえば、10.0 から 10.1 への変更は_マイナー_ バージョンのアップグレードであり、10 から 11 への変更は_メジャー_ バージョンのアップグレードです)。

## <a name="next-steps"></a>次の手順
さまざまな PostgreSQL 拡張機能のサポートについては、[PostgreSQL 拡張機能](concepts-extensions.md)に関するページをご覧ください。
