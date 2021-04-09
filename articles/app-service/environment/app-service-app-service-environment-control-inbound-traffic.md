---
title: 着信トラフィックを制御する v1
description: App Service 環境への受信トラフィックを制御する方法について説明します。 このドキュメントは、レガシ v1 ASE を使用するお客様にのみ提供されます。
author: ccompy
ms.assetid: 4cc82439-8791-48a4-9485-de6d8e1d1a08
ms.topic: article
ms.date: 01/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: fe9326ea9ebd5afe981b7ba6c34b1a5d51e084b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88962062"
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>App Service 環境への受信トラフィックを制御する方法
## <a name="overview"></a>概要
App Service 環境は、Azure Resource Manager 仮想ネットワーク **または** クラシック デプロイ モデル [仮想ネットワーク][virtualnetwork]の **どちらにでも** 作成できます。  App Service 環境の作成時に、新しい仮想ネットワークと新しいサブネットを定義できます。 その代わりに、既存の仮想ネットワークと既存のサブネットに App Service 環境を作成することもできます。  2016 年 6 月の時点で、ASE はパブリック アドレス範囲または RFC1918 アドレス空間 (プライベート アドレス) のいずれかを使用する仮想ネットワークにもデプロイすることができます。  詳細については、「[App Service Environment の作成方法][HowToCreateAnAppServiceEnvironment]」を参照してください。

App Service 環境は常にサブネット内に作成します。 サブネットには、アップストリーム デバイスおよびサービスの背後にある受信トラフィックをロック ダウンするために使用できるネットワーク境界が用意されています。 このセットアップでは、特定のアップストリーム IP アドレスのみに対して、HTTP および HTTPS トラフィックの受け入れを許可します。

サブネット上の受信および送信ネットワーク トラフィックは、[ネットワーク セキュリティ グループ][NetworkSecurityGroups]を使用して制御されます。 受信トラフィックを制御するには、ネットワーク セキュリティ グループにネットワーク セキュリティ ルールを作成します。 次に、App Service 環境を含むサブネットにネットワーク セキュリティ グループを割り当てます。

ネットワーク セキュリティ グループをサブネットに割り当てると、App Service 環境におけるアプリへの受信トラフィックは、ネットワーク セキュリティ グループで定義された許可ルールと拒否ルールに基づいて許可またはブロックされます。

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>App Service 環境で使用される受信ネットワーク ポート
ネットワーク セキュリティ グループで受信ネットワーク トラフィックをロック ダウンする前に、App Service 環境で使用される必須およびオプションのネットワーク ポートのセットを把握しておいてください。  誤っていくつかのポートへのトラフィックを遮断すると、App Service 環境の機能が失われることがあります。

App Service 環境で使用されるポートの一覧を次に示します。 特に断りのない限り、すべてのポートは **TCP** です。

* 454:TLS を介した App Service Environment の管理および保守のために Azure インフラストラクチャによって使用される **必須ポート**。  このポートへのトラフィックはブロックしないでください。  このポートは常に、ASE のパブリック VIP にバインドします。
* 455:TLS を介した App Service Environment の管理および保守のために Azure インフラストラクチャによって使用される **必須ポート**。  このポートへのトラフィックはブロックしないでください。  このポートは常に、ASE のパブリック VIP にバインドします。
* 80:App Service Environment において App Service プランで実行されているアプリへの受信 HTTP トラフィック用の既定のポート。  ILB 対応の ASE では、このポートを ASE の ILB アドレスにバインドします。
* 443:App Service 環境において App Service プランで実行されているアプリへの受信 TLS トラフィック用の既定のポート。  ILB 対応の ASE では、このポートを ASE の ILB アドレスにバインドします。
* 21:FTP 用のコントロール チャネル。  FTP が使用されていない場合は、このポートを安全にブロックできます。  ILB 対応の ASE では、このポートを ASE の ILB アドレスにバインドできます。
* 990:FTPS 用のコントロール チャネル。  FTPS が使用されていない場合は、このポートを安全にブロックできます。  ILB 対応の ASE では、このポートを ASE の ILB アドレスにバインドできます。
* 10001 ～ 10020:FTP 用のデータ チャネル。  コントロール チャネルと同様、FTP が使用されていない場合は、これらのポートを安全にブロックできます。  ILB 対応の ASE では、このポートを ASE の ILB アドレスにバインドできます。
* 4016:Visual Studio 2012 でのリモート デバッグに使用されます。  機能が使用されていない場合は、このポートを安全にブロックできます。  ILB 対応の ASE では、このポートを ASE の ILB アドレスにバインドします。
* 4018:Visual Studio 2013 でのリモート デバッグに使用されます。  機能が使用されていない場合は、このポートを安全にブロックできます。  ILB 対応の ASE では、このポートを ASE の ILB アドレスにバインドします。
* 4020:Visual Studio 2015 でのリモート デバッグに使用されます。  機能が使用されていない場合は、このポートを安全にブロックできます。  ILB 対応の ASE では、このポートを ASE の ILB アドレスにバインドします。

