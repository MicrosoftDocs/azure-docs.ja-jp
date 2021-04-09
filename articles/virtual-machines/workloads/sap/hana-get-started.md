---
title: Azure Virtual Machines への SAP HANA のインストール | Microsoft Docs
description: Azure VM に SAP HANA をインストールするためのガイド
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: juergent
ms.openlocfilehash: 54bfd7c6e691efbd754e9ea2b804e16b3e07ebbe
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670962"
---
# <a name="installation-of-sap-hana-on-azure-virtual-machines"></a>Azure Virtual Machines への SAP HANA のインストール
## <a name="introduction"></a>はじめに
このガイドは、Azure Virtual Machines に HANA を正常にデプロイするための適切なリソースを見つけるのに役立ちます。 このガイドでは、Azure VM に SAP HANA をインストールする前に確認する必要があるドキュメント リソースを示します。 それにより、Azure VM での SAP HANA のサポートされている構成を行うための適切な手順を実行することができます。  

> [!NOTE]
> このガイドでは、Azure VM への SAP HANA のデプロイについて説明します。 SAP HANA を HANA Large Instances にデプロイする方法については、「[SAP HANA on Azure (L インスタンス) のインストールと構成の方法](./hana-installation.md)」を参照してください。
 
## <a name="prerequisites"></a>前提条件
このガイドは、次の事項を熟知していることを前提としています。
* SAP HANA と SAP NetWeaver およびそれらをオンプレミスにインストールする方法。
* Azure 上で SAP HANA および SAP アプリケーション インスタンスをインストールして操作する方法。
* 以下で説明されている概念と手順:
   * Azure Virtual Network の計画と Azure Storage の使用を含む、Azure 上での SAP デプロイの計画。 [Azure Virtual Machines での SAP NetWeaver の計画と実装に関するガイド](./planning-guide.md)を参照してください。
   * デプロイの基本原則と Azure で VM をデプロイする方法。 [SAP のための Azure Virtual Machines のデプロイ](./deployment-guide.md)に関する記事を参照してください。
   * 「[Azure 仮想マシンの SAP HANA の高可用性](./sap-hana-availability-overview.md)」で説明されている SAP HANA での高可用性の概念。

## <a name="step-by-step-before-deploying"></a>デプロイ前の詳細な手順
このセクションでは、Azure Virtual Machine への SAP HANA のインストールを開始する前に実行する必要があるさまざまな手順を一覧で示します。 順序は列挙されており、列挙されているとおりに実行する必要があります。

