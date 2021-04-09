---
title: Azure で動作保証済みの Linux ディストリビューション
description: Azure での動作保証済み Linux ディストリビューションについて、Ubuntu、CentOS、Oracle、および SUSE に関する指針も含めて、説明します。
services: virtual-machines
documentationcenter: ''
author: danielsollondon
manager: gwallace
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: conceptual
ms.date: 01/03/2021
ms.author: guybo
ms.openlocfilehash: 091a06fd608763ac7265670733890e93bf71dde1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102547409"
---
# <a name="endorsed-linux-distributions-on-azure"></a>Azure で動作保証済みの Linux ディストリビューション

パートナーが Azure Marketplace で Linux イメージを提供しています。 Microsoft では、さまざまな Linux コミュニティと連携して動作保証済みディストリビューション リストを拡充しています。 Marketplace から入手できないディストリビューションについては、「[Linux オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード](./create-upload-generic.md)」のガイドラインに従って、お使いの Linux をいつでも持ち込んで使用できます。

## <a name="supported-distributions-and-versions"></a>サポートされているディストリビューションとバージョン

次の表に、Azure でサポートされている Linux ディストリビューションとバージョンを示します。 詳細については、[Microsoft Azure での Linux イメージのサポート](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure)に関するページを参照してください。

