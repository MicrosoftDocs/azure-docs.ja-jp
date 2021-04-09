---
title: SAP HANA on Azure (L インスタンス) のアーキテクチャ | Microsoft Docs
description: SAP HANA on Azure (L インスタンス) をデプロイする方法のアーキテクチャ。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f45b84360c941e879461500a033ada3642fd5cbb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101671612"
---
# <a name="sap-hana-large-instances-architecture-on-azure"></a>Azure の SAP HANA (L インスタンス) のアーキテクチャ

大まかに言うと、SAP HANA on Azure (L インスタンス) ソリューションでは、VM 内に SAP アプリケーション層があります。 データベース層は、Azure IaaS に接続される同じ Azure リージョン内の L インスタンス スタンプにある SAP TDI で構成されたハードウェア上にあります。

> [!NOTE]
> SAP アプリケーション層は、SAP DBMS 層と同じ Azure リージョンにデプロイします。 この規則は、Azure 上の SAP ワークロードに関して公開されている情報で明確に文書化されています。 

SAP HANA on Azure (L インスタンス) の全体的なアーキテクチャでは、SAP TDI 認定のハードウェア構成 (SAP HANA データベース用の仮想化されていない、ベア メタルの高パフォーマンス サーバー) が提供されます。 また、SAP アプリケーション層のリソースをニーズに合わせてスケーリングするための Azure の機能と柔軟性も提供されます。

![SAP HANA on Azure (L インスタンス) のアーキテクチャの概要](./media/hana-overview-architecture/image1-architecture.png)

上記のアーキテクチャは、次の 3 つのセクションに分かれています。

- **右**: エンド ユーザーが SAP などの LOB アプリケーションにアクセスできるように、データ センターでさまざまなアプリケーションを実行するオンプレミス インフラストラクチャを示します。 理想的には、このオンプレミス インフラストラクチャは [ExpressRoute](https://azure.microsoft.com/services/expressroute/) で Azure に接続されます。

- **中央**: Azure IaaS と、この場合は、SAP または DBMS システムとして SAP HANA を使用するその他のアプリケーションをホストするための VM の使用を示します。 VM が提供するメモリを使用して機能する小規模な HANA インスタンスは、アプリケーション層と共に VM にデプロイされます。 仮想マシンの詳細については、「[Virtual Machines](https://azure.microsoft.com/services/virtual-machines/)」をご覧ください。

   Azure ネットワーク サービスは、SAP システムと他のアプリケーションをグループ化して仮想ネットワークを作成する際に使用されます。 これらの仮想ネットワークは、オンプレミスのシステムおよび SAP HANA on Azure (L インスタンス) に接続します。

   Azure での実行がサポートされている SAP NetWeaver アプリケーションおよびデータベースについては、次を参照してください。「[SAP Support Note #1928533 – SAP applications on Azure: Supported products and Azure VM types (SAP サポート ノート #1928533 - Azure 上の SAP アプリケーション: サポート対象の製品と Azure VM の種類)](https://launchpad.support.sap.com/#/notes/1928533)」。 Azure で SAP ソリューションをデプロイする方法については、次のドキュメントをご覧ください。

  -  [Windows 仮想マシンにおける SAP の使用](./get-started.md?toc=/azure/virtual-machines/linux/toc.json)
  -  [Azure 仮想マシンにおける SAP ソリューションの使用](get-started.md)

- **左**: Azure L インスタンス スタンプの SAP HANA TDI 認定ハードウェアを示します。 HANA L インスタンス ユニットは、オンプレミスから Azure への接続と同じテクノロジを使用して、Azure サブスクリプションの仮想ネットワークに接続されます。 2019 年 5 月の時点では、ExpressRoute ゲートウェイの関与なしに HANA L インスタンス ユニットと Azure VM の間の通信を可能にする最適化が導入されています。 ExpressRoute Fast Path と呼ばれるこの最適化は、このアーキテクチャに表示されています (赤い線)。 

Azure L インスタンス スタンプ自体は次のコンポーネントを結合します。

- **コンピューティング**: 必要なコンピューティング能力を提供し、SAP HANA 認定を取得している各世代の Intel Xeon プロセッサに基づいたサーバー。
- **ネットワーク**:コンピューティング、ストレージ、および LAN コンポーネントを相互接続する統合された高速ネットワーク ファブリック。
- **ストレージ**: 統合されたネットワーク ファブリック経由でアクセスされるストレージ インフラストラクチャ。 デプロイする SAP HANA on Azure (L インスタンス) 構成に応じて、特定のストレージ容量が提供されます。 追加の月額料金をお支払いいただくと、ストレージ容量を増やすことができます。

L インスタンス スタンプのマルチテナント インフラストラクチャ内では、分離されたテナントとしてお客様の環境がデプロイされます。 テナントのデプロイ時に、Azure 加入契約内で Azure サブスクリプションに名前を付けます。 これが、HANA L インスタンスの課金対象となる Azure サブスクリプションになります。 これらのテナントには Azure サブスクリプションとの 1 対 1 の関係があります。 ネットワークについては、異なる Azure サブスクリプションに属する異なる仮想ネットワークから、1 つの Azure リージョン内の 1 つのテナントにデプロイされた HANA L インスタンス ユニットにアクセスできます。 それらの Azure サブスクリプションが同じ Azure 加入契約に属している必要があります。 

VM と同様に、SAP HANA on Azure (L インスタンス) は複数の Azure リージョンで提供されます。 ディザスター リカバリー機能を提供するために、オプトインを選択できます。 1 つの地政学的リージョン内の異なる L インスタンス スタンプが相互に接続されます。 たとえば、米国西部と米国東部の HANA L インスタンス スタンプは、ディザスター リカバリー レプリケーションのために、専用のネットワーク リンクを介して接続されます。 

Azure Virtual Machines でさまざまな VM の種類の中から選択できるのと同様に、SAP HANA のさまざまなワークロードの種類に対応した HANA L インスタンスの各種 SKU の中から選択できます。 SAP では、Intel プロセッサの世代に基づいて、さまざまなワークロードにメモリ対プロセッサ ソケット比を適用します。 提供される SKU の種類を次の表に示します。

利用可能な SKU を検索できます[HLI で利用可能な SKU](hana-available-skus.md)。

**次の手順**
- [SAP HANA (L インスタンス) のネットワーク アーキテクチャ](hana-network-architecture.md)を参照する
