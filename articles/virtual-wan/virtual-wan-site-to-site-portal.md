---
title: チュートリアル:Azure Virtual WAN を使用してサイト間接続を作成する
description: このチュートリアルでは、Azure Virtual WAN を使用して Azure へのサイト間 VPN 接続を作成する方法を学習します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 03/05/2021
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my local site to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: 5628c5df7ff7176896731ff8a24b4b0c990720ca
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102430670"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan"></a>チュートリアル:Azure Virtual WAN を使用してサイト間接続を作成する

このチュートリアルでは、Virtual WAN を使用して IPsec/IKE (IKEv1 と IKEv2) VPN 接続経由で Azure のリソースに接続する方法を示します。 この種類の接続では、外部接続用パブリック IP アドレスが割り当てられていてるオンプレミスの VPN デバイスが必要です。 Virtual WAN の詳細については、[Virtual WAN の概要](virtual-wan-about.md)に関するページを参照してください。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 仮想 WAN を作成する
> * ハブを作成する
> * サイトを作成する
> * サイトをハブに接続する
> * VPN サイトをハブに接続する
> * VNet をハブに接続する
> * 構成ファイルをダウンロードする
> * VPN ゲートウェイを構成する

> [!NOTE]
> 通常、多くのサイトがある場合は、[Virtual WAN パートナー](https://aka.ms/virtualwan)を利用してこの構成を作成します。 ただし、ネットワークに慣れていて、独自の VPN デバイスの構成に熟練している場合は、この構成を自分で作成することができます。
>

![Virtual WAN のダイアグラム](./media/virtual-wan-about/virtualwan.png)

## <a name="prerequisites"></a>前提条件

構成を開始する前に、以下の条件を満たしていることを確認します。

[!INCLUDE [Before you begin](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>仮想 WAN を作成する

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-hub"></a><a name="hub"></a>ハブを作成する

ハブは、サイト間、ExpressRoute、またはポイント対サイト機能のためのゲートウェイを含めることができる仮想ネットワークです。 ハブが作成されると、サイトをアタッチしていない場合でも、ハブに対して課金されます。 仮想ハブ内にサイト間 VPN ゲートウェイを作成するには 30 分かかります。

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-s2s-hub-include.md)]

## <a name="create-a-site"></a><a name="site"></a>サイトを作成する

このセクションでは、サイトを作成します。 サイトは、物理的な場所に対応します。 必要な数のサイトを作成します。 たとえば、NY、ロンドン、および LA にブランチ オフィスがある場合は、3 つの別個のサイトを作成します。 これらのサイトには、オンプレミス VPN デバイスのエンドポイントが含まれています。 仮想 WAN 内の仮想ハブあたり最大 1,000 個のサイトを作成できます。 複数のハブがある場合は、それらの各ハブあたり 1000 個作成できます。 仮想 WAN パートナーの CPE デバイスがある場合は、そのパートナーに Azure へのオートメーションについて確認してください。 通常、オートメーションとは、単純なクリック操作で、大規模なブランチ情報を Azure にエクスポートし、CPE から Azure Virtual WAN VPN ゲートウェイへの接続性を設定できることを意味します。 詳細については、[Azure から CPE パートナーへのオートメーション ガイダンス](virtual-wan-configure-automation-providers.md)を参照してください。

[!INCLUDE [Create a site](../../includes/virtual-wan-tutorial-s2s-site-include.md)]

## <a name="connect-the-vpn-site-to-the-hub"></a><a name="connectsites"></a>VPN サイトをハブに接続する

この手順では、VPN サイトをハブに接続します。

[!INCLUDE [Connect VPN sites](../../includes/virtual-wan-tutorial-s2s-connect-vpn-site-include.md)]

## <a name="connect-the-vnet-to-the-hub"></a><a name="vnet"></a>VNet をハブに接続する

[!INCLUDE [Connect](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="download-vpn-configuration"></a><a name="device"></a>VPN 構成をダウンロードする

オンプレミス VPN デバイスを構成するには、VPN デバイス構成を使用します。

1. 仮想 WAN のページで、 **[概要]** をクリックします。
2. **[Hub ->VPNSite] (ハブから VPNSite)** ページの上部にある **[Download VPN config] (VPN 構成のダウンロード)** をクリックします。Azure により、リソース グループ "microsoft-network-[location]" にストレージ アカウントが作成されます。ここで、location は WAN の場所です。 VPN デバイスに構成を適用した後は、このストレージ アカウントを削除できます。
3. ファイルの作成が完了したら、リンクをクリックしてファイルをダウンロードできます。
4. オンプレミスの VPN デバイスに構成を適用します。

### <a name="about-the-vpn-device-configuration-file"></a>VPN デバイス構成ファイルについて

デバイス構成ファイルには、オンプレミスの VPN デバイスを構成するときに使用する構成が含まれています。 このファイルを表示すると、次の情報を確認できます。

* **vpnSiteConfiguration -** このセクションは、仮想 WAN に接続するサイトとして設定されたデバイスの詳細を示します。 ブランチ デバイスの名前とパブリック IP アドレスが含まれています。
* **vpnSiteConnections -** このセクションには、次の設定に関する情報が含まれています。

    * 仮想ハブ VNet の **アドレス空間**<br>例:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * ハブに接続されている VNet の **アドレス空間**<br>例:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.3.0.0/16"]
         ```
    * 仮想ハブ vpngateway の **IP アドレス**。 vpngateway の各接続はアクティブ/アクティブ構成の 2 つのトンネルで構成されているため、このファイルには両方の IP アドレスが示されています。 この例では、サイトごとに "Instance0" と "Instance1" が表示されています。<br>例:

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
                  "10.3.0.0/16"
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
* 新しい仮想 WAN は、IKEv1 と IKEv2 の両方をサポートできます。
* Virtual WAN では、ポリシー ベースとルート ベースの両方の VPN デバイスとデバイスの手順を使用できます。

## <a name="configure-your-vpn-gateway"></a><a name="gateway-config"></a>VPN ゲートウェイを構成する

VPN ゲートウェイの設定は、 **[表示/構成]** を選択することでいつでも確認、構成することができます。

:::image type="content" source="media/virtual-wan-site-to-site-portal/view-configuration-1.png" alt-text="[VPN (サイト間)] ページのスクリーンショット。[表示/構成] アクションを指す矢印が表示されている。" lightbox="media/virtual-wan-site-to-site-portal/view-configuration-1-expand.png":::

**[VPN Gateway の編集]** ページでは、次の設定を確認できます。

* VPN Gateway のパブリック IP アドレス (Azure によって割り当てられます)
* VPN Gateway のプライベート IP アドレス (Azure によって割り当てられます)
* VPN Gateway の既定の BGP IP アドレス (Azure によって割り当てられます)
* カスタム BGP IP アドレスの構成オプション: このフィールドは APIPA (Automatic Private IP Addressing) 用に予約されています。 Azure では、169.254.21.* から 169.254.22.* の範囲の BGP IP がサポートされます。 Azure では、これらの範囲の BGP 接続が受け入れられますが、既定の BGP IP を使用して接続がダイヤルされます。

   :::image type="content" source="media/virtual-wan-site-to-site-portal/view-configuration-2.png" alt-text="構成を表示する" lightbox="media/virtual-wan-site-to-site-portal/view-configuration-2-expand.png":::

## <a name="clean-up-resources"></a><a name="cleanup"></a>リソースをクリーンアップする

作成したリソースは、不要になったら削除してください。 Virtual WAN リソースのいくつかは、依存関係に応じた特定の順序で削除する必要があります。 削除が完了するまでに 30 分程度かかる場合があります。

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>次のステップ

次に、Virtual WAN の詳細については下記を参照してください。

> [!div class="nextstepaction"]
> * [Virtual WAN の FAQ](virtual-wan-faq.md)