Hyper-V および Azure 用の Linux Integration Services (LIS) ドライバーは、Microsoft からアップストリームの Linux カーネルに直接提供されるカーネル モジュールです。 既定でディストリビューションのカーネルに組み込まれている LIS ドライバーもあります。 Red Hat Enterprise (RHEL)/CentOS に基づく古いディストリビューションは、[Linux Integration Services Version 4.2 for Hyper-V and Azure](https://www.microsoft.com/download/details.aspx?id=55106) で個別にダウンロードして入手できます。 詳細については、「[Linux カーネルの要件](create-upload-generic.md#linux-kernel-requirements)」を参照してください。

Azure Linux エージェントは、Azure Marketplace イメージにプレインストールされており、通常はディストリビューションのパッケージ リポジトリから入手できます。 ソース コードは [GitHub](https://github.com/azure/walinuxagent)にあります。

| Distribution | Version | ドライバー | エージェント |
| --- | --- | --- | --- |
| Rogue Wave Software の CentOS |CentOS 6.x、7.x、8.x |CentOS 6.3:[LIS のダウンロード](https://www.microsoft.com/download/details.aspx?id=55106)<p>CentOS 6.4 以降:カーネル内 |パッケージ:[リポジトリ](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/)内の "WALinuxAgent" <br/>ソース コード:[GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/)<p> CoreOS は、2020 年 5 月 26 日付けで[サポートが終了](https://coreos.com/os/eol/)します。 |利用不可 | | |
| Credativ の Debian |8.x、9.x、10.x |カーネル内 |パッケージ:リポジトリ内の "waagent" <br/>ソース コード:[GitHub](https://github.com/Azure/WALinuxAgent) |
|Kinvolk の Flatcar Container Linux| Pro、安定版、ベータ版| カーネル内 | wa-linux-agent は /usr/share/oem/bin/waagent に既にインストールされています |
| Oracle の Oracle Linux |6.x、7.x、8.x |カーネル内 |パッケージ:リポジトリ内の "WALinuxAgent" <br/>ソース コード:[GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| [Red Hat の Red Hat Enterprise Linux](../workloads/redhat/overview.md) |6.x、7.x、8.x |カーネル内 |パッケージ:リポジトリ内の "WALinuxAgent" <br/>ソース コード:[GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE の SUSE Linux Enterprise |SLES/SLES for SAP 11.x、12.x、15.x <br/> [SUSE Public Cloud Image Lifecycle (SUSE パブリック クラウドのイメージのライフ サイクル)](https://www.suse.com/c/suse-public-cloud-image-life-cycle/) |カーネル内 |パッケージ:<p> [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) リポジトリ内の 11 の場合<br>"python-azure-agent" の下の "パブリック クラウド" モジュールに含まれる 12 の場合<br/>ソース コード:[GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| SUSE の openSUSE |openSUSE Leap 15.x |カーネル内 |パッケージ:[Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) リポジトリ内の "python-azure-agent" <br/>ソース コード:[GitHub](https://github.com/Azure/WALinuxAgent) |
| Canonical の Ubuntu |Ubuntu Server および Pro 16.x、18.x、20.x<p>Ubuntu 12.04 および 14.04 の拡張サポートについては、「[Ubuntu Extended Security Maintenance (Ubuntu 拡張セキュリティのメンテナンス)](https://www.ubuntu.com/esm)」を参照してください。 |カーネル内 |パッケージ:リポジトリ内の "WALinuxAgent" <br/>ソース コード:[GitHub](https://github.com/Azure/WALinuxAgent) |

## <a name="image-update-cadence"></a>イメージの更新頻度

Azure の場合、動作保証済み Linux ディストリビューションのパブリッシャーは、四半期ごと、またはそれより短い頻度で、最新のパッチとセキュリティ修正プログラムを適用して、Azure Marketplace 内のイメージを定期的に更新する必要があります。 Marketplace 内の更新されたイメージは、新しいバージョンのイメージ SKU として、自動的に顧客に提供されます。 Linux イメージの検索方法の詳細については、[Azure Marketplace での Linux VM イメージの検索](./cli-ps-findimage.md)に関する記事を参照してください。

## <a name="azure-tuned-kernels"></a>Azure で調整されたカーネル

Azure は、各種の動作保証済み Linux ディストリビューションと密接に連携して、Azure Marketplace に公開されたイメージを最適化しています。 この共同作業の 1 つとして、Azure プラットフォーム用に最適化された "調整済み" Linux カーネルの開発があります。このカーネルは、完全にサポートされる Linux ディストリビューションのコンポーネントとして提供されます。 Azure で調整されたカーネルは、新機能を備えており、パォーマンスが向上しています。また、ディストリビューションで提供される既定のカーネルや汎用のカーネルと比べて、更新頻度が高くなっています (通常は四半期ごと)。

ほとんどの場合、これらのカーネルは、Azure Marketplace 内の既定のイメージにプレインストールされています。そのため、顧客は、これらの最適化されたカーネルの利点をすぐに享受できます。 Azure で調整されたカーネルの詳細については、次のリンクを参照してください。

- [CentOS の Azure で調整されたカーネル - CentOS 仮想化 SIG 経由で使用可能](https://wiki.centos.org/SpecialInterestGroup/Virtualization)
- [Debian クラウド カーネル - Azure 上の Debian 10 および Debian 9 の "backports" イメージで使用可能](https://wiki.debian.org/Cloud/MicrosoftAzure)
- [SLES の Azure で調整されたカーネル](https://www.suse.com/c/a-different-builtin-kernel-for-azure-on-demand-images/)
- [Ubuntu の Azure で調整されたカーネル](https://blog.ubuntu.com/2017/09/21/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel)
- [Flatcar Container Linux Pro](https://azuremarketplace.microsoft.com/marketplace/apps/kinvolk.flatcar_pro)

## <a name="partners"></a>パートナー

### <a name="coreos"></a>CoreOS

CoreOS は、2020 年 5 月 26 日に[サポートが終了する](https://coreos.com/os/eol/)ようにスケジュールされています。
Microsoft では、CoreOS ユーザー向けに 2 つの移行チャネルを用意しています。

- Kinvolk の Flatcar ("Kinvolk の Flatcar Container Linux" 項目を参照してください。)
- [Fedora Core OS](https://docs.fedoraproject.org/en-US/fedora-coreos/provisioning-azure/) (顧客は独自のイメージをアップロードする必要があります。 こちらに[移行に関するドキュメント](https://docs.fedoraproject.org/en-US/fedora-coreos/migrate-cl/)があります)。

### <a name="credativ"></a>credativ

[https://www.credativ.de/en/portfolio/support/open-source-support-center/](https://www.credativ.de/en/portfolio/support/open-source-support-center/)

credativ は、無償のソフトウェアを使用したプロフェッショナルなソリューションの開発および実装を専門とする、独立したコンサルティングおよびサービス企業です。 大手のオープン ソース スペシャリストとして、credativ のサポートを利用している多くの IT 部門から国際的に認知されています。 Microsoft との業務提携で credativ は Debian イメージを準備しています。 イメージは、Azure で実行するために特別に設計され、プラットフォーム経由で簡単に管理できます。 credativ では、自社のオープン ソース サポート センターを通じて、Azure 用の Debian イメージの長期にわたる保守および更新もサポートします。

### <a name="kinvolk"></a>Kinvolk
[https://www.kinvolk.io/flatcar-container-linux/](https://www.kinvolk.io/flatcar-container-linux/)

Kinvolk は、Flatcar Container Linux を提供する企業です。コンテナー化されたアプリケーション用の、最小限で、変更不可の、自動更新の基盤のために、元の CoreOS ビジョンを引き続き使用しています。 最小限のディストリビューションである Flatcar には、コンテナーのデプロイに必要なパッケージのみが含まれています。 そのファイル システムは変更不可であり、一貫性とセキュリティを保証します。また、最新のセキュリティ修正プログラムを使用して、自動更新機能が常に最新の状態を保ちます。 

Flatcar Container Linux は、Kinvolk の Linux グローバル チームとコンテナー テクノロジの専門家によって支えられ、24 時間 365 日体制の対応、セキュリティ アラートとテクニカル アラート、専用の Azure 最適化イメージ、長期的なサポート チャネルを含め、商用サポート サブスクリプションがオプションで提供されています。


### <a name="oracle"></a>Oracle

[https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Oracle は、パブリック クラウドおよびプライベート クラウドに幅広いソリューション ポートフォリオを提供するという戦略を取っています。 この戦略により、顧客は、Oracle クラウドやその他のクラウドで Oracle ソフトウェアをデプロイするときに、その方法を複数の選択肢から選び、柔軟に対応することができます。 マイクロソフトと Oracle との提携により、お客様はマイクロソフトのパブリック クラウドおよびプライベート クラウドにおいて Oracle の保証とサポートの下で安心して Oracle のソフトウェアをデプロイできます。  Oracle のパブリックおよびプライベート クラウド ソリューションに対する取り組みと投資が変わることはありません。

### <a name="red-hat"></a>Red Hat

[https://www.redhat.com/en/partners/strategic-alliance/microsoft](https://www.redhat.com/en/partners/strategic-alliance/microsoft)

世界をリードするオープン ソース ソリューションのプロバイダーである Red Hat は、90% 超の Fortune 500 企業が、ビジネス上の課題を解決したり、IT およびビジネス戦略を調整したり、将来のテクノロジを準備するのを支援しています。 Red Hat は、オープン ビジネス モデルおよび手頃な価格の予測可能なサブスクリプション モデルを使用して、安全なソリューションを提供することによって、これを実現しています。

### <a name="suse"></a>SUSE

[https://www.suse.com/suse-linux-enterprise-server-on-azure](https://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server on Azure は、クラウド コンピューティングで優れた信頼性とセキュリティを実現する実績あるプラットフォームです。 SUSE の多機能な Linux プラットフォームは Azure クラウド サービスとシームレスに統合され、管理の容易なクラウド環境を実現します。 SUSE Linux Enterprise Server には、1,800 社を超える独立系ソフトウェア ベンダーによる 9,200 本を超える公認アプリケーションが存在し、SUSE は、データ センターで実行されているワークロードを Azure に安心して自信を持ってデプロイできることを保証しています。

### <a name="canonical"></a>Canonical

[https://www.ubuntu.com/cloud/azure](https://www.ubuntu.com/cloud/azure)

Canonical のエンジニアリングおよびオープン コミュニティ体制は、消費者向け個人クラウド サービスも含めて、クライアント、サーバー、およびクラウド コンピューティングにおける Ubuntu の成功を支えています。 Ubuntu における、携帯電話からクラウドまでの統一されたフリー プラットフォームという Canonical のビジョンにより、携帯電話、タブレット、テレビ、およびデスクトップで一貫したインターフェイス ファミリーが提供されます。 このビジョンにより、Ubuntu はパブリック クラウド プロバイダーから家電メーカーに至るまでの多様な企業の最初の選択肢となり、個々の技術者のお気に入りとなっています。

世界中に開発者と開発センターが存在しているため、Canonical は独自の地位を確立しており、ハードウェア メーカーやコンテンツ プロバイダー、ソフトウェア開発者と提携して、PC やサーバー、携帯機器など、さまざまな Ubuntu ソリューションを市場に送り出しています。
