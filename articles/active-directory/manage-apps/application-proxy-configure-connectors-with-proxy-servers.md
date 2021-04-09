---
title: 既存のオンプレミス プロキシ サーバーおよび Azure Active Directory と連携する
description: Azure Active Directory で既存のオンプレミス プロキシ サーバーと連携する方法について説明します。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 04/07/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperf-fy21q2
ms.openlocfilehash: 09a257c4b80fd796ac4e1e8203f00857d2d95eaf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259119"
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>既存のオンプレミス プロキシ サーバーと連携する

この記事では、送信プロキシ サーバーと連携するように Azure Active Directory (Azure AD) アプリケーション プロキシ コネクタを構成する方法について説明します。 この記事は、既存のプロキシがあるネットワーク環境のお客様を対象としています。

まず、次の主なデプロイ シナリオを見ていきます。

* オンプレミスの送信プロキシをバイパスするようにコネクタを構成する。
* 送信プロキシを使用して Azure AD アプリケーション プロキシにアクセスにするようにコネクタを構成する。
* コネクタとバックエンド アプリケーション間のプロキシを使用して構成する。

コネクタの動作方法について詳しくは、「[Azure AD アプリケーション プロキシ コネクタを理解する](application-proxy-connectors.md)」をご覧ください。

## <a name="bypass-outbound-proxies"></a>送信プロキシをバイパス

コネクタには、送信要求を行う基になる OS コンポーネントがあります。 これらのコンポーネントは、Web プロキシ自動発見 (WPAD) を使って自動的にネットワーク上のプロキシ サーバーの特定を試みます。

この OS コンポーネントは、wpad.domainsuffix の DNS 参照を実行することで、プロキシ サーバーの検出を試みます。 この参照が DNS で解決すると、wpad.dat の IP アドレスに対して HTTP 要求が作成されます。 この要求が、環境におけるプロキシ構成スクリプトになります。 コネクタは、このスクリプトを使用して送信プロキシ サーバーを選択します。 ただし、プロキシで追加の構成設定が必要なため、コネクタのトラフィックがプロキシを経由しないことがあります。

オンプレミスのプロキシをバイパスして Azure サービスへの直接接続を使用するように、コネクタを構成できます。 管理する構成が 1 つ少なくて済むため、(ネットワーク ポリシーで許可されている場合) この方法をお勧めします。

コネクタで送信プロキシの使用を無効にするには、次のコード例に示すように、C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config ファイルを編集し、*system.net* セクションを追加します。

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>
    <defaultProxy enabled="false"></defaultProxy>
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

コネクタ アップデーター サービスもプロキシをバイパスするようにするには、ApplicationProxyConnectorUpdaterService.exe.config ファイルに同じような変更を加えます。 このファイルは C:\Program Files\Microsoft AAD App Proxy Connector Updater にあります。

既定の .config ファイルに戻す必要がある場合に備えて、元のファイルのコピーを作成するようにしてください。

## <a name="use-the-outbound-proxy-server"></a>送信プロキシ サーバーの使用

一部の環境では、すべての送信トラフィックが例外なく送信プロキシを経由する必要がある場合があります。 そのような場合は、プロキシのバイパスは選択肢にありません。

次の図に示すように、コネクタのトラフィックが送信プロキシを経由するように構成できます。

 ![コネクタ トラフィックが送信プロキシを経由して Azure AD アプリケーション プロキシに到達するように構成する](./media/application-proxy-configure-connectors-with-proxy-servers/configure-proxy-settings.png)

送信トラフィックしかないため、ファイアウォール経由で受信アクセスを構成する必要はありません。

> [!NOTE]
> アプリケーション プロキシは、他のプロキシに対する認証をサポートしていません。 コネクタ/アップデータのネットワーク サービス アカウントは、認証を求められることなく、プロキシに接続できる必要があります。

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>手順 1:送信プロキシを経由するようにコネクタと関連サービスを構成する

