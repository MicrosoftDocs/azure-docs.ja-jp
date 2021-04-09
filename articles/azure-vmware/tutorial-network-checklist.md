---
title: チュートリアル - ネットワーク計画のチェックリスト
description: Azure VMware Solution のネットワーク接続とネットワーク ポートのネットワーク要件について説明します。
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 8cee5fa24aab8bd7fe6a9527f9c8e7cdff997511
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462067"
---
# <a name="networking-planning-checklist-for-azure-vmware-solution"></a>Azure VMware Solution のネットワーク計画のチェックリスト 

Azure VMware Solution は、オンプレミスおよび Azure ベースの環境またはリソースからユーザーやアプリケーションがアクセスできる VMware プライベート クラウド環境を提供します。 接続は、Azure ExpressRoute 接続や VPN 接続などのネットワーク サービスを通じて提供されます。 サービスを有効にするには、特定のネットワーク アドレス範囲とファイアウォール ポートが必要です。 この記事では、Azure VMware Solution で適切に動作するようにネットワークを構成するために必要な情報を提供します。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 仮想ネットワークと ExpressRoute 回線に関する考慮事項
> * ルーティングとサブネットの要件
> * サービスと通信するために必要なネットワーク ポート
> * Azure VMware Solution での DHCP と DNS に関する考慮事項

## <a name="prerequisite"></a>前提条件
ExpressRoute プロバイダーのサービスを含め、すべてのゲートウェイで 4 バイトの自律システム番号 (ASN) がサポートされていることを確認します。 Azure VMware Solution では、4 バイトのパブリック ASN を使用してルートをアドバタイズします。

## <a name="virtual-network-and-expressroute-circuit-considerations"></a>仮想ネットワークと ExpressRoute 回線に関する考慮事項
サブスクリプション内に仮想ネットワーク接続を作成するときには、ピアリングによって ExpressRoute 回線が確立され、承認キーと、Azure portal で要求したピアリング ID が使用されます。 ピアリングは、プライベート クラウドと仮想ネットワークの間のプライベートな 1 対 1 の接続です。

> [!NOTE] 
> ExpressRoute 回線は、プライベート クラウドのデプロイには含まれません。 オンプレミスの ExpressRoute 回線については、このドキュメントでは取り扱いません。 プライベート クラウドへのオンプレミス接続が必要な場合は、既存の ExpressRoute 回線のいずれかを使用するか、Azure portal で購入することができます。

プライベート クラウドをデプロイすると、vCenter および NSX-T Manager の IP アドレスが提供されます。 これらの管理インターフェイスにアクセスするには、ご使用のサブスクリプションの仮想ネットワークにさらに多くのリソースを作成する必要があります。 これらのリソースを作成し、[ExpressRoute プライベート ピアリング](tutorial-expressroute-global-reach-private-cloud.md)を確立する手順については、チュートリアルを参照してください。

プライベート クラウドの論理ネットワークには、事前にプロビジョニングされた NSX-T が付属しています。 Tier-0 ゲートウェイと Tier-1 ゲートウェイが事前にプロビジョニングされています。 セグメントを作成し、それを既存の Tier-1 ゲートウェイにアタッチすることも、自分が定義する新しい Tier-1 ゲートウェイにアタッチすることもできます。 NSX-T 論理ネットワーク コンポーネントは、ワークロード間の East-West 接続を提供し、インターネットおよび Azure サービスへの North-South 接続を提供します。

## <a name="routing-and-subnet-considerations"></a>ルーティングとサブネットに関する考慮事項
Azure VMware Solution プライベート クラウドは、Azure ExpressRoute 接続を使用して Azure 仮想ネットワークに接続されます。 この高い帯域幅と短い待ち時間の接続によって、ご自分のプライベート クラウド環境から、ご自分の Azure サブスクリプションで実行されているサービスにアクセスできます。 ルーティングは Border Gateway Protocol (BGP) ベースであり、プライベート クラウドのデプロイごとに既定で自動的にプロビジョニングされ、有効になります。 

