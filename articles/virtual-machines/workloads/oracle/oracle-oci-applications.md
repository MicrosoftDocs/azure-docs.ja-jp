---
title: Azure Virtual Machines に Oracle アプリをデプロイするアーキテクチャ | Microsoft Docs
description: Azure または Oracle Cloud Infrastructure (OCI) のデータベースを使用して、Microsoft Azure 仮想マシンに E-Business Suite、JD Edwards EnterpriseOne、PeopleSoft などの Oracle アプリをデプロイするアプリケーション アーキテクチャ。
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 07/18/2019
ms.author: kegorman
ms.openlocfilehash: 862bb886c7ec5dfd40c7acdbae2f70f6698a711b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101669939"
---
# <a name="architectures-to-deploy-oracle-applications-on-azure"></a>Azure に Oracle アプリケーションをデプロイするアーキテクチャ

Microsoft と Oracle は、お客様が Oracle E-Business Suite、JD Edwards EnterpriseOne、PeopleSoft などの Oracle アプリケーションをクラウドにデプロイできるように協力しています。 Microsoft Azure と Oracle Cloud Infrastructure (OCI) 間にプレビューの[プライベート ネットワーク相互接続性](configure-azure-oci-networking.md)が導入され、Oracle アプリケーションを Azure または OCI のバックエンド データベースと共に Azure にデプロイできるようになりました。 Oracle アプリケーションは Azure Active Directory と統合することもできるので、ユーザーが自分の Azure Active Directory (Azure AD) の資格情報を使用して Oracle アプリケーションにサインインできるようにシングル サインオンを設定できます。

OCI には、DBaaS、Exadata Cloud Service、Oracle RAC、Infrastructure-as-a-Service (IaaS) など、Oracle アプリケーション用の複数の Oracle データベース オプションが用意されています。 現在、Autonomous Database は、Oracle アプリケーション用にサポートされているバックエンドではありません。

可用性が高くセキュリティで保護された方法を含め、Azure には Oracle アプリケーションをデプロイするための[複数の選択肢](oracle-overview.md)があります。 Azure にも、Oracle アプリケーションを完全に Azure 上で実行することを選択した場合にデプロイできる [Oracle データベース VM イメージ](oracle-vm-solutions.md)が用意されています。

以下のセクションでは、Oracle E-Business Suite、JD Edwards EnterpriseOne、および PeopleSoft をクロスクラウド構成または完全に Azure にデプロイする場合の、Microsoft と Oracle の両方によるアーキテクチャの推奨事項について概要を説明します。 Microsoft と Oracle はこれらのアプリケーションをテストし、これらのアプリケーションのパフォーマンスが Oracle が設定した基準を満たしていることを確認しました。

## <a name="architecture-considerations"></a>アーキテクチャの考慮事項

Oracle アプリケーションは複数のサービスで構成されています。これらのサービスは、Azure と必要に応じて OCI で、同じまたは複数の仮想マシン上でホストできます。 

アプリケーション インスタンスは、プライベート エンドポイントまたはパブリック エンドポイントで設定できます。 Microsoft と Oracle は、アプリケーションの管理用に別のサブネットにパブリック IP アドレスを持つ "*踏み台ホスト VM*" を設定することをお勧めします。 次に、データベース層を含め、他のマシンにプライベート IP アドレスのみを割り当てます。 

クロスクラウド アーキテクチャでアプリケーションを設定するときは、Azure 仮想ネットワークの IP アドレス空間が OCI 仮想クラウド ネットワークのプライベート IP アドレス空間と重ならないように計画する必要があります。

セキュリティを強化するために、特定のポートと IP アドレスでのトラフィックのみが許可されるように、ネットワーク セキュリティ グループをサブネット レベルで設定します。 たとえば、中間層のマシンは仮想ネットワーク内からのみトラフィックを受信するようにします。 外部トラフィックが中間層マシンに直接到達しないようにします。

