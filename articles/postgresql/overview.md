---
title: Azure Database for PostgreSQL リレーショナル データベース サービスの概要
description: Azure Database for PostgreSQL リレーショナル データベース サービスについて概説します。
author: rachel-msft
ms.author: raagyema
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 11/14/2018
ms.openlocfilehash: 318778a83c82b0ddb88f8bbd852442ab389fedb3
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352259"
---
# <a name="what-is-azure-database-for-postgresql"></a>Azure Database for PostgreSQL とは

Azure Database for PostgreSQL は、オープンソースの [PostgreSQL](https://www.postgresql.org/) データベース エンジン バージョン 9.5、9.6、および 10 のコミュニティ バージョンに基づいて開発者向けに構築された、Microsoft Cloud のリレーショナル データベース サービスです。 Azure Database for PostgreSQL には、次の特長があります。

- 追加コストなしの組み込みの高可用性
- 包括的な従量課金制の料金を使用した、予測可能なパフォーマンス
- 必要に応じて数秒以内にスケーリング
- 保存中や移動中の機密データのキュリティ保護
- 自動バックアップと最大 35 日間のポイントインタイム リストア
- エンタープライズグレードのセキュリティとコンプライアンス

これらすべての機能は、ほとんど管理を必要とせず、いずれも追加費用なしで利用することができます。 これらの機能を使用すると、貴重な時間とリソースを仮想マシンとインフラストラクチャの管理に奪われることなく、迅速なアプリケーション開発や、製品化に要する時間の短縮化に専念することができます。 加えて、オープンソースのツールとプラットフォームを自由に選んでアプリケーションを開発し続けることができるので、新たにスキルを身に付けなくても、ビジネスの現場で必要となるスピードと効率を高めることができます。 

この記事では、パフォーマンス、スケーラビリティ、管理容易性に関連する、Azure Database for PostgreSQL の中心概念と機能について紹介します。 すぐに始めるには、次のクイック スタートをご覧ください。

- [Azure Portal を使用した Azure Database for PostgreSQL の作成](quickstart-create-server-database-portal.md)
- [Azure CLI を使用した Azure Database for PostgreSQL の作成](quickstart-create-server-database-azure-cli.md)

Azure CLI の各種サンプルについては、次のページを参照してください。

- [Azure Database for PostgreSQL 用 Azure CLI サンプル](./sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>数秒以内でのパフォーマンスの調整とスケール
Azure Database for PostgreSQL サービスでは 3 つの価格レベルが提供されています:Basic、汎用、メモリ最適化。 使用しているデータベースのワークロードをサポートするために、各レベルでは異なるリソース機能が提供されています。 最初は月数ドルの小規模データベースでアプリを構築し、後から実際のソリューションのニーズに応じて、スケールを調整することができます。 動的なスケーラビリティにより、データベースは変化の激しいリソース要件に透過的に対処することができます。 必要なときに必要な分のリソースにのみ課金されます。 詳しくは、 [価格レベル](concepts-pricing-tiers.md)に関するページをご覧ください。

## <a name="monitoring-and-alerting"></a>監視とアラート
スケールを調整するときに、適切なパフォーマンス レベルはどのようにして見極めればよいのでしょうか。 組み込みの Azure 監視機能とアラート機能を使用します。 これらのツールを使用すると、現在または今後のパフォーマンスまたはストレージのニーズに基づいて、スケールアップまたはスケールダウンの影響をすばやく評価できます。 詳細については、[アラート](howto-alert-on-metric.md)に関するページを参照してください。

## <a name="keep-your-app-and-business-running"></a>アプリケーションとビジネスの継続的な稼働
Microsoft が管理するデータセンターのグローバル ネットワークによって強化された、Azure の業界をリードする可用性 99.99% のサービス レベル アグリーメント (SLA) により、アプリケーションの 24 時間 365 日の継続的な稼働が可能になります。 すべての Azure Database for PostgreSQL サーバーで、組み込みのセキュリティ、フォールト トレランス、データ保護を利用できます。これらは、本来なら自身で購入または設計し、構築、管理する必要があります。 Azure Database for PostgreSQL では、価格レベルごとに、包括的な一連のビジネス継続性に関する機能とオプションが用意されており、これらを使用すると、稼働させてその状態を維持することができます。 [ポイントインタイム リストア](howto-restore-server-portal.md)を使用すると、データベースを 35 日間分さかのぼって以前の状態に戻すことができます。 さらに、データベースをホストしているデータセンターで障害が発生した場合は、最新のバックアップの geo 冗長コピーからデータベースを復元することができます。

## <a name="secure-your-data"></a>データのセキュリティ保護
Azure データベース サービスは、アクセスの制限、保存データと移動中のデータの保護、監視アクティビティの支援を行う機能を備えた Azure Database for PostgreSQL によって、データ セキュリティを維持してきました。 Azure のプラットフォーム セキュリティについては、[Azure セキュリティ センター](https://azure.microsoft.com/overview/trusted-cloud/)をご覧ください。

Azure Database for PostgreSQL サービスは、保存データにストレージ暗号化を使用し、FIPS 140-2 に準拠しています。 バックアップを含むデータはディスク上で暗号化されます (ただし、クエリの実行中にエンジンによって作成された一時ファイルを除きます)。 このサービスでは、Azure ストレージ暗号化に含まれる AES 256 ビット暗号が使用され、キーはシステムによって管理されます。 ストレージの暗号化は常にオンになっており、無効にすることはできません。

Azure Database for PostgreSQL サービスは、既定で、ネットワーク上で転送されるデータに [SSL 接続セキュリティ](./concepts-ssl-connection-security.md)を要求するように構成されます。 データベース サーバーとクライアント アプリケーション間に SSL 接続を適用すると、サーバーとアプリケーション間のデータ ストリームが暗号化されて、"man in the middle" 攻撃から保護されます。 クライアント アプリケーションが SSL 接続をサポートしていない場合は、必要に応じて、データベース サービスに接続するための SSL 要求を無効にできます。

## <a name="contacts"></a>連絡先
Azure Database for PostgreSQL についての質問や提案は、Azure Database for PostgreSQL チームにメール ([@Ask Azure DB for PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)) でお送りください。 これはテクニカル サポートの別名ではないことに注意してください。

さらに、適切な連絡先について次の点を考慮してください。
- Azure サポートに問い合わせる場合は、[Azure portal からチケットを申請します](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
- アカウントを使用して問題を修正するには、Azure Portal で[サポート要求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を提出します。
- フィードバックを提供したり、新しい機能を要求したりするには、[UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql) でエントリを作成します。

## <a name="next-steps"></a>次の手順
- コストの比較と計算については、[価格のページ](https://azure.microsoft.com/pricing/details/postgresql/)を参照してください。
- 実際に[初めての Azure Database for PostgreSQL を作成](./quickstart-create-server-database-portal.md)してみましょう。
- 初めてのアプリを Python、PHP、Ruby、C\#、Java、Node.js で作成します:[接続ライブラリ](./concepts-connection-libraries.md)
