---
title: Azure Virtual WAN パートナー向けの自動化のガイドライン | Microsoft Docs
description: Azure Virtual WAN 用にオンプレミスの VPN または SD-WAN CPE (ブランチ デバイス) を接続して構成するための自動化環境を設定します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 29fff3a6a430e3bc1a0b3a13876b55d22f7cb545
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94566471"
---
# <a name="automation-guidelines-for-virtual-wan-partners"></a>Virtual WAN パートナー向けの自動化のガイドライン

この記事では、Azure Virtual WAN のブランチ デバイス (お客様のオンプレミスの VPN デバイスまたは SDWAN CPE) を接続および構成する自動化環境を設定する方法を理解できます。 IPsec/IKEv2 または IPsec/IKEv1 経由で VPN 接続に対応できるブランチ デバイスを提供するプロバイダーの場合は、この記事が適しています。

ブランチ デバイス (お客様のオンプレミス VPN デバイスまたは SDWAN CPE) は通常、プロビジョニングするコントローラー/デバイス ダッシュボードを使用します。 多くの場合、SD-WAN ソリューションの管理者は、管理コンソールを使用して、ネットワークに接続する前にデバイスを事前プロビジョニングすることができます。 この VPN 対応デバイスは、コントローラーからコントロール プレーンのロジックを取得します。 VPN デバイスまたは SD-WAN コントローラーは、Azure API を使用して、Azure Virtual WAN への接続を自動化できます。 この種類の接続では、オンプレミスのデバイスに外部接続用パブリック IP アドレスを割り当てる必要があります。

## <a name="before-you-begin-automating"></a><a name ="before"></a>自動化を始める前に

