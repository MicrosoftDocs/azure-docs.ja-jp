---
title: セキュリティで保護された Azure AD Web アプリケーションを開発する | Microsoft Docs
description: この簡単なサンプル アプリでは、セキュリティのベスト プラクティスを実装して、Azure で開発を行う際のアプリケーションと組織のセキュリティ体制を向上させます。
keywords: NA
services: security
documentationcenter: na
author: fehase
manager: alclabo
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2019
ms.author: v-fehase
ms.openlocfilehash: 87df7824a182e68d849fdf967f96b2974b7e0c16
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2019
ms.locfileid: "71148024"
---
# <a name="develop-secure-app-for-an-azure-ad-app"></a>Azure AD アプリ用のセキュリティで保護されたアプリの開発
## <a name="overview"></a>概要

このサンプルは、Azure でアプリを開発するためのセキュリティ リソースにリンクする Web アプリを備えた単純な Azure Active Directory です。 このアプリでは、Azure でアプリを開発するときに、アプリケーションと組織のセキュリティ体制を向上させるうえで役立つセキュリティのベスト プラクティスを実装します。

デプロイ スクリプトによって、インフラストラクチャがセットアップされます。 デプロイ スクリプトを実行した後は、Azure portal でいくつかの手動構成を実行し、コンポーネントとサービスを互いにリンクする必要があります。 このサンプルは、セキュリティで保護された Azure インフラストラクチャを使用して、セキュリティで保護された Azure Active Directory を構築する必要がある、小売業に従事する Azure の経験豊富な開発者を対象としています。 


このアプリを開発してデプロイする中で、次の方法を学習します。 
- Azure Key Vault インスタンスを作成して保存し、そこからシークレットを取得する。
- フロントエンド ファイアウォール アクセスで分離された専用の Azure Web アプリをデプロイする。 
- OWASP 上位 10 のルール セットを使用するファイアウォールを使用して、Azure Application Gateway インスタンスを作成して構成する。 
- Azure サービスを使用して、転送中および保存時のデータの暗号化を有効にする。 
- Azure ポリシーとセキュリティ センターを設定してコンプライアンスを評価する。 

このアプリを開発してデプロイすると、次のサンプル Web アプリと、説明されている構成およびセキュリティ対策が設定されます。

## <a name="architecture"></a>アーキテクチャ
このアプリは、3 つの階層を持つ典型的な n 層アプリケーションです。 次に示すように、フロントエンド、バックエンド、データベースの各レイヤーに、監視コンポーネントとシークレット管理コンポーネントが統合されています。

![アプリのアーキテクチャ](./media/secure-aad-app/architecture.png)

このソリューションでは、次の Azure サービスを使用します。 デプロイ アーキテクチャの詳細については、デプロイ アーキテクチャのセクションを参照してください。 

このアーキテクチャは、次のコンポーネントで構成されています。

- [Azure Application Gateway](../../application-gateway/index.yml)。 アプリケーション アーキテクチャにゲートウェイとファイアウォールを提供します。
- [Application Insights](../../azure-monitor/app/app-insights-overview.md)。 複数のプラットフォームで拡張可能なアプリケーション パフォーマンス管理 (APM) サービスを提供します。
- [Azure Key Vault](../../key-vault/index.yml)。 アプリのシークレットを格納して暗号化し、それらに関連するアクセス ポリシーの作成を管理します。
- [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)。クラウドベースの ID とアクセス管理サービス、サインイン、およびアクセス リソースを提供します。
- [Azure ドメイン ネーム システム](../../dns/dns-overview.md)。 ドメインをホストするサービスを提供します。
- [Azure Load Balancer](../../load-balancer/load-balancer-overview.md)。 アプリケーションのスケーリングを提供し、サービスを高可用性にします。
- [Azure Web アプリ](../../app-service/overview.md)。  Web アプリケーションをホストするための HTTP ベースのサービスを提供します。
- [Azure Security Center](../../security-center/index.yml)。 クラウド内のハイブリッド ワークロード全体で高度な脅威保護を提供します。
- [Azure Policy](../../governance/policy/overview.md) 割り当てられているポリシーへの非準拠について、リソースの評価を提供します。

## <a name="threat-model"></a>脅威モデル
脅威モデリングは、ビジネスとアプリケーションに対する潜在的なセキュリティ上の脅威を特定して、適切な軽減計画を確保するプロセスです。

このサンプルでは、[Microsoft Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) を使用して、セキュリティで保護されたサンプル アプリに対する脅威モデリングを実装しています。 コンポーネントとデータ フローを図式化することで、開発プロセスの早い段階で問題と脅威を特定できます。 これを使用することで、時間と費用を節約できます。

次に示すのは、サンプル アプリの脅威モデルです。

![脅威モデル](./media/secure-aad-app/threat-model.png)

Threat Modeling Tool によって生成される脅威と潜在的脆弱性の例を、次のスクリーンショットに示します。 脅威モデルでは、公開される攻撃対象領域の概要が示され、問題を軽減する方法を検討するよう開発者に促します。

![脅威モデルの出力](./media/secure-aad-app/threat-model-output.png)

### <a name="prerequisites"></a>前提条件
アプリケーションを起動して稼働させるには、次のツールをインストールする必要があります。

