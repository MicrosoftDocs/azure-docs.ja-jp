---
title: Azure Virtual WAN:ハブでネットワーク仮想アプライアンス (NVA) を作成する
description: Virtual WAN ハブでネットワーク仮想アプライアンスをデプロイする方法について説明します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a Network Virtual Appliance (NVA) in my Virtual WAN hub.
ms.openlocfilehash: f02edf8e192f4d32f8bd2583d46bbb17c86d2049
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102454806"
---
# <a name="how-to-create-a-network-virtual-appliance-in-an-azure-virtual-wan-hub-preview"></a>Azure Virtual WAN ハブでネットワーク仮想アプライアンスを作成する方法 (プレビュー)

この記事では、Azure の **Network Virtual Appliance** (NVA) を使用して、Azure のリソースに接続する方法を示します。 この種類の接続では、外部接続用パブリック IP アドレスが割り当てられていてるオンプレミスの VPN デバイスが必要です。 Virtual WAN の詳細については、「[Virtual WAN とは](virtual-wan-about.md)」を参照してください。

この記事のステップは、Virtual WAN ハブで **Barracuda CloudGen WAN** ネットワーク仮想アプライアンスを作成する際に役立ちます。 この演習を完了するには、開始する前に、Barracuda Cloud Premise Device (CPE) と、ハブにデプロイする Barracuda CloudGen WAN アプライアンスのライセンスが必要です。

Azure Virtual WAN 内の **Cisco SD-WAN** のデプロイに関するドキュメントについては、「[Cisco Cloud OnRamp for Multi-Cloud](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701)」を参照してください。 

