---
title: Azure Application Gateway のバックエンドの正常性に関する問題のトラブルシューティング
description: この記事では、Azure Application Gateway のバックエンドの正常性に関する問題のトラブルシューティングについて説明します
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: article
ms.date: 08/30/2019
ms.author: surmb
ms.openlocfilehash: 1fd4e9156e29133b1db4fe9ab9a0825eb1aa3b55
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097590"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>Application Gateway のバックエンドの正常性に関する問題のトラブルシューティング
==================================================

<a name="overview"></a>概要
--------

Application Gateway は、既定で、バックエンド サーバーに対して probe を実行して、その正常性状態を確認すると共に、バックエンド サーバーが要求を処理する準備ができているかどうかを確認します。 ユーザーは、カスタム probe を作成して、ホスト名、probe の対象となるパス、および正常として受け入れられる状態コードを指定することもできます。 どちらのケースでも、バックエンド サーバーが正常に応答しない場合、Application Gateway はそのサーバーを "異常" とマークし、サーバーへの要求の転送を停止します。 サーバーが正常に応答し始めると、要求の処理が再開されます。

### <a name="how-to-check-backend-health"></a>バックエンドの正常性を確認する方法

バックエンド プールの正常性を確認するには、Azure portal の [バックエンド正常性] ページを使用できます。 また、[Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhealth?view=azps-2.6.0)、[CLI](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest#az-network-application-gateway-show-backend-health)、または [REST API](https://docs.microsoft.com/rest/api/application-gateway/applicationgateways/backendhealth) を使用して正常性状態を取得することもできます。 詳細については、各方法のリンク先の記事を参照してください。

上記のいずれかの方法で取得される状態には、次の 3 つの種類があります。

1.  Healthy

2.  異常

3.  Unknown

サーバーのバックエンドの正常性状態が正常の場合、Application Gateway が要求をそのサーバーに転送することを意味します。 一方、バックエンド プール内の全サーバーのバックエンドの正常性が異常または不明な場合、アプリケーション アクセス中にいくつかの問題が発生する可能性があります。 このドキュメントでは、バックエンド サーバーが異常または不明な場合に表示される各エラーの症状、原因、および解決策について説明します。

<a name="backend-health-status-unhealthy"></a>バックエンドの正常性状態: "異常"
-------------------------------

バックエンドの正常性状態が [異常] と表示される場合は、次のスクリーンショットのようにポータルに示されます。

![Application Gateway のバックエンドの正常性 - 異常](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

または、Azure PowerShell、CLI、または Azure REST API クエリを使用している場合は、次のような応答が表示されます。
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
バックエンド プール内のすべてのサーバーでバックエンド サーバーの状態が [異常] と表示される場合、要求はサーバーに転送されず、Application Gateway は 502 "ゲートウェイが不適切です" というエラーを要求元のクライアントに返します。 この問題のトラブルシューティングを行うには、[バックエンド正常性] タブの [詳細] 列を確認します。

[バックエンド正常性] タブの [詳細] 列に表示されるメッセージは問題に関する詳細な分析情報を示すため、これに基づいて問題のトラブルシューティングを開始できます。

> [!NOTE]
> 既定の probe 要求は \<プロトコル\>://127.0.0.1:\<ポート\>/ の形式で送信されます。たとえば、ポート 80 における HTTP probe の場合は <http://127.0.0.1/> で、HTTP 状態コード 200 から 399 の応答のみが正常な応答と見なされます。 プロトコルと宛先ポートは、HTTP 設定から継承されます。 Application Gateway で別のプロトコル、ホスト名、またはパスに対して probe を実行し、他の状態コードを正常として受け入れるようにするには、カスタム probe を構成し、それを HTTP 設定に関連付けます。

<a name="error-messages"></a>エラー メッセージ
------------------------
#### <a name="backend-server-timeout"></a>バックエンド サーバーのタイムアウト

**メッセージ:** Time taken by the backend to respond to application gateway\'s health probe is more than the time-out threshold in the probe setting. (バックエンドがアプリケーション ゲートウェイの正常性 probe に応答するのにかかる時間が、probe 設定のタイムアウトしきい値を超えています。)

**原因:** Application Gateway は、HTTP(S) probe 要求をバックエンド サーバーに送信した後、構成されている一定の時間にわたってバックエンド サーバーからの応答を待ちます。 バックエンド サーバーは、このタイムアウト値以内に応答しないと、再びタイムアウト時間内に応答を開始するまで "異常" とマークされます。

**解決策:** バックエンド サーバーまたはアプリケーションが構成されているタイムアウト時間内に応答しない理由を確認すると共に、アプリケーションの依存関係も確認します。たとえば、データベースに問題がある場合は、応答の遅延につながる可能性があります。 アプリケーションの動作を把握したうえでアプリケーションがタイムアウト値の経過後にのみ応答するようにする必要がある場合は、カスタム probe 設定でタイムアウト値を引き上げます。
タイムアウトの値を変更するには、カスタム probe が必要です。カスタム probe を構成する方法については、この[ドキュメントのページ](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal)を参照してください。

タイムアウトを引き上げるには、次の手順に従います。

1.  バックエンド サーバーに直接アクセスし、そのページでサーバーが応答するまでにかかった時間を確認します。 開発者ツールを使用するブラウザーなど、任意のツールを使用してアクセスできます。

2.  アプリケーションが応答するまでの時間を把握したら、[正常性プローブ] タブをクリックし、対象の HTTP 設定に関連付けられている probe を選択します。

3.  アプリケーションの応答時間を超える任意のタイムアウト値を秒単位で入力します。

4.  カスタム probe 設定を保存し、バックエンドの正常性が [正常] と表示されるかどうかを確認します。

#### <a name="dns-resolution-error"></a>DNS の解決エラー

**メッセージ:** Application gateway could not create a probe for this backend. (Application Gateway でこのバックエンドに対する probe を作成できませんでした。) This usually happens when the FQDN of the backend has not been entered correctly. (これは、通常、バックエンドの FQDN が正しく入力されていない場合に発生します。) 

**原因:** バックエンド プールの種類が [IP アドレス/FQDN] または [App Service] の場合、Application Gateway は、DNS サーバー (カスタムまたは Azure の既定) を使用して入力された FQDN の IP アドレスに解決し、HTTP 設定で指定されている TCP ポートでサーバーへの接続を試行します。 ただし、このメッセージが表示された場合は、Application Gateway が入力された FQDN の IP アドレスを正常に解決できなかったことを示しています。

**解決策:**

1.  バックエンド プールに入力された FQDN が正しいかどうかを確認し、それがパブリック ドメインである場合は、ローカル コンピューターから解決を試みます。

2.  IP アドレスを解決できる場合は、VNet の DNS 構成に問題がある可能性があります。

3.  VNet にカスタム DNS サーバーが構成されているかどうかを確認します。 その場合は、DNS サーバーが特定の FQDN の IP アドレスに解決できない理由を確認します。

4.  それが Azure の既定の DNS の場合は、適切な A レコードまたは CNAME レコード マッピングが行われているかどうかをドメイン名レジストラーに確認します。

5.  ドメインがプライベートまたは内部の場合は、同じ VNet 内の VM から解決を試みます。それに解決できる場合は Application Gateway を再起動して、もう一度確認します。 Application Gateway を再起動するには、それぞれのドキュメント リンクに記載されている PowerShell コマンドを使用して、[停止](https://docs.microsoft.com/powershell/module/azurerm.network/stop-azurermapplicationgateway?view=azurermps-6.13.0)および[起動](https://docs.microsoft.com/powershell/module/azurerm.network/start-azurermapplicationgateway?view=azurermps-6.13.0)操作を実行する必要があります。

#### <a name="tcp-connect-error"></a>TCP 接続エラー

**メッセージ:** Application gateway could not connect to the backend. (Application Gateway がバックエンドに接続できませんでした。)
Please check that the backend responds on the port used for the probe. (バックエンドが probe に使用されるポートで応答することを確認してください。)
Also check whether any NSG/UDR/Firewall is blocking access to the Ip and port of this backend (また、NSG、UDR、またはファイアウォールによってこのバックエンドの IP とポートへのアクセスがブロックされているかどうかも確認してください)

**原因:** DNS 解決フェーズの後、Application Gateway は、HTTP 設定で構成されている TCP ポートでバックエンド サーバーへの接続を試行します。 Application Gateway が指定されたポートで TCP セッションを確立できない場合、probe はこのメッセージで "異常" とマークされます。

**解決策:** このエラーが表示された場合は、次のことを確認します。

1.  ブラウザーまたは PowerShell を使用して、HTTP 設定に指定されているポートでバックエンド サーバーに接続できるかどうかを確認します。たとえば、次のコマンドを使用できます。Test-NetConnection -ComputerName www.bing.com -Port 443

2.  指定されているポートが目的のポートではない場合は、Application Gateway がバックエンド サーバーに接続するための適切なポート番号を入力します

3.  そのポート経由でローカル コンピューターからも接続できない場合は、次のようにします。

    a.  バックエンド サーバーの NIC とサブネットの NSG 設定を調べて、構成されているポートへのインバウンド接続が許可されているかどうかを確認します。 許可されていない場合は、接続を許可する新しい規則を作成します。 NSG 規則の作成方法については、[このドキュメント ページを確認](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic#create-security-rules)してください。

    b.  Application Gateway のサブネットの NSG 設定を調べて、接続を確立できるようにパブリックおよびプライベートのアウトバウンド トラフィックが許可されているかどうかを確認します。 NSG 規則の作成方法については、(a) でリンクされているドキュメントを参照してください。
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  Application Gateway およびバックエンド サーバーのサブネットの UDR 設定でルーティングの異常の有無を確認します。 UDR によってトラフィックがバックエンド サブネットの外へ送信される設定になっていないことを確認します。 たとえば、ネットワーク仮想アプライアンスへのルートや、ExpressRoute または VPN 経由でアプリケーション ゲートウェイ サブネットにアドバタイズされる既定のルートを確認します。

    d.  NIC の有効なルートと規則を確認するために、次の PowerShell コマンドを使用できます。
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
4.  NSG または UDR で問題が見つからない場合は、構成されているポートでクライアントが TCP セッションを確立するのを妨げるようなアプリケーション関連の問題がないかどうかを、バックエンド サーバーで確認します。 いくつかの確認事項を次に示します。

    e.  コマンド プロンプト (Win+R -\> cmd) を開き、「netstat」と入力して Enter キーを押します

    f.  構成されているポートでサーバーがリッスンしているかどうかを確認します。
        例:
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    g.  リッスンしていない場合は、ご自分の Web サーバーの設定 (たとえば、IIS のサイト バインド、NGINX のサーバー ブロック、Apache の仮想ホストなど) を確認します

    h.  OS のファイアウォール設定を調べて、ポートへの受信トラフィックが許可されていることを確認します

#### <a name="http-status-code-mismatch"></a>HTTP 状態コードの不一致

**メッセージ:** Status code of the backend\'s HTTP response did not match the probe setting. (バックエンドの HTTP 応答の状態コードが probe 設定と一致しませんでした。) Expected:{HTTPStatusCode0} Received:{HTTPStatusCode1}. (必要: {HTTPStatusCode0} 受信: {HTTPStatusCode1}。)

**原因:** TCP 接続が確立され、SSL ハンドシェイクが完了すると (SSL が有効な場合)、Application Gateway は probe を HTTP GET 要求としてバックエンド サーバーに送信します。 前述のように、既定の probe の対象は \<protocol\>://127.0.0.1:\<port\>/ であり、200 から 399 の範囲の応答状態コードは正常であると見なされます。 サーバーからそれ以外の状態コードが返された場合、そのサーバーはこのメッセージで "異常" とマークされます。

**解決策:** バックエンド サーバーの応答コードに応じて、次の手順を実行できます。 一般的な状態コードをいくつか次に示します。

| **Error** | **アクション** |
| --- | --- |
| probe 状態コードの不一致: 401 を受信 | バックエンド サーバーで認証が必要かどうかを確認します。 この時点で、Application Gateway の probe で認証用の資格情報を渡すことができません。 probe 状態コードの照合で \"HTTP 401\" を許可するか、サーバーが認証を必要としないパスに対して probe を実行します。 | |
| probe 状態コードの不一致: 403 を受信 | アクセスは禁止されています。 バックエンド サーバーでパスへのアクセスが許可されているかどうかを確認します。 | |
| probe 状態コードの不一致: 404 を受信 | ページが見つかりません。 ホスト名のパスにバックエンド サーバーでアクセス可能な場合は、それを確認します。 ホスト名またはパス パラメーターをアクセス可能な値に変更します。 | |
| probe 状態コードの不一致: 405 を受信 | Application Gateway の probe 要求で HTTP GET メソッドが使用されています。 対象のサーバーでこれが許可されているかどうかを確認します。 | |
| probe 状態コードの不一致: 500 を受信 | 内部サーバー エラー。 バックエンド サーバーの正常性と、サービスが実行されているかどうかを確認します。 | |
| probe 状態コードの不一致: 503 を受信 | Service unavailable. (サービス利用不可。) バックエンド サーバーの正常性と、サービスが実行されているかどうかを確認します。 | |

または、応答が正当なものであると判断したうえで、Application Gateway が他の状態コードを正常として受け入れるようにする場合は、カスタム probe を作成できます。 この変更は、バックエンド Web サイトで認証が必要なときに、probe 要求にユーザー資格情報が含まれないために要求が失敗し、バックエンド サーバーによって HTTP 401 状態コードが返される状況で役立ちます。

カスタム probe を作成するには、[こちら](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal)に記載されている手順に従います。

#### <a name="http-response-body-mismatch"></a>HTTP 応答本文の不一致

**メッセージ:** Body of the backend\'s HTTP response did not match the probe setting. (バックエンドの HTTP 応答の本文が probe 設定と一致しませんでした。) Received response body does not contain {string}. (受信した応答本文に {string} が含まれていません。)

**原因:** カスタム probe を作成する場合、応答本文の文字列と一致させることによってバックエンド サーバーを "正常" とマークすることもできます。 たとえば、一致する文字列として "unauthorized" を受け入れるように Application Gateway を構成できます。 probe 要求に対するバックエンド サーバーの応答に文字列 "unauthorized" が含まれている場合は、"正常" とマークされます。
それ以外の場合は、このメッセージで "異常" とマークされます。

**解決策:** 次の手順に従って、この問題を解決できます。

1.  バックエンド サーバーにローカルでアクセスするか、probe パス上のクライアント マシンからアクセスして、応答本文を確認します。

2.  Application Gateway のカスタム probe 構成を調べて、応答本文が構成内容と一致するかどうかを確認します。

3.  それらが一致しない場合は、受け入れる適切な文字列値で probe 構成を変更します。

Application Gateway の probe の一致については、[こちら](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)を参照してください。

#### <a name="backend-server-certificate-invalid-ca"></a>バックエンド サーバーの証明書: 無効な CA

**メッセージ:** The server certificate used by the backend is not signed by a well-known Certificate Authority (CA). (バックエンドによって使用されるサーバー証明書が既知の証明機関 (CA) によって署名されていません。) Whitelist the backend on the application gateway by uploading the root certificate of the server certificate used by the backend. (バックエンドによって使用されるサーバー証明書のルート証明書をアップロードして、アプリケーション ゲートウェイでバックエンドをホワイトリストに登録してください。)

**原因:** Application Gateway v2 でのエンドツーエンド SSL を使用するには、サーバーが正常であると判断するためにバックエンド サーバーの証明書を検証する必要があります。
SSL 証明書を信頼するには、そのバックエンド サーバーの証明書が、Application Gateway の信頼されたストアに含まれる CA によって発行されている必要があります。 証明書が信頼された CA によって発行されていない場合 (自己署名証明書など)、ユーザーは、発行者の証明書を Application Gateway にアップロードする必要があります。

**解決策:** 次の手順に従って、信頼されたルート証明書をエクスポートして Application Gateway にアップロードします (次に示す手順は Windows クライアント用です)

1.  対象のアプリケーションがホストされているマシンにサインインします

2.  Win + R キーを押すか、[スタート] ボタンを右クリックして [実行] を選択して、[実行] を開きます

3.  「certmgr.msc」と入力し、Enter キーを押します。 [スタート] メニューで証明書マネージャーを検索することもできます。

4.  証明書を見つけ (通常は \'[証明書 - 現在のユーザー]\\[個人]\\[証明書]\')、証明書を開きます

5.  [証明書のプロパティ] で、[証明書のパス] タブを選択します

6.  ルート証明書を選択し、[証明書の表示] を選択します

7.  [証明書のプロパティ] で、[詳細] タブに切り替えます

8.  [詳細] タブで、[ファイルへコピー] を選択し、ファイルを Base-64 エンコード X.509 (.CER) 形式で保存します

9.  保存したら、Azure portal で Application Gateway の [HTTP 設定] ページを開きます

10. HTTP 設定を開き、[証明書の追加] をクリックして、先ほど保存した証明書ファイルを見つけます

11. [保存] をクリックして HTTP 設定を保存します

または、ブラウザーを使用して (Application Gateway をバイパスして) サーバーに直接アクセスし、ブラウザーからルート証明書をエクスポートすることによって、クライアント マシンからルート証明書をエクスポートすることもできます。

Application Gateway での信頼されたルート証明書の抽出とアップロードの詳細な手順については、[こちら](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku)を参照してください。

#### <a name="trusted-root-certificate-mismatch"></a>信頼されたルート証明書の不一致

**メッセージ:** The root certificate of the server certificate used by the backend does not match the trusted root certificate added to the application gateway. (バックエンドによって使用されるサーバー証明書のルート証明書が、アプリケーション ゲートウェイに追加されている信頼されたルート証明書と一致しません。) Ensure that you add the correct root certificate to whitelist the backend (バックエンドをホワイトリストに登録するために適切なルート証明書を追加していることを確認してください)

**原因:** Application Gateway v2 でのエンドツーエンド SSL を使用するには、サーバーが正常であると判断するためにバックエンド サーバーの証明書を検証する必要があります。
SSL 証明書を信頼するには、そのバックエンド サーバーの証明書が、Application Gateway の信頼されたストアに含まれる CA によって発行されている必要があります。 証明書が信頼された CA によって発行されていない場合 (自己署名証明書など)、ユーザーは、発行者の証明書を Application Gateway にアップロードする必要があります。

Application Gateway の HTTP 設定にアップロードされた証明書は、バックエンド サーバー証明書のルート証明書と一致している必要があります。

**解決策:** 上記のエラー メッセージが表示された場合は、Application Gateway にアップロードされた証明書とバックエンド サーバーにアップロードされた証明書が一致していないことを意味します。

上記の手順 1 から 11 に従って、適切な信頼されたルート証明書を Application Gateway にアップロードしてください。

Application Gateway での信頼されたルート証明書の抽出とアップロードの詳細な手順については、[こちら](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku)を参照してください。
> [!NOTE]
> 言及されているエラーは、バックエンド サーバーが TLS ハンドシェイク中に "ルート -> 中間 (該当する場合) -> リーフ" を含む証明書の完全なチェーンを交換していない場合にも発生する可能性があります。 確認するには、任意のクライアントから OpenSSL コマンドを使用し、Application Gateway probe に構成された設定を使用してバックエンド サーバーに接続します。

たとえば、次のように入力します。
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
返される証明書の完全なチェーンが出力に表示されない場合は、ルート証明書を含む完全なチェーンと共に証明書を再度エクスポートし、それをバックエンド サーバー内で構成します。 

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

#### <a name="backend-certificate-invalid-common-name-cn"></a>バックエンド証明書の無効な共通名 (CN)

**メッセージ:** The Common Name (CN) of the backend certificate does not match the host header of the probe. (バックエンド証明書の共通名 (CN) が probe のホスト ヘッダーと一致しません。)

**原因:** Application Gateway は、バックエンドの HTTP 設定に指定されているホスト名が、バックエンド サーバーの SSL 証明書によって提示される共通名 (CN) のものと一致するかどうかを検証します。 これは、Standard_v2 SKU と WAF_v2 SKU のみの動作です。 Standard SKU と WAF SKU の SNI は、バックエンド プール アドレスの FQDN として設定されます。

v2 SKU では、既定の probe が存在する場合 (カスタム probe が構成または関連付けられていない場合)、または [Pick hostname from backend address]\(バックエンド アドレスからホスト名を取得する\) が HTTP 設定に指定され、そのバックエンド アドレス プールに有効な FQDN が含まれている場合、SNI は HTTP 設定に指定されているホスト名から設定されます。

HTTP 設定に関連付けられたカスタム probe がある場合、SNI は、カスタム probe 構成に指定されているホスト名から設定されます。また、カスタム probe の [Pick hostname from backend HTTP settings]\(バックエンドの HTTP 設定からホスト名を取得する\) がオンの場合、SNI は、HTTP 設定に指定されているホスト名から設定されます。

HTTP 設定で [Pick hostname from backend address]\(バックエンド アドレスからホスト名を取得する\) が設定されている場合は、バックエンド アドレス プールに有効な FQDN が含まれている必要があります。

上記のエラー メッセージが表示された場合は、バックエンド証明書の共通名 (CN) が、カスタム probe または HTTP 設定で構成されているホスト名と一致しないことを意味します ([Pick Hostname from Backend HTTP Settings]\(バックエンドの HTTP 設定からホスト名を取得する\) が選択されている場合)。 既定の probe を使用している場合、ホスト名は "127.0.0.1" に設定されます。 これが目的の値でない場合は、カスタム probe を作成し、HTTP 設定に関連付ける必要があります。

**解決策:**

この問題を解決するには、次の手順に従います。

Windows の場合:

1.  対象のアプリケーションがホストされているマシンにサインインします

2.  Win + R キーを押すか、[スタート] ボタンを右クリックして [実行] を選択して、[実行] を開きます

3.  「certmgr.msc」と入力し、Enter キーを押します。 [スタート] メニューで証明書マネージャーを検索することもできます。

4.  証明書を見つけ (通常は \'[証明書 - 現在のユーザー]\\[個人]\\[証明書]\')、証明書を開きます

5.  [詳細] タブで、証明書のサブジェクトを確認します

6.  詳細から証明書の CN を確認し、これをカスタム probe または HTTP 設定のホスト名フィールドに入力します ([Pick Hostname from Backend HTTP Settings]\(バックエンドの HTTP 設定からホスト名を取得する\) が選択されている場合)。 これが対象の Web サイトの目的のホスト名ではない場合は、そのドメインの証明書を取得するか、カスタム probe または HTTP 設定の構成に適切なホスト名を入力する必要があります。

OpenSSL を使用する Linux の場合

1.  OpenSSL で次のコマンドを実行します 
```
openssl x509 -in certificate.crt -text -noout
```

2.  表示されたプロパティから証明書の CN を見つけ、HTTP 設定のホスト名フィールドに同じ値を入力します。 これが対象の Web サイトの目的のホスト名ではない場合は、そのドメインの証明書を取得するか、カスタム probe または HTTP 設定の構成に適切なホスト名を入力する必要があります。

#### <a name="backend-certificate-is-invalid"></a>バックエンド証明書が無効

**メッセージ:** Backend certificate is invalid. (バックエンド証明書が無効です。) Current date is not within the \"Valid from\" and \"Valid to\" date range on the certificate. (現在の日付が証明書の [有効期間の開始] と [有効期間の終了] の日付範囲内ではありません。)

**原因:** すべての証明書には有効期限があり、サーバーの SSL 証明書が有効でない限り、HTTPS 接続はセキュリティで保護されません。
現在の日付が [有効期間の開始] と [有効期間の終了] の範囲内にある必要があります。 そうでない場合、証明書は無効と見なされ、セキュリティ上の問題になります。 この時点で、Application Gateway は、バックエンド サーバーを "異常" とマークします。

**解決策:** SSL 証明書の有効期限が切れている場合は、ベンダーで証明書を更新し、新しい証明書を使用してサーバーの設定を更新します。 自己署名証明書の場合は、有効な証明書を生成し、ルート証明書を Application Gateway の HTTP 設定にアップロードする必要があります。 これを行うには、次の手順に従います。

1.  ポータルで Application Gateway の HTTP 設定を開きます

2.  有効期限が切れた証明書が含まれている HTTP 設定を選択し、[証明書の追加] を選択して、新しい証明書ファイルを開きます

3.  証明書の横にある削除アイコンを使用して古い証明書を削除し、[保存] をクリックします

#### <a name="certificate-verification-failed"></a>証明書の検証の失敗

**メッセージ:** The validity of the backend certificate could not be verified. (バックエンド証明書の有効性を検証できませんでした。) To find out the reason, check Open SSL diagnostics for the message associated with error code {errorCode} (理由を調べるには、エラー コード {errorCode} に関連付けられたメッセージの Open SSL 診断を確認してください)

**原因:** このエラーは、Application Gateway が証明書の有効性を確認できない場合に発生します。

**解決策:** この問題を解決するには、サーバー上の証明書が適切に作成されているかどうかを確認します。 たとえば、[OpenSSL](https://www.openssl.org/docs/man1.0.2/man1/verify.html) を使用して、証明書とそのプロパティを確認し、証明書を Application Gateway の HTTP 設定に再アップロードすることを試みることができます。

<a name="backend-health-status-unknown"></a>バックエンドの正常性状態: "不明"
-------------------------------
バックエンドの正常性状態が [不明] と表示される場合は、次のスクリーンショットのようにポータルに表示されます。

![Application Gateway のバックエンドの正常性 - 不明](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

バックエンドの正常性状態が [不明] と表示される場合は、次の 1 つまたは複数の理由が考えられます。

1.  Application Gateway サブネット上の NSG によって、"インターネット" からの、ポート 65503 から 65534 (v1 SKU) または 65200 から 65535 (v2 SKU) へのインバウンド アクセスがブロックされている
2.  Application Gateway サブネット上の UDR に、次ホップが "インターネット" ではない既定のルート (0.0.0.0/0) がある
3.  BGP 経由で VNet への ExpressRoute または VPN 接続によってアドバタイズされる既定のルート
4.  パブリック ドメイン名を解決できない VNet にカスタム DNS サーバーが構成されている
5.  異常な状態にある Application Gateway

**解決策:**

1.  NSG によって、"インターネット" からの、ポート 65503 から 65534 (v1 SKU) または 65200 から 65535 (v2 SKU) へのアクセスがブロックされているかどうかを確認します

    a.  Application Gateway の [概要] タブで、[仮想ネットワーク/サブネット] リンクを選択します

    b.  対象の仮想ネットワークの [サブネット] タブで、Application Gateway がデプロイされているサブネットを選択します

    c.  NSG が構成されているかどうかを確認します

    d.  構成されている場合は、[検索] タブまたは [すべてのリソース] でその NSG リソースを検索します

    e.  [受信規則] セクションで、[ソース] が [すべて] または [インターネット] である、65503 から 65534 (v1 SKU の場合) または 65200 から 65535 (v2 SKU の場合) の宛先ポート範囲を許可するインバウンド規則を追加します

    f.  [保存] をクリックして、バックエンドが正常に [正常] と表示されるかどうかを確認します

    g.  または、[PowerShell または CLI](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group) を使用してこれを行うこともできます

2.  次ホップが "インターネット" ではない既定のルート (0.0.0.0/0) が UDR にあるかどうかを確認します
    
    a.  手順 1.a および 1.b に従って、目的のサブネットを判定します

    b.  UDR が構成されているかどうかを確認します。 構成されている場合は、検索バーまたは [すべてのリソース] でそのリソースを検索します

    c.  次ホップが "インターネット" ではない既定のルート (0.0.0.0/0) があるかどうかを確認します。 それが仮想アプライアンスまたは仮想ネットワーク ゲートウェイの場合は、仮想アプライアンスまたはオンプレミスのデバイスによって、パケットが変更されることなくインターネットの宛先に適切に戻されるようにする必要があります

    d.  それ以外の場合は、次ホップを [インターネット] に変更し、[保存] をクリックした後、バックエンドの正常性を確認します

3.  BGP 経由で VNet への ExpressRoute または VPN 接続によってアドバタイズされる既定のルート

    a.  BGP 経由で VNet への ExpressRoute または VPN 接続を使用していて、既定のルートをアドバタイズしている場合は、パケットが変更されることなくインターネットの宛先に戻されるようにする必要があります

    b.  Application Gateway ポータルの [接続のトラブルシューティング] オプションを使用して確認できます

    c.  "1.1.1.1" などの任意のインターネット ルーティング可能な IP アドレスとして宛先を手動で選択し、任意の宛先ポートを選択して、接続を確認します。

    d.  次ホップが "仮想ネットワーク ゲートウェイ" の場合、ExpressRoute または VPN 経由でアドバタイズされる既定のルートが存在する可能性があります

4.  VNet にカスタム DNS サーバーが構成されている場合は、サーバーがパブリック ドメインを解決できるかどうかを確認します。 パブリック ドメイン名の解決は、Application Gateway が OCSP サーバーなどの外部ドメインにアクセスする必要があるシナリオや、証明書の失効状態などを確認する必要があるシナリオで必要になる場合があります。

5.  Application Gateway が正常で実行中であるかどうかを確認するには、ポータルの [Resource Health]\(リソース正常性\) オプションにアクセスし、"正常" であるかどうかを確認します。 [異常] または [デグレード] 状態が表示される場合は、[サポートにお問い合わせください](https://azure.microsoft.com/support/options/)。

<a name="next-steps"></a>次の手順
----------

Application Gateway の診断とログの詳細については、[こちら](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)を参照してください。
