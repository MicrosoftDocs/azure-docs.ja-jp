---
title: App Service 環境への受信トラフィックを制御する - Azure
description: App Service 環境への受信トラフィックを制御するネットワーク セキュリティ ルールを構成する方法について説明します。
services: app-service
documentationcenter: ''
author: ccompy
manager: erikre
editor: ''
ms.assetid: 4cc82439-8791-48a4-9485-de6d8e1d1a08
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 84575dcb67845a074ce19cf9d819e1dda3f90e20
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53271978"
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>App Service 環境への受信トラフィックを制御する方法
## <a name="overview"></a>概要
App Service 環境は、Azure Resource Manager 仮想ネットワーク**と**クラシック デプロイ モデル[仮想ネットワーク][virtualnetwork]の**どちらにでも**作成できます。  App Service 環境の作成時に、新しい仮想ネットワークと新しいサブネットを定義できます。  または、既存の仮想ネットワークと既存のサブネットに App Service 環境を作成することもできます。  また、2016 年 6 月に行われた変更で、パブリック アドレス範囲と RFC1918 アドレス空間 (つまりプライベート アドレス) のどちらかを使用した仮想ネットワークに ASE をデプロイできるようになりました。  App Service 環境の作成方法の詳細については、「[App Service 環境の作成方法][HowToCreateAnAppServiceEnvironment]」を参照してください。

App Service 環境は常にサブネット内で作成する必要があります。これは、HTTP トラフィックと HTTPS トラフィックが特定のアップストリーム IP アドレスのみから受け取られるように、アップストリーム デバイスおよびサービスの背後で受信トラフィックをロックダウンするために使用できるネットワーク境界がサブネットによって提供されるためです。

サブネット上の受信および送信ネットワーク トラフィックは、[ネットワーク セキュリティ グループ][NetworkSecurityGroups]を使用して制御されます。 受信トラフィックを制御するには、ネットワーク セキュリティ グループにネットワーク セキュリティ ルールを作成してから、そのネットワーク セキュリティ グループを App Service 環境が含まれるサブネットに割り当てる必要があります。

ネットワーク セキュリティ グループがサブネットに割り当てられると、App Service 環境におけるアプリへの受信トラフィックは、ネットワーク セキュリティ グループで定義された許可ルールと拒否ルールに基づいて許可またはブロックされます。

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>App Service 環境で使用される受信ネットワーク ポート
ネットワーク セキュリティ グループで受信ネットワーク トラフィックをロックダウンする前に、App Service 環境で使用される必須およびオプションのネットワーク ポートのセットを把握しておくことが重要です。  誤っていくつかのポートへのトラフィックを遮断すると、App Service 環境の機能が失われることがあります。

App Service 環境で使用されるポートの一覧を次に示します。 特に断りのない限り、すべてのポートは **TCP** です。

* 454:SSL を介した App Service Environment の管理および保守のために Azure インフラストラクチャによって使用される**必須ポート**。  このポートへのトラフィックはブロックしないでください。  このポートは常に、ASE のパブリック VIP にバインドします。
* 455:SSL を介した App Service Environment の管理および保守のために Azure インフラストラクチャによって使用される**必須ポート**。  このポートへのトラフィックはブロックしないでください。  このポートは常に、ASE のパブリック VIP にバインドします。
* 80:App Service Environment において App Service プランで実行されているアプリへの受信 HTTP トラフィック用の既定のポート。  ILB 対応の ASE では、このポートを ASE の ILB アドレスにバインドします。
* 443:App Service Environment において App Service プランで実行されているアプリへの受信 SSL トラフィック用の既定のポート。  ILB 対応の ASE では、このポートを ASE の ILB アドレスにバインドします。
* 21:FTP 用のコントロール チャネル。  FTP が使用されていない場合は、このポートを安全にブロックできます。  ILB 対応の ASE では、このポートを ASE の ILB アドレスにバインドできます。
* 990:FTPS 用のコントロール チャネル。  FTPS が使用されていない場合は、このポートを安全にブロックできます。  ILB 対応の ASE では、このポートを ASE の ILB アドレスにバインドできます。
* 10001 ～ 10020:FTP 用のデータ チャネル。  コントロール チャネルと同様、FTP が使用されていない場合は、これらのポートを安全にブロックできます。  ILB 対応の ASE では、このポートを ASE の ILB アドレスにバインドできます。
* 4016:Visual Studio 2012 でのリモート デバッグに使用されます。  機能が使用されていない場合は、このポートを安全にブロックできます。  ILB 対応の ASE では、このポートを ASE の ILB アドレスにバインドします。
* 4018:Visual Studio 2013 でのリモート デバッグに使用されます。  機能が使用されていない場合は、このポートを安全にブロックできます。  ILB 対応の ASE では、このポートを ASE の ILB アドレスにバインドします。
* 4020:Visual Studio 2015 でのリモート デバッグに使用されます。  機能が使用されていない場合は、このポートを安全にブロックできます。  ILB 対応の ASE では、このポートを ASE の ILB アドレスにバインドします。

