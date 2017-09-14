---
title: "Site Recovery を使用して VMware から Azure レプリケーションへのネットワークを計画する | Microsoft Docs"
description: "この記事では、Azure Site Recovery を使用して Azure VM をレプリケートする場合に必要なネットワーク計画について説明します。"
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/01/2017
ms.author: sujayt
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: 7b37e853f6b97ba313111f9201f877846a28fae9
ms.contentlocale: ja-jp
ms.lasthandoff: 08/02/2017

---
# <a name="step-3-plan-networking-for-azure-vm-replication"></a>手順 3: Azure VM レプリケーションのネットワークを計画する

[デプロイの前提条件](azure-to-azure-walkthrough-prerequisites.md)を確認した後、この記事を読んで、Azure Site Recovery サービスを使って、1 つの Azure リージョンから別のリージョンへ Azure 仮想マシン (VM) をレプリケートおよび復旧するときのネットワークの考慮事項を理解します。 

- 記事の手順を完了すると、レプリケートする Azure VM の送信アクセスを設定する方法と、オンプレミス サイトから該当の VM に接続する方法を明確に理解できます。
- この記事の末尾にあるコメント欄でご意見をお送りください。または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) に質問を投稿してください。

>[!NOTE]
> Site Recovery を使った Azure VM レプリケーションは現在プレビューの段階です。



## <a name="network-overview"></a>ネットワークの概要

Azure VM は通常、Azure 仮想ネットワーク / サブネットに配置され、Azure ExpressRoute を使用したオンプレミス サイトからの接続、または VPN 接続が行われています。

ネットワークは通常、ファイアウォールやネットワーク セキュリティ グループ (NSG) を使用して保護されます。 ファイアウォールは、ネットワーク接続の制御に URL ベースまたは IP ベースの一覧を使用できます。 NSG では、IP の範囲を基にしたルールを使用します。 


Site Recovery が期待どおりに動作するには、レプリケートしたい VM から、送信ネットワークの接続に変更を加える必要があります。 Site Recovery では、ネットワーク接続を制御するための認証プロキシの使用をサポートしていません。 認証プロキシがある場合、レプリケーションを有効にできません。 


次の図は、Azure VM で実行しているアプリケーションの一般的な環境を示しています。

![customer-environment](./media/azure-to-azure-walkthrough-network/source-environment.png)

**Azure VM 環境**

また、Azure ExpressRoute または VPN 接続を使って、オンプレミス サイトから Azure セットアップへの接続がある場合もあります。 

![customer-environment](./media/azure-to-azure-walkthrough-network/source-environment-expressroute.png)

**Azure へのオンプレミス接続**



## <a name="outbound-connectivity-for-urls"></a>URL に対する送信接続

送信接続を制御するために URL ベースのファイアウォール プロキシを使用している場合、必ず Site Recovery によって使用される以下の URL を許可してください。

**URL** | **詳細**  
--- | ---
*.blob.core.windows.net | ソース リージョンのキャッシュ ストレージ アカウントに、VM からデータが書き込まれるよう許可します。
login.microsoftonline.com | Site Recovery サービス URL に対する承認と認証を提供します。
*.hypervrecoverymanager.windowsazure.com | VM からの Site Recovery サービスとの通信を許可します。
*.servicebus.windows.net | VM から Site Recovery の監視および診断データを書き込むことができるようにするために必要です。

## <a name="outbound-connectivity-for-ip-address-ranges"></a>IP アドレス範囲に対する送信接続

