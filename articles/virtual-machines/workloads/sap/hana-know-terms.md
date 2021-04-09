---
title: SAP HANA on Azure (L インスタンス) のアーキテクチャの用語の確認 | Microsoft Docs
description: SAP HANA on Azure (L インスタンス) のアーキテクチャの用語を確認します。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 92ef2e59dab1921eae8e7d88249e75116601c597
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101670875"
---
# <a name="know-the-terms"></a>用語の確認

このアーキテクチャ/テクニカル デプロイ ガイドでは、いくつかの一般的な定義が広く使用されています。 次の用語とその意味に注意してください。

- **IaaS**:サービスとしてのインフラストラクチャ。
- **PaaS**:サービスとしてのプラットフォーム。
- **SaaS**:サービスとしてのソフトウェア。
- **SAP コンポーネント**:個々の SAP アプリケーション (ERP Central Component (ECC)、Business Warehouse (BW)、Solution Manager、Enterprise Portal (EP) など)。 SAP コンポーネントは、従来の ABAP または Java テクノロジか、ビジネス オブジェクトなどの非 NetWeaver ベース アプリケーションに基づいて作成できます。
- **SAP 環境**:開発、品質保証、トレーニング、ディザスター リカバリー、運用など、ビジネス機能を実行するために論理的にグループ化された、1 つまたは複数の SAP コンポーネント。
- **SAP ランドスケープ**:IT ランドスケープ内にある SAP 資産全体を指します。 SAP ランドス ケープには、運用環境と非運用環境のすべてが含まれます。
- **SAP システム**:SAP ERP 開発システム、SAP BW テスト システム、SAP CRM 運用システムなどの DBMS 層とアプリケーション層の組み合わせ。 Azure のデプロイでは、この 2 つの層をオンプレミスと Azure に分けることはできません。 SAP システムは、オンプレミスまたは Azure のいずれかにデプロイされます。 SAP ランドスケープの各種システムは、Azure とオンプレミスのどちらにデプロイしても構いません。 たとえば、SAP CRM の開発システムとテスト システムを Azure にデプロイし、SAP CRM 運用システムをオンプレミスにデプロイできます。 SAP HANA on Azure (L インスタンス) の場合、VM で SAP システムの SAP アプリケーション層をホストし、SAP HANA on Azure (L インスタンス) スタンプのユニットで関連する SAP HANA インスタンスをホストすることを目的としています。
- **L インスタンス スタンプ**:SAP HANA TDI 認定のハードウェア インフラストラクチャ スタック。Azure 内で SAP HANA インスタンスを実行するための専用のスタックです。
- **Azure 上の SAP HANA (L インスタンス):** さまざまな Azure リージョンの L インスタンス スタンプにデプロイされた SAP HANA TDI 認定ハードウェア上で HANA インスタンスを実行する、Azure におけるプランの正式名称。 関連用語の *HANA L インスタンス* は、*SAP HANA on Azure (L インスタンス)* の省略形であり、このテクニカル デプロイ ガイドで広く使用されています。
- **クロスプレミス**:オンプレミスのデータ センターと Azure の間で、サイト間接続、マルチサイト接続、または Azure ExpressRoute 接続を利用する Azure サブスクリプションに VM をデプロイするシナリオを指します。 この種のデプロイは、共通の Azure ドキュメントでもクロスプレミス シナリオとして説明されています。 この接続の目的は、オンプレミスのドメイン、オンプレミスの Azure Active Directory/OpenLDAP、およびオンプレミスの DNS を Azure に拡張することです。 オンプレミスのランドスケープが、Azure サブスクリプションの Azure 資産に拡張されます。 この拡張により、VM をオンプレミス ドメインに含めることができます。 

   オンプレミス ドメインのドメイン ユーザーは、サーバーにアクセスし、それらの VM 上でサービス (DBMS サービスなど) を実行できます。 オンプレミスにデプロイした VM と Azure にデプロイした VM 間での通信と名前解決が可能です。 このシナリオが、ほとんどの SAP 資産をデプロイする際の一般的な方法です。 詳しくは、[Azure VPN Gateway ](../../../vpn-gateway/vpn-gateway-about-vpngateways.md)に関する記事、および[Azure Portal を使用したサイト間接続の仮想ネットワークの作成](../../../vpn-gateway/tutorial-site-to-site-portal.md)に関する記事をご覧ください。
- **Tenant**: HANA L インスタンス スタンプにデプロイされたお客様は、"*テナント*" に分離されます。 テナントは、ネットワーク、ストレージ、およびコンピューティング レイヤーで他のテナントから分離されます。 別のテナントに割り当てられているストレージ ユニットやコンピューティング ユニットは、HANA L インスタンス スタンプ レベルで相互に認識したり通信したりすることはできません。 同じお客様が別々のテナントにデプロイを置くこともありますが、 その場合でも HANA L インスタンス スタンプ レベルでテナント間が通信することはありません。
- **SKU カテゴリ**:HANA L インスタンスの場合、次の 2 つの SKU のカテゴリが提供されます。
    - **Type I クラス**:S72、S72m、S96、S144、S144m、S192、S192m、S192xm、S224、および S224m
    - **Type II クラス**:S384、S384m、S384xm、S384xxm、S576m、S576xm、S768m、S768xm、S960m
- **スタンプ**:HANA L インスタンスの Microsoft 内部デプロイ サイズを定義します。 HANA L インスタンス ユニットをデプロイするは、コンピューティング、ネットワーク、およびストレージ ラックで構成されている HANA L インスタンス スタンプをデータセンターの場所に事前にデプロイする必要があります。 このようなデプロイは HANA L インスタンス スタンプと呼ばれます。またはリビジョン 4 以降では (下記参照) **ラージ インスタンス行** の用語の代わりに使用しています。
- **リビジョン**:HANA L インスタンス スタンプには、2 つの異なるスタンプ リビジョンがあります。 これらは、アーキテクチャと Azure 仮想マシン ホストとの近接性の点で異なります。
    - "リビジョン 3" (Rev 3): 2016 年の中頃からデプロイされた元の設計
    - "リビジョン 4" (Rev 4): Azure VM と HANA L インスタンス ユニット間のネットワーク待機時間を短縮しながら、Azure 仮想マシンのホストにより近い場所を提供できる新しい設計です 
    - "リビジョン 4.2" (Rev 4.2): 既存のリビジョン 4 の DC では、リソースは BareMetal Infrastructure に再ブランディングされます。  お客様は、Azure portal から BareMetal インスタンスとしてリソースにアクセスできます。 

クラウドに SAP ワークロードをデプロイする方法については、他のさまざまなリソースが用意されています。 Azure への SAP HANA のデプロイを計画する場合、Azure IaaS の原則と Azure IaaS での SAP ワークロードのデプロイに関する知識と経験が必要です。 作業を続行する前に、[Azure 仮想マシンでの SAP ソリューションの使用](get-started.md)に関する記事で詳細を確認してください。 

**次の手順**
- [HLI 認定](hana-certification.md)を参照してください