## <a name="outbound-connectivity-and-dns-requirements"></a>発信接続と DNS の要件
App Service 環境が正常に機能するためには、さまざまなエンドポイントへの発信アクセスも必要です。 [ExpressRoute を使用した環境のネットワーク構成](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) の記事の「必要なネットワーク接続」というセクションに、App Service Environment で使用されるすべての外部エンドポイントが掲載されています。

App Service 環境では、仮想ネットワーク用に構成された有効な DNS インフラストラクチャが必要です。  App Service 環境の作成後に DNS の構成が変更された場合、開発者は App Service 環境に、新しい DNS 構成の選択を強制できます。  **[再起動]** アイコンを使用してローリングによる環境の再起動をトリガーすると、環境によって新しい DNS 構成が選択されます。 ( **[再起動]** アイコンは、[Azure portal][NewPortal] の [App Service Environment management]\(App Service Environment の管理\) ブレードの上部にあります。)

また、App Service 環境を作成する前に、VNet 上のカスタム DNS サーバーを予め設定しておくことをお勧めします。  App Service 環境の作成中に仮想ネットワークの DNS 構成が変更されると、App Service 環境の作成プロセスは失敗します。  同様に、VPN ゲートウェイのもう一方の端に到達不能または使用できないカスタム DNS サーバーがある場合も、App Service 環境の作成プロセスは失敗します。

## <a name="creating-a-network-security-group"></a>ネットワーク セキュリティ グループの作成
ネットワーク セキュリティ グループの動作の詳細については、次の[情報][NetworkSecurityGroups]を参照してください。  次の Azure サービス管理の例では、ネットワーク セキュリティ グループの要点に触れています。 この例では、App Service 環境を含むサブネットにネットワーク セキュリティ グループを構成して適用します。