環境内で WPAD を有効にし、適切に構成している場合、コネクタは送信プロキシ サーバーを自動的に検出して使用を試みます。 一方で、送信プロキシを経由するようにコネクタを明示的に構成することができます。

これを行うには、次のコード例に示すように、C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config ファイルを編集し、*system.net* セクションを追加します。 ローカルのプロキシ サーバー名または IP アドレスとリッスンしているポートを反映するように、*proxyserver:8080* を変更します。 IP アドレスを使用している場合でも、値にはプレフィックス http:// を付ける必要があります。

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>  
    <defaultProxy>   
      <proxy proxyaddress="http://proxyserver:8080" bypassonlocal="True" usesystemdefault="True"/>   
    </defaultProxy>  
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

次に、C:\Program Files\Microsoft AAD App Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config ファイルに同じような変更を加えて、コネクタ アップデーター サービスがプロキシを使用するように構成します。

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>手順 2:コネクタと関連サービスからのトラフィックが経由して流れることを許可するようにプロキシを構成する

送信プロキシで考慮すべき点は次の 4 つです。

* プロキシ送信規則
* プロキシの認証
* プロキシ ポート
* TLS インスペクション

#### <a name="proxy-outbound-rules"></a>プロキシ送信規則

次の URL へのアクセスを許可します。

| URL | Port |  用途 |
| --- | --- | --- |
| &ast;.msappproxy.net<br>&ast;.servicebus.windows.net | 443/HTTPS | コネクタとアプリケーション プロキシ クラウド サービスの間の通信 |
| crl3.digicert.com<br>crl4.digicert.com<br>ocsp.digicert.com<br>crl.microsoft.com<br>oneocsp.microsoft.com<br>ocsp.msocsp.com<br> | 80/HTTP | コネクタでは、証明書の検証にこれらの URL が使用されます。 |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>&ast;.microsoftonline.com<br>&ast;.microsoftonline-p.com<br>&ast;.msauth.net<br>&ast;.msauthimages.net<br>&ast;.msecnd.net<br>&ast;.msftauth.net<br>&ast;.msftauthimages.net<br>&ast;.phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net<br>ctldl.windowsupdate.com | 443/HTTPS | コネクタでは、登録プロセスの間にこれらの URL が使用されます。 |
| ctldl.windowsupdate.com | 80/HTTP | 登録プロセスの間、この URL がコネクタで使用されます。 |

ファイアウォールまたはプロキシで DNS 許可リストを構成できる場合は、\*.msappproxy.net と \*.servicebus.windows.net への接続を許可できます。

FQDN による接続を許可することはできず、代わりに IP 範囲を指定する必要がある場合は、これらのオプションを使用します。

