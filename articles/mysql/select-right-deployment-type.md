---
title: 適切なデプロイの種類の選択 - Azure Database for MySQL
description: この記事では、Azure Database for MySQL をサービスとしてのインフラストラクチャ (IaaS) またはサービスとしてのプラットフォーム (PaaS) としてデプロイする前に考慮すべき要素について説明します。
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 827c1954456ee7943c48525919ed411836f610aa
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86114025"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>Azure で適切な MySQL サーバー オプションを選択する

Azure では、MySQL サーバーのワークロードをホスト型仮想マシンのサービスとしてのインフラストラクチャ (IaaS) で実行することも、ホスト型のサービスとしてのプラットフォーム (PaaS) として実行することもできます。 PaaS には複数のデプロイ オプションがあり、各デプロイ オプション内にサービス レベルがあります。 IaaS か PaaS かを選択する際には、データベースの管理、修正プログラムの適用、バックアップの実行を自分で行うか、これらの操作を Azure に委任するかを決定する必要があります。

決定する際に、次の 2 つのオプションを検討します。

- **Azure Database for MySQL**。 このオプションは、MySQL コミュニティ エディションの安定バージョンに基づくフル マネージドの MySQL データベース エンジンです。 このサービスとしてのリレーショナル データベース (DBaaS) は、Azure クラウド プラットフォームでホストされ、業界内のカテゴリとしては PaaS に分類されます。

  Azure 上の MySQL のマネージド インスタンスでは、MySQL サーバーがオンプレミスまたは Azure VM に存在する場合に多くの構成が必要な組み込みの機能を使用できます。

  MySQL をサービスとして使用する場合は、従量課金制になり、中断することなくスケールアップまたはスケールアウトして制御を強化できるオプションがあります。 スタンドアロンの MySQL サーバーとは異なり、Azure Database for MySQL には組み込みの高可用性、インテリジェンス、管理などの追加の機能があります。

- **Azure VM 上の MySQL**。 このオプションは、IaaS の業界カテゴリに分類されます。 このサービスを使用すると、Azure クラウドプラットフォーム上のフル マネージドの仮想マシン内で MySQL サーバーを実行できます。 MySQL のすべての最新バージョンとエディションは、IaaS 仮想マシンにインストールできます。

  Azure Database for MySQL との最も大きな違いは、Azure VM 上の MySQL でデータベース エンジンを制御できることです。 ただし、この制御には、VM と多くのデータベース管理 (DBA) タスクを管理する責任を伴います。 これらのタスクには、データベース サーバーの保守および修正プログラムの適用、データベースの復旧、高可用性の設計が含まれます。

これらのオプションの主な違いを次の表に示します。