- [このスクリプト](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702)をダウンロードして実行することで、NSG に必要なルールを自動的に作成できます。
- 必要な NSG ルールをテスト NSG に作成し、問題がないことを確認してから、運用環境の NSG にルールを作成することをお勧めします。
- 必要な数の NSG ルールを作成するには、サブスクリプションがホワイトリストに登録されていることを確認します。 サブスクリプションで NSG ルールの制限値を増やす必要があるときは、サポートにお問い合わせください。
IP ベースのファイアウォール プロキシまたは NSG ルールを使用して送信接続を制御する場合は、仮想マシンのソースとターゲットの場所に応じて、次の IP 範囲をホワイトリストに登録する必要があります。

    - ソースの場所に対応するすべての IP アドレス範囲  ([範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)をダウンロードする)。VM からキャッシュ ストレージ アカウントにデータを書き込みできるように、ホワイトリストの登録が必要です。
    - Office 365 [認証と ID IP V4 エンドポイント](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)に対応するすべての IP 範囲。 新しい IP が Office 365 の IP 範囲に追加された場合は、新しい NSG ルールを作成する必要があります。
-     Site Recovery サービス エンドポイント IP アドレス ([XML ファイルで入手可能](https://aka.ms/site-recovery-public-ips))。これはターゲットの場所によって異なります。 

   **ターゲットの場所** | **Site Recovery サービス IP** |  **Site Recovery 監視 IP**
   --- | --- | ---
   東アジア | 52.175.17.132</br>40.83.121.61 | 13.94.47.61
   東南アジア | 52.187.58.193</br>52.187.169.104 | 13.76.179.223
   インド中部 | 52.172.187.37</br>52.172.157.193 | 104.211.98.185
   インド南部 | 52.172.46.220</br>52.172.13.124 | 104.211.224.190
   米国中北部 | 23.96.195.247</br>23.96.217.22 | 168.62.249.226
   北ヨーロッパ | 40.69.212.238</br>13.74.36.46 | 52.169.18.8
   西ヨーロッパ | 52.166.13.64</br>52.166.6.245 | 40.68.93.145
   米国東部 | 13.82.88.226</br>40.71.38.173 | 104.45.147.24
   米国西部 | 40.83.179.48</br>13.91.45.163 | 104.40.26.199
   米国中南部 | 13.84.148.14</br>13.84.172.239 | 104.210.146.250
   米国中央部 | 40.69.144.231</br>40.69.167.116 | 52.165.34.144
   米国東部 2 | 52.184.158.163</br>52.225.216.31 | 40.79.44.59
   東日本 | 52.185.150.140</br>13.78.87.185 | 138.91.1.105
   西日本 | 52.175.146.69</br>52.175.145.200 | 138.91.17.38
   ブラジル南部 | 191.234.185.172</br>104.41.62.15 | 23.97.97.36
   オーストラリア東部 | 104.210.113.114</br>40.126.226.199 | 191.239.64.144
   オーストラリア南東部 | 13.70.159.158</br>13.73.114.68 | 191.239.160.45
   カナダ中部 | 52.228.36.192</br>52.228.39.52 | 40.85.226.62
   カナダ東部 | 52.229.125.98</br>52.229.126.170 | 40.86.225.142
   米国中西部 | 52.161.20.168</br>13.78.230.131 | 13.78.149.209
   米国西部 2 | 52.183.45.166</br>52.175.207.234 | 13.66.228.204
   英国西部 | 51.141.3.203</br>51.140.226.176 | 51.141.14.113
   英国南部 | 51.140.43.158</br>51.140.29.146 | 51.140.189.52

## <a name="example-nsg-configuration"></a>NSG 構成の例

ここでは、VM のレプリケーションが機能するように、NSG のルールを構成する方法を示します。 NSG ルールを使用して送信接続を制御している場合は、必要なすべての IP 範囲に対して "HTTPS 送信を許可" ルールを使用します。

この例では、VM ソースの場所は "米国東部" です。 レプリケーション ターゲットの場所は、米国中部です。


### <a name="example"></a>例

#### <a name="east-us"></a>米国東部

1. [米国東部 IP 範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)に対応するルールを作成します。 これは、VM からキャッシュ ストレージ アカウントにデータを書き込むことができるようにするために必要です。
2. Office 365 [認証と ID IP V4 エンドポイント](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)に対応するすべての IP 範囲に対して、ルールを作成します。
3. ターゲットの場所に対応するルールを作成します。

   **場所** | **Site Recovery サービス IP** |  **Site Recovery 監視 IP**
    --- | --- | ---
   米国中央部 | 40.69.144.231</br>40.69.167.116 | 52.165.34.144

#### <a name="central-us"></a>米国中央部

フェールオーバー後にターゲット リージョンからソース リージョンへのレプリケーションが有効になるようにするには、次のルールが必要です。

1. [米国中部 IP 範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)に対応するルールを作成します。
2. Office 365 [認証と ID IP V4 エンドポイント](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)に対応するすべての IP 範囲に対して、ルールを作成します。
3. ソースの場所に対応するルールを次のように作成します。

   **場所** | **Site Recovery サービス IP** |  **Site Recovery 監視 IP**
    --- | --- | ---
   米国東部 | 13.82.88.226</br>40.71.38.173 | 104.45.147.24


## <a name="existing-on-premises-connection"></a>既存のオンプレミス接続

オンプレミス サイトと Azure のソースの場所が ExpressRoute または VPN で接続されている場合は、次のガイドラインに従ってください。

**構成** | **詳細**
--- | ---
**強制トンネル処理** | 通常、既定のルート (0.0.0.0/0) は、送信インターネット トラフィックを強制して、オンプレミスの場所を通過します。 これはお勧めできません。 レプリケーション トラフィックと Site Recovery の通信は、Azure 内に存在するべきです。<br/><br/> 解決策として、トラフィックがオンプレミスに移動しないように、[こちらの IP 範囲](#outbound-connectivity-for-azure-site-recovery-ip-ranges)のユーザー定義ルート (UDR) を追加します。
**接続** | アプリケーションがオンプレミスのマシンに接続する必要がある場合、または VPN/ExpressRoute 経由のオンプレミスを介してオンプレミス クライアントがアプリケーションに接続する場合は、ターゲットの Azure リージョンとオンプレミス サイトの間に少なくとも 1 つの[サイト間接続](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)があることを確認します。<br/><br/> ターゲット Azure リージョンおよびオンプレミス サイト間のトラフィックの量が多い場合は、ターゲット リージョンとオンプレミスの間に別の [ExpressRoute 接続](../expressroute/expressroute-introduction.md)を作成します。<br/><br/> フェールオーバー後に VM の IP を保持する必要がある場合は、ターゲット リージョンのサイト間/ExpressRoute 接続を切断状態のままにしてください。 これにより、ソースとターゲットの IP アドレス範囲間で、確実に範囲の競合がないようにします。
**ExpressRoute** | ソース リージョンとターゲット リージョンの両方で、ExpressRoute 回線を作成します。<br/><br/> ソース ネットワークと ExpressRoute 回線の間と、ターゲット ネットワークと回線の間に、接続を作成します。<br/><br/> ソース リージョンとターゲット リージョンでは、異なる IP 範囲を使用することをお勧めします。 回線では、同じ IP 範囲を使って複数の Azure ネットワークに同時に接続することはできません。<br/><br/> 両方のリージョンに同じ IP 範囲で仮想ネットワークを作成してから、両方のリージョンに ExpressRoute 回線を作成できます。 フェールオーバーの場合は、ソース仮想ネットワークから回線を切断し、ターゲット仮想ネットワークで回線を接続します。<br/><br/> プライマリ リージョンが完全にダウンしている場合は、切断操作が失敗することがあります。 この場合、ターゲット仮想ネットワークでは、ExpressRoute 接続を取得しません。
**ExpressRoute Premium** | 回線は、同じ地理的リージョンに作成できます。<br/><br/> 異なる地理的リージョンに回線を作成するには、Azure ExpressRoute Premium が必要です。<br/><br/> Premium を使用すると、コストが増大します。 Premiun を既に使用している場合、追加のコストはかかりません。<br/><br/> 詳細については、[場所](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region)と[価格](https://azure.microsoft.com/pricing/details/expressroute/)に関する記事をご覧ください。



## <a name="next-steps"></a>次のステップ

「[手順 4: コンテナーを作成する](azure-to-azure-walkthrough-vault.md)」にページに進む


