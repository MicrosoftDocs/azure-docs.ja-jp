---
title: Azure Application Gateway のバックエンドの正常性に関する問題のトラブルシューティング
description: Azure Application Gateway のバックエンドの正常性に関する問題のトラブルシューティング方法について説明します
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 06/09/2020
ms.author: surmb
ms.openlocfilehash: 95b74e5fc6c5d2c09ff04b3f14e920ae675ab6e1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99592757"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>Application Gateway のバックエンドの正常性に関する問題のトラブルシューティング
==================================================

<a name="overview"></a>概要
--------

既定では、Azure Application Gateway は、バックエンド サーバーに対して probe を実行して、その正常性状態を確認すると共に、バックエンド サーバーが要求を処理する準備ができているかどうかを確認します。 ユーザーは、カスタム probe を作成して、ホスト名、probe の対象となるパス、および正常として受け入れられる状態コードを指定することもできます。 どちらのケースでも、バックエンド サーバーが正常に応答しない場合、Application Gateway はそのサーバーを "異常" とマークし、サーバーへの要求の転送を停止します。 サーバーが正常に応答し始めると、Application Gateway は要求の転送を再開します。

### <a name="how-to-check-backend-health"></a>バックエンドの正常性を確認する方法

バックエンド プールの正常性を確認するには、Azure portal の **[バックエンド正常性]** ページを使用できます。 また、[Azure PowerShell](/powershell/module/az.network/get-azapplicationgatewaybackendhealth)、[CLI](/cli/azure/network/application-gateway#az-network-application-gateway-show-backend-health)、または [REST API](/rest/api/application-gateway/applicationgateways/backendhealth) を使用することもできます。

これらのいずれかの方法で取得される状態は、次のいずれかになります。

- Healthy

- 異常

- Unknown

サーバーのバックエンドの正常性状態が正常の場合、Application Gateway が要求をそのサーバーに転送することを意味します。 一方、バックエンド プール内の全サーバーのバックエンドの正常性が異常または不明の場合、アプリケーションへアクセスしようとすると問題が発生する可能性があります。 この記事では、表示される各エラーの症状、原因、および解決策について説明します。

<a name="backend-health-status-unhealthy"></a>バックエンドの正常性状態:異常
-------------------------------

バックエンドの正常性状態が異常の場合、ポータル ビューは次のスクリーンショットのようになります。

![Application Gateway のバックエンドの正常性 - 異常](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

また、Azure PowerShell、CLI、または Azure REST API クエリを使用している場合は、次のような応答が返されます。
```azurepowershell
PS C:\Users\testuser\> Get-AzApplicationGatewayBackendHealth -Name "appgw1" -ResourceGroupName "rgOne"
BackendAddressPools :
{Microsoft.Azure.Commands.Network.Models.PSApplicationGatewayBackendHealthPool}
BackendAddressPoolsText : [
{
                              "BackendAddressPool": {
                                "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appgw1/b
                          ackendAddressPools/appGatewayBackendPool"
                              },
                              "BackendHttpSettingsCollection": [
                                {
                                  "BackendHttpSettings": {
                                    "TrustedRootCertificates": [],
                                    "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appg
                          w1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
                                  },
                                  "Servers": [
                                    {
                                      "Address": "10.0.0.5",
                                      "Health": "Healthy"
                                    },
                                    {
                                      "Address": "10.0.0.6",
                                      "Health": "Unhealthy"
                                    }
                                  ]
                                }
                              ]
                            }
                        ]
```
バックエンド プール内のすべてのサーバーについて、バックエンド サーバーの状態として "異常" が返される場合、要求はサーバーに転送されず、Application Gateway は 502 "ゲートウェイが不適切です" というエラーを要求元のクライアントに返します。 この問題のトラブルシューティングを行うには、 **[バックエンド正常性]** タブの **[詳細]** 列を確認します。

**[詳細]** 列に表示されるメッセージは問題に関する詳細な分析情報を示すため、これに基づいて問題のトラブルシューティングを開始できます。

> [!NOTE]
> 既定の probe 要求は、\<protocol\>://127.0.0.1:\<port\>/ の形式で送信されます。 たとえば、ポート80 における HTTP probe の場合は http://127.0.0.1:80 になります。 HTTP 状態コード 200 から 399 のみが正常と見なされます。 プロトコルと宛先ポートは、HTTP 設定から継承されます。 Application Gateway で別のプロトコル、ホスト名、またはパスに対して probe を実行し、他の状態コードを正常として認識させるには、カスタム probe を構成し、それを HTTP 設定に関連付けます。

<a name="error-messages"></a>エラー メッセージ
------------------------
#### <a name="backend-server-timeout"></a>バックエンド サーバーのタイムアウト

**メッセージ:** Time taken by the backend to respond to application gateway\'s health probe is more than the timeout threshold in the probe setting (バックエンドがアプリケーション ゲートウェイの正常性 probe に応答するのにかかる時間が、probe 設定のタイムアウトしきい値を超えています。)

**原因:** Application Gateway は、HTTP(S) probe 要求をバックエンド サーバーに送信した後、構成された期間にわたってバックエンド サーバーからの応答を待ちます。 バックエンドサーバーが、構成された期間 (タイムアウト値) 内に応答しない場合は、構成されたタイムアウト期間内に応答が再開されるまで、"異常" とマークされます。

**解決策:** 構成されたタイムアウト期間内にバックエンド サーバーまたはアプリケーションが応答しない理由を確認し、アプリケーションの依存関係も確認します。 たとえば、応答の遅延を引き起こす可能性がある問題がデータベースにあるかどうかを確認します。 アプリケーションの動作を把握したうえでアプリケーションがタイムアウト値の経過後にのみ応答するようにする必要がある場合は、カスタム probe 設定でタイムアウト値を引き上げます。 タイムアウト値を変更するには、カスタム probe が必要です。 カスタム probe を構成する方法については、[こちらのドキュメントのページ](./application-gateway-create-probe-portal.md)を参照してください。

タイムアウト値を引き上げるには、次の手順に従います。

1.  バックエンド サーバーに直接アクセスし、そのページでサーバーが応答するまでにかかった時間を確認します。 開発者ツールを使用するブラウザーなど、任意のツールを使用してバックエンド サーバーにアクセスできます。

1.  アプリケーションが応答するまでの時間を把握したら、 **[正常性プローブ]** タブを選択し、対象の HTTP 設定に関連付けられている probe を選択します。

1.  アプリケーションの応答時間を超える任意のタイムアウト値を秒単位で入力します。

1.  カスタム probe 設定を保存し、バックエンドの正常性が [正常] と表示されるかどうかを確認します。

#### <a name="dns-resolution-error"></a>DNS の解決エラー

**メッセージ:** Application Gateway could not create a probe for this backend. (Application Gateway でこのバックエンドに対する probe を作成できませんでした。) This usually happens when the FQDN of the backend has not been entered correctly. (これは、通常、バックエンドの FQDN が正しく入力されていない場合に発生します。) 

**原因:** バックエンド プールの種類が IP アドレス/FQDN または App Service の場合、Application Gateway は、ドメイン ネーム システム (DNS) (カスタムまたは Azure の既定) を使用して入力された FQDN の IP アドレスに解決し、HTTP 設定で指定されている TCP ポートでサーバーへの接続を試行します。 ただし、このメッセージが表示された場合は、Application Gateway が入力された FQDN の IP アドレスを正常に解決できなかったことを示しています。

**解決策:**

1.  バックエンド プールに入力された FQDN が正しいことと、それがパブリック ドメインであることを確認し、ローカル コンピューターから解決を試みます。

1.  IP アドレスを解決できる場合は、仮想ネットワークの DNS 構成に問題がある可能性があります。

1.  仮想ネットワークがカスタム DNS サーバーで構成されているかどうかを確認してください。 そのようになっている場合は、DNS サーバーが指定された FQDN の IP アドレスに解決されない理由を確認します。

1.  Azure の既定の DNS を使用している場合は、適切な A レコードまたは CNAME レコード マッピングが完了しているかどうかをドメイン名レジストラーで確認します。

1.  ドメインがプライベートまたは内部の場合は、同じ仮想ネットワーク内の VM から解決を試みます。 解決できる場合は、Application Gateway を再起動して、もう一度確認してください。 Application Gateway を再起動するには、次のリンク先のリソースで説明されている PowerShell コマンドを使用して、[停止](/powershell/module/azurerm.network/stop-azurermapplicationgateway)および[起動](/powershell/module/azurerm.network/start-azurermapplicationgateway)する必要があります。

#### <a name="tcp-connect-error"></a>TCP 接続エラー

**メッセージ:** Application Gateway could not connect to the backend. (Application Gateway がバックエンドに接続できませんでした。)
Please check that the backend responds on the port used for the probe. (バックエンドが probe に使用されるポートで応答することを確認してください。)
Also check whether any NSG/UDR/Firewall is blocking access to the Ip and port of this backend (また、NSG、UDR、またはファイアウォールによってこのバックエンドの IP とポートへのアクセスがブロックされているかどうかも確認してください)

**原因:** DNS 解決フェーズの後、Application Gateway は、HTTP 設定で構成されている TCP ポートでバックエンド サーバーへの接続を試行します。 Application Gateway が指定されたポートで TCP セッションを確立できない場合、probe はこのメッセージで "異常" とマークされます。

**解決方法:** このエラーが発生した場合は、次の手順を実行します。

1.  ブラウザーまたは PowerShell を使用して、HTTP 設定に指定されているポートでバックエンド サーバーに接続できるかどうかを確認します。 たとえば、`Test-NetConnection -ComputerName
    www.bing.com -Port 443` コマンドを実行します。

1.  指定されているポートが目的のポートではない場合は、Application Gateway がバックエンド サーバーに接続するための適切なポート番号を入力します

1.  そのポート経由でローカル コンピューターからも接続できない場合は、次のようにします。

    a.  バックエンド サーバーのネットワーク アダプターとサブネットのネットワーク セキュリティ グループ (NSG) 設定を調べて、構成されているポートへのインバウンド接続が許可されているかどうかを確認します。 許可されていない場合は、接続を許可する新しい規則を作成します。 NSG 規則の作成方法については、[こちらのドキュメント ページを参照](../virtual-network/tutorial-filter-network-traffic.md#create-security-rules)してください。

    b.  Application Gateway サブネットの NSG 設定で、接続を確立できるようにパブリックおよびプライベートのアウトバウンド トラフィックが許可されているかどうかを確認します。 NSG 規則を作成する方法の詳細については、手順 3a に示されているドキュメントのページを参照してください。
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  Application Gateway およびバックエンド サーバーのサブネットのユーザー定義ルート (UDR) 設定でルーティングの異常の有無を確認します。 UDR によってトラフィックがバックエンド サブネットの外へ送信される設定になっていないことを確認します。 たとえば、ネットワーク仮想アプライアンスへのルートや、Azure ExpressRoute および VPN 経由で Application Gateway サブネットにアドバタイズされる既定のルートを確認します。

    d.  ネットワーク アダプターの有効なルートと規則を確認するために、次の PowerShell コマンドを使用できます。
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
1.  NSG または UDR で問題が見つからない場合は、構成されているポートでクライアントが TCP セッションを確立するのを妨げるようなアプリケーション関連の問題がないかどうかを、バックエンド サーバーで確認します。 いくつかの確認事項を次に示します。

    a.  コマンド プロンプトを開き (Win+R -\> cmd)、「`netstat`」と入力して、Enter キーを押します。

    b.  構成されているポートでサーバーがリッスンしているかどうかを確認します。 次に例を示します。
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    c.  構成されているポートでリッスンしていない場合は、Web サーバーの設定を確認します。 たとえば、IIS のサイト バインド、NGINX のサーバー ブロック、Apache の仮想ホストなどです。

    d.  OS のファイアウォール設定を調べて、ポートへの受信トラフィックが許可されていることを確認します。

#### <a name="http-status-code-mismatch"></a>HTTP 状態コードの不一致

**メッセージ:** Status code of the backend\'s HTTP response did not match the probe setting. (バックエンドの HTTP 応答の状態コードが probe 設定と一致しませんでした。) Expected:{HTTPStatusCode0} Received:{HTTPStatusCode1}. (必要: {HTTPStatusCode0} 受信: {HTTPStatusCode1}。)

**原因:** TCP 接続が確立され、TLS ハンドシェイクが完了すると (TLS が有効な場合)、Application Gateway は probe を HTTP GET 要求としてバックエンド サーバーに送信します。 前述のように、既定の probe の対象は \<protocol\>://127.0.0.1:\<port\>/ であり、200 から 399 の範囲の応答状態コードは正常であると見なされます。 サーバーからそれ以外の状態コードが返された場合、そのサーバーはこのメッセージで "異常" とマークされます。

**解決方法:** バックエンド サーバーの応答コードに応じて、次の手順を実行できます。 一般的な状態コードをいくつか次に示します。

| **Error** | **アクション** |
| --- | --- |
| probe 状態コードの不一致: 401 を受信 | バックエンド サーバーで認証が必要かどうかを確認します。 Application Gateway の probe では、認証用の資格情報を渡すことはできません。 probe 状態コードの照合で \"HTTP 401\" を許可するか、サーバーが認証を必要としないパスに対して probe を実行します。 | |
| probe 状態コードの不一致: 403 を受信 | アクセスは禁止されています。 バックエンド サーバーでパスへのアクセスが許可されているかどうかを確認します。 | |
| probe 状態コードの不一致: 404 を受信 | ページが見つかりません。 バックエンド サーバーでホスト名のパスにアクセスできるかどうかを確認します。 ホスト名またはパス パラメーターをアクセス可能な値に変更します。 | |
| probe 状態コードの不一致: 405 を受信 | Application Gateway の probe 要求で HTTP GET メソッドが使用されています。 対象のサーバーでこのメソッドが許可されているかどうかを確認します。 | |
| probe 状態コードの不一致: 500 を受信 | 内部サーバー エラー。 バックエンド サーバーの正常性と、サービスが実行されているかどうかを確認します。 | |
| probe 状態コードの不一致: 503 を受信 | Service unavailable. (サービス利用不可。) バックエンド サーバーの正常性と、サービスが実行されているかどうかを確認します。 | |

または、応答が正当なものであると判断したうえで、Application Gateway が他の状態コードを正常として受け入れるようにする場合は、カスタム probe を作成できます。 この方法は、バックエンド Web サイトで認証が必要な場合に役立ちます。 プローブ要求にユーザーの資格情報が含まれないために要求が失敗し、バックエンド サーバーから HTTP 401 状態コードが返されます。

カスタム probe を作成するには、[こちらの手順](./application-gateway-create-probe-portal.md)に従います。

#### <a name="http-response-body-mismatch"></a>HTTP 応答本文の不一致

**メッセージ:** Body of the backend\'s HTTP response did not match the probe setting. (バックエンドの HTTP 応答の本文が probe 設定と一致しませんでした。) Received response body does not contain {string}. (受信した応答本文に {string} が含まれていません。)

**原因:** カスタム probe を作成する場合、応答本文の文字列と一致させることによってバックエンド サーバーを "正常" とマークすることもできます。 たとえば、一致する文字列として "unauthorized" を受け入れるように Application Gateway を構成できます。 probe 要求に対するバックエンド サーバーの応答に文字列 **unauthorized** が含まれている場合は、"正常" とマークされます。 それ以外の場合は、このメッセージで "異常" とマークされます。

**解決方法:** この問題を解決するには、次の手順に従ってください。

1.  バックエンド サーバーにローカルでアクセスするか、probe パス上のクライアント マシンからアクセスして、応答本文を確認します。

1.  Application Gateway のカスタム probe 構成で、応答本文が構成内容と一致するかどうかを確認します。

1.  それらが一致しない場合は、受け入れる適切な文字列値が含まれるように probe 構成を変更します。

[Application Gateway の probe の一致](./application-gateway-probe-overview.md#probe-matching)の詳細を確認します。

>[!NOTE]
> すべての TLS 関連のエラー メッセージについて、SNI の動作と、v1 と v2 の SKU 間の違いを確認するには、「[TLS の概要](ssl-overview.md)」ページを参照してください。


#### <a name="backend-server-certificate-invalid-ca"></a>バックエンド サーバーの証明書: 無効な CA

**メッセージ:** The server certificate used by the backend is not signed by a well-known Certificate Authority (CA). (バックエンドによって使用されるサーバー証明書が既知の証明機関 (CA) によって署名されていません。) Allow the backend on the Application Gateway by uploading the root certificate of the server certificate used by the backend. (バックエンドによって使用されるサーバー証明書のルート証明書をアップロードして、アプリケーション ゲートウェイでバックエンドを許可してください。)

**原因:** Application Gateway v2 でのエンドツーエンド SSL を使用するには、サーバーが正常であると判断するためにバックエンド サーバーの証明書を検証する必要があります。
TLS または SSL 証明書を信頼するには、そのバックエンド サーバーの証明書が、Application Gateway の信頼されたストアに含まれる CA によって発行されている必要があります。 証明書が信頼された CA によって発行されていない場合 (自己署名証明書が使用された場合など)、ユーザーは、発行者の証明書を Application Gateway にアップロードする必要があります。

**解決方法:** 信頼されたルート証明書をエクスポートして Application Gateway にアップロードするには、次の手順に従います。 (この手順は、Windows クライアント向けです。)

1.  対象のアプリケーションがホストされているマシンにサインインします。

1.  Win + R キーを押すか、 **[スタート]** を右クリックして **[実行]** を選択します。

1.  「`certmgr.msc`」と入力し、Enter キーを押します。 **[スタート]** メニューで証明書マネージャーを検索することもできます。

1.  証明書を見つけて開きます (通常は、`\Certificates - Current User\\Personal\\Certificates\` にあります)。

1.  ルート証明書を選択し、 **[証明書の表示]** を選択します。

1.  証明書のプロパティで、 **[詳細]** タブを選択します。

1.  **[詳細]** タブで、 **[ファイルへコピー]** オプションを選択し、ファイルを Base-64 エンコード X.509 (.CER) 形式で保存します。

1.  Azure portal で Application Gateway の **[HTTP 設定]** ページを開きます。

1. HTTP 設定を開き、 **[証明書の追加]** を選択して、先ほど保存した証明書ファイルを見つけます。

1. **[保存]** を選択して HTTP 設定を保存します。

または、ブラウザーを使用して (Application Gateway をバイパスして) サーバーに直接アクセスし、ブラウザーからルート証明書をエクスポートすることによって、クライアント マシンからルート証明書をエクスポートすることもできます。

Application Gateway で信頼されるルート証明書を抽出してアップロードする方法の詳細については、「[信頼されたルート証明書をエクスポートする (V2 SKU の場合)](./certificates-for-backend-authentication.md#export-trusted-root-certificate-for-v2-sku)」を参照してください。

#### <a name="trusted-root-certificate-mismatch"></a>信頼されたルート証明書の不一致

**メッセージ:** The root certificate of the server certificate used by the backend does not match the trusted root certificate added to the application gateway. (バックエンドによって使用されるサーバー証明書のルート証明書が、アプリケーション ゲートウェイに追加されている信頼されたルート証明書と一致しません。) Ensure that you add the correct root certificate to allowlist the backend. (バックエンドを許可リストに登録するために適切なルート証明書を追加していることを確認してください)

**原因:** Application Gateway v2 でのエンドツーエンド SSL を使用するには、サーバーが正常であると判断するためにバックエンド サーバーの証明書を検証する必要があります。
TLS または SSL 証明書を信頼するには、そのバックエンド サーバーの証明書が、Application Gateway の信頼されたストアに含まれる CA によって発行されている必要があります。 証明書が信頼された CA によって発行されていない場合 (自己署名証明書が使用された場合など)、ユーザーは、発行者の証明書を Application Gateway にアップロードする必要があります。

Application Gateway の HTTP 設定にアップロードされた証明書は、バックエンド サーバー証明書のルート証明書と一致している必要があります。

**解決方法:** このエラー メッセージが表示された場合は、Application Gateway にアップロードされた証明書とバックエンド サーバーにアップロードされた証明書が一致していません。

上記の方法の手順 1 から 11 に従って、正しい信頼されたルート証明書を Application Gateway にアップロードします。

Application Gateway で信頼されるルート証明書を抽出してアップロードする方法の詳細については、「[信頼されたルート証明書をエクスポートする (V2 SKU の場合)](./certificates-for-backend-authentication.md#export-trusted-root-certificate-for-v2-sku)」を参照してください。
> [!NOTE]
> このエラーは、バックエンド サーバーが TLS ハンドシェイク中に "ルート -> 中間 (該当する場合) -> リーフ" を含む証明書の完全なチェーンを交換していない場合にも発生する可能性があります。 確認するには、任意のクライアントから OpenSSL コマンドを使用し、Application Gateway probe に構成された設定を使用してバックエンド サーバーに接続します。

次に例を示します。
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
返される証明書の完全なチェーンが出力に表示されない場合は、ルート証明書を含む完全なチェーンと共に証明書を再度エクスポートします。 バックエンドサーバーでその証明書を構成します。 

```
  CONNECTED(00000188)\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=20:unable to get local issuer certificate\
  verify return:1\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=21:unable to verify the first certificate\
  verify return:1\
  \-\-\-\
  Certificate chain\
   0 s:/OU=Domain Control Validated/CN=*.example.com\
     i:/C=US/ST=Arizona/L=Scottsdale/O=GoDaddy.com, Inc./OU=http://certs.godaddy.com/repository//CN=Go Daddy Secure Certificate Authority - G2\
  \-----BEGIN CERTIFICATE-----\
  xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\
  \-----END CERTIFICATE-----
```

#### <a name="backend-certificate-invalid-common-name-cn"></a>バックエンド証明書の無効な共通名 (CN)

**メッセージ:** The Common Name (CN) of the backend certificate does not match the host header of the probe. (バックエンド証明書の共通名 (CN) が probe のホスト ヘッダーと一致しません。)

**原因:** Application Gateway は、バックエンドの HTTP 設定に指定されているホスト名が、バックエンド サーバーの TLS または SSL 証明書によって提示される CN のものと一致するかどうかを確認します。 これは、Standard_v2 SKU と WAF_v2 (V2) SKU における動作です。 Standard SKU と WAF SKU (v1) の Server Name Indication (SNI) は、バックエンド プール アドレスの FQDN として設定されます。 SNI の動作と、v1 と v2 の SKU 間の違いの詳細については、「[Application Gateway での TLS 終了とエンド ツー エンド TLS の概要](ssl-overview.md)」を参照してください。

v2 SKU では、既定の probe がある (カスタムの probe が構成および関連付けられていない) 場合、SNI は HTTP 設定に指定されているホスト名から設定されます。 または、HTTP 設定で [バックエンド アドレスからホスト名を選択します] が指定されている場合 (バックエンド アドレス プールに有効な FQDN が含まれる)、この設定が適用されます。

HTTP 設定に関連付けられているカスタム probe がある場合、SNI はカスタム probe 構成で指定されているホスト名から設定されます。 または、カスタム probe で **[Pick Hostname from Backend HTTP Settings]\(ホスト名をバックエンド HTTP 設定から選択します\)** が選択されている場合、SNI は HTTP 設定で指定されているホスト名から設定されます。

HTTP 設定で **[バックエンド アドレスからホスト名を選択します]** が設定されている場合、バックエンド アドレス プールには有効な FQDN が含まれている必要があります。

このエラー メッセージが表示される場合は、バックエンド証明書の CN が、カスタム probe または HTTP 設定 ( **[Pick Hostname from Backend HTTP Settings]\(ホスト名をバックエンド HTTP 設定から選択します\)** が選択されている場合) で構成されているホスト名と一致していません。 既定の probe を使用している場合、ホスト名は **127.0.0.1** に設定されます。 これが目的の値でない場合は、カスタム probe を作成し、HTTP 設定に関連付ける必要があります。

**解決方法:**

この問題を解決するには、次の手順に従ってください。

Windows の場合:

1.  対象のアプリケーションがホストされているマシンにサインインします。

1.  Win + R キーを押すか、 **[スタート]** を右クリックして **[実行]** を選択します。

1.  「**certmgr.msc**」と入力し、Enter キーを押します。 **[スタート]** メニューで証明書マネージャーを検索することもできます。

1.  証明書を見つけて開きます (通常は、`\Certificates - Current User\\Personal\\Certificates` にあります)。

1.  **[詳細]** タブで、証明書の **[サブジェクト]** を確認します。

1.  詳細から証明書の CN を確認し、これをカスタム probe または HTTP 設定 ( **[Pick Hostname from Backend HTTP Settings]\(ホスト名をバックエンド HTTP 設定から選択します\)** が選択されている場合) のホスト名フィールドに入力します。 これが対象の Web サイトの目的のホスト名ではない場合は、そのドメインの証明書を取得するか、カスタム probe または HTTP 設定の構成に正しいホスト名を入力する必要があります。

OpenSSL を使用する Linux の場合:

1.  OpenSSL で次のコマンドを実行します。
    ```
    openssl x509 -in certificate.crt -text -noout
    ```

2.  表示されたプロパティから証明書の CN を見つけ、HTTP 設定のホスト名フィールドに同じ値を入力します。 これが対象の Web サイトの目的のホスト名ではない場合は、そのドメインの証明書を取得するか、カスタム probe または HTTP 設定の構成に正しいホスト名を入力する必要があります。

#### <a name="backend-certificate-is-invalid"></a>バックエンド証明書が無効

**メッセージ:** Backend certificate is invalid. (バックエンド証明書が無効です。) Current date is not within the \"Valid from\" and \"Valid to\" date range on the certificate. (現在の日付が証明書の [有効期間の開始] と [有効期間の終了] の日付範囲内ではありません。)

**原因:** すべての証明書には有効期限の範囲があり、サーバーの TLS または SSL 証明書が有効でない限り、HTTPS 接続はセキュリティで保護されません。 現在の日付が **[有効期間の開始]** と **[有効期間の終了]** の範囲内にある必要があります。 そうでない場合、証明書は無効と見なされ、セキュリティ上の問題となります。この場合、Application Gateway によってバックエンド サーバーが "異常" とマークされます。

**解決方法:** TLS または SSL 証明書の有効期限が切れた場合は、ベンダーで証明書を更新し、新しい証明書を使用してサーバーの設定を更新します。 自己署名証明書の場合は、有効な証明書を生成し、ルート証明書を Application Gateway の HTTP 設定にアップロードする必要があります。 そのためには、次の手順に従います。

1.  ポータルで Application Gateway の HTTP 設定を開きます。

1.  有効期限が切れた証明書が含まれている設定を選択し、 **[証明書の追加]** を選択して、新しい証明書ファイルを開きます。

1.  証明書の横にある **削除** アイコンを使用して古い証明書を削除し、 **[保存]** を選択します。

#### <a name="certificate-verification-failed"></a>証明書の検証の失敗

**メッセージ:** The validity of the backend certificate could not be verified. (バックエンド証明書の有効性を検証できませんでした。) To find out the reason, check OpenSSL diagnostics for the message associated with error code {errorCode} (理由を調べるには、エラー コード {errorCode} に関連付けられたメッセージの OpenSSL 診断を確認してください)

**原因:** このエラーは、Application Gateway が証明書の有効性を確認できない場合に発生します。

**解決方法:** この問題を解決するには、サーバー上の証明書が適切に作成されていることを確認します。 たとえば、[OpenSSL](https://www.openssl.org/docs/man1.0.2/man1/verify.html) を使用して、証明書とそのプロパティを確認し、Application Gateway の HTTP 設定への証明書の再アップロードを試行できます。

<a name="backend-health-status-unknown"></a>バックエンドの正常性状態: 不明
-------------------------------
バックエンドの正常性状態が不明の場合、ポータル ビューは次のスクリーンショットのようになります。

![Application Gateway のバックエンドの正常性 - 不明](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

この動作は、次の 1 つ以上の理由で発生することがあります。

1.  Application Gateway サブネット上の NSG によって、ポート 65503 から 65534 (v1 SKU) または 65200 から 65535 (v2 SKU) への "インターネット" からのインバウンド アクセスがブロックされている。
1.  Application Gateway サブネット上の UDR が既定のルート (0.0.0.0/0) に設定され、次ホップが "インターネット" として指定されていない。
1.  仮想ネットワークへの BGP 経由の ExpressRoute/VPN 接続によって、既定のルートがアドバタイズされる。
1.  パブリック ドメイン名を解決できない仮想ネットワーク上にカスタム DNS サーバーが構成されている。
1.  Application Gateway が異常な状態である。

**解決方法:**

1.  NSG によって、ポート 65503 から 65534 (v1 SKU) または 65200 から 65535 (v2 SKU) への **インターネット** からのアクセスがブロックされているかどうかを確認します。

    a.  Application Gateway の **[概要]** タブで、 **[仮想ネットワーク/サブネット]** リンクを選択します。

    b.  対象の仮想ネットワークの **[サブネット]** タブで、Application Gateway がデプロイされているサブネットを選択します。

    c.  NSG が構成されているかどうかを確認します。

    d.  NSG が構成されている場合は、 **[検索]** タブまたは **[すべてのリソース]** でその NSG リソースを検索します。

    e.  **[受信規則]** セクションで、 **[ソース]** が **[すべて]** または **[インターネット]** に設定されている、65503 から 65534 (v1 SKU の場合) または 65200 から 65535 (v2 SKU の場合) の宛先ポート範囲を許可するインバウンド規則を追加します。

    f.  **[保存]** を選択し、バックエンドが正常として表示されることを確認します。 または、[PowerShell または CLI](../virtual-network/manage-network-security-group.md) を使用してこれを行うこともできます。

1.  次ホップが **[インターネット]** に設定されていない既定のルート (0.0.0.0/0) が UDR にあるかどうかを確認します。
    
    a.  手順 1a および 1b に従って、目的のサブネットを判別します。

    b.  UDR が構成されているかどうかを確認します。 構成されている場合は、検索バーまたは **[すべてのリソース]** でそのリソースを検索します。

    c.  次ホップが **[インターネット]** に設定されていない既定のルート (0.0.0.0/0) があるかどうかを確認します。 設定が **[仮想アプライアンス]** または **[仮想ネットワーク ゲートウェイ]** の場合は、仮想アプライアンスまたはオンプレミスのデバイスによって、パケットが変更されることなくインターネットの宛先に適切に戻されるようにする必要があります。

    d.  それ以外の場合は、次ホップを **[インターネット]** に変更し、 **[保存]** を選択した後、バックエンドの正常性を確認します。

1.  仮想ネットワークへの BGP 経由の ExpressRoute または VPN 接続によってアドバタイズされる既定のルート:

    a.  BGP 経由で仮想ネットワークへの ExpressRoute または VPN 接続を使用していて、既定のルートをアドバタイズしている場合は、パケットが変更されることなくインターネットの宛先に戻されるようにする必要があります。 Application Gateway ポータルの **[接続のトラブルシューティング]** オプションを使用して確認できます。

    b.  1\.1.1.1 などの任意のインターネット ルーティング可能な IP アドレスとして宛先を手動で選択します。 任意の宛先ポートを選択して、接続を確認します。

    c.  次ホップが仮想ネットワーク ゲートウェイの場合、ExpressRoute または VPN 経由でアドバタイズされる既定のルートが存在する可能性があります。

1.  仮想ネットワークにカスタム DNS サーバーが構成されている場合は、サーバーがパブリック ドメインを解決できるかどうかを確認します。 パブリック ドメイン名の解決は、Application Gateway が OCSP サーバーなどの外部ドメインにアクセスする必要があるシナリオや、証明書の失効状態などを確認する必要があるシナリオで必要になる場合があります。

1.  Application Gateway が正常で実行中であることを確認するには、ポータルの **[Resource Health]\(リソース正常性\)** オプションにアクセスし、状態が **[正常]** であることを確認します。 **[異常]** または **[デグレード]** 状態が表示される場合は、[サポートにお問い合わせください](https://azure.microsoft.com/support/options/)。

<a name="next-steps"></a>次のステップ
----------

[Application Gateway の診断とログ](./application-gateway-diagnostics.md)の詳細を確認します。
