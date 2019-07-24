---
title: App Service を使用した Azure Application Gateway のトラブルシューティング - App Service の URL へのリダイレクト
description: この記事では、Azure Application Gateway を Azure App Service と共に使用した場合のリダイレクトの問題のトラブルシューティング方法について説明します。
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/22/2019
ms.author: absha
ms.openlocfilehash: f456cfec82a315a2be877a52e4f3f1850b992736
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797791"
---
# <a name="troubleshoot-application-gateway-with-app-service"></a>Application Gateway と App Service のトラブルシューティング

Application Gateway とバックエンド サーバーとしての App Service で発生した問題を診断して解決する方法について説明します。

## <a name="overview"></a>概要

この記事では、次の問題のトラブルシューティング方法について説明します。

> [!div class="checklist"]
> * リダイレクトが生じたときに App Service の URL がブラウザーに公開される
> * App Service の ARRAffinity Cookie ドメインが元のホストではなく App Service のホスト名 (example.azurewebsites.net) に設定される

Application Gateway のバックエンド プールで公開される App Service を構成し、アプリケーション コードでリダイレクトを構成した場合、Application Gateway にアクセスすると、ブラウザーから App Service の URL に直接リダイレクトされることがあります。

この問題が発生する主な理由としては、次のことが考えられます。

- App Service 上でリダイレクトが構成されています。 要求の末尾にスラッシュを追加するだけでリダイレクトが実行される場合があります。
- Azure AD 認証が構成されており、これが原因でリダイレクトが実行されています。
- Application Gateway の HTTP 設定で [バックエンド アドレスからホスト名を選択します] スイッチを有効にしています。
- カスタム ドメインを App Service に登録していません。

また、Application Gateway の内側で App Services を使用していて、かつカスタム ドメインを使用して Application Gateway にアクセスしている場合、App Service によって設定される ARRAffinity Cookie のドメイン値に "example.azurewebsites.net" というドメイン名が反映されることがわかります。 元のホスト名を Cookie ドメインとしても使用したい場合は、この記事の解決策に従ってください。

## <a name="sample-configuration"></a>サンプル構成

- HTTP リスナー:基本またはマルチサイト
- バックエンド アドレス プール:App Service
- HTTP 設定:[Pick Hostname from Backend Address]\(バックエンド アドレスからホスト名を選択する\) を有効にします
- プローブ:[Pick Hostname from HTTP Settings]\(HTTP 設定からホスト名を選択する\) を有効にします

## <a name="cause"></a>原因

App Service にアクセスするには、カスタム ドメイン設定で構成されたホスト名を使用する必要があります。既定値は "example.azurewebsites.net" です。App Service に登録されていないホスト名、または Application Gateway の FQDN で Application Gateway を使用する App Service にアクセスする場合、元の要求のホスト名をオーバーライドして、App Service のホスト名にする必要があります。

Application Gateway でこれを実現するために、ここでは [Pick Hostname from HTTP Settings]\(HTTP 設定からホスト名を選択する\) スイッチを使用します。また、プローブを機能させるために、プローブ構成で [Pick Hostname from Backend HTTP Settings]\(バックエンド HTTP 設定からホスト名を選択する\) を使用します。