Azure VMware Solution プライベート クラウドでは、次に示すように、サブネット用に少なくとも `/22` の CIDR ネットワーク アドレス ブロックが必要です。 このネットワークによって、ご自分のオンプレミス ネットワークが補完されます。 アドレス ブロックは、サブスクリプションやオンプレミス ネットワークにある他の仮想ネットワークで使用されているアドレス ブロックと重複しないようにする必要があります。 このアドレス ブロック内では、管理、プロビジョニング、vMotion ネットワークが自動的にプロビジョニングされます。

>[!NOTE]
>アドレス ブロックで許可される範囲は RFC 1918 プライベート アドレス空間 (10.0.0.0/8、172.16.0.0/12、192.168.0.0/16) です。ただし、172.17.0.0/16 を除きます。

`/22` CIDR ネットワーク アドレス ブロックの例: `10.10.0.0/22`

サブネット:

| ネットワークの使用法             | Subnet | 例          |
| ------------------------- | ------ | ---------------- |
| プライベート クラウドの管理  | `/26`  | `10.10.0.0/26`   |
| HCX 管理の移行       | `/26`  | `10.10.0.64/26`  |
| 予約済み Global Reach     | `/26`  | `10.10.0.128/26` |
| 予約済み ExpressRoute     | `/27`  | `10.10.0.192/27` |
| ExpressRoute ピアリング      | `/27`  | `10.10.0.224/27` |
| ESXi の管理           | `/25`  | `10.10.1.0/25`   |
| vMotion ネットワーク           | `/25`  | `10.10.1.128/25` |
| レプリケーション ネットワーク       | `/25`  | `10.10.2.0/25`   |
| vSAN                      | `/25`  | `10.10.2.128/25` |
| HCX アップリンク                | `/26`  | `10.10.3.0/26`   |
| 予約済み                  | `/26`  | `10.10.3.64/26`  |
| 予約済み                  | `/26`  | `10.10.3.128/26` |
| 予約済み                  | `/26`  | `10.10.3.192/26` |



## <a name="required-network-ports"></a>必要なネットワーク ポート

