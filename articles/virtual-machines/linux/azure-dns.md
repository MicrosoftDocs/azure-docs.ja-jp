---
title: Linux VM での DNS 名前解決のオプション
description: Azure IaaS での Linux 仮想マシンの名前解決のシナリオを示します (提供される DNS サービス、ハイブリッド外部 DNS、独自 DNS サーバーの使用など)。
author: RicksterCDN
ms.service: virtual-machines
ms.subservice: networking
ms.topic: conceptual
ms.date: 10/19/2016
ms.author: rclaus
ms.collection: linux
ms.openlocfilehash: e689e934f11e3cc2a621f25525e507a72a7044b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102556793"
---
# <a name="dns-name-resolution-options-for-linux-virtual-machines-in-azure"></a>Azure での Linux 仮想マシンの DNS 名前解決のオプション
Azure では、既定で、単一の仮想ネットワーク内に含まれるすべての仮想マシンの DNS 名の解決を提供しています。 Azure でホストされている仮想マシンに独自の DNS サービスを構成することにより、DNS 名を解決する独自のソリューションを実装できます。 次のシナリオは、どちらの方法が状況に適しているかを判断するのに役立ちます。

* [Azure で提供されている名前解決](#name-resolution-that-azure-provides)
* [独自の DNS サーバーを使用する名前解決](#name-resolution-using-your-own-dns-server)

どちらの名前解決方法を使用するかは、仮想マシンとロール インスタンスが互いに通信するために必要な方法によって決まります。

次の表では、シナリオと対応する名前解決の方法を示します。

| **シナリオ** | **ソリューション** | **サフィックス** |
| --- | --- | --- |
| 同じ仮想ネットワーク内のロール インスタンスまたは仮想マシン間での名前解決 |Azure で提供されている名前解決 |ホスト名または完全修飾ドメイン名 (FQDN) |
| 異なる仮想ネットワーク内のロール インスタンスまたは仮想マシン間での名前解決 |Azure で解決するために仮想ネットワーク間でクエリを転送する、ユーザーが管理する DNS サーバー (DNS プロキシ)。 「[独自の DNS サーバーを使用する名前解決](#name-resolution-using-your-own-dns-server)」を参照してください。 |FQDN のみ |
| Azure でのロール インスタンスまたは仮想マシンからのオンプレミスのコンピューターとサービスの名前解決 |ユーザーが管理する DNS サーバー (たとえば、オンプレミスのドメイン コントローラー、ローカルの読み取り専用のドメイン コントローラー、ゾーン転送を使用して同期する DNS セカンダリ)。 「[独自の DNS サーバーを使用する名前解決](#name-resolution-using-your-own-dns-server)」を参照してください。 |FQDN のみ |
| オンプレミスのコンピューターからの Azure のホスト名解決 |対応する仮想ネットワーク内のユーザーが管理する DNS プロキシ サーバーにクエリを転送します。 プロキシ サーバーは、解決のために Azure にクエリを転送します。 「[独自の DNS サーバーを使用する名前解決](#name-resolution-using-your-own-dns-server)」を参照してください。 |FQDN のみ |
| 内部 IP 用の逆引き DNS |[独自の DNS サーバーを使用する名前解決](#name-resolution-using-your-own-dns-server) |該当なし |

## <a name="name-resolution-that-azure-provides"></a>Azure で提供されている名前解決
Azure では、パブリック DNS 名の解決と共に、同じ仮想ネットワーク内の仮想マシンとロール インスタンス用に内部の名前解決が提供されます。 Azure Resource Manager に基づく仮想ネットワークでは、仮想ネットワーク全体で DNS サフィックスに一貫性があり、FQDN は必要ありません。 DNS 名は、ネットワーク インターフェイス カード (NIC) と仮想マシンの両方に割り当てることができます。 Azure 提供の名前解決ではどのような構成も必要ありませんが、前の表で示すように、すべてのデプロイメント シナリオに適しているわけではありません。

### <a name="features-and-considerations"></a>機能と注意事項
**機能:**

* Azure が提供する名前解決を使用するための構成は必要ありません。
* Azure が提供する名前解決サービスは高可用性です。 独自の DNS サーバーのクラスターを作成および管理する必要はありません。
* Azure が提供する名前解決サービスは、オンプレミスと Azure の両方のホスト名を解決するために、独自の DNS サーバーと一緒に使用できます。
* 仮想ネットワーク内の仮想マシン間では、FQDN を必要としない名前解決が提供されます。
* 自動生成される名前を使用するのではなく、デプロイの内容がよくわかるホスト名を使用できます。

**考慮事項:**

* Azure が作成する DNS サフィックスは変更できません。
* 独自のレコードを手動で登録することはできません。
* WINS と NetBIOS はサポートされません 
* ホスト名は DNS 互換である必要があります。
    使用できる文字は 0 ～ 9、a ～ z、'-' のみであり、最初または最後の文字として '-' は使用できません。 RFC 3696 セクション 2 を参照してください。
* DNS クエリ トラフィックは仮想マシンごとに調整されます。 調整は、ほとんどのアプリケーションに影響がありません。  要求の調整が発生した場合は、クライアント側のキャッシュが有効になっていることを確認します。  詳しくは、「[Azure が提供する名前解決から最大限の効果を得る](#getting-the-most-from-name-resolution-that-azure-provides)」をご覧ください。

### <a name="getting-the-most-from-name-resolution-that-azure-provides"></a>Azure が提供する名前解決から最大限の効果を得る
**クライアント側のキャッシュ:**

一部の DNS クエリは、ネットワーク経由で送信されません。 クライアント側のキャッシュは、ローカル キャッシュから繰り返される DNS クエリを解決することで、待機時間を短縮し、ネットワークの不整合に対する復元性を高めることができます。 DNS レコードには、有効期限 (TTL) が含まれています。有効期限により、キャッシュは、レコードの鮮度に影響を与えずに、可能な限り長い時間レコードを格納できます。 このため、クライアント側のキャッシュはほとんどの状況に適しています。

Linux ディストリビューションの一部では、既定でキャッシュは含まれていません。 ローカル キャッシュがまだ含まれていないことを確認したら、各 Linux 仮想マシンにキャッシュを追加することをお勧めします。

dnsmasq など、いくつかの異なる DNS キャッシュ パッケージを利用できます。 最も一般的なディストリビューションに dnsmasq をインストールする手順を次に示します。

**Ubuntu (resolvconf を使用)**
  * dnsmasq パッケージをインストールします ("sudo apt-get install dnsmasq")。

**SUSE (netconf を使用)** :
1. dnsmasq パッケージをインストールします ("sudo zypper install dnsmasq")。
2. dnsmasq サービスを有効にします ("systemctl enable dnsmasq.service")。
3. dnsmasq サービスを開始します ("systemctl start dnsmasq.service")。
4. "/etc/sysconfig/network/config" を編集し、NETCONFIG_DNS_FORWARDER="" を "dnsmasq" に変更します。
5. キャッシュをローカル DNS リゾルバーとして設定するように resolv.conf を更新します ("netconfig update")。

**Rogue Wave Software (以前の OpenLogic) の CentOS (NetworkManager を使用)**
1. dnsmasq パッケージをインストールします ("sudo yum install dnsmasq")。
2. dnsmasq サービスを有効にします ("systemctl enable dnsmasq.service")。
3. dnsmasq サービスを開始します ("systemctl start dnsmasq.service")。
4. "prepend domain-name-servers 127.0.0.1;" を "/etc/dhclient-eth0.conf" に追加します。
5. ネットワーク サービスを再起動し ("service network restart")、ローカル DNS リゾルバーとしてキャッシュを設定する

> [!NOTE]
> :'dnsmasq' パッケージは、Linux で使用可能な多くの DNS キャッシュの 1 つにすぎません。 使用する前に、目的とするニーズに適合するかどうかと、その他のキャッシュがインストールされていないことを確認してください。
>
>

**クライアント側の再試行**

DNS では、主に UDP プロトコルが使用されます。 UDP プロトコルでは、メッセージの配信が保証されないため、DNS プロトコル自体で再試行ロジックが処理されます。 各 DNS クライアント (オペレーティング システム) では、作成者の選択に応じて、再試行ロジックが異なる場合があります。

* Windows オペレーティング システムでは、1 秒後に再試行されます。その後、2 秒後、4 秒後に再試行され、さらにもう一度その 4 秒後に再試行されます。
* 既定の Linux の設定では、5 秒後に再試行されます。  1 秒間隔で 5 回再試行するよう、この設定を変更する必要があります。  

Linux 仮想マシンの現在の設定を確認するには、次に示す 'cat/etc/resolv.conf' の 'options' 行を確認します。

```config-conf
options timeout:1 attempts:5
```

resolv.conf ファイルは自動生成され、編集すべきではありません。 'options' 行を追加する具体的な手順は、ディストリビューションによって異なります。

**Ubuntu** (resolvconf を使用)
1. options 行を '/etc/resolvconf/resolv.conf.d/head' に追加します。
2. ' resolvconf -u' を実行して更新します。

**SUSE** (netconf を使用)
1. 'timeout:1 attempts:5' を '/etc/sysconfig/network/config' の NETCONFIG_DNS_RESOLVER_OPTIONS="" パラメーターに追加します。
2. 'netconfig update' を実行して更新します。

**Rogue Wave Software (以前の OpenLogic) の CentOS** (NetworkManager を使用)
1. 'RES_OPTIONS="timeout:1 attempts:5"' を '/etc/sysconfig/network' に追加します。
2. 'service network restart' を実行して更新します。

## <a name="name-resolution-using-your-own-dns-server"></a>独自 DNS サーバー使用の名前解決
名前解決のニーズが、Azure で提供される機能の範囲を超えている場合もあります。 たとえば、仮想ネットワーク間の DNS 解決を必要とする場合があります。 このシナリオに対応するには、独自の DNS サーバーを使用できます。  

仮想ネットワーク内の DNS サーバーは、同じ仮想ネットワーク内にあるホスト名を解決するために、Azure の再帰的競合回避モジュールに DNS クエリを転送することができます。 たとえば、Azure で実行している DNS サーバーは、自身の DNS ゾーン ファイルに対する DNS クエリに応答し、他のすべてのクエリを Azure に転送することができます。 この機能により、仮想マシンがゾーン ファイル内のエントリと Azure が提供するホスト名の両方を参照できます (フォワーダー経由で)。 Azure の再帰的競合回避モジュールへのアクセスは、仮想 IP 168.63.129.16 を通じて提供されます。

DNS 転送は、仮想ネットワーク間の DNS 解決も可能にし、オンプレミスのマシンで、Azure が提供するホスト名を解決できます。 仮想マシンのホスト名を解決するため、DNS サーバー仮想マシンは、同じ仮想ネットワーク内に存在し、Azure にホスト名のクエリを転送するように構成する必要があります。 DNS サフィックスは仮想ネットワークごとに異なるため、条件付きの転送ルールを使用し、解決のために正しい仮想ネットワークに DNS クエリを送信します。 次の図は、2 つの仮想ネットワークと、このメソッドを使用して仮想ネットワーク間 DNS 解決を行うオンプレミスのネットワークを示しています。

![仮想ネットワーク間の DNS 解決](./media/azure-dns/inter-vnet-dns.png)

Azure が提供する名前解決を使用すると、内部 DNS サフィックスが DHCP を使用して各仮想マシンに提供されます。 独自の名前解決のソリューションを使用している場合、このサフィックスは他の DNS アーキテクチャに干渉するため、仮想マシンには提供されません。 FQDN を使用してマシンを参照するか、VM でサフィックスを構成するために、次の PowerShell または API を使用して、サフィックスを決定することができます。

* Azure Resource Manager によって管理される仮想ネットワークでは、サフィックスは[ネットワーク インターフェイス カード](/rest/api/virtualnetwork/networkinterfaces) リソースから入手できます。 `azure network public-ip show <resource group> <pip name>` コマンドを実行して、NIC の FQDN が含まれているパブリック IP の詳細を表示することもできます。

Azure へのクエリの転送がニーズに合わない場合は、独自の DNS ソリューションを提供する必要があります。  DNS 解決では次を行う必要があります。

* たとえば、 [DDNS](../../virtual-network/virtual-networks-name-resolution-ddns.md)経由で、適切なホスト名解決を提供する。 DDNS を使用する場合、DNS レコードの清掃を無効にする必要がある場合があります。 Azure の DHCP リースがきわめて長く、清掃によって、完了前に DNS レコードが削除されることがあります。
* 適切な再帰的解決を提供し、外部ドメイン名の解決を可能にする。
* 対象のクライアントからのアクセスを可能にし (ポート 53 の TCP および UDP)、インターネットへのアクセスを可能にする。
* 外部エージェントによる脅威を軽減するために、インターネットからのアクセスをセキュリティ保護する。

> [!NOTE]
> 最適なパフォーマンスのため、Azure DNS サーバーで仮想マシンを使用する場合は、IPv6 を無効にし、各 DNS サーバー仮想マシンに[インスタンスレベル パブリック IP](/previous-versions/azure/virtual-network/virtual-networks-instance-level-public-ip) を割り当てます。  
>
>