* すべてのアクセス先に対するコネクタの送信アクセスを許可する。
* Azure データセンターの全 IP アドレス範囲に対するコネクタの送信アクセスを許可する。 Azure データセンターの IP 範囲の一覧を使用するうえでの課題は、この一覧が毎週更新されることにあります。 アクセス規則が適宜更新されるようにプロセスを整備する必要があります。 IP アドレスのサブセットのみを使用すると、構成が分断される可能性があります。 Azure データ センターの最新 IP 範囲をダウンロードするには、[https://download.microsoft.com](https://download.microsoft.com) に移動して、"Azure IP 範囲とサービス タグ" を検索します。 必ず関連するクラウドを選択してください。 たとえば、パブリック クラウドの IP 範囲は、"Azure IP 範囲とサービス タグ – パブリック クラウド" で見つかります。 米国政府のクラウドは、"Azure IP 範囲とサービス タグ - 米国政府のクラウド" で検索すると見つかります。

#### <a name="proxy-authentication"></a>プロキシの認証

プロキシの認証は現在サポートされていません。 現在はインターネット上のアクセス先に対して、コネクタの匿名アクセスを許可することをお勧めします。

#### <a name="proxy-ports"></a>プロキシ ポート

コネクタは、CONNECT メソッドを使用して TLS ベースの送信接続を確立します。 このメソッドにより、送信プロキシを経由するトンネルが設定されます。 プロキシ サーバーが、ポート 443 と 80 へのトンネリングを許可するように構成します。

> [!NOTE]
> Service Bus を HTTPS 経由で実行する場合は、ポート 443 が使用されます。 ただし、既定では、Service Bus は直接 TCP 接続を試みて、直接接続に失敗した場合にのみ HTTPS に戻ってきます。

#### <a name="tls-inspection"></a>TLS インスペクション

コネクタのトラフィックに問題が生じるため、コネクタのトラフィックには TLS インスペクションを使用しないでください。 コネクタは証明書を使用してアプリケーション プロキシ サービスに対する認証を行いますが、その証明書が TLS インスペクションの間に失われることがあります。

## <a name="configure-using-a-proxy-between-the-connector-and-backend-application"></a>コネクタとバックエンド アプリケーション間のプロキシを使用して構成する
一部の環境では、バックエンド アプリケーションへの通信での転送プロキシの使用は、特別な要件になる場合があります。
これを有効にするには、次の手順に従ってください。

### <a name="step-1-add-the-required-registry-value-to-the-server"></a>手順 1:必要なレジストリ値をサーバーに追加する
1. 既定のプロキシを使用できるようにするには、次のレジストリ値 (DWORD) `UseDefaultProxyForBackendRequests = 1` を "HKEY_LOCAL_MACHINE\Software\Microsoft\Microsoft AAD App Proxy Connector" にあるコネクタ構成レジストリ キーに追加します。

### <a name="step-2-configure-the-proxy-server-manually-using-netsh-command"></a>手順 2:netsh コマンドを使用してプロキシ サーバーを手動で構成する
1.  グループ ポリシーの [マシン別にプロキシを設定する] を有効にします。 この場所は次のとおりです: コンピューターの構成\ポリシー\管理用テンプレート\Windows コンポーネント\Internet Explorer。 このポリシーを、ユーザーごとに設定する代わりに設定する必要があります。
2.  サーバーで `gpupdate /force` を実行するか、サーバーを再起動して、更新されたグループ ポリシー設定を使用していることを確認します。
3.  管理者権限で管理者特権でのコマンド プロンプトを起動し、`control inetcpl.cpl` を入力します。
4.  必要なプロキシ設定を構成します。 

これらの設定によって、コネクタは Azure との通信とバックエンド アプリケーションとの通信に同じ転送プロキシを使用するようになります。 Azure 通信へのコネクタが転送プロキシを必要としない場合、または別の転送プロキシを必要とする場合は、「送信プロキシをバイパス」または「送信プロキシ サーバーの使用」セクションで説明されているように、ApplicationProxyConnectorService.exe.config ファイルを変更してこれを設定できます。

> [!NOTE]
> オペレーティング システムでは、さまざまな方法でインターネット プロキシを構成できます。 NETSH WINHTTP によって構成されたプロキシ設定 (確認するには `NETSH WINHTTP SHOW PROXY` を実行します) は、手順 2 で構成したプロキシ設定をオーバーライドします。 

コネクタ アップデーター サービスでもコンピューター プロキシが使用されます。 この動作は、ApplicationProxyConnectorUpdaterService.exe.config ファイルを変更することで変更できます。

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>コネクタのプロキシの問題とサービスの接続の問題のトラブルシューティング

これですべてのトラフィックがプロキシを経由します。 問題が生じた場合は、次のトラブルシューティング情報が役に立ちます。

コネクタの接続の問題を特定してトラブルシューティングを行う最善の方法は、コネクタ サービスの開始時にネットワーク キャプチャを実行することです。 ネットワーク トレースをキャプチャおよびフィルター処理する際に役立つヒントを簡単に紹介します。

任意の監視ツールを使用することができます。 ここでは、Microsoft Message Analyzer を使用しています。

> [!NOTE]
> [Microsoft Message Analyzer (MMA) は廃止](/openspecs/blog/ms-winintbloglp/dd98b93c-0a75-4eb0-b92e-e760c502394f)され、2019 年 11 月 25 日に microsoft.com サイトからダウンロード パッケージが削除されました。  現時点では、開発における Microsoft Message Analyzer の Microsoft 代替製品はありません。  同様の機能については、Wireshark などのサード パーティ製のネットワーク プロトコル アナライザー ツールを使用することを検討してください。

以下に示すのは Message Analyzer の例ですが、原則はどの分析ツールでも同じです。

### <a name="take-a-capture-of-connector-traffic"></a>コネクタ トラフィックのキャプチャを実行する

最初のトラブルシューティングでは、次の手順を実行します。

1. services.msc で、Azure AD アプリケーション プロキシ コネクタ サービスを停止します。

   ![services.msc の Azure AD アプリケーション プロキシ コネクタ サービス](./media/application-proxy-configure-connectors-with-proxy-servers/services-local.png)

1. Message Analyzer を管理者として実行します。
1. **[Start Local Trace]\(ローカル トレースの開始\)** を選択します。
1. Azure AD アプリケーション プロキシ コネクタ サービスを開始します。
1. ネットワーク キャプチャを停止します。

   ![[ネットワーク キャプチャの停止] ボタンを示すスクリーンショット](./media/application-proxy-configure-connectors-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>コネクタのトラフィックが送信プロキシをバイパスするかどうかを確認する

アプリケーション プロキシ コネクタがプロキシ サーバーをバイパスしてアプリケーション プロキシ サービスに直接接続するよう構成している場合は、ネットワーク キャプチャを調べて、TCP 接続が失敗していないかを確認します。

Message Analyzer のフィルターを使用すると、これらの試みを識別できます。 フィルターのボックスに `property.TCPSynRetransmit` と入力し、 **[Apply]\(適用\)** を選択します。

SYN パケットは、TCP 接続を確立するために最初に送信されるパケットです。 このパケットにより応答が返されない場合は、SYN パケットの送信が再試行されます。 前述のフィルターを使用すると、再送信されたすべての SYN パケットが表示されます。 次に、これらの SYN パケットがコネクタ関連のトラフィックに対応するかどうかを調べます。

コネクタが Azure サービスに直接接続するよう構成している場合、ポート 443 での SynRetransmit の応答は、ネットワークまたはファイアウォールの問題が発生していることを示しています。

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>コネクタのトラフィックが送信プロキシを使用しているかどうかを確認する

アプリケーション プロキシ コネクタのトラフィックがプロキシ サーバーを経由するよう構成している場合は、プロキシへの https 接続が失敗していないかを確認します。

ネットワーク キャプチャをフィルター処理してこれらの試みを確認するには、Message Analyzer のフィルターで `(https.Request or https.Response) and tcp.port==8080` と入力し、ポート 8080 をご自身のプロキシ サービス ポートに置き換えます。 **[Apply]\(適用\)** を選択してフィルター結果を確認します。

前述のフィルターにより、プロキシ ポートとの間の HTTPS 要求と応答のみが表示されます。 プロキシ サーバーとの通信を示す CONNECT 要求を調べます。 成功すると、HTTP OK (200) の応答が表示されます。

他の応答コード (407 や 502 など) が表示される場合は、プロキシが認証を必要としているか、何らかの理由でトラフィックを許可しないことを示しています。 その場合は、プロキシ サーバー サポート チームのサポートを得てください。

## <a name="next-steps"></a>次のステップ

* [Azure AD アプリケーション プロキシ コネクタについて](application-proxy-connectors.md)
* コネクタの接続に問題がある場合は、[Azure Active Directory に関する Microsoft Q&A 質問ページ](/answers/topics/azure-active-directory.html)に質問を投稿するか、サポート チームに対するチケットを作成してください。