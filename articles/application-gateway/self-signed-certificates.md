---
title: カスタム ルート CA を使用して自己署名証明書を生成する
titleSuffix: Azure Application Gateway
description: カスタム ルート CA を使用して Azure Application Gateway の自己署名証明書を生成する方法について説明します
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 07/23/2019
ms.author: victorh
ms.openlocfilehash: 3cf4f2314c7de2b2f7d581faeea88fe3c3177e81
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975059"
---
# <a name="generate-an-azure-application-gateway-self-signed-certificate-with-a-custom-root-ca"></a>カスタム ルート CA を使用して Azure Application Gateway の自己署名証明書を生成する

Application Gateway v2 SKU では、バックエンド サーバーを許可するために、信頼されたルート証明書の使用を導入しています。 これにより、v1 SKU で必要だった認証証明書が不要になります。 この "*ルート証明書*" は、バックエンド証明書サーバーからの Base-64 エンコード X.509(.CER) 形式のルート証明書です。 サーバー証明書を発行したルート証明機関 (CA) が識別され、サーバー証明書が SSL 通信に使用されます。

Application Gateway はよく知られている CA (GoDaddy や DigiCert など) によって署名されている Web サイトの証明書を既定で信頼します。 その場合は、ルート証明書を明示的にアップロードする必要はありません。 詳細については、「[Application Gateway での SSL ターミネーションとエンド ツー エンド SSL の概要](ssl-overview.md)」を参照してください。 ただし、開発/テスト環境を所有していて、検証済みの CA 署名証明書を購入したくない場合は、独自のカスタム CA を作成し、それを使用して自己署名証明書を作成することができます。 

> [!NOTE]
> 自己署名証明書は、既定では信頼されないため、管理が困難になる可能性があります。 また、強力でない可能性のある旧式のハッシュや暗号スイートが使用される場合もあります。 セキュリティを強化するには、よく知られている証明機関によって署名された証明書を購入してください。

この記事では、次のことについて説明します。

- 独自のカスタム証明機関を作成する
- カスタム CA によって署名された自己署名証明書を作成する
- Application Gateway に自己署名ルート証明書をアップロードしてバックエンド サーバーを認証する

## <a name="prerequisites"></a>前提条件