高可用性を確保するために、同じ可用性セットまたは異なる可用性ゾーンに異なるサーバーの冗長インスタンスを設定できます。 可用性ゾーンを使用すると、99.99% のアップタイム SLA を達成できます。一方、可用性セットを使用すると、リージョン内で 99.95% のアップタイム SLA を達成できます。 この記事に示すサンプル アーキテクチャは、2 つの可用性ゾーンにまたがってデプロイされます。

クロスクラウド相互接続を使用してアプリケーションをデプロイするときは、引き続き既存の ExpressRoute 回線を使用して Azure 環境をオンプレミス ネットワークに接続することができます。 ただし、OCI への相互接続には、オンプレミス ネットワークに接続するものとは別の ExpressRoute 回線が必要です。

## <a name="e-business-suite"></a>E-Business Suite

Oracle E-Business Suite (EBS) は、サプライ チェーン管理 (SCM) および顧客関係管理 (CRM) を含む一連のアプリケーションです。 OCI のマネージド データベース ポートフォリオを利用するために、Microsoft Azure と OCI 間にクロスクラウド相互接続を使用して EBS をデプロイできます。 この構成では、次のアーキテクチャ図 (図 1) に示すように、プレゼンテーション層とアプリケーション層は Azure で実行され、データベース層は OCI で実行されます。

![E-Business Suite のクロスクラウド アーキテクチャ](media/oracle-oci-applications/ebs-arch-cross-cloud.png)

*図 1: E-Business Suite のクロスクラウド アーキテクチャ* 

このアーキテクチャで、Azure の仮想ネットワークは、クロスクラウド相互接続を使用して OCI 内の仮想クラウド ネットワークに接続されています。 アプリケーション層は Azure 内に設定され、データベースは OCI 内に設定されます。 特定のポート上の特定のサブネットからのトラフィックのみを許可するように、各コンポーネントをネットワーク セキュリティ グループを含む独自のサブネットに展開することをお勧めします。

このアーキテクチャは、リージョン内の 2 つの可用性ゾーンで Oracle Data Guard を使用して構成された高可用 Oracle データベースを使用して、完全に Azure 上にデプロイするように調整することもできます。 次の図 (図 2) は、このアーキテクチャ パターンの例です。

![E-Business Suite の Azure のみのアーキテクチャ](media/oracle-oci-applications/ebs-arch-azure.png)

*図 2: E-Business Suite の Azure のみのアーキテクチャ*

以下のセクションでは、さまざまなコンポーネントについて概要を説明します。

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-middle-tier"></a>アプリケーション (中間) 層

アプリケーション層は独自のサブネットに分離されています。 フォールト トレランスと簡単なパッチ管理のために設定された複数の仮想マシンがあります。 これらの VM は、Azure NetApp Files と Ultra SSD が提供する共有ストレージに支援されています。 この構成により、ダウンタイムなしでパッチを簡単に展開できます。 障害のために層の 1 台のマシンがオフラインであっても EBS アプリケーション層への要求が処理されるように、アプリケーション層のマシンをパブリック ロード バランサーで処理する必要があります。

### <a name="load-balancer"></a>Load Balancer

Azure ロード バランサーを使用すると、ワークロードの複数のインスタンスにトラフィックを分散して高可用性を確保できます。 この場合、ユーザーは Web 経由で EBS アプリケーションにアクセスすることが許可されているため、パブリック ロード バランサーが設定されます。 ロード バランサーによって、中間層の両方のマシンに負荷が分散されます。 セキュリティを強化するために、サイト間 VPN または ExpressRoute とネットワーク セキュリティ グループを使用して企業ネットワークからシステムにアクセスするユーザーからのトラフィックのみを許可します。

### <a name="database-tier"></a>データベース層

この層には Oracle データベースがホストされ、独自のサブネットに分割されます。 アプリケーション層から Oracle 固有のデータベースポート 1521 上のデータベース層へのトラフィックのみを許可するネットワーク セキュリティ グループを追加することをお勧めします。

