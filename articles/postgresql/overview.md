---
title: Azure Database for PostgreSQL とは
description: フレキシブル サーバーのコンテキストにおける Azure Database for PostgreSQL リレーショナル データベース サービスについて概説します。
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: c3ea7930f41fe89538a817da032e993e534db9cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92491327"
---
# <a name="what-is-azure-database-for-postgresql"></a>Azure Database for PostgreSQL とは

Azure Database for PostgreSQL は、Microsoft クラウドにおける、(GPLv2 ライセンスで利用可能な) [PostgreSQL Community Edition](https://www.postgresql.org/) のデータベース エンジンをベースとしたリレーショナル データベース サービスです。 Azure Database for PostgreSQL には、次の特長があります。

- 組み込みの高可用性。
- 自動バックアップとポイントインタイム リストアを使用したデータ保護 (最大 35 日間)。
- 基盤となるハードウェア、オペレーティング システム、データベース エンジンのサービスをセキュリティで保護し、最新の状態に保つための自動メンテナンス。
- 包括的な従量課金制の料金を使用した、予測可能なパフォーマンス。
- 秒単位のエラスティック スケーリング。
- 保存および移動中の機密データを保護するエンタープライズ レベルのセキュリティと業界をリードするコンプライアンス。
- 大規模なデプロイの管理と監視を容易にする監視と自動化。
- 業界をリードするサポート エクスペリエンス。

 :::image type="content" source="./media/overview/overview-what-is-azure-postgres.png" alt-text="Azure Database for PostgreSQL":::

これらの機能は、ほとんど管理を必要とせず、いずれも追加費用なしで利用することができます。 そのため貴重な時間とリソースを仮想マシンとインフラストラクチャの管理に奪われることなく、迅速なアプリケーション開発や、製品化に要する時間の短縮化に専念することができます。 加えて、オープン ソースのツールとプラットフォームを自由に選んでアプリケーションを開発し続けることができるので、新たにスキルを身に付けなくても確実に、ビジネスの現場で必要となるスピードと効率を高めることができます。

## <a name="deployment-models"></a>デプロイ モデル

PostgreSQL コミュニティ エディションを搭載した Azure Database for PostgreSQL は、次の 3 つのデプロイ モードで利用できます。

- シングル サーバー
- フレキシブル サーバー (プレビュー)
- Hyperscale (Citus)

### <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - Single Server

Azure Database for PostgreSQL 単一サーバーは、最小限の要件でデータベースをカスタマイズできる、完全に管理されたデータベースサービスです。 単一サーバー プラットフォームは、修正プログラムの適用、バックアップ、高可用性、最小限のユーザー構成と制御によるセキュリティなど、データベース管理機能のほとんどを処理するよう設計されています。 このアーキテクチャは、単一の可用性ゾーンで 99.99% の可用性を備えた組み込みの高可用性を実現するよう最適化されています。 PostgreSQL 9.5、9.6、10、および 11 のコミュニティ バージョンをサポートしています。 このサービスは現時点で一般公開されており、さまざまな [Azure リージョン](https://azure.microsoft.com/global-infrastructure/services/)で利用できます。

Single Server デプロイ オプションでは、次の 3 つの価格レベルが提供されます。Basic、汎用、メモリ最適化。 使用しているデータベースのワークロードをサポートするために、各レベルでは異なるリソース機能が提供されています。 最初は月数ドルの小規模データベースでアプリを構築し、後から実際のソリューションのニーズに応じて、スケールを調整することができます。 動的なスケーラビリティにより、データベースは変化の激しいリソース要件に透過的に対処することができます。 必要なときに必要な分のリソースにのみ課金されます。 詳細については、[価格レベル](./concepts-pricing-tiers.md)に関するページを参照してください。

単一サーバーは、修正プログラムの適用スケジュールや PostgreSQL のカスタム構成の設定を細かく制御する必要がない、自動化された修正プログラムの適用を処理するように設計された、クラウド ネイティブ アプリケーションに最適です。

単一サーバー デプロイ モードの詳細については、[単一サーバーの概要](./overview-single-server.md)を参照してください。

### <a name="azure-database-for-postgresql---flexible-server-preview"></a>Azure Database for PostgreSQL - フレキシブル サーバー (プレビュー)

Azure Database for PostgreSQL フレキシブル サーバーは、データベース管理機能と構成設定のよりきめ細かな制御と柔軟性を提供するように設計されたフル マネージド データベース サービスです。 一般に、サービスでは、ユーザーの要件に基づいて、より高い柔軟性とカスタマイズが提供されます。 フレキシブル サーバー アーキテクチャにより、ユーザーは単一の可用性ゾーン内および複数の可用性ゾーンにまたがる高可用性を選択できます。 また、フレキシブル サーバーでは、より優れたコスト最適化制御によって、サーバーを停止/起動する機能や、完全なコンピューティング能力を継続的には必要としないワークロードに最適な、バースト可能なコンピューティング層を実現できます。 現在このサービスでは PostgreSQL 11 および 12 のコミュニティ バージョンがサポートされており、近日中により新しいバージョンを追加する予定です。 このサービスは現在パブリック プレビュー段階にあり、現時点ではさまざまな Azure リージョンで利用できます。

フレキシブル サーバーは、以下の場合に最適です。

- より適切な制御とカスタマイズが必要なアプリケーション開発。
- サーバーを停止/起動する機能が備わったコスト最適化制御。
- ゾーン冗長の高可用性
- マネージド メンテナンス期間
  
フレキシブル サーバー デプロイ モードの詳細については、[フレキシブル サーバーの概要](./flexible-server/overview.md)を参照してください。

### <a name="azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL - Hyperscale (Citus)

Hyperscale (Citus) オプションでは、シャーディングを使用して複数のマシン間でクエリを水平にスケーリングします。 そのクエリ エンジンにより、大規模なデータセットに対する応答を高速化するために、これらのサーバー間で受信 SQL クエリが並列処理されます。 より大きいスケールとパフォーマンスを必要とするアプリケーション (通常は、100 GB のデータに近づいているか、または既に超えているワークロード) に対して、サービスを提供します。

Hyperscale (Citus) デプロイ オプションでは、次のことを実現できます。

- シャーディングを使用した複数のマシン間での水平スケーリング
- 大規模なデータセットに対する応答を迅速化するため、これらのサーバー間でクエリを並列処理
- マルチテナント アプリケーション、リアルタイムの運用分析、および高スループットのトランザクション ワークロードに対する優れたサポート
  
PostgreSQL 向けにビルドされたアプリケーションでは、標準の[接続ライブラリ](./concepts-connection-libraries.md)と最小限の変更により、Hyperscale (Citus) に対して分散クエリを実行できます。

## <a name="next-steps"></a>次の手順

Azure Database for PostgreSQL の 3 つのデプロイ モードの詳細を確認し、ニーズに応じて適切なオプションを選択してください。

- [単一サーバー](./overview-single-server.md)
- [フレキシブル サーバー](./flexible-server/overview.md)
- Hyperscale (Citus)