* デバイスが IPsec IKEv1/IKEv2 をサポートしていることを確認します。 「[IPsec 接続の既定のポリシー](#default)」をご覧ください。
* Azure Virtual WAN への接続の自動化に使用する [REST API](#additional) を参照してください。
* Azure Virtual WAN のポータル エクスペリエンスをテストします。
* 次に、接続手順の自動化する部分を決定します。 少なくとも、次の部分を自動化することお勧めします。

  * アクセス制御
  * Azure Virtual WAN へのブランチ デバイス情報のアップロード
  * Azure の構成のダウンロードと、ブランチ デバイスから Azure Virtual WAN への接続の設定

### <a name="additional-information"></a><a name ="additional"></a>追加情報

* 仮想ハブの作成を自動化する [REST API](/rest/api/virtualwan/virtualhubs)
* 仮想 WAN 用の Azure VPN ゲートウェイを自動化する [REST API](/rest/api/virtualwan/vpngateways)
* VPNSite を Azure VPN Hub に接続する [REST API](/rest/api/virtualwan/vpnconnections)
* [既定の IPsec ポリシー](#default)

## <a name="customer-experience"></a><a name ="ae"></a>カスタマー エクスペリエンス

Azure Virtual WAN と共に予想される顧客エクスペリエンスを理解します。

  1. 通常、仮想 WAN のユーザーは Virtual WAN リソースを作成することによってプロセスを始めます。
  2. ユーザーは、Azure Virtual WAN にブランチの情報を書き込むため、オンプレミスのシステム (ブランチ コントローラーまたは VPN デバイスのプロビジョニング ソフトウェア) に対してサービス プリンシパル ベースのリソース グループのアクセスを設定します。
  3. ユーザーはこの時点で、UI にログインしてサービス プリンシパルの資格情報を設定する場合があります。 それが完了すると、コントローラーはお客様が提供する自動化を使用してブランチの情報をアップロードできるようになります。 Azure 側でこれを手作業で行う場合は、"サイトの作成" に相当します。
  4. サイト (デバイス ブランチ) の情報を Azure で使用できるようになった後、ユーザーはハブにサイトを接続します。 仮想ハブは、Microsoft のマネージド仮想ネットワークです。 ハブには、オンプレミス ネットワーク (vpnsite) からの接続を可能にするさまざまなサービス エンドポイントが含まれています。 ハブは、リージョン内のネットワークのコアです。 ハブは Azure リージョンごとに 1 つしか存在できず、このプロセスの間に VPN エンドポイント (vpngateway) がリージョン内に作成されます。 VPN ゲートウェイは、帯域幅や接続ニーズに基づいて適切なサイズに設定されるスケーラブルなゲートウェイです。 仮想ハブと vpngateway の作成を、ブランチ デバイス コントローラーのダッシュボードから自動化できます。
  5. 仮想ハブがサイトに関連付けられると、ユーザーが手動でダウンロードするための構成ファイルが生成されます。 この部分に自動化を使用して、ユーザー エクスペリエンスをシームレスにできます。 ユーザーが手動でダウンロードしてブランチ デバイスを構成する代わりに、自動化を設定して、UI で最小限のクリックスルー エクスペリエンスを提供することにより、共有キーの不一致、IPSec パラメーターの不一致、構成ファイルの読みやすさなどの一般的な接続の問題を軽減できます。
  6. ソリューションのこのステップの最後では、ブランチ デバイスと仮想ハブの間にシームレスなサイト対サイト接続が作成されます。 他のハブの間に追加の接続を設定することもできます。 各接続はアクティブ/アクティブ トンネルです。 ユーザーは、トンネルに対するリンクごとに異なる ISP を使用できます。
  7. CPE 管理インターフェイスで、トラブルシューティングと監視の機能を提供することを検討してください。 一般的なシナリオとしては、"CPE の問題が原因でお客様が Azure リソースにアクセスできない"、"CPE 側に IPsec パラメーターを表示する" などが挙げられます。

## <a name="automation-details"></a><a name ="understand"></a>自動化の詳細

###  <a name="access-control"></a><a name="access"></a>アクセス制御

お客様は、デバイスの UI で仮想 WAN の適切なアクセス制御を設定できる必要があります。 Azure サービス プリンシパルを使用することをお勧めします。 サービス プリンシパルに基づくアクセスは、ブランチ情報をアップロードするために適した認証をデバイス コント ローラーに提供します。 詳しくは、「[サービス プリンシパルの作成](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)」をご覧ください。 この機能は Azure Virtual WAN のオファリングには含まれませんが、Azure でアクセスを設定するときの一般的な手順を以下に示しておきます。この後、関連する詳細がデバイス管理ダッシュボードに入力されます

* オンプレミスのデバイス コントローラー用の Azure Active Directory アプリケーションを作成します。
* アプリケーション ID と認証キーを取得する
* テナント ID を取得する
* アプリケーションを "共同作成者" ロールに割り当てる

###  <a name="upload-branch-device-information"></a><a name="branch"></a>ブランチ デバイスの情報をアップロードする

ブランチ (オンプレミス サイト) の情報を Azure にアップロードするユーザー エクスペリエンスをデザインする必要があります。 VPNSite 用の [REST API](/rest/api/virtualwan/vpnsites) を使用して、Virtual WAN でサイト情報を作成できます。 すべてのブランチ SDWAN/VPN デバイスを提供したり、適切なデバイスのカスタマイズを選択したりすることができます。

### <a name="device-configuration-download-and-connectivity"></a><a name="device"></a>デバイス構成のダウンロードと接続

このステップでは、Azure の構成をダウンロードし、ブランチ デバイスから Azure Virtual WAN への接続を設定します。 この手順で、プロバイダーを使用していないお客様が、手動で Azure の構成をダウンロードしてオンプレミス SDWAN/VPN デバイスに適用します。 プロバイダーは、この手順を自動化する必要があります。 詳細については、ダウンロード [REST API](/rest/api/virtualwan/vpnsitesconfiguration/download) を参照してください。 デバイス コントローラーでは、"GetVpnConfiguration" REST API を呼び出して、Azure の構成をダウンロードできます。

**構成メモ**

  * Azure Vnet が仮想ハブに接続されている場合は、ConnectedSubnets として表示されます。
  * VPN 接続では、ルートベースの構成が使用され、IKEv1 と IKEv2 の両方のプロトコルがサポートされます。

## <a name="device-configuration-file"></a><a name="devicefile"></a>デバイス構成ファイル

デバイス構成ファイルには、オンプレミスの VPN デバイスを構成するときに使用する構成が含まれています。 このファイルを表示すると、次の情報を確認できます。

* **vpnSiteConfiguration -** このセクションは、仮想 WAN に接続するサイトとして設定されたデバイスの詳細を示します。 ブランチ デバイスの名前とパブリック IP アドレスが含まれています。
* **vpnSiteConnections -** このセクションには、次の情報が含まれています。

    * 仮想ハブ VNet の **アドレス空間**<br>例:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * ハブに接続されている VNet の **アドレス空間**<br>例:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.3.0.0/16"]
         ```
    * 仮想ハブ vpngateway の **IP アドレス**。 vpngateway にはアクティブ/アクティブ構成の 2 つのトンネルで構成される接続があるため、このファイルには両方の IP アドレスが示されています。 この例では、サイトごとに "Instance0" と "Instance1" が表示されています。<br>例:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * BGP、事前共有キーなどの、**vpngateway 接続構成の詳細**。PSK は、自動的に生成される事前共有キーです。 カスタム PSK の [概要] ページで接続をいつでも編集できます。
  
**デバイス構成ファイルの例**

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

## <a name="connectivity-details"></a><a name="default"></a>接続に関する詳細

ご使用のオンプレミス SDWAN/VPN デバイスまたは SD-WAN 構成は、Azure IPsec/IKE ポリシーで指定した次のアルゴリズムおよびパラメーターと一致している (または含んでいる) 必要があります。

* IKE 暗号化アルゴリズム
* IKE 整合性アルゴリズム
* DH グループ
* IPsec 暗号化アルゴリズム
* IPsec 整合性アルゴリズム
* PFS グループ

### <a name="default-policies-for-ipsec-connectivity"></a><a name="default"></a> IPsec 接続の既定のポリシー

[!INCLUDE [IPsec Default](../../includes/virtual-wan-ipsec-include.md)]

### <a name="custom-policies-for-ipsec-connectivity"></a><a name="custom"></a>IPsec 接続のカスタム ポリシー

[!INCLUDE [IPsec Custom](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="next-steps"></a>次のステップ

仮想 WAN の詳細については、「[About Azure Virtual WAN](virtual-wan-about.md)」(Azure Virtual WAN について) および「[Azure Virtual WAN FAQ](virtual-wan-faq.md)」(Azure Virtual WAN のよくあるご質問) を参照してください。

その他の情報については、<azurevirtualwan@microsoft.com> までメールをお送りください。 件名の “[ ]” には、お客様の会社名を入力してください。