## <a name="outbound-connectivity-and-dns-requirements"></a>発信接続と DNS の要件
App Service 環境が正常に機能するためには、さまざまなエンドポイントへの発信アクセスも必要です。 [ExpressRoute を使用した環境のネットワーク構成](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) の記事の「必要なネットワーク接続」というセクションに、App Service Environment で使用されるすべての外部エンドポイントが掲載されています。

App Service 環境では、仮想ネットワーク用に構成された有効な DNS インフラストラクチャが必要です。  何らかの理由で、App Service Environment の作成後に DNS 構成が変わった場合、開発者は強制的に App Service Environment から新しい DNS 構成を選択することができます。  [Azure Portal][NewPortal] の App Service 環境管理ブレードの上部にある [再起動] アイコンを使用して、ローリングする環境の再起動をトリガーすると、新しい DNS 構成が自動的に選択されます。

また、App Service 環境を作成する前に、vnet 上のカスタム DNS サーバーをセットアップしておくことをお勧めします。  App Service 環境の作成中に仮想ネットワークの DNS 構成が変更された場合、App Service 環境の作成プロセスは失敗します。  同様に、VPN ゲートウェイの他端にカスタム DNS サーバーが存在していて、その DNS サーバーが到達不能または使用できない場合、App Service 環境の作成プロセスも失敗します。

## <a name="creating-a-network-security-group"></a>ネットワーク セキュリティ グループの作成
ネットワーク セキュリティ グループの動作の詳細については、次の[情報][NetworkSecurityGroups]を参照してください。  下の Azure Service Management のサンプルは、ネットワーク セキュリティ グループの特徴に触れています。ここでは、App Service 環境を含むサブネットでのネットワーク セキュリティ グループの構成と適用に注目しています。