1. 可能なすべてのデプロイ シナリオが Azure でサポートされているわけではありません。 したがって、SAP HANA のデプロイで使用しようとしているシナリオについては、「[Azure 仮想マシンの SAP ワークロードでサポートされるシナリオ](./sap-planning-supported-configurations.md)」というドキュメントを確認してください。 シナリオが一覧に記載されていない場合、それはテストされておらず、結果としてサポートされていないと想定する必要があります。
2. SAP HANA デプロイのメモリ要件について大体の目安があると仮定した場合、適合する Azure VM を見つける必要があります。 [SAP のサポート ノート #1928533](https://launchpad.support.sap.com/#/notes/1928533) に記載されているように、SAP NetWeaver 用に認定されているすべての VM が SAP HANA の認定を受けているわけではありません。 SAP HANA の認定を受けた Azure VM に関する資料については、[SAP HANA ハードウェア ディレクトリ](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)という Web サイトを参照してください。 **S** で始まるユニットは [HANA Large Instances](./hana-overview-architecture.md) のユニットであり、Azure VM ではありません。
3. Azure VM の種類によって、SUSE Linux または Red Hat Linux のオペレーティング システムの最小リリースが異なります。 [SAP HANA ハードウェア ディレクトリ](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)という Web サイトで、SAP HANA 認定ユニット一覧にあるエントリをクリックして、このユニットの詳細データを取得する必要があります。 サポートされている HANA ワークロードに加えて、SAP HANA のそれらのユニットでサポートされている OS リリースが一覧表示されます。
4. オペレーティング システムのリリースについては、特定の最小カーネル リリースを考慮する必要があります。 これらの最小リリースは、次の SAP サポート ノートに記載されています。
    - [SAP サポート ノート #2814271 SAP HANA バックアップがチェックサム エラーにより Azure で失敗する](https://launchpad.support.sap.com/#/notes/2814271)
    - [SAP サポート ノート #2753418 タイマーのフォールバックが原因でパフォーマンスが低下する可能性がある](https://launchpad.support.sap.com/#/notes/2753418)
    - [SAP サポート ノート #2791572 Azure で Hyper-V に対する VDSO サポートがないため、パフォーマンスが低下する](https://launchpad.support.sap.com/#/notes/2791572)
4. 選択した仮想マシンの種類でサポートされている OS リリースに基づいて、目的の SAP HANA リリースがそのオペレーティング システムのリリースでサポートされているかどうかを確認する必要があります。 さまざまなオペレーティング システムのリリースでの SAP HANA リリースのサポート マトリックスについては、[SAP サポート ノート #2235581](https://launchpad.support.sap.com/#/notes/2235581) をお読みください。
5. Azure VM の種類、オペレーティング システムのリリース、SAP HANA のリリースの有効な組み合わせが見つかったと思われる場合は、SAP 製品の可用性マトリックスで確認する必要があります。 SAP 可用性マトリックスでは、SAP HANA データベースに対して実行しようとしている SAP 製品がサポートされているかどうかを確認できます。


## <a name="step-by-step-vm-deployment-and-guest-os-considerations"></a>VM のデプロイの詳細な手順とゲスト OS に関する考慮事項
このフェーズでは、VM をデプロイして HANA をインストールする手順を実行し、インストール後に、選択したオペレーティング システムを最終的に最適化する必要があります。

1. Azure ギャラリーから基本イメージを選択します。 SAP HANA 用に独自のオペレーティング システム イメージを構築する場合は、SAP HANA を正常にインストールするために必要なさまざまなパッケージをすべて把握しておく必要があります。 それ以外の場合は、Azure イメージ ギャラリーから SAP または SAP HANA 用の SUSE および Red Hat のイメージを使用することをお勧めします。 これらのイメージには、HANA を正常にインストールするために必要なパッケージが含まれています。 オペレーティング システム プロバイダーとのサポート契約に基づいて、ライセンスを持ち込むイメージを選択する必要があります。 または、サポートを含む OS イメージを選択します。
2. ライセンスを持ち込む必要があるゲスト OS イメージを選択した場合は、最新の修正プログラムをダウンロードして適用できるように、その OS イメージをサブスクリプションに登録する必要があります。 この手順では、パブリック インターネット アクセスが必要になります。 ただし、Azure 内に (たとえば、SMT サーバーの) プライベート インスタンスを設定している場合を除きます。
3. VM のネットワーク構成を決定します。 詳細については、「[Azure における SAP HANA インフラストラクチャの構成と運用](./hana-vm-operations.md)」ドキュメントを参照してください。 Azure の仮想ネットワーク カードに割り当てられるネットワーク スループット クォータはないことに注意してください。 そのため、さまざまな vNIC 経由でトラフィックを転送する唯一の目的は、セキュリティに関する考慮事項に基づいています。 複数の vNIC を経由するトラフィック ルーティングの複雑さと、セキュリティ面から適用される要件との間で、サポート可能な妥協点を見つけてください。
3. VM をデプロイして登録したら、最新の修正プログラムをオペレーティング システムに適用します。 登録は、独自のサブスクリプションに対するものです。 または、オペレーティング システムのサポートが含まれているイメージを選択した場合は、VM は既に修正プログラムにアクセスできます。 
4. SAP HANA に必要なチューニングを適用します。 これらのチューニングは、次の SAP サポート ノートに記載されています。

    - [SAP サポート ノート #2694118 - Azure での Red Hat Enterprise Linux HA アドオン](https://launchpad.support.sap.com/#/notes/2694118)
    - [SAP サポート ノート #1984787 - SUSE LINUX Enterprise Server 12: インストールに関する注意事項](https://launchpad.support.sap.com/#/notes/1984787) 
    - [SAP サポート ノート #2578899 - SUSE Linux Enterprise Server 15: インストールに関する注意事項](https://launchpad.support.sap.com/#/notes/2578899)
    - [SAP support note #2002167 - Red Hat Enterprise Linux 7.x: インストールとアップグレード](https://launchpad.support.sap.com/#/notes/0002002167)
    - [SAP support note #2292690 - SAP HANA DB:Recommended OS settings for RHEL 7 (SAP HANA DB: RHEL 7 に推奨される OS 設定)](https://launchpad.support.sap.com/#/notes/0002292690) 
    -  [SAP サポート ノート #2772999 - Red Hat Enterprise Linux 8.x: インストールと構成](https://launchpad.support.sap.com/#/notes/2772999) 
    -  [SAP サポート ノート #2777782 - SAP HANA DB: RHEL 8 に推奨される OS 設定](https://launchpad.support.sap.com/#/notes/2777782)
    -  [SAP サポート ノート #2455582 - Linux:GCC 6.x でコンパイルされた SAP アプリケーションの実行](https://launchpad.support.sap.com/#/notes/0002455582)
    -  [SAP サポート ノート #2382421 - HANA および OS レベルでのネットワーク構成の最適化](https://launchpad.support.sap.com/#/notes/2382421)

1. SAP HANA の Azure Storage の種類を選択します。 この手順では、SAP HANA インストールのストレージ レイアウトを決定する必要があります。 接続されている Azure ディスクまたはネイティブ Azure NFS 共有のいずれかを使用します。 サポートされている Azure Storage の種類、および使用できるさまざまな Azure Storage の種類の組み合わせについては、「[SAP HANA Azure 仮想マシンのストレージ構成](./hana-vm-operations-storage.md)」で説明されています。 記載されている構成を開始点とします。 非運用環境システムでは、より低いスループットまたは IOPS を構成できる場合があります。 運用を目的とする場合は、もう少し多くのスループットと IOPS の構成が必要になる可能性があります。
2. M シリーズや Mv2 シリーズの VM を使用している場合は、DBMS トランザクション ログや REDO ログを含むボリュームに対して [Azure 書き込みアクセラレータ](../../how-to-enable-write-accelerator.md)を構成していることを確認します。 ドキュメントに記載されている書き込みアクセラレータの制限事項に注意してください。
2. デプロイされている VM で [Azure 高速ネットワーク](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)が有効になっているかどうか確認します。

> [!NOTE]
> さまざまな SAP チューニング プロファイルにある、またはノートに記載されているとおりに、すべてのコマンドが Azure で正常に実行されるとは限りません。 VM の電源モードを操作するコマンドでは、通常、エラーが返されます。これは、基になる Azure ホスト ハードウェアの電源モードを操作できないためです。

## <a name="step-by-step-preparations-specific-to-azure-virtual-machines"></a>Azure Virtual Machines に固有の詳細な準備手順
Azure 固有の手順の 1 つは、SAP Host Agent の監視データを提供する Azure VM 拡張機能のインストールです。 この監視拡張機能のインストールに関する詳細については、次のドキュメントをご覧ください。

-  [SAP Note 2191498](https://launchpad.support.sap.com/#/notes/2191498/E) には、Azure 上の Linux VM を使用した SAP の "拡張された監視機能" についての説明が記載されています。 
-  [SAP Note 1102124](https://launchpad.support.sap.com/#/notes/1102124/E) には、Linux での SAPOSCOL に関する情報が記載されています。 
-  [SAP Note 2178632](https://launchpad.support.sap.com/#/notes/2178632/E) には、Microsoft Azure 上の SAP 向けの主要な監視メトリックの説明が記載されています。
-  [SAP NetWeaver のための Azure Virtual Machines のデプロイ](./deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca)

## <a name="sap-hana-installation"></a>SAP HANA のインストール
Azure Virtual Machine をデプロイし、オペレーティング システムを登録して構成したら、SAP のインストールに従って SAP HANA をインストールできます。 このドキュメントを取得するには、まず、SAP の [HANA リソース](https://www.sap.com/products/s4hana-erp.html?btp=9d3e6f82-d8ab-4122-8d2d-bf4971217afd)の Web サイトをご覧ください。

Azure Premium Storage または Ultra ディスクの直接接続ディスクを使用する SAP HANA スケールアウト構成については、「[Azure における SAP HANA インフラストラクチャの構成と運用](./hana-vm-operations.md#configuring-azure-infrastructure-for-sap-hana-scale-out)」ドキュメントで詳細を確認してください。


## <a name="additional-resources-for-sap-hana-backup"></a>SAP HANA バックアップ用の追加リソース
Azure VM での SAP HANA データベースのバックアップ方法の詳細については以下を参照してください。
* [Azure Virtual Machines 上の SAP HANA のバックアップ ガイド](./sap-hana-backup-guide.md)
* [ファイル レベルの SAP HANA Azure バックアップ](./sap-hana-backup-file-level.md)

## <a name="next-steps"></a>次のステップ
次のドキュメントをお読みください。

- [Azure における SAP HANA インフラストラクチャの構成と運用](./hana-vm-operations.md)
- [SAP HANA Azure 仮想マシンのストレージ構成](./hana-vm-operations-storage.md)
