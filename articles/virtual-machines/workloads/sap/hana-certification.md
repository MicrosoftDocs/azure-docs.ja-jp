---
title: SAP HANA on Azure (L インスタンス) の認定 | Microsoft Docs
description: SAP HANA on Azure (L インスタンス) の認定。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a8d4781ad9ca2a30e8523d0e2446dc3903a7cb8a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101668854"
---
# <a name="certification"></a>認定資格

SAP では、Azure IaaS などの特定のインフラストラクチャ上の SAP HANA をサポートするための要件として、NetWeaver 認定資格のほか、SAP HANA の特別な認定資格を要求しています。

NetWeaver (および一部の SAP HANA 認定資格) に関する主要な SAP ノートは、「[SAP Note #1928533 - SAP applications on Azure: Supported products and Azure VM types (SAP ノート #1928533 - Azure 上の SAP アプリケーション: サポート対象の製品と Azure VM の種類)](https://launchpad.support.sap.com/#/notes/1928533)」です。

SAP HANA on Azure (L インスタンス) ユニットの SAP HANA の認定記録については、[SAP HANA 認定 IaaS プラットフォーム](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)のサイトを参照してください。 

SAP HANA 認定 IaaS プラットフォームのサイトで取り上げられている SAP HANA on Azure (L インスタンス) タイプを使用すると、Microsoft と SAP のお客様は大規模な SAP Business Suite、SAP BW、S/4 HANA、BW/4HANA、または他の SAP HANA ワークロードを Azure にデプロイできます。 このソリューションは、SAP-HANA 認定の専用のハードウェア スタンプ ([SAP HANA テーラード データ センター統合 - TDI](https://scn.sap.com/docs/DOC-63140)) に基づいています。 SAP HANA TDI で構成されたソリューションを実行すると、SAP HANA ベースのすべてのアプリケーション (SAP Business Suite on SAP HANA、SAP BW on SAP HANA、S4/HANA、BW4/HANA など) がハードウェア インフラストラクチャ上で動作します。

VM で SAP HANA を実行する場合に比べ、このソリューションには利点があります。 このソリューションでは、メモリ ボリュームが大幅に増加します。 このソリューションを有効にする場合は、次の重要な側面を理解しておく必要があります。

- SAP アプリケーション層と SAP 以外のアプリケーションは、通常の Azure ハードウェア スタンプでホストされる VM で実行されます。
- お客様のオンプレミスのインフラストラクチャ、データ センター、アプリケーションのデプロイは、ExpressRoute (推奨) または仮想プライベート ネットワーク (VPN) 経由でクラウド プラットフォームに接続されます。 Active Directory と DNS も Azure に拡張されます。
- HANA ワークロード用の SAP HANA データベース インスタンスは SAP HANA on Azure (L インスタンス) で実行されます。 L インスタンス スタンプは Azure ネットワークに接続されるので、VM で実行されるソフトウェアは HANA L インスタンスで実行される HANA インスタンスとやり取りできます。
- SAP HANA on Azure (L インスタンス) のハードウェアは、SUSE Linux Enterprise Server または Red Hat Enterprise Linux がプレインストールされた IaaS で提供される専用のハードウェアです。 仮想マシンと同様に、オペレーティング システムの更新とメンテナンスはお客様が行います。
- HANA のインストール、または HANA L インスタンスのユニットで SAP HANA を実行するために必要な追加コンポーネントのインストールはお客様が行います。 SAP HANA on Azure の個々の継続的な運用と管理もお客様が行います。
- ここで説明するソリューション以外にも、SAP HANA on Azure (L インスタンス) に接続する Azure サブスクリプションに他のコンポーネントをインストールできます。 例として、SAP HANA データベースとの (直接) 通信を可能にするコンポーネント (ジャンプ サーバー、RDP サーバー、SAP HANA Studio、SAP BI シナリオ向け SAP Data Services、ネットワーク監視ソリューションなど) が挙げられます。
- Azure 内と同様に、HANA L インスタンスは高可用性とディザスター リカバリー機能をサポートします。

**次の手順**
- [HLI で利用可能な SKU](hana-available-skus.md)を参照してください 