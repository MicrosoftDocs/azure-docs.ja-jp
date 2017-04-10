---
title: "Azure Government イメージ ギャラリー | Microsoft Docs"
description: "この記事では、Azure Government イメージ ギャラリーと、含まれるイメージの概要について説明します。"
services: azure-government
cloud: gov
documentationcenter: 
author: brendalee
manager: zakramer
ms.assetid: f6dd4386-7b79-448a-8ae3-409258cc257b
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/20/2016
ms.author: brendal
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: e2d82497c2ceaa9a6c0a1592b5785449f095f85c
ms.lasthandoff: 03/10/2017


---
# <a name="azure-government-marketplace-images"></a>Azure Government Marketplace のイメージ
## <a name="overview"></a>概要
Microsoft Azure Government Marketplaceの使い心地は Azure Portal と似ています。  Microsoft や Microsoft のパートナーが事前に構築したイメージのデプロイを選択し、自身の VHD にアップロードできます。  これによって、必要に応じて自身のニーズに応じて標準化したイメージを自由にデプロイできるようになります。

## <a name="variations"></a>バリエーション
* 現時点では、Marketplace イメージは DoD ではサポートされていません。

Azure Government Marketplace 内で利用できるイメージの一覧を次に示します。  事前に構成されたイメージの内いくつかでは、特定のソフトウェアのライセンスに対する支払いも生じます。  詳細は「<a href="http://azure.microsoft.com/pricing/details/virtual-machines/">Virtual Machines 料金</a>」をご確認いただき、Azure Government 特有の料金について Microsoft アカウント チームや販売店にご確認ください。

## <a name="images---azure-resource-manager"></a>イメージ - Azure Resource Manager
| 発行元 | イメージ名 |
| --- | --- |
| Esri |ArcGIS 10.4.1 for Server |
| Barracuda Networks, Inc. |Barracuda NextGen Control Center (BYOL) |
| Barracuda Networks, Inc. |Barracuda NextGen Firewall F シリーズ (BYOL) |
| Microsoft |BizTalk Server 2016 Community Technology Preview 2 |
| Microsoft |{BYOL} Windows Server 2012 R2 の SQL Server 2012 SP3 Enterprise |
| Microsoft |{BYOL} Windows Server 2012 R2 の SQL Server 2012 SP3 Standard |
| Microsoft |{BYOL} Windows Server 2012 R2 の SQL Server 2014 SP1 Enterprise |
| Microsoft |{BYOL} Windows Server 2012 R2 の SQL Server 2014 SP1 Standard |
| Microsoft |{BYOL} Windows Server 2012 R2 の SQL Server 2016 RTM Enterprise |
| Microsoft |{BYOL} Windows Server 2012 R2 の SQL Server 2016 RTM Standard |
| OpenLogic |CentOS-based 6.7 |
| OpenLogic |CentOS-based 6.8 |
| OpenLogic |CentOS-based 7.2 |
| CoreOS |CoreOS Linux (Stable) |
| F5 Networks |F5 BIG-IP ADC+SEC BEST – BYOL |
| F5 Networks |F5 BIG-IP ADC BETTER – BYOL |
| F5 Networks |F5 BIG-IP ADC GOOD (LTM) - BYOL |
| Bitnami |Moodle |
| Citrix |NetScaler VPX ライセンス持ち込み |
| SUSE |openSUSE Leap 42.1 |
| Red Hat |Red Hat Enterprise Linux 6.8 |
| Red Hat |Red Hat Enterprise Linux 7.2 |
| Microsoft |Windows Server 2012 R2 の SQL Server 2012 SP3 Enterprise |
| Microsoft |Windows Server 2012 R2 の SQL Server 2012 SP3 Standard |
| Microsoft |SQL Server 2014 SP1 Enterprise on Windows Server 2012 R2 |
| Microsoft |Windows Server 2012 R2 の SQL Server 2014 SP1 Standard |
| Microsoft |Windows Server 2012 R2 の SQL Server 2016 RTM Enterprise |
| Microsoft |Windows Server 2012 R2 の SQL Server 2016 RTM Standard |
| SUSE |SLES 11 SP4 |
| SUSE |SLES 12 SP1 |
| Microsoft |Windows Server 2012 R2 Datacenter |
| Barracuda Networks, Inc. |Barracuda Web Application Firewall (WAF) - BYOL |
| Microsoft |Windows Server 2008 R2 SP1 |
| Microsoft |Windows Server 2012 Datacenter |
| Microsoft |Windows Server 2012 R2 Datacenter |

