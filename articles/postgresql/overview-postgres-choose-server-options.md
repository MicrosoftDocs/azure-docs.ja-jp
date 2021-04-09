---
title: Azure で適切な PostgreSQL サーバー オプションを選択する
description: デプロイに適した PostgreSQL サーバー オプションを選択するためのガイドラインを提供します。
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: db80ae66761e49ce90f25a22f0e74cf8a1300796
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98797242"
---
# <a name="choose-the-right-postgresql-server-option-in-azure"></a>Azure で適切な PostgreSQL サーバー オプションを選択する

Azure では、PostgreSQL サーバーのワークロードをホステッド仮想マシンのサービスとしてのインフラストラクチャ (IaaS) で実行することも、ホステッドのサービスとしてのプラットフォーム (PaaS) として実行することもできます。 PaaS には複数のデプロイ オプションがあり、それぞれに複数のサービス レベルがあります。 IaaS か PaaS かを選択する際には、データベースの管理、修正プログラムの適用、バックアップの実行を自分で行うか、これらの操作を Azure に委任するかを決定する必要があります。

決定する際には、PaaS での次の 3 つのオプションか、または Azure VM (IaaS) での実行を検討してください
- [Azure Database for PostgreSQL 単一サーバー](./overview-single-server.md)
- [Azure Database for PostgreSQL フレキシブル サーバー](./flexible-server/overview.md)
- [Azure Database for PostgreSQL Hyperscale (Citus)](hyperscale/index.yml)

**Azure VM 上の PostgreSQL** オプションは、IaaS の業界カテゴリに分類されます。 このサービスを使用すると、Azure クラウドプラットフォーム上のフル マネージドの仮想マシン内で PostgreSQL サーバーを実行できます。 PostgreSQL のすべての最新バージョンとエディションは、IaaS 仮想マシンにインストールできます。 Azure Database for PostgreSQL との最も大きな違いは、Azure VM 上の PostgreSQL によってデータベース エンジンの制御が提供されていることです。 ただし、この制御には、VM と多くのデータベース管理 (DBA) タスクを管理する責任を伴います。 これらのタスクには、データベース サーバーの保守および修正プログラムの適用、データベースの復旧、高可用性の設計が含まれます。

これらのオプションの主な違いを次の表に示します。

| **属性** | **Azure VM 上の Postgres** | **PaaS としての PostgreSQL** |
| ----- | ----- | ----- |
| <B> 可用性 SLA |- 99.99% (可用性セットを含む) <br> - 99.95% (単一 VM を含む) | - 単一サーバー – 99.99% <br> - フレキシブル サーバー – プレビュー期間中は使用できません <br> - Hyperscale (Citus) - 99.95% (高可用性が有効になっている場合)|
| <B> OS と PostgreSQL の修正プログラム | - カスタマー マネージド | - 単一サーバー – 自動 <br> - フレキシブル サーバー – 自動 (オプションのカスタマー マネージド ウィンドウを含む) <br> - Hyperscale (Citus) – 自動 |
| <B> 高可用性 | - お客様が高可用性の設計、実装、テスト、保守を行います。 機能には、クラスタリング、レプリケーションなどが含まれる場合があります。 | - 単一サーバー: 組み込み <br> - フレキシブル サーバー: 組み込み <br> - Hyperscale (Citus): スタンバイでビルドされる |
| <B> ゾーン冗長性 | - Azure VM は、さまざまな可用性ゾーンで実行するように設定できます。 オンプレミス ソリューションの場合、お客様は独自のセカンダリ データ センターを作成、管理、および保守する必要があります。 | - 単一サーバー:いいえ <br> - フレキシブル サーバー:はい <br> - Hyperscale (Citus):いいえ |
| <B> ハイブリッド シナリオ | - カスタマー マネージド |- 単一サーバー:読み取りレプリカ <br> - フレキシブル サーバー:プレビュー期間中は使用できません <br> - Hyperscale (Citus):いいえ |
| <B> バックアップと復元 | - カスタマー マネージド | - 単一サーバー: ローカルと geo のユーザー構成を含む組み込み <br> - フレキシブル サーバー: ゾーン冗長ストレージでのユーザー構成を含む組み込み <br> - Hyperscale (Citus): 組み込み |
| <B> データベース操作の監視 | - カスタマー マネージド | - 単一サーバー、フレキシブル サーバー、および Hyperscale (Citus):すべてのオファーにおいて、お客様はデータベース操作にアラートを設定し、しきい値に達したときに対応できます。 |
| <B> Advanced Threat Protection | - お客様は自身でこの保護を構築する必要があります。 |- 単一サーバー:はい <br> - フレキシブル サーバー:プレビュー期間中は使用できません <br> - Hyperscale (Citus):いいえ |
| <B>ディザスター リカバリー | - カスタマー マネージド | - 単一サーバー:geo 冗長バックアップと geo 読み取りレプリカ <br> - フレキシブル サーバー:プレビュー期間中は使用できません <br> - Hyperscale (Citus):いいえ |
| <B> インテリジェントなパフォーマンス | - カスタマー マネージド | - 単一サーバー:はい <br> - フレキシブル サーバー:プレビュー期間中は使用できません <br> - Hyperscale (Citus):いいえ |

## <a name="total-cost-of-ownership-tco"></a>総保有コスト (TCO)

TCO は多くの場合、データベースをホストするための最適なソリューションを決定する主な考慮事項です。 これは、資金が少ないスタートアップ企業であるか、厳しい予算の制約下で運用している老舗企業内のチームであるかに関係なく当てはまります。 このセクションでは、Azure Database for PostgreSQL と Azure VM 上の PostgreSQL に適用される Azure の課金とライセンスの基礎について説明します。

