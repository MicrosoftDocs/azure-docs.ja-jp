---
title: 概要 - Azure Database for MariaDB
description: MySQL Community Edition をベースとした、Microsoft クラウドのリレーショナル データベース サービス、Azure Database for MariaDB サービスについて説明します。
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: overview
ms.custom: mvc
ms.date: 3/18/2020
ms.openlocfilehash: 8115625099543d378728a6313a8cc4c95fec0cd2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98662019"
---
# <a name="what-is-azure-database-for-mariadb"></a>Azure Database for MariaDB とは

Azure Database for MariaDB は、Microsoft Cloud のリレーショナル データベース サービスです。 Azure Database for MariaDB は、[MariaDB コミュニティ エディション](https://mariadb.org/download/) (GPLv2 ライセンスで入手可能) のデータベース エンジン バージョン 10.2 および 10.3 を基にしています。

Azure Database for MariaDB では、次のことを実現できます。

- 追加コストなしの組み込みの高可用性。
- 包括的な従量課金制の料金を使用した、予測可能なパフォーマンス。
- 必要に応じて、数秒以内でスケーリング。
- 保存中および転送中の機密データのセキュリティ保護。
- 自動バックアップと最大 35 日間のポイントインタイム リストア。
- エンタープライズグレードのセキュリティとコンプライアンス。

これらの機能は、ほとんど管理を必要とせず、 追加費用なしで利用することができます。 Azure Database for MariaDB を使用すれば、アプリ開発を迅速化し、製品化にかかる期間を短縮することができます。 仮想マシンとインフラストラクチャの管理に貴重な時間とリソースを費やす必要はありません。 また、引き続きオープンソースの任意のツールとプラットフォームを使用してアプリケーションを開発できます。 新たにスキルを身に付けることなく、ビジネスで要求されるスピードと効率を実現することができます。

パフォーマンス、スケーラビリティ、管理の容易性など、Azure Database for MariaDB の主要な概念と機能について詳しくは、以下のクイック スタートを参照してください。

- [Azure portal を使用した Azure Database for MariaDB サーバーの作成](quickstart-create-mariadb-server-database-using-azure-portal.md)
- [Azure CLI を使用して Azure Database for MariaDB サーバーを作成する](quickstart-create-mariadb-server-database-using-azure-cli.md)

<!--
For a set of Azure CLI samples, see:
- [Azure CLI samples for Azure Database for MariaDB](sample-scripts-azure-cli.md) 
-->

## <a name="adjust-performance-and-scale-within-seconds"></a>数秒以内でのパフォーマンスの調整とスケール

Azure Database for MariaDB サービスには、複数のサービス レベル (Basic、汎用、およびメモリ最適化) が用意されています。 軽量のデータベース ワークロードから重量のデータベース ワークロードに至るまでサポートできるように、レベルごとに異なるパフォーマンスと機能 を提供しています。 最初は月数ドルの小規模データベースでアプリを構築し、後から実際のソリューションのニーズに応じて、スケールを調整することができます。 動的なスケーラビリティにより、データベースは変化の激しいリソース要件に透過的に対処することができます。 必要なときに必要な分のリソースにのみ課金されます。 詳細については、[価格レベル](concepts-pricing-tiers.md)に関するページを参照してください。

## <a name="monitoring-and-alerting"></a>監視とアラート

スケールアップまたはスケールダウンするときに、適切なパフォーマンス レベルはどのようにして見極めればよいのでしょうか。 Azure Database for MariaDB の組み込みのパフォーマンス監視機能およびアラート機能と、仮想コアに基づくパフォーマンス評価とを組み合わせて使用できます。 これらのツールを使用すると、現在または今後のパフォーマンスのニーズに基づいて、仮想コアのスケールアップまたはスケールダウンの効果をすばやく評価することができます。 詳細については、[アラート](howto-alert-metric.md)に関するページを参照してください。

## <a name="keep-your-app-and-business-running"></a>アプリケーションとビジネスの継続的な稼働

Azure の業界をリードする可用性 99.99% の SLA は、Microsoft が管理するデータセンターのグローバル ネットワークによって実現されています。 このネットワークが、24 時間 365 日の継続的なアプリの稼働を支えています。 Azure Database for MariaDB の強みである組み込みセキュリティ、フォールト トレランス、データ保護を活かしましょう。 Azure Database for MariaDB では、ポイントインタイム リストアを使用して、サーバーを 35 日間分さかのぼって以前の状態に戻すことができます。

## <a name="secure-your-data"></a>データのセキュリティ保護

Azure データベース サービスは、Azure Database for MariaDB によって、データ セキュリティを維持してきました。 アクセスの制限、保存データと移動中のデータの保護、監視アクティビティの支援を行う機能が Azure Database for MariaDB には備わっています。 Azure のプラットフォーム セキュリティについては、[Azure セキュリティ センター](https://www.microsoft.com/trustcenter/security)をご覧ください。 Azure Database for MariaDB のセキュリティ機能の詳細については、[セキュリティの概要](concepts-security.md)に関するページを参照してください。

## <a name="contacts"></a>連絡先

Azure Database for MariaDB についての質問や提案は、(テクニカル サポート エイリアスではなく) [Azure Database for MariaDB チーム](mailto:AskAzureDBforMariaDB@service.microsoft.com)にお送りください。

次の連絡先もご利用いただけます。
- Azure サポートに連絡するには、Azure portal で[サポート要求をオープン](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)します。
- ご利用のアカウントに関する問題を解決するには、Azure portal で[サポート要求をオープン](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)します。
- フィードバックを提供したり、新しい機能を要求したりするには、[Azure フィードバック フォーラム](https://feedback.azure.com/forums/915439-azure-database-for-mariadb)でエントリを作成します。

## <a name="next-steps"></a>次の手順

Azure Database for MariaDB の概要を読み終えたら、次の作業に進むことができます。
- コストの比較と計算については、[価格](https://azure.microsoft.com/pricing/details/mariadb/)のページを参照してください。 
- [初めてのサーバーを作成](quickstart-create-mariadb-server-database-using-azure-portal.md)してみましょう。

<!--- - Build your first app using your preferred language: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md) --->