**注:** ネットワーク セキュリティ グループは、[Azure portal](https://portal.azure.com) を利用して視覚的に構成することもできますし、Azure PowerShell を利用して構成することもできます。

ネットワーク セキュリティ グループは、最初に、サブスクリプションに関連付けられたスタンドアロン エンティティとして作成されます。 ネットワーク セキュリティ グループは Azure リージョン内に作成されるため、ネットワーク セキュリティ グループは App Service 環境と同じリージョンに作成してください。

以下のコマンドは、ネットワーク セキュリティ グループ作成の例を示しています。

```azurepowershell-interactive
New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"
```

ネットワーク セキュリティ グループが作成されると、それに 1 つ以上のネットワーク セキュリティ ルールが追加されます。  一連のルールは時間の経過と共に変わる可能性があるため、ルールの優先順位付けに使用する番号付けスキームで間隔をあけることをお勧めします。 こうすることで、時間の経過に伴う追加のルールを簡単に挿入できるようになります。

次の例では、App Service 環境の管理および保守のために Azure インフラストラクチャが必要とする管理ポートへのアクセスを、ルールによって明示的に付与しています。  すべての管理トラフィックは TLS 経由で送信され、クライアント証明書によってセキュリティで保護されます。 ポートが開かれている場合でも、Azure の管理インフラストラクチャ以外のエンティティからはアクセスできません。

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
```

ポート 80 と 443 へのアクセスをロック ダウンして、App Service 環境をアップストリーム デバイスまたはサービスの背後に "隠す" 場合は、アップストリーム IP アドレスを覚えておいてください。  たとえば、Web アプリケーション ファイアウォール (WAF) を使用している場合、WAF には独自の IP アドレス (1 つまたは複数) が割り当てられます。 WAF では、ダウンストリームの App Service 環境にトラフィックをプロキシするときにそれらが使用されます。  この IP アドレスは、ネットワーク セキュリティ ルールの *SourceAddressPrefix* パラメーターで使用する必要があります。

次の例では、特定のアップストリーム IP アドレスからの受信トラフィックが明示的に許可されています。  アドレス *1.2.3.4* は、アップストリーム WAF の IP アドレスのプレースホルダーとして使用されています。  アップストリーム デバイスまたはサービスで使用されているアドレスと一致するように値を変更します。

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
```

FTP サポートが必要な場合は、次のルールをテンプレートとして使用して、FTP 制御ポートとデータ チャネル ポートへのアクセス権を付与してください。  FTP はステートフルなプロトコルであるため、従来の HTTP/HTTPS ファイアウォールまたはプロキシ デバイス経由で FTP トラフィックをルーティングできない場合があります。  この場合は *SourceAddressPrefix* を、FTP クライアントが実行されている開発者コンピューターまたはデプロイメント コンピューターの IP アドレス範囲などの別の値に設定する必要があります。 

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP
```

(**注:** データ チャネル ポート範囲は、プレビュー期間中に変更される場合があります)。

Visual Studio でのリモート デバッグが使用されている場合、次のルールでは、アクセス権を付与する方法を示しています。  Visual Studio では、バージョンごとにリモート デバッグに使用するポートが異なるため、サポートされているバージョンごとに個別のルールがあります。  FTP アクセスと同様に、リモート デバッグ トラフィックは、従来の WAF またはプロキシ デバイス経由で適切にフローされない場合があります。  代わりに、 *SourceAddressPrefix* を、Visual Studio が実行されている開発者コンピューターの IP アドレス範囲に設定できます。

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP
```

## <a name="assigning-a-network-security-group-to-a-subnet"></a>サブネットへのネットワーク セキュリティ グループの割り当て
ネットワーク セキュリティ グループには、すべての外部トラフィックへのアクセスを拒否する既定のセキュリティ ルールがあります。 上記のネットワーク セキュリティ ルールとこのルールを組み合わせると、*許可* アクションに関連付けられた送信元アドレス範囲からのトラフィックのみが、App Service 環境で実行中のアプリにトラフィックを送信できるようになります。

ネットワーク セキュリティ グループは、セキュリティ ルールが設定された後に、App Service 環境を含むサブネットに割り当てられる必要があります。  割り当てコマンドは、App Service 環境が存在する仮想ネットワークの名前と、App Service 環境が作成されたサブネットの名前の 2 つを参照します。  

次の例は、ネットワーク セキュリティ グループがサブネットと仮想ネットワークに割り当てられることを示しています。

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'
```

割り当ては実行時間の長い操作であり、完了するまでに数分かかる場合があります。 ネットワーク セキュリティ グループの割り当てが成功すると、*許可* ルールに一致する受信トラフィックのみが App Service 環境内のアプリに正常に到達します。

完全を期すため、次の例では、サブネットからネットワーク セキュリティ グループを削除し、関連付けを解除する方法を示します。

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'
```

## <a name="special-considerations-for-explicit-ip-ssl"></a>明示的な IP SSL に関する特別な考慮事項
アプリが、App Service 環境の既定の IP アドレスを使わず、明示的な IP SSL アドレスで構成されている場合 (パブリック VIP がある ASE に "*のみ*" あてはまります)、HTTP と HTTPS の両方のトラフィックは、ポート 80 と 443 以外のポートを経由してサブネットにフローされます。

各 IP SSL アドレスによって使用されるポートの個々のペアを見つけるには、ポータルにアクセスし、[App Service Environment's details UX]\(App Service Environment の詳細 UX\) ブレードを表示します。  **[すべての設定]**  >  **[IP アドレス]** の順に選択します。  **[IP アドレス]** ブレードには、App Service 環境に対して明示的に構成されたすべての IP SSL アドレスのテーブルが表示されます。 このブレードには、各 IP SSL アドレスに関連付けられた HTTP および HTTPS トラフィックをルーティングするために使用される特殊なポート ペアも表示されます。  ネットワーク セキュリティ グループのルールを構成する際には、DestinationPortRange パラメーターにこのポート ペアを使用します。

ASE でアプリが IP SSL を使用するように構成されている場合、外部の顧客には特殊なポート ペアのマッピングは表示されず、確認する必要もありません。  アプリへのトラフィックは、構成済みの IP SSL アドレスに通常流れていきます。  特殊なポート ペアへの変換は、ASE を含むサブネットへのトラフィックのルーティングの最終段階において内部で自動的に実行されます。 

## <a name="getting-started"></a>作業の開始
App Service Environment の使用を開始するには、「[App Service Environment の概要][IntroToAppServiceEnvironment]」を参照してください。

詳細については、「[App Service 環境からバックエンド リソースに安全に接続する][SecurelyConnecttoBackend]」を参照してください。

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->