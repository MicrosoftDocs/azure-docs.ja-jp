---
title: Azure Database for MariaDB に適したデプロイの種類の選択
description: この記事では、Azure Database for MariaDB に対するサービスとしてのインフラストラクチャ (IaaS) またはサービスとしてのプラットフォーム (PaaS) を導入する前に対応する必要がある考慮事項について説明します。
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: fa87748719e2d3775034e5a2850281cf8f1a0e8a
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71817382"
---
# <a name="choose-the-right-mariadb-server-option-in-azure"></a>Azure で適切な MariaDB サーバーオプションを選択する

Azure では、MariaDB サーバーのワークロードをホスト型インフラストラクチャ VM (IaaS) で実行することも、ホスト型サービス (PaaS) として実行することもできます。 PaaS には複数のデプロイ オプションがあり、各デプロイ オプション内にサービス レベルがあります。 PaaS か IaaS かを決定する際には、データベースの管理、修正プログラムの適用、バックアップの実行を自分で行うか、これらの操作を Azure に委任するかを決定する必要があります。</br>

その質問に対する回答に基づいて、次のオプションを検討してください。 <br/>

**Azure Database for MariaDB** は、コミュニティ エディションの安定バージョンに基づくフル マネージドの MariaDB データベース エンジンです。 このサービスとしてのリレーショナル データベース (DBaaS) は、Azure クラウドでホストされ、業界内のカテゴリとしてはサービスとしてのプラットフォーム (PaaS) に分類されます。 Azure 上の MariaDB のマネージド インスタンスでは、(オンプレミスまたは Azure 仮想マシンで) MariaDB サーバー を使用した場合に多くの構成が必要な組み込みの機能を使用できます。 MariaDB をサービスとして使用する場合は、従量課金制になり、中断することなくスケールアップまたはスケールアウトして強化できるオプションがあります。 さらに、スタンドアロンの MariaDB サーバーとは異なり、Azure Database for MariaDB には組み込みの高可用性、インテリジェンス、管理などの追加の機能があります。 <br/><br/>
**Azure VM 上の MariaDB** は、業界内のカテゴリとしてはサービスとしてのインフラストラクチャ (IaaS) に分類され、Azure クラウドのフルマネージドの仮想マシン内で MariaDB サーバーを実行できます。 MariaDB のすべての最新バージョンとエディションは、IaaS 仮想マシンにインストールできます。 Azure Database for MariaDB との最も大きな違いは、Azure VM 上の MariaDB でデータベース エンジンを制御できることです。 ただし、この制御には、仮想マシンと、データベース サーバーのメンテナンス/修正プログラムの適用、復旧と高可用性の設計などの多数の DBA タスクを管理する追加の責任が伴います。

これらのオプションの主な違いを次の表に示します。

