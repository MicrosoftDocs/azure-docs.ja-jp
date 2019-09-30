---
title: App Service を使用した Azure Application Gateway のトラブルシューティング - App Service URL へのリダイレクト
description: この記事では、Azure Application Gateway を Azure App Service と共に使用した場合のリダイレクトの問題のトラブルシューティング方法について説明します。
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 07/19/2019
ms.author: absha
ms.openlocfilehash: 4b233117bc0f967368aeac7baec8c4875aa16826
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051426"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>Application Gateway での App Service に関する問題のトラブルシューティング

Azure Application Gateway と共に Azure App Service をバックエンド ターゲットとして使用するときに発生する問題を診断し、解決する方法について説明します。

## <a name="overview"></a>概要

この記事では、次の問題のトラブルシューティング方法について説明します。

> [!div class="checklist"]
> * リダイレクトが生じたときに App Service URL がブラウザーに公開されます。
> * App Service の ARRAffinity Cookie ドメインが元のホストではなく App Service のホスト名 (example.azurewebsites.net) に設定されます。

バックエンド アプリケーションでリダイレクト応答が送信されるとき、バックエンド アプリケーションで指定されているものとは別の URL にクライアントをリダイレクトしたい場合があります。 App Service が Application Gateway の背後でホストされており、クライアントに相対パスへのリダイレクトを行わせる必要があるときに、これを行うことがあります。 たとえば、contoso.azurewebsites.net/path1 から contoso.azurewebsites.net/path2 へのリダイレクトです。 

App Service では、リダイレクト応答を送信するとき、その応答の場所ヘッダーで、Application Gateway から受信した要求のものと同じホスト名が使用されます。 たとえば、クライアントでは、Application Gateway contoso.com/path2 を経由するのではなく、contoso.azurewebsites.net/path2 に直接要求を行います。 Application Gateway の迂回は望まれません。

この問題は次のような主な理由から発生することがあります。

- App Service でリダイレクトを構成しています。 要求の末尾にスラッシュを追加するだけでリダイレクトが実行される場合があります。
- 導入している Azure Active Directory 認証がリダイレクトを発生させます。

また、Application Gateway の背後で App Service を使用するとき、Application Gateway (example.com) に関連付けられているドメイン名が App Service のドメイン名と異なっています (たとえば、example.azurewebsites.net になっています)。 App Service によって設定された ARRAffinity Cookie のドメイン値では example.azurewebsites.net というドメイン名が保持されますが、それは望ましくありません。 Cookie のドメイン名の値は、元のホスト名 (example.com) である必要があります。

## <a name="sample-configuration"></a>サンプル構成

- HTTP リスナー:基本またはマルチサイト
- バックエンド アドレス プール:App Service
- HTTP 設定: **[Pick Hostname from Backend Address]\(バックエンド アドレスからホスト名を選択する\)** を有効にします
- プローブ: **[Pick Hostname from HTTP Settings]\(HTTP 設定からホスト名を選択する\)** を有効にします

## <a name="cause"></a>原因

App Service はマルチテナント サービスであるため、要求のホスト ヘッダーを使用して適切なエンドポイントに要求をルーティングします。 App Service の既定のドメイン名である *.azurewebsites.net (たとえば contoso.azurewebsites.net) は、Application Gateway のドメイン名 (たとえば contoso.com) とは異なります。 

クライアントからの元の要求には、ホスト名として Application Gateway のドメイン名 (contoso.com) があります。 App Service バックエンドに要求をルーティングするとき、元の要求にあるホスト名を App Service のホスト名に変更するように Application Gateway を構成する必要があります。 Application Gateway の HTTP 設定構成にある **[Pick Hostname from Backend Address]\(バックエンド アドレスからホスト名を選択する\)** というスイッチを使用します。 正常性プローブ構成にある **[Pick Hostname from Backend HTTP Settings]\(バックエンド HTTP 設定からホスト名を選択する\)** というスイッチを使用します。



