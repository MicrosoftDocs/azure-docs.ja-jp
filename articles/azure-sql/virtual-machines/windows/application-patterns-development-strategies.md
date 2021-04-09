---
title: VM での SQL Server のアプリケーション パターン | Microsoft Docs
description: この記事では、Azure Virtual Machines 上の SQL Server のアプリケーション パターンについて説明します。 ソリューション設計者および開発者向けに、優れたアプリケーション アーキテクチャと設計の基礎について説明しています。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 41863c8d-f3a3-4584-ad86-b95094365e05
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mathoma
ms.openlocfilehash: e877f775c6a7bc83ce6c4a559c9f74b606c952fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359491"
---
# <a name="application-patterns-and-development-strategies-for-sql-server-on-azure-virtual-machines"></a>Azure Virtual Machines 上の SQL Server のアプリケーション パターンと開発計画
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="summary"></a>概要:
Azure 環境で SQL Server ベースのアプリケーションに使用するアプリケーション パターンを決めることは、設計上の重要な決断です。そのためには、SQL Server と Azure の各インフラストラクチャ コンポーネントを連携させる方法を確実に把握している必要があります。 Azure インフラストラクチャ サービスの SQL Server を使用すると、Windows Server 上に構築された既存の SQL Server アプリケーションを Azure の仮想マシン (VM) に簡単に移行し、管理および監視できます。

この記事の目的は、ソリューション設計者および開発者に、既存のアプリケーションを Azure に移行する際や Azure で新しいアプリケーションを開発する際に利用できる優れたアプリケーション アーキテクチャと設計の基礎を理解してもらうことです。

アプリケーション パターンごとに、オンプレミスのシナリオ、それぞれのクラウド対応ソリューション、および関連する技術的な推奨事項が示されています。 また、アプリケーションを正しく設計できるように、Azure 固有の開発計画についても説明します。 考えられるアプリケーション パターンはたくさんありますが、設計者および開発者には、アプリケーションやユーザーに最も適したパターンを選択することをお勧めします。

**技術寄稿者:** Luis Carlos Vargas Herring、Madhan Arumugam Ramakrishnan

**技術校閲者:** Corey Sanders、Drew McDaniel、Narayan Annamalai、Nir Mashkowski、Sanjay Mishra、Silvano Coriani、Stefan Schackow、Tim Hickey、Tim Wieman、Xin Jin

## <a name="introduction"></a>はじめに
さまざまなアプリケーション レイヤーのコンポーネントを異なるコンピューターや個々のコンポーネントに分離することで、さまざまな種類の n 層アプリケーションを開発できます。 たとえば、クライアント アプリケーションとビジネス ルールのコンポーネントを 1 台のコンピューターに、フロントエンド Web 層とデータ アクセス層のコンポーネントを別のコンピューターに、バックエンド データベース層をまた別のコンピューターに配置することができます。 この種の構成により、各層を互いに分離することができます。 データ ソースを変更する際に、クライアントや Web アプリケーションを変更する必要はなく、変更はデータ アクセス層のコンポーネントのみで済みます。

一般的な *n 層* アプリケーションには、プレゼンテーション層、ビジネス層、データ層があります。

| レベル | 説明 |
| --- | --- |
| **プレゼンテーション** |*プレゼンテーション層* (Web 層、フロントエンド層) は、ユーザーがアプリケーションとやり取りするレイヤーです。 |
| **勤務先** |*ビジネス層* (中間層) は、プレゼンテーション層とデータ層が相互に通信するために使用するレイヤーです。システムのコア機能が含まれています。 |
| **データ** |*データ層* は、基本的に、アプリケーションのデータを格納するサーバー (たとえば、SQL Server を実行するサーバー) です。 |

アプリケーションのレイヤーは、アプリケーションの機能とコンポーネントの論理的なグループを表します。一方、層は、個々の物理サーバー、コンピューター、ネットワーク、またはリモートの場所にある機能とコンポーネントの物理的な分散を表します。 アプリケーションのレイヤーは、同じ物理コンピューター (同じ層) に配置することや、別々のコンピューター (n 層) に分散させることができます。各レイヤー内のコンポーネントは、適切に定義されたインターフェイスを介して他のレイヤーのコンポーネントと通信します。 層という用語は、2 層、3 層、n 層など、物理的な分散パターンを指していると考えることができます。 **2 層アプリケーション パターン** には、アプリケーション サーバーとデータベース サーバーという 2 つのアプリケーション層があります。 アプリケーション サーバーとデータベース サーバー間の通信は、直接行われます。 アプリケーション サーバーには、Web 層とビジネス層の両方のコンポーネントが含まれています。 **3 層アプリケーション パターン** には、Web サーバー、アプリケーション サーバー (ビジネス ロジック層やビジネス層のデータ アクセス コンポーネントが含まれる)、およびデータベース サーバーという 3 つのアプリケーション層があります。 Web サーバーとデータベース サーバー間の通信は、アプリケーション サーバーを介して行われます。 アプリケーションのレイヤーと層の詳細については、『[Microsoft アプリケーション アーキテクチャ ガイド](/previous-versions/msp-n-p/ff650706(v=pandp.10))』をご覧ください。