**注:** ネットワーク セキュリティ グループは、[Azure portal](https://portal.azure.com) を利用して視覚的に構成することも、Azure PowerShell を利用して構成することもできます。

ネットワーク セキュリティ グループは、最初に、サブスクリプションに関連付けられたスタンドアロン エンティティとして作成されます。 ネットワーク セキュリティ グループは Azure リージョン内に作成されるため、ネットワーク セキュリティ グループが App Service 環境と同じリージョンに作成されるようにします。

以下は、ネットワーク セキュリティ グループの作成を示しています。

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

ネットワーク セキュリティ グループが作成されると、それに 1 つ以上のネットワーク セキュリティ ルールが追加されます。  ルール セットは時間の経過と共に変更される可能性があるため、時間の経過に伴い追加のルールを容易に挿入できるように、ルールの優先度に使用される番号付けスキームを一定間隔で配置することをお勧めします。

次の例では、Azure インフラストラクチャで App Service 環境の管理と保守に必要とされる管理ポートへのアクセスを明示的に付与するルールを示しています。  すべての管理トラフィックが SSL 経由で流れ、クライアント証明書によってセキュリティで保護されているため、ポートが開かれている場合でも、Azure の管理インフラストラクチャ以外のどのエンティティからもアクセスできないことにご注意ください。

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP


ポート 80 と 443 へのアクセスをロックダウンして、App Service 環境をアップストリーム デバイスまたはサービスの背後に "隠す" 場合は、アップストリーム IP アドレスを知っている必要があります。  たとえば、Web アプリケーション ファイアウォール (WAF) を使用している場合、WAF は、ダウンストリーム App Service 環境へのトラフィックをプロキシするときに使用する専用の IP アドレス (複数の場合もあり) を保持します。  この IP アドレスは、ネットワーク セキュリティ ルールの *SourceAddressPrefix* パラメーターで使用する必要があります。

次の例では、特定のアップストリーム IP アドレスからの受信トラフィックが明示的に許可されています。  アドレス *1.2.3.4* は、アップストリーム WAF の IP アドレスのプレースホルダーとして使用されています。  アップストリーム デバイスまたはサービスで使用されているアドレスと一致するように値を変更します。

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

FTP サポートが必要な場合は、次のルールをテンプレートとして使用して、FTP 制御ポートとデータ チャネル ポートへのアクセス権を付与できます。  FTP はステートフルなプロトコルであるため、従来の HTTP/HTTPS ファイアウォールまたはプロキシ デバイス経由で FTP トラフィックをルーティングできない場合があります。  この場合は、*SourceAddressPrefix* を別の値に設定する必要があります。たとえば、FTP クライアントが実行されている開発者コンピューターまたはデプロイメント コンピューターの IP アドレス範囲です。 

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

(**注:** データ チャネル ポート範囲は、プレビュー期間中に変更される場合があります)。

Visual Studio でのリモート デバッグが使用されている場合、次のルールでは、アクセス権を付与する方法を示しています。  Visual Studio では、バージョンごとにリモート デバッグに使用するポートが異なるため、サポートされているバージョンごとに個別のルールがあります。  FTP アクセスと同様に、リモート デバッグ トラフィックは、従来の WAF またはプロキシ デバイス経由で適切にフローされない場合があります。  代わりに、 *SourceAddressPrefix* を、Visual Studio が実行されている開発者コンピューターの IP アドレス範囲に設定できます。

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## <a name="assigning-a-network-security-group-to-a-subnet"></a>サブネットへのネットワーク セキュリティ グループの割り当て
ネットワーク セキュリティ グループには、すべての外部トラフィックへのアクセスを拒否する既定のセキュリティ ルールがあります。  前に説明したネットワーク セキュリティ ルールと、受信トラフィックをブロックする既定のセキュリティ ルールを組み合わせた結果、 *許可* アクションに関連付けられた送信元アドレス範囲からのトラフィックのみを App Service 環境内で実行中のアプリへ送信できます。

ネットワーク セキュリティ グループは、セキュリティ ルールが設定された後、App Service 環境を含むサブネットに割り当てる必要があります。  割り当てコマンドは、App Service 環境が存在する仮想ネットワークの名前と、App Service 環境が作成されたサブネットの名前の両方を参照します。  

次の例は、ネットワーク セキュリティ グループがサブネットと仮想ネットワークに割り当てられることを示しています。

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

ネットワーク セキュリティ グループの割り当てが完了すると (割り当ては時間のかかる操作のため、完了するまでに数分かかる場合があります)、 *許可* ルールに一致する受信トラフィックのみが App Service 環境内のアプリに正常に到達します。

完全を期すため、次の例では、サブネットからネットワーク セキュリティ グループを削除し、関連付けを解除する方法を示します。

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## <a name="special-considerations-for-explicit-ip-ssl"></a>明示的な IP SSL に関する特別な考慮事項
アプリが、App Service 環境の既定の IP アドレスを使わず、明示的な IP SSL アドレスで構成されている場合 (パブリック VIP がある ASE に "*のみ*" あてはまります)、HTTP と HTTPS の両方のトラフィックは、ポート 80 と 443 以外の別のポート セットを経由してサブネットにフローされます。

各 IP SSL アドレスで使用されるポートの個々のペアは、App Service 環境の詳細 UX ブレードから、ポータルのユーザー インターフェイスで確認できます。  [すべての設定]、[IP アドレス] の順に選択します。  [IP アドレス] ブレードには、App Service 環境に対して明示的に構成されているすべての IP SSL アドレスを示すテーブルと共に、各 IP SSL アドレスに関連付けられた HTTP および HTTPS トラフィックをルーティングするのに使用される特殊なポート ペアが表示されます。  ネットワーク セキュリティ グループの規則を構成する際に、DestinationPortRange パラメーターで使用する必要があるのはこのポート ペアです。

ASE でアプリが IP SSL を使用するように構成されている場合、外部の顧客に特殊なポート ペアのマッピングは表示されず、外部の顧客がマッピングを確認する必要はありません。  アプリへのトラフィックは、構成済みの IP SSL アドレスに通常流れていきます。  特殊なポート ペアへの変換は、ASE を含むサブネットへのトラフィックのルーティングの最終段階において内部で自動的に実行されます。 

## <a name="getting-started"></a>使用の開始
App Service 環境の使用を開始するには、「[App Service 環境の概要][IntroToAppServiceEnvironment]」を参照してください。

アプリの App Service 環境からバックエンド リソースへの安全な接続の詳細については、「[App Service 環境からバックエンド リソースへの安全な接続][SecurelyConnecttoBackend]」を参照してください。

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->