![appservice-1](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

このため、App Service がリダイレクトを行うときは、特に構成されていない限り、Location ヘッダーには元のホスト名ではなくホスト名 "example.azurewebsites.net" が使用されます。 以下の要求と応答のヘッダー例を参照してください。
```
## Request headers to Application Gateway:

Request URL: http://www.contoso.com/path

Request Method: GET

Host: www.contoso.com

## Response headers:

Status Code: 301 Moved Permanently

Location: http://example.azurewebsites.net/path/

Server: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=example.azurewebsites.net

X-Powered-By: ASP.NET
```
上の例で、応答ヘッダーにはリダイレクト用の状態コード 301 があり、location ヘッダーには元のホスト名 "www.contoso.com" ではなく App Service のホスト名があります。

## <a name="solution"></a>解決策

この問題はアプリケーション側にリダイレクトを設定しないことで解決できますが、それが不可能な場合は、ホストのオーバーライドを実行するのではなく、Application Gateway が受信に使用するものと同じホスト ヘッダーを App Service にも渡す必要があります。

これを実行すると、(リダイレクトがある場合) App Service は、Application Gateway を指し、自ホストではない、元の同じホスト ヘッダーに対してリダイレクトを行います。

これを実現するには、カスタム ドメインを所有し、以下の手順を実行する必要があります。

- ドメインを App Service のカスタム ドメイン リストに登録します。 そのためには、カスタム ドメインに App Service の FQDN を指す CNAME が必要です。 詳細については、「[既存のカスタム DNS 名を Azure App Service にマップする](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain)」を参照してください。

![appservice-2](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- それが完了すると、App Service でホスト名 "www.contoso.com" を受け入れる準備は完了です。 次は、DNS の CNAME エントリを変更して Application Gateway の FQDN を指すようにします。 たとえば、"appgw.eastus.cloudapp.azure.com" です。

- DNS クエリを実行するときに、ドメイン "www.contoso.com" が Application Gateway の FQDN に解決されることを確認します。

- [Pick Hostname from Backend HTTP Settings]\(バックエンド HTTP 設定からホスト名を選択する\) を無効にするようにカスタム プローブを設定します。 ポータルのプローブ設定でチェックボックスをオフにして、PowerShell の Set-AzApplicationGatewayProbeConfig コマンドで -PickHostNameFromBackendHttpSettings スイッチを使用しないことで、これを実行できます。 プローブのホスト名フィールドに App Service の FQDNである「example.azurewebsites.net」を入力します。これは、Application Gateway から送信されたプローブ要求のホスト ヘッダーで送信されるためです。

  > [!NOTE]
  > この時点では、HTTP 設定でまだ [Pick Hostname from Backend Address]\(バックエンド アドレスからホスト名を選択する\) スイッチが有効なため、次の手順を実行するときに、カスタム プローブがバックエンドの HTTP 設定に関連付けられていないことを確認してください。

- [Pick Hostname from Backend Address]\(バックエンド アドレスからホスト名を選択する\) を無効にするように Application Gateway の HTTP 設定を指定します。 ポータルでチェックボックスをオフにして、PowerShell の Set-AzApplicationGatewayBackendHttpSettings コマンドで -PickHostNameFromBackendAddress スイッチを使用しないことで、これを実行できます。

- カスタム プローブを元どおりバックエンドの HTTP 設定に関連付け、バックエンドの正常性に問題がないことを確認します。

- これが完了すると、Application Gateway は同じホスト名 "www.contoso.com" を App Service に転送し、同じホスト名でリダイレクトが実行されます。 以下の要求と応答のヘッダー例を参照してください。

既存の構成に対し、PowerShell を使用して前述の手順を実装するには、下のサンプル PowerShell スクリプトに従ってください。 Probe と HTTP Settings の構成に -PickHostname スイッチを使用していないことに注目してください。

```azurepowershell-interactive
$gw=Get-AzApplicationGateway -Name AppGw1 -ResourceGroupName AppGwRG
Set-AzApplicationGatewayProbeConfig -ApplicationGateway $gw -Name AppServiceProbe -Protocol Http -HostName "example.azurewebsites.net" -Path "/" -Interval 30 -Timeout 30 -UnhealthyThreshold 3
$probe=Get-AzApplicationGatewayProbeConfig -Name AppServiceProbe -ApplicationGateway $gw
Set-AzApplicationGatewayBackendHttpSettings -Name appgwhttpsettings -ApplicationGateway $gw -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 30
Set-AzApplicationGateway -ApplicationGateway $gw
```
  ```
  ## Request headers to Application Gateway:

  Request URL: http://www.contoso.com/path

  Request Method: GET

  Host: www.contoso.com

  ## Response headers:

  Status Code: 301 Moved Permanently

  Location: http://www.contoso.com/path/

  Server: Microsoft-IIS/10.0

  Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=www.contoso.com

  X-Powered-By: ASP.NET
  ```
  ## <a name="next-steps"></a>次の手順

前の手順で問題を解決できない場合は、 [サポート チケット](https://azure.microsoft.com/support/options/)を開きます。
