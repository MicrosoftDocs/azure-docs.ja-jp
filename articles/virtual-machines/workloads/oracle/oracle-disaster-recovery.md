---
title: Azure 環境における Oracle ディザスター リカバリー シナリオの概要 | Microsoft Docs
description: Azure 環境内の Oracle Database 12c データベースのディザスター リカバリー シナリオ
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: db0b9887b80f13938045a5d11fb09ed0a43efc19
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706976"
---
# <a name="disaster-recovery-for-an-oracle-database-12c-database-in-an-azure-environment"></a>Azure 環境内の Oracle Database 12c データベースのディザスター リカバリー

## <a name="assumptions"></a>前提条件

- Oracle Data Guard の設計と Azure 環境について理解していること


## <a name="goals"></a>目標
- ディザスター リカバリー (DR) の要件を満たすトポロジと構成を設計すること

## <a name="scenario-1-primary-and-dr-sites-on-azure"></a>シナリオ 1:Azure 上のプライマリ サイトと DR サイト

Oracle データベースがプライマリ サイトにセットアップされています。 DR サイトは別のリージョンにあります。 Oracle Data Guard はこれらのサイト間での迅速な回復に使用されます。 また、プライマリ サイトにはレポートの作成や他の用途のためにセカンダリ データベースが用意されています。 

### <a name="topology"></a>トポロジ

Azure セットアップの概要は次のとおりです。

- 2 つのサイト (プライマリ サイトと DR サイト)
- 2 つの仮想ネットワーク
- Data Guard を備えた 2 つの Oracle データベース (プライマリとスタンバイ)
- Golden Gate または Data Guard を備えた 2 つの Oracle データベース (プライマリ サイトのみ)
- 2 つのアプリケーション サービス (プライマリ サイトに 1 つ、DR サイトに 1 つ)
- プライマリ サイト上のデータベースおよびアプリケーション サービスに使用されている "*可用性セット*"
- 各サイトに 1 つのジャンプボックス (プライベート ネットワークへのアクセスを制限し、管理者によるサインインのみを許可)
- 別個のサブネット上にあるジャンプボックス、アプリケーション サービス、データベース、および VPN ゲートウェイ
- アプリケーションおよびデータベースのサブネットに適用されている NSG

![DR トポロジ ページのスクリーンショット](./media/oracle-disaster-recovery/oracle_topology_01.png)

## <a name="scenario-2-primary-site-on-premises-and-dr-site-on-azure"></a>シナリオ 2:オンプレミスのプライマリ サイトと Azure 上の DR サイト

Oracle データベースがオンプレミスにセットアップされています (プライマリ サイト)。 DR サイトは Azure にあります。 Oracle Data Guard はこれらのサイト間の迅速な回復に使用されます。 また、プライマリ サイトにはレポートの作成や他の用途のためにセカンダリ データベースが用意されています。 

このセットアップには 2 つのアプローチがあります。

### <a name="approach-1-direct-connections-between-on-premises-and-azure-requiring-open-tcp-ports-on-the-firewall"></a>アプローチ 1: オンプレミスと Azure を直接接続する (ファイアウォールの TCP ポートを開く必要がある) 

直接接続は、外部に TCP ポートが公開されるため、お勧めしません。

#### <a name="topology"></a>トポロジ

Azure セットアップの概要は次のとおりです。

- 1 つの DR サイト 
- 1 つの仮想ネットワーク
- Data Guard を備えた 1 つの Oracle データベース (アクティブ)
- DR サイトに 1 つのアプリケーション サービス
- 1 つのジャンプボックス (プライベート ネットワークへのアクセスを制限し、管理者によるサインインのみを許可)
- 別個のサブネット上にあるジャンプボックス、アプリケーション サービス、データベース、および VPN ゲートウェイ
- アプリケーションおよびデータベースのサブネットに適用されている NSG
- 受信 TCP ポート 1521 (またはユーザー定義ポート) を許可するNSG ポリシー/ルール
- オンプレミス (DB またはアプリケーション) の IP アドレスから仮想ネットワークへのアクセスのみを制限するための NSG ポリシー/ルール

![DR トポロジ ページのスクリーンショット](./media/oracle-disaster-recovery/oracle_topology_02.png)

### <a name="approach-2-site-to-site-vpn"></a>アプローチ 2: サイト間 VPN
サイト対サイト VPN は、より優れたアプローチです。 VPN の設定に関する詳細については、「[CLI を使用したサイト間 VPN 接続を持つ仮想ネットワークの作成](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli)」を参照してください。

#### <a name="topology"></a>トポロジ

Azure セットアップの概要は次のとおりです。

- 1 つの DR サイト 
- 1 つの仮想ネットワーク 
- Data Guard を備えた 1 つの Oracle データベース (アクティブ)
- DR サイトに 1 つのアプリケーション サービス
- 1 つのジャンプボックス (プライベート ネットワークへのアクセスを制限し、管理者によるサインインのみを許可)
- ジャンプボックス、アプリケーション サービス、データベース、および VPN ゲートウェイは別個のサブネット上にある
- アプリケーションおよびデータベースのサブネットに適用されている NSG
- オンプレミスと Azure 間のサイト間 VPN 接続

![DR トポロジ ページのスクリーンショット](./media/oracle-disaster-recovery/oracle_topology_03.png)

## <a name="additional-reading"></a>その他の情報

- [Azure での Oracle データベースの設計と実装](oracle-design.md)
- [Oracle Data Guard の構成](configure-oracle-dataguard.md)
- [Oracle Golden Gate の構成](configure-oracle-golden-gate.md)
- [Oracle のバックアップと回復](oracle-backup-recovery.md)


## <a name="next-steps"></a>次の手順

- [チュートリアル:高可用性 VM の作成](../../linux/create-cli-complete.md)
- [VM デプロイ Azure CLI サンプルを探索する](../../linux/cli-samples.md)
