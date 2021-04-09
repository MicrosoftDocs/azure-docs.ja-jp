---
title: 概要 - Azure Database for MySQL
description: MySQL Community Edition をベースとした、Microsoft クラウドのリレーショナル データベース サービス、Azure Database for MySQL サービスについて説明します。
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 3/18/2020
ms.openlocfilehash: 20b54afdfbb5641def921ffc17b3c38f6650acab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101736221"
---
# <a name="what-is-azure-database-for-mysql"></a>Azure Database for MySQL とは -

Azure Database for MySQL は、Microsoft クラウドにおける、(GPLv2 ライセンスで利用可能な) [MySQL Community Edition](https://www.mysql.com/products/community/) のデータベース エンジン バージョン 5.6、5.7、および 8.0 をベースとしたリレーショナル データベース サービスです。 Azure Database for MySQL には、次の特長があります。

- 組み込みの高可用性。
- 自動バックアップとポイントインタイム リストアを使用したデータ保護 (最大 35 日間)。
- 基盤となるハードウェア、オペレーティング システム、データベース エンジンのサービスをセキュリティで保護し、最新の状態に保つための自動メンテナンス。
- 包括的な従量課金制の料金を使用した、予測可能なパフォーマンス。
- 秒単位のエラスティック スケーリング。
- サーバーを停止/起動する機能が備わったコスト最適化制御。 
- 保存および移動中の機密データを保護するエンタープライズ レベルのセキュリティと業界をリードするコンプライアンス。
- 大規模なデプロイの管理と監視を容易にする監視と自動化。
- 業界をリードするサポート エクスペリエンス。

これらの機能は、ほとんど管理を必要とせず、いずれも追加費用なしで利用することができます。 そのため貴重な時間とリソースを仮想マシンとインフラストラクチャの管理に奪われることなく、迅速なアプリ開発や、製品化に要する時間の短縮化に専念することができます。 加えて、オープン ソースのツールとプラットフォームを自由に選んでアプリケーションを開発し続けることができるので、新たにスキルを身に付けなくても確実に、ビジネスの現場で必要となるスピードと効率を高めることができます。

:::image type="content" source="media/overview/1-azure-db-for-mysql-conceptual-diagram.png" alt-text="Azure Database for MySQL の概念図":::

## <a name="deployment-models"></a>デプロイ モデル

MySQL コミュニティ エディションを搭載した Azure Database for MySQL は、次の 2 つのデプロイ モードで利用できます。
- シングル サーバー 
- フレキシブル サーバー (プレビュー)
  
### <a name="azure-database-for-mysql---single-server"></a>Azure Database for MySQL - 単一サーバー

Azure Database for MySQL 単一サーバーは、最小限の要件でデータベースをカスタマイズできる、完全に管理されたデータベースサービスです。 単一サーバー プラットフォームは、修正プログラムの適用、バックアップ、高可用性、最小限のユーザー構成と制御によるセキュリティなど、データベース管理機能のほとんどを処理するよう設計されています。 このアーキテクチャは、単一の可用性ゾーンで 99.99% の可用性を備えた組み込みの高可用性を実現するよう最適化されています。 MySQL 5.6、5.7、および 8.0 のコミュニティ バージョンをサポートしています。 このサービスは現時点で一般提供されており、さまざまな [Azure リージョン](https://azure.microsoft.com/global-infrastructure/services/)で利用できます。

Single Server デプロイ オプションでは、次の 3 つの価格レベルが提供されます。Basic、汎用、メモリ最適化。 使用しているデータベースのワークロードをサポートするために、各レベルでは異なるリソース機能が提供されています。 最初は月数ドルの小規模データベースでアプリを構築し、後から実際のソリューションのニーズに応じて、スケールを調整することができます。 動的なスケーラビリティにより、データベースは変化の激しいリソース要件に透過的に対処することができます。 必要なときに必要な分のリソースにのみ課金されます。 詳細については、[価格レベル](concepts-pricing-tiers.md)に関するページを参照してください。

単一サーバーは、修正プログラムの適用スケジュールや MySQL のカスタム構成の設定を細かく制御する必要がない、自動化された修正プログラムの適用を処理するように設計された、クラウド ネイティブ アプリケーションに最適です。 

単一サーバー デプロイ モードの詳細については、[単一サーバーの概要](single-server-overview.md)を参照してください。

### <a name="azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL - フレキシブル サーバー (プレビュー)

Azure Database for MySQL フレキシブル サーバーは、データベース管理機能と構成設定のよりきめ細かな制御と柔軟性を提供するように設計されたフル マネージド データベース サービスです。 一般に、サービスでは、ユーザーの要件に基づいて、より高い柔軟性とカスタマイズが提供されます。 フレキシブル サーバー アーキテクチャにより、ユーザーは単一の可用性ゾーン内および複数の可用性ゾーンにまたがる高可用性を選択できます。 また、フレキシブル サーバーでは、より優れたコスト最適化制御によって、サーバーを停止/起動する機能や、完全なコンピューティング能力を継続的には必要としないワークロードに最適な、バースト可能なコンピューティング層を実現できます。 サービスでは、MySQL 5.7 と 8.0 のコミュニティ バージョンがサポートされています。 このサービスは現在パブリック プレビュー段階にあり、現時点ではさまざまな [Azure リージョン](https://azure.microsoft.com/global-infrastructure/services/)で利用できます。

フレキシブル サーバーは、以下の場合に最適です。 
- より優れた制御とカスタマイズが必要なアプリケーション開発。
- ゾーン冗長の高可用性
- マネージド メンテナンス期間

フレキシブル サーバー デプロイ モードの詳細については、[フレキシブル サーバーの概要](flexible-server/overview.md)を参照してください。

## <a name="contacts"></a>連絡先
Azure Database for MySQL についての質問や提案は、Azure Database for MySQL チームにメール ([@Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)) でお送りください。 このメール アドレスはテクニカル サポートのエイリアスではありません。

さらに、適切な連絡先について次の点を考慮してください。

- Azure サポートに問い合わせる場合は、[Azure portal からチケットを申請します](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
- アカウントを使用して問題を修正するには、Azure Portal で[サポート要求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を提出します。
- フィードバックを提供したり、新しい機能を要求したりするには、[UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql) でエントリを作成します。

## <a name="next-steps"></a>次のステップ

Azure Database for MySQL の 2 つのデプロイ モードの詳細を確認し、ニーズに応じて適切なオプションを選択してください。

- [単一サーバー](single-server/index.yml)
- [フレキシブル サーバー](flexible-server/index.yml)
- [ワークロードに適した MySQL のデプロイ オプションを選択する](select-right-deployment-type.md)