Microsoft と Oracle は、高可用性の設定を推奨します。 Azure で高可用性を実現するには、Oracle Data Guard を使用して 2 つの可用性ゾーンに 2 つの Oracle データベースを設定するか、OCI で Oracle Database Exadata Cloud Service を使用します。 Oracle Database Exadata Cloud Service を使用すると、データベースは 2 つのサブネットにデプロイされます。 Oracle Data Guard を使用して、OCI の 2 つの可用性ドメインの VM に Oracle Database を設定することもできます。


### <a name="identity-tier"></a>ID 層

ID 層には EBS Asserter VM が含まれています。 EBS Asserter を使用すると、Oracle Identity Cloud Service (IDCS) と Azure AD の ID を同期できます。 EBS は SAML 2.0 や OpenID Connect などのシングル サインオン プロトコルをサポートしていないため、EBS Asserter が必要です。 EBS Asserter では、(IDCS によって生成された) OpenID 接続トークンが使用され、それが検証され、EBS でユーザーのセッションが作成されます。 

このアーキテクチャは IDCS の統合を示していますが、Oracle Access Manager と Oracle Internet Directory または Oracle Unified Directory を使用して Azure AD の統合アクセスとシングル サインオンを有効にすることもできます。 詳細については、[IDCS 統合による Oracle EBS の展開](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_idcs.pdf)または [OAM 統合による Oracle EBS の展開](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_oam.pdf)のホワイト ペーパーを参照してください。

高可用性を確保するには、EBS Asserter の冗長サーバーを複数の可用性ゾーンにデプロイし、その前にロード バランサーを配置することをお勧めします。

インフラストラクチャを設定したら、Oracle が提供するインストール ガイドに従って E-Business Suite をインストールできます。

## <a name="jd-edwards-enterpriseone"></a>JD Edwards EnterpriseOne

Oracle の JD Edwards EnterpriseOne は、包括的なエンタープライズ リソース計画ソフトウェアの統合アプリケーション スイートです。 これは、Oracle または SQL Server データベース バックエンドのいずれかで設定できる多層アプリケーションです。 このセクションでは、OCI または Azure で Oracle データベース バックエンドを使用して JD Edwards EnterpriseOne をデプロイする方法について詳しく説明します。

次の推奨アーキテクチャ (図 3) では、管理層、プレゼンテーション層、および中間層が Azure の仮想ネットワークにデプロイされています。 データベースは OCI の仮想クラウド ネットワークに展開されます。

E-Business Suite と同様に、セキュリティで保護された管理の目的のためにオプションの Bastion 層を設定できます。 踏み台 VM ホストをジャンプ サーバーとして使用して、アプリケーションおよびデータベース インスタンスにアクセスします。

![JD Edwards Enterprise One のクロスクラウド アーキテクチャ](media/oracle-oci-applications/jdedwards-arch-cross-cloud.png)

*図 3: JD Edwards EnterpriseOne のクロスクラウド アーキテクチャ*

このアーキテクチャで、Azure の仮想ネットワークは、クロスクラウド相互接続を使用して OCI 内の仮想クラウド ネットワークに接続されています。 アプリケーション層は Azure 内に設定され、データベースは OCI 内に設定されます。 特定のポート上の特定のサブネットからのトラフィックのみを許可するように、各コンポーネントをネットワーク セキュリティ グループを含む独自のサブネットに展開することをお勧めします。

このアーキテクチャは、リージョン内の 2 つの可用性ゾーンで Oracle Data Guard を使用して構成された高可用 Oracle データベースを使用して、完全に Azure 上にデプロイするように調整することもできます。 次の図 (図 4) は、このアーキテクチャ パターンの例です。

![JD Edwards EnterpriseOne Azure のみのアーキテクチャ](media/oracle-oci-applications/jdedwards-arch-azure.png)

*図 4: JD Edwards EnterpriseOne Azure のみのアーキテクチャ*

以下のセクションでは、さまざまなコンポーネントについて概要を説明します。

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="administrative-tier"></a>管理層

