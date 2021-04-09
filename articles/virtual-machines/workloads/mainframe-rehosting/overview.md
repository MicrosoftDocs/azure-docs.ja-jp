---
title: Azure 仮想マシンでのメインフレーム リホスト
description: Microsoft Azure の仮想マシン (VM) を使用して、IBM Z ベースのシステムなどのメインフレーム ワークロードをリホストします。
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.openlocfilehash: 7e4b41497a0ed49ab0b39ab2c93c0142fce6416e
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104951319"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Azure 仮想マシンでのメインフレーム リホスト

ワークロードをメインフレーム環境からクラウドに移行すると、インフラストラクチャを最新化したり、多くの場合はコストを節約したりできます。 ワークロードの多くは、データベースの名前を更新するなど、コードを少し変更するだけで Azure に転送できます。

一般に、"*メインフレーム*" という用語は大規模なコンピューター システムを意味します。 具体的には、現在使用されている大多数は IBM System Z サーバーか、MVS、DOS、VSE、OS/390、z/OS を稼働する IBM プラグ互換システムです。

Azure 仮想マシン (VM) は、1 つのインスタンス上で特定のアプリケーションのリソースを分離および管理するために使用されます。 IBM z/OS などのメインフレームは、この目的のために論理パーティション (LPAR) を使用します。 メインフレームでは、関連付けられた COBOL プログラムを含む CICS 領域のために 1 つの LPAR が、IBM DB2 データベースのために別の LPAR が使用されることがあります。 標準的な [Azure 上の N 層アプリケーション](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)は、Azure VM を層ごとのサブネットにセグメント化できる仮想ネットワークにデプロイします。

Azure VM は、リフトアンドシフト シナリオをサポートするメインフレーム エミュレーション環境およびコンパイラを実行できます。 開発やテストは多くの場合、メインフレームから Azure の開発/テスト環境に移行する最初のワークロードに含まれます。 次の図に示すように、エミュレートできる一般的なサーバー コンポーネントには、オンライン トランザクション処理 (OLTP)、バッチ、およびデータ インジェスト システムが含まれます。

![Azure 上のエミュレーション環境では、z/OS ベースのシステムを実行できます。](media/01-overview.png)