## <a name="images---classic-resource-provider"></a>イメージ - 従来のリソース プロバイダー
| 発行元 | イメージ名 | 説明 | OS | ディスク サイズ |
| --- | --- | --- | --- | --- |
| ASR-Prod-GalleryImages |Microsoft Azure Site Recovery プロセス サーバー |Microsoft Azure Site Recovery プロセス サーバーは、仮想マシンのフェールバックを設定するという Microsoft Azure Site Recovery のシナリオで、レプリケーション トラフィックのキャッシュ、圧縮、および暗号化に使用され... |Windows |GB |
| ASR-Prod-GalleryImages |Microsoft Azure Site Recovery マスター ターゲット サーバー |Microsoft Azure Site Recovery マスター ターゲット サーバーは、リポジトリとして、Azure Site Recovery のシナリオで Windows ソースの仮想または物理サーバーから送信されるレプリケーション トラフィックの保持に使用され... |Windows |GB |
| ASR-Prod-GalleryImages |Microsoft Azure Site Recovery 構成サーバー (VPN) |Microsoft Azure Site Recovery 構成サーバーは、Microsoft Azure Site Recovery の設定シナリオで、障害復旧の管理操作の一元的な管理サーバーとして動作し... |Windows |GB |
| ASR-Prod-GalleryImages |Microsoft Azure Site Recovery 構成サーバー (VPN 以外) |Microsoft Azure Site Recovery 構成サーバーは、Microsoft Azure Site Recovery の設定シナリオで、障害復旧の管理操作の一元的な管理サーバーとして動作し... |Windows |GB |
| ASR-Prod-GalleryImages |Microsoft Azure Site Recovery プロセス サーバー V2 |Microsoft Azure Site Recovery プロセス サーバーは、仮想マシンのフェールバックを設定するという Microsoft Azure Site Recovery のシナリオで、レプリケーション トラフィックのキャッシュ、圧縮、および暗号化に使用され... |Windows |GB |
| Barracuda Networks, Inc. |Barracuda NextGen Firewall F シリーズ 7.0.0-672 |Barracuda の NextGen Firewall F シリーズ (https://www.barracuda.com/products/nextgenfirewall-f) は、ネットワークの保護と管理を行う強力で使いやすいアプライアンスです。 Linux ベースのセキュ... |Linux |80 GB |
| Barracuda Networks, Inc. |Barracuda Web Application Firewall 8.0.101101 |Barracuda の Web Application Firewall (https://www.barracuda.com/products/webapplicationfirewall) は、Web サイトを攻撃から保護する、強力で使いやすいアプライアンスで... |Linux |50 GB |
| Bitnami |Ruby Stack 2.1 |Bitnami RubyStack は、Rails アプリケーションにおける Ruby の開発やデプロイの手間を大幅に削減します。 Ruby on Rails は、データベースを備えた Web アプリケーション向けのフルスタックの MVC フレームワークで... |Linux |30 GB |
| Bitnami |Ruby Stack 2.0 |Bitnami RubyStack は、Rails アプリケーションにおける Ruby の開発やデプロイの手間を大幅に削減します。 Ruby on Rails は、データベースを備えた Web アプリケーション向けのフルスタックの MVC フレームワークで... |Linux |30 GB |
| Bitnami |Nginx Stack 1.6 |Bitnami Nginx Stack は、包括的で、完全に統合された、すぐに使用できる PHP、MySQL、Nginx の開発環境です。 さらに、phpMyAdmin、SQLite、ImageMagick、FastCGI,、Memcache、GD... |Linux |30 GB |
| Bitnami |LAMP Stack 5.6 |Bitnami LAMPStack は、PHP アプリケーションの開発やデプロイの手間を大幅に削減します。 すぐに使用できる Apache、MySQL、PHP、phpMyAdmin、他のソフトウェアが含まれ... |Linux |30 GB |
| Bitnami |LAMP Stack 5.4 |Bitnami LAMPStack は、PHP アプリケーションの開発やデプロイの手間を大幅に削減します。 すぐに使用できる Apache、MySQL、PHP、phpMyAdmin、他のソフトウェアが含まれ... |Linux |30 GB |
| Bitnami |Drupal 8 |Bitnami が提要する Drupal は、Microsoft Azure で Drupal を実行するための事前構成されたすぐに使用できるイメージです。 Drupal は、最も汎用性の高いオープン ソースのコンテンツ管理システムの&1; つです。 次の... |Linux |30 GB |
| Bitnami |Drupal 7 |Bitnami が提要する Drupal は、Microsoft Azure で Drupal を実行するための事前構成されたすぐに使用できるイメージです。 Drupal は、最も汎用性の高いオープン ソースのコンテンツ管理システムの&1; つです。 次の... |Linux |30 GB |
| Bitnami |Drupal 6 |Bitnami が提要する Drupal は、Microsoft Azure で Drupal を実行するための事前構成されたすぐに使用できるイメージです。 Drupal は、最も汎用性の高いオープン ソースのコンテンツ管理システムの&1; つです。 次の... |Linux |30 GB |
| BizTalk |BizTalk Server 2016 Community Technology Preview 2 |Windows Server 2016 Technical Preview 4 の Microsoft BizTalk Server 2016 Community Technology Preview 2 です。 このリリースでは、多くの重要な機能強化に重点が置かれています。 SQL Server 2016 AlwaysOn 可用性グループのサポート... |Windows |128 GB |
| Canonical |Ubuntu Server 12.04.5-LTS |Microsoft Azure 用 Ubuntu Server 12.04.5-LTS (amd64 20160315)。 Ubuntu Server は、クラウド環境の世界で最も人気のある Linux です。 Ubuntu 12.04.5-LTS の更新とパッチを入手できます... |Linux |30 GB |
| Canonical |Ubuntu Server 14.04.4-LTS |Microsoft Azure 用 Ubuntu Server 14.04.4-LTS (amd64 20160314)。 Ubuntu Server は、クラウド環境の世界で最も人気のある Linux です。 Ubuntu 14.04.4-LTS の更新とパッチを入手できます... |Linux |30 GB |
| Canonical |Ubuntu Server 16.04 LTS |Microsoft Azure 用 Ubuntu Server 16.04 LTS (amd64 20160420.3)。 Ubuntu Server は、クラウド環境の世界で最も人気のある Linux です。 Ubuntu 16.04 LTS の更新とパッチを入手できます... |Linux |30 GB |
| Check Point |Check Point vSec、R77.30-015052 |Azure 用 Check Point vSEC は、次世代のファイアウォールとセキュリティ ゲートウェイを提供します。 |Linux |50 GB |
| Check Point |Check Point vSec、R77.30.022.105 |Azure 用 Check Point vSEC は、次世代のファイアウォールとセキュリティ ゲートウェイを提供します。 |Linux |50 GB |
| CloudLink |CloudLink SecureVM 5.0 BYOL |簡単かつ確実に VM を制御、監視、暗号化します。 EMC の CloudLink SecureVM は、ポリシー ベースのキー管理とオーケストレーションを提供することによってネイティブ Windows BitLocker および Linux OS 暗号化機能をロック解除します。 |Linux |30 GB |
| CloudLink |CloudLink SecureVM |CloudLink® SecureVM は、仮想マシンのボリュームの暗号化と VM イメージの整合性の検証による起動前の承認を提供します。これにより、仮想マシンを起動するタイミングと場所を制御できます。 SecureVM は、ネイティブの Windows のロックを解除... |Linux |30 GB |
| coreos |CoreOS Stable |運用クラスターは Stable チャネルを使用する必要があります。 CoreOS のバージョンは、昇格される前に、ベータおよびアルファ チャネル内で実証済みです。 |Linux |31 GB |
| coreos |CoreOS Stable |運用クラスターは Stable チャネルを使用する必要があります。 CoreOS のバージョンは、昇格される前に、ベータおよびアルファ チャネル内で実証済みです。 |Linux |31 GB |
| credativ |Debian 7 "Wheezy" |Microsoft Azure 用 Debian 7 "Wheezy" (20160628.0)。 Debian GNU/Linux は最も人気のある Linux ディストリビューションの&1; つです。 |Linux |30 GB |
| credativ |Debian 8 "Jessie" |Microsoft Azure 用 Debian 8 "Jessie" (20160628.0)。 Debian GNU/Linux は最も人気のある Linux ディストリビューションの&1; つです。 |Linux |30 GB |
| credativ |Debian 9 "Stretch" DAILY |Microsoft Azure 用 Debian 9 "Stretch" ( 20160816.0)。 Debian GNU/Linux は最も人気のある Linux ディストリビューションの&1; つです。 |Linux |30 GB |
| Dynamics NAV |Windows Server 2012 R2 の Microsoft Dynamics NAV 2016 |Microsoft Dynamics NAV は、迅速に実装して簡単に使用できる Microsoft のビジネス ソリューションです。お客様のビジネスにおけるご要望を強力にサポートします。 このイメージには、事前構成された... |Windows |128 GB |
| Esri |ArcGIS 10.3.1 for Server (Windows) |世界で最も汎用的な GIS サーバーである ArcGIS for Server 、組織の重要な geo 資産およびデータを完全に制御します。 |Windows |128 GB |
| Microsoft Hybrid Cloud Storage Group |StorSimple Virtual Appliance 1100、May 2015 |Microsoft StorSimple Virtual Appliance 1100 |Windows |32 GB |
| Microsoft Hybrid Cloud Storage Group |StorSimple Virtual Appliance 1100、July 2015 |Microsoft StorSimple Virtual Appliance 1100 |Windows |32 GB |
| Microsoft Hybrid Cloud Storage Group |StorSimple Virtual Appliance 1100、October 2015 |Microsoft StorSimple Virtual Appliance 1100 |Windows |32 GB |
| Microsoft Hybrid Cloud Storage Group |StorSimple Virtual Appliance GU2、Feb 2016 |Microsoft StorSimple Virtual Appliance GU2 |Windows |32 GB |
| Microsoft Open Technologies, Inc. |Oracle WebLogic Server 11g Standard Edition on Windows Server 2008 R2 |Oracle WebLogic Server (http://www.oracle.com/weblogicserver) 11g Standard Edition (10.3.6) は、あらゆる規模の企業向けの優れた Java アプリケーション サーバーで、開発者に... |Windows |128 GB |
| Microsoft Open Technologies, Inc. |Oracle WebLogic Server 11g Enterprise Edition on Windows Server 2008 R2 |Oracle WebLogic Server (http://www.oracle.com/weblogicserver) 11g Enterprise Edition (10.3.6) は最新のデータ センター向けの優れた Java アプリケーション サーバーです。 最新の機能を... |Windows |128 GB |
| Microsoft Open Technologies, Inc. |Oracle Database 12c Standard Edition on Windows Server 2012 |Oracle Database (http://www.oracle.com/database) 12c Standard Edition (12.1.0.1.0) は、中規模の企業に最適な、高すぎず、機能を取り揃えたデータ管理ソリューションです。  最小の... |Windows |128 GB |
| Microsoft Open Technologies, Inc. |Oracle Database 12c and WebLogic Server 12c Standard Edition on Windows Server 2012 |Oracle Database (http://www.oracle.com/database) 12c Standard Edition (12.1.0.1.0) は、中規模の企業に最適な、高すぎず、機能を取り揃えたデータ管理ソリューションです。 Oracle WebLogic... |Windows |128 GB |
| Microsoft Open Technologies, Inc. |Oracle Database 12c and WebLogic Server 12c Enterprise Edition on Windows Server 2012 |Oracle Database (http://www.oracle.com/database) 12c Enterprise Edition (12.1.0.1.0) は、クラウド向けの次世代データベースで、新しいマルチテナント アーキテクチャを... |Windows |128 GB |
| Microsoft Open Technologies, Inc. |Oracle Database 12c Enterprise Edition on Windows Server 2012 |Oracle Database (http://www.oracle.com/database) 12c Enterprise Edition (12.1.0.1.0) は、クラウド向けの次世代データベースで、新しいマルチテナント アーキテクチャを... |Windows |128 GB |
| Microsoft Open Technologies, Inc. |Oracle WebLogic Server 12c Standard Edition on Windows Server 2012 |Oracle WebLogic Server (http://www.oracle.com/weblogicserver) 12c Standard Edition (12.1.2.0) は優れた Java EE アプリケーション サーバーで、次世代アプリケーションをミッション クリティカルな... |Windows |128 GB |
| Microsoft Open Technologies, Inc. |Oracle Database 11g R2 Standard Edition on Windows Server 2008 R2 |Oracle Database (http://www.oracle.com/database) 11g R2 Standard Edition (11.2.0.4.0) は、中規模の企業に最適な、高すぎず、機能を取り揃えたデータ管理ソリューションです。 最小の... |Windows |128 GB |
| Microsoft Open Technologies, Inc. |Oracle Database 11g R2 と WebLogic Server 11g Enterprise Edition on Windows Server 2008 R2 |Oracle Database (http://www.oracle.com/database) 11g R2 Enterprise Edition (11.2.0.4.0) は、きわめて負荷の高いトランザクション処理の管理を容易にする包括的な機能や... |Windows |128 GB |
| Microsoft Open Technologies, Inc. |Oracle Database 11g R2 Enterprise Edition on Windows Server 2008 R2 |Oracle Database (http://www.oracle.com/database) 11g R2 Enterprise Edition (11.2.0.4.0) は、きわめて負荷の高いトランザクション処理の管理を容易にする包括的な機能や... |Windows |128 GB |
| Microsoft Open Technologies, Inc. |Windows Server 2012 R2 の JDK 8 |Java プラットフォーム (http://www.oracle.com/java) の Standard Edition 8 (update 25) は、安全で持ち運べる、パフォーマンスの高いアプリケーションの開発に役立ちます。Java Development Kit (JDK) をはじめ... |Windows |128 GB |
| Microsoft Open Technologies, Inc. |Windows Server 2012 R2 の JDK 7 |Java プラットフォーム (http://www.oracle.com/java) の Standard Edition 7 (update 71) は、安全で持ち運べる、パフォーマンスの高いアプリケーションの開発に役立ちます。Java Development Kit (JDK) をはじめ... |Windows |128 GB |
| Microsoft Open Technologies, Inc. |Windows Server 2012 の JDK 6 |Java プラットフォーム (http://www.oracle.com/java) の Standard Edition 6 (update 85) は、安全で持ち運べる、パフォーマンスの高いアプリケーションの開発に役立ちます。Java Development Kit (JDK) をはじめ... |Windows |128 GB |
| Microsoft Open Technologies, Inc. |Oracle Database 11g R2 と WebLogic Server 11g Standard Edition on Windows Server 2008 R2 |Oracle Database (http://www.oracle.com/database) 11g R2 Standard Edition (11.2.0.4.0) は、中規模の企業に最適な、高すぎず、機能を取り揃えたデータ管理ソリューションです。 Oracle WebLo... |Windows |128 GB |
| Microsoft Open Technologies, Inc. |Oracle WebLogic Server 12c Enterprise Edition on Windows Server 2012 |Oracle WebLogic Server (http://www.oracle.com/weblogicserver) 12c Enterprise Edition (12.1.2.0) は優れた Java EE アプリケーション サーバーで、次世代アプリケーションをミッション クリティカルな... |Windows |128 GB |
| Microsoft SQL Server グループ |SQL Server 2012 SP2 Standard on Windows Server 2012 |一部の SQL Server コンポーネントでは、使用前ｊに設定や構成を追加する必要があります。 A2 以上のサイズの仮想マシンを使用することをお勧めします。 このイメージは、Azure 向けに事前に構成され... |Windows |127 GB |
| Microsoft SQL Server グループ |SQL Server 2014 RTM Enterprise on Windows Server 2012 R2 |このイメージには、SQL Server の完全バージョンが含まれています。 A3 以上のサイズの仮想マシンを使用することをお勧めします。 このイメージは、Azure 向けに事前に構成されました。構成には CEIP を有効に... |Windows |127 GB |
| Microsoft SQL Server グループ |SQL Server 2014 SP1 Enterprise on Windows Server 2012 R2 |このイメージには、SQL Server の完全バージョンが含まれています。 運用環境のワークロードには、DS3 以上のサイズの仮想マシンを使用することをお勧めします。 このイメージは、Microsoft Azure 向けに事前に構成されており... |Windows |127 GB |
| Microsoft SQL Server グループ |SQL Server 2014 RTM Enterprise on Windows Server 2012 R2 |このイメージには、SQL Server の完全バージョンが含まれています。 A3 以上のサイズの仮想マシンを使用することをお勧めします。 このイメージは、Azure 向けに事前に構成されました。構成には CEIP を有効に... |Windows |127 GB |
| Microsoft SQL Server グループ |SQL Server 2014 RTM Standard on Windows Server 2012 R2 |このイメージには、SQL Server の完全バージョンが含まれています。 A2 以上のサイズの仮想マシンを使用することをお勧めします。 このイメージは、Azure 向けに事前に構成されました。構成には CEIP を有効に... |Windows |127 GB |
| Microsoft SQL Server グループ |Windows Server 2012 R2 の SQL Server 2012 SP3 Standard |このイメージには、SQL Server の完全バージョンが含まれています。 運用環境のワークロードには、DS3 以上のサイズの仮想マシンを使用することをお勧めします。 このイメージは、Microsoft Azure 向けに事前に構成されており... |Windows |127 GB |
| Microsoft SQL Server グループ |Windows Server 2012 R2 の SQL Server 2012 SP3 Enterprise |このイメージには、SQL Server の完全バージョンが含まれています。 運用環境のワークロードには、DS3 以上のサイズの仮想マシンを使用することをお勧めします。 このイメージは、Microsoft Azure 向けに事前に構成されており... |Windows |127 GB |
| Microsoft SQL Server グループ |Windows Server 2012 R2 の SQL Server 2012 SP3 Express |このイメージには、SQL Server の Express with Tools バージョンが含まれています。 このイメージは、開発/テストおよびマイクロ ワークロード用に調整されており、SQL のライセンス料は課金されません。 このイメージは、事前に構成され... |Windows |127 GB |
| Microsoft SQL Server グループ |Windows Server 2012 R2 の SQL Server 2014 SP1 Web |このイメージには、SQL Server の完全バージョンが含まれています。 運用環境のワークロードには、DS3 以上のサイズの仮想マシンを使用することをお勧めします。 このイメージは、Microsoft Azure 向けに事前に構成されており... |Windows |127 GB |
| Microsoft SQL Server グループ |Windows Server 2012 R2 の SQL Server 2014 SP1 Express |このイメージには、SQL Server の Express with Tools バージョンが含まれています。 このイメージは、開発/テストおよびマイクロ ワークロード用に調整されており、SQL のライセンス料は課金されません。 このイメージは、事前に構成され... |Windows |127 GB |
| Microsoft SQL Server グループ |Windows Server 2012 R2 の SQL Server 2014 SP1 Standard |このイメージには、SQL Server の完全バージョンが含まれています。 運用環境のワークロードには、DS3 以上のサイズの仮想マシンを使用することをお勧めします。 このイメージは、Microsoft Azure 向けに事前に構成されており... |Windows |127 GB |
| Microsoft SQL Server グループ |SQL Server 2014 RTM Standard on Windows Server 2012 R2 |このイメージには、SQL Server の完全バージョンが含まれています。 A2 以上のサイズの仮想マシンを使用することをお勧めします。 このイメージは、Azure 向けに事前に構成されました。構成には CEIP を有効に... |Windows |127 GB |
| Microsoft SQL Server グループ |Windows Server 2012 R2 の SQL Server 2016 RTM Developer |このイメージには、SQL Server 2016 の Developer バージョンが含まれています。 Enterprise Edition のすべての機能が付属していますが、ライセンスが許諾されているのは開発およびテストのみです。運用は許諾されていません。 これには... |Windows |127 GB |
| Microsoft SQL Server グループ |Windows Server 2012 R2 の SQL Server 2016 RTM Express |このイメージには、SQL Server の Express with Tools バージョンが含まれています。 このイメージは、開発/テストおよびマイクロ ワークロード用に調整されており、SQL のライセンス料は課金されません。 このイメージは、事前に構成され... |Windows |127 GB |
| Microsoft SQL Server グループ |Windows Server 2012 R2 の SQL Server 2016 RTM Standard |このイメージには、SQL Server の完全バージョンが含まれています。 運用環境のワークロードには、DS3 以上のサイズの仮想マシンを使用することをお勧めします。 このイメージは、Microsoft Azure 向けに事前に構成されており... |Windows |127 GB |
| Microsoft SQL Server グループ |Windows Server 2012 R2 の SQL Server 2016 RTM Web |このイメージには、SQL Server の完全バージョンが含まれています。 運用環境のワークロードには、DS3 以上のサイズの仮想マシンを使用することをお勧めします。 このイメージは、Microsoft Azure 向けに事前に構成されており... |Windows |127 GB |
| Microsoft SQL Server グループ |SQL Server 2012 SP2 Enterprise on Windows Server 2012 |このイメージには、SQL Server の完全バージョンが含まれています。 一部の SQL Server コンポーネントでは、使用前ｊに設定や構成を追加する必要があります。 A3 以上のサイズの仮想マシンを使用することをお勧めします。 ... |Windows |127 GB |
| Microsoft SQL Server グループ |Windows Server 2012 R2 の SQL Server 2012 SP3 Web |このイメージには、SQL Server の完全バージョンが含まれています。 運用環境のワークロードには、DS3 以上のサイズの仮想マシンを使用することをお勧めします。 このイメージは、Microsoft Azure 向けに事前に構成されており... |Windows |127 GB |
| Microsoft SQL Server グループ |Windows Server 2012 R2 の SQL Server 2016 RTM Enterprise |このイメージには、SQL Server の完全バージョンが含まれています。 運用環境のワークロードには、DS3 以上のサイズの仮想マシンを使用することをお勧めします。 このイメージは、Microsoft Azure 向けに事前に構成されており... |Windows |127 GB |
| Microsoft Windows Server グループ |Windows Server 2012 R2 Datacenter、2016 年 6 月 |Windows Server 2012 R2 は Microsoft クラウド OS ビジョンの中核として、クラウド サービスに関する Microsoft の世界規模の経験をユーザーのインフラストラクチャにお届けします。 そのパフォーマンスは企業規模の... |Windows |128 GB |
| Microsoft Windows Server グループ |Windows Server 2012 R2 Datacenter、April 2016 |Windows Server 2012 R2 は Microsoft クラウド OS ビジョンの中核として、クラウド サービスに関する Microsoft の世界規模の経験をユーザーのインフラストラクチャにお届けします。 そのパフォーマンスは企業規模の... |Windows |128 GB |
| Microsoft Windows Server グループ |Windows Server 2012 R2 Datacenter、February 2016 |Windows Server 2012 R2 は Microsoft クラウド OS ビジョンの中核として、クラウド サービスに関する Microsoft の世界規模の経験をユーザーのインフラストラクチャにお届けします。 そのパフォーマンスは企業規模の... |Windows |128 GB |
| Microsoft Windows Server グループ |Windows Server 2012 R2 Datacenter、January 2016 |Windows Server 2012 R2 は Microsoft クラウド OS ビジョンの中核として、クラウド サービスに関する Microsoft の世界規模の経験をユーザーのインフラストラクチャにお届けします。 そのパフォーマンスは企業規模の... |Windows |128 GB |
| Microsoft Windows Server グループ |Windows Server 2016 Technical Preview 5 - Nano Server |Nano Server は、フル サイズのオペレーティング システムの一部で実行される Windows Server 用の新しいヘッドレス デプロイ オプションです。 このテクニカル プレビューでは、Nano Server を試すことができます。 |Windows |8 GB |
| Microsoft Windows Server グループ |Windows Server 2012 R2 Datacenter、2016 年 7 月 |Windows Server 2012 R2 は Microsoft クラウド OS ビジョンの中核として、クラウド サービスに関する Microsoft の世界規模の経験をユーザーのインフラストラクチャにお届けします。 そのパフォーマンスは企業規模の... |Windows |128 GB |
| Microsoft Windows Server グループ |Windows Server 2012 R2 Datacenter、December 2015 |Windows Server 2012 R2 は Microsoft クラウド OS ビジョンの中核として、クラウド サービスに関する Microsoft の世界規模の経験をユーザーのインフラストラクチャにお届けします。 そのパフォーマンスは企業規模の... |Windows |128 GB |
| Microsoft Windows Server グループ |Windows Server 2008 R2 SP1、2016 年 8 月 |Windows Server 2008 R2 は、サーバーやプライベート クラウド インフラストラクチャの信頼性や柔軟性を高めるように構築された多目的サーバーで、時間やコストの削減に役立ちます。  また... |Windows |128 GB |
| Microsoft Windows Server グループ |Windows Server 2012 Datacenter、2016 年 7 月 |Windows Server 2012 は、パブリック クラウドの構築や運用における Microsoft の経験をもとに構築された、ダイナミックで可用性の高いサーバー プラットフォームです。 スケーラブルでダイナミック、マルチ テナント... |Windows |128 GB |
| Microsoft Windows Server グループ |Windows Server 2012 Datacenter、2016 年 8 月 |Windows Server 2012 は、パブリック クラウドの構築や運用における Microsoft の経験をもとに構築された、ダイナミックで可用性の高いサーバー プラットフォームです。 スケーラブルでダイナミック、マルチ テナント... |Windows |128 GB |
| Microsoft Windows Server グループ |Windows Server 2016 with Containers Tech Preview 5 |Windows Server はクラウド サービスに関する Microsoft の世界規模の経験をユーザーのデータセンターのインフラストラクチャにお届けします。 Windows Server 2016 with Containers Technical Preview 5 ... |Windows |128 GB |
| Microsoft Windows Server グループ |Windows Server 2012 R2 Datacenter、November 2015 |Windows Server 2012 R2 は Microsoft クラウド OS ビジョンの中核として、クラウド サービスに関する Microsoft の世界規模の経験をユーザーのインフラストラクチャにお届けします。 そのパフォーマンスは企業規模の... |Windows |128 GB |
| Microsoft Windows Server グループ |Windows Server 2008 R2 SP1、November 2015 |Windows Server 2008 R2 は、サーバーやプライベート クラウド インフラストラクチャの信頼性や柔軟性を高めるように構築された多目的サーバーで、時間やコストの削減に役立ちます。  また... |Windows |128 GB |
| Microsoft Windows Server グループ |Windows Server 2012 Datacenter、April 2016 |Windows Server 2012 は、パブリック クラウドの構築や運用における Microsoft の経験をもとに構築された、ダイナミックで可用性の高いサーバー プラットフォームです。 スケーラブルでダイナミック、マルチ テナント... |Windows |128 GB |
| Microsoft Windows Server グループ |Windows Server 2012 Datacenter、2016 年 6 月 |Windows Server 2012 は、パブリック クラウドの構築や運用における Microsoft の経験をもとに構築された、ダイナミックで可用性の高いサーバー プラットフォームです。 スケーラブルでダイナミック、マルチ テナント... |Windows |128 GB |
| Microsoft Windows Server グループ |Windows Server 2016 Technical Preview 5 |Windows Server 2016 Technical Preview は、クラウド対応のオペレーティング システムを体験する機会をもたらし、アプリケーションとインフラストラクチャに新しいセキュリティ レイヤーと Azure に触発された革新性を提供し... |Windows |128 GB |
| Microsoft Windows Server グループ |Windows Server 2012 R2 Datacenter、2016 年 8 月 |Windows Server 2012 R2 は Microsoft クラウド OS ビジョンの中核として、クラウド サービスに関する Microsoft の世界規模の経験をユーザーのインフラストラクチャにお届けします。 そのパフォーマンスは企業規模の... |Windows |128 GB |
| Microsoft Windows Server グループ |Windows Server 2008 R2 SP1、December 2015 |Windows Server 2008 R2 は、サーバーやプライベート クラウド インフラストラクチャの信頼性や柔軟性を高めるように構築された多目的サーバーで、時間やコストの削減に役立ちます。  また... |Windows |128 GB |
| Microsoft Windows Server グループ |Windows Server 2008 R2 SP1、January 2016 |Windows Server 2008 R2 は、サーバーやプライベート クラウド インフラストラクチャの信頼性や柔軟性を高めるように構築された多目的サーバーで、時間やコストの削減に役立ちます。  また... |Windows |128 GB |
| Microsoft Windows Server グループ |Windows Server 2008 R2 SP1、February 2016 |Windows Server 2008 R2 は、サーバーやプライベート クラウド インフラストラクチャの信頼性や柔軟性を高めるように構築された多目的サーバーで、時間やコストの削減に役立ちます。  また... |Windows |128 GB |
| Microsoft Windows Server グループ |Windows Server 2008 R2 SP1、2016 年 7 月 |Windows Server 2008 R2 は、サーバーやプライベート クラウド インフラストラクチャの信頼性や柔軟性を高めるように構築された多目的サーバーで、時間やコストの削減に役立ちます。  また... |Windows |128 GB |
| Microsoft Windows Server グループ |Windows Server 2008 R2 SP1、2016 年 6 月 |Windows Server 2008 R2 は、サーバーやプライベート クラウド インフラストラクチャの信頼性や柔軟性を高めるように構築された多目的サーバーで、時間やコストの削減に役立ちます。  また... |Windows |128 GB |
| Microsoft Windows Server グループ |Windows Server 2012 Datacenter、November 2015 |Windows Server 2012 は、パブリック クラウドの構築や運用における Microsoft の経験をもとに構築された、ダイナミックで可用性の高いサーバー プラットフォームです。 スケーラブルでダイナミック、マルチ テナント... |Windows |128 GB |
| Microsoft Windows Server グループ |Windows Server 2012 Datacenter、December 2015 |Windows Server 2012 は、パブリック クラウドの構築や運用における Microsoft の経験をもとに構築された、ダイナミックで可用性の高いサーバー プラットフォームです。 スケーラブルでダイナミック、マルチ テナント... |Windows |128 GB |
| Microsoft Windows Server グループ |Windows Server 2012 Datacenter、January 2016 |Windows Server 2012 は、パブリック クラウドの構築や運用における Microsoft の経験をもとに構築された、ダイナミックで可用性の高いサーバー プラットフォームです。 スケーラブルでダイナミック、マルチ テナント... |Windows |128 GB |
| Microsoft Windows Server グループ |Windows Server 2008 R2 SP1、2016 年 4 月 |Windows Server 2008 R2 は、サーバーやプライベート クラウド インフラストラクチャの信頼性や柔軟性を高めるように構築された多目的サーバーで、時間やコストの削減に役立ちます。  また... |Windows |128 GB |
| Microsoft Windows Server グループ |Windows Server 2012 Datacenter、February 2016 |Windows Server 2012 は、パブリック クラウドの構築や運用における Microsoft の経験をもとに構築された、ダイナミックで可用性の高いサーバー プラットフォームです。 スケーラブルでダイナミック、マルチ テナント... |Windows |128 GB |
| OpenLogic |OpenLogic 6.7 |この Linux のディストリビューションは CentOS バージョン 6.7 に基づいており、OpenLogic によって提供されます。 Basic Server パッケージのインストールが含まれています。 |Linux |30 GB |
| OpenLogic |OpenLogic 6.8 |この Linux のディストリビューションは CentOS バージョン 7.2 に基づいており、OpenLogic によって提供されます。 Basic Server パッケージのインストールが含まれています。 |Linux |30 GB |
| OpenLogic |OpenLogic 7.2 |この Linux のディストリビューションは CentOS バージョン 7.2 に基づいており、OpenLogic によって提供されます。 Basic Server パッケージのインストールが含まれています。 |Linux |30 GB |
| OpenLogic |OpenLogic 7.2 |この Linux のディストリビューションは CentOS バージョン 7.2 に基づいており、OpenLogic によって提供されます。 Basic Server パッケージのインストールが含まれています。 |Linux |30 GB |
| OpenLogic |OpenLogic 7.2n |この Linux のディストリビューションは CentOS バージョン 7.2 に基づいており、OpenLogic によって提供されます。 Basic Server パッケージのインストールが含まれています。 このイメージは、標準の CentOS 7.2 イメージと同じ... |Linux |30 GB |
| Oracle |Oracle WebLogic Server 12.1.2 on Oracle Linux 6.4.0.0.0 |Oracle WebLogic Server 12c Enterprise Edition は優れた Java EE アプリケーション サーバーで、次世代アプリケーションをミッション クリティカルなクラウド プラットフォームで提供し、ネイティブなクラウド管理、... |Linux |30 GB |
| Oracle |Oracle Database 12.1.0.1 Standard Edition on Oracle Linux 6.4.0.0.0 |Oracle Database 12c Standard Edition は、中規模の企業に最適な、高すぎず、機能を取り揃えたデータ管理ソリューションです。 詳細については、http://www.oracle.com/database を参照してください。 |Linux |40 GB |
| Oracle |Oracle Linux 7.0.0.0.0 |Oracle Linux 7.0.0.0 は、エンタープライズ アプリケーションおよびシステムに対して、きわめて高いパフォーマンス、スケーラビリティ、信頼性を提供します。 エンタープライズのワークロードに最適化されている Oracle Linux は、... |Linux |30 GB |
| Oracle |Oracle Database 12.1.0.1 Enterprise Edition on Oracle Linux 6.4.0.0.0 |Oracle Database 12c Enterprise Edition は、クラウド向けの次世代データベースで、新しいマルチテナント アーキテクチャを、速度、拡張性、信頼性、安全性に優れたデータベース プラットフォームで提供... |Linux |40 GB |
| リモート デスクトップ ホスト |Windows Server Remote Desktop Session Host with Microsoft Office 365 ProPlus |このイメージには、共有コンピューターを持つ Microsoft Office 365 ProPlus と共にリモート デスクトップ セッション ホスト (RD セッション ホスト) のロールがインストールされている Windows Server 2012 R2 オペレーティング システムが含まれています... |Windows |127 GB |
| リモート デスクトップ ホスト |Windows Server Remote Desktop Session Host on Windows Server 2012 R2 |このイメージには、リモート デスクトップ セッション ホスト (RD セッション ホスト) のロールがインストールされている Windows Server 2012 R2 オペレーティング システムが含まれています。 このイメージは、Microsoft Azure 向けに事前に構成されます。 RD セッション... |Windows |127 GB |
| SharePoint |SharePoint Server 2016 評価版 |Windows Server 2012 R2 Datacenter の Microsoft SharePoint Server 2016 評価版。 この評価版のイメージから作成された仮想マシンは、2016 年 9 月 19 日に有効期限が切れます。 このイメージには完全なインストールが含まれています... |Windows |128 GB |
| SharePoint |SharePoint Server 2013 評価版 |Windows Server 2012 Datacenter の Microsoft SharePoint Server 2013 評価版。 この評価版のイメージから作成された仮想マシンは、2016 年 9 月 19 日に有効期限が切れます。 このイメージには完全なインストールが含まれています... |Windows |128 GB |
| SUSE |SUSE Linux Enterprise Server 12 SP1 |Microsoft Azure 上の SUSE Linux Enterprise Server で実稼働ワークロードを実行すると、サービス レベルが保証され、SUSE と Microsoft のエンジニアからのサポートが得られます... |Linux |30 GB |
| SUSE |SUSE Linux Enterprise Server 12 SP1 |Microsoft Azure 上の SUSE Linux Enterprise Server で実稼働ワークロードを実行すると、サービス レベルが保証され、SUSE と Microsoft のエンジニアからのサポートが得られます... |Linux |30 GB |
| SUSE |SUSE Linux Enterprise Server 11 SP4 |Microsoft Azure 上の SUSE Linux Enterprise Server で実稼働ワークロードを実行すると、サービス レベルが保証され、SUSE と Microsoft のエンジニアからのサポートが得られます... |Linux |30 GB |
| SUSE |openSUSE Leap 42.1 |安定していて革新的で簡単です。 1 年をかけたツールと手順の継続的な改善、膨大な時間をかけた開発、パッケージ化、およびテストから、安定したリリースが生まれ... |Linux |30 GB |
| SUSE |SAP Cloud Appliance Library の SUSE Linux Enterprise Server 11 SP4 |SAP Cloud Appliance Library イメージ用 SUSE Linux Enterprise Server 11 SP4 は、SAP Cloud Appliance Library の基本イメージです。 SAP Cloud Appliance Library をデプロイするときに自動的に使用され... |Linux |30 GB |
| SUSE |SUSE Linux Enterprise Server 12 SP1 |Microsoft Azure 上の SUSE Linux Enterprise Server で実稼働ワークロードを実行すると、サービス レベルが保証され、SUSE と Microsoft のエンジニアからのサポートが得られます... |Linux |30 GB |
| SUSE |SUSE Linux Enterprise Server 11 SP4 |Microsoft Azure 上の SUSE Linux Enterprise Server で実稼働ワークロードを実行すると、サービス レベルが保証され、SUSE と Microsoft のエンジニアからのサポートが得られます... |Linux |30 GB |
| SUSE |SUSE Linux Enterprise Server 11 SP4 |Microsoft Azure 上の SUSE Linux Enterprise Server で実稼働ワークロードを実行すると、サービス レベルが保証され、SUSE と Microsoft のエンジニアからのサポートが得られます... |Linux |30 GB |
| Visual Studio |Windows Server 2012 R2 の Team Foundation Server 2013 Update 4 |Windows Server 2012 R2 Update の Microsoft Team Foundation Server 2013 評価版。 この評価版のイメージから作成された仮想マシンには、Team Foundation Server (MSDN サブスクリプションなど) のプロダクト キーが必要です。 |Windows |128 GB |
| Visual Studio |Windows Server 2012 R2 の Visual Studio Community 2013 Update 5 with Tools for Apache Cordova CTP 3.2 |このイメージは、Windows Server 2012 R2 で実行されている Apache Cordova CTP 3.2 に Visual Studio Community 2013 Update 5、Azure Tools 2.8、およびツールを提供します。  これを使用すると、簡単かつ迅速に設定... |Windows |128 GBVisual Studio |
| Visual Studio |Windows Server 2012 R2 の Visual Studio Community 2015 with Azure SDK 2.9 |Visual Studio Community 2015 は、エンタープライズ以外のアプリケーション開発向けの、機能豊富で拡張可能な無料 IDE です。  このイメージには、Windows Server 2012 R2 と Visual Studio Community 2015 が含まれています  ... |Windows |128 GB |
| Visual Studio |Windows Server 2012 R2 の Visual Studio Community 2015 Update 2 with Azure SDK 2.9 |Visual Studio Community 2015 Update 2 は、エンタープライズ以外のアプリケーション開発向けの、機能豊富で拡張可能な無料 IDE です。 このイメージには、Windows Server 2012 R2 と Visual Studio Community... |Windows |128 GB |
| Visual Studio |Windows Server 2012 R2 の Visual Studio Enterprise 2015 with Azure SDK 2.9 |Visual Studio Enterprise では、開発環境が一貫した単一のソリューションを使用して、デバイスおよびサービスでアプリケーションを作成できます。  ツールを入手して、デスクトップ... |Windows |128 GB |
| Visual Studio |Windows Server 2012 R2 の Visual Studio Enterprise 2015 Update 3 with Azure SDK 2.9.1 |Visual Studio Enterprise 2015 Update 3 では、開発環境が一貫した単一のソリューションを使用して、デバイスおよびサービスでアプリケーションを作成できます。 ツールを入手して、実行する必要があります... |Windows |128 GB |
| Visual Studio |Windows Server 2012 R2 の Visual Studio Community 2013 Update 5 with Azure 2.9 |Visual Studio Community 2013 Update 5 with Azure 2.9 のこのイメージを使用すると、Visual Studio の全機能を最大限まで活用して、クロスプラットフォーム ソリューションを開発することができます。  統合された&1; つの IDE でアプリを作成し... |Windows |128 GB |
| Visual Studio |Visual Studio Enterprise 2015 Update 2 with Azure SDK 2.9 on Windows Server 2012 R2 |Visual Studio Enterprise 2015 Update 2 では、開発環境が一貫した単一のソリューションを使用して、デバイスおよびサービスでアプリケーションを作成できます。  ツールを入手して、実行する必要があります... |Windows |128 GB |

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged -->

## <a name="quick-start-templates"></a>クイック スタート テンプレート
### <a name="step-1"></a>手順 1
Azure クイック スタート テンプレート GitHub リポジトリに移動し、次のソリューション テンプレートの一覧から選択します。

| クイック スタート テンプレート |
| --- |
| <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/ethereum-consortium-blockchain-network"> Ethereum Consortium Blockchain Network </a> |
| <a href="https://github.com/Azure/azure-quickstart-templates/tree/052db5feeba11f85d57f170d8202123511f72044/splunk-on-ubuntu"> Splunk on Ubuntu </a> |
| <a href="https://github.com/Azure/azure-quickstart-templates/tree/058f18cbb43165b043e5bb56a82a406290c02dac/cisco-csr-1000v"> Cisco CSR1000v (2 NIC) </a> |
| <a href="https://github.com/Azure/azure-quickstart-templates/tree/058f18cbb43165b043e5bb56a82a406290c02dac/cisco-csr-1000v-4-nic"> Cisco CSR1000v (4 NIC) </a> |
| <a href="https://github.com/Azure/azure-quickstart-templates/tree/058f18cbb43165b043e5bb56a82a406290c02dac/cisco-csr-1000v-existing-vnet-4-nic"> Cisco CSR1000v (4 NIC の既存の Vnet) </a> |

### <a name="step-2"></a>手順 2.
Readme ファイルで、ページの上部にある [Deploy to Azure Government (Azure Government にデプロイ)] ボタン/リンクをクリックしてテンプレートを起動します。

### <a name="step-3"></a>手順 3.
ブラウザーのページに、Azure Government ポータルの [テンプレートのデプロイ] ブレードが開きます。 [パラメーター] ブレードで、必須フィールドに入力し、[OK] をクリックします。

### <a name="step-4"></a>手順 4.
使用条件に同意し、[ダッシュボードにピン留めする] ボックスをオンにしてから [作成] をクリックします。

> [!NOTE]
> 続行するには、有効な Azure Government アカウントが必要です。 Azure Government の機能を組織で体験するには、[Azure Government 試用版](https://azuregov.microsoft.com/trial/azuregovtrial)にサインアップします。
> 
> 

### <a name="step-5"></a>手順 5.
ブレードが閉じ、イメージのデプロイを示しているポータルのダッシュ ボード ビューに戻ります。 これには数分かかります。 VM ソリューションが実行されると、ダッシュ ボードに表示されます。 手順 4 で [ダッシュボードにピン留めする] オプションを選択しなかった場合は、リソースで VM を検索する必要があります。

> [!NOTE]
> ソリューション テンプレートは、Microsoft とサード パーティ プロバイダーから開始点として使用するためにのみ提供されています。 Microsoft Azure と Azure Government で実行されるアプリケーションの開発、実装、および管理とサブスクリプションの管理を、適用法、規制、および契約上の義務に準拠して行うことについては、お客様に全責任がかかります。  これらのテンプレートは "現状有姿" で提供され、明示的、黙示的、または制定法上のいずれを問わず、いかなる種類の保証もありません。Microsoft は、商品性、特定目的への適合性、または非侵害に関するすべての保証を明示的に否定します。
> 
> 

## <a name="next-steps"></a>次のステップ
イメージ ギャラリーからのデプロイについて、また VHD の作成についての詳細は、次をご覧ください。  Azure Government 使用時におけるエンドポイントのプログラム的な相違点の詳細について、[Azure Government 開発者向けガイド](documentation-government-developer-guide.md)を参照してください。

### <a name="other-resources"></a>その他のリソース:
* [Windows を実行する仮想マシンの作成](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [仮想マシンに関する FAQ](../virtual-machines/virtual-machines-windows-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Linux 仮想マシンをキャプチャする方法](../virtual-machines/virtual-machines-linux-classic-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

<!--- **<A href="/azure-government-service-description">Azure Government Service Descriptions</a>**-->




<!-- Images. -->

[1]: ./media/azure-government-developer-guide/publisherguide.png
[2]: ./media/azure-government-overview/azure-gov-overview.jpg

<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines/virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: app-service-web/web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md

