---
title: "SAP HANA on Azure (L インスタンス) のインフラストラクチャと接続 | Microsoft Docs"
description: "SAP HANA on Azure (L インスタンス) を使用するために必要な接続インフラストラクチャを構成します。"
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 177627d8f72dbd04fb918ac7ece18321246a9c62
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="sap-hana-large-instances-infrastructure-and-connectivity-on-azure"></a>Azure での SAP HANA on Azure (L インスタンス) のインフラストラクチャと接続 

このガイドを読む前に、いくつかの定義を確認しておいてください。 「[SAP HANA on Azure (L インスタンス) の概要とアーキテクチャ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)」では、HANA L インスタンス ユニットの次の 2 種類のクラスを紹介しました。

- S72、S72m、S144、S144m、S192、S192m - "Type I クラス" の SKU と呼びます。
- S384、S384m、S384xm、S576、S768、S960 - "Type II クラス" の SKU と呼びます。

クラス指定子は、HANA L インスタンス SKU に基づいてさまざまな機能や要件について言及する際に、HANA L インスタンスのドキュメント全体にわたって使用されます。

頻繁に使用される他の定義は次のとおりです。
- **L インスタンス スタンプ:** SAP HANA TDI 認定のハードウェア インフラストラクチャ スタック。Azure 内で SAP HANA インスタンスを実行するための専用のスタックです。
- **SAP HANA on Azure (L インスタンス):** 異なる Azure リージョンの L インスタンス スタンプにデプロイされる SAP HANA TDI 認定ハードウェアで HANA インスタンスを実行する、Azure におけるサービスの正式名称。 関連用語の **HANA L インスタンス**は、SAP HANA on Azure (L インスタンス) の省略形であり、このテクニカル デプロイ ガイドで広く使用されています。
 

お客様と Microsoft のエンタープライズ アカウント チームとの間で SAP HANA on Azure (L インスタンス) の購入手続きが完了した後、Microsoft が HANA L インスタンス ユニットをデプロイするためには、次の情報が必要となります。

- お客様名
- 会社の連絡先情報 (電子メール アドレスや電話番号など)
- 技術担当者の連絡先情報 (電子メール アドレスや電話番号など)
- 技術担当者のネットワーキング用の連絡先情報 (電子メール アドレスや電話番号など)
- Azure デプロイ リージョン (7 月時点では、米国西部、米国東部、オーストラリア東部、オーストラリア南東部、西ヨーロッパ、北ヨーロッパ) 
- 2017)
- SAP HANA on Azure (L インスタンス) の SKU (構成) の確認
- デプロイ先のすべての Azure リージョンについて次の情報が必要となります (HANA L インスタンスの概要とアーキテクチャについてのドキュメントを参照)。
    - Azure VNet を HANA L インスタンスに接続する ER-P2P 接続の /29 IP アドレス範囲
    - HANA L インスタンスのサーバー IP プールに使用する /24 CIDR ブロック
- HANA L インスタンスに接続するすべての Azure VNet の "VNet のアドレス空間" 属性に使用される IP アドレス範囲の値
- 各 HANA L インスタンス システムのデータ:
  - 適切なホスト名 (できれば完全修飾ドメイン名を含める)。
  - HANA L インスタンス ユニットの適切な IP アドレス (サーバー IP プールのアドレス範囲から)。サーバー IP プール アドレス範囲に含まれる最初の 30 個の IP アドレスは、HANA L インスタンスの内部的な用途で予約されています。
  - SAP HANA インスタンス用の SAP HANA SID 名 (SAP HANA に関連する必要なディスク ボリュームの作成に必要)。 HANA SID は、NFS ボリューム上の <sidadm> のアクセス許可を作成するために必要です。これらのアクセス許可は、HANA L インスタンス ユニットに関連付けられているところです。 また、マウントされるディスク ボリュームの名前の構成要素の 1 つとしても使用されます。 ユニット上で複数の HANA インスタンスを実行する場合は、複数の HANA SID の一覧を指定する必要があります。 それぞれに個別のボリュームのセットが割り当てられます。
  - Linux OS における hana-sidadm ユーザーのグループ ID (SAP HANA に関連する必要なディスク ボリュームの作成に必要)。 SAP HANA のインストールでは通常、グループ ID 1001 の sapsys グループが作成されます。 hana-sidadm ユーザーは、そのグループに属しています。
  - Linux OS における hana-sidadm ユーザーのユーザー ID (SAP HANA に関連する必要なディスク ボリュームの作成に必要)。 ユニット上で複数の HANA インスタンスを実行している場合は、すべての <sid>adm ユーザーの一覧を指定する必要があります。 
