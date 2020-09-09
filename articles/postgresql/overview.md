---
title: Azure Database for PostgreSQL リレーショナル データベース サービスの概要
description: Azure Database for PostgreSQL リレーショナル データベース サービスについて概説します。
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 11/25/2019
ms.openlocfilehash: deb720638e0921762eb9c5800d218c3cfccb7f55
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2020
ms.locfileid: "85213603"
---
# <a name="what-is-azure-database-for-postgresql"></a>Azure Database for PostgreSQL とは
Azure Database for PostgreSQL は、開発者向けに構築された、Microsoft クラウドのリレーショナル データベース サービスです。 これは、コミュニティ バージョンのオープンソースの [PostgreSQL](https://www.postgresql.org/) データベース エンジンに基づいており、2 つのデプロイ オプション: Single Server と Hyperscale (Citus) で使用できます。

## <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - Single Server
Single Server デプロイ オプションでは、次のことを実現できます。

- 追加コストなしの組み込みの[高可用性](concepts-high-availability.md) (99.99% SLA)
- 包括的な従量課金制の料金を使用した、予測可能なパフォーマンス
- 数秒以内に[必要に応じて垂直スケーリング](concepts-pricing-tiers.md)
- サーバーを評価するための[監視とアラート](concepts-monitoring.md)
- エンタープライズグレードのセキュリティとコンプライアンス
- 保存中や転送中の機密データの[セキュリティ保護](concepts-security.md)
- [自動バックアップとポイントインタイム リストア](concepts-business-continuity.md) (最大 35 日間)


これらすべての機能は、ほとんど管理を必要とせず、いずれも追加費用なしで利用することができます。 これらにより、貴重な時間とリソースを仮想マシンとインフラストラクチャの管理に費やすことなく、迅速なアプリケーション開発や、製品化に要する時間の短縮に専念することができます。 新しいスキルを身に付けなくても、オープンソースのツールとプラットフォームを自由に選んでアプリケーションの開発を続けることができます。

Single Server デプロイ オプションでは、次の 3 つの価格レベルが提供されます。Basic、汎用、メモリ最適化。 使用しているデータベースのワークロードをサポートするために、各レベルでは異なるリソース機能が提供されています。 最初は月数ドルの小規模データベースでアプリを構築し、後から実際のソリューションのニーズに応じて、スケールを調整することができます。 動的なスケーラビリティにより、データベースは変化の激しいリソース要件に透過的に対処することができます。 必要なときに必要な分のリソースにのみ課金されます。 詳しくは、 [価格レベル](concepts-pricing-tiers.md)に関するページをご覧ください。

## <a name="azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL - Hyperscale (Citus)
Hyperscale (Citus) オプションでは、シャーディングを使用して複数のマシン間でクエリを水平にスケーリングします。 そのクエリ エンジンにより、大規模なデータセットに対する応答を高速化するために、これらのサーバー間で受信 SQL クエリが並列処理されます。 より大きいスケールとパフォーマンスを必要とするアプリケーション (通常は、100 GB のデータに近づいているか、または既に超えているワークロード) に対して、サービスを提供します。

Hyperscale (Citus) デプロイ オプションでは、次のことを実現できます。

- シャーディングを使用した複数のマシン間での水平スケーリング
- 大規模なデータセットに対する応答を迅速化するため、これらのサーバー間でクエリを並列処理
- マルチテナント アプリケーション、リアルタイムの運用分析、および高スループットのトランザクション ワークロードに対する優れたサポート

PostgreSQL 向けにビルドされたアプリケーションでは、標準の[接続ライブラリ](./concepts-connection-libraries.md)と最小限の変更により、Hyperscale (Citus) に対して分散クエリを実行できます。

## <a name="contacts"></a>連絡先
Azure Database for PostgreSQL についての質問やご提案は、Azure Database for PostgreSQL チームにメール ([@Ask Azure DB for PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)) でお送りください。 このアドレスは、サポート チケットではなく一般的な質問用です。

また、必要に応じて次の連絡先をご利用ください。
- Azure サポートに問い合わせるか、アカウントに関する問題を修正するには、[Azure portal からチケットを申請します](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
- フィードバックを提供したり、新しい機能を要求したりするには、[UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql) でエントリを作成します。

## <a name="next-steps"></a>次のステップ
- コストの比較と計算については、[価格のページ](https://azure.microsoft.com/pricing/details/postgresql/)を参照してください。 Single Server と Hyperscale (Citus) の両方のデプロイ オプションで、前払いの予約インスタンス割引も提供されます。詳細については、[Single Server RI の価格](concept-reserved-pricing.md)および [Hyperscale RI の価格](concepts-hyperscale-reserved-pricing.md)に関するページを参照してください。
- まず、最初の Azure Database for PostgreSQL [Single Server](./quickstart-create-server-database-portal.md) または [Hyperscale (Citus)](./quickstart-create-hyperscale-portal.md) を作成してください。
- 初めてのアプリを Python、PHP、Ruby、C\#、Java、Node.js で作成します:[接続ライブラリ](./concepts-connection-libraries.md)
