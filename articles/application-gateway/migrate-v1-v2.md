---
title: Azure Application Gateway と Web アプリケーション ファイアウォールを v1 から v2 に移行する
description: この記事では、Azure Application Gateway と Web アプリケーション ファイアウォールを v1 から v2 に移行する方法を説明します
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: 0fd605d7d502970dccd37da1f3f70fdadb1094a1
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550445"
---
# <a name="migrate-azure-application-gateway-and-web-application-firewall-from-v1-to-v2"></a>Azure Application Gateway と Web アプリケーション ファイアウォールを v1 から v2 に移行する

[Azure Application Gateway と Web アプリケーション ファイアウォール (WAF) v2](application-gateway-autoscaling-zone-redundant.md) の提供が開始され、自動スケールや可用性ゾーン冗長性などの追加機能をご利用いただけるようになりました。 ただし、既存の v1 ゲートウェイは、v2 に自動的にアップグレードされません。 v1 から v2 に移行する場合は、この記事の手順に従ってください。

移行には、次の 2 つの段階があります。

1. 構成の移行
2. クライアント トラフィックの移行

この記事では、構成の移行について説明します。 クライアント トラフィックの移行は、お客様に固有の環境によって異なります。 ただし、一般的な推奨事項を簡単に[説明しています](#migrate-client-traffic)。

## <a name="migration-overview"></a>移行の概要

次の処理を行う Azure PowerShell スクリプトが用意されています。

* 指定した仮想ネットワーク サブネットに新しい Standard_v2 または WAF_v2 ゲートウェイを作成する。
* v1 Standard または WAF ゲートウェイに関連付けられている構成を、新しく作成した Standard_V2 または WAF_V2 ゲートウェイにシームレスにコピーする。

### <a name="caveatslimitations"></a>注意事項と制限事項

* 新しい v2 ゲートウェイには、新しいパブリックおよびプライベート IP アドレスがあります。 既存の v1 ゲートウェイに関連付けられている IP アドレスを、v2 にシームレスに移動することはできません。 ただし、既存の (未割り当ての) パブリックまたはプライベート IP アドレスを、新しい v2 ゲートウェイに割り当てることはできます。
* v1 ゲートウェイが配置されている仮想ネットワーク内の別のサブネットの IP アドレス空間を指定する必要があります。 スクリプトでは、v1 ゲートウェイが既に存在する既存のサブネットに v2 ゲートウェイを作成することはできません。 ただし、既存のサブネットに既に v2 ゲートウェイがあり、十分な IP アドレス空間がある場合は、引き続き動作させることができます。
* SSL 構成を移行するには、v1 ゲートウェイで使用されているすべての SSL 証明書を指定する必要があります。
* v1 ゲートウェイに対して FIPS モードを有効にしている場合、それは新しい v2 ゲートウェイには移行されません。 FIPS モードは v2 ではサポートされません。
* v2 では IPv6 がサポートされていないため、IPv6 が有効になっている v1 ゲートウェイは移行されません。 スクリプトを実行しても、完了しない可能性があります。
* v1 ゲートウェイにプライベート IP アドレスのみがある場合、スクリプトでは、新しい v2 ゲートウェイ用のパブリック IP アドレスとプライベート IP アドレスが作成されます。 v2 ゲートウェイでは、現在、プライベート IP アドレスのみはサポートされていません。

## <a name="download-the-script"></a>スクリプトのダウンロード

[PowerShell ギャラリー](https://www.powershellgallery.com/packages/AzureAppGWMigration)から移行スクリプトをダウンロードします。

## <a name="use-the-script"></a>スクリプトの使用

ローカルの PowerShell 環境のセットアップと設定に応じて、次の 2 つのオプションがあります。

* Azure Az モジュールがインストールされていない場合、または Azure Az モジュールをアンインストールしてもかまわない場合、最善の方法は `Install-Script` オプションを使用してスクリプトを実行することです。
* Azure Az モジュールを保持する必要がある場合は、スクリプトをダウンロードして直接実行するのが最善の方法です。

Azure Az モジュールがインストールされているかどうかを確認するには、`Get-InstalledModule -Name az` を実行します。 インストールされている Az モジュールが見つからなかった場合は、`Install-Script` メソッドを使用できます。

### <a name="install-using-the-install-script-method"></a>Install-Script メソッドを使用してインストールする

このオプションを使用するには、コンピューターに Azure Az モジュールがインストールされていないことが必要です。 インストールされている場合、次のコマンドにはエラーが表示されます。 Azure Az モジュールをアンインストールするか、もう 1 つのオプションであるスクリプトを手動でダウンロードして実行する方法を使用します。
  
次のコマンドを実行して、スクリプトを実行します。

`Install-Script -Name AzureAppGWMigration`

このコマンドでは、必要な Az モジュールもインストールされます。  

### <a name="install-using-the-script-directly"></a>スクリプトを使用して直接インストールする

Azure Az モジュールがインストールされていて、それらをアンインストールできない (またはそれらをアンインストールしたくない) 場合は、スクリプトのダウンロード リンクの **[Manual Download]\(手動ダウンロード\)** タブを使用して、手動でスクリプトをダウンロードすることができます。 スクリプトは、生の nupkg ファイルとしてダウンロードされます。 この nupkg ファイルからスクリプトをインストールするには、「[パッケージの手動ダウンロード](https://docs.microsoft.com/powershell/gallery/how-to/working-with-packages/manual-download)」を参照してください。

スクリプトを実行するには、次の手順を実行します。

1. `Connect-AzAccount` を使用して Azure に接続します。

1. `Import-Module Az` を使用して Az モジュールをインポートします。

1. `Get-Help AzureAppGWMigration.ps1` を実行して必要なパラメーターを調べます。

   ```
   AzureAppGwMigration.ps1
    -resourceId <v1 application gateway Resource ID>
    -subnetAddressRange <subnet space you want to use>
    -appgwName <string to use to append>
    -sslCertificates <comma-separated SSLCert objects as above>
    -trustedRootCertificates <comma-separated Trusted Root Cert objects as above>
    -privateIpAddress <private IP string>
    -publicIpResourceName <public IP name string>
    -validateMigration -enableAutoScale
   ```

   スクリプトのパラメーターは次のとおりです。
   * **resourceId: [String]:必須** - 既存の Standard v1 または WAF v1 ゲートウェイの Azure リソース ID です。 この文字列の値を調べるには、Azure portal に移動し、アプリケーション ゲートウェイまたは WAF リソース選択して、ゲートウェイの **[プロパティ]** リンクをクリックします。 そのページにリソース ID があります。

     次の Azure PowerShell コマンドを実行してリソース ID を取得することもできます。

     ```azurepowershell
     $appgw = Get-AzApplicationGateway -Name <v1 gateway name> -ResourceGroupName <resource group Name> 
     $appgw.Id
     ```

   * **subnetAddressRange: [String]:必須** - 新しい v2 ゲートウェイを含む新しいサブネットに割り当てた (または割り当てる) IP アドレス空間です。 これは、CIDR 表記で指定する必要があります。 例: 10.0.0.0/24。 このサブネットを事前に作成しておく必要はありません。 存在しない場合はスクリプトによって作成されます。
   * **appgwName: [String]:省略可能**。 新しい Standard_v2 または WAF_v2 ゲートウェイの名前として使用するように指定する文字列です。 このパラメーターが指定されていない場合、サフィックス *_v2* が付加された既存の v1 ゲートウェイの名前が使用されます。
   * **sslCertificates: [PSApplicationGatewaySslCertificate]:省略可能**。  新しい v2 ゲートウェイにアップロードする必要がある、v1 ゲートウェイの SSL 証明書を表すために作成する PSApplicationGatewaySslCertificate オブジェクトのコンマ区切りの一覧です。 Standard v1 または WAF v1 ゲートウェイ用に構成された SSL 証明書のそれぞれに対して、次に示す `New-AzApplicationGatewaySslCertificate` コマンドを使用して新しい PSApplicationGatewaySslCertificate オブジェクトを作成することができます。 SSL 証明書ファイルへのパスとパスワードが必要です。

       このパラメーターは、v1 ゲートウェイまたは WAF 用に構成された HTTPS リスナーがない場合は省略可能です。 HTTPS リスナーのセットアップが少なくとも 1 つある場合、このパラメーターを指定する必要があります。

      ```azurepowershell  
      $password = ConvertTo-SecureString <cert-password> -AsPlainText -Force
      $mySslCert1 = New-AzApplicationGatewaySslCertificate -Name "Cert01" `
        -CertificateFile <Cert-File-Path-1> `
        -Password $password 
      $mySslCert2 = New-AzApplicationGatewaySslCertificate -Name "Cert02" `
        -CertificateFile <Cert-File-Path-2> `
        -Password $password
      ```

      スクリプトでは前の例の `$mySslCert1, $mySslCert2` (コンマ区切り) をこのパラメーターの値として渡すことができます。
   * **trustedRootCertificates: [PSApplicationGatewayTrustedRootCertificate]:省略可能**。 v2 ゲートウェイのバックエンド インスタンスの認証用の[信頼されたルート証明書](ssl-overview.md)を表すために作成する、PSApplicationGatewayTrustedRootCertificate オブジェクトのコンマ区切りの一覧です。  

      PSApplicationGatewayTrustedRootCertificate オブジェクトの一覧を作成するには、「[New-AzApplicationGatewayTrustedRootCertificate](https://docs.microsoft.com/powershell/module/Az.Network/New-AzApplicationGatewayTrustedRootCertificate?view=azps-2.1.0&viewFallbackFrom=azps-2.0.0)」を参照してください。
   * **privateIpAddress: [String]:省略可能**。 新しい v2 ゲートウェイに関連付ける特定のプライベート IP アドレスです。  新しい v2 ゲートウェイに割り当てる同じ VNet のものである必要があります。 これが指定されていない場合、スクリプトによって v2 ゲートウェイにプライベート IP アドレスが割り当てられます。
    * **publicIpResourceId: [String]:省略可能**。 新しい v2 ゲートウェイに割り当てる、サブスクリプション内のパブリック IP アドレス (Standard SKU) リソースのリソース ID です。 これが指定されていない場合、スクリプトによって同じリソース グループ内の新しいパブリック IP が割り当てられます。 名前は、v2 ゲートウェイの名前に *-IP* が付加されたものになります。
   * **validateMigration: [switch]:省略可能**。 v2 ゲートウェイの作成と構成のコピーが完了した後に、スクリプトで基本的な構成比較検証を実行する場合は、このパラメーターを使用します。 既定では、検証は行われません。
   * **enableAutoScale: [switch]:省略可能**。 スクリプトで新しい v2 ゲートウェイを作成した後に自動スケールを有効にする場合は、このパラメーターを使用します。 既定では、自動スケールは無効です。 これは、新しく作成した v2 ゲートウェイで、後からいつでも手動で有効にすることができます。

1. 適切なパラメーターを使用してスクリプトを実行します。 完了するまで 5 から 7 分かかることがあります。

    **例**

   ```azurepowershell
   AzureAppGWMigration.ps1 `
      -resourceId /subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/applicationGateways/myv1appgateway `
      -subnetAddressRange 10.0.0.0/24 `
      -appgwname "MynewV2gw" `
      -sslCertificates $Certs `
      -trustedRootCertificates $trustedCert `
      -privateIpAddress "10.0.0.1" `
      -publicIpResourceId "MyPublicIP" `
      -validateMigration -enableAutoScale
   ```

## <a name="migrate-client-traffic"></a>クライアント トラフィックの移行

まず、スクリプトで v1 ゲートウェイから移行された正確な構成で v2 の新しいゲートウェイが正常に作成されたことを再確認します。 この確認は Azure portal から行うことができます。

また、手動テストとして少量のトラフィックを v2 ゲートウェイ経由で送信します。
  
次に、現在のアプリケーション ゲートウェイ (Standard) がクライアントのトラフィックを受信するシナリオをいくつか示し、それぞれに対する推奨事項を紹介します。

* **(A レコードを使用して) Standard v1 または WAF v1 ゲートウェイに関連付けられているフロントエンド IP アドレスを指すカスタム DNS ゾーン (例: contoso.com)** 。

    Standard_v2 アプリケーション ゲートウェイに関連付けられているフロントエンド IP または DNS ラベルを指すように、DNS レコードを更新することができます。 DNS レコードに構成された TTL に応じて、すべてのクライアント トラフィックが新しい v2 ゲートウェイに移行されるまでに時間がかかる場合があります。
* **(CNAME レコードを使用して) v1 ゲートウェイに関連付けられた DNS ラベル (例: *myappgw.eastus.cloudapp.azure.com*) を指すカスタム DNS ゾーン (例: contoso.com)** 。

   次の 2 つの選択肢があります。

  * アプリケーション ゲートウェイでパブリック IP アドレスを使用している場合は、Traffic Manager プロファイルを使用してコントロールおよび細分化された移行を行い、トラフィックを段階的に新しい v2 ゲートウェイにルーティングすることができます (重み付けによるトラフィック ルーティング方法)。

    これを行うには、v1 と v2 の両方のアプリケーション ゲートウェイの DNS ラベルを [Traffic Manager プロファイル](../traffic-manager/traffic-manager-routing-methods.md#weighted-traffic-routing-method)に追加し、カスタム DNS レコード (例: www.contoso.com) を Traffic Manager ドメイン (例: contoso.trafficmanager.net) に CNAME します。
  * または、新しい v2 アプリケーション ゲートウェイの DNS ラベルを指すように、カスタム ドメインの DNS レコードを更新することができます。 DNS レコードに構成された TTL に応じて、すべてのクライアント トラフィックが新しい v2 ゲートウェイに移行されるまでに時間がかかる場合があります。
* **クライアントがアプリケーション ゲートウェイのフロントエンド IP アドレスに接続する**。

   新しく作成した v2 アプリケーション ゲートウェイに関連付けられている IP アドレスを使用するように、クライアントを更新します。 IP アドレスを直接使用しないことをお勧めします。 独自のカスタム DNS ゾーン (例: contoso.com) に CNAME できる、アプリケーション ゲートウェイに関連付けられた DNS 名ラベル (例: yourgateway.eastus.cloudapp.azure.com) を使用することを検討してください。

## <a name="common-questions"></a>一般的な質問

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Azure PowerShell スクリプトで v1 から v2 に構成を移行するにあたり、何か制限事項はありますか?

はい。 「[注意事項と制限事項](#caveatslimitations)」をご覧ください。

### <a name="is-this-article-and-the-azure-powershell-script-applicable-for-application-gateway-waf-product-as-well"></a>この記事の内容と Azure PowerShell スクリプトは、Application Gateway WAF 製品にも適用されますか? 

はい。

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-v1-gateway-to-the-newly-created-v2-gateway"></a>Azure PowerShell スクリプトでは、v1 ゲートウェイから新しく作成した v2 ゲートウェイにトラフィックを切り替えることもできますか?

いいえ。 Azure PowerShell スクリプトで移行されるのは構成のみです。 実際のトラフィックの移行は、お客様ご自身の責任において行っていただく必要があります。

### <a name="is-the-new-v2-gateway-created-by-the-azure-powershell-script-sized-appropriately-to-handle-all-of-the-traffic-that-is-currently-served-by-my-v1-gateway"></a>Azure PowerShell スクリプトで作成した新しい v2 ゲートウェイのサイズは、現在 v1 ゲートウェイで対応しているすべてのトラフィックを処理するのに適切な大きさになりますか?

Azure PowerShell スクリプトにより、既存の v1 ゲートウェイのトラフィックの処理に適したサイズで新しい v2 ゲートウェイが作成されます。 既定では自動スケールが無効になっていますが、スクリプトを実行するときに自動スケールを有効にすることができます。

### <a name="i-configured-my-v1-gateway--to-send-logs-to-azure-storage-does-the-script-replicate-this-configuration-for-v2-as-well"></a>Azure Storage にログを送信するように v1 ゲートウェイを構成しました。 スクリプトによって、この構成も v2 用にレプリケートされますか?

いいえ。 スクリプトでは、この構成は v2 用にレプリケートされません。 ログ構成は、移行後の v2 ゲートウェイに個別に追加する必要があります。

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>このスクリプトの使用中に問題が発生しました。 どこに問い合わせればよいですか?
  
appgwmigrationsup@microsoft.com 宛に電子メールを送信する方法と、Azure サポートでサポート ケースを開く方法があります (両方利用できます)。

## <a name="next-steps"></a>次の手順

[Application Gateway v2 について](application-gateway-autoscaling-zone-redundant.md)
