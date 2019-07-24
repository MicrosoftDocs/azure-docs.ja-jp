---
title: SSL オフロードの構成 - Azure Application Gateway - PowerShell クラシック | Microsoft Docs
description: この記事では、Azure クラシック デプロイ モデルを使用して、SSL オフロード用にアプリケーション ゲートウェイを作成する方法について説明します
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.assetid: 63f28d96-9c47-410e-97dd-f5ca1ad1b8a4
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: victorh
ms.openlocfilehash: 89a88d79b6b93a233dbd4f335d0eb449e49d5289
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "53001776"
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-classic-deployment-model"></a>クラシック デプロイ モデルを使用して SSL オフロード用にアプリケーション ゲートウェイを構成する

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-ssl-portal.md)
> * [Azure Resource Manager の PowerShell](application-gateway-ssl-arm.md)
> * [Azure クラシック PowerShell](application-gateway-ssl.md)
> * [Azure CLI](application-gateway-ssl-cli.md)

Azure Application Gateway をゲートウェイでの Secure Sockets Layer (SSL) セッションを停止するように構成し、Web ファーム上で発生するコストのかかる SSL 暗号化解除タスクを回避することができます。 また、SSL オフロードはフロントエンド サーバーのセットアップと Web アプリケーションの管理も簡素化します。

## <a name="before-you-begin"></a>開始する前に

