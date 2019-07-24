---
title: Azure で動作保証済みの Linux ディストリビューション | Microsoft Docs
description: Azure での動作保証済み Linux ディストリビューションについて、Ubuntu、CentOS、Oracle、および SUSE に関する指針も含めて、説明します。
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: szark
ms.openlocfilehash: c0124d133fe2db9acb16251bc21ffb19f1b1268f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57992176"
---
# <a name="linux-distributions-endorsed-on-azure"></a>Azure で動作保証済みの Linux ディストリビューション
パートナーが Azure Marketplace で Linux イメージを提供しています。 Microsoft はさまざまな Linux コミュニティと連携し、動作保証済みディストリビューション リストを拡充しています。 それまでの間、Marketplace からご利用いただけないディストリビューションについては、「[Linux オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic)」のガイドラインに従って、独自の Linux をいつでも使用できます。

## <a name="supported-distributions-and-versions"></a>サポートされているディストリビューションとバージョン
次の表に、Azure でサポートされている Linux ディストリビューションとバージョンを示します。 Azure での Linux とオープン ソース テクノロジーのサポートの詳細については、[Microsoft Azure での Linux イメージのサポート](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure)に関するページをご覧ください。

Hyper-V および Azure 用の Linux Integration Services (LIS) ドライバーは、Microsoft からアップストリームの Linux カーネルに直接提供されるカーネル モジュールです。  既定でディストリビューションのカーネルに組み込まれている LIS ドライバーもあります。 Red Hat Enterprise (RHEL)/CentOS に基づく古いディストリビューションは、[Linux Integration Services Version 4.2 for Hyper-V and Azure](https://www.microsoft.com/en-us/download/details.aspx?id=55106) で個別にダウンロードして入手できます。 LIS ドライバーの詳細については、「[Linux カーネルの要件](create-upload-generic.md#linux-kernel-requirements)」を参照してください。

Azure Linux エージェントは、Azure Marketplace のイメージにあらかじめインストールされており、通常はディストリビューションのパッケージのリポジトリで入手できます。 ソース コードは [GitHub](https://github.com/azure/walinuxagent)にあります。

  
| ディストリビューション | Version | ドライバー | エージェント |
| --- | --- | --- | --- |
| CentOS |CentOS 6.3+、7.0+ |CentOS 6.3:[LIS のダウンロード](https://www.microsoft.com/en-us/download/details.aspx?id=55106)<p>CentOS 6.4 以降:カーネル内 |パッケージ:[リポジトリ](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/)内の "WALinuxAgent" <br/>ソース コード:[GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) |494.4.0+ |カーネル内 |ソース コード:[GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent) |
| Debian |Debian 7.9+、8.2+ |カーネル内 |パッケージ:リポジトリ内の "waagent" <br/>ソース コード:[GitHub](https://github.com/Azure/WALinuxAgent) |
| Oracle Linux |6.4+、7.0+ |カーネル内 |パッケージ:リポジトリ内の "WALinuxAgent" <br/>ソース コード:[GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| Red Hat Enterprise Linux |RHEL 6.7+、7.1+ |カーネル内 |パッケージ:リポジトリ内の "WALinuxAgent" <br/>ソース コード:[GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise |SLES/SLES for SAP<br>11 SP4<br>12 SP1+<br>15|カーネル内 |パッケージ:<p> [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) リポジトリ内の 11 の場合<br>"python-azure-agent" の下の "パブリック クラウド" モジュールに含まれる 12 の場合<br/>ソース コード:[GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE |openSUSE Leap 42.2 + |カーネル内 |パッケージ:[Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) リポジトリ内の "python-azure-agent" <br/>ソース コード:[GitHub](https://github.com/Azure/WALinuxAgent) |
| Ubuntu |Ubuntu 12.04+ **<sup>1</sup>** |カーネル内 |パッケージ:リポジトリ内の "WALinuxAgent" <br/>ソース コード:[GitHub](https://github.com/Azure/WALinuxAgent) |

  - **<sup>1</sup>** Azure での Ubuntu 12.04 のサポートの詳細については、[提供終了に関するお知らせ](https://azure.microsoft.com/blog/ubuntu-12-04-precise-pangolin-nearing-end-of-life/)の記事をご覧ください。


## <a name="partners"></a>パートナー

### <a name="coreos"></a>CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

CoreOS Web サイトから入手:

*CoreOS はセキュリティ、一貫性、信頼性を実現するように設計されています。yum や apt でパッケージをインストールする代わりに、Linux コンテナーを使用して、より抽象的なレベルでサービスを管理します。コンテナーには 1 つのサービス コードとすべての依存関係がパッケージ化されており、1 つまたは複数の CoreOS マシン上で実行できます。*

### <a name="credativ"></a>Credativ
[https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ は、無償のソフトウェアを使用したプロフェッショナルなソリューションの開発および実装を専門とする、独立したコンサルティングおよびサービス企業です。 大手のオープン ソース スペシャリストとして、Credativ のサポートを利用している多くの IT 部門から国際的に認知されています。 Credativ は現在、マイクロソフトと協力して、Debian 8 (Jessie) および 7 より前の Debian (Wheezy) に対応する Debian イメージを準備しています。 両方のイメージは、Azure で実行するために特別に設計され、プラットフォーム経由で簡単に管理できます。 Credativ では、自社のオープン ソース サポート センターを通じて、Azure 用の Debian イメージの長期にわたる保守および更新もサポートします。

### <a name="oracle"></a>Oracle
[https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Oracle は、パブリック クラウドおよびプライベート クラウドに幅広いソリューション ポートフォリオを提供するという戦略を取っています。 この戦略により、顧客は、Oracle クラウドやその他のクラウドで Oracle ソフトウェアをデプロイするときに、その方法を複数の選択肢から選び、柔軟に対応することができます。 マイクロソフトと Oracle との提携により、お客様はマイクロソフトのパブリック クラウドおよびプライベート クラウドにおいて Oracle の保証とサポートの下で安心して Oracle のソフトウェアをデプロイできます。  Oracle のパブリックおよびプライベート クラウド ソリューションに対する取り組みと投資が変わることはありません。

### <a name="red-hat"></a>Red Hat
[https://www.redhat.com/en/partners/strategic-alliance/microsoft](https://www.redhat.com/en/partners/strategic-alliance/microsoft)

世界をリードするオープン ソース ソリューションのプロバイダーである Red Hat は、90% 超の Fortune 500 企業が、ビジネス上の課題を解決したり、IT およびビジネス戦略を調整したり、将来のテクノロジを準備するのを支援しています。 Red Hat は、オープン ビジネス モデルおよび手頃な価格の予測可能なサブスクリプション モデルを使用して、安全なソリューションを提供することによってこれを実現しています。

### <a name="suse"></a>SUSE
[https://www.suse.com/suse-linux-enterprise-server-on-azure](https://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server on Azure は、クラウド コンピューティングで優れた信頼性とセキュリティを実現する実績あるプラットフォームです。 SUSE の多機能な Linux プラットフォームは Azure クラウド サービスとシームレスに統合され、管理の容易なクラウド環境を実現します。 SUSE Linux Enterprise Server には、1,800 社を超える独立系ソフトウェア ベンダーによる 9,200 本を超える公認アプリケーションが存在し、SUSE は、データ センターで実行されているワークロードを Azure に安心して自信を持ってデプロイできることを保証しています。

### <a name="canonical"></a>Canonical
[https://www.ubuntu.com/cloud/azure](https://www.ubuntu.com/cloud/azure)

Canonical のエンジニアリングおよびオープン コミュニティ体制は、消費者向け個人クラウド サービスも含めて、クライアント、サーバー、およびクラウド コンピューティングにおける Ubuntu の成功を支えています。 Ubuntu における、携帯電話からクラウドまでの統一されたフリー プラットフォームという Canonical のビジョンにより、携帯電話、タブレット、テレビ、およびデスクトップで一貫したインターフェイス ファミリーが提供されます。 このビジョンにより、Ubuntu はパブリック クラウド プロバイダーから家電メーカーに至るまでの多様な企業の最初の選択肢となり、個々の技術者のお気に入りとなっています。

世界中に開発者と開発センターが存在しているため、Canonical は独自の地位を確立しており、ハードウェア メーカーやコンテンツ プロバイダー、ソフトウェア開発者と提携して、PC やサーバー、携帯機器など、さまざまな Ubuntu ソリューションを市場に送り出しています。
