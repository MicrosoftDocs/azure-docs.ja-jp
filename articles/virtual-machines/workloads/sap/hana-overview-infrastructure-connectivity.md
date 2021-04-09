---
title: SAP HANA on Azure (L インスタンス) のインフラストラクチャと接続 | Microsoft Docs
description: SAP HANA on Azure (L インスタンス) を使用するために必要な接続インフラストラクチャを構成します。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 27dc2f6c647308984a02f2cf52dbc0213abad088
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101676840"
---
# <a name="sap-hana-large-instances-deployment"></a>SAP HANA (L インスタンス) のデプロイ 

この記事は、読者が SAP HANA on Azure (L インスタンス) を Microsoft から購入済みであることを前提としています。 この記事を読む前に、全般的な背景情報として、[HANA L インスタンスの一般的な用語](hana-know-terms.md)と [HANA L インスタンスの SKU](hana-available-skus.md) に関するページを参照してください。


HANA L インスタンス ユニットをデプロイするためには、次の情報が必要となります。

- お客様名。
- 会社の連絡先情報 (メール アドレスや電話番号など)。
- 技術担当者の連絡先情報 (メール アドレスや電話番号など)。
- 技術担当者のネットワーキング用の連絡先情報 (メール アドレスや電話番号など)。
- Azure デプロイ リージョン (例: 米国西部、オーストラリア東部、北ヨーロッパ)。
- SAP HANA on Azure (L インスタンス) の SKU (構成)。
- Azure デプロイ リージョンごとに次の情報が必要となります。
    - Azure 仮想ネットワークを HANA L インスタンスに接続する ER-P2P 接続の /29 IP アドレス範囲。
    - HANA L インスタンスのサーバー IP プールに使用する /24 CIDR ブロック。
    - [ExpressRoute Global Reach](../../../expressroute/expressroute-global-reach.md) を使用してオンプレミスから HANA L インスタンス ユニットへの直接ルーティング、または異なる Azure リージョン内の HANA L インスタンス ユニット間のルーティングを有効にする場合は省略可能です。別の /29 の IP アドレス範囲を予約する必要があります。 この特定の範囲は、以前に定義した他の IP アドレス範囲と重複しないようにする必要があります。
- HANA L インスタンスに接続するすべての Azure 仮想ネットワークの "仮想ネットワーク アドレス空間" 属性に使用される IP アドレス範囲の値。
- 各 HANA L インスタンス システムのデータ:
  - 適切なホスト名 (できれば完全修飾ドメイン名を含める)。
  - HANA L インスタンス ユニットの適切な IP アドレス (サーバー IP プールのアドレス範囲から)。 サーバー IP プール アドレス範囲に含まれる最初の 30 個の IP アドレスは、HANA L インスタンスの内部的な用途で予約されています。
  - SAP HANA インスタンス用の SAP HANA SID 名 (SAP HANA に関連する必要なディスク ボリュームの作成に必要)。 HANA SID は、NFS ボリューム上に sidadm のアクセス許可を作成するために必要となります。 これらのボリュームが、HANA L インスタンス ユニットにアタッチされます。 また、HANA SID は、マウントされるディスク ボリュームの名前の構成要素の 1 つとしても使用されます。 ユニット上で複数の HANA インスタンスを実行する場合は、複数の HANA SID の一覧を指定する必要があります。 それぞれに個別のボリュームのセットが割り当てられます。
  - Linux OS では、sidadm ユーザーにグループ ID が割り当てられます。 この ID は、SAP HANA 関連の必要なディスク ボリュームを作成するうえでの要件となっています。 SAP HANA のインストールでは通常、グループ ID 1001 の sapsys グループが作成されます。 sidadm ユーザーは、そのグループに属しています。
  - Linux OS では、sidadm ユーザーにユーザー ID が割り当てられます。 この ID は、SAP HANA 関連の必要なディスク ボリュームを作成するうえでの要件となっています。 ユニット上で複数の HANA インスタンスを実行している場合は、すべての sidadm ユーザーをリストしてください。 
- SAP HANA on Azure (L インスタンス) が直接接続する Azure サブスクリプションの Azure サブスクリプション ID。 このサブスクリプション ID は、Azure サブスクリプションを参照します。そのサブスクリプションに HANA L インスタンス ユニットが課金されます。

以上の情報を入力すると、Microsoft によって SAP HANA on Azure (L インスタンス) がプロビジョニングされます。 Microsoft からは、Azure 仮想ネットワークを HANA L インスタンスにリンクさせるための情報が送信されます。 HANA L インスタンス ユニットにアクセスすることもできます。

HANA L インスタンスが Microsoft によってデプロイされたら、次の手順で接続します。

1. [HANA L インスタンスへの Azure VM の接続](hana-connect-azure-vm-large-instances.md)
2. [HANA L インスタンスの ExpressRoute への VNet の接続](hana-connect-vnet-express-route.md)
3. [追加のネットワーク要件 (オプション)](hana-additional-network-requirements.md)
