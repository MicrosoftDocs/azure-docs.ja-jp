---
title: Azure Database for PostgreSQL リレーショナル データベース サービスの概要
description: Azure Database for PostgreSQL リレーショナル データベース サービスについて概説します。
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 05/06/2019
ms.openlocfilehash: 5194cf51fd7f1debeba76edb48e8377919ae448a
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177965"
---
# <a name="what-is-azure-database-for-postgresql"></a>Azure Database for PostgreSQL とは
Azure Database for PostgreSQL は、開発者向けに構築された、Microsoft クラウドのリレーショナル データベース サービスです。 これは、コミュニティ バージョンのオープンソースの [PostgreSQL](https://www.postgresql.org/) データベース エンジンに基づいており、2 つのデプロイ オプション: Single Server と Hyperscale (Citus) (プレビュー) で利用できます。

## <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - Single Server
Single Server デプロイ オプションでは、次のことを実現できます。

- 追加コストなしの組み込みの高可用性 (99.99% SLA)
- 包括的な従量課金制の料金を使用した、予測可能なパフォーマンス
- 必要に応じて数秒以内に垂直スケーリング
- スケーリングの影響をすばやく評価するための監視とアラート
- 保存中や移動中の機密データのキュリティ保護
- 自動バックアップと最大 35 日間のポイントインタイム リストア
- エンタープライズグレードのセキュリティとコンプライアンス

これらすべての機能は、ほとんど管理を必要とせず、いずれも追加費用なしで利用することができます。 これらにより、貴重な時間とリソースを仮想マシンとインフラストラクチャの管理に費やすことなく、迅速なアプリケーション開発や、製品化に要する時間の短縮に専念することができます。 新しいスキルを身に付けなくても、オープンソースのツールとプラットフォームを自由に選んでアプリケーションの開発を続けることができます。

Single Server デプロイ オプションでは、次の 3 つの価格レベルが提供されます。Basic、汎用、メモリ最適化。 使用しているデータベースのワークロードをサポートするために、各レベルでは異なるリソース機能が提供されています。 最初は月数ドルの小規模データベースでアプリを構築し、後から実際のソリューションのニーズに応じて、スケールを調整することができます。 動的なスケーラビリティにより、データベースは変化の激しいリソース要件に透過的に対処することができます。 必要なときに必要な分のリソースにのみ課金されます。 詳しくは、 [価格レベル](concepts-pricing-tiers.md)に関するページをご覧ください。

## <a name="azure-database-for-postgresql---hyperscale-citus-preview"></a>Azure Database for PostgreSQL - Hyperscale (Citus) (プレビュー)
Hyperscale (Citus) オプションでは、シャーディングを使用して複数のマシン間でクエリを水平にスケーリングします。 そのクエリ エンジンにより、大規模なデータセットに対する応答を高速化するために、これらのサーバー間で受信 SQL クエリが並列処理されます。 より大きいスケールとパフォーマンスを必要とするアプリケーション (通常は、100 GB のデータに近づいているか、または既に超えているワークロード) に対して、サービスを提供します。

Hyperscale (Citus) デプロイ オプションでは、次のことを実現できます。

- シャーディングを使用した複数のマシン間での水平スケーリング
- 大規模なデータセットに対する応答を迅速化するため、これらのサーバー間でクエリを並列処理
- マルチテナント アプリケーション、リアルタイムの運用分析、および高スループットのトランザクション ワークロードに対する優れたサポート

PostgreSQL 向けにビルドされたアプリケーションでは、標準の[接続ライブラリ](./concepts-connection-libraries.md)と最小限の変更により、Hyperscale (Citus) に対して分散クエリを実行できます。

Hyperscale (Citus) はパブリック プレビュー段階であるため、SLA はまだ提供されていない点に注意してください。

## <a name="data-security"></a>データのセキュリティ
Azure Database for PostgreSQL では、Azure データベース サービスの従来のデータ セキュリティが維持されています。 アクセスの制限、保存データと移動中のデータの保護、監視アクティビティの支援を行う機能を備えいます。 Azure のプラットフォーム セキュリティについては、[Azure セキュリティ センター](https://azure.microsoft.com/overview/trusted-cloud/)をご覧ください。

Azure Database for PostgreSQL サービスでは、保存データのストレージ暗号化に FIPS 140-2 認証済みの暗号モジュールが使用されます。 バックアップを含むデータはディスク上で暗号化されます (ただし、クエリの実行中に作成された一時ファイルを除きます)。 このサービスでは、Azure ストレージ暗号化に含まれる AES 256 ビット暗号が使用され、キーはシステムによって管理されます。 ストレージの暗号化は常にオンになっており、無効にすることはできません。 既定で、Azure Database for PostgreSQL サービスでは、ネットワーク経由の移動データ、およびデータベースとクライアント アプリケーション間の移動データに対して、セキュリティで保護された接続が必要です。

## <a name="contacts"></a>連絡先
Azure Database for PostgreSQL についての質問やご提案は、Azure Database for PostgreSQL チームにメール ([@Ask Azure DB for PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)) でお送りください。 このアドレスは、サポート チケットではなく一般的な質問用です。

また、必要に応じて次の連絡先をご利用ください。
- Azure サポートに問い合わせるか、アカウントに関する問題を修正するには、[Azure portal からチケットを申請します](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
- フィードバックを提供したり、新しい機能を要求したりするには、[UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql) でエントリを作成します。

## <a name="next-steps"></a>次の手順
- コストの比較と計算については、[価格のページ](https://azure.microsoft.com/pricing/details/postgresql/)を参照してください。
- 実際に初めての Azure Database for PostgreSQL [Single Server](./quickstart-create-server-database-portal.md) または [Hyperscale (Citus) (プレビュー)](./quickstart-create-hyperscale-portal.md) を作成してみましょう。
- 初めてのアプリを Python、PHP、Ruby、C\#、Java、Node.js で作成します:[接続ライブラリ](./concepts-connection-libraries.md)