- **Windows または Linux を実行しているコンピューター上の [OpenSSL](https://www.openssl.org/)** 

   証明書の管理に使用できるツールは他にもありますが、このチュートリアルでは OpenSSL を使用します。 OpenSSL は、Ubuntu など、多くの Linux ディストリビューションにバンドルされています。
- **Web サーバー**

   たとえば、Apache、IIS、NGINX などで証明書をテストします。

- **Application Gateway v2 SKU**
   
  既存のアプリケーション ゲートウェイがない場合は、次を参照してください: [クイックスタート:Azure Application Gateway による Web トラフィックのルーティング - Azure portal](quick-create-portal.md)。

## <a name="create-a-root-ca-certificate"></a>ルート CA 証明書を作成する

OpenSSL を使用してルート CA 証明書を作成します。

### <a name="create-the-root-key"></a>ルート キーを作成する

1. OpenSSL がインストールされているコンピューターにサインインし、次のコマンドを実行します。 これでパスワードで保護されたキーが作成されます。

   ```
   openssl ecparam -out contoso.key -name prime256v1 -genkey
   ```
1. プロンプトが表示されたら、強力なパスワードを入力します。 たとえば、9 文字以上の大文字、小文字、数字、記号の組み合わせです。

### <a name="create-a-root-certificate-and-self-sign-it"></a>ルート証明書を作成して自己署名する

1. 次のコマンドを使用して、CSR と証明書を生成します。

   ```
   openssl req -new -sha256 -key contoso.key -out contoso.csr

   openssl x509 -req -sha256 -days 365 -in contoso.csr -signkey contoso.key -out contoso.crt
   ```
   上のコマンドで、ルート証明書が作成されます。 これを使用して、サーバー証明書に署名します。

1. プロンプトが表示されたら、ルート キーのパスワード、カスタム CA (国、都道府県、組織、OU など) の組織情報、完全修飾ドメイン名 (発行者のドメイン) を入力します。

   ![ルート証明書の作成](media/self-signed-certificates/root-cert.png)

## <a name="create-a-server-certificate"></a>サーバー証明書を作成する

次に、OpenSSL を使用してサーバー証明書を作成します。

### <a name="create-the-certificates-key"></a>証明書のキーを作成する

次のコマンドを使用して、サーバー証明書のキーを生成します。

   ```
   openssl ecparam -out fabrikam.key -name prime256v1 -genkey
   ```

### <a name="create-the-csr-certificate-signing-request"></a>CSR (証明書署名要求) を生成する

CSR は、証明書を要求するときに CA に与えられる公開キーです。 CA は、この特定の要求に対して証明書を発行します。

> [!NOTE]
> サーバー証明書の CN (共通名) は、発行者のドメインと別のものである必要があります。 たとえば、この例では、発行者の CN は `www.contoso.com` で、サーバー証明書の CN は `www.fabrikam.com` です。


1. 次のコマンドを使用して CSR を生成します。

   ```
   openssl req -new -sha256 -key fabrikam.key -out fabrikam.csr
   ```

1. プロンプトが表示されたら、ルート キーのパスワードと、カスタム CA の組織情報を入力します:国、都道府県、組織、OU、および完全修飾ドメイン名。 これは Web サイトのドメインであり、発行者とは別のものにする必要があります。

   ![サーバー証明書](media/self-signed-certificates/server-cert.png)

### <a name="generate-the-certificate-with-the-csr-and-the-key-and-sign-it-with-the-cas-root-key"></a>CSR とキーを使用して証明書を生成し、CA のルート キーで署名する

1. 次のコマンドを使用して、証明書を作成します。

   ```
   openssl x509 -req -in fabrikam.csr -CA  contoso.crt -CAkey contoso.key -CAcreateserial -out fabrikam.crt -days 365 -sha256
   ```
### <a name="verify-the-newly-created-certificate"></a>新しく作成された証明書を検証する

1. 次のコマンドを使用して、CRT ファイルの出力を印刷し、その内容を検証します。

   ```
   openssl x509 -in fabrikam.crt -text -noout
   ```

   ![証明書の検証](media/self-signed-certificates/verify-cert.png)

1. ディレクトリ内のファイルを検証し、次のファイルがあることを確認します。

   - contoso.crt
   - contoso.key
   - fabrikam.crt
   - fabrikam.key

## <a name="configure-the-certificate-in-your-web-servers-ssl-settings"></a>Web サーバーの SSL 設定で証明書を構成する

Web サーバーで fabrikam.crt ファイルと fabrikam.key ファイルを使用して SSL を構成します。 Web サーバーで 2 つのファイルを受け取ることができない場合は、OpenSSL コマンドを使用して、単一の .pem ファイルまたは .pfx ファイルに結合することができます。

### <a name="iis"></a>IIS

証明書をインポートして IIS でサーバー証明書としてアップロードする方法については、「[[HOW TO] インポートした証明書を Windows Server 2003 の Web サーバーにインストールする方法](https://support.microsoft.com/help/816794/how-to-install-imported-certificates-on-a-web-server-in-windows-server)」をご覧ください。

SSL バインドの手順については、「[How to Set Up SSL on IIS 7](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#create-an-ssl-binding-1)」 (IIS 7 で SSL を設定する方法) を参照してください。

### <a name="apache"></a>Apache

次の構成は、Apache で [SSL 用に構成された仮想ホスト](https://cwiki.apache.org/confluence/display/HTTPD/NameBasedSSLVHosts)の例です。

```
<VirtualHost www.fabrikam:443>
      DocumentRoot /var/www/fabrikam
      ServerName www.fabrikam.com
      SSLEngine on
      SSLCertificateFile /home/user/fabrikam.crt
      SSLCertificateKeyFile /home/user/fabrikam.key
</VirtualHost>
```

### <a name="nginx"></a>NGINX

次の構成は、SSL 構成での [NGINX サーバー ブロック](https://nginx.org/docs/http/configuring_https_servers.html)の例です。

![SSL での NGINX](media/self-signed-certificates/nginx-ssl.png)

## <a name="access-the-server-to-verify-the-configuration"></a>サーバーにアクセスして構成を確認する

1. ご利用のマシンの信頼できるルート ストアにルート証明書を追加します。 Web サイトにアクセスするときに、証明書チェーン全体がブラウザーに表示されることを確認します。

   ![信頼されたルート証明書](media/self-signed-certificates/trusted-root-cert.png)

   > [!NOTE]
   > Web サーバー名 (この例では www.fabrikam.com ) が Web サーバーの IP アドレスを指すように DNS が構成されていることを前提としています。 そうでない場合は、[ホスト ファイル](https://answers.microsoft.com/en-us/windows/forum/all/how-to-edit-host-file-in-windows-10/7696f204-2aaf-4111-913b-09d6917f7f3d)を編集して名前を解決することができます。
1. Web サイトを参照し、ブラウザーのアドレス ボックスにあるロック アイコンをクリックして、サイトと証明書の情報を確認します。

## <a name="verify-the-configuration-with-openssl"></a>OpenSSL を使用して構成を検証する

OpenSSL を使用して証明書を検証することもできます。

```
openssl s_client -connect localhost:443 -servername www.fabrikam.com -showcerts
```

![OpenSSL 証明書の検証](media/self-signed-certificates/openssl-verify.png)

## <a name="upload-the-root-certificate-to-application-gateways-http-settings"></a>ルート証明書を Application Gateway の HTTP 設定にアップロードする

Application Gateway に証明書をアップロードするには、.crt 証明書を Base-64 エンコード形式の .cer にエクスポートする必要があります。 .crt には既に Base-64 エンコード形式の公開キーが含まれているため、ファイル拡張子の名前を .crt から .cer に変更するだけです。 

### <a name="azure-portal"></a>Azure ポータル

信頼されたルート証明書を portal からアップロードするには、 **[HTTP 設定]** を選択し、 **[HTTPS]** プロトコルを選択します。

![portal を使用して証明書を追加する](media/self-signed-certificates/portal-cert.png)

### <a name="azure-powershell"></a>Azure PowerShell

Azure CLI または Azure PowerShell を使用して、ルート証明書をアップロードすることもできます。 次のコードは Azure PowerShell のサンプルです。

> [!NOTE]
> 次のサンプルでは、バックエンド プールとリスナーが既に存在すると仮定して、信頼されたルート証明書をアプリケーション ゲートウェイに追加し、新しい HTTP 設定を作成して、新しい規則を追加します。

```azurepowershell
## Add the trusted root certificate to the Application Gateway

$gw=Get-AzApplicationGateway -Name appgwv2 -ResourceGroupName rgOne

Add-AzApplicationGatewayTrustedRootCertificate `
   -ApplicationGateway $gw `
   -Name CustomCARoot `
   -CertificateFile "C:\Users\surmb\Downloads\contoso.cer"

$trustedroot = Get-AzApplicationGatewayTrustedRootCertificate `
   -Name CustomCARoot `
   -ApplicationGateway $gw

## Get the listener, backend pool and probe

$listener = Get-AzApplicationGatewayHttpListener `
   -Name basichttps `
   -ApplicationGateway $gw

$bepool = Get-AzApplicationGatewayBackendAddressPool `
  -Name testbackendpool `
  -ApplicationGateway $gw

Add-AzApplicationGatewayProbeConfig `
  -ApplicationGateway $gw `
  -Name testprobe `
  -Protocol Https `
  -HostName "www.fabrikam.com" `
  -Path "/" `
  -Interval 15 `
  -Timeout 20 `
  -UnhealthyThreshold 3

$probe = Get-AzApplicationGatewayProbeConfig `
  -Name testprobe `
  -ApplicationGateway $gw

## Add the configuration to the HTTP Setting and don’t forget to set the “hostname” field
## to the domain name of the server certificate as this will be set as the SNI header and
## will be used to verify the backend server’s certificate. Note that SSL handshake will
## fail otherwise and might lead to backend servers being deemed as Unhealthy by the probes

Add-AzApplicationGatewayBackendHttpSettings `
  -ApplicationGateway $gw `
  -Name testbackend `
  -Port 443 `
  -Protocol Https `
  -Probe $probe `
  -TrustedRootCertificate $trustedroot `
  -CookieBasedAffinity Disabled `
  -RequestTimeout 20 `
  -HostName www.fabrikam.com

## Get the configuration and update the Application Gateway

$backendhttp = Get-AzApplicationGatewayBackendHttpSettings `
  -Name testbackend `
  -ApplicationGateway $gw

Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $gw `
  -Name testrule `
  -RuleType Basic `
  -BackendHttpSettings $backendhttp `
  -HttpListener $listener `
  -BackendAddressPool $bepool

Set-AzApplicationGateway -ApplicationGateway $gw 
```
### <a name="verify-the-application-gateway-backend-health"></a>アプリケーション ゲートウェイのバックエンドの正常性を確認する

1. アプリケーションゲートウェイの **[バックエンド正常性]** ビューをクリックして、プローブが正常であるかどうかを確認します。
1.  HTTPS プローブの状態が**正常**であることがわかるはずです。

    ![HTTPS プローブ](media/self-signed-certificates/https-probe.png)

## <a name="next-steps"></a>次の手順

Application Gateway の SSL/TLS の詳細については、「[Application Gateway での SSL ターミネーションとエンド ツー エンド SSL の概要](ssl-overview.md)」を参照してください。