一部のメインフレーム ワークロードが比較的容易に Azure に移行できるのに対して、その他は、パートナー ソリューションを使用して Azure でリホストできます。 パートナー ソリューションの選択に関する詳細なガイダンスについては、[Azure メインフレーム移行センター](https://azure.microsoft.com/migration/mainframe/)が役立ちます。

## <a name="mainframe-migration"></a>メインフレーム移行

リホスト、再構築、交換、それとも廃止か。 IaaS と PaaS のいずれにするか。 メインフレーム アプリケーションの適切な移行戦略を決定するには、Azure アーキテクチャ センターの[メインフレーム移行](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)のガイドを参照してください。

## <a name="micro-focus-rehosting-platform"></a>Micro Focus リホスト プラットフォーム

Micro Focus Enterprise Server は、使用可能な最大のメインフレーム リホスト プラットフォームの 1 つです。 これを使用すると、Azure 上のより安価な x86 プラットフォームで z/OS ワークロードを実行できます。

作業を開始するには:

- [Enterprise Server と Enterprise Developer を Azure にインストールする](./microfocus/set-up-micro-focus-azure.md)
- [Azure で Enterprise Developer 用の CICS BankDemo を設定する](./microfocus/demo.md)
- [Azure 上の Docker コンテナーで Enterprise Server を実行する](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>Azure 上の TmaxSoft OpenFrame

TmaxSoft OpenFrame は、リフトアンドシフトのシナリオで使用される一般的なメインフレーム リホスト ソリューションです。 Azure 上の OpenFrame 環境は、開発、デモ、テスト、または運用環境のワークロードに適しています。

作業を開始するには:

- [TmaxSoft OpenFrame で作業を開始する](./tmaxsoft/get-started.md)
- [電子ブックをダウンロードする](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/)

## <a name="ibm-zdt-120"></a>IBM zD&T 12.0

IBM Z Development and Test Environment (IBM zD&T) は、z/OS ベースのアプリケーションの開発、テスト、およびデモに使用できる非運用環境を Azure 上に設定します。

Azure 上のエミュレーション環境では、Application Developers Controlled Distribution (ADCD) を使用して、さまざまな Z インスタンスをホストできます。 Azure および Azure Stack 上で zD&T Personal Edition、zD&T Parallel Sysplex、および zD&T Enterprise Edition を実行できます。

作業を開始するには:

- [Azure で IBM zD&T 12.0 を設定する](./ibm/install-ibm-z-environment.md)
- [zD&T で ADCD を設定する](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>Azure 上の IBM DB2 pureScale

IBM DB2 pureScale 環境は、Azure 向けデータベース クラスターを提供します。 これは元の環境と同一ではないものの、Parallel Sysplex 設定で動作する IBM DB2 for z/OS と同程度の可用性とスケーラビリティを実現します。

開始するには、「[Azure 上の IBM DB2 pureScale](.//ibm/ibm-db2-purescale-azure.md)」を参照してください。

## <a name="considerations"></a>考慮事項

メインフレーム ワークロードを Azure IaaS (サービスとしてのインフラストラクチャ) に移行する場合は、Azure VM を含む、何種類かのオンデマンドでスケーラブルなコンピューティング リソースから選択できます。 Azure は、さまざまな [Linux](../../linux/overview.md) および [Windows](../../windows/overview.md) VM を提供しています。

### <a name="compute"></a>Compute

Azure のコンピューティング能力は、メインフレームのキャパシティと比べて遜色ありません。 メインフレームのワークロードを Azure に移行することを検討中の場合、100 万命令/秒 (MIPS) のメインフレーム メトリックを仮想 CPU と比較してください。 

[メインフレーム コンピューティングを Azure に移行](./concepts/mainframe-compute-azure.md)する方法について確認してください。

### <a name="high-availability-and-failover"></a>高可用性とフェールオーバー

Azure では、コミットメントベースのサービス レベル アグリーメント (SLA) を提供します。 複数ナインの可用性が既定になっていて、SLA は、ローカルまたは geo ベースのサービスのレプリケーションによって最適化できます。 完全な [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) では、全体としての Azure の可用性の確保について説明します。

VM などの Azure IaaS の場合、特定のシステム関数によってフェールオーバー サポートが提供されます。たとえば、フェールオーバー クラスタリング インスタンスや可用性セットなどです。 Azure PaaS (サービスとしてのプラットフォーム) リソースを使用する場合、フェールオーバーはプラットフォームで自動的に処理されます。 たとえば、[Azure SQL Database](../../../azure-sql/database/sql-database-paas-overview.md) や [Azure Cosmos DB](../../../cosmos-db/introduction.md) などがあります。

### <a name="scalability"></a>スケーラビリティ

メインフレームは通常スケールアップしますが、クラウド環境はスケールアウトします。Azure は、ユーザーのニーズを満たすために、さまざまな [Linux](../../sizes.md) および [Windows](../../sizes.md) のサイズを提供しています。 クラウドはまた、正確なユーザー仕様に一致するようにスケールアップまたはスケールダウンします。 コンピューティング能力、ストレージ、およびサービスは、使用量ベースの課金モデルのもとで、オンデマンドで[スケール調整](/azure/architecture/best-practices/auto-scaling)されます。

### <a name="storage"></a>ストレージ

クラウドには、柔軟でスケーラブルなストレージ オプションがあり、必要な分だけ支払うことができます。 [Azure Storage](../../../storage/common/storage-introduction.md) は、データ オブジェクトのための高度にスケーラブルなオブジェクト ストア、クラウドのためのファイル システム サービス、信頼性の高いメッセージング ストア、および NoSQL ストアを提供します。 VM の場合、マネージド ディスクとアンマネージド ディスクは、永続的でセキュリティで保護されたディスク ストレージを提供します。

[メインフレーム ストレージを Azure に移行](./concepts/mainframe-storage-azure.md)する方法について確認してください。

### <a name="backup-and-recovery"></a>バックアップと回復

独自のディザスター リカバリー サイトを維持することは、高価な提案になる場合があります。 Azure では、[バックアップ](../../../backup/backup-overview.md)、[復旧](../../../site-recovery/site-recovery-overview.md)、および[冗長性](../../../storage/common/storage-redundancy.md)のための、実装が容易でコスト効率に優れたオプションをローカルまたはリージョン レベルで、または geo 冗長性経由で使用できます。

## <a name="azure-government-for-mainframe-migrations"></a>メインフレーム移行のための Azure Government

多くの公的機関は、そのメインフレーム アプリケーションをより現代的で柔軟なプラットフォームに移行したいと考えています。 Microsoft Azure Government は、グローバルな Microsoft Azure プラットフォームの物理的に分離されたインスタンスであり、連邦政府、州政府、および地方政府のシステム向けにパッケージ化されています。 これにより、特に米国政府機関やそのパートナーに世界レベルのセキュリティ、保護、およびコンプライアンス サービスが提供されます。

Azure Government は、この種類の環境が必要なシステムに対して FedRAMP High Impact の Provisional Authority to Operate (P-ATO) を獲得しました。

開始するには、[メインフレーム アプリケーション用の Microsoft Azure Government クラウド](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/)をダウンロードします。

## <a name="next-steps"></a>次のステップ

メインフレーム アプリケーションの移行またはリホストの手伝いを Microsoft [パートナー](partner-workloads.md)に依頼します。 

関連項目:

- [メインフレームのトピックに関するホワイト ペーパー](mainframe-white-papers.md)
- [メインフレーム移行](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [トラブルシューティング](../../troubleshooting/index.yml)
- [メインフレームから Azure への移行の解明](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md