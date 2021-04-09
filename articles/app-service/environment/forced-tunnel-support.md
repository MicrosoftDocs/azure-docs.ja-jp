---
title: 強制トンネリングについて
description: 仮想ネットワークでアウトバウンド トラフィックが強制トンネリングされている場合に App Service Environment を正しく機能させるための設定方法について説明します。
author: ccompy
ms.assetid: 384cf393-5c63-4ffb-9eb2-bfd990bc7af1
ms.topic: quickstart
ms.date: 05/29/2018
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: 95a4d00a27a0da363561f469b4c5e9e2ad16463c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97510500"
---
# <a name="configure-your-app-service-environment-with-forced-tunneling"></a>強制トンネリングを使用した App Service Environment の構成

App Service Environment (ASE) は、ユーザーの Azure 仮想ネットワーク内に Azure App Service をデプロイしたものです。 多くのユーザーは、VPN または Azure ExpressRoute 接続を使用して、Azure 仮想ネットワークをオンプレミス ネットワークの拡張網として構成しています。 このとき、インターネットに向かうトラフィックを VPN や仮想アプライアンスにリダイレクトすることを強制トンネリングといいます。 アウトバウンド ネットワーク トラフィックの調査と監査には、よく仮想アプライアンスが使用されます。 

ASE には外部の依存関係が多数あります。これらは、[App Service Environment のネットワーク アーキテクチャ][network]に関するドキュメントで説明されています。 通常、外に向かう ASE の依存関係トラフィックはすべて、その ASE に対してプロビジョニングされた VIP を経由する必要があります。 ASE に向かうトラフィックまたは ASE から出て行くトラフィックのルーティングを変更する場合、以下の情報に従わないと、ASE が正しく機能しなくなります。

Azure 仮想ネットワークでは、最長プレフィックス一致 (LPM) に基づいてルーティングが実行されます。 同じ LPM マッチの複数のルートが存在する場合は、そのルートが検出された経緯に応じて次の順序でルートが選択されます。

* ユーザー定義のルート (UDR)
* BGP のルート (ExpressRoute を使用している場合)
* システム ルート

仮想ネットワークにおけるルーティングの詳細については、[ユーザー定義ルートと IP 転送][routes]に関するページをご覧ください。 

インターネットに直接向かう以外の経路で ASE の送信トラフィックをルーティングする場合、次の選択肢があります。

* ASE で直接インターネットにアクセスできるようにする
* BGP ルートを無視するように ASE サブネットを構成する
* Azure SQL と Azure Storage へのサービス エンドポイントを使用するように ASE のサブネットを構成する
* ASE の Azure SQL のファイアウォールに自分の IP を追加する

## <a name="enable-your-app-service-environment-to-have-direct-internet-access"></a>インターネットに直接アクセスするように App Service Environment を設定する

Azure Virtual Network が ExpressRoute を使って構成されていても ASE からのトラフィックが直接インターネットに向かえるようにするには、次の作業を行ってください。

* 0\.0.0.0/0 をアドバタイズするように ExpressRoute を構成します。 これにより、すべての送信トラフィックが既定でオンプレミスにルーティングされます。
* アドレス プレフィックスが 0.0.0.0/0 で、次ホップの種類がインターネットであるような UDR を作成し、それを ASE のサブネットに適用します。

これらの 2 つの変更を行った場合、App Service Environment サブネットから発信されたインターネット宛てのトラフィックは ExpressRoute 接続を強制的に経由しません。

既にネットワークのトラフィックがオンプレミスにルーティングされている場合は、ASE をデプロイする前に、ASE をホストするサブネットを作成して UDR を構成する必要があります。  

> [!IMPORTANT]
> UDR に定義されているルートは、ExpressRoute 構成でアドバタイズされたどのルートよりも優先されるように、詳細にする必要があります。 前の例では、0.0.0.0/0 という広いアドレス範囲を使用しています。 これは、より具体的なアドレス範囲を使用するルート アドバタイズによって誤ってオーバーライドされる可能性があります。
>
> App Service Environment は、パブリック ピアリング パスからプライベート ピアリング パスにルートをクロスアドバタイズした ExpressRoute 構成ではサポートされません。 パブリック ピアリングが構成された ExpressRoute 構成は、Microsoft からルート アドバタイズを受信します。 これらのアドバタイズには、Microsoft Azure の一連の広いアドレス範囲が含まれています。 これらのアドレス範囲がプライベートピアリング パスでクロスアドバタイズされた場合、App Service Environment のサブネットからの送信ネットワーク パケットはすべて、顧客のオンプレミスのネットワーク インフラストラクチャにルーティングされます。 既定では、このネットワーク フローは App Service Environment でサポートされていません。 この問題の 1 つの解決策として、パブリックピアリング パスからプライベートピアリング パスへのクロスアドバタイズ ルートの停止があります。 もう 1 つの解決策は、強制トンネリング構成で機能するように App Service Environment を設定することです。