## <a name="billing"></a>課金

Azure Database for PostgreSQL は現在、リソースの料金が異なる複数のレベルで、サービスとして利用できます。 すべてのリソースは、固定レートで時間単位で課金されます。 現在サポートされているサービス レベル、コンピューティング サイズ、およびストレージ容量に関する最新情報については、[価格に関するページ](https://azure.microsoft.com/pricing/details/postgresql/server/)をご覧ください。サービス レベルとコンピューティング サイズを動的に調整して、アプリケーションのさまざまなスループット ニーズを満たすことができます。 インターネット トラフィックの送信に対しては、通常の[データ転送料金](https://azure.microsoft.com/pricing/details/data-transfers/)で課金されます。

Azure Database for PostgreSQL では、Microsoft によってデータベース ソフトウェアの構成、修正プログラムの適用、およびアップグレードが自動的に行われます。 これらの自動化されたアクションにより、管理コストが削減されます。 また、Azure Database for PostgreSQL には、[自動バックアップ リンク]()機能が用意されています。 こうした機能は、特に多数のデータベースがある場合の大幅なコスト削減に役立ちます。 対照的に、Azure VM 上の PostgreSQL では、任意の PostgreSQL バージョンを選択して実行できます。 ただし、プロビジョニングされた VM、データ、バックアップ、監視データ、およびログ ストレージに関連するストレージ コスト、また、使用される特定の PostgreSQL ライセンスの種類 (存在する場合) に対応するコストを支払う必要があります。

Azure Database for PostgreSQL によって、あらゆる種類のノード レベルの中断に対して組み込みの高可用性が提供されると共に、サービスに対する 99.99% の SLA 保証が引き続き維持されます。 ただし、VM 内のデータベース高可用性を実現するには、PostgreSQL データベースで利用可能な[ストリーミング レプリケーション](https://www.postgresql.org/docs/12/warm-standby.html#STREAMING-REPLICATION)などの高可用性オプションを使用します。 サポートされている高可用性オプションを使用しても、追加の SLA は提供されません。 ただし、追加コストと管理オーバーヘッドで 99.99% を超えるデータベース可用性を実現できます。

価格の詳細については、次の記事を参照してください。
- [Azure Database for PostgreSQL の価格](https://azure.microsoft.com/pricing/details/postgresql/server/)
- [仮想マシンの価格](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)

## <a name="administration"></a>管理

多くの企業にとって、クラウド サービスへの移行の決定には、コストだけでなく、管理の複雑さの軽減も重要な要素です。

IaaS の場合、Microsoft では次のことを行います。

- 基になるインフラストラクチャを管理します。
- 基になるハードウェアと OS に対して修正プログラムを自動的に適用します

PaaS の場合、Microsoft では次のことを行います。

- 基になるインフラストラクチャを管理します。
- 基になるハードウェア、OS、およびデータベース エンジンに対して修正プログラムを自動的に適用します。
- データベースの高可用性を管理します。
- 自動的にバックアップを実行してすべてのデータをレプリケートし、ディザスター リカバリーを提供します。
- 既定では、保存データと移動中のデータを暗号化します。
- サーバーを監視して、クエリ パフォーマンスの分析情報とパフォーマンスに関する推奨事項に対する機能を提供します。

Azure Database for PostgreSQL を使用して、データベースを引き続き管理できます。 しかしながら、データベース エンジン、オペレーティング システム、ハードウェアを管理する必要はなくなります。 引き続き管理できる項目の例を次に示します。

- データベース
- サインイン
- インデックスのチューニング
- クエリのチューニング
- 監査
- セキュリティ

また、別のデータ センターに高可用性を構成するために必要な構成や管理は、最小限で済むか、まったく行わないで済みます。

- Azure VM 上の PostgreSQL を使用する場合は、オペレーティング システムと PostgreSQL サーバー インスタンスの構成を全面的に制御できます。 VM を使用する場合は、オペレーティング システムとデータベース ソフトウェアの更新またはアップグレードをいつ行うかと、どのパッチを適用するかを決定します。 また、ウイルス対策アプリケーションなどの追加ソフトウェアをインストールするタイミングも決定します。 修正プログラムの適用、バックアップ、高可用性の実現を大幅に簡素化するために、自動化された機能がいくつか用意されています。 VM のサイズ、ディスクの数、ストレージの構成を制御できます。 詳細については、[Azure の仮想マシンおよびクラウド サービスのサイズ](../virtual-machines/sizes.md)に関する記事を参照してください。

## <a name="time-to-move-to-azure-postgresql-service-paas"></a>Azure PostgreSQL Service (PaaS) へ移行するタイミング

- Azure Database for PostgreSQL は、開発者の生産性と新しいソリューションの製品化に要する時間の短縮が重要になる場合に、クラウド設計のアプリケーションに適したソリューションです。 このサービスは、DBA のようなプログラムによる機能を備えることで、基になるオペレーティング システムとデータベースを管理する必要性が減少するため、クラウドの設計者と開発者に適しています。

- Azure VM 上の PostgreSQL は、新しいオンプレミス ハードウェアを取得する時間とコストがかからないようにする場合に、サービスではサポートされていないか、または基になる OS のアクセスを必要とする PostgreSQL エンジンを、より詳しく制御してカスタマイズしなければならないアプリケーションに適したソリューションです。

## <a name="next-steps"></a>次のステップ

- [Azure Database for PostgreSQL の価格](https://azure.microsoft.com/pricing/details/postgresql/server/)に関するページをご覧ください。
- まず、最初のサーバーを作成します。