| 属性          | Azure Database for MySQL | Azure VM 上の MySQL    |
|:-------------------|:-----------------------------|:--------------------|
| サービス レベル アグリーメント (SLA)                | 99.99% の可用性の SLA を提供| 同じ可用性セットに 2 つ以上のインスタンスがある場合、最大 99.95% の可用性。<br/><br/>Premium Storage を使用した単一インスタンス VM の場合、99.9% の可用性。<br/><br/>複数の可用性セット内の複数のインスタンスで Availability Zones を使用した場合、99.99%。<br/><br/>「[Virtual Machines の SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)」を参照してください。 |
| オペレーティング システムの修正プログラムの適用        | 自動  | お客様が管理 |
| MySQL の修正プログラムの適用     | 自動  | お客様が管理 |
| 高可用性 | 高可用性 (HA) モデルは、ノード レベルの中断が発生した場合の組み込みのフェールオーバー メカニズムに基づいています。 このような場合、このサービスは自動的に新しいインスタンスを作成し、このインスタンスにストレージを接続します。 | お客様が高可用性の設計、実装、テスト、保守を行います。 機能には、クラスタリング、レプリケーションなどが含まれます。|
| ゾーン冗長性 | 以下は現在サポートされていません | Azure VM は、さまざまな可用性ゾーンで実行するように設定できます。 オンプレミス ソリューションの場合、お客様は独自のセカンダリ データ センターを作成、管理、および保守する必要があります。|
| ハイブリッド シナリオ | [データイン レプリケーション](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication)では、外部の MySQL サーバーから Azure Database for MySQL サービスにデータを同期できます。 外部サーバーとして、オンプレミス、仮想マシン、または他のクラウド プロバイダーによってホストされるデータベース サービスを使用できます。<br/><br/> [読み取りレプリカ](https://docs.microsoft.com/azure/mysql/concepts-read-replicas)機能を使用すると、Azure Database for MySQL マスター サーバーから、最大 5 台の読み取り専用レプリカ サーバーにデータをレプリケートできます。 レプリカは、同じ Azure リージョン内に存在するか、複数のリージョンにまたがっています。 読み取り専用レプリカは、binlog レプリケーション テクノロジを使用して非同期的に更新されます。| お客様が管理
| バックアップと復元 | [サーバーのバックアップ](https://docs.microsoft.com/azure/mysql/concepts-backup#backups)を自動的に作成し、ローカル冗長または geo 冗長のいずれかであるユーザー構成ストレージに保存します。 このサービスでは、完全バックアップ、差分バックアップ、およびトランザクション ログ バックアップが作成されます。 | お客様が管理 |
| データベース操作の監視 | お客様は、データベース操作に[アラートを設定](https://docs.microsoft.com/azure/mysql/concepts-monitoring)し、しきい値に達したときに対応できます。 | お客様が管理 |
| 高度な脅威保護 | [Advanced Threat Protection](https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal) を提供します。 この保護により、データベースにアクセスしたりデータベースを悪用したりしようとする、通常とは異なる、害を及ぼす可能性のある試行を示す異常なアクティビティが検出されます。 | お客様は自身でこの保護を構築する必要があります。
| 障害復旧 | ユーザー構成の[ローカル冗長または geo 冗長ストレージ](https://docs.microsoft.com/azure/mysql/howto-restore-server-portal)に自動バックアップを保存します。 バックアップを使用して、特定の時点にサーバーを復元することもできます。 保持期間は 7 日から 35 日の範囲です。 復元は、Azure portal を使用して実行できます。 | お客様が全面的に管理します。 責任には、スケジュール設定、テスト、アーカイブ、ストレージ、保持が含まれますが、これに限定されるものではありません。 追加のオプションとして、Azure Recovery Services コンテナーを使用して、Azure VM と VM 上のデータベースをバックアップすることができます。 このオプションはプレビュー段階です。 |
| パフォーマンスに関する推奨事項 | システム生成の使用状況ログ ファイルに基づいて、[パフォーマンスに関する推奨事項](https://techcommunity.microsoft.com/t5/Azure-Database-for-MySQL/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110)をお客様に提供します。 推奨事項は、ワークロードを最適化するのに役立ちます。 | お客様が管理 |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>PaaS と IaaS のいずれかを選択するときのビジネスの要因

MySQL データベースをホストするために PaaS と IaaS のどちらを選択するかの決定に影響する可能性のある要素がいくつかあります。

### <a name="cost"></a>コスト

データベースをホストするための最適なソリューションを決定する主な考慮事項は、多くの場合、限られた資金です。 これは、資金が少ないスタートアップ企業であるか、厳しい予算の制約下で運用している老舗企業内のチームであるかに関係なく当てはまります。 このセクションでは、Azure Database for MySQL と Azure VM 上の MySQL に適用される Azure の課金とライセンスの基礎について説明します。

#### <a name="billing"></a>課金

Azure Database for MySQL は、リソースの料金が異なる複数のサービス レベルでサービスとして利用できます。 すべてのリソースは、固定レートで時間単位で課金されます。 現在のサポートされているサービス レベル、コンピューティング サイズ、ストレージ容量の最新情報については、[仮想コアベースの購入モデル](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers)に関する記事を参照してください。 サービス レベルとコンピューティング サイズを動的に調整して、アプリケーションのさまざまなスループット ニーズを満たすことができます。 インターネット トラフィックの送信に対しては、通常の[データ転送料金](https://azure.microsoft.com/pricing/details/data-transfers/)で課金されます。

Azure Database for MySQL では、Microsoft がデータベース ソフトウェアの構成、修正プログラムの適用、およびアップグレードを行います。 これらの自動化されたアクションにより、管理コストが削減されます。 また、Azure Database for MySQL には [組み込みのバックアップ](https://docs.microsoft.com/azure/mysql/concepts-backup)機能があります。 こうした機能は、特に多数のデータベースがある場合の大幅なコスト削減に役立ちます。 対照的に、Azure VM 上の MySQL では、任意の MySQL バージョンを選択して実行できます。 どの MySQL バージョンを使用する場合でも、プロビジョニングされた VM と使用されている特定の MySQL ライセンスの種類のコストをお支払いいただきます。

Azure Database for MySQL は、あらゆる種類のノード レベルの中断に対して組み込みの高可用性を提供し、サービスに対する 99.99% の SLA 保証を維持します。 ただし、VM 内のデータベース高可用性を実現するには、MySQL データベースで利用可能な [MySQL レプリケーション](https://dev.mysql.com/doc/refman/8.0/en/replication.html)などの高可用性オプションを使用する必要があります。 サポートされている高可用性オプションを使用しても、追加の SLA は提供されません。 ただし、追加コストと管理オーバーヘッドで 99.99% を超えるデータベース可用性を実現できます。

価格の詳細については、次の記事を参照してください。
* [Azure Database for MySQL の価格](https://azure.microsoft.com/pricing/details/mysql/)
* [仮想マシンの価格](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>管理

多くの企業にとって、クラウド サービスへの移行の決定には、コストだけでなく、管理の複雑さの軽減も重要な要素です。 IaaS と PaaS で、Microsoft は次のことを行います。

- 基になるインフラストラクチャを管理します。
- すべてのデータを自動的にレプリケートし、ディザスター リカバリーを提供します。
- データベース ソフトウェアを構成してアップグレードします。
- 負荷分散を管理します。
- サーバーに障害が発生した場合に透過的なフェールオーバーを実行します。

各オプションの管理上の考慮事項について、次の一覧で説明します。

* Azure Database for MySQL では、データベースを引き続き管理できます。 しかしながら、データベース エンジン、オペレーティング システム、ハードウェアを管理する必要はなくなります。 引き続き管理できる項目の例を次に示します。

  - データベース
  - サインイン
  - インデックスのチューニング
  - クエリのチューニング
  - 監査
  - セキュリティ

  また、別のデータ センターに高可用性を構成するために必要な構成や管理は、最小限で済むか、まったく行わないで済みます。

* Azure VM 上の MySQL では、オペレーティング システムと MySQL サーバー インスタンスの構成を全面的に制御できます。 VM では、オペレーティング システムとデータベース ソフトウェアを更新またはアップグレードするタイミングを自分で決定します。 また、ウイルス対策アプリケーションなどの追加ソフトウェアをインストールするタイミングも決定します。 修正プログラムの適用、バックアップ、高可用性の実現を大幅に簡素化するために、自動化された機能がいくつか用意されています。 VM のサイズ、ディスクの数、ストレージの構成を制御できます。 詳細については、[Azure の仮想マシンおよびクラウド サービスのサイズ](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)に関する記事を参照してください。

### <a name="time-to-move-to-azure"></a>Azure へ移行するタイミング

* Azure Database for MySQL は、開発者の生産性と新しいソリューションの製品化に要する時間の短縮が重要な場合において、クラウド用に設計されたアプリケーションに最適なソリューションです。 このサービスは、DBA のようなプログラムによる機能を備えることで、基になるオペレーティング システムとデータベースを管理する必要性が減少するため、クラウドの設計者と開発者に適しています。

* 新しいオンプレミス ハードウェアの取得にかかる時間とコストを回避したい場合、Azure VM 上の MySQL は、MySQL データベースを必要とするアプリケーションや、Windows または Linux 上の MySQL 機能にアクセスする必要があるアプリケーションに適したソリューションです。 このソリューションは、既存のオンプレミス アプリケーションとデータベースを Azure にそのまま移行する場合、Azure Database for MySQL が適さない場合にも適しています。

  プレゼンテーション層、アプリケーション層、データ層を変更する必要がないため、既存のソリューションを再設計する時間と予算が節約されます。 その代わりに、すべてのソリューションを Azure に移行することと、Azure プラットフォームで必要となる可能性のあるパフォーマンス最適化に取り組むことに集中できます。

## <a name="next-steps"></a>次のステップ

* 「[Azure Database for MySQL の価格](https://azure.microsoft.com/pricing/details/MySQL/)」をご確認ください。
* [初めてのサーバーを作成](https://docs.microsoft.com/azure/MySQL/quickstart-create-MySQL-server-database-using-azure-portal)してみましょう。
