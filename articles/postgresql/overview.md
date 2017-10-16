---
title: "Azure Database for PostgreSQL リレーショナル データベース サービスの概要 | Microsoft Docs"
description: "Azure Database for PostgreSQL リレーショナル データベース サービスについて概説します。"
services: postgresql
author: kamathsun
ms.author: sukamat
manager: jhubbard
editor: jasonwhowell
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 08/01/2017
ms.openlocfilehash: 0dba4db0ae62a4f7e8440e80e830e5f64f5b49e0
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2017
---
# <a name="what-is-azure-database-for-postgresql"></a>Azure Database for PostgreSQL とは

Azure Database for PostgreSQL は、オープン ソースの [PostgreSQL](https://www.postgresql.org/) データベース エンジンのコミュニティ バージョンに基づいて開発者向けに構築された、Microsoft Cloud のリレーショナル データベース サービスです。 このサービスは、パブリック プレビューの段階です。 Azure Database for PostgreSQL には、次の特長があります。
- さまざまなサービス レベルで予測可能なパフォーマンス
- アプリケーションのダウンタイムを伴わない動的スケーラビリティ
- 組み込みの高可用性
- データ保護

これらすべての機能は、ほとんど管理を必要とせず、いずれも追加費用なしで利用することができます。 これらの機能を使用すると、貴重な時間とリソースを仮想マシンとインフラストラクチャの管理に奪われることなく、迅速なアプリケーション開発や、製品化に要する時間の短縮化に専念することができます。 加えて、オープン ソースのツールとプラットフォームを自由に選んでアプリケーションを開発し続けることができるので、新たにスキルを身に付けなくても、ビジネスの現場で必要となるスピードと効率を高めることができます。 

この記事では、パフォーマンス、スケーラビリティ、管理容易性に関連する、Azure Database for PostgreSQL の中心概念と機能について紹介します。 すぐに始めるには、次のクイック スタートをご覧ください。

- [Azure Portal を使用した Azure Database for PostgreSQL の作成](quickstart-create-server-database-portal.md)
- [Azure CLI を使用した Azure Database for PostgreSQL の作成](quickstart-create-server-database-azure-cli.md)

Azure CLI と PowerShell の各種サンプルについては、以下のページをご覧ください。

- [Azure Database for PostgreSQL 用 Azure CLI サンプル](./sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-without-downtime"></a>ダウンタイムなしでのパフォーマンスの調整とスケール

Azure Database for PostgreSQL サービスには現在、Basic と Standard の 2 つのサービス レベルがあります。 軽量のデータベース ワークロードから重量のデータベース ワークロードに至るまでサポートできるように、サービス レベルごとに[異なるレベルのパフォーマンス、IOPS 保証、機能](concepts-service-tiers.md)を提供しています。 最初は月数ドルの小規模サーバーでアプリを構築し、後でソリューションのニーズに合わせていつでも、手動またはプログラムでサービス レベルの[パフォーマンス レベルを変更](scripts/sample-scale-server-up-or-down.md)することができます。 その際に、アプリケーションにも顧客にもダウンタイムは発生しません。 動的なスケーラビリティにより、データベースは変化の激しいリソース要件に透過的に対処することができ、必要なときに必要な分のリソースにのみ課金されます。

## <a name="monitoring-and-alerting"></a>監視とアラート
スケールを調整するときに、適切なパフォーマンス レベルはどのようにして見極めればよいのでしょうか。 組み込みのパフォーマンス監視機能およびアラート機能と、コンピューティング ユニットに基づくパフォーマンス評価とを組み合わせて使用してください。 これらのツールを使用すると、現在または今後のパフォーマンスのニーズに基づいて、コンピューティング ユニットのスケールアップまたはスケールダウンの影響をすばやく評価することができます。 詳細については、「[Azure Database for PostgreSQL options and performance: Understand what's available in each service tier (Azure Database for PostgreSQL のオプションとパフォーマンス: 各サービス レベルで使用できる内容について理解する)](./concepts-service-tiers.md)」を参照してください。

## <a name="keep-your-app-and-business-running"></a>アプリケーションとビジネスの継続的な稼働
Microsoft が管理するデータセンターのグローバル ネットワークによって強化された、Azure の業界をリードする可用性 99.99% (プレビューでは利用できません) のサービス レベル アグリーメント (SLA) により、アプリケーションの 24 時間 365 日の継続的な稼働が可能になります。 すべての Azure Database for PostgreSQL サーバーで、組み込みのセキュリティ、フォールト トレランス、データ保護を利用できます。これらは、本来なら自身で購入または設計し、構築、管理する必要があります。 Azure Database for PostgreSQL では、サービス レベルごとに、包括的な一連のビジネス継続性に関する機能とオプションが用意されており、これらを使用すると、稼働させてその状態を維持することができます。 [ポイントインタイム リストア](howto-restore-server-portal.md)を使用すると、データベースを 35 日間分さかのぼって以前の状態に戻すことができます。 さらに、データベースをホストしているデータセンターで障害が発生した場合は、最新のバックアップの geo 冗長コピーからデータベースを復元することができます。

## <a name="secure-your-data"></a>データのセキュリティ保護
Azure データベース サービスは、アクセスの制限、保存データと移動中のデータの保護、監視アクティビティの支援を行う機能を備えた Azure Database for PostgreSQL によって、データ セキュリティを維持してきました。 Azure のプラットフォーム セキュリティについては、[Azure セキュリティ センター](https://www.microsoft.com/TrustCenter/Security/default.aspx)をご覧ください。

Azure Database for PostgreSQL サービスでは、保存されるデータにストレージ暗号化が使用されます。 (クエリの実行中に、エンジンによって作成されるテンポラリ ファイル) を除くディスクでは、バックアップを含むデータを暗号化します。 このサービスでは、Azure ストレージ暗号化に含まれる AES 256 ビット暗号が使用され、キーはシステムによって管理されます。 ストレージの暗号化は常にオンになっており、無効にすることはできません。

Azure Database for PostgreSQL サービスは、既定で、ネットワーク上で転送されるデータに [SSL 接続セキュリティ](./concepts-ssl-connection-security.md)を要求するように構成されます。 データベース サーバーとクライアント アプリケーション間に SSL 接続を適用すると、サーバーとアプリケーション間のデータ ストリームが暗号化されて、"man in the middle" 攻撃から保護されます。  クライアント アプリケーションが SSL 接続をサポートしていない場合は、必要に応じて、データベース サービスに接続するための SSL 要求を無効にできます。

## <a name="next-steps"></a>次のステップ
- コストの比較と計算については、[価格のページ](https://azure.microsoft.com/pricing/details/postgresql/)を参照してください。
- 実際に[初めての Azure Database for PostgreSQL を作成](./quickstart-create-server-database-portal.md)してみましょう。
- 初めてのアプリを Python、PHP、Ruby、C\#、Java、Node.js で作成します。[接続ライブラリ](./concepts-connection-libraries.md)に関するページを参照してください。
