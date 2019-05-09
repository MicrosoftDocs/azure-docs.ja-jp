---
title: Azure AD で既存のオンプレミス プロキシ サーバーと連携する| Microsoft Docs
description: 既存のオンプレミス プロキシ サーバーと連携する方法について説明します。
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/12/2018
ms.author: celested
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b4cb1f6cc3da5230f510f57a56c7297341f82f3
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56175576"
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>既存のオンプレミス プロキシ サーバーと連携する

この記事では、送信プロキシ サーバーと連携するように Azure Active Directory (Azure AD) アプリケーション プロキシ コネクタを構成する方法について説明します。 この記事は、既存のプロキシがあるネットワーク環境のお客様を対象としています。

まず、次の主なデプロイ シナリオを見ていきます。
* オンプレミスの送信プロキシをバイパスするようにコネクタを構成する。
* 送信プロキシを使用して Azure AD アプリケーション プロキシにアクセスにするようにコネクタを構成する。

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

>[!NOTE]
>アプリケーション プロキシは、他のプロキシに対する認証をサポートしていません。 コネクタ/アップデータのネットワーク サービス アカウントは、認証を求められることなく、プロキシに接続できる必要があります。

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
* SSL インスペクション

#### <a name="proxy-outbound-rules"></a>プロキシ送信規則
次の URL へのアクセスを許可します。

| URL | 用途 |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | コネクタとアプリケーション プロキシ クラウド サービスの間の通信 |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | Azure では、これらの URL を使用して証明書が検証されます |
| login.windows.net<br>login.microsoftonline.com | コネクタでは、登録プロセスの間にこれらの URL が使用されます。 |

ファイアウォールまたはプロキシで DNS ホワイトリスト登録が許可されている場合は、\*.msappproxy.net と \*.servicebus.windows.net への接続をホワイトリストに登録できます。 そうでない場合は、[Azure データセンターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)へのアクセスを許可する必要があります。 これらの IP 範囲は毎週更新されます。


FQDN による接続を許可することはできず、代わりに IP 範囲を指定する必要がある場合は、これらのオプションを使用します。