Azure Virtual WAN 内の **VMware SD-WAN** のデプロイ ドキュメントの場合 - 「[Deployment Guide for VMware SD-WAN in Virtual WAN Hub](https://kb.vmware.com/s/article/82746)」 (仮想 WAN ハブの VMware SD-WAN 向けデプロイ ガイド) を参照してください。

## <a name="prerequisites"></a>前提条件

構成を開始する前に、以下の条件を満たしていることを確認します。

* Barracuda CloudGen WAN ゲートウェイのライセンスを取得します。 これを行う方法の詳細については、[Barracuda CloudGen WAN のドキュメント](https://www.barracuda.com/products/cloudgenwan)を参照してください。

* 接続先の仮想ネットワークが用意されていること。 オンプレミス ネットワークのどのサブネットも接続先の仮想ネットワークと重複していないことを確認してください。 Azure portal で仮想ネットワークを作成するには、[クイックスタート](../virtual-network/quick-create-portal.md)を参照してください。

* 仮想ネットワークに仮想ネットワーク ゲートウェイが存在しないこと。 仮想ネットワークにゲートウェイ (VPN または ExpressRoute のどちらか) が存在する場合は、すべてのゲートウェイを削除する必要があります。 代わりに、この構成では、仮想ネットワークが Virtual WAN ハブ ゲートウェイに接続されている必要があります。

* ハブ リージョンの IP アドレス範囲を取得します。 このハブは、Virtual WAN によって作成および使用される仮想ネットワークです。 ハブに指定するアドレス範囲が、接続先の既存の仮想ネットワークと重複することはできません。 さらに、オンプレミスで接続するアドレス範囲と重複することもできません。 オンプレミス ネットワーク構成に含まれている IP アドレス範囲になじみがない場合は、それらの詳細を提供できるだれかと調整してください。

* Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>仮想 WAN を作成する

[!INCLUDE [Create virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-hub"></a><a name="hub"></a>ハブを作成する

ハブは、サイト間、ExpressRoute、ポイント対サイト、またはネットワーク仮想アプライアンス機能のためのゲートウェイを含めることができる仮想ネットワークです。 ハブが作成されると、サイトをアタッチしていない場合でも、ハブに対して課金されます。 サイト間 VPN ゲートウェイを作成することを選択した場合は、仮想ハブ内にサイト間 VPN ゲートウェイを作成するまでに 30 分かかります。 サイト間、ExpressRoute、またはポイント対サイトとは異なり、ハブ VNet にネットワーク仮想アプライアンスをデプロイする前に、まずハブを作成する必要があります。

1. 作成した仮想 WAN を探します。 **[Virtual WAN]** ページの **[接続]** セクションで、 **[ハブ]** を選択します。
1. **[ハブ]** ページで、[+ 新しいハブ] を選択して **[仮想ハブを作成する]** ページを開きます。

   :::image type="content" source="./media/how-to-nva-hub/vwan-hub.png" alt-text="基本操作":::
1. **[仮想ハブを作成する]** ページの **[基本]** タブで、次のフィールドを入力します。

   **プロジェクトの詳細**

   * リージョン (旧称は場所)
   * 名前
   * ハブのプライベート アドレス空間。 ハブを作成するための最小アドレス空間は /24 です。これは、/25 から /32 のいずれの範囲でも、作成中にエラーが生成されることを意味します。 Microsoft のマネージド サービスである Azure Virtual WAN では、異なるゲートウェイまたはサービスに対して、適切なサブネットが仮想ハブに作成されます。 (例: ネットワーク仮想アプライアンス、VPN ゲートウェイ、ExpressRoute ゲートウェイ、ユーザー VPN/ポイント対サイト ゲートウェイ、ファイアウォール、ルーティングなど) 仮想ハブ内のサービスのサブネット アドレス空間は、サービスの一部として Microsoft によって明示的に計画されるため、ユーザーが行う必要はありません。
1. **[確認と作成]** を選択して検証します。
1. **[作成]** を選択してハブを作成します。

## <a name="create-the-network-virtual-appliance-in-the-hub"></a>ハブでネットワーク仮想アプライアンスを作成する

このステップでは、ハブでネットワーク仮想アプライアンスを作成します。 各 NVA の手順は、NVA パートナーの製品ごとに異なります。 この例では、Barracuda CloudGen WAN ゲートウェイを作成しています。

1. 前のステップで作成した Virtual WAN ハブを見つけて開きます。

   :::image type="content" source="./media/how-to-nva-hub/nva-hub.png" alt-text="仮想ハブ":::
1. [ネットワーク仮想アプライアンス] タイルを見つけて、 **[作成]** リンクを選択します。
1. **[ネットワーク仮想アプライアンス]** ブレードで **[Barracuda CloudGen WAN]** を選択し、 **[作成]** ボタンを選択します。

   :::image type="content" source="./media/how-to-nva-hub/select-nva.png" alt-text="NVA を選択する":::
1. これにより、Barracuda CloudGen WAN ゲートウェイ向けの Azure Marketplace オファーが表示されます。 ご契約条件を読み、準備ができたら **[作成]** ボタンを選択します。

   :::image type="content" source="./media/how-to-nva-hub/barracuda-create-basics.png" alt-text="Barracuda NVA 基本":::
1. **[基本]** ページでは、次の情報を指定する必要があります。

   * **[サブスクリプション]** - Virtual WAN とハブをデプロイするために使用したサブスクリプションを選択します。
   * **[リソース グループ]** - Virtual WAN とハブをデプロイするために使用したリソース グループを選択します。
   * **[リージョン]** - 仮想ハブ リソースが配置されているリージョンと同じリージョンを選択します。
   * **[アプリケーション名]** - Barracuda NextGen WAN はマネージド アプリケーションです。 このリソースはサブスクリプションに表示されるときに呼び出されるため、簡単に識別できる名前を選択してください。
   * **[管理対象リソース グループ]** - Barracuda で管理されているリソースをデプロイする管理対象リソース グループの名前です。 この名前は事前に設定されている必要があります。
1. ページの下部にある **[次へ: CloudGen WAN ゲートウェイ]** ボタンを選択します。

   :::image type="content" source="./media/how-to-nva-hub/barracuda-cloudgen-wan.png" alt-text="CloudGen WAN ゲートウェイ":::
1. ここでは、次の情報を指定します。

   * **[Virtual WAN ハブ]** - この NVA をデプロイする Virtual WAN ハブです。
   * **[NVA インフラストラクチャ ユニット]** - この NVA をデプロイする NVA インフラストラクチャ ユニットの数を示します。 この NVA を使用して、このハブに接続するすべてのブランチ サイトにわたって指定する集約帯域幅容量を選択します。
   * **[トークン]** - Barracuda では、この製品の登録済みユーザーとしてユーザーを識別するために、ここで認証トークンを指定する必要があります。 これは、Barracuda から入手する必要があります。
1. **[確認と作成]** ボタンを選択して続行します。
1. このページでは、共同管理者アクセス契約の条項に同意するように求められます。 これは、このデプロイでパブリッシャーが一部のリソースへのアクセス権を持っているマネージド アプリケーションで標準です。 **[上記のご契約条件に同意します]** ボックスを確認し、 **[作成]** を選択します。

## <a name="connect-the-vnet-to-the-hub"></a><a name="vnet"></a>VNet をハブに接続する

[!INCLUDE [Connect](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="next-steps"></a>次のステップ

* Virtual WAN の詳細については、「[Virtual WAN とは](virtual-wan-about.md)」ページを参照してください。
* Virtual WAN ハブでの NVA の詳細については、「[Virtual WAN ハブのネットワーク仮想アプライアンスについて (プレビュー)](about-nva-hub.md)」を参照してください。