![アプリケーション ゲートウェイでホスト名が変更される](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

App Service でリダイレクトが実行されるときは、特に構成されていない限り、場所ヘッダーには元のホスト名 "contoso.com" ではなくオーバーライドされたホスト名 "example.azurewebsites.net" が使用されます。 次の要求と応答のヘッダー例を参照してください。
```
## Request headers to Application Gateway:

Request URL: http://www.contoso.com/path

Request Method: GET

Host: www.contoso.com

## Response headers:

Status Code: 301 Moved Permanently

Location: http://contoso.azurewebsites.net/path/

Server: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=contoso.azurewebsites.net

X-Powered-By: ASP.NET
```
前の例では、応答ヘッダーにリダイレクトの状態コード 301 があることがわかります。 場所ヘッダーには、元のホスト名 www.contoso.com ではなく、App Service のホスト名があります。

## <a name="solution-rewrite-the-location-header"></a>解決方法:場所ヘッダーを書き換える

場所ヘッダーのホスト名を Application Gateway のドメイン名に設定します。 これを行うには、応答の場所ヘッダーに azurewebsites.net が含まれているかどうかを評価する条件で[書き換え規則](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)を作成します。 また、場所ヘッダーを書き換え、Application Gateway のホスト名を含めるようにする必要があります。 詳細は、[場所ヘッダーの書き換え方法](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#modify-a-redirection-url)に関する記事の手順を参照してください。

> [!NOTE]
> HTTP ヘッダーの書き換えのサポートは、Application Gateway の [Standard_v2 と WAF_v2 SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) でのみ利用できます。 v1 SKU を使用する場合、[v1 から v2 に移行する](https://docs.microsoft.com/azure/application-gateway/migrate-v1-v2)ことをお勧めします。 v2 SKU で利用できる書き換えやその他の[高度な機能](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#feature-comparison-between-v1-sku-and-v2-sku)を使用することがあります。

## <a name="alternate-solution-use-a-custom-domain-name"></a>代替ソリューション:カスタム ドメイン名の使用

v1 SKU を使用する場合、場所ヘッダーを書き換えることはできません。 この機能は v2 SKU でのみ利用できます。 リダイレクトの問題を解決するには、ホストのオーバーライドの代わりに、Application Gateway で受信されるのと同じホスト名を App Service に渡します。

(リダイレクトがある場合) App Service は、Application Gateway を指し、自ホストではない、元の同じホスト ヘッダーに対してリダイレクトを行うようになります。

カスタム ドメインを所有していることと、このプロセスに従うことが必要になります。

- App Service のカスタム ドメイン一覧にドメインを登録します。 App Service の FQDN を指す CNAME がカスタム ドメインに必要です。 詳細については、「[既存のカスタム DNS 名を Azure App Service にマップする](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain)」を参照してください。

    ![App Service のカスタムドメインの一覧](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- App Service では、ホスト名 www.contoso.com を受け取る準備ができています。 Application Gateway の FQDN を再び指すように DNS の CNAME を変更します。たとえば、appgw.eastus.cloudapp.azure.com にします。

- DNS クエリの実行時、ドメイン www.contoso.com が Application Gateway の FQDN に解決されることを確認します。

- **[Pick Hostname from Backend HTTP Settings]\(バックエンド HTTP 設定からホスト名を選択する\)** を無効にするようにカスタム プローブを設定します。 Azure portal で、プローブ設定でこのチェック ボックスをオフにします。 PowerShell では、**Set-AzApplicationGatewayProbeConfig** コマンドで **-PickHostNameFromBackendHttpSettings** スイッチを使用しないでください。 プローブのホスト名フィールドに、App Service の FQDN、example.azurewebsites.net を入力します。 Application Gateway から送信されたプローブ要求のホスト ヘッダーにこの FQDN が含まれます。

  > [!NOTE]
  > 次の手順のために、カスタム プローブがバックエンド HTTP 設定に関連付けられていないことを確認します。 HTTP 設定では、この時点で **[Pick Hostname from Backend Address]\(バックエンド アドレスからホスト名を選択する\)** スイッチはまだ有効です。

- **[Pick Hostname from Backend Address]\(バックエンド アドレスからホスト名を選択する\)** を無効にするように Application Gateway の HTTP 設定を指定します。 Azure portal でこのチェック ボックスをオフにします。 PowerShell では、**Set-AzApplicationGatewayBackendHttpSettings** コマンドで **-PickHostNameFromBackendAddress** スイッチを使用しないでください。

- カスタム プローブを元どおりバックエンドの HTTP 設定に関連付け、バックエンドが正常であることを確認します。

- Application Gateway からは同じホスト名 (www.contoso.com) が App Service に転送されるようになったはずです。 リダイレクトは同じホスト名で行われます。 次の要求と応答のヘッダー例を参照してください。

既存の構成に対し、PowerShell を使用して前述の手順を実装するには、下のサンプル PowerShell スクリプトを使用します。 Probe と HTTP Settings の構成に **-PickHostname** スイッチを使用していないことに注目してください。

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

前の手順で問題を解決できなかった場合、[サポート チケット](https://azure.microsoft.com/support/options/)を開きます。