この記事を読む前に、SQL Server と Azure の基本的な概念に関する知識が必要です。 詳細については、[SQL Server オンライン ブック](/sql/sql-server/)、[Azure Virtual Machines 上の SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md) に関するページ、および [Azure.com](https://azure.microsoft.com/) を参照してください。

この記事では、単純なアプリケーションおよび非常に複雑なエンタープライズ アプリケーションに適したいくつかのアプリケーション パターンについて説明します。 各パターンの詳細を読む前に、[Azure Storage](../../../storage/common/storage-introduction.md)、[Azure SQL Database](../../database/sql-database-paas-overview.md)、[Azure の仮想マシン内の SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md) など、Azure で利用可能なデータ ストレージ サービスについて理解しておくことをお勧めします。 アプリケーションの設計に関して最適な決断を下すには、いつ、どのデータ ストレージ サービスを使用するかを明確に把握する必要があります。

### <a name="choose-sql-server-on-azure-virtual-machines-when"></a>次の場合に、Azure Virtual Machines 上の SQL Server を選択します。

* SQL Server および Windows を制御する必要がある。 たとえば、制御対象には、SQL Server バージョン、特別な修正プログラム、パフォーマンスの構成などがあります。
* SQL Server と完全な互換性が必要で、既存のアプリケーションをそのまま Azure に移行する。
* Azure 環境の機能を活用したいが、Azure SQL Database ではアプリケーションに必要なすべての機能をサポートしていない。 これには、次のような点が関係します。
  
  * **データベースのサイズ**: この記事の更新時点では、SQL Database は最大で 1 TB のデータのデータベースをサポートしています。 アプリケーションで 1 TB を超すデータが必要で、カスタム シャーディング ソリューションを実装したくない場合は、Azure の仮想マシン内の SQL Server を使用することをお勧めします。 最新情報については、[Azure SQL Database のスケール アウト](/previous-versions/azure/dn495641(v=azure.100))、[DTU ベースの購入モデル](../../database/service-tiers-dtu.md)、および[仮想コアベースの購入モデル](../../database/service-tiers-vcore.md) (プレビュー) に関する記事を参照してください。
  * **HIPAA のコンプライアンス**: Azure Virtual Machines 上の SQL Server は HIPAA Business Associate Agreement (BAA) の対象になっているため、医療関係のお客様や独立系ソフトウェア ベンダー (ISV) は、[Azure SQL Database](../../database/sql-database-paas-overview.md) の代わりに、[Azure Virtual Machines 上の SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md) を選択することをお勧めします。 コンプライアンスについては、「[Microsoft Azure セキュリティ センター: コンプライアンス](https://azure.microsoft.com/support/trust-center/compliance/)」を参照してください。
  * **インスタンス レベルの機能**: 現時点で、SQL Database では、データベース外部の機能 (リンク サーバー、エージェント ジョブ、FileStream、Service Broker など) をサポートしていません。 詳細については、「 [Azure SQL Database のガイドラインと制限事項](/previous-versions/azure/ff394102(v=azure.100))」を参照してください。

## <a name="1-tier-simple-single-virtual-machine"></a>1 層 (単純):1 つの仮想マシン
このアプリケーション パターンでは、SQL Server アプリケーションとデータベースを Azure のスタンドアロンの仮想マシンにデプロイします。 同じ仮想マシンに、クライアントや Web アプリケーション、ビジネス コンポーネント、データ アクセス レイヤー、およびデータベース サーバーが含まれます。 プレゼンテーション、ビジネス、データ アクセス コードは、論理的に分離されますが、1 台のサーバー コンピューターに物理的に配置されます。 ほとんどのお客様は、このアプリケーション パターンから開始し、その後、システムに Web ロールや仮想マシンを追加することでスケールアウトします。

このアプリケーション パターンは、次のような場合に便利です。

* Azure プラットフォームがアプリケーションの要件に対応しているかどうかを評価するために、プラットフォームへの簡単な移行を実行する。
* 層の間の待機時間を減らすために、同じ仮想マシンでホストされているすべてのアプリケーション層を同じ Azure データ センターに保持する。
* 開発をすばやくプロビジョニングし、短期間の環境テストを実施する。
* さまざまなワークロード レベルのストレス テストを実行したいが、多数の物理コンピューターをずっと所有して管理したくない。

次の図には、単純なオンプレミスのシナリオと、そのクラウド対応ソリューションを Azure の単一の仮想マシンにデプロイする方法を示します。

![1 層アプリケーション パターン](./media/application-patterns-development-strategies/IC728008.png)

スケーラビリティやセキュリティ上の理由から個別の層を使用する必要がない限り、プレゼンテーション レイヤーと同じ物理層にビジネス レイヤー (ビジネス ロジックおよびデータ アクセス コンポーネント) をデプロイすると、アプリケーションのパフォーマンスを最大限に引き出すことができます。

これは出発点として非常に一般的なパターンです。データを SQL Server VM に移行する際は、移行に関する次の記事が役に立ちます: [Azure VM の SQL Server へのデータベースの移行](migrate-to-vm-from-sql-server.md)。

## <a name="3-tier-simple-multiple-virtual-machines"></a>3 層 (単純):複数の仮想マシン
このアプリケーション パターンでは、各アプリケーション層を別々の仮想マシンに配置して、3 層アプリケーションを Azure にデプロイします。 こうすると、簡単にスケールアップおよびスケールアウトできる柔軟な環境が実現します。 1 台の仮想マシンにクライアントや Web アプリケーションを配置し、別の 1 台でビジネス コンポーネントをホストして、また別の 1 台でデータベース サーバーをホストします。

このアプリケーション パターンは、次のような場合に便利です。

* 複雑なデータベース アプリケーションを Azure Virtual Machines に移行する。
* 異なるアプリケーション層を別々のリージョンでホストする。 たとえば、レポート目的で共有データベースを複数のリージョンにデプロイできます。
* エンタープライズ アプリケーションをオンプレミスの仮想化プラットフォームから Azure Virtual Machines に移行する。 エンタープライズ アプリケーションの詳細については、 [エンタープライズ アプリケーションの概要](/previous-versions/visualstudio/aa267045(v=vs.60))に関するページを参照してください。
* 開発をすばやくプロビジョニングし、短期間の環境テストを実施する。
* さまざまなワークロード レベルのストレス テストを実行したいが、多数の物理コンピューターをずっと所有して管理したくない。

次の図には、各アプリケーション層を別々の仮想マシンに配置して、単純な 3 層アプリケーションを Azure に配置する方法を示します。

![3 層アプリケーション パターン](./media/application-patterns-development-strategies/IC728009.png)

このアプリケーション パターンでは、層ごとに 1 台だけ仮想マシンがあります。 Azure に複数の VM がある場合は、仮想ネットワークをセットアップすることをお勧めします。 [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) は、信頼できるセキュリティ境界を作成し、VM 間でプライベート IP アドレスを使用して通信できるようにします。 さらに、必ず、すべてのインターネット接続がプレゼンテーション層にのみ転送されるようにします。 このアプリケーション パターンに従う場合は、ネットワーク セキュリティ グループのルールを管理してアクセスを制御します。 詳しくは、「[Azure Portal を使用して VM への外部アクセスを許可する方法](../../../virtual-machines/windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」に関する記事をご覧ください。

この図では、インターネット プロトコルには、TCP、UDP、HTTP、または HTTPS を使用できます。

> [!NOTE]
> Azure の仮想ネットワークは無料でセットアップできます。 ただし、オンプレミスに接続する VPN ゲートウェイについては料金が発生します。 この料金は、接続がプロビジョニングされて使用可能になっている時間に基づいて課金されます。
> 
> 

## <a name="2-tier-and-3-tier-with-presentation-tier-scale-out"></a>2 層および 3 層 (プレゼンテーション層のスケールアウト)
このアプリケーション パターンでは、各アプリケーション層を別々の仮想マシンに配置して、2 層または 3 層データベース アプリケーションを Azure Virtual Machines にデプロイします。 さらに、着信クライアント要求の量の増加に伴い、プレゼンテーション層をスケールアウトします。

このアプリケーション パターンは、次のような場合に便利です。

* エンタープライズ アプリケーションをオンプレミスの仮想化プラットフォームから Azure Virtual Machines に移行する。
* 着信クライアント要求の量の増加に伴い、プレゼンテーション層をスケールアウトする。
* 開発をすばやくプロビジョニングし、短期間の環境テストを実施する。
* さまざまなワークロード レベルのストレス テストを実行したいが、多数の物理コンピューターをずっと所有して管理したくない。
* 必要に応じてスケールアップおよびスケールダウンできるインフラストラクチャ環境を所有したい。

次の図には、着信クライアント要求の量の増加に伴い、プレゼンテーション層をスケールアウトして、アプリケーション層を Azure の複数の仮想マシンに配置する方法を示します。 この図に示すように、Azure Load Balancer が複数の仮想マシンにトラフィックを分散し、さらに、接続する Web サーバーを決定します。 ロード バランサーの背後に Web サーバーのインスタンスを複数配置すると、プレゼンテーション層の高可用性が確保されます。

![アプリケーション パターン - プレゼンテーション層のスケールアウト](./media/application-patterns-development-strategies/IC728010.png)

### <a name="best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier"></a>1 つの層に複数の VM を配置する 2 層、3 層、または n 層パターンのベスト プラクティス
同じ層に属する仮想マシンを同じクラウド サービスおよび同じ可用性セットに配置することをお勧めします。 たとえば、一連の Web サーバーを **CloudService1** と **AvailabilitySet1** に配置し、一連のデータベース サーバーを **CloudService2** と **AvailabilitySet2** に配置します。 Azure の可用性セットを使用すると、高可用性ノードを別個の障害ドメインとアップグレード ドメインに配置できるようになります。

1 つの層の複数の VM インスタンスを活用するには、アプリケーション層の間に Azure Load Balancer を構成する必要があります。 各層で Load Balancer を構成するには、各層の VM に個別に負荷分散エンドポイントを作成します。 まず特定の層の VM を同じクラウド サービスに作成します。 これにより、すべての VM で同じパブリック仮想 IP アドレスを使用するようになります。 次に、その層の仮想マシンの 1 つにエンドポイントを作成します。 その後、負荷分散のために同じエンドポイントをその層の他の仮想マシンに割り当てます。 負荷分散セットを作成すると、複数の仮想マシンにトラフィックが分散され、バックエンド VM ノードに障害が発生したときに接続するノードを Load Balancer が決定できるようになります。 たとえば、ロード バランサーの背後に Web サーバーのインスタンスを複数配置すると、プレゼンテーション層の高可用性が確保されます。

ベスト プラクティスとして、必ず、すべてのインターネット接続がプレゼンテーション層にのみ転送されるようにします。 プレゼンテーション レイヤーがビジネス層にアクセスし、ビジネス層がデータ層にアクセスします。 プレゼンテーション層へのアクセスを許可する方法の詳細については、「[Azure ポータルを使用して VM への外部アクセスを許可する](../../../virtual-machines/windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。

Azure Load Balancer はオンプレミスの環境のロード バランサーと同じような動作をします。 詳細については、「[Azure インフラストラクチャ サービスの負荷分散](../../../virtual-machines/windows/tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。

また、Azure Virtual Network を使用して、仮想マシン用のプライベート ネットワークをセットアップすることもお勧めします。 これにより、仮想マシン間でプライベート IP アドレスを使用して通信できるようになります。 詳細については、 [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md)に関するページを参照してください。

## <a name="2-tier-and-3-tier-with-business-tier-scale-out"></a>2 層および 3 層 (ビジネス層のスケールアウト)
このアプリケーション パターンでは、各アプリケーション層を別々の仮想マシンに配置して、2 層または 3 層データベース アプリケーションを Azure Virtual Machines にデプロイします。 また、アプリケーションの複雑さに伴い、アプリケーション サーバー コンポーネントを複数の仮想マシンに分散することもできます。

このアプリケーション パターンは、次のような場合に便利です。

* エンタープライズ アプリケーションをオンプレミスの仮想化プラットフォームから Azure Virtual Machines に移行する。
* アプリケーションの複雑さに伴い、アプリケーション サーバー コンポーネントを複数の仮想マシンに分散する。
* ビジネス ロジックの負荷が高いオンプレミスの LOB (基幹業務) アプリケーションを Azure Virtual Machines に移行する。 LOB アプリケーションとは、アカウンティング、人事 (HR)、給与処理、サプライ チェーン管理、リソース計画アプリケーションなど、企業の運営に欠かせない一連の重要なコンピューター アプリケーションです。
* 開発をすばやくプロビジョニングし、短期間の環境テストを実施する。
* さまざまなワークロード レベルのストレス テストを実行したいが、多数の物理コンピューターをずっと所有して管理したくない。
* 必要に応じてスケールアップおよびスケールダウンできるインフラストラクチャ環境を所有したい。

次の図には、オンプレミスのシナリオとそのクラウド対応ソリューションを示します。 このシナリオでは、ビジネス ロジック層とデータ アクセス コンポーネントを含むビジネス層をスケールアウトして、アプリケーション層を Azure の複数の仮想マシンに配置します。 この図に示すように、Azure Load Balancer が複数の仮想マシンにトラフィックを分散し、さらに、接続する Web サーバーを決定します。 ロード バランサーの背後にアプリケーション サーバーのインスタンスを複数配置すると、ビジネス層の高可用性が確保されます。 詳細については、「 [1 つの層に複数の VM を配置する 2 層、3 層、または n 層パターンのベスト プラクティス](#best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier)」を参照してください。

![アプリケーション パターン (ビジネス層のスケールアウト)](./media/application-patterns-development-strategies/IC728011.png)

## <a name="2-tier-and-3-tier-with-presentation-and-business-tiers-scale-out-and-hadr"></a>2 層および 3 層 (プレゼンテーション層とビジネス層のスケールアウトおよび HADR)
このアプリケーション パターンでは、プレゼンテーション層 (Web サーバー) とビジネス層 (アプリケーション サーバー) のコンポーネントを複数の仮想マシンに分散して、2 層または 3 層データベース アプリケーションを Azure Virtual Machines にデプロイします。 また、Azure Virtual Machines にデータベースの高可用性とディザスター リカバリー (HADR) ソリューションを実装します。

このアプリケーション パターンは、次のような場合に便利です。

* SQL Server の高可用性とディザスター リカバリー機能を実装することで、エンタープライズ アプリケーションをオンプレミスの仮想化プラットフォームから Azure に移行する。
* 着信クライアント要求の量の増加とアプリケーションの複雑さに伴い、プレゼンテーション層とビジネス層をスケールアウトする。
* 開発をすばやくプロビジョニングし、短期間の環境テストを実施する。
* さまざまなワークロード レベルのストレス テストを実行したいが、多数の物理コンピューターをずっと所有して管理したくない。
* 必要に応じてスケールアップおよびスケールダウンできるインフラストラクチャ環境を所有したい。

次の図には、オンプレミスのシナリオとそのクラウド対応ソリューションを示します。 このシナリオでは、プレゼンテーション層とビジネス層のコンポーネントを Azure の複数の仮想マシンにスケールアウトします。 また、Azure に SQL Server データベースの高可用性とディザスター リカバリー (HADR) 手法を実装します。

アプリケーションの複数のコピーを別々の VM で実行すると、VM 間で要求が負荷分散されます。 複数の仮想マシンがある場合は、単一時点ですべての VM が稼働し、アクセスできるようにする必要があります。 負荷分散を構成すると、Azure Load Balancer は VM の正常性を追跡し、着信呼び出しを、正常に機能している VM ノードに適切に転送します。 仮想マシンの負荷分散を設定する方法の詳細については、「[Azure インフラストラクチャ サービスの負荷分散](../../../virtual-machines/windows/tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。 ロード バランサーの背後に Web サーバーとアプリケーション サーバーのインスタンスを複数配置すると、プレゼンテーション層とビジネス層の高可用性が確保されます。

![スケールアウトと高可用性](./media/application-patterns-development-strategies/IC728012.png)

### <a name="best-practices-for-application-patterns-requiring-sql-hadr"></a>SQL HADR を必要とするアプリケーション パターンのベスト プラクティス
Azure Virtual Machines で SQL Server の高可用性とディザスター リカバリー ソリューションをセットアップする場合は、[Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) を使用して、仮想マシン用の仮想ネットワークをセットアップする必要があります。 Virtual Network 内の仮想マシンにはサービスのダウンタイム後でも安定したプライベート IP アドレスが割り当てられるため、DNS 名前解決に必要な更新時間を回避できます。 また、仮想ネットワークを使用すると、オンプレミスのネットワークを Azure に拡張でき、信頼できるセキュリティ境界が作成されます。 たとえば、アプリケーションに企業ドメイン制約 (Windows 認証、Active Directory など) が設定されている場合は、 [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) をセットアップする必要があります。

Azure で運用コードを実行しているお客様のほとんどは Azure にプライマリとセカンダリの両方のレプリカを保持しています。

高可用性とディザスター リカバリーの手法に関する包括的な情報とチュートリアルについては、[Azure Virtual Machines 上の SQL Server の高可用性とディザスター リカバリー](business-continuity-high-availability-disaster-recovery-hadr-overview.md)に関するページを参照してください。

## <a name="2-tier-and-3-tier-using-azure-virtual-machines-and-cloud-services"></a>Azure Virtual Machines と Cloud Services を使用した 2 層と 3 層
このアプリケーション パターンでは、[Azure Cloud Services](../../../cloud-services/cloud-services-choose-me.md) (Web ロールと worker ロール - サービスとしてのプラットフォーム (PaaS)) と [Azure Virtual Machines](../../../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (サービスとしてのインフラストラクチャ (IaaS)) の両方を使用して、2 層または 3 層アプリケーションを Azure にデプロイします。 プレゼンテーション層とビジネス層に [Azure Cloud Services](https://azure.microsoft.com/documentation/services/cloud-services/) を使用し、データ層に [Azure Virtual Machines](../../../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 内の SQL Server を使用すると、Azure で実行されるほとんどのアプリケーションにとってメリットがあります。 その理由は、Cloud Services で実行されるコンピューティング インスタンスにより、管理、デプロイ、監視、およびスケールアウトが簡単になるためです。

Cloud Services を使用すると、Azure がユーザーに代わってインフラストラクチャの保守と日常的な保守を行い、オペレーティング システムにパッチを適用し、サービスとハードウェアの障害時には回復を図ります。 アプリケーションでスケールアウトが必要な場合、クラウド サービス プロジェクトでは、アプリケーションで使用するインスタンスまたは仮想マシンの数を増減させることで、自動および手動のスケールアウト オプションを使用できます。 また、オンプレミスの Visual Studio を使用して、アプリケーションを Azure のクラウド サービス プロジェクトにデプロイすることもできます。

まとめると、アプリケーションでソフトウェアやオペレーティング システムの複雑な構成が必要なく、プレゼンテーション層やビジネス層に対する広範な管理タスクを負わないようにしたい場合は、Azure Cloud Services を使用します。 必要なすべての機能を Azure SQL Database がサポートしていない場合は、データ層に Azure の仮想マシン内の SQL Server を使用します。 Azure Cloud Services でアプリケーションを実行し、Azure Virtual Machines にデータを格納すると、両方のサービスのメリットが得られます。 詳細な比較については、このトピックの [Azure の開発計画の比較](#comparing-web-development-strategies-in-azure)に関するセクションを参照してください。

このアプリケーション パターンでは、プレゼンテーション層に Web ロールが含まれています。このロールは、Azure 実行環境で実行される Cloud Services コンポーネントで、IIS および ASP.NET でサポートされている Web アプリケーション プログラミング用にカスタマイズされています。 ビジネス層またはバックエンド層には、worker ロールが含まれています。このロールは、Azure 実行環境で実行される Cloud Services コンポーネントで、一般的な開発に役立ち、Web ロールのバックグラウンド処理を実行できます。 データベース層は、Azure の SQL Server 仮想マシンに配置されています。 プレゼンテーション層とデータベース層の間の通信は、直接またはビジネス層 (worker ロール コンポーネント) を介して行われます。

このアプリケーション パターンは、次のような場合に便利です。

* SQL Server の高可用性とディザスター リカバリー機能を実装することで、エンタープライズ アプリケーションをオンプレミスの仮想化プラットフォームから Azure に移行する。
* 必要に応じてスケールアップおよびスケールダウンできるインフラストラクチャ環境を所有したい。
* アプリケーションまたはデータベースに必要なすべての機能を Azure SQL Database がサポートしていない。
* さまざまなワークロード レベルのストレス テストを実行したいが、多数の物理コンピューターをずっと所有して管理したくない。

次の図には、オンプレミスのシナリオとそのクラウド対応ソリューションを示します。 このシナリオでは、プレゼンテーション層を Web ロールに、ビジネス層を worker ロールに配置していますが、データ層は Azure の仮想マシンに配置しています。 プレゼンテーション層の複数のコピーを別々の Web ロールで実行すると、Web ロール間で要求が負荷分散されます。 Azure Cloud Services と Azure Virtual Machines を組み合わせる場合は、 [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) もセットアップすることをお勧めします。 [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md)を使用すると、クラウドの同じクラウド サービス内で安定した永続的なプライベート IP アドレスを利用できます。 仮想マシンとクラウド サービス用の仮想ネットワークを定義すると、プライベート IP アドレスで通信を開始できるようになります。 さらに、仮想マシンと Azure の web/worker ロールを同じ [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) に配置すると、待機時間が短くなり、接続の安全性が向上します。 詳細については、「 [クラウド サービスとは](../../../cloud-services/cloud-services-choose-me.md)」を参照してください。

この図に示すように、Azure Load Balancer が複数の仮想マシンにトラフィックを分散し、さらに、接続するアプリケーション サーバーを決定します。 ロード バランサーの背後に Web サーバーとアプリケーション サーバーのインスタンスを複数配置すると、プレゼンテーション層とビジネス層の高可用性が確保されます。 詳細については、「 [SQL HADR を必要とするアプリケーション パターンのベスト プラクティス](#best-practices-for-application-patterns-requiring-sql-hadr)」を参照してください。

![Azure 仮想ネットワーク内で Azure ロード バランサーを介して Web ロール インスタンスに接続されているオンプレミスの物理マシンまたは仮想マシンを示す図。](./media/application-patterns-development-strategies/IC728013.png)

このアプリケーション パターンを実装する別の方法としては、次の図に示すように、プレゼンテーション層とビジネス層の両方のコンポーネントを含む統合 Web ロールを使用します。 このアプリケーション パターンは、ステートフルな設計を必要とするアプリケーションに有効です。 Azure では Web ロールと worker ロールでステートレスなコンピューティング ノードが提供されるため、次のいずれかのテクノロジを使用して、セッション状態を格納するロジックを実装することをお勧めします: [Azure Caching](https://azure.microsoft.com/documentation/services/azure-cache-for-redis/)、[Azure Table Storage](../../../cosmos-db/tutorial-develop-table-dotnet.md)、[Azure SQL Database](../../database/sql-database-paas-overview.md)。

![Azure 仮想ネットワーク内で統合 Web および worker ロール インスタンスに接続されているオンプレミスの物理マシンまたは仮想マシンを示す図。](./media/application-patterns-development-strategies/IC728014.png)

## <a name="pattern-with-azure-virtual-machines-azure-sql-database-and-azure-app-service-web-apps"></a>Azure Virtual Machines、Azure SQL Database、Azure App Service を使用したパターン (Web Apps)
このアプリケーション パターンの主な目的は、ソリューション内で Azure のサービスとしてのインフラストラクチャ (IaaS) コンポーネントと Azure のサービスとしてのプラットフォーム (PaaS) コンポーネントを組み合わせる方法を示すことです。 このパターンは、リレーショナル データ ストレージ向けの Azure SQL Database に焦点を当てています。 Azure の仮想マシン内の SQL Server は含まれません。これは、Azure のサービスとしてのインフラストラクチャ プランで提供されます。

このアプリケーション パターンでは、プレゼンテーション層とビジネス層を同じ仮想マシンに配置し、Azure SQL Database (SQL Database) サーバーのデータベースにアクセスして、データベース アプリケーションを Azure にデプロイします。 従来の IIS ベースの Web ソリューションを使用して、プレゼンテーション層を実装できます。 または、[Azure App Service](https://azure.microsoft.com/documentation/services/app-service/web/) を使用して、プレゼンテーション層とビジネス層を結合して実装することもできます。

このアプリケーション パターンは、次のような場合に便利です。

* Azure で構成されている既存の SQL Database サーバーが既にあり、アプリケーションをすばやくテストする。
* Azure 環境の機能をテストする。
* 開発をすばやくプロビジョニングし、短期間の環境テストを実施する。
* ビジネス ロジックとデータ アクセス コンポーネントを Web アプリケーション内で自己完結型にすることができる。

次の図には、オンプレミスのシナリオとそのクラウド対応ソリューションを示します。 このシナリオでは、アプリケーション層を Azure の単一の仮想マシンに配置し、Azure SQL Database 内のデータにアクセスします。

![混合アプリケーション パターン](./media/application-patterns-development-strategies/IC728015.png)

Azure Web Apps を使用して Web 層とアプリケーション層を結合して実装する場合は、Web アプリケーションのコンテキストで中間層またはアプリケーション層をダイナミック リンク ライブラリ (DLL) として保持することをお勧めします。

また、プログラミング方法の詳細については、この記事の末尾にある「 [Azure の開発計画の比較](#comparing-web-development-strategies-in-azure) 」に記載されている推奨事項を参照してください。

## <a name="n-tier-hybrid-application-pattern"></a>n 層ハイブリッド アプリケーション パターン
n 層ハイブリッド アプリケーション パターンでは、オンプレミスと Azure に分散した複数の層にアプリケーションを実装します。 そのため、柔軟で再利用可能なハイブリッド システムが構築され、他の層を変更することなく特定の層を変更または追加できます。 企業ネットワークをクラウドに拡張するには、 [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) サービスを使用します。

このハイブリッド アプリケーション パターンは、次のような場合に便利です。

* 構築したアプリケーションの一部をクラウドで、一部をオンプレミスで実行する。
* 既存のオンプレミスのアプリケーションの一部またはすべての要素をクラウドに移行する。
* エンタープライズ アプリケーションをオンプレミスの仮想化プラットフォームから Azure に移行する。
* 必要に応じてスケールアップおよびスケールダウンできるインフラストラクチャ環境を所有したい。
* 開発をすばやくプロビジョニングし、短期間の環境テストを実施する。
* エンタープライズ データベース アプリケーションのバックアップを実行するコスト効率に優れた方法を必要としている。

次の図には、オンプレミスと Azure に分散した n 層ハイブリッド アプリケーション パターンを示します。 図に示すように、オンプレミスのインフラストラクチャには、ユーザーの認証と承認をサポートするために、[Active Directory Domain Services](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831484(v=ws.11)) ドメイン コントローラーが含まれています。 図に示すシナリオでは、データ層の一部がオンプレミスのデータ センターに、一部が Azure に存在していることに注意してください。 アプリケーションのニーズに応じて、他のさまざまなハイブリッド シナリオを実装できます。 たとえば、プレゼンテーション層とビジネス層をオンプレミスの環境に保持したまま、データ層を Azure に配置できます。

![N 層アプリケーション パターン](./media/application-patterns-development-strategies/IC728016.png)

Azure では、Active Directory を組織のスタンドアロン クラウド ディレクトリとして使用することや、既存のオンプレミスの Active Directory を [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/) と統合することもできます。 図に示すように、ビジネス層のコンポーネントは、複数のデータ ソースにアクセスできます。たとえば、プライベート内部 IP アドレスを使用して [Azure の SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md) に、[Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) を使用してオンプレミスの SQL Server に、.NET Framework データ プロバイダー テクノロジを使用して [SQL Database](../../database/sql-database-paas-overview.md) にアクセスすることができます。 この図では、Azure SQL Database はオプションのデータ ストレージ サービスです。

n 層ハイブリッド アプリケーション パターンでは、指定の順序で次のワークフローを実装できます。

1. [Microsoft Assessment and Planning (MAP) Toolkit](https://microsoft.com/map)を使用して、クラウドに移行する必要があるエンタープライズ データベース アプリケーションを特定します。 MAP Toolkit では、仮想化を検討しているコンピューターからインベントリとパフォーマンス データを収集し、容量と評価の計画に関する推奨事項を提示します。
2. ストレージ アカウントや仮想マシンなど、Azure プラットフォームで必要なリソースと構成を計画します。
3. オンプレミスの企業ネットワークと [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md)間のネットワーク接続をセットアップします。 オンプレミスの企業ネットワークと Azure の仮想マシン間の接続をセットアップするには、次の 2 つの方法のどちらかを使用します。
   
   1. Azure の仮想マシンのパブリック エンドポイントを介して、オンプレミスと Azure 間の接続を確立する。 この方法はセットアップが簡単で、仮想マシンで SQL Server 認証を使用することができます。 また、ネットワーク セキュリティ グループのルールをセットアップして、VM へのパブリック トラフィックを制御します。 詳しくは、「[Azure Portal を使用して VM への外部アクセスを許可する方法](../../../virtual-machines/windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」に関する記事をご覧ください。
   2. Azure 仮想プライベート ネットワーク (VPN) トンネルを介して、オンプレミスと Azure 間の接続を確立する。 この方法を使用すると、ドメイン ポリシーを Azure の仮想マシンに拡張できます。 また、ファイアウォール規則を設定し、仮想マシンで Windows 認証を使用することもできます。 現時点では、Azure はセキュリティで保護されたサイト間 VPN 接続とポイント対サイト VPN 接続をサポートしています。
      
      * セキュリティで保護されたサイト間接続を使用すると、オンプレミスのネットワークと Azure の仮想ネットワーク間のネットワーク接続を確立できます。 オンプレミスのデータ センター環境を Azure に接続することをお勧めします。
      * セキュリティで保護されたポイント対サイト接続を使用すると、Azure の仮想ネットワークと場所を問わず稼働している個々のコンピューター間のネットワーク接続を確立できます。 この接続は、ほとんどの場合、開発およびテスト目的で使用することをお勧めします。
      
      Azure の SQL Server に接続する方法の詳細については、「[Azure 上にある SQL Server 仮想マシンへの接続](ways-to-connect-to-sql.md)」を参照してください。
4. オンプレミスのデータを Azure の仮想マシン ディスクにバックアップするようにスケジュールされたジョブとアラートを設定します。 詳細については、[Azure Blob Storage を使用した SQL Server のバックアップと復元](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)に関するページと「[Azure Virtual Machines における SQL Server のバックアップと復元](../../../azure-sql/virtual-machines/windows/backup-restore.md)」を参照してください。
5. アプリケーションのニーズに応じて、次の 3 つの一般的なシナリオのいずれかを実装できます。
   
   1. Web サーバー、アプリケーション サーバー、およびデータベース サーバー内の非機密データを Azure に保持し、機密データをオンプレミスに保持する。
   2. Web サーバーとアプリケーション サーバーをオンプレミスに保持し、データベース サーバーを Azure の仮想マシンに保持する。
   3. データベース サーバー、Web サーバー、およびアプリケーション サーバーをオンプレミスに保持し、データベース レプリカを Azure の仮想マシンに保持する。 この設定では、オンプレミスの Web サーバーまたはレポート アプリケーションが Azure のデータベース レプリカにアクセスできます。 そのため、オンプレミスのデータベース内のワークロードの削減を実現することができます。 このシナリオは読み取り負荷の高いワークロードおよび開発目的で実装することをお勧めします。 Azure でデータベース レプリカを作成する方法の詳細については、[Azure Virtual Machines 上の SQL Server の高可用性とディザスター リカバリー](business-continuity-high-availability-disaster-recovery-hadr-overview.md)に関するページの Always On 可用性グループの説明を参照してください。

## <a name="comparing-web-development-strategies-in-azure"></a>Azure の開発計画の比較
SQL Server ベースの多層アプリケーションを Azure に実装およびデプロイする場合は、次の 2 つのプログラミング方法のどちらかを使用できます。

* Azure で従来の Web サーバー (IIS - インターネット インフォメーション サービス) を設定し、Azure Virtual Machines 上の SQL Server データベースにアクセスする。
* Azure にクラウド サービスを実装およびデプロイする。 その後、このクラウド サービスが Azure Virtual Machines 上の SQL Server データベースにアクセスできることを確認してください。 クラウド サービスには、複数の Web ロールおよび worker ロールを含めることができます。

次の表では、Azure Virtual Machines 上の SQL Server について、従来の Web 開発、Azure Cloud Services、および Azure Web Apps を比較しています。 この表に Azure Web Apps が含まれているのは、Azure Web Apps では、パブリック仮想 IP アドレスまたは DNS 名を使用して Azure VM 内の SQL Server を Azure Web Apps のデータ ソースとして利用できるためです。

|  | Azure Virtual Machines での従来の Web 開発 | Azure 内のクラウド サービス | Azure Web Apps を使用した Web ホスティング |
| --- | --- | --- | --- |
| **オンプレミスからのアプリケーションの移行** |既存のアプリケーションはそのまま。 |アプリケーションには Web ロールと worker ロールが必要です。 |既存のアプリケーションはそのままですが、迅速な拡張性を必要とする自己完結型の Web アプリケーションと Web サービスが適しています。 |
| **開発とデプロイ** |Visual Studio、WebMatrix、Visual Web Developer、WebDeploy、FTP、TFS、IIS マネージャー、PowerShell。 |Visual Studio、Azure SDK、TFS、PowerShell。 各クラウド サービスには、ステージング環境と運用環境という、サービス パッケージと構成をデプロイできる環境が 2 つ用意されています。 クラウド サービスは、まずステージング環境にデプロイしてテストした後、運用環境に昇格させることができます。 |Visual Studio、WebMatrix、Visual Web Developer、FTP、GIT、BitBucket、CodePlex、DropBox、GitHub、Mercurial、TFS、Web Deploy、PowerShell。 |
| **管理と設定** |アプリケーション、データ、ファイアウォール規則、仮想ネットワーク、およびオペレーティング システムに対する管理作業を行う必要があります。 |アプリケーション、データ、ファイアウォール規則、および仮想ネットワークに対する管理作業を行う必要があります。 |アプリケーションとデータのみに対する管理作業を行う必要があります。 |
| **高可用性とディザスター リカバリー (HADR)** |仮想マシンを同じ可用性セットと同じクラウド サービス内に配置することをお勧めします。 VM を同じ可用性セットに保持すると、Azure で高可用性ノードを別個の障害ドメインとアップグレード ドメインに配置できるようになります。 同様に、同じクラウド サービスに VM を保持すると負荷を分散することができ、複数の VM がAzure データ センター内のローカル ネットワーク を介して直接相互通信できます。<br/><br/>Azure Virtual Machines 上の SQL Server でダウンタイムが発生しないようにするために、高可用性とディザスター リカバリー ソリューションを実装する責任があります。 サポートされている HADR テクノロジについては、[Azure Virtual Machines 上の SQL Server の高可用性とディザスター リカバリー](business-continuity-high-availability-disaster-recovery-hadr-overview.md)に関するページを参照してください。<br/><br/>ご自身のデータやアプリケーションをバックアップする責任も負うことになります。<br/><br/>ハードウェアの問題によりデータ センター のホストマシンに障害が発生した場合、Azure では仮想マシンを移動できます。 また、セキュリティやソフトウェアの更新のためにホスト コンピューターを更新するときなど、VM の計画的なダウンタイムが発生する場合があります。 そのため、継続的な可用性を確保するために、アプリケーション層ごとに少なくとも 2 台の VM を保持することをお勧めします。 Azure では、単一の仮想マシンに対する SLA は提供していません。 |基になるハードウェアやオペレーティング システム ソフトウェアに起因するエラーは Azure で管理されます。 アプリケーションの高可用性を確保するために、Web ロールまたは worker ロールの複数のインスタンスを実装することをお勧めします。 詳しくは、[Cloud Services、Virtual Machines、および Virtual Network Service のサービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)に関するページをご覧ください。<br/><br/>ご自身のデータやアプリケーションをバックアップする責任も負うことになります。<br/><br/>Azure VM の SQL Server データ ベースにあるデータ ベースでダウンタイムが発生しないようにするため、高可用性とディザスター リカバリー ソリューションを実装する責任があります。 サポートされている HDAR テクノロジについては、Azure Virtual Machines 上の SQL Server の高可用性とディザスター リカバリーに関するページを参照してください。<br/><br/>**SQL Server データベース ミラーリング**: Azure Cloud Services (Web/worker ロール) を使用。 SQL Server VM とクラウド サービス プロジェクトは、同じ Azure Virtual Network 内に配置できます。 SQL Server VM が同じ Virtual Network 内にない場合は、SQL Server のインスタンスに通信をルーティングするために、SQL Server エイリアスを作成する必要があります。 さらに、エイリアス名は SQL Server 名と一致する必要もあります。 |高可用性は、Azure worker ロール、Azure Blob Storage、および Azure SQL Database から継承されます。 たとえば、Azure Storage では、すべての BLOB、テーブル、およびキューのデータの 3 つのレプリカを保持します。 Azure SQL Database では、常に、実行中のデータの 3 つのレプリカ (1 つのプライマリ レプリカと 2 つのセカンダリ レプリカ) を保持します。 詳細については、「[Azure Storage](https://azure.microsoft.com/documentation/services/storage/)」と「[Azure SQL Database](../../database/sql-database-paas-overview.md)」をご覧ください。<br/><br/>Azure Web Apps のデータ ソースとして Azure VM 内の SQL Server を使用する場合は、Azure Web Apps では Azure Virtual Network がサポートされないことに注意してください。 つまり、Azure Web Apps から Azure の SQL Server VM への接続はすべて仮想マシンのパブリック エンドポイントを介して行う必要があります。 そのため、高可用性とディザスター リカバリーのシナリオが一部制限される可能性があります。 たとえば、データベース ミラーリングでは Azure の SQL Server ホスト VM 間に Azure Virtual Network をセットアップする必要があるため、データベース ミラーリングを使用している SQL Server VM に接続している Azure Web Apps のクライアント アプリケーションでは、新しいプライマリ サーバーに接続することができません。 そのため、**SQL Server データベース ミラーリング** と Azure Web Apps の併用は現在サポートされていません。<br/><br/>**SQL Server AlwaysOn 可用性グループ**: Azure で Azure Web Apps と SQL Server VM を使用するときに、AlwaysOn 可用性グループを設定できます。 ただし、負荷分散されたパブリック エンドポイントを介してプライマリ レプリカに通信をルーティングするように AlwaysOn 可用性グループ リスナーを構成する必要があります。 |
| **クロスプレミス接続** |Azure Virtual Network を使用して、オンプレミスに接続できます。 |Azure Virtual Network を使用して、オンプレミスに接続できます。 |Azure Virtual Network がサポートされています。 詳細については、 [Web Apps Virtual Network 統合](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/)に関する記事を参照してください。 |
| **スケーラビリティ** |スケールアップは、仮想マシンのサイズを大きくするか、ディスクを追加することで実現できます。 仮想マシンのサイズの詳細については、「[Azure の仮想マシンのサイズ](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。<br/><br/>**データベース サーバーの場合**: データベースのパーティション分割テクニックと SQL Server AlwaysOn 可用性グループによりスケールアウトできます。<br/><br/>読み取り負荷が高い場合、複数のセカンダリ ノードで[AlwaysOn 可用性グループ](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)を SQL Server のレプリケーションとして使用できます。<br/><br/>書き込み負荷が高い場合、複数の物理サーバー間で水平方向にデータのパーティション分割を実装して、アプリケーションをスケールアウトできます。<br/><br/>さらに、[SQL Server と データ依存ルーティング](/previous-versions/sql/sql-server-2005/administrator/cc966448(v=technet.10))を使用することでスケールアウトできます。 データ依存型ルーティング (DDR) を使用する場合は、クライアント アプリケーション (通常は、ビジネス層レイヤー) にパーティション分割メカニズムを実装して、データベース要求を複数の SQL Server ノードにルーティングする必要があります。 ビジネス層には、データのパーティション分割方法とデータが含まれているノードのマッピングが含まれています。<br/><br/>仮想マシンを実行しているアプリケーションの規模を設定できます。 詳細については、[アプリケーションのスケールの設定方法](../../../cloud-services/cloud-services-how-to-scale-portal.md)に関する記事をご覧ください。<br/><br/>**重要な注意**: Azure の **自動スケーリング** 機能を使用すると、アプリケーションで使用する仮想マシンの規模を自動的に拡張または縮小できます。 この機能は、ピーク時にエンド ユーザー エクスペリエンスに悪影響が出ないようにし、要求が少ないときには VM をシャットダウンします。 クラウド サービスに SQL Server VM が含まれている場合は、自動スケール オプションを設定しないことをお勧めします。 これは、自動スケール機能を使用すると、Azure が、仮想マシンの CPU 使用率がしきい値を超えるとその VM をオンにし、CPU 使用率がしきい値を下回ると仮想マシンをオフにできるようになるためです。 自動スケール機能は、VM が以前の状態を参照せずにワークロードを管理できる、Web サーバーなどのステートレスなアプリケーションに役立ちます。 ただし、1 つのインスタンスだけがデータベースに書き込むことができる、SQL Server などのステートフルなアプリケーションには適していません。 |スケールアップは、複数の Web ロールおよび worker ロールを使用することで実現できます。 Web ロールとworker ロールの仮想マシン サイズの詳細については、[クラウド サービスのサイズを構成する方法](../../../cloud-services/cloud-services-sizes-specs.md)に関する記事をご覧ください。<br/><br/>**クラウド サービス** を使用するときに、複数のロールを定義して処理を分配し、アプリケーションのスケーリングを柔軟に実行できます。 各クラウド サービスには 1 つ以上の Web ロールまたは worker ロールが含まれており、各ロールにそれぞれ専用のアプリケーション ファイルと構成が関連付けられます。 ロールにデプロイされているロール インスタンス (仮想マシン) の数を増やすことでクラウド サービスをスケールアップし、ロール インスタンスの数を減らすことでクラウド サービスをスケールダウンできます。 詳細については、「[Azure Execution Models (Azure 実行モデル)](../../../cloud-services/cloud-services-choose-me.md)」をご覧ください。<br/><br/>スケールアウトは、[クラウド サービス、Virtual Machines、Virtual Network サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)とロード バランサーでのビルトインの Azure 高可用性サポートで実行できます。<br/><br/>多階層アプリケーションについては、Azure Virtual Network で Web/worker ロール アプリケーションをデータベース サーバー VM に接続することをお勧めします。 また、Azure は同じクラウド サービス内の VM に負荷分散を提供して、VM 間でユーザー要求を分散します。 この方法で接続している仮想マシンは、Azure データセンター内でローカル ネットワークを介して直接相互通信できます。<br/><br/>Azure Portal で **自動スケール** とそのスケジュール時間を設定できます。 詳細については、「[ポータルでクラウド サービスの自動スケールを構成する方法](../../../cloud-services/cloud-services-how-to-scale-portal.md)」を参照してください。 |**スケールアップとスケールダウン**: Web サイト向けに予約したインスタンス (VM) のサイズを増大/縮小できます。<br/><br/>スケールアウト: Web サイト向けに予約インスタンス (VM) をさらに追加できます。<br/><br/>**自動スケール** とそのスケジュール時間を設定できます。 詳細については、[Web Apps のスケールの設定方法](../../../app-service/manage-scale-up.md)に関する記事をご覧ください。 |

これらのプログラミング方法の選択に関する詳細については、[Azure Web Apps、Cloud Services、および VM についていつ、どれを使用するか](/azure/architecture/guide/technology-choices/compute-decision-tree)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
Azure Virtual Machines 上の SQL Server の実行については、[Azure Virtual Machines 上の SQL Server の概要](sql-server-on-azure-vm-iaas-what-is-overview.md)に関するページを参照してください。