![直接インターネット アクセス][1]

## <a name="configure-your-ase-subnet-to-ignore-bgp-routes"></a>BGP ルートを無視するように ASE サブネットを構成する ## 

すべての BGP ルートを無視するように、ASE サブネットを構成することができます。  BGP ルートを無視するように構成すると、ASE は何の問題もなくその依存関係にアクセスすることができます。  ただし、アプリがオンプレミスのリソースにアクセスできるようにするには、UDR を作成する必要があります。

BGP ルートを無視するように ASE サブネットを構成するには

* まだない場合は、UDR を作成して ASE サブネットに割り当てます。
* Azure portal で、ASE サブネットに割り当てられているルート テーブルの UI を開きます。  [構成] を選択します。  [仮想ネットワーク ゲートウェイのルート伝達] を [無効] に設定します。  [保存] をクリックします。 オフにすることについては、「[ルート テーブルの作成][routetable]」をご覧ください。

すべての BGP ルートを無視するように ASE サブネットを構成すると、以後、アプリはオンプレミスにアクセスできなくなります。 アプリからオンプレミスのリソースにアクセスできるようにするには、ASE サブネットに割り当てられている UDR を編集し、オンプレミスのアドレス範囲へのルートを追加します。 次のホップの種類は、仮想ネットワーク ゲートウェイに設定する必要があります。 


## <a name="configure-your-ase-with-service-endpoints"></a>サービス エンドポイントを使って ASE を構成する ##

ASE から外に出て行く送信トラフィックを、Azure SQL と Azure Storage に向かうトラフィックを除いてすべてルーティングするには、次の手順を実行します。

1. ルート テーブルを作成して ASE サブネットに割り当てます。 「[App Service Environment の管理アドレス][management]」で、ご利用のリージョンに該当するアドレスを確認してください。 それらのアドレスについて、インターネットを次ホップとするルートを作成します。 これらのルートが必要な理由は、App Service Environment に向かうインバウンド方向の管理トラフィックは、その送信先となった同じアドレスから応答する必要があるためです。   

2. Azure SQL/Azure Storage と ASE サブネットとの間でサービス エンドポイントを有効にします。  この手順を完了すると、強制トンネリングを使用して VNet を構成できます。

テンプレートを使用して ASE をデプロイする方法の詳細については、[テンプレートを使用した App Service Environment の作成][template]に関するページを参照してください。

サービス エンドポイントを設けることで、マルチテナント サービスへのアクセスを、一連の Azure 仮想ネットワークとサブネットに制限することができます。 サービス エンドポイントについて詳しくは、「[仮想ネットワーク サービス エンドポイント][serviceendpoints]」のドキュメントをご覧ください。 

リソースに対するサービス エンドポイントを有効にすると、他のどのルートよりも高い優先度でルートが作成されます。 トンネリングが強制された ASE との間でサービス エンドポイントを使用した場合、Azure SQL と Azure Storage の管理トラフィックについては、強制的にトンネリングされることはありません。 その他の ASE の依存関係トラフィックは強制トンネリングされ、決して失われることがありません。もし仮に依存関係トラフィックが失われたとすれば、ASE が適切に機能しなくなってしまいます。

サブネットに対し、Azure SQL インスタンスとのサービス エンドポイントを有効にすると、そのサブネットから接続されるすべての Azure SQL インスタンスについてサービス エンドポイントが有効になります。 同じサブネットから複数の Azure SQL インスタンスにアクセスする場合に、サービス エンドポイントの有効と無効を Azure SQL インスタンスごとに分けることはできません。  Azure Storage の動作は、Azure SQL のそれとは異なります。  Azure Storage とのサービス エンドポイントを有効にした場合、そのリソースには、自分のサブネットからしかアクセスできないようロックされますが、他の Azure Storage アカウントには、サービス エンドポイントが有効になっていなくても引き続きアクセスすることができます。  

ネットワーク フィルター アプライアンスを使って強制トンネリングを構成する場合、ASE には Azure SQL と Azure Storage だけでなく他の依存関係が存在することに注意してください。 これらの依存関係へのトラフィックがブロックされていると ASE は正しく機能しません。

![サービス エンドポイントを使った強制トンネリング][2]

## <a name="add-your-own-ips-to-the-ase-azure-sql-firewall"></a>ASE の Azure SQL のファイアウォールに自分の IP を追加する ##

