---
title: SAP HANA on Azure (L インスタンス) の概要 | Microsoft Docs
description: SAP HANA on Azure (L インスタンス) をデプロイする方法の概要。
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ea337101a5fe44e42ce85c17fec32028c75d3b85
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101182"
---
#  <a name="what-is-sap-hana-on-azure-large-instances"></a>SAP HANA on Azure (L インスタンス) とは

SAP HANA on Azure (L インスタンス) は、Azure 独自のソリューションです。 Azure では、SAP HANA をデプロイおよび実行するための仮想マシンを提供するだけでなく、お客様専用のベア メタル サーバー上でも SAP HANA を実行およびデプロイすることができます。 SAP HANA on Azure (L インスタンス) ソリューションは、お客様に割り当てられた非共有ホスト/サーバーのベア メタル ハードウェア上に構築されます。 サーバー ハードウェアは、コンピューティング/サーバー、ネットワーク、ストレージのインフラストラクチャを含むより大きなスタンプに埋め込まれます。 この全体が HANA テーラード データ センター統合 (TDI) 認定の対象となります。 SAP HANA on Azure (L インスタンス) には、さまざまなサーバー SKU やサイズが用意されています。 ユニットには、36 個の Intel CPU コアと 768 GB のメモリを搭載できます。また、最大 480 個の Intel CPU コアと最大 24 TB のメモリを搭載するユニットに拡張できます。

インフラストラクチャ スタンプ内のお客様の分離はテナントで行われ、次のようになります。

- **ネットワーク**:お客様に割り当てられているテナントごとに、仮想ネットワークを通じてインフラストラクチャ スタック内のお客様を分離します。 テナントは 1 件のお客様に割り当てられます。 お客様は複数のテナントを持つことができます。 テナントのネットワーク分離では、各テナントが同じお客様に属している場合でも、インフラストラクチャ スタンプ レベルでのテナント間のネットワーク通信が禁止されます。
- **ストレージ コンポーネント**: ストレージ ボリュームが割り当てられているストレージ仮想マシンを通じて分離します。 ストレージ ボリュームは 1 つのストレージ仮想マシンにのみ割り当てることができます。 ストレージ仮想マシンは、SAP HANA TDI 認定のインフラストラクチャ スタック内の 1 つのテナントに排他的に割り当てられます。 そのため、ストレージ仮想マシンに割り当てられているストレージ ボリュームには、関連付けられている特定のテナント内でのみアクセスできます。 デプロイされている別のテナントには表示されません。
- **サーバーまたはホスト**: サーバー ユニットまたはホスト ユニットがお客様間やテナント間で共有されることはありません。 お客様にデプロイされたサーバーまたはホストは、1 つのテナントに割り当てられたアトミック ベア メタル コンピューティング ユニットです。 別のお客様とホストまたはサーバーを共有することになるハードウェア パーティション分割やソフト パーティション分割が使用されることは*ありません*。 特定のテナントのストレージ仮想マシンに割り当てられたストレージ ボリュームは、このようなサーバーにマウントされます。 テナントは、排他的に割り当てられている別の SKU のサーバー ユニットを 1 つから複数持つことができます。
- SAP HANA on Azure (L インスタンス) インフラストラクチャ スタンプ内では多数のさまざまなテナントがデプロイされており、テナントの概念により、ネットワーク、ストレージ、およびコンピューティング レベルで相互に分離されています。 


これらのベア メタル サーバー ユニットでは、SAP HANA の実行のみがサポートされます。 SAP アプリケーション層またはワークロードのミドルウェア層は仮想マシン内で実行されます。 SAP HANA on Azure (L インスタンス) のユニットを実行しているインフラストラクチャ スタンプは、Azure ネットワーク サービスのバックボーンに接続されています。 これにより、SAP HANA on Azure (L インスタンス) のユニットと仮想マシン間の低遅延の接続が実現されます。

2019 年 7 月の時点では、HANA L インスタンス スタンプの 2 つの異なるリビジョンとデプロイの場所を区別しています。

- "リビジョン 3" (Rev 3):2019 年 7 月より前にお客様がデプロイに使用できたスタンプ
- "リビジョン 4" (Rev 4):Azure VM ホストに近接する場所にデプロイされる新しいスタンプ設計。これまでに次の Azure リージョンでリリースされています。
    -  米国西部 2 
    -  East US 
    -  西ヨーロッパ
    -  北ヨーロッパ


このドキュメントは、SAP HANA on Azure (L インスタンス) について説明する複数のドキュメントの 1 つです。 このドキュメントでは、基本的なアーキテクチャ、責任、ソリューションで提供されるサービスについて説明します。 また、ソリューションの機能の概要についても説明します。 ネットワークや接続など、他のほとんどの領域については、他の 4 つのドキュメントで詳しく説明しています。 SAP HANA on Azure (L インスタンス) のドキュメントでは、SAP NetWeaver のインストールや VM へのデプロイについては取り上げていません。 Azure における SAP NetWeaver については、同じ Azure ドキュメント コンテナーにある別のドキュメントで取り上げています。 


HANA L インスタンス ガイダンスの別のドキュメントでは、次の分野について説明します。

- [SAP HANA on Azure (L インスタンス) の概要とアーキテクチャ](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA on Azure (L インスタンス) のインフラストラクチャと接続](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA on Azure (L インスタンス) のインストールと構成](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure での SAP HANA (L インスタンス) の高可用性とディザスター リカバリー](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [SAP HANA on Azure (L インスタンス) のトラブルシューティングと監視](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [STONITH を使用した SUSE での高可用性のセットアップ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/ha-setup-with-stonith)
- [リビジョン 3 スタンプの Type II SKU の OS バックアップと復元](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-backup-type-ii-skus)

**次のステップ**
- [用語の確認](hana-know-terms.md)を参照してください