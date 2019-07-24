---
title: Azure Virtual WAN を使用して Azure へのサイト間接続を作成する | Microsoft Docs
description: このチュートリアルでは、Azure Virtual WAN を使用して Azure へのサイト間 VPN 接続を作成する方法を学習します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 02/26/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my local site to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: 4b44eec5557d2083c38fe2714d93800f79b21b0f
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58338447"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan"></a>チュートリアル:Azure Virtual WAN を使用してサイト間接続を作成する

このチュートリアルでは、Virtual WAN を使用して IPsec/IKE (IKEv1 と IKEv2) VPN 接続経由で Azure のリソースに接続する方法を示します。 この種類の接続では、外部接続用パブリック IP アドレスが割り当てられていてるオンプレミスの VPN デバイスが必要です。 Virtual WAN の詳細については、[Virtual WAN の概要](virtual-wan-about.md)に関するページを参照してください

> [!NOTE]
> 通常、多くのサイトがある場合は、[Virtual WAN パートナー](https://aka.ms/virtualwan)を利用してこの構成を作成します。 ただし、ネットワークに慣れていて、独自の VPN デバイスの構成に熟練している場合は、この構成を自分で作成することができます。
>

![Virtual WAN のダイアグラム](./media/virtual-wan-about/virtualwan.png)

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * WAN を作成する
> * サイトを作成する
> * ハブを作成する
> * ハブをサイトに接続する
> * VNet をハブに接続する
> * VPN デバイス構成をダウンロードして適用する
> * 仮想 WAN を表示する
> * リソースの正常性を表示する
> * 接続を監視する

## <a name="before-you-begin"></a>開始する前に

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="vnet"></a>1.仮想ネットワークの作成

[!INCLUDE [Create a virtual network](../../includes/virtual-wan-tutorial-vnet-include.md)]

## <a name="openvwan"></a>2.仮想 WAN を作成する

ブラウザーから [Azure ポータル](https://aka.ms/azurevirtualwanpreviewfeatures) に移動し、Azure アカウントでサインインします。

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="site"></a>3.サイトを作成する

物理的な場所に合わせて必要な数のサイトを作成します。 たとえば、NY、ロンドン、および LA にブランチ オフィスがある場合は、3 つの別個のサイトを作成します。 これらのサイトには、オンプレミス VPN デバイスのエンドポイントが含まれています。 現時点では、サイトに対してプライベート アドレス空間を 1 つだけ指定できます。

1. 作成した WAN をクリックします。 [WAN] ページの **[WAN Architecture]\(WAN アーキテクチャ\)** で **[VPN サイト]** をクリックして VPN サイト ページを開きます。
2. **[VPN サイト]** ページで **[+ サイトの作成]** をクリックします。
3. **[サイトの作成]** ページで、次のフィールドに入力します。

   * **[名前]** - これは、オンプレミスのサイトの呼称です。
   * **[パブリック IP アドレス]** - これは、オンプレミスのサイトにある VPN デバイスのパブリック IP アドレスです。
   * **[プライベート アドレス空間]** - これは、オンプレミスのサイトにある IP アドレス空間です。 このアドレス空間宛てのトラフィックは、ローカル サイトにルーティングされます。
   * **[サブスクリプション]** - サブスクリプションを確認します。
   * **[リソース グループ]** - 使用するリソース グループ。
   * **場所**。
4. **[詳細設定の表示]** をクリックして追加の設定を表示します。 **[BGP]** を選択して BGP を有効にすることができます。Azure のこのサイト用に作成されたすべての接続でこの機能が有効になります。 **[デバイス情報]** (省略可能なフィールド) に入力することもできます。 これにより、Azure チームがお客様の環境をよりよく理解し、将来の最適化の可能性を追加したり、トラブルシューティングに役立てることができます。
5. **[Confirm]\(確認\)** をクリックします。
6. **[確認]** をクリックすると、VPN サイト ページに状態が表示されます。 このサイトは "**プロビジョニング中**" から "**プロビジョニング済み**" に切り替わります。

## <a name="hub"></a>4.ハブを作成する

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="associate"></a>5.サイトをハブに関連付ける

ハブは、通常、VNet が存在するリージョンと同じリージョンにあるサイトに関連付ける必要があります。

1. **[VPN サイト]** ページで、ハブに関連付ける 1 つまたは複数のサイトを選択し、**[+ 新しいハブの関連付け]** をクリックします。
2. **[Associate sites with one or more hubs]\(サイトを 1 つまたは複数のハブと関連付ける\)** ページで、ドロップダウンからハブを選択します。 サイトを追加のハブと関連付けるには、**[関連付けの追加]** をクリックします。
3. ここで特定の **PSK** を追加するか、既定値を使用することもできます。
4. **[Confirm]\(確認\)** をクリックします。
5. **[VPN サイト]** ページで接続状態を表示できます。

## <a name="vnet"></a>6.VNet をハブに接続する

この手順では、ハブと VNet の間にピアリング接続を作成します。 接続する VNet ごとにこれらの手順を繰り返します。

1. 仮想 WAN のページで、**[仮想ネットワーク接続]** をクリックします。
2. 仮想ネットワーク接続のページで、**[+ 接続の追加]** をクリックします。
3. **[接続の追加]** ページで、次のフィールドに入力します。

    * **[接続名]** - 接続に名前を付けます。
    * **[ハブ]** - この接続に関連付けるハブを選択します。
    * **[サブスクリプション]** - サブスクリプションを確認します。
    * **[仮想ネットワーク]** - このハブに接続する仮想ネットワークを選択します。 仮想ネットワークに既存の仮想ネットワーク ゲートウェイを設定することはできません。
4. **[OK]** をクリックして、ピアリング接続を作成します。

## <a name="device"></a>7.VPN 構成をダウンロードする

オンプレミス VPN デバイスを構成するには、VPN デバイス構成を使用します。

1. 仮想 WAN のページで、**[概要]** をクリックします。
2. [概要] ページの上部にある **[VPN 構成をダウンロードする]** をクリックします。 Azure により、リソース グループ "microsoft-network-[location]" にストレージ アカウントが作成されます。ここで、location は WAN の場所です。 VPN デバイスに構成を適用した後は、このストレージ アカウントを削除できます。
3. ファイルの作成が完了したら、リンクをクリックしてファイルをダウンロードできます。
4. VPN デバイスに構成を適用します。

### <a name="understanding-the-vpn-device-configuration-file"></a>VPN デバイス構成ファイルについて

デバイス構成ファイルには、オンプレミスの VPN デバイスを構成するときに使用する構成が含まれています。 このファイルを表示すると、次の情報を確認できます。

* **vpnSiteConfiguration -** このセクションは、仮想 WAN に接続するサイトとして設定されたデバイスの詳細を示します。 ブランチ デバイスの名前とパブリック IP アドレスが含まれています。
* **vpnSiteConnections -** このセクションには、次の情報が含まれています。

    * 仮想ハブ VNet の**アドレス空間**<br>例:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * ハブに接続されている VNet の**アドレス空間**<br>例:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.30.0.0/16"]
         ```
    * 仮想ハブ vpngateway の **IP アドレス**。 vpngateway にはアクティブ/アクティブ構成の 2 つのトンネルで構成される接続があるため、このファイルには両方の IP アドレスが示されています。 この例では、サイトごとに "Instance0" と "Instance1" が表示されています。<br>例:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * BGP、事前共有キーなどの、**vpngateway 接続構成の詳細**。PSK は、自動的に生成される事前共有キーです。 カスタム PSK の [概要] ページで接続をいつでも編集できます。
  
### <a name="example-device-configuration-file"></a>デバイス構成ファイルの例

  ```
  { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"r403583d-9c82-4cb8-8570-1cbbcd9983b5"
      },
      "vpnSiteConfiguration":{ 
         "Name":"testsite1",
         "IPAddress":"73.239.3.208"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe",
               "ConnectedSubnets":[ 
                  "10.2.0.0/16",
                  "10.30.0.0/16"
               ]
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.186",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"bkOWe5dPPqkx0DfFE3tyuP7y3oYqAEbI",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"1f33f891-e1ab-42b8-8d8c-c024d337bcac"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite2",
         "IPAddress":"66.193.205.122"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"XzODPyAYQqFs4ai9WzrJour0qLzeg7Qg",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"cd1e4a23-96bd-43a9-93b5-b51c2a945c7"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite3",
         "IPAddress":"182.71.123.228"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"YLkSdSYd4wjjEThR3aIxaXaqNdxUwSo9",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   }
  ```

### <a name="configuring-your-vpn-device"></a>VPN デバイスの構成

>[!NOTE]
> Virtual WAN パートナー ソリューションを使用して作業している場合、VPN デバイスの構成が自動的に行われます。 デバイス コントローラーは Azure から構成ファイルを取得し、デバイスに適用して Azure への接続を設定します。 これは、VPN デバイスを手動で構成する方法を知る必要がないことを意味します。
>

デバイスを構成する手順が必要な場合は、[VPN デバイス構成スクリプトのページ](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts)の手順を使用できます。このとき、次の点に注意してください。

* VPN デバイスのページに書かれている手順は Virtual WAN 用ではありませんが、構成ファイルの Virtual WAN の値を使用して VPN デバイスを手動で構成することができます。 
* VPN Gateway 用のダウンロード可能なデバイス構成スクリプトは、構成が異なるため、Virtual WAN では機能しません。
* 新しい Virtual WAN は、IKEv1 と IKEv2 の両方をサポートできます。
* Virtual WAN では、ルートベースの VPN デバイスとデバイスの手順のみを使用できます。

## <a name="viewwan"></a>8.仮想 WAN を表示する

1. 仮想 WAN に移動します。
2. [概要] ページのマップ上の各ポイントは、ハブを表します。 任意のポイントにカーソルを置くと、ハブの正常性の概要が表示されます。
3. ハブと接続のセクションでは、ハブの状態、サイト、リージョン、VPN 接続の状態、および入出力バイト数を表示できます。

## <a name="viewhealth"></a>9.リソースの正常性を表示する

1. WAN に移動します。
2. WAN のページの **[サポート + トラブルシューティング]** セクションで、**[正常性]** をクリックしてリソースを表示します。

## <a name="connectmon"></a>10.接続を監視する

Azure VM とリモート サイト間の通信を監視するための接続を作成します。 接続モニターを設定する方法については、[ネットワーク通信の監視](~/articles/network-watcher/connection-monitor.md)に関するページを参照してください。 ソース フィールドは Azure の VM IP で、宛先 IP はサイト IP です。

## <a name="cleanup"></a>11.リソースのクリーンアップ

これらのリソースが不要になったら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) を使用して、リソース グループとその中のすべてのリソースを削除できます。 "myResourceGroup" をリソース グループの名前に置き換えて、次の PowerShell コマンドを実行します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * WAN を作成する
> * サイトを作成する
> * ハブを作成する
> * ハブをサイトに接続する
> * VNet をハブに接続する
> * VPN デバイス構成をダウンロードして適用する
> * 仮想 WAN を表示する
> * リソースの正常性を表示する
> * 接続を監視する

Virtual WAN の詳細については、[Virtual WAN の概要](virtual-wan-about.md)に関するページを参照してください。
