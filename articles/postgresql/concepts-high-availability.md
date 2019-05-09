---
title: Azure Database for PostgreSQL での高可用性の概念
description: この記事では、Azure Database for PostgreSQL を使用する場合の高可用性の情報を提供します。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: d43647b57469efa4581dc1c74a842a51e9d54ad7
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2019
ms.locfileid: "55699818"
---
# <a name="high-availability-concepts-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL での高可用性の概念
Azure Database for PostgreSQL サービスは、保証された高いレベルの可用性を提供します。 財務的な裏付けのあるサービス レベル アグリーメント (SLA) は、一般公開時に 99.99% です。 このサービスを使用すると、アプリケーション ダウンタイムはほぼ存在しません。

## <a name="high-availability"></a>高可用性
高可用性 (HA) モデルは、ノード レベルの中断が発生した場合の組み込みのフェールオーバー メカニズムに基づいています。 ノード レベルの中断は、ハードウェア障害のために、またはサービスのデプロイに応答して発生する可能性があります。

Azure Database for PostgreSQL データベース サーバーに対する変更は、常にトランザクションのコンテキストで行われます。 変更は、トランザクションがコミットされたときに、同期的に Azure ストレージ内に記録されます。 ノード レベルの中断が発生した場合、データベース サーバーは新しいノードを自動的に作成し、データ ストレージをその新しいノードに接続します。 アクティブな接続はすべて削除され、処理中のどのトランザクションもコミットされません。

## <a name="application-retry-logic-is-essential"></a>アプリケーションの再試行ロジックが不可欠です。
PostgreSQL データベース アプリケーションが、削除された接続や失敗したトランザクションを検出し、再試行するように構築されていることが重要です。 アプリケーションが再試行すると、そのアプリケーションの接続は新しく作成されたインスタンスに透過的にリダイレクトされ、それが失敗したインスタンスを引き継ぎます。

Azure では内部的に、接続を新しいインスタンスにリダイレクトするためにゲートウェイが使用されます。 中断時は通常、フェールオーバー プロセス全体に数十秒かかります。 リダイレクトはゲートウェイによって内部的に処理されるため、クライアント アプリケーションの外部接続文字列は変わりません。

## <a name="scaling-up-or-down"></a>スケールアップまたはスケールダウン
HA モデルと同様に、Azure Database for PostgreSQL がスケールアップまたはスケールダウンされると、指定されたサイズの新しいサーバー インスタンスが作成されます。 既存のデータ ストレージは元のインスタンスからデタッチされ、新しいインスタンスに接続されます。

スケール操作中、データベース接続の中断が発生します。 クライアント アプリケーションは切断され、未処理のコミットされていないトランザクションは取り消されます。 クライアント アプリケーションが接続を再試行するか、または新しい接続を作成すると、ゲートウェイはその接続を新しくサイズ設定されたインスタンスに転送します。 

## <a name="next-steps"></a>次の手順
- [一時的な接続エラーへの対処](concepts-connectivity.md)について学習する
- [読み取りレプリカを使用してデータをレプリケートする](howto-read-replicas-portal.md)方法を学習する