* すべてのアクセス先に対するコネクタの送信アクセスを許可する。
* [Azure データセンターの全 IP アドレス範囲](https://www.microsoft.com//download/details.aspx?id=41653)に対するコネクタの送信アクセスを許可する。 Azure データセンターの IP 範囲の一覧を使用するうえでの課題は、この一覧が毎週更新されることにあります。 アクセス規則が適宜更新されるようにプロセスを整備する必要があります。 IP アドレスのサブセットのみを使用すると、構成が分断される可能性があります。

#### <a name="proxy-authentication"></a>プロキシの認証

プロキシの認証は現在サポートされていません。 現在はインターネット上のアクセス先に対して、コネクタの匿名アクセスを許可することをお勧めします。

#### <a name="proxy-ports"></a>プロキシ ポート

コネクタは、CONNECT メソッドを使用して SSL ベースの送信接続を確立します。 このメソッドにより、送信プロキシを経由するトンネルが設定されます。 プロキシ サーバーが、ポート 443 と 80 へのトンネリングを許可するように構成します。

>[!NOTE]
>Service Bus を HTTPS 経由で実行する場合は、ポート 443 が使用されます。 ただし、既定では、Service Bus は直接 TCP 接続を試みて、直接接続に失敗した場合にのみ HTTPS に戻ってきます。

#### <a name="ssl-inspection"></a>SSL インスペクション
コネクタのトラフィックに問題が生じるため、コネクタのトラフィックには SSL インスペクションを使用しないでください。 コネクタは証明書を使用してアプリケーション プロキシ サービスに対する認証を行いますが、その証明書が SSLインスペクションの間に失われることがあります。 

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>コネクタのプロキシの問題とサービスの接続の問題のトラブルシューティング
これですべてのトラフィックがプロキシを経由します。 問題が生じた場合は、次のトラブルシューティング情報が役に立ちます。

コネクタの接続の問題を特定してトラブルシューティングを行う最善の方法は、コネクタ サービスの開始時にネットワーク キャプチャを実行することです。 ネットワーク トレースをキャプチャおよびフィルター処理する際に役立つヒントを簡単に紹介します。

任意の監視ツールを使用することができます。 ここでは、Microsoft Message Analyzer を使用しています。 [これは Microsoft Web サイトからダウンロード](https://www.microsoft.com/download/details.aspx?id=44226)できます。

以下に示すのは Message Analyzer の例ですが、原則はどの分析ツールでも同じです。

### <a name="take-a-capture-of-connector-traffic"></a>コネクタ トラフィックのキャプチャを実行する

最初のトラブルシューティングでは、次の手順を実行します。

1. services.msc で、Azure AD アプリケーション プロキシ コネクタ サービスを停止します。

   ![services.msc の Azure AD アプリケーション プロキシ コネクタ サービス](./media/application-proxy-configure-connectors-with-proxy-servers/services-local.png)

2. Message Analyzer を管理者として実行します。
3. **[Start Local Trace]\(ローカル トレースの開始\)** を選択します。

   ![ネットワーク キャプチャの開始](./media/application-proxy-configure-connectors-with-proxy-servers/start-local-trace.png)

3. Azure AD アプリケーション プロキシ コネクタ サービスを開始します。
4. ネットワーク キャプチャを停止します。

   ![ネットワーク キャプチャの停止](./media/application-proxy-configure-connectors-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>コネクタのトラフィックが送信プロキシをバイパスするかどうかを確認する

アプリケーション プロキシ コネクタがプロキシ サーバーをバイパスしてアプリケーション プロキシ サービスに直接接続するよう構成している場合は、ネットワーク キャプチャを調べて、TCP 接続が失敗していないかを確認します。 

Message Analyzer のフィルターを使用すると、これらの試みを識別できます。 フィルターのボックスに `property.TCPSynRetransmit` と入力し、**[Apply]\(適用\)** を選択します。 

SYN パケットは、TCP 接続を確立するために最初に送信されるパケットです。 このパケットにより応答が返されない場合は、SYN パケットの送信が再試行されます。 前述のフィルターを使用すると、再送信されたすべての SYN パケットが表示されます。 次に、これらの SYN パケットがコネクタ関連のトラフィックに対応するかどうかを調べます。

コネクタが Azure サービスに直接接続するよう構成している場合、ポート 443 での SynRetransmit の応答は、ネットワークまたはファイアウォールの問題が発生していることを示しています。

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>コネクタのトラフィックが送信プロキシを使用しているかどうかを確認する

アプリケーション プロキシ コネクタのトラフィックがプロキシ サーバーを経由するよう構成している場合は、プロキシへの https 接続が失敗していないかを確認します。 

ネットワーク キャプチャをフィルター処理してこれらの試みを確認するには、Message Analyzer のフィルターで `(https.Request or https.Response) and tcp.port==8080` と入力し、ポート 8080 をご自身のプロキシ サービス ポートに置き換えます。 **[Apply]\(適用\)** を選択してフィルター結果を確認します。 

前述のフィルターにより、プロキシ ポートとの間の HTTPS 要求と応答のみが表示されます。 プロキシ サーバーとの通信を示す CONNECT 要求を調べます。 成功すると、HTTP OK (200) の応答が表示されます。

他の応答コード (407 や 502 など) が表示される場合は、プロキシが認証を必要としているか、何らかの理由でトラフィックを許可しないことを示しています。 その場合は、プロキシ サーバー サポート チームのサポートを得てください。

## <a name="next-steps"></a>次の手順

- [Azure AD アプリケーション プロキシ コネクタについて](application-proxy-connectors.md)

- コネクタの接続に問題がある場合は、[Azure Active Directory フォーラム](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD&forum=WindowsAzureAD)に質問を投稿するか、サポート チケットを作成してください。