| source | 宛先 | Protocol | Port | 説明  | 
| ------ | ----------- | :------: | :---:| ------------ | 
| プライベート クラウドの DNS サーバー | オンプレミスの DNS サーバー | UDP | 53 | DNS クライアント - オンプレミスの DNS クエリのために、PC vCenter からの要求を転送します (後述の DNS に関するセクションを確認してください) |  
| オンプレミスの DNS サーバー   | プライベート クラウドの DNS サーバー | UDP | 53 | DNS クライアント - オンプレミスのサービスからの要求をプライベート クラウド DNS サーバーに転送します (後述の DNS に関するセクションを確認してください) |  
| オンプレミス ネットワーク  | プライベート クラウドの vCenter Server  | TCP (HTTP)  | 80 | vCenter Server では、直接 HTTP 接続用にポート 80 が必要です。 ポート 80 から HTTPS ポート 443 に要求がリダイレクトされます。 このリダイレクトは、`https://server` の代わりに `http://server` を使用する場合に役立ちます。  <br><br>WS-Management (ポート 443 も開いている必要があります) <br><br>vCenter Server にバンドルされている SQL Server 2008 データベースではなく、カスタムの Microsoft SQL データベースを使用する場合、SQL Reporting Services によってポート 80 が使用されます。 vCenter Server をインストールするときに、インストーラーによって、vCenter Server 用の HTTP ポートを変更するよう求められます。 インストールを正常に完了するには、vCenter Server の HTTP ポートをカスタム値に変更します。 Microsoft インターネット インフォメーション サービス (IIS) でもポート 80 が使用されます。 ポート 80 に対する vCenter Server と IIS の競合に関するページを参照してください。 |  
| プライベート クラウドの管理ネットワーク | オンプレミスの Active Directory  | TCP  | 389 | このポートは、vCenter Server のローカル インスタンスとすべてのリモート インスタンスで開かれている必要があります。 このポートは、vCenter Server グループのディレクトリ サービス用の LDAP ポート番号です。 この vCenter Server インスタンスをリンク モード グループに参加させない場合でも、vCenter Server システムをポート 389 にバインドする必要があります。 このポートで別のサービスが実行されている場合は、それを削除するか、そのポートを別のポートに変更することをお勧めします。 LDAP サービスは、1025 から 65535 の任意のポートで実行できます。  このインスタンスが Microsoft Windows Active Directory として機能している場合は、ポート番号 389 を、1025 から 65535 の使用可能なポートに変更します。 このポートはオプションです。プライベート クラウドの vCenter 上でオンプレミス AD を ID ソースとして構成するために使用します。 |  
| オンプレミス ネットワーク  | プライベート クラウドの vCenter Server  | TCP (HTTPS)  | 443 | このポートを使用すると、オンプレミス ネットワークから vCenter にアクセスできます。 vCenter Server システムが vSphere クライアントからの接続をリッスンするために使用する既定のポート。 vCenter Server システムが vSphere クライアントからデータを受信できるようにするには、ファイアウォールでポート 443 を開きます。 また、vCenter Server システムは、ポート 443 を使用して、SDK クライアントからのデータ転送を監視します。 このポートは、次のサービスにも使用されます。WS-Management (ポート 80 も開いている必要があります)。 vSphere Update Manager への vSphere Client アクセス。 vCenter Server へのサードパーティのネットワーク管理クライアント接続。 ホストへのサードパーティのネットワーク管理クライアント アクセス。 |  
| Web ブラウザー  | Hybrid Cloud Manager  | TCP (HTTPS) | 9443 | Hybrid Cloud Manager のシステム構成用の Hybrid Cloud Manager 仮想アプライアンス管理インターフェイス。 |
| 管理ネットワーク  | Hybrid Cloud Manager | SSH | 22 | Hybrid Cloud Manager への管理者 SSH アクセス。 |
| HCM | クラウド ゲートウェイ | TCP (HTTPS) | 8123 | ホストベースのレプリケーション サービスの指示をハイブリッド クラウド ゲートウェイに送信します。 |
| HCM | クラウド ゲートウェイ | HTTP TCP (HTTPS) | 9443 | REST API を使用してローカルのハイブリッド クラウド ゲートウェイに管理指示を送信します。 |
| クラウド ゲートウェイ | L2C | TCP (HTTPS) | 443 | L2C がハイブリッド クラウド ゲートウェイと同じパスを使用する場合、クラウド ゲートウェイから L2C に管理指示を送信します。 |
| クラウド ゲートウェイ | ESXi ホスト | TCP | 80、902 | 管理と OVF のデプロイ。 |
| クラウド ゲートウェイ (ローカル)| クラウド ゲートウェイ (リモート) | UDP | 4500 | IPsec に必要<br>   双方向トンネルのワークロードをカプセル化するためのインターネット キー交換 (IKEv2)。 ネットワーク アドレス変換トラバーサル (NAT-T) もサポートされています。 |
| クラウド ゲートウェイ (ローカル) | クラウド ゲートウェイ (リモート)  | UDP | 500 | IPsec に必要<br> 双方向トンネルのためのインターネット キー交換 (ISAKMP)。 |
| オンプレミスの vCenter ネットワーク | プライベート クラウドの管理ネットワーク | TCP | 8000 |  オンプレミスの vCenter Server からプライベート クラウドの vCenter への VM の vMotion   |     

## <a name="dhcp-and-dns-resolution-considerations"></a>DHCP と DNS の解決に関する考慮事項
プライベート クラウド環境で実行されるアプリケーションとワークロードには、ルックアップと IP アドレスの割り当てのための名前解決と DHCP サービスが必要です。 これらのサービスを提供するために、DHCP と DNS の適切なインフラストラクチャが必要です。 仮想マシンを構成して、ご自分のプライベート クラウド環境でこれらのサービスを提供することができます。  

ブロードキャスト DHCP トラフィックを WAN 経由でオンプレミスにルーティングして戻すのではなく、NSX に組み込まれている DHCP サービスを使用するか、プライベート クラウドのローカル DHCP サーバーを使用します。

詳細については、「[DHCP サービスを NSX-T ネットワーク セグメントに提供する](deploy-azure-vmware-solution.md#optional-provide-dhcp-services-to-nsx-t-network-segment)」の記事を参照してください。


## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure VMware Solution プライベート クラウドをデプロイするための考慮事項と要件について説明しました。 適切なネットワークを準備できたら、次のチュートリアルに進んで Azure VMware Solution のプライベート クラウドを作成します。

> [!div class="nextstepaction"]
> [Azure VMware Solution のプライベート クラウドを作成する](tutorial-create-private-cloud.md)