名前が示すように、この層は管理タスクに使用されます。 管理層用に別のサブネットを作成できます。 この層のサービスとサーバーは、主にアプリケーションのインストールと管理に使用されます。 そのため、これらのサーバーの 1 つのインスタンスで十分です。 アプリケーションの高可用性を確保するために冗長インスタンスは必要ありません。

この層のコンポーネントは次のとおりです。
    
 - **プロビジョニング サーバー** - このサーバーは、アプリケーションのさまざまなコンポーネントをエンドツーエンドでデプロイするために使用されます。 ポート 22 を介して、データベース層のインスタンスを含む他の層のインスタンスと通信されます。 JD Edwards EnterpriseOne 用の Server Manager Console がホストされます。
 - **デプロイ サーバー** - このサーバーは主に JD Edwards EnterpriseOne のインストールに必要です。 インストール プロセス中、このサーバーは必要なファイルとインストール パッケージの中央リポジトリとして機能します。 このソフトウェアは、このサーバーから他のサーバーおよびクライアントに配布または展開されます。
 - **開発クライアント** - このサーバーには、Web ブラウザーおよびネイティブ アプリケーションで実行されるコンポーネントが含まれています。

### <a name="presentation-tier"></a>プレゼンテーション層

この層には、Application Interface Services (AIS)、Application Development Framework (ADF)、Java Application Servers (JAS) などのさまざまなコンポーネントが含まれています。 この層のサーバーは、中間層のサーバーと通信します。 これらは、トラフィックが受信されるポート番号と URL に基づいてトラフィックを必要なサーバーにルーティングするロード バランサーによって提供されます。 高可用性を確保するために、各サーバーの種類のインスタンスを複数展開することをお勧めします。

この層のコンポーネントは次のとおりです。
    
- **Application Interface Services (AIS)** - AIS サーバーによって、JD Edwards Enterprise One モバイル エンタープライズ アプリケーションと JD Edwards Enterprise One の間に通信インターフェイスが提供されます。
- **Java Application Server (JAS)** - JAS では、ロード バランサーから要求を受け取り、それを中間層に渡して複雑なタスクを実行します。 JAS には単純なビジネス ロジックを実行する機能があります。
- **BI Publisher Server (BIP)** - このサーバーでは、JD Edwards EnterpriseOne アプリケーションによって収集されたデータに基づいてレポートが表示されます。 さまざまなテンプレートに基づいてレポートのデータの表示方法を設計および制御できます。
- **Business Services Server (BSS)** - BSS により、他の Oracle アプリケーションとの情報交換と相互運用が可能になります。
- **Real-Time Events Server (RTE)** - RTE Server を使用すると、JDE EnterpriseOne システムで発生したトランザクションに関する外部システムへの通知を設定できます。 サブスクライバー モデルが使用されており、サードパーティ製システムを使用してイベントにサブスクライブできます。 両方の RTE サーバーに要求の負荷を分散するには、サーバーを 1 つのクラスター内に配置します。
- **Application Development Framework (ADF) Server** - ADF サーバーは、Oracle ADF を使用して開発された JD Edwards Enterprise One アプリケーションを実行するために使用されます。 これは、ADF ランタイムを使用して Oracle WebLogic サーバーにデプロイされます。

### <a name="middle-tier"></a>中間層

中間層には、ロジック サーバーとバッチ サーバーが含まれています。 この場合、両方のサーバーが同じ仮想マシンにインストールされています。 ただし、運用環境シナリオでは、ロジック サーバーとバッチ サーバーを別々のサーバーに配置することをお勧めします。 複数のサーバーが 2 つの可用性ゾーンにまたがって中間層にデプロイされ、可用性が高くなります。 Azure ロード バランサーを作成し、これらのサーバーをバックエンド プールに配置して、両方のサーバーがアクティブで要求が処理されることを確保するようにします。

