---
title: バージョン管理ポリシー - Azure Database for PostgreSQL - シングル サーバーとフレキシブル サーバー (プレビュー)
description: Azure Database for PostgreSQL - シングル サーバーの Postgres メジャーおよびマイナー バージョンに関するポリシーについて説明します。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/05/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 62fe1b3391eb4cb2d409a92b936fd3f1ae56d992
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100518421"
---
# <a name="azure-database-for-postgresql-versioning-policy"></a>Azure Database for PostgreSQL のバージョン管理ポリシー

このページでは、Azure Database for PostgreSQL のバージョン管理ポリシーについて説明します。Azure Database for PostgreSQL - シングル サーバーと Azure Database for PostgreSQL - フレキシブル サーバー (プレビュー) のデプロイ モードに適用できます。

## <a name="supported--postgresql-versions"></a>サポートされている PostgreSQL のバージョン

Azure Database for PostgreSQL では、次のデータベース バージョンがサポートされています。

| Version | シングル サーバー | フレキシブル サーバー (プレビュー) |
| ----- | :------: | :----: |
| PostgreSQL 12 |  | X  | 
| PostgreSQL 11 | X | X |
| PostgreSQL 10 | X |  |
| PostgreSQL 9.6 | X |  |
| *PostgreSQL 9.5 (提供終了)* | X |  |

## <a name="major-version-support"></a>メジャー バージョンのサポート
PostgreSQL の各メジャー バージョンは、[PostgreSQL コミュニティ バージョン管理ポリシー](https://www.postgresql.org/support/versioning/)に記載されているように、Azure によってバージョンのサポートが開始される日付から PostgreSQL コミュニティによってバージョンが提供終了されるまで、Azure Database for PostgreSQL によってサポートされます。

## <a name="minor-version-support"></a>マイナー バージョンのサポート
Azure Database for PostgreSQL では、定期的なメンテナンスの一環として、Azure で優先される PostgreSQL バージョンへのマイナー バージョン アップグレードが自動的に実行されます。 

## <a name="major-version-retirement-policy"></a>メジャー バージョンの提供終了ポリシー
次の表は、PostgreSQL メジャー バージョンの提供終了の詳細を示しています。 日付は、[PostgreSQL コミュニティ バージョン管理ポリシー](https://www.postgresql.org/support/versioning/)に従っています。

| Version | 新着記事 | Azure サポートの開始日 | 提供終了日|
| ----- | ----- | ------ | ----- |
| [PostgreSQL 9.5 (提供終了)](https://www.postgresql.org/about/news/postgresql-132-126-1111-1016-9621-and-9525-released-2165/)| [機能](https://www.postgresql.org/docs/9.5/release-9-5.html)  | 2018 年 4 月 18 日   | 2021 年 2 月 11 日
| [PostgreSQL 9.6](https://www.postgresql.org/about/news/postgresql-96-released-1703/) | [機能](https://wiki.postgresql.org/wiki/NewIn96) | 2018 年 4 月 18 日  | 2021 年 11 月 11 日
| [PostgreSQL 10](https://www.postgresql.org/about/news/postgresql-10-released-1786/) | [機能](https://wiki.postgresql.org/wiki/New_in_postgres_10) | 2018 年 6 月 4 日  | 2022 年 11 月 10 日
| [PostgreSQL 11](https://www.postgresql.org/about/news/postgresql-11-released-1894/) | [機能](https://www.postgresql.org/docs/11/release-11.html) | 2019 年 7 月 24 日  | 2023 年 11 月 9 日
| [PostgreSQL 12](https://www.postgresql.org/about/news/postgresql-12-released-1976/) | [機能](https://www.postgresql.org/docs/12/release-12.html) | 2020 年 9 月 22 日  | 2024 年 11 月 14 日

## <a name="retired-postgresql-engine-versions-not-supported-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL でサポートされていない、廃止された PostgreSQL エンジンのバージョン

Azure Database for PostgreSQL では、提供が終了されたバージョンを引き続き実行できます。 ただし、各 PostgreSQL データベース バージョンの提供終了日以降は、次の制限事項に注意してください。
- コミュニティではバグの修正やセキュリティの修正プログラムが今後リリースされることはないため、Azure Database for PostgreSQL では、提供終了したデータベース エンジンにバグやセキュリティ上の問題に対応するパッチが適用されたり、提供終了したデータベース エンジンに関してセキュリティ対策が実施されたりすることはありません。 その結果、セキュリティの脆弱性やその他の問題が発生する可能性があります。 ただし、Azure では、ホスト、OS、コンテナー、およびその他のサービス関連のコンポーネントに対して、定期的なメンテナンスとパッチの適用が引き続き実行されます。
- PostgreSQL データベースに関連するサポートの問題が発生した場合、Microsoft ではサポートを提供できない場合があります。 このような場合、Microsoft がサポートを提供するには、お客様がデータベースをアップグレードする必要があります。
- 提供終了したバージョンの新しいデータベース サーバーを作成することはできません。 ただし、特定の時点への回復を実行し、既存のサーバーの読み取りレプリカを作成することはできます。
- Azure Database for PostgreSQL で開発された新しいサービス機能は、サポートされているデータベース サーバーのバージョンでのみ使用できます。
- アップタイム SLA は、Azure Database for PostgreSQL のサービス関連の問題に対してのみ適用され、データベース エンジンに関連するバグに起因するダウンタイムには適用されません。  
- サービスに深刻な脅威を及ぼす PostgreSQL データベース エンジンの脆弱性が廃止されたデータベース バージョンに見つかった場合、Azure はサービスをセキュリティで保護するため、ユーザーのデータベース サーバーを停止する場合があります。 このような場合、サーバーを稼働させる前に、サーバーをアップグレードするように通知されます。

## <a name="postgresql-version-syntax"></a>PostgreSQL バージョンの構文
PostgreSQL バージョン 10 より前は、[PostgreSQL のバージョン管理ポリシー](https://www.postgresql.org/support/versioning/)では、1 番目 _または_ 2 番目の番号が増えることが _メジャー バージョン_ のアップグレードと見なされていました。 たとえば、9.5 から 9.6 への変更は、_メジャー_ バージョンのアップグレードと見なされていました。 バージョン 10 以降は、1 番目の番号の変更のみがメジャー バージョンのアップグレードと見なされます。 たとえば、10.0 から 10.1 への変更は、_マイナー_ リリースのアップグレードになります。 バージョン 10 から 11 への変更が、_メジャー_ バージョンのアップグレードになります。

## <a name="next-steps"></a>次のステップ
- Azure Database for PostgreSQL - シングル サーバーで[サポートされるバージョン](./concepts-supported-versions.md)を参照してください
- Azure Database for PostgreSQL - フレキシブル サーバー (プレビュー) で[サポートされるバージョン](flexible-server/concepts-supported-versions.md)を参照してください
- メジャー バージョンのアップグレードを実行する方法の詳細については、[メジャー バージョンのアップグレード](how-to-upgrade-using-dump-and-restore.md)に関するドキュメントを参照してください。
- サポートされている PostgreSQL 拡張機能の詳細については、[拡張機能に関するドキュメント](concepts-extensions.md)を参照してください。