- アプリケーション コードを表示および変更するためのコード エディター。[Visual Studio Code](https://code.visualstudio.com/) はオープンソース オプションです。
- 開発コンピューター上の [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest,)。
- システム上の [Git](https://git-scm.com/)。 Git を使用してソース コードをローカルに複製します。
- [jq](https://stedolan.github.io/jq/)。わかりやすい方法で JSON にクエリを実行するための UNIX ツールです。

サンプル アプリのリソースをデプロイするには、Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、[無料のアカウントを作成して](https://azure.microsoft.com/free/)、サンプル アプリをテストできます。

これらのツールをインストールすると、アプリを Azure にデプロイする準備が整います。

### <a name="implementation-guidance"></a>実装ガイダンス
デプロイ スクリプトは、4 つのフェーズに分割できる 1 つのスクリプトです。 各フェーズでは、[アーキテクチャ ダイアグラム](#architecture)に含まれる Azure リソースが 1 つずつデプロイおよび構成されます。

4 つのフェーズは次のとおりです。

- Azure Key Vault をデプロイする。
- Azure Web Apps をデプロイする
- Web アプリケーション ファイアウォールが有効になったアプリケーション ゲートウェイをデプロイする。
- デプロイされたアプリで Azure AD を構成する。

各フェーズは、それまでにデプロイしたリソースの構成を使用して、前のフェーズに基づいて構築されます。

実装手順を完了するには、「[前提条件](#prerequisites)」に記載されているツールがインストールされていることを確認します。

#### <a name="deploy-azure-key-vault"></a>Azure Key Vault をデプロイする
このセクションでは、シークレットと証明書の格納に使用する Azure Key Vault インスタンスを作成してデプロイします。

デプロイが完了すると、Azure Key Vault インスタンスが Azure にデプロイされます。

Powershell を使用して Azure Key Vault をデプロイするには、次の手順を実行します。
 
1. Azure Key Vault の変数を宣言します。
2. Azure Key Vault プロバイダーを登録します。
3. インスタンスのリソース グループを作成します。
4. 手順 3. で作成したリソース グループに Azure Key Vault インスタンスを作成します。

#### <a name="the-below-azure-ad-user-will-have-admin-permissions-to-the-key-vault"></a>次の Azure AD ユーザーには、Key Vault に対する管理者アクセス許可が付与されます。
    $keyVaultAdminUsers = @($user1,user2)

#### <a name="register-the-az-providers"></a>Az Providers を登録する
    Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

#### <a name="create-the-azure-key-vault-instance"></a>Azure Key Vault インスタンスを作成する
    New-AzKeyVault -Name $kvName 
                -ResourceGroupName $ResourceGroup 
                -Location 'East US'
                -EnabledForDiskEncryption

#### <a name="add-the-administrator-policies-to-the-key-vault"></a>Key Vault に管理者ポリシーを追加する
    foreach ($keyVaultAdminUser in $keyVaultAdminUsers) {
    $UserObjectId = (Get-AzADUser -SearchString $keyVaultAdminUser).Id
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName -ObjectId $UserObjectId 
    -PermissionsToKeys all -PermissionsToSecrets all -PermissionsToCertificates all
    }

#### <a name="to-create-an-access-policy-to-allow-a-user-to-get-and-list-cryptographic-keys-certificates-and-secrets-if-you-know-the-user-principal-name"></a>ユーザー プリンシパル名がわかっている場合に、ユーザーが暗号化キー、証明書、およびシークレットを取得して一覧表示できるようにするアクセス ポリシーを作成するには、次のようにします。
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName 
                           -ResourceGroupName $resourceGroupName 
                           -UserPrincipalName 'user1@contoso.com 
                           -PermissionsToCertificates list, get 
                           -PermissionsToKeys list, get 
                           -PermissionsToSecrets list, get 

リソースへのアクセスにキー コンテナーを使用するアプリでは、Azure リソースにマネージド ID を使用することをお勧めします。 キー コンテナーへのアクセス キーをコードや構成に格納しない場合、その分、セキュリティ体制が高まります。

ルート証明書はコンテナーに含まれています。 証明書を取得するには、次の手順を実行します。

1. [証明機関](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt)から証明書ファイルをダウンロードします。
2. 次のコマンドを実行して、証明書ファイルをデコードします。

   ```powershell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```
このスクリプトにより、シークレットをコードまたは構成にハード コーディングすることなく、Azure Key Vault と対話するために MSI で使用できる、App Service インスタンス用の割り当て ID が作成されます。

ポータルの Azure Key Vault インスタンスに移動し、[アクセス ポリシー] タブで割り当て ID を承認します。 **[新しいアクセス ポリシーの追加]** を選択します。 **[プリンシパルの選択]** で、作成した App Service インスタンスの名前に類似のアプリケーション名を検索します。
アプリケーションに関連付けられているサービス プリンシパルが表示されます。 次のスクリーンショットに示すように、それを選択し、アクセス ポリシー ページを保存します。

アプリケーションではキーを取得すればよいだけなので、シークレットのオプションで **[取得]** アクセス許可を選択してアクセスを許可し、付与されている特権を減らします。

![Key Vault アクセス ポリシー](./media/secure-aad-app/kv-access-policy.png)

"*キー コンテナーのアクセス ポリシーを作成する*"

アクセス ポリシーを保存し、 **[アクセスポリシー]** タブで新しい変更を保存して、ポリシーを更新します。

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Web アプリケーション ファイアウォールが有効になったアプリケーション ゲートウェイをデプロイする
Web アプリでは、外部のインターネット上にサービスを直接公開することはお勧めしません。
負荷分散とファイアウォール規則を使用すると、セキュリティが強化され、受信トラフィックが制御され、管理が容易になります。

Application Gateway インスタンスをデプロイするには、次の手順を実行します。

1. アプリケーション ゲートウェイを格納するリソース グループを作成します。
2. ゲートウェイに接続する仮想ネットワークをプロビジョニングします。
3. 仮想ネットワーク内にゲートウェイ用のサブネットを作成します。
4. パブリック IP アドレスをプロビジョニングします。
5. アプリケーション ゲートウェイをプロビジョニングします。
6. ゲートウェイで Web アプリケーション ファイアウォールを有効にします。

```
Connect-AzAccount
Select-AzSubscription -SubscriptionId '$SubscriptionId'
New-AzResourceGroup -Name appgw-rg -Location "East US"

#Create a virtual network and a subnet for the application gateway

#Assign an address range for the subnet to be used for the application gateway.

$gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

#Assign an address range to be used for the back-end address pool.

$nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.0.0/24

#Create a virtual network with the subnets defined in the preceding steps.

$vnet = New-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

#Retrieve the virtual network resource and subnet resources to be used in the steps that follow.

$vnet = Get-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
$nicSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet


#Create a public IP address for the front-end configuration

$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "East US" -AllocationMethod Dynamic

#Create an application gateway configuration object

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

#Create a front-end IP configuration

$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

#Configure the back-end IP address pool with the IP addresses of the back-end web servers

$pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 10.0.0.0

#Configure the front-end IP port for the public IP endpoint

$fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443

#Configure the certificate for the application gateway. This certificate is used to decrypt and reencrypt the traffic on the application gateway

$passwd = ConvertTo-SecureString  "P@ssword!1" -AsPlainText -Force 
$cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer" -Password $passwd 

#Create the HTTP listener for the application gateway

$listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert

#Upload the certificate to be used on the SSL-enabled back-end pool resources

#$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer

$trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile "C:\AAD\Securities\Certificates\sslcert.com.cer"

#Configure the HTTP settings for the application gateway back end

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name “setting01” -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"

#Create a load-balancer routing rule that configures the load balancer

$rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

#Configure the instance size of the application gateway

$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

#Configure the SSL policy to be used on the application gateway

$SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom

$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "East US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose

```

#### <a name="deploy-azure-web-apps"></a>Azure Web Apps をデプロイする
Azure App Service を使用すると、Python、Ruby、C#、Java などの言語を使用して、Web アプリをビルドおよびホストすることができます。 Azure ではカスタム コンテナーもサポートしているため、事実上すべてのプログラミング言語を Azure App Service プラットフォームで実行できます。

#### <a name="create-an-app-service-plan-in-free-tier"></a>Free レベルで App Service プランを作成する
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $webappname -Tier Free

#### <a name="create-a-web-app"></a>Web アプリを作成する
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $webappname

    Write-Host "Configure a CNAME record that maps $fqdn to $webappname.azurewebsites.net"
    Read-Host "Press [Enter] key when ready ..."

#### <a name="before-continuing-go-to-your-azure-domain-name-system-configuration-ui-for-your-custom-domain-and-follow-the-instructions-at-httpsakamsappservicecustomdns-to-configure-a-cname-record-for-the-hostname-www-and-point-it-your-web-apps-default-domain-name"></a>続行する前に、カスタム ドメインの Azure ドメイン ネーム システム構成 UI にアクセスし、 https://aka.ms/appservicecustomdns の指示に従ってホスト名 "www" の CNAME レコードを構成し、Web アプリの既定のドメイン名を指定します。

#### <a name="upgrade-app-service-plan-to-shared-tier-minimum-required-by-custom-domains"></a>App Service プランを Shared レベル (カスタム ドメインで必要な最低限のレベル) にアップグレードする
    Set-AzAppServicePlan -Name $webappname -ResourceGroupName $webappname -Tier Shared

#### <a name="add-a-custom-domain-name-to-the-web-app"></a>カスタム ドメイン名を Web アプリに追加する
    Set-AzWebApp -Name $webappname -ResourceGroupName $webappname `-HostNames @($fqdn,"$webappname.azurewebsites.net")

## <a name="guidance-and-recommendations"></a>ガイダンスと推奨事項

### <a name="network"></a>ネットワーク
デプロイが完了すると、Web アプリケーション ファイアウォールが有効になったアプリケーション ゲートウェイが作成されます。

ゲートウェイ インスタンスでは、HTTPS 用にポート 443 が公開されます。 この構成により、アプリには HTTPS 経由でポート 443 からのみアクセスできるようになります。

未使用のポートをブロックし、攻撃対象として公開される領域を制限することは、セキュリティ上のベスト プラクティスです。

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>App Service インスタンスにネットワーク セキュリティ グループを追加する

App Service インスタンスは、仮想ネットワークと統合できます。 この統合により、アプリの送受信トラフィックを管理するネットワーク セキュリティ グループ ポリシーを使用して、App Service インスタンスを構成することができます。

1. この機能を有効にするには、Azure App Service インスタンスのブレードの **[設定]** で **[ネットワーク]** を選択します。 右側のウィンドウの **[VNet 統合]** の下で構成します。

   ![新しい仮想ネットワーク統合](./media/secure-web-app/app-vnet-menu.png)

    "*App Service 用の新しい仮想ネットワーク統合*"
1. 次のページで、 **[VNet の追加 (プレビュー)]** を選択します。

1. 次のメニューで、デプロイで作成した、名前が `aad-vnet` で始まる仮想ネットワークを選択します。 新しいワークスペースを作成することも、既存のワークスペースを選択することができます。
   ここでは、新しいサブネットを作成します。 **[アドレス範囲]** を「**10.0.3.0/24**」に設定し、サブネットに「**app-subnet**」という名前を付けます。

   ![App Service 仮想ネットワークの構成](./media/secure-web-app/app-vnet-config.png)

    "*App Service 用の仮想ネットワークの構成*"

仮想ネットワークの統合を有効にしたので、ネットワーク セキュリティ グループをアプリに追加できます。

1. 検索ボックスを使用して、「**ネットワーク セキュリティ グループ**」を検索します。 結果で **[ネットワーク セキュリティ グループ]** を選択します。

    ![ネットワーク セキュリティ グループを検索する](./media/secure-web-app/nsg-search-menu.png)

    "*ネットワーク セキュリティ グループを検索する*"

2. 次のメニューで、 **[追加]** を選択します。 NSG の**名前**と、NSG を配置する**リソース グループ**を入力します。 この NSG は、アプリケーション ゲートウェイのサブネットに適用されます。

    ![NSG を作成する](./media/secure-web-app/nsg-create-new.png)

    "*NSG を作成する*"

3. NSG を作成したら、それを選択します。 そのブレードの **[設定]** で **[受信セキュリティ規則]** を選びます。 次の設定を構成して、ポート 443 経由でアプリケーション ゲートウェイに送信されてくる接続を許可します。

   ![NSG を構成する](./media/secure-web-app/nsg-gateway-config.png)

   "*NSG を構成する*"

4. ゲートウェイ NSG の送信規則では、サービス タグ `AppService` をターゲットとする規則を作成して、App Service インスタンスへの送信接続を許可する規則を追加します。

   ![NSG の送信規則を追加する](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   "*NSG の送信規則を追加する*"

    ゲートウェイから仮想ネットワークに送信規則を送信できるように、別の送信規則を追加します。

   ![別の送信規則を追加する](./media/secure-web-app/nsg-outbound-vnet.png)

    "*別の送信規則を追加する*"

5. NSG のサブネット ブレードで **[関連付け]** を選択し、デプロイで作成した仮想ネットワークを選択して、**gw-subnet** という名前のゲートウェイ サブネットを選択します。 この NSG はサブネットに適用されます。

6. 前の手順と同じように、今回は App Service インスタンス用に別の NSG を作成します。 NSG に名前を付けます。 アプリケーション ゲートウェイ NSG の場合と同じように、ポート 443 の受信規則を追加します。

   このアプリ用ではない App Service Environment インスタンスに App Service インスタンスがデプロイされている場合は、App Service NSG の受信セキュリティ グループでポート 454 と 455 を開いて Azure Service Health プローブを許可する受信規則を追加できます。 その構成は次のとおりです。

   ![Azure Service Health プローブ用の規則を追加する](./media/secure-web-app/nsg-create-healthprobes.png)

    "*Azure Service Health プローブ用の規則を追加する (App Service Environment のみ)* "

攻撃対象領域を制限するには、App Service のネットワーク設定を変更して、アプリケーション ゲートウェイのみがアプリケーションにアクセスできるようにします。
設定を適用するには、App Service のネットワーク タブに移動して、 **[IP 制限]** タブを選択し、アプリケーション ゲートウェイの IP のみがサービスに直接アクセスできるようにする許可規則を作成します。 ゲートウェイの IP アドレスは、その概要ページから取得できます。 **[IP アドレスまたは CIDR]** タブで、`<GATEWAY_IP_ADDRESS>/32` の形式で IP アドレスを入力します。

![ゲートウェイのみを許可する](./media/secure-web-app/app-allow-gw-only.png)

"*ゲートウェイ IP のみに App Service へのアクセスを許可する*"

### <a name="azure-domain-name-system"></a>Azure ドメイン ネーム システム 
Azure ドメイン ネーム システムは、Web サイトまたはサービスの名前をその IP アドレスに変換する (または解決する) 役割を担います。 Azure ドメイン ネーム システム (https://docs.microsoft.com/azure/dns/dns-overview) は、ドメイン ネーム システム ドメインのホスティング サービスであり、Azure インフラストラクチャを使用した名前解決を提供します。 Azure でドメインをホストすることで、その他の Azure サービスと同じ資格情報、API、ツール、課金情報を使用してドメイン ネーム システム レコードを管理できます。 Azure ドメイン ネーム システムでは、プライベート ドメイン ネーム システム ドメインもサポートされています。

### <a name="azure-disk-encryption"></a>Azure Disk Encryption
Azure Disk Encryption では、Windows の BitLocker 機能を利用して、データ ディスクのボリュームを暗号化します。 このソリューションは、ディスクの暗号化キーを制御および管理できるように、Azure Key Vault と統合されています。

### <a name="identity-management"></a>ID 管理
次のテクノロジによって、Azure 環境でのカードホルダー データへのアクセスを管理する機能が提供されます。
- Azure Active Directory は、Microsoft のマルチテナント クラウドベースのディレクトリおよび ID 管理サービスです。 このソリューションのすべてのユーザー (Azure WebApp にアクセスするユーザーを含む) は、Azure Active Directory で作成されています。
- Azure ロールベースのアクセス制御によって、管理者は、業務の遂行に必要なアクセスのみをユーザーに付与するきめの細かいアクセス許可を定義できます。 すべてのユーザーに Azure リソースへの無制限のアクセス許可を付与する代わりに、管理者は、カードホルダー データにアクセスするための特定のアクションのみを許可できます。 サブスクリプションへのアクセスは、サブスクリプションの管理者に制限されます。
- Azure Active Directory Privileged Identity Management では、お客様は、カード所有者データなどの特定の情報にアクセスできるユーザーの数を最小限に抑えることができます。 管理者は、Azure Active Directory Privileged Identity Management を使用して、特権 ID と特権 ID によるリソースへのアクセスを検出、制限、監視できます。 この機能を使用して、必要に応じて、オンデマンドの Just-In-Time 管理アクセスを適用することもできます。
- Azure Active Directory Identity Protection では、組織の ID に影響する可能性のある脆弱性を検出し、組織の ID に関連する検出された疑わしいアクションに対する自動応答を構成します。さらに、疑わしいインシデントを調査し、適切なアクションを実行してそれらを解決します。
### <a name="secrets-management"></a>シークレットの管理
ソリューションでは、キーとシークレットの管理に Azure Key Vault が使用されます。 Azure Key Vault は、クラウド アプリケーションやサービスで使用される暗号化キーとシークレットをセキュリティで保護するために役立ちます。 次の Azure Key Vault 機能は、そのようなデータの保護とアクセスに役立ちます。
   - 必要に応じて、高度なアクセス ポリシーが構成されます。
   - Key Vault のアクセス ポリシーは、キーとシークレットに対する最低限必要なアクセス許可で定義されます。
   - Key Vault のすべてのキーとシークレットに有効期限があります。
   - Key Vault 内のすべてのキーは、特殊なハードウェア セキュリティ モジュールによって保護されます。 キーの種類は、ハードウェア セキュリティ モジュール (HSM) によって保護された 2048 ビット RSA キーです。
   - Key Vault を使用すると、キーとシークレット (認証キー、ストレージ アカウント キー、データ暗号化キー、.PFX ファイル、パスワードなど) をハードウェア セキュリティ モジュール (HSM) で保護されたキーを使用して暗号化できます。 
   - ロールベースのアクセス制御 (RBAC) を使用して、特定のスコープ内のユーザー、グループ、アプリケーションにアクセス許可を割り当てます。     
   - Key Vault を使用して、自動更新で TLS 証明書を管理します。 
   - Key Vault の診断ログは、少なくとも 365 日のリテンション期間で有効になっています。
   - キーに対して許可される暗号化操作は必要なものに制限されます。
### <a name="azure-security-center"></a>Azure Security Center
Azure Security Center を使用すると、お客様はワークロード全体へのセキュリティ ポリシーの一元的な適用と管理、脅威にさらされる状態の軽減、攻撃の検出とその対応を行うことができます。 さらに、 
   - Azure Security Center は、Azure サービスの既存の構成にアクセスして、セキュリティ状況の改善とデータの保護に役立つ、構成とサービスに関する推奨事項を提供します。
   - Azure Security Center では、さまざまな検出機能を使用して、お客様の環境を対象とする攻撃の可能性を通知します。 これらのアラートには、アラートをトリガーした要因、対象となったリソース、攻撃元に関する重要な情報が含まれています。 Azure Security Center には、事前に定義された一連のセキュリティ アラートが用意されています。これらは、脅威または疑わしいアクティビティが発生した際にトリガーされます。 Azure Security Center のカスタム アラート ルールを使用すると、お客様が環境から既に収集されているデータに基づいて、新しいセキュリティ アラートを定義できます。
   - Azure Security Center では、お客様の潜在的なセキュリティの問題の検出と対処が簡単になるように、優先度付けしたセキュリティの警告とインシデントを提供しています。 インシデント対応チームによる脅威の調査と修復を支援するために、検出された脅威ごとに脅威インテリジェンス レポートが生成されます。
### <a name="azure-application-gateway"></a>Azure Application Gateway 
   このアーキテクチャでは、Web アプリケーション ファイアウォールが構成され、OWASP ルールセットが有効な Azure Application Gateway を使用してセキュリティの脆弱性のリスクを軽減します。 その他の機能には次のものがあります。
   - エンドツーエンド SSL。
   - TLS v1.0 および v1.1 の無効化。
   - TLS v1.2 の有効化。
   - Web アプリケーション ファイアウォール (防止モード)。
   - OWASP 3.0 ルールセットを使用した防止モード。
   - 診断ログの有効化。
   - カスタム正常性プローブ。
   - Azure Security Center と Azure Advisor による、その他の保護と通知の提供。 Azure Security Center は評価システムも提供します。
### <a name="logging-and-auditing"></a>ログ記録と監査
Azure サービスは、システムの正常性だけではなく、システムとユーザーのアクティビティも詳細に記録します。
   - アクティビティ ログ:[アクティビティ ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)は、サブスクリプションのリソースに対して実行された操作に関する分析情報を提供します。 アクティビティ ログは、操作のイニシエーター、発生時刻、状態の判断に役立ちます。
   - 診断ログ:[診断ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)には、各リソースによって出力されるすべてのログが含まれます。 これらのログには、Windows イベント システム ログ、Azure Storage ログ、Key Vault 監査ログ、および Application Gateway のアクセス ログとファイアウォール ログが含まれます。 すべての診断ログは、暗号化され、集中管理された Azure Storage アカウントに書き込まれ、アーカイブされます。 リテンション期間には、組織固有の保有要件を満たすために最長 730 日までの日数をユーザーが設定できます。
### <a name="azure-monitor-logs"></a>Azure Monitor ログ
   これらのログは、処理、格納、およびダッシュボードでのレポート表示を行うために、[Azure Monitor ログ](https://azure.microsoft.com/services/log-analytics/)に統合されます。 収集されたデータは、その型ごとに Log Analytics ワークスペース内の別個のテーブルにまとめられ、すべてのデータがその収集元にかかわらず一斉に分析できる状態になります。 さらに、Azure Security Center を Azure Monitor ログと統合することで、お客様は Kusto クエリを使用してセキュリティ イベント データにアクセスし、それを他のサービスからのデータと組み合わせることができます。

   このアーキテクチャの一部として、次の Azure [監視ソリューション](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)が含まれます。

   - [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment):Active Directory 正常性チェック ソリューションは、一定の間隔でサーバー環境のリスクと正常性を評価し、デプロイされたサーバー インフラストラクチャに固有の推奨事項を重要度別に示した一覧を提供します。
   - [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth):Agent Health ソリューションは、デプロイされたエージェント数とその地理的配置に加え、応答しないエージェント数やオペレーショナル データを送信しているエージェント数を報告します。
   - [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity):Activity Log Analytics ソリューションは、顧客向けのすべての Azure サブスクリプションにわたる Azure アクティビティ ログの分析に役立ちます。
### <a name="azure-monitor"></a>Azure Monitor
   [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) は、組織で監査、アラートの作成、データのアーカイブ、Azure リソース内の API 呼び出しの追跡を実行できるようにすることで、ユーザーがパフォーマンスの追跡、セキュリティの維持、傾向の識別を実行できるようにします。
### <a name="application-insights"></a>Application Insights 
   [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) は、複数のプラットフォームで使用できる Web 開発者向けの拡張可能なアプリケーション パフォーマンス管理サービスです。 Application Insights は、パフォーマンスの異常を検出します。それを使用して、実行中の Web アプリケーションを監視できます。 組み込まれている強力な分析ツールを使えば、問題を診断し、ユーザーがアプリを使用して実行している操作を把握できます。 それは、パフォーマンスやユーザビリティを継続的に向上させるうえで役立つように設計されています。

### <a name="azure-key-vault"></a>Azure Key Vault
   キーを格納する組織のコンテナーを作成し、以下のような運用タスクのアカウンタビリティを維持します。

   - Key Vault に格納されるデータは次のとおりです。   
   - Application Insight キー
   - データ ストレージ アクセス キー
   - 接続文字列
   - データ テーブル名
   - ユーザーの資格情報
   - 必要に応じて、高度なアクセス ポリシーが構成されます。
   - Key Vault のアクセス ポリシーは、キーとシークレットに対する最低限必要なアクセス許可で定義されます。
   - Key Vault のすべてのキーとシークレットに有効期限があります。
   - Key Vault 内のすべてのキーは、ハードウェア セキュリティ モジュール (HSM) によって保護されます。[キーの種類は、ハードウェア セキュリティ モジュール (HSM) によって保護された       
     2048 ビット RSA キーです]
   - ロールベースのアクセス制御 (RBAC) を使用して、すべてのユーザー/ID に最低限必要なアクセス許可が付与されます。
   - アプリケーションは、相互に信頼し、実行時に同じシークレットへのアクセスを必要とする場合を除き、Key Vault を共有しません。
   - Key Vault の診断ログは、少なくとも 365 日のリテンション期間で有効になっています。
   - キーの許可される暗号化操作は必要なものに制限されます。

### <a name="vpn-and-expressroute"></a>VPN と ExpressRoute
   この PaaS Web アプリケーションの参照アーキテクチャの一部としてデプロイされるリソースへの接続を安全に確立することによって、[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) またはセキュリティ保護された VPN トンネルを構成する必要があります。 ExpressRoute または VPN を適切に設定することで、お客様は転送中のデータに対して保護のレイヤーを追加できます。

   Azure を使用してセキュリティ保護された VPN トンネルを実装することにより、オンプレミス ネットワークと Azure 仮想ネットワークの間で仮想プライベート接続を作成できます。 この接続はインターネットを介して行われ、お客様はご自分のネットワークと Azure の間の暗号化されたリンク内で情報を安全に "トンネリング" することができます。 サイト間 VPN は、数十年にわたってあらゆる規模の企業に導入されている安全で成熟したテクノロジです。 このオプションでは、暗号化メカニズムとして IPsec トンネル モードが使用されます。

   VPN トンネル内のトラフィックはサイト間 VPN を使用してインターネット上を転送されるため、Microsoft は、さらに高いセキュリティで保護されたもう 1 つの接続オプションを提供します。 Azure ExpressRoute は、オンプレミスの場所または Exchange ホスティング プロバイダーと Azure の間に確立される専用 WAN リンクです。 ExpressRoute 接続はインターネットを経由しないため、これらの接続はインターネット経由の一般的な接続に比べて、安全性と信頼性が高く、待機時間も短く、高速です。 さらに、これはお客様の通信プロバイダーの直接接続であり、データがインターネット上を移動しないため、インターネットに公開されることはありません。

   オンプレミス ネットワークを Azure に拡張するセキュアなハイブリッド ネットワークを実装するためのベスト プラクティスが[提供されています](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)。

#### <a name="implement-azure-active-directory-oidc"></a>Azure Active Directory OIDC を実装する

1. ソース コード リポジトリを複製するには、次の Git コマンドを使用します。

 ``` git
 git clone https://github.com/Azure-Samples/AAD-Security
   ```
## <a name="update-the-redirect-urls"></a>リダイレクト URL の更新
1.  Azure portal に戻ります。 左側のナビゲーション ウィンドウで、[Azure Active Directory] サービスを選択し、[アプリの登録] を選択します。
2.  結果の画面で、WebApp-OpenIDConnect-DotNet-code-v2 アプリケーションを選択します。
3.  [認証] タブの [リダイレクト URI] セクションで、コンボボックスの [Web] を選択し、次のリダイレクト URI を追加します。
    [https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net](https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net ) https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signin-oidc [詳細設定] セクションの [ログアウト URL] を「 https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net/signout-oidc 」に設定します。
4.  [ブランド] タブで、ホーム ページの URL を App Service のアドレスに更新します (たとえば https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net )。
        構成を保存します。
5.  アプリケーションで Web API を呼び出す場合、appsettings.json プロジェクトに必要な変更を適用し、localhost でなく公開された API の URL が呼び出されるようにしてください。
サンプルを発行します。
    1.  App Service の [概要] タブで、[発行プロファイルの取得] リンクをクリックして発行プロファイルをダウンロードし、保存します。 ソース管理などの他のデプロイ メカニズムを使用することもできます。
    2.  Visual Studio に切り替えて、WebApp-OpenIDConnect-DotNet-code-v2 プロジェクトに移動します。 ソリューション エクスプローラーでプロジェクトを右クリックし、[発行] を選択します。 下部のバーにある [プロファイルのインポート] をクリックし、前にダウンロードした発行プロファイルをインポートします。
    3.  [構成] をクリックし、[接続] タブで宛先 URL を更新してホーム ページの URL の https になるようにします (https://WebApp-OpenIDConnect-DotNet-code-v2-contoso.azurewebsites.net など)。 [次へ] をクリックします。
    4.  [設定] タブで、[組織認証の有効化] が選択されていないことを確認します。 [保存] をクリックします。 メイン画面で [発行] をクリックします。
    5.  Visual Studio によってプロジェクトが発行され、ブラウザーでプロジェクトの URL が自動的に開かれます。 プロジェクトの既定の Web ページが表示された場合、発行は成功しています。
#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Azure Active Directory に Multi-Factor Authentication を実装する
   管理者は、ポータルのサブスクリプション アカウントが保護されていることを確認する必要があります。 作成したリソースはサブスクリプションで管理されているため、サブスクリプションは攻撃に対して脆弱です。 サブスクリプションを保護するには、サブスクリプションの **[Azure Active Directory]** タブで Multi-Factor Authentication を有効にします。

   Azure AD は、特定の条件に合致するユーザーまたはユーザー グループに適用されるポリシーに基づいて動作します。
Azure では、管理者がポータルにサインインするために 2 要素認証を必要とすることを指定する既定のポリシーが作成されます。
このポリシーを有効にすると、いったん Azure portal からサインアウトして、再度サインインするように求められる場合があります。

管理者のサインイン用に MFA を有効にするには、次の手順を実行します。

   1. Azure portal の **[Azure Active Directory]** タブに移動します
   2. [セキュリティ] カテゴリで、[条件付きアクセス] を選択します。 次の画面が表示されます。

       ![条件付きアクセス - ポリシー](./media/secure-aad-app/ad-mfa-conditional-add.png)

新しいポリシーを作成できない場合は、次の手順を実行します。

   1. **[MFA]** タブに移動します。
   2. 無料試用版をサブスクライブするには、Azure AD Premium の**無料試用版**のリンクを選択します。

   ![Azure AD Premium の無料試用版](./media/secure-aad-app/ad-trial-premium.png)

[条件付きアクセス] 画面に戻ります。

   1. [新しいポリシー] タブを選択します。
   2. ポリシー名を入力します。
   3. MFA を有効にするユーザーまたはグループを選択します。
   4. **[アクセス制御]** で **[許可]** タブを選択し、 **[多要素認証が必要です]** (および必要に応じてその他の設定) を選択します。

   ![Require MFA (MFA が必須)](./media/secure-aad-app/ad-mfa-conditional-add.png)

   このポリシーを有効にするには、画面上部にあるチェック ボックスをオンにするか、 **[条件付きアクセス]** タブで同じことを実行します。ポリシーが有効になると、ユーザーは、ポータルへのサインインには MFA が必要になります。

   すべての Azure 管理者に対して MFA を要求するベースライン ポリシーがあります。 ポータルですぐにこれを有効にすることができます。 このポリシーを有効にすると、現在のセッションが無効になり、再度サインインするように強制される可能性があります。

   ベースライン ポリシーが有効になっていない場合は、次の手順を実行します。
   1.   **[管理者に MFA を要求する]** を選択します。
   2.   **[ポリシーをすぐに使用する]** を選択します。

   ![[ポリシーをすぐに使用する] を選択する](./media/secure-aad-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Azure Sentinel を使用してアプリとリソースを監視する

   アプリケーションの規模が拡大するにつれて、リソースから受信したすべてのセキュリティ信号とメトリックを集約し、アクション指向なやり方で役立てることが困難になります。

   Azure Sentinel は、データを収集し、考えられる脅威の種類を検出し、セキュリティ インシデントを可視化するように設計されています。
Azure Sentinel では、ユーザーが手動で介入する前に、記述済みのプレイブックを使用してアラートおよびインシデント管理プロセスを開始できます。

   このサンプル アプリは、Azure Sentinel の監視対象となる複数のリソースで構成されています。
Azure Sentinel を設定するには、さまざまなリソースから収集されたすべてのデータを格納する Log Analytics ワークスペースをまず作成する必要があります。

このワークスペースを作成するには、次の手順を実行します。

   1. Azure portal の検索ボックスで、「**Log Analytics**」を検索します。 **[Log Analytics ワークスペース]** を選択します。

   ![Log Analytics ワークスペースを検索する](./media/secure-aad-app/sentinel-log-analytics.png)

   "*Log Analytics ワークスペースを検索する*"

   2. 次のページで **[追加]** を選択し、ワークスペースの名前、リソース グループ、および場所を指定します。
   ![Log Analytics ワークスペースの作成](./media/secure-aad-app/sentinel-log-analytics-create.png)

   *Log Analytics ワークスペースの作成*

   3. 検索ボックスを使用して、「**Azure Sentinel**」を検索します。

   ![Azure Sentinel を検索する](./media/secure-aad-app/sentinel-add.png)

   "*Azure Sentinel を検索する*"

   4. **[追加]** を選択し、先ほど作成した Log Analytics ワークスペースを選択します。

   ![Log Analytics ワークスペースを追加する](./media/secure-aad-app/sentinel-workspace-add.png)

   "*Log Analytics ワークスペースを追加する*"

   5. **[Azure Sentinel - データ コネクタ]** ページの **[構成]** で、 **[データ コネクタ]** を選択します。 Azure Sentinel での分析対象として Log Analytics ストレージ インスタンスにリンクできる Azure サービスの配列が表示されます。

   ![Log Analytics データ コネクタ](./media/secure-aad-app/sentinel-connectors.png)

      "*Azure Sentinel にデータ コネクタを追加する*"

   たとえば、アプリケーション ゲートウェイを接続するには、次の手順を実行します。

   1. Azure Application Gateway インスタンスのブレードを開きます。
   2. **[監視]** で **[診断設定]** を選択します。
   3. **[診断設定の追加]** を選択します。

   ![アプリケーション ゲートウェイの診断を追加する](./media/secure-aad-app/sentinel-gateway-connector.png)
         
   "*アプリケーション ゲートウェイの診断を追加する*"

   4. **[診断設定]** ページで、作成した Log Analytics ワークスペースを選択し、収集して Azure Sentinel に送信するすべてのメトリックを選択します。 **[保存]** を選択します。

   ![Azure Sentinel のコネクタ設定](./media/secure-aad-app/sentinel-connector-settings.png)



## <a name="cost-considerations"></a>コストに関する考慮事項
   まだ Azure アカウントを持っていない場合は、無料で作成できます。 開始するには、[無料アカウントのページ](https://azure.microsoft.com/free/)に移動し、無料の Azure アカウントでできることと、12 か月間無料で使用できる製品をご確認ください。

   セキュリティ機能を備えたサンプル アプリのリソースをデプロイするには、いくつかの Premium 機能に対して料金を支払う必要があります。 アプリケーションの規模が拡大し、Azure によって提供される無料レベルと試用版をアップグレードしてアプリケーションの要件を満たす必要が生じるにつれ、コストが増加する可能性があります。 コストの見積もりには、Azure [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)をご利用ください。

## <a name="next-steps"></a>次の手順
   次の記事は、セキュリティで保護されたアプリケーションを設計、開発、およびデプロイする際に役立ちます。

- [Design (デザイン)](secure-design.md)
- [開発](secure-develop.md)
- [デプロイする](secure-deploy.md)