ASE から外に出て行く送信トラフィックを、Azure Storage に向かうトラフィックを除いてすべてトンネリングするには、次の手順を実行します。

1. ルート テーブルを作成して ASE サブネットに割り当てます。 「[App Service Environment の管理アドレス][management]」で、ご利用のリージョンに該当するアドレスを確認してください。 それらのアドレスについて、インターネットを次ホップとするルートを作成します。 これらのルートが必要な理由は、App Service Environment に向かうインバウンド方向の管理トラフィックは、その送信先となった同じアドレスから応答する必要があるためです。 

2. Azure Storage と ASE サブネットとの間でサービス エンドポイントを有効にします。

3. ご利用の App Service Environment からインターネットに向かうすべての送信トラフィックに使用されるアドレスを取得します。 トラフィックをオンプレミスでルーティングする場合、それらのアドレスはご利用の NAT またはゲートウェイの IP となります。 NVA を経由するよう App Service Environment の送信トラフィックをルーティングする場合は、その NVA のパブリック IP がエグレス アドレスになります。

4. _既存の App Service Environment でエグレス アドレスを設定するには:_ resources.azure.com にアクセスして Subscription/\<subscription id>/resourceGroups/\<ase resource group>/providers/Microsoft.Web/hostingEnvironments/\<ase name> に移動します。 すると、ご利用の App Service Environment を表す JSON を確認できます。 一番上に **読み取り/書き込み** の表示があることを確認してください。 **[編集]** を選択します。 一番下までスクロールします。 **userWhitelistedIpRanges** の値を **null** から次のような値に変更します。 エグレス アドレス範囲として設定するアドレスを使用してください。 

    ```json
    "userWhitelistedIpRanges": ["11.22.33.44/32", "55.66.77.0/24"]
    ```

   一番上にある **[PUT]** を選択します。 このオプションは、App Service Environment 上のスケール操作をトリガーし、ファイアウォールを調整します。

_エグレス アドレスを持つ ASE を作成するには_:[テンプレートを使用した App Service Environment の作成][template]に関するページの説明に従い、適切なテンプレートを入手してください。  azuredeploy.json ファイルの ("properties" ブロックではなく) "resources" セクションを編集します。実際の値に合わせて、**userWhitelistedIpRanges** の行を追加してください。

```json
"resources": [
    {
        "apiVersion": "2015-08-01",
        "type": "Microsoft.Web/hostingEnvironments",
        "name": "[parameters('aseName')]",
        "kind": "ASEV2",
        "location": "[parameters('aseLocation')]",
        "properties": {
            "name": "[parameters('aseName')]",
            "location": "[parameters('aseLocation')]",
            "ipSslAddressCount": 0,
            "internalLoadBalancingMode": "[parameters('internalLoadBalancingMode')]",
            "dnsSuffix" : "[parameters('dnsSuffix')]",
            "virtualNetwork": {
                "Id": "[parameters('existingVnetResourceId')]",
                "Subnet": "[parameters('subnetName')]"
            },
            "userWhitelistedIpRanges":  ["11.22.33.44/32", "55.66.77.0/30"]
        }
    }
]
```

このように変更することで、Azure Storage には ASE から直接トラフィックが送信され、また、Azure SQL には、ASE の VIP とは異なるアドレスからアクセスできるようになります。

   ![SQL 許可リストを使った強制トンネリング][3]

## <a name="preventing-issues"></a>問題の回避 ##

ASE とその依存関係との間で通信ができなくなった場合、ASE が正常に機能しなくなります。  異常な状態があまりに長く続いた場合、ASE は停止状態に移行します。 ASE を停止解除するには、ASE ポータルから該当する手順に従ってください。

単に通信できなくなるだけでなく、待ち時間が極端に長くなることによる悪影響が ASE に生じることもあります。 待ち時間が極端に長くなる症状は、ASE とオンプレミス ネットワークとの間の距離が離れすぎている場合に生じることがあります。  "距離が離れすぎている" とは、たとえばオンプレミス ネットワークに到達するために海や大陸を横断して通信しなければならないケースが該当します。 イントラネットが混雑していたり送信帯域幅に制約があったりすることが原因で生じる待ち時間もあります。


<!--IMAGES-->
[1]: ./media/forced-tunnel-support/asedependencies.png
[2]: ./media/forced-tunnel-support/forcedtunnelserviceendpoint.png
[3]: ./media/forced-tunnel-support/forcedtunnelexceptstorage.png

<!--Links-->
[management]: ./management-addresses.md
[network]: ./network-info.md
[routes]: ../../virtual-network/virtual-networks-udr-overview.md
[template]: ./create-from-template.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[routetable]: ../../virtual-network/manage-route-table.md#create-a-route-table