中間層のサーバーは、プレゼンテーション層のサーバーとパブリック ロード バランサーからのみ要求を受け取ります。 プレゼンテーション層のサブネットとロード バランサー以外のアドレスからのトラフィックを拒否するようにネットワーク セキュリティ グループ規則を設定する必要があります。 管理目的で踏み台ホストからポート 22 のトラフィックを許可するように NSG 規則を設定することもできます。 パブリック ロード バランサーを使用して、中間層の VM 間で要求の負荷を分散できます。

次の 2 つのコンポーネントは中間層にあります。

- **ロジック サーバー** - ビジネス ロジックまたはビジネス機能が含まれています。
- **バッチ サーバー** - バッチ処理に使用されます

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="peoplesoft"></a>PeopleSoft

Oracle の PeopleSoft アプリケーション スイートには、人事管理と財務管理のためのソフトウェアが含まれています。 このアプリケーション スイートは多層であり、アプリケーションには人事管理システム (HRMS)、顧客関係管理 (CRM)、財務およびサプライ チェーン管理 (FSCM)、および企業業績管理 (EPM) が含まれます。

ソフトウェア スイートの各層をそれぞれ独自のサブネットに配置することをお勧めします。 アプリケーションのバックエンド データベースとして、Oracle データベースまたは Microsoft SQL Server が必要です。 このセクションでは、Oracle データベース バックエンドを使用して PeopleSoft を展開する方法について詳しく説明します。

PeopleSoft アプリケーション スイートをクロスクラウド アーキテクチャに展開する場合の標準的なアーキテクチャを次に示します (図 5)。

![PeopleSoft クロスクラウド アーキテクチャ](media/oracle-oci-applications/peoplesoft-arch-cross-cloud.png)

*図 5: PeopleSoft クロスクラウド アーキテクチャ*

このサンプル アーキテクチャで、Azure の仮想ネットワークは、クロスクラウド相互接続を使用して OCI 内の仮想クラウド ネットワークに接続されています。 アプリケーション層は Azure 内に設定され、データベースは OCI 内に設定されます。 特定のポート上の特定のサブネットからのトラフィックのみを許可するように、各コンポーネントをネットワーク セキュリティ グループを含む独自のサブネットに展開することをお勧めします。

このアーキテクチャは、リージョン内の 2 つの可用性ゾーンで Oracle Data Guard を使用して構成された高可用 Oracle データベースを使用して、完全に Azure 上にデプロイするように調整することもできます。 次の図 (図 6) は、このアーキテクチャ パターンの例です。

![PeopleSoft Azure のみのアーキテクチャ](media/oracle-oci-applications/peoplesoft-arch-azure.png)

*図 6: PeopleSoft Azure のみのアーキテクチャ*

以下のセクションでは、さまざまなコンポーネントについて概要を説明します。

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-tier"></a>アプリケーション層

アプリケーション層には、PeopleSoft アプリケーション サーバー、PeopleSoft Web サーバー、エラスティック検索、および PeopleSoft Process Scheduler のインスタンスが含まれています。 Azure ロード バランサーは、アプリケーション層の適切なサーバーにルーティングされるユーザーからの要求を受け入れるように設定されています。

高可用性を確保するために、異なる可用性ゾーンにまたがってアプリケーション層の各サーバーの冗長インスタンスを設定することを検討します。 Azure ロード バランサーは、各要求を適切なサーバーに転送するように複数のバックエンド プールを使用して設定できます。

### <a name="peopletools-client"></a>PeopleTools クライアント

PeopleTools クライアントは、開発、移行、アップグレードなどの管理作業を実行するために使用されます。 PeopleTools クライアントはアプリケーションの高可用性を実現するために必要ではないため、PeopleTools クライアントの冗長サーバーは必要ありません。

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="next-steps"></a>次のステップ

[Terraform スクリプト](https://github.com/microsoft/azure-oracle)を使用して、Azure で Oracle アプリを設定し、OCI とのクロスクラウド接続を確立します。

OCI の詳細およびホワイトペーパーについては、[Oracle Cloud](https://docs.cloud.oracle.com/iaas/Content/home.htm) のドキュメントを参照してください。