1. Web Platform Installer を使用して、Azure PowerShell コマンドレットの最新バージョンをインストールします。 **ダウンロード ページ** の [Windows PowerShell](https://azure.microsoft.com/downloads/)セクションから最新バージョンをダウンロードしてインストールできます。
2. 有効なサブネットがある作業用の仮想ネットワークがあることを確認します。 仮想マシンまたはクラウドのデプロイメントでサブネットを使用していないことを確認します。 Application Gateway そのものが、仮想ネットワーク サブネットに含まれている必要があります。
3. アプリケーション ゲートウェイを使用するように構成するサーバーが存在している必要があります。または、それらのエンドポイントが仮想ネットワーク内に作成されているか、パブリック IP または仮想 IP アドレス (VIP) が割り当てられている必要があります。

アプリケーション ゲートウェイで SSL オフロードを構成するには、次の手順をそのままの順序で完了します。

1. [アプリケーション ゲートウェイの作成](#create-an-application-gateway)
2. [SSL 証明書のアップロード](#upload-ssl-certificates)
3. [ゲートウェイの構成](#configure-the-gateway)
4. [ゲートウェイ構成の設定](#set-the-gateway-configuration)
5. [ゲートウェイの起動](#start-the-gateway)
6. [ゲートウェイの状態の確認](#verify-the-gateway-status)

## <a name="create-an-application-gateway"></a>アプリケーション ゲートウェイの作成

ゲートウェイを作成するには、値を独自の値に置き換えて、`New-AzureApplicationGateway` コマンドレットを入力します。 この時点ではゲートウェイの課金は開始されません。 課金は後の手順でゲートウェイが正しく起動されたときに開始します。

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

ゲートウェイが作成されたことを確認するには、`Get-AzureApplicationGateway` コマンドレットを入力します。

サンプルでは、**Description**、**InstanceCount**、および **GatewaySize** は省略可能なパラメーターです。 **InstanceCount** の既定値は **2**、最大値は **10** です。 **GatewaySize** の既定値は **Medium** です。 その他の値は Small および Large です。 ゲートウェイがまだ起動していないため、**VirtualIPs** と **DnsName** は空白で表示されます。 これらの値は、ゲートウェイが実行中の状態になったときに作成されます。

```powershell
Get-AzureApplicationGateway AppGwTest
```

## <a name="upload-ssl-certificates"></a>SSL 証明書のアップロード

`Add-AzureApplicationGatewaySslCertificate` を入力して、サーバー証明書を PFX 形式でアプリケーション ゲートウェイにアップロードします。 証明書の名前はユーザーが指定し、アプリケーション ゲートウェイ内で一意である必要があります。 この証明書はアプリケーション ゲートウェイ上のすべての証明書管理操作でこの名前で呼ばれます。

次のサンプルは、このコマンドレットを示しています。 サンプルの値は実際の値に置き換えてください。

```powershell
Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>
```

次に、証明書のアップロードを検証します。 `Get-AzureApplicationGatewayCertificate` コマンドレットを入力します。

次のサンプルは、コマンドレットの 1 行目とその出力を示しています。

```powershell
Get-AzureApplicationGatewaySslCertificate AppGwTest
```

```
VERBOSE: 5:07:54 PM - Begin Operation: Get-AzureApplicationGatewaySslCertificate
VERBOSE: 5:07:55 PM - Completed Operation: Get-AzureApplicationGatewaySslCertificate
Name           : SslCert
SubjectName    : CN=gwcert.app.test.contoso.com
Thumbprint     : AF5ADD77E160A01A6......EE48D1A
ThumbprintAlgo : sha1RSA
State..........: Provisioned
```

> [!NOTE]
> 証明書のパスワードは、4 ～ 12 文字の英数字で指定する必要があります。 特殊文字は使用できません。

## <a name="configure-the-gateway"></a>ゲートウェイの構成

アプリケーション ゲートウェイの構成は、複数の値で構成されます。 値を相互に関連付けて構成を作成します。

値は次のとおりです。

* **バックエンド サーバー プール**: バックエンド サーバーの IP アドレスの一覧。 一覧の IP アドレスは、仮想ネットワークのサブネットに属しているか、パブリック IP または VIP アドレスである必要があります。
* **バックエンド サーバー プール設定**: すべてのプールには、ポート、プロトコル、cookie ベースのアフィニティなどの設定があります。 これらの設定はプールに関連付けられ、プール内のすべてのサーバーに適用されます。
* **フロントエンド ポート**: このポートは、アプリケーション ゲートウェイで開かれたパブリック ポートです。 このポートにトラフィックがヒットすると、バックエンド サーバーのいずれかにリダイレクトされます。
* **リスナー**: リスナーには、フロントエンド ポート、プロトコル (Http または Https で、値には大文字小文字の区別あり)、SSL 証明書名 (オフロードの SSL を構成する場合) があります。
* **ルール**: ルールはリスナーとバックエンド サーバー プールを結び付け、特定のリスナーにヒットしたときにトラフィックが送られるバックエンド サーバー プールを定義します。 現在、 *basic* ルールのみサポートされます。 *basic* ルールは、ラウンド ロビンの負荷分散です。

**構成に関する追加の注意**

SSL 証明書の構成では、 **HttpListener** のプロトコルを **Https** (大文字小文字の区別あり) に変更する必要があります。 **SslCertificate** 要素を、「[SSL 証明書のアップロード](#upload-ssl-certificates)」セクションで使用したのと同じ名前に設定された値を使用して、**HttpListener** に追加します。 フロントエンド ポートは **443** に更新する必要があります。

**Cookie ベースのアフィニティを有効にするには**: クライアント セッションからの要求が確実に Web ファーム内の同じ VM に送られるようにアプリケーション ゲートウェイを構成できます。 これを実現するには、ゲートウェイがトラフィックを適切に送ることを可能にするセッション Cookie を挿入します。 Cookie ベースのアフィニティを有効にするには、**BackendHttpSettings** 要素で **CookieBasedAffinity** を **Enabled** に設定します。

構成は、構成オブジェクトを作成するか、構成 XML ファイルを使用して構築できます。
構成 XML ファイルを使用して構成を構築するには、次のサンプルを入力します。


```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationGatewayConfiguration xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendIPConfigurations />
    <FrontendPorts>
        <FrontendPort>
            <Name>FrontendPort1</Name>
            <Port>443</Port>
        </FrontendPort>
    </FrontendPorts>
    <BackendAddressPools>
        <BackendAddressPool>
            <Name>BackendPool1</Name>
            <IPAddresses>
                <IPAddress>10.0.0.1</IPAddress>
                <IPAddress>10.0.0.2</IPAddress>
            </IPAddresses>
        </BackendAddressPool>
    </BackendAddressPools>
    <BackendHttpSettingsList>
        <BackendHttpSettings>
            <Name>BackendSetting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        </BackendHttpSettings>
    </BackendHttpSettingsList>
    <HttpListeners>
        <HttpListener>
            <Name>HTTPListener1</Name>
            <FrontendPort>FrontendPort1</FrontendPort>
            <Protocol>Https</Protocol>
            <SslCert>GWCert</SslCert>
        </HttpListener>
    </HttpListeners>
    <HttpLoadBalancingRules>
        <HttpLoadBalancingRule>
            <Name>HttpLBRule1</Name>
            <Type>basic</Type>
            <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
            <Listener>HTTPListener1</Listener>
            <BackendAddressPool>BackendPool1</BackendAddressPool>
        </HttpLoadBalancingRule>
    </HttpLoadBalancingRules>
</ApplicationGatewayConfiguration>
```

## <a name="set-the-gateway-configuration"></a>ゲートウェイ構成の設定

次に、Application Gateway を設定します。 構成オブジェクトまたは構成 XML ファイルのいずれの場合でも、`Set-AzureApplicationGatewayConfig` コマンドレットを入力できます。

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml
```

## <a name="start-the-gateway"></a>ゲートウェイの起動

ゲートウェイが構成されたら、`Start-AzureApplicationGateway` コマンドレットを入力してゲートウェイを起動します。 Application Gateway の課金は、ゲートウェイが正常に起動された後に開始します。

> [!NOTE]
> `Start-AzureApplicationGateway` コマンドレットは、終了するまで 15 ～ 20 分かかる可能性があります。
>
>

```powershell
Start-AzureApplicationGateway AppGwTest
```

## <a name="verify-the-gateway-status"></a>ゲートウェイの状態の確認

`Get-AzureApplicationGateway` コマンドレットを入力してゲートウェイの状態を確認します。 前の手順で `Start-AzureApplicationGateway` が成功した場合、**State** は **Running** になり、**VirtualIPs** と **DnsName**に有効な値が入ります。

次のサンプルは、起動に成功し、実行中で、トラフィックを受け取る準備が完了しているアプリケーション ゲートウェイを示しています。

```powershell
Get-AzureApplicationGateway AppGwTest
```

```
Name          : AppGwTest2
Description   :
VnetName      : testvnet1
Subnets       : {Subnet-1}
InstanceCount : 2
GatewaySize   : Medium
State         : Running
VirtualIPs    : {23.96.22.241}
DnsName       : appgw-4c960426-d1e6-4aae-8670-81fd7a519a43.cloudapp.net
```

## <a name="next-steps"></a>次の手順

負荷分散のオプション全般の詳細については、次を参照してください。

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure の Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)