- SAP HANA on Azure (L インスタンス) が直接接続する Azure サブスクリプションの Azure サブスクリプション ID。 このサブスクリプション ID は、Azure サブスクリプションを参照します。そのサブスクリプションに HANA L インスタンス ユニットが課金されます。

これらの情報を入力すると、Microsoft によって SAP HANA on Azure (L インスタンス) がプロビジョニングされ、Azure VNet と HANA L インスタンスとのリンクや HANA L インスタンス ユニットへのアクセスに必要な情報が返されます。

## <a name="connecting-azure-vms-to-hana-large-instances"></a>HANA L インスタンスへの Azure VM の接続

既に「[SAP HANA on Azure (L インスタンス) の概要とアーキテクチャ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)」でも触れたように、Azure における SAP アプリケーション レイヤーと HANA L インスタンスの最小デプロイ環境は次のようになります。

![SAP HANA on Azure (L インスタンス) およびオンプレミスに接続された Azure VNet](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

Azure VNet 側をよく見ると、次のものが必要であることがわかります。

- SAP アプリケーション レイヤーの VM のデプロイ先となる Azure VNet の定義。
- これは、Azure VNet に定義されている既定のサブネットが、実際に VM のデプロイ先として使用されることを意味します。
- 作成される Azure VNet には、少なくとも 1 つの VM サブネットと 1 つの ExpressRoute ゲートウェイ サブネットが必要です。 これらのサブネットには、以降のセクションで指定および説明されているとおりに IP アドレス範囲を割り当てる必要があります。

それでは、HANA L インスタンスに使用する Azure VNet の作成についてもう少し詳しく見ていきましょう。

### <a name="creating-the-azure-vnet-for-hana-large-instances"></a>Azure VNet for HANA L インスタンスの作成

>[!Note]
>Azure VNet for HANA L インスタンスは、Azure Resource Manager デプロイメント モデルを使って作成する必要があります。 クラシック デプロイメント モデルとして一般的に知られている以前の Azure デプロイメント モデルは、HANA L インスタンス ソリューションではサポートされていません。

VNet は、Azure Portal、PowerShell、Azure テンプレート、または Azure CLI を使用して作成することができます ([Azure Portal を使用した仮想ネットワークの作成](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページを参照してください)。 以下の例では、Azure Portal で作成した VNet について詳しく説明しています。

Azure VNet の定義を Azure Portal で見ながら、いくつかの定義と、それらがさまざまな IP アドレス範囲の一覧とどのように関係しているかを見ていきます。 ここで用いている**アドレス空間**とは、Azure VNet が使用できるアドレス空間を意味します。 このアドレス空間は、VNet が BGP ルートの伝播に使用するアドレス範囲でもあります。 この**アドレス空間**は、次の画面で確認できます。

![Azure Portal に表示される Azure VNet のアドレス空間](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

前のケースでは、Azure VNet で使用される IP アドレス範囲が 10.16.0.0/16 と、かなり広範囲に割り当てられています。 つまり、この VNet に後から追加されるサブネットにはすべて、このアドレス空間内の IP アドレス範囲を割り当てることができます。 通常 Azure では、このように大きなアドレス範囲を 1 つの VNet に使用することは推奨されません。 しかし、Azure VNet に定義されるサブネットについて、さらに踏み込んで見てみましょう。

![Azure VNet のサブネットとその IP アドレス範囲](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

ご覧のように、この VNet にはまず "default" という VM サブネットがあり、さらに "GatewaySubnet" というサブネットがあります。
次のセクションでは、この図の "default" サブネットの IP アドレス範囲を **Azure VM サブネット IP アドレス範囲**と呼びます。 また、ゲートウェイ サブネットの IP アドレス範囲を **VNet ゲートウェイ サブネット IP アドレス範囲**と呼びます。 

上の 2 つの図に示したケースでは、**VNet アドレス空間**に、**Azure VM サブネット IP アドレス範囲**と **VNet ゲートウェイ サブネット IP アドレス範囲**の両方が含まれていることがわかります。 

アドレス空間を節約しなければならないケースや IP アドレス範囲が限られているケースでは、VNet の **VNet アドレス空間**を各サブネットで使用されている特定の範囲に限定することができます。 このケースでは、はっきり限定された複数の範囲として次のように **VNet アドレス空間**を定義することができます。

![2 つの空間から成る Azure VNet アドレス空間](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

このケースでは、**VNet アドレス空間**に 2 つの空間が定義されています。 この 2 つの空間は、**Azure VM サブネット IP アドレス範囲**と **VNet ゲートウェイ サブネット IP アドレス範囲**に対して定義されている IP アドレス範囲と同じです。

これらのテナント サブネット (VM サブネット) には、任意の命名標準を使用できます。 ただし、**各 VNet にゲートウェイ サブネットが常に 1 つだけ存在する必要があります**。それが SAP HANA on Azure (L インスタンス) ExpressRoute 回線に接続します。 また、**このゲートウェイ サブネットは、常に "GatewaySubnet" という名前である必要があります**。これは、ExpressRoute ゲートウェイが適切に配置されるようにするためです。

> [!WARNING] 
> ゲートウェイ サブネットに必ず "GatewaySubnet" と命名することは非常に重要です。

使用するアドレス範囲が不連続であっても、複数の VM サブネットを使用することができます。 ただし前述のように、そのようなアドレス範囲は、集約された形式か、VM サブネットとゲートウェイ サブネットの範囲を厳密に列挙したリスト形式の **VNet アドレス空間**に含まれている必要があります。

HANA L インスタンスに接続する Azure VNet について重要な情報を以下にまとめます。

- HANA L インスタンスの初期デプロイを行う際、**VNet アドレス空間**を Microsoft に提出する必要があります。 
- **VNet アドレス空間**は、Azure VM サブネット IP アドレス範囲と VNet ゲートウェイ サブネット IP アドレス範囲を含む 1 つの大きな範囲です。
- また、VM サブネット IP アドレス範囲と VNet ゲートウェイ サブネット IP アドレス範囲という異なる IP アドレス範囲を含んだ複数の範囲を **VNet アドレス空間**として送信することもできます。
- 定義した **VNet アドレス空間**は、BGP ルートの伝播に使用されます。
- ゲートウェイ サブネットの名前は **"GatewaySubnet"** にする必要があります。
- **VNet アドレス空間**は、HANA L インスタンス側で、Azure から HANA L インスタンス ユニットへのトラフィックを許可または拒否するためのフィルターとして使用されます。 Azure VNet の BGP ルーティング情報と、HANA L インスタンス側でフィルター処理用に構成されている IP アドレス範囲とが一致しない場合、接続に問題が生じることがあります。
- ゲートウェイ サブネットについては、「HANA L インスタンスの ExpressRoute への VNet の接続」セクションでさらに詳しく説明します。



### <a name="different-ip-address-ranges-to-be-defined"></a>定義する各種の IP アドレス範囲 

前のセクションで、HANA L インスタンスをデプロイするうえで必要な IP アドレス範囲をいくつか挙げました。 しかし、重要な IP アドレス範囲は他にも存在します。 そのいくつかについて詳しく見ていきましょう 初期デプロイを依頼する前に、以下の IP アドレス (Microsoft に提出する必要があるのは一部のみ) を定義する必要があります。

- **VNet アドレス空間:** 前述したように、SAP HANA L インスタンス環境に接続する Azure Virtual Network (VNet) のアドレス空間パラメーターに割り当て済み (または割り当て予定) の IP アドレス範囲が VNet アドレス空間です。 このアドレス空間パラメーターは、Azure VM サブネット範囲と Azure ゲートウェイ サブネット範囲から成る複数行の値として指定することをお勧めします (前の図を参照)。 この範囲をオンプレミスのアドレス範囲やサーバー IP プールのアドレス範囲、ER-P2P のアドレス範囲と重複させることはできません。 この IP アドレス範囲を取得するには、どうすればよいでしょうか。 通常、社内のネットワークで使われていない IP アドレス範囲は、社内のネットワーク チームまたはサービス プロバイダーから入手できます。 たとえば、Azure VM サブネット (上の行) が 10.0.1.0/24 で、Azure ゲートウェイ サブネット (下の行) が 10.0.2.0/28 である場合、Azure VNet アドレス空間は 2 行 (10.0.1.0/24 と 10.0.2.0/28) に分けて記述することをお勧めします。 アドレス空間の値は集約することもできますが、将来ネットワーク内のどこかで大きなアドレス空間内の未使用の IP アドレス範囲が誤って再利用されることを防ぐために、サブネット範囲に合わせることをお勧めします。 **VNET アドレス空間は、初期デプロイを依頼する際、Microsoft に提出する必要がある IP アドレス範囲です。**

- **Azure VM サブネット IP アドレス範囲:** 前述したように、SAP HANA L インスタンス環境に接続する Azure VNET の Azure VNet サブネット パラメーターに割り当て済み (または割り当て予定) の IP アドレス範囲です。 この IP アドレス範囲は、Azure VM に IP アドレスを割り当てる目的で使用されます。 この範囲外の IP アドレスは、SAP HANA L インスタンス サーバーへの接続が許可されます。 必要に応じて、複数の Azure VM サブネットを使用できます。 Microsoft は、各 Azure VM サブネットに /24 CIDR ブロックの使用を推奨しています。 このアドレス範囲は、Azure VNet アドレス空間に使用されている値の範囲内であることが必要です。 この IP アドレス範囲を取得するには、どうすればよいでしょうか。 通常、社内のネットワークで使われていない IP アドレス範囲は、社内のネットワーク チームまたはサービス プロバイダーから入手できます。

- **VNet ゲートウェイ サブネット IP アドレス範囲:** 推奨されるサイズは、使用する予定の機能によって異なります。
   - Ultra-performance ExpressRoute ゲートウェイ: /26 アドレス ブロック - Type II クラスの SKU で必要
   - High Performance ExpressRoute ゲートウェイ (以下) を使った ExpressRoute と VPN の共存: /27 アドレス ブロック
   - その他すべて: /28 アドレス ブロック。 このアドレス範囲は、"VNet アドレス空間" に使用されている値の範囲内であることが必要です。 このアドレス範囲は、Microsoft に提出する必要のある Azure VNet アドレス空間に使用されている値の範囲内であることが必要です。 この IP アドレス範囲を取得するには、どうすればよいでしょうか。 通常、社内のネットワークで使われていない IP アドレス範囲は、社内のネットワーク チームまたはサービス プロバイダーから入手できます。 

- **ER-P2P 接続のアドレス範囲:** SAP HANA L インスタンスの ExpressRoute (ER) P2P 接続に使用する IP 範囲です。 この範囲の IP アドレスは、/29 CIDR の IP アドレス範囲であることが必要です。 この範囲をオンプレミスのアドレス範囲や他の Azure IP アドレス範囲と重複させることはできません。 この IP アドレス範囲は、Azure VNet ExpressRoute ゲートウェイから SAP HANA L インスタンス サーバーへの ER 接続を設定する際に使用されます。 この IP アドレス範囲を取得するには、どうすればよいでしょうか。 通常、社内のネットワークで使われていない IP アドレス範囲は、社内のネットワーク チームまたはサービス プロバイダーから入手できます。 **この IP アドレス範囲は、初期デプロイを依頼する際、Microsoft に提出する必要があります。**
  
- **サーバー IP プール アドレス範囲:** HANA L インスタンス サーバーに個々の IP アドレスを割り当てるときに、この IP アドレス範囲が使用されます。 推奨サブネット サイズは /24 CIDR ブロックですが、必要であればもっと小さくすることもできます (IP アドレス数の下限は 64 個)。 この範囲の先頭から 30 個の IP アドレスは、Microsoft 用に予約されています。 範囲のサイズを選ぶ際は、必ずこの点を考慮してください。 この範囲をオンプレミスのアドレスや他の Azure IP アドレスと重複させることはできません。 この IP アドレス範囲を取得するには、どうすればよいでしょうか。 通常、社内のネットワークで使われていない IP アドレス範囲は、社内のネットワーク チームまたはサービス プロバイダーから入手できます。 /24 (推奨) の一意の CIDR ブロック。SAP HANA on Azure (L インスタンス) に必要な特定の IP アドレスの割り当てに使用します。 **この IP アドレス範囲は、初期デプロイを依頼する際、Microsoft に提出する必要があります。**
 
以上の IP アドレス範囲を自分で設計、定義する必要があります。ただしそのすべてを Microsoft に送信する必要はありません。 これらを要約すると、Microsoft に提出する必要がある IP アドレス範囲は次のとおりです。

- Azure VNet アドレス空間
- ER-P2P 接続のアドレス範囲
- サーバー IP プールのアドレス範囲

HANA L インスタンスへの接続を必要とする VNet をさらに追加する場合は、新たに追加する Azure VNet アドレス空間を Microsoft に提出する必要があります。 

以下に示したのは、各種アドレス範囲の例です。また、ご自分で構成して最終的に Microsoft に提出することになるアドレス範囲の例もいくつか挙げています。 これを見るとわかるように、1 つ目の例では、Azure VNet アドレス空間の値が集約されておらず、先頭の Azure VM サブネット IP アドレス範囲と VNet ゲートウェイ サブネット IP アドレス範囲に基づいて定義されています。 Azure VNet 内で複数の VM サブネットを使用する場合、Azure VNet アドレス空間に追加する新しい VM サブネットの IP アドレス範囲を新たに構成して提出すれば、それに応じて適切に機能します。

![SAP HANA on Azure (L インスタンス) の最小デプロイ環境に必要な IP アドレス範囲](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

Microsoft に提出するデータは集約することもできます。 その場合、Azure VNet のアドレス空間に含まれる空間は 1 つだけです。 上の例で使用した IP アドレス範囲を使用しています。 この集約された VNet アドレス空間は、次のようになります。

![SAP HANA on Azure (L インスタンス) の最小限のデプロイに必要な IP アドレス範囲の例 (その 2)](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

小さい範囲を 2 つ使って Azure VNet のアドレス空間を定義する代わりに、4,096 個の IP アドレスを含んだ、より大きい範囲が 1 つ使われていることがわかります。 このように大きなアドレス空間を定義すると、かなり大きな範囲が未使用のまま残ってしまいます。 VNet アドレス空間の値は BGP ルートの伝播に使用されるため、未使用の範囲がオンプレミスまたはネットワーク内のどこかで使用されると、ルーティングに問題が生じる可能性があります。 そのため VNet アドレス空間は、実際に使用するサブネットのアドレス空間の境界と厳密に合わせることをお勧めします。 必要に応じて、VNet にダウンタイムを発生させることなく、後からいつでも新しいアドレス空間の値を追加することができます。
 
> [!IMPORTANT] 
> ER-P2P、サーバー IP プール、Azure VNet アドレス空間の各 IP アドレス範囲は、互いに (またはネットワーク内のどこかで使用されている他の範囲と) **重複しない**ようにしてください。つまりそれぞれの IP アドレス範囲は不連続であること、そして他の範囲のサブネットになっていないことが必要です (上の 2 つの図を参照)。 範囲が重複すると、Azure VNet が ExpressRoute 回線に接続できなくなります。

### <a name="next-steps-after-address-ranges-have-been-defined"></a>アドレス範囲を定義した後に行う手順
IP アドレス範囲の定義後、次の作業を行う必要があります。

1. Azure VNet アドレス空間の IP アドレス範囲、ER-P2P 接続のアドレス範囲、サーバー IP プールのアドレス範囲を、このドキュメントの冒頭で挙げた他のデータと一緒に提出します。 この時点で VNet と VM サブネットの作成を開始することもできます。 
2. Azure サブスクリプションと HANA L インスタンス スタンプの間の ExpressRoute 回線が Microsoft によって作成されます。
3. Microsoft によって、L インスタンス スタンプにテナント ネットワークが作成されます。
4. Microsoft が SAP HANA on Azure (L インスタンス) インフラストラクチャ内に、HANA L インスタンスと通信する Azure VNet アドレス空間からの IP アドレスを受け入れるネットワークを構成します。
5. 注文された SAP HANA on Azure (L インスタンス) SKU に応じて、Microsoft がテナント ネットワークにコンピューティング ユニットを割り当て、ストレージを割り当ててマウントし、オペレーティング システム (SUSE または Red Hat Linux) をインストールします。 これらのユニットの IP アドレスは、お客様が Microsoft に提出したサーバー IP プール アドレス範囲から割り当てられます。

デプロイ プロセスの最後に、Microsoft からお客様に以下のデータが送信されます。
- Azure VNet を HANA L インスタンスに接続する ExpressRoute 回線にお客様の Azure VNet を接続するために必要な情報。
     - 承認キー
     - ExpressRoute PeerID
- ExpressRoute 回線と Azure VNet を設定した後で HANA L インスタンスにアクセスするためのデータ。

HANA L インスタンスを接続する手順については、ドキュメント「[End to End Setup for SAP HANA Large Instances (SAP HANA L インスタンスのエンド ツー エンドのセットアップ)](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/)」で確認することもできます。 以下の手順の多くは、そのドキュメントのデプロイの例に示されています。 


## <a name="connecting-a-vnet-to-hana-large-instance-expressroute"></a>HANA L インスタンスの ExpressRoute への VNet の接続

IP アドレス範囲をすべて定義し、必要なデータを Microsoft から入手したら、いよいよ先ほど作成した VNet を HANA L インスタンスに接続することができます。 Azure VNet を作成した後、VNet 上に ExpressRoute ゲートウェイを作成する必要があります。これを利用して、L インスタンス スタンプのカスタマー テナントに接続する ExpressRoute 回線に VNet をリンクします。

> [!NOTE] 
> この手順では、新しいゲートウェイは指定された Azure サブスクリプション内で作成されてから、指定された Azure VNet に接続されるので、最大 30 分かかる場合があります。

ゲートウェイが既に存在する場合、それが ExpressRoute ゲートウェイかどうかを確認します。 そうでなかった場合、ゲートウェイを削除し、ExpressRoute ゲートウェイとして再作成する必要があります。 ExpressRoute ゲートウェイが既に確立されている場合、Azure VNet は既にオンプレミス接続を行うために ExpressRoute 回線に接続されているので、次の「VNet のリンク」セクションに進んでください。

- (新しい) [Azure Portal](https://portal.azure.com/) または PowerShell を使用して、お使いの VNet に接続された ExpressRoute VPN ゲートウェイを作成します。
  - Azure Portal を使用する場合、新しい **Virtual Network ゲートウェイ**を追加し、ゲートウェイの種類として **ExpressRoute** を選択します。
  - 代わりに PowerShell を使用する場合、まずは最新の [Azure PowerShell SDK](https://azure.microsoft.com/downloads/) をダウンロードして使用し、最適なエクスペリエンスを確保します。 次のコマンドは ExpressRoute ゲートウェイを作成します。 _$_ の後に続くテキストはユーザー定義変数で、ユーザー固有の情報に更新する必要があります。

```PowerShell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA Large Instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzureRmVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

この例では、HighPerformance ゲートウェイ SKU が使用されました。 使用できるのは、HighPerformance または UltraPerformance です。SAP HANA on Azure (L インスタンス) でサポートされているゲートウェイ SKU はこれらのみとなります。

> [!IMPORTANT]
> SKU タイプ S384、S384m、S384xm、S576、S768、S960 (Type II クラス SKU) の HANA L インスタンスの場合、UltraPerformance Gateway SKU の使用は必須です。

### <a name="linking-vnets"></a>VNet のリンク

Azure VNet 用に ExpressRoute ゲートウェイを作成した後は、Microsoft から提供された承認情報を使用して、ExpressRoute ゲートウェイを SAP HANA on Azure (L インスタンス) の ExpressRoute 回線 (この接続を行うために作成したもの) に接続します。 この手順は、Azure Portal または PowerShell で実行できます。 ポータルの使用を推奨しますが、PowerShell での手順は次のとおりです。 

- 接続ごとに異なる AuthGUID を使用して、各 VNet ゲートウェイで次のコマンドを実行します。 次のスクリプトに示す最初の 2 つのエントリは、Microsoft から提供される情報です。 また、AuthGUID はすべての VNet とそのゲートウェイに固有です。 つまり、別の Azure VNet を追加する場合は、HANA L インスタンスを Azure に接続する ExpressRoute 回線の AuthID がもう 1 つ必要になります。 

```PowerShell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway Information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzureRmVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

サブスクリプションに関連付けられた複数の ExpressRoute 回線にゲートウェイを接続する場合は、この手順を複数回実行しなければならないことがあります。 たとえば、通常は同じ VNet ゲートウェイを、VNet とオンプレミス ネットワークとを接続する ExpressRoute 回線に接続します。

## <a name="adding-more-ip-addresses-or-subnets"></a>IP アドレスまたはサブネットの追加

IP アドレスまたはサブネットを追加するには、Azure Portal、PowerShell、CLI のいずれかを使用します。

この場合、集約した範囲を新たに作成するのではなく、新しい IP アドレス範囲を新しい範囲として VNet アドレス空間に追加することをお勧めします。 どちらにしても、新しい IP アドレス範囲のクライアントから HANA L インスタンス ユニットへの接続を許可するためには、この変更を Microsoft に提出する必要があります。 追加する新しい VNet アドレス空間を取得するために、Azure サポート要求を開くことができます。 確認通知を受信したら、次の手順を実行します。

追加のサブネットを Azure Portal で作成するには、「[Azure Portal を使用した仮想ネットワークの作成](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。PowerShell で作成するには、「[PowerShell を使用した仮想ネットワークの作成](../../../virtual-network/virtual-networks-create-vnet-arm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。

## <a name="adding-vnets"></a>VNet の追加

最初に 1 つ以上の Azure VNet を接続した後、SAP HANA on Azure (L インスタンス) にアクセスする追加の Azure VNet を作成することができます。 最初に Azure サポート要求を送信します。この要求には、特定の Azure デプロイを識別するための固有の情報と、Azure VNet アドレス空間の IP アドレス範囲を含めます。 その後、SAP HANA on Azure サービス管理から、追加の VNet および ExpressRoute を接続するために必要な情報が提供されます。 ExpressRoute 回線を HANA L インスタンスに接続するためには、すべての VNet に一意の承認キーが必要です。

新しい Azure VNet を追加する手順:

1. オンボード プロセスの最初の手順を完了します。_Azure VNet の作成_に関するセクションを参照してください。
2. オンボード プロセスの 2 番目の手順を完了します。_ゲートウェイ サブネットの作成_に関するセクションを参照してください。
3. 追加の VNet を HANA L インスタンスの ExpressRoute 回線に接続するには、新しい VNet の情報で Azure サポート要求を開き、新しい承認キーを要求します。
4. 承認の完了通知を受け取った後、Microsoft から提供された承認情報を使用して、オンボード プロセスの 3 番目の手順を完了します。「_VNet のリンク_」セクションを参照してください。

## <a name="increasing-expressroute-circuit-bandwidth"></a>ExpressRoute 回線の帯域幅の増加

SAP HANA on Azure サービス管理に問い合わせてください。 SAP HANA on Azure (L インスタンス) の ExpressRoute 回線の帯域幅を増やすよう推奨された場合は、Azure サポート要求を作成します  (1 つの回線につき、帯域幅を最大 10 Gbps 増やすよう要求することができます)。作業が完了すると通知が届きます。その他の作業は不要です。これで Azure が高速化されます。

## <a name="adding-an-additional-expressroute-circuit"></a>ExpressRoute 回線の追加

SAP HANA on Azure サービス管理に問い合わせてください。ExpressRoute 回線の追加を推奨された場合は、Azure サポート要求を作成して新しい ExpressRoute 回線の作成を要求します (また、それに接続するための承認情報も取得します)。 VNet で使用されるアドレス空間は、SAP HANA on Azure サービス管理での承認に必要となるため、要求を作成する前に定義しておく必要があります。

新しい回線の作成と、SAP HANA on Azure サービス管理の構成が完了した後、作業の続行に必要な情報が記載された通知が届きます。 上の手順に従って新しい VNet を作成し、この追加の回線に接続します。 既に同じ Azure リージョン内の別の SAP HANA on Azure (L インスタンス) ExpressRoute 回線に接続されている Azure VNet は、この追加の回線には接続できません。

## <a name="deleting-a-subnet"></a>サブネットの削除

VNet のサブネットを削除するには、Azure Portal、PowerShell、CLI のいずれかを使用することができます。 Azure VNet の IP アドレス範囲/Azure VNet アドレス空間の範囲が集約されている場合、Microsoft のフォローアップはありません。 ただし、削除したサブネットを含む BGP ルート アドレス空間が依然として VNet から伝播されている場合は除きます。 Azure VNet IP アドレス範囲/Azure VNet アドレス空間を複数の IP アドレス範囲として定義した場合で、かつそのいずれかが、削除したサブネットに割り当てられていた場合、そのアドレス範囲を SAP HANA on Azure (L インスタンス) の通信可能範囲から削除するには、該当するアドレス範囲を VNet アドレス空間から削除したうえで、SAP HANA on Azure の Service Management に伝える必要があります。

まだ具体的な手順にはなっていませんが、Azure.com のサブネットの削除専用ガイダンス (サブネットの削除の手順) は、追加時とは逆の手順となります。 サブネットの作成の詳細については、[Azure Portal を使用した仮想ネットワークの作成](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページを参照してください。

## <a name="deleting-a-vnet"></a>VNet の削除

VNet を削除するには、Azure Portal、PowerShell、CLI のいずれかを使用します。 SAP HANA on Azure Service Management は、SAP HANA on Azure (L インスタンス) の ExpressRoute 回線での既存の承認を取り消し、HANA L インスタンスとの通信に使用される Azure VNet IP アドレス範囲/Azure VNet アドレス空間を削除します。

VNet が削除された後、Azure サポート要求を開き、削除する IP アドレス空間範囲を指定します。

まだ具体的な手順にはなっていませんが、Azure.com の VNet の削除専用ガイダンス (VNet の削除の手順) は、上で説明されている追加時の手順とは逆になります。 VNet の作成の詳細については、「[Azure Portal を使用した仮想ネットワークの作成](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」と「[PowerShell を使用した仮想ネットワークの作成](../../../virtual-network/virtual-networks-create-vnet-arm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。

すべてを確実に削除するために、次の項目を削除します。

- ExpressRoute 接続、VNet ゲートウェイ、VNet ゲートウェイのパブリック IP、VNet

## <a name="deleting-an-expressroute-circuit"></a>ExpressRoute 回線の削除

SAP HANA on Azure (L インスタンス) の追加の ExpressRoute 回線を削除するには、SAP HANA on Azure サービス管理で Azure サポート要求を開き、回線の削除を要求します。 Azure サブスクリプション内では、必要に応じて VNet を削除または保持することができます。 ただし、HANA L インスタンスの ExpressRoute 回線と、そのリンク先 VNet ゲートウェイとを結ぶ接続は削除する必要があります。

VNet も削除する場合は、前のセクション「VNet の削除」のガイダンスに従います。


