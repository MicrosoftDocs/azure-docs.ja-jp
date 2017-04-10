---
title: "動作保証済み Linux ディストリビューション | Microsoft Docs"
description: "Azure での動作保証済み Linux ディストリビューションについて、Ubuntu、OpenLogic、Oracle、および SUSE に関する指針も含めて、説明します。"
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: szark
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: cd6c3be6a2667807f29156900b0a5f1fc63704d3
ms.lasthandoff: 03/27/2017


---
# <a name="linux-on-azure-endorsed-distributions"></a>Azure での動作保証済み Linux ディストリビューション
> [!NOTE]
> お時間がございましたら、Azure Linux VM ドキュメントの向上のため、こちらの [アンケート](https://aka.ms/linuxdocsurvey) にご回答ください。 いただいた回答は、今後のドキュメントの改善に活用させていただきます。
> 
> 

Azure ギャラリーまたは Marketplace にある Linux イメージは多くのパートナーから提供されており、Microsoft はさまざまな Linux コミュニティと協力して、動作保証済みディストリビューションの一覧をよりいっそう充実させようと努めています。 ギャラリーから利用できないディストリビューションの場合も、 [このページ](linux/classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)の指針に従うことにより、いつでも独自の Linux を展開することができます。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="supported-distributions--versions"></a>サポートされているディストリビューションとバージョン
次の表に、Azure でサポートされている Linux ディストリビューションとバージョンを示します。 詳細については、[Microsoft Azure での Linux イメージのサポート](https://support.microsoft.com/en-us/kb/2941892)に関するページを参照してください。

Hyper-V および Azure 用の Linux Integration Services (LIS) ドライバーは、Microsoft からアップストリームの Linux カーネルに直接提供されるカーネル モジュールです。  LIS ドライバーは、ディストリビューションのカーネルに既定で組み込まれているか、以前の RHEL/CentOS ベースのディストリビューションの場合は [こちらで](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409)個別にダウンロードする形で入手できます。  LIS ドライバーの詳細については、 [こちらの記事](virtual-machines-linux-create-upload-generic.md#linux-kernel-requirements) を参照してください。

Azure Linux エージェントは、Azure ギャラリーのイメージにあらかじめインストールされており、通常はディストリビューションのパッケージのリポジトリで入手できます。  ソース コードは [GitHub](https://github.com/azure/walinuxagent)にあります。

| ディストリビューション | バージョン | ドライバー | エージェント |
| --- | --- | --- | --- |
| CentOS by OpenLogic |CentOS 6.3+、7.0+ |CentOS 6.3: [LIS のダウンロード](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409)<p>CentOS 6.4 以上、カーネル内。 |パッケージ: [OpenLogic リポジトリ](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/)内の "WALinuxAgent" <br/>ソース コード: [GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) |494.4.0+ |カーネル内 |ソース コード: [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent) |
| Debian |Debian 7.9+、8.2+ |カーネル内 |パッケージ: リポジトリ内の "waagent"  <br/>ソース コード: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Oracle Linux |6.4+、7.0+ |カーネル内 |パッケージ: リポジトリ内の "WALinuxAgent"  <br/>ソース コード: [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998) |
| Red Hat Enterprise Linux |RHEL 6.7+、7.1+ |カーネル内 |パッケージ: リポジトリ内の "WALinuxAgent"  <br/>ソース コード: [GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise |SLES/SLES for SAP<br>11 SP4<br>12 SP1+|カーネル内 |パッケージ:<p> [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) リポジトリ内の 11 の場合<br>"python-azure-agent" の下の "パブリック クラウド" モジュールに含まれる 12 の場合<br/>ソース コード: [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE |openSUSE Leap 42.1+ |カーネル内 |パッケージ: [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) リポジトリ内の "python-azure-agent" <br/>ソース コード: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Ubuntu |Ubuntu 12.04、14.04、16.04、16.10 |カーネル内 |パッケージ: リポジトリ内の "WALinuxAgent"  <br/>ソース コード: [GitHub](https://github.com/Azure/WALinuxAgent) |

## <a name="partners"></a>パートナー
### <a name="openlogic"></a>OpenLogic
[http://www.openlogic.com/azure](http://www.openlogic.com/azure)

OpenLogic は、クラウドおよびデータ センター向けの企業オープン ソース ソリューションの一流プロバイダーです。 OpenLogic は、多種多様な業界の一流企業数百社に対して、オープン ソース ソフトウェアを安全に入手し、サポートし、コントロールできるよう支援しています。 OpenLogic は、600 種類のオープン ソース パッケージについて、OpenLogic Expert Community に支えられた商用品質のテクニカル サポートと保証を提供しています。たとえば、CentOS を企業レベルでサポートし、Azure に CentOS ベースのイメージを提供する開設時パートナー様でもあります。

### <a name="coreos"></a>CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

CoreOS Web サイトから入手:

*CoreOS はセキュリティ、一貫性、信頼性を実現するように設計されています。yum や apt でパッケージをインストールする代わりに、Linux コンテナーを使用して、より抽象的なレベルでサービスを管理します。コンテナーには 1 つのサービス コードとすべての依存関係がパッケージ化されており、1 つまたは複数の CoreOS マシン上で実行できます。*

### <a name="credativ"></a>Credativ
[http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ は、無償のソフトウェアを使用したプロフェッショナルなソリューションの開発および実装を専門とする、独立したコンサルティングおよびサービス企業です。 大手のオープン ソース スペシャリストとして、Credativ のサポートを利用している多くの IT 部門から国際的に認知されています。 Credativ は現在、マイクロソフトと協力して、Azure で実行するために特別に設計され、プラットフォーム経由で簡単に管理できる Debian 8 (Jessie) および 7 より前の Debian (Wheezy) に対応する Debian イメージを準備しています。 Credativ では、自社のオープン ソース サポート センターを通じて、Azure 用の Debian イメージの長期にわたる保守および更新もサポートします。

### <a name="oracle"></a>Oracle
[http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Oracle の戦略は、パブリック クラウドとプライベート クラウドに対して広範なソリューションを用意し、顧客が Oracle のクラウドや他社のクラウドにおいて Oracle のソフトウェアを柔軟にデプロイできるようにすることです。  マイクロソフトと Oracle との提携により、お客様はマイクロソフトのパブリック クラウドおよびプライベート クラウドにおいて Oracle の保証とサポートの下で安心して Oracle のソフトウェアをデプロイできます。  Oracle のパブリックおよびプライベート クラウド ソリューションに対する取り組みと投資が変わることはありません。

### <a name="red-hat"></a>Red Hat
[http://www.redhat.com/en/partners/strategic-alliance/microsoft](http://www.redhat.com/en/partners/strategic-alliance/microsoft)

世界をリードするオープン ソース ソリューションのプロバイダーである Red Hat は、90% 超の Fortune 500 企業が、ビジネス上の課題を解決したり、IT およびビジネス戦略を調整したり、将来のテクノロジを準備するのを支援しています。 Red Hat は、オープン ビジネス モデルおよび手頃な価格の予測可能なサブスクリプション モデルを使用して、安全なソリューションを提供することによってこれを実現しています。

### <a name="suse"></a>SUSE
[http://www.suse.com/suse-linux-enterprise-server-on-azure](http://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server on Azure は、クラウド コンピューティングで優れた信頼性とセキュリティを実現する実績あるプラットフォームです。 SUSE の多機能な Linux プラットフォームは Azure クラウド サービスとシームレスに統合され、管理の容易なクラウド環境を実現します。 SUSE Linux Enterprise Server には、1,800 社を超える独立系ソフトウェア ベンダーによる 9,200 本を超える公認アプリケーションが存在し、SUSE は、データ センターで実行されているワークロードを Azure に安心して自信を持ってデプロイできることを保証しています。

### <a name="canonical"></a>Canonical
[http://www.ubuntu.com/cloud/azure](http://www.ubuntu.com/cloud/azure)

Canonical のエンジニアリングおよびオープン コミュニティ体制は、消費者向け個人クラウド サービスも含めて、クライアント、サーバー、およびクラウド コンピューティングにおける Ubuntu の成功を支えています。 Canonical は、Ubuntu に関して、携帯電話、タブレット、テレビ、およびデスクトップで一貫したインターフェイスを備え、携帯電話からクラウドまで統一されたフリー プラットフォームにするというビジョンを持っているため、Ubuntu はパブリック クラウド プロバイダーから家電メーカーに至るまで多様な企業が最初に選択するディストリビューションとなり、個々の技術者のお気に入りとなっています。

世界中に開発者と開発センターが存在しているため、Canonical は独自の地位を確立しており、ハードウェア メーカーやコンテンツ プロバイダー、ソフトウェア開発者と提携して、PC からサーバーや携帯機器まで、さまざまな Ubuntu ソリューションを市場に送り出しています。


