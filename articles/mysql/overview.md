---
title: "Azure Database for MySQL リレーショナル データベース サービスの概要 | Microsoft Docs"
description: "Azure Database for MySQL リレーショナル データベース サービスの概要"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 09/15/2017
ms.custom: mvc
ms.openlocfilehash: 3eff97a9e7568566cd01a4eb3c7ee66806eac4cc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-database-for-mysql-service-introduction"></a>Azure Database for MySQL とは -  サービスの概要
Azure Database for MySQL は、Microsoft クラウドにおける、[MySQL Community Edition](https://www.mysql.com/products/community/) のデータベース エンジンをベースとしたリレーショナル データベース サービスです。  Azure Database for MySQL には、次の特長があります。

- さまざまなサービス レベルで予測可能なパフォーマンス。
- アプリケーションのダウンタイムを伴わない動的スケーラビリティ。
- 組み込みの高可用性。
- データ保護。

これらの機能は、ほとんど管理を必要とせず、いずれも追加費用なしで利用することができます。 そのため貴重な時間とリソースを仮想マシンとインフラストラクチャの管理に奪われることなく、迅速なアプリ開発や、製品化に要する時間の短縮化に専念することができます。 加えて、オープン ソースのツールとプラットフォームを自由に選んでアプリケーションを開発し続けることができるので、新たにスキルを身に付けなくても確実に、ビジネスの現場で必要となるスピードと効率を高めることができます。

![Azure Database for MySQL の概念図](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

この記事では、パフォーマンスとスケーラビリティ、管理容易性に関連する、Azure Database for MySQL の中心概念と機能について紹介し、詳細を参照するためのリンクも提供します。 すぐに始めるには、次のクイック スタートをご覧ください。
- [Azure Portal を使用した Azure Database for MySQL サーバーの作成](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Azure CLI を使用した Azure Database for MySQL サーバーの作成](quickstart-create-mysql-server-database-using-azure-cli.md)

Azure CLI の各種サンプルについては、次のページを参照してください。
- [Azure Database for MySQL 用 Azure CLI サンプル](sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-without-downtime"></a>ダウンタイムなしでのパフォーマンスの調整とスケール
Azure Database for MySQL サービスには、Basic と Standard の 2 つのサービス レベルがあります。 軽量のデータベース ワークロードから重量のデータベース ワークロードに至るまでサポートできるように、レベルごとに異なるパフォーマンスと機能 を提供しています。 最初は月数ドルの小規模データベースでアプリを構築し、後から実際のソリューションのニーズに応じて、ダウンタイムなしでサービス レベルを変更することができます。 動的なスケーラビリティにより、データベースは変化の激しいリソース要件に透過的に対処することができます。 必要なときに必要な分のリソースにのみ課金されます。

## <a name="monitoring-and-alerting"></a>監視とアラート
パフォーマンスの高低を調整するとき、何に基づいて右クリックでストップをかけますか? 組み込みのパフォーマンス監視機能およびアラート機能と、コンピューティング ユニットに基づくパフォーマンス評価とを組み合わせて使用してください。 これらの機能を使用すると、現在または今後のパフォーマンスのニーズに基づいて、スケールアップとスケールダウンの影響をすばやく評価することができます。 詳細については、[サービス レベルの概念](concepts-service-tiers.md)に関するページを参照してください。

## <a name="keep-your-app-and-business-running"></a>アプリケーションとビジネスの継続的な稼働
Microsoft が管理するデータセンターのグローバル ネットワークによって強化された、Azure の業界をリードする可用性 99.99% のサービス レベル アグリーメント (SLA) により、アプリケーションの 24 時間 365 日の継続的な稼働が可能になります。 すべての Azure Database for MySQL サーバーで、組み込みのセキュリティ、フォールト トレランス、データ保護を利用できます。これらは、本来なら自身で購入または設計し、構築、管理する必要があります。 Azure Database for MySQL では、ポイントインタイム リストアを使用して、サーバーを 35 日間分さかのぼって以前の状態に戻すことができます。

## <a name="secure-your-data"></a>データのセキュリティ保護
Azure データベース サービスは、アクセスの制限、保存データと移動中のデータの保護、監視アクティビティの支援を行う機能を備えた Azure Database for MySQL によって、データ セキュリティを維持してきました。 Azure のプラットフォーム セキュリティについては、[Azure セキュリティ センター](https://www.microsoft.com/en-us/TrustCenter/Security/default.aspx)をご覧ください。

Azure Database for MySQL サービスでは、保存されるデータにストレージ暗号化が使用されます。 バックアップを含むデータはディスク上で暗号化されます (ただし、クエリの実行中にエンジンによって作成された一時ファイルを除きます)。 このサービスでは、Azure ストレージ暗号化に含まれる AES 256 ビット暗号が使用され、キーはシステムによって管理されます。 ストレージの暗号化は常にオンになっており、無効にすることはできません。

Azure Database for MySQL サービスは、既定で、ネットワーク上で転送されるデータに [SSL 接続セキュリティ](./concepts-ssl-connection-security.md)を要求するように構成されています。 データベース サーバーとクライアント アプリケーションの間に SSL 接続を適用すると、サーバーとアプリケーションの間のデータ ストリームが暗号化されて、"man in the middle" 攻撃から保護されます。  クライアント アプリケーションが SSL 接続をサポートしていない場合は、必要に応じて、データベース サービスに接続するための SSL 要求を無効にできます。

## <a name="next-steps"></a>次のステップ
ここでは、Azure Database for MySQL の概要を紹介し、"Azure Database for MySQL とは何か" という問いに対する答えを示したので、次のステップに進むことができます。
- コストの比較と計算については、価格のページを参照してください。 [料金](https://azure.microsoft.com/pricing/details/mysql/)
- まず、最初のサーバーを作成します。 [Azure Portal を使用した Azure Database for MySQL サーバーの作成](quickstart-create-mysql-server-database-using-azure-portal.md)
- 初めてのアプリを Python、PHP、Ruby、C\#、Java、Node.js で作成します。[Azure Database for MySQL への接続に使用する接続ライブラリ](concepts-connection-libraries.md)に関するページを参照してください。