|            | **Azure Database for MariaDB** | **Azure VM 上の MariaDB**    |
|:-------------------|:-----------------------------|:--------------------|
| **SLA**                | 99.99% の可用性の SLA を提供| 同じ可用性セットに 2 つ以上のインスタンスがある場合、最大 99.95% の可用性。 <br/>Premium Storage を使用した単一インスタンス VM の場合、99.9% <br/> 2 つ以上の可用性セットに 2 つ以上のインスタンスがある可用性ゾーンの場合、99.99%。<br/> 注 - [仮想マシンの SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) |
| **OS の修正プログラムの適用**        | 自動  | お客様が管理 |
|**MariaDB の修正プログラムの適用**     | 自動  | お客様が管理 |
| **高可用性** | 高可用性 (HA) モデルは、ノード レベルの中断が発生した場合の組み込みのフェールオーバー メカニズムに基づいています。 このような場合、このサービスは自動的にインスタンスを作成し、その新しいインスタンスにストレージを接続します。 | 高可用性は、お客様によって構築、実装、テスト、および維持されます。 これには、使用している MariaDB エンジンのバージョンに応じて、Always On (フェールオーバー クラスタリングまたはグループ レプリケーション)、ログ配布、およびトランザクション レプリケーションが含まれる場合があります。|
| **ゾーン冗長性** | 現在サポートされていません | Azure VM は、さまざまな可用性ゾーンで実行するように設定できます。 オンプレミス ソリューションの場合、お客様は独自のセカンダリ データセンターを作成、管理、および保守する必要があります。|
| **ハイブリッド シナリオ** | [データイン レプリケーション](https://docs.microsoft.com/azure/MariaDB/concepts-data-in-replication)では、外部の MariaDB サーバーから Azure Database for MariaDB サービスにデータを同期できます。 外部サーバーとして、オンプレミス、仮想マシン、または他のクラウド プロバイダーによってホストされるデータベース サービスを使用できます。  <br/> <br/> [読み取りレプリカ](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas)機能を使用すると、Azure Database for MariaDB サーバー (マスター) から、同じ Azure リージョン内または複数のリージョン間で最大 5 つの読み取り専用サーバー (レプリカ) にデータをレプリケートできます。 読み取り専用レプリカは、binlog レプリケーション テクノロジを使用して非同期的に更新されます。   <br/> <br/> 注 - リージョン間読み取りレプリケーションは、現在パブリック プレビュー段階です。| お客様が管理 <br/>
| **バックアップと復元** | [サーバーのバックアップ](https://docs.microsoft.com/azure/MariaDB/concepts-backup#backups)を自動的に作成し、ユーザーが構成したローカル冗長または geo 冗長ストレージに保存します。 このサービスでは、完全バックアップ、差分バックアップ、およびトランザクション ログ バックアップが作成されます。 | お客様が管理 |
| **データベース操作の監視** | お客様は、データベース操作に[アラートを設定](https://docs.microsoft.com/azure/MariaDB/concepts-monitoring)し、しきい値に達したときに対応できます。 | お客様が管理 |
| **高度なスレッド保護** | データベースにアクセスしたりデータベースを悪用したりしようとする、通常とは異なる、害を及ぼす可能性のある試行を示す異常なアクティビティを検出する [Advanced Threat Protection](https://docs.microsoft.com/azure/MariaDB/howto-database-threat-protection-portal) を提供します。 <br/> <br/> 注 - 高度なスレッド保護は現在パブリック プレビュー段階です。| お客様は自身で構築する必要があります。
| **バックアップ (ディザスター リカバリー)** | ユーザーが構成した[ローカル冗長または geo 冗長ストレージ](https://docs.microsoft.com/azure/MariaDB/howto-restore-server-portal)に自動バックアップを保存します。 バックアップを使用して、特定の時点にサーバーを復元することもできます。 保持期間は 7 日から 35 日の間で設定できます。 復元は、Azure portal を使用して実行できます。 <br/> | お客様によって完全に管理されます。これには、スケジュール設定、テスト、アーカイブ、ストレージ、保持が含まれますが、これに限定されるものではありません。 追加のオプションとして、Azure Recovery Services コンテナーを使用して、VM 上の Azure VM とデータベースをバックアップすることができます (プレビュー段階)。 |
| **パフォーマンスに関する推奨** | ワークロードの最適化を支援するために、使用状況テレメトリに基づいたプロアクティブな[パフォーマンスに関する推奨](https://techcommunity.microsoft.com/t5/Azure-Database-for-MariaDB/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110)をお客様に提供します。 <br/> <br/> 注 - パフォーマンスに関する推奨は現在パブリック プレビュー段階です。 | お客様が管理 |


## <a name="business-motivations-for-choosing-paas-or-iaas"></a>PaaS と IaaS のいずれかを選択するときのビジネスの要因

MariaDB データベースをホストするために PaaS と IaaS のどちらを選択するかの決定に影響する可能性のある要素がいくつかあります。

### <a name="cost"></a>コスト

現金が潤沢にないスタートアップ企業や運営予算に厳しい上限のある大手企業内のチームでは、データベースをホストするための最適なソリューションを決定する際に、資金調達での限界が主な考慮事項になることがよくあります。 このセクションでは、Azure Database for MariaDB と Azure VM 上の MariaDB に関する Azure の課金とライセンスの基礎について説明します。

#### <a name="billing"></a>課金

現在、Azure Database for MariaDB は、リソースの料金が異なる複数のサービス レベルでサービスとして利用できます。いずれの場合も、固定レートで時間単位で課金されます。 現在のサポートされているサービス レベル、コンピューティング サイズ、ストレージ容量の最新情報については、[仮想コアベースの購入モデル](https://docs.microsoft.com/azure/MariaDB/concepts-pricing-tiers)に関する記事を参照してください。 サービス レベルとコンピューティング サイズを動的に調整して、アプリケーションのさまざまなスループット ニーズを満たすことができます。 インターネット トラフィックの送信に対しては、通常の [データ転送料金](https://azure.microsoft.com/pricing/details/data-transfers/)で課金されます。

Azure Database for MariaDB では、データベース ソフトウェアの自動的な構成、修正プログラムの適用、およびアップグレードが Microsoft によって行われるため、管理コストが軽減されます。 また、 [組み込みのバックアップ](https://docs.microsoft.com/azure/MariaDB/concepts-backup) 機能は、特に、多数のデータベースがある場合の大幅なコスト削減に役立ちます。 Azure VM 上の MariaDB では、任意の MariaDB バージョンを選択して実行することができます。 使用する MariaDB のバージョンに関係なく、プロビジョニングされた VM と MariaDB に使用されている特定のライセンスの種類のコストをお支払いいただきます。

Azure Database for MariaDB は、あらゆる種類のノード レベルの中断に対して組み込みの高可用性を提供し、サービスに対する 99.99% の SLA を維持します。 ただし、VM 内のデータベース高可用性 (HA) を実現するには、[MariaDB レプリケーション](https://mariadb.com/kb/en/library/setting-up-replication/)など、MariaDB データベースで利用可能な高可用性オプションを使用することをお勧めします。 サポートされている高可用性オプションを使用しても追加の SLA は提供されませんが、追加コストと管理オーバーヘッドで 99.99% を超えるデータベース可用性を実現できます。

価格の詳細については、次のリソースを参照してください。
* [Azure Database for MariaDB の価格](https://azure.microsoft.com/pricing/details/MariaDB/)
* [仮想マシンの価格](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>管理

多くの企業にとって、クラウド サービスに切り替えるかどうかの決定には、管理の複雑さの軽減が重要な要素になります。 IaaS および PaaS では、Microsoft が、基盤となるインフラストラクチャを管理して、すべてのデータを自動的にレプリケートし、ディザスター リカバリーを提供し、さらにデータベース ソフトウェアの構成とアップグレード、負荷分散の管理、サーバーに障害が発生した場合の透過的なフェールオーバーを実行します。

* **Azure Database for MariaDB** を使用すると、ユーザーは引き続きデータベースを管理できますが、データベース エンジン、オペレーティング システム、ハードウェアを管理する必要はありません。 引き続き管理できるアイテムの例として、データベースとログイン、インデックスとクエリのチューニング、監査とセキュリティなどがあります。 さらに、別のデータ センターへの高可用性を構成するために必要な構成や管理は、最小限で済むか、またはまったく行わないで済みます。<br/><br/>
* **Azure VM 上の MariaDB**では、オペレーティング システムと MariaDB サーバー インスタンスの構成を全面的に制御できます。 VM を使用する場合、オペレーティング システムとデータベース ソフトウェアの更新やアップグレードのタイミングに加えて、ウイルス対策アプリケーションなどの追加ソフトウェアのインストールのタイミングは、ユーザーが決定します。 修正プログラムの適用、バックアップ、高可用性の実現を大幅に簡素化するために、自動化された機能がいくつか用意されています。 また、VM のサイズ、ディスクの数、ストレージの構成を制御できます。 詳細については、「Azure の仮想マシンおよびクラウド サービスのサイズ」を参照してください。

### <a name="time-to-move-to-azure-br"></a>Azure へ移行するタイミング <br/>
* **Azure Database for MariaDB** は、開発者の生産性と新しいソリューションの製品化に要する時間の短縮が重要な、クラウド用に設計されたアプリケーションに最適なソリューションです。 このサービスは、プログラムによる DBA のような機能を備えることで、基になるオペレーティング システムとデータベースを管理する必要性が減少するため、クラウドの設計者と開発者に最適です。<br/><br/>
* **Azure VM 上の MariaDB** は、既存または新規のアプリケーションに、MariaDB データベースまたは Windows/Linux 上の MariaDB データベースの機能へのアクセスが必要で、新しいオンプレミス ハードウェアの取得にかかる時間とコストを回避したい場合に最適です。 このオプションは、既存のオンプレミス アプリケーションとデータベースを Azure にそのまま移行する場合で、 Azure Database for MariaDB インスタンスが適合しない場合にも適しています。 プレゼンテーション層、アプリケーション層、およびデータ層を変更する必要がないため、既存のソリューションを再設計する時間と予算が節約されます。 その一方で、すべてのソリューションを Azure に移行し、Azure プラットフォームで必要となる可能性のあるいくつかのパフォーマンスの最適化に集中できます。

## <a name="next-steps"></a>次の手順

* 「[Azure Database for MariaDB の価格](https://azure.microsoft.com/pricing/details/MariaDB/)」を参照してください
* [初めてのサーバーを作成](https://review.docs.microsoft.com/azure/MariaDB/quickstart-create-MariaDB-server-database-using-azure-portal)してみましょう。

