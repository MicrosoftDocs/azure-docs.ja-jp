---
title: "Azure の Linux VM での DNS 名前解決のオプション"
description: "Azure IaaS の Linux VM での名前解決のシナリオを示します (提供される DNS サービス、ハイブリッド外部 DNS、独自 DNS サーバーの使用など)。"
services: virtual-machines
documentationcenter: na
author: RicksterCDN
manager: timlt
editor: tysonn
ms.assetid: 787a1e04-cebf-4122-a1b4-1fcf0a2bbf5f
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2016
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 3c4bd01781b256542650add05faf5ec508be61ef


---
# <a name="dns-name-resolution-options-for-linux-vms-in-azure"></a>Azure の Linux VM での DNS 名前解決のオプション
Azure では、既定で、単一の Virtual Network 内に含まれるすべての VM の DNS 名の解決を提供しています。 Azure でホストされている VM に独自の DNS サービスを構成することにより、DNS 名を解決する独自のソリューションを実装できます。 次のシナリオは、どちらの方法が特定の状況に適しているかを判断するのに役立ちます。

* [Azure で提供される名前解決](#azure-provided-name-resolution)
* [独自の DNS サーバーを使用する名前解決](#name-resolution-using-your-own-dns-server) 

どちらの名前解決方法を使用するかは、VM とロール インスタンスが互いに通信するために必要な方法によって決まります。

**次の表では、シナリオと対応する名前解決の方法を示します。**

| **シナリオ** | **ソリューション** | **サフィックス** |
| --- | --- | --- |
| 同じ仮想ネットワーク内に存在するロール インスタンスまたは VM の間での名前解決 |[Azure で提供される名前解決](#azure-provided-name-resolution) |ホスト名または FQDN |
| 異なる仮想ネットワーク内に存在するロール インスタンスまたは VM 間での名前解決 |Azure で解決するために VNet 間でクエリを転送するユーザーが管理する DNS サーバー (DNS プロキシ)。  「 [独自の DNS サーバーを使用する名前解決](#name-resolution-using-your-own-dns-server) |FQDN のみ |
| Azure 内のロール インスタンスまたは VM からのオンプレミスのコンピューターとサービスの名前解決 |ユーザーが管理する DNS サーバー (たとえば、オンプレミスのドメイン コントローラー、ローカルの読み取り専用のドメイン コントローラー、ゾーン転送を使用して同期する DNS セカンダリ)。  See [独自の DNS サーバーを使用する名前解決](#name-resolution-using-your-own-dns-server) |FQDN のみ |
| オンプレミスのコンピューターからの Azure のホスト名解決 |対応する VNet 内のユーザーが管理する DNS プロキシ サーバーにクエリを転送し、プロキシ サーバーが解決するために Azure にクエリを転送します。 See [独自の DNS サーバーを使用する名前解決](#name-resolution-using-your-own-dns-server) |FQDN のみ |
| 内部 IP 用の逆引き DNS |[独自の DNS サーバーを使用する名前解決](#name-resolution-using-your-own-dns-server) |該当なし |

## <a name="azure-provided-name-resolution"></a>Azure で提供される名前解決
Azure では、パブリック DNS 名の解決と共に、同じ仮想ネットワーク内に存在する VM とロール インスタンス用に内部の名前解決が提供されます。  ARM ベースの仮想ネットワークでは、DNS サフィックスは仮想ネットワーク間で共通であり (そのため、FQDN は必要ありません)、DNS 名は NIC または VM に割り当てることができます。 Azure 提供の名前解決ではどのような構成も必要ありませんが、前の表で示すように、すべてのデプロイメント シナリオに適しているわけではありません。

### <a name="features-and-considerations"></a>機能と注意事項
**機能:**

* 使いやすさ: Azure 提供の名前解決を使用するための構成は必要ありません。
* Azure で提供される名前解決サービスは高可用性を備えているため、独自の DNS サーバーのクラスターを作成、管理する必要はありません。
* オンプレミスと Azure の両方のホスト名を解決するために、独自の DNS サーバーと一緒に使用できます。
* 仮想ネットワーク内の VM 間では、FQDN を必要としない名前解決が提供されます。 
* 自動生成される名前を使用するのではなく、デプロイの内容がよくわかるホスト名を使用できます。

**考慮事項:**

* Azure によって作成される DNS サフィックスは変更できません。
* 独自のレコードを手動で登録することはできません。
* WINS と NetBIOS はサポートされません 
* ホスト名は DNS 互換である必要があります (使用できる文字は 0 ～ 9、a ～ z、'-' のみであり、最初または最後の文字として '-' は使用できません。 RFC 3696 セクション 2 を参照してください)。
* DNS クエリ トラフィックは VM ごとに調整されます。 このことは、ほとんどのアプリケーションには影響がありません。  要求の調整が発生した場合は、クライアント側のキャッシュが有効になっていることを確認します。  詳しくは、「[Azure で提供される名前解決から最大限の効果を得る](#Getting-the-most-from-Azure-provided-name-resolution)」をご覧ください。

### <a name="getting-the-most-from-azure-provided-name-resolution"></a>Azure で提供される名前解決から最大限の効果を得る
**クライアント側のキャッシュ:**

DNS クエリには、ネットワーク経由で送信されないものもあります。  クライアント側のキャッシュは、ローカル キャッシュから繰り返される DNS クエリを解決することで、待機時間を短縮し、ネットワークの停止に対する復元性を高めることができます。  DNS レコードには、有効期限 (TTL) が含まれています。有効期限により、キャッシュは、レコードの鮮度に影響を与えずに、可能な限り長い時間レコードを格納できます。  このため、クライアント側のキャッシュはほとんどの状況に適しています。

Linux ディストリビューションの一部では、既定でキャッシュは含まれていません。  (ローカル キャッシュが既に存在していないことを確認後) 各 Linux VM にキャッシュを追加することをお勧めします。

dnsmasq など、複数の DNS キャッシュ パッケージを使用することができます。ここでは、最も一般的なディストリビューションに dnsmasq をインストールする手順を示します。

* **Ubuntu (resolvconf を使用)**:
  * dnsmasq パッケージをインストールする ("sudo apt-get install dnsmasq")。
* **SUSE (netconf を使用)**:
  * dnsmasq パッケージをインストールする ("sudo zypper install dnsmasq") 
  * dnsmasq サービスを有効にする ("systemctl enable dnsmasq.service") 
  * dnsmasq サービスを開始する ("systemctl start dnsmasq.service") 
  * "/etc/sysconfig/network/config" を編集し、NETCONFIG_DNS_FORWARDER="" を "dnsmasq" に変更する
  * キャッシュをローカル DNS リゾルバーとして設定するよう resolv.conf を更新する ("netconfig update")
* **OpenLogic (NetworkManager を使用)**:
  * dnsmasq パッケージをインストールする ("sudo yum install dnsmasq")
  * dnsmasq サービスを有効にする ("systemctl enable dnsmasq.service")
  * dnsmasq サービスを開始する ("systemctl start dnsmasq.service")
  * "prepend domain-name-servers 127.0.0.1;" を "/etc/dhclient-eth0.conf" に追加する
  * ネットワーク サービスを再起動し ("service network restart")、ローカル DNS リゾルバーとしてキャッシュを設定する

> [!NOTE]
> : 'dnsmasq' パッケージは、Linux で使用可能な多くの DNS キャッシュの 1 つにすぎません。  使用する前に、目的とするニーズに適合するかどうかと、その他のキャッシュがインストールされていないことを確認してください。
> 
> 

**クライアント側の再試行**

DNS では、主に UDP プロトコルが使用されます。  UDP プロトコルでは、メッセージの配信が保証されないため、再試行ロジックは、DNS プロトコル自体で処理されます。  各 DNS クライアント (オペレーティング システム) では、作成者の選択に応じて、再試行ロジックが異なる場合があります。

* Windows オペレーティング システムでは、1 秒後に再試行されます。その後、2 秒後、4 秒後に再試行され、さらにもう一度その 4 秒後に再試行されます。 
* 既定の Linux の設定では、5 秒後に再試行されます。  1 秒間隔で 5 回再試行するよう、この設定を変更する必要があります。  

Linux VM の現在の設定を確認するには、次に示す 'cat/etc/resolv.conf' の 'options' 行を確認します。

    options timeout:1 attempts:5

resolv.conf ファイルは自動生成され、編集すべきではありません。  'options' 行を追加する具体的な手順は、ディストリビューションによって異なります。

* **Ubuntu** (resolvconf を使用):
  * options 行を '/etc/resolveconf/resolv.conf.d/head' に追加する 
  * ' resolvconf -u' を実行して更新する
* **SUSE** (netconf を使用):
  * 'timeout:1 attempts:5' を '/etc/sysconfig/network/config' の NETCONFIG_DNS_RESOLVER_OPTIONS="" パラメーターに追加する 
  * 'netconfig update' を実行して更新する
* **OpenLogic** (NetworkManager を使用):
  * 'echo "options timeout:1 attempts:5"' を '/etc/NetworkManager/dispatcher.d/11-dhclient' に追加する 
  * 'service network restart' を実行して更新する

## <a name="name-resolution-using-your-own-dns-server"></a>独自 DNS サーバー使用の名前解決
名前解決のニーズが Azure で提供される機能の範囲を超える状況は複数あります。たとえば、仮想ネットワーク (VNet) 間で DNS 解決を必要とする場合です。  このシナリオを可能にするために、Azure では独自の DNS サーバーを使用する機能を提供します。  

仮想ネットワーク内の DNS サーバーは、その仮想ネットワーク内のホスト名を解決するために、Azure の再帰的競合回避モジュールに DNS クエリを転送することができます。  たとえば、Azure で実行している DNS サーバーは、自身の DNS ゾーン ファイルに対する DNS クエリに応答し、他のすべてのクエリを Azure に転送することができます。  これにより、VM はゾーン ファイル内のエントリと Azure で提供されるホスト名 (フォワーダー経由) の両方を参照することができます。  Azure の再帰的競合回避モジュールへのアクセスは、仮想 IP 168.63.129.16 を通じて提供されます。

また、DNS の転送は、VNET 間の DNS 解決も可能にし、オンプレミスのマシンが Azure で提供されるホスト名を解決できるようにします。  VM のホスト名を解決するには、DNS サーバー VM は、同じ仮想ネットワークに存在し、Azure にホスト名のクエリを転送するように構成する必要があります。  DNS サフィックスは VNet ごとに異なるため、条件付きの転送ルールを使用し、解決するために正しい VNet に DNS クエリを送信します。  次の図は、2 つの VNet と、このメソッドを使用して VNet 間 DNS 解決を行うオンプレミスのネットワークを示しています。

![VNET 間 DNS](./media/virtual-machines-linux-azure-dns/inter-vnet-dns.png)

Azure で提供される名前解決を使用すると、内部 DNS サフィックスが DHCP を使用して各 VM に提供されます。  独自の名前解決のソリューションを使用している場合、このサフィックスは他の DNS アーキテクチャに干渉するため、VM には提供されません。  FQDN を使用してマシンを参照するか、VM でサフィックスを構成するために、次の PowerShell または API を使用して、サフィックスを決定することができます。

* Azure リソース管理で管理されている VNet の場合、サフィックスは[ネットワーク インターフェイス カード](https://msdn.microsoft.com/library/azure/mt163668.aspx) リソース経由で使うことができます。または、コマンド `azure network public-ip show <resource group> <pip name>` を実行して、NIC の FQDN などのパブリック IP の詳細を表示できます。    

Azure へのクエリの転送がニーズに合わない場合は、独自の DNS ソリューションを提供する必要があります。  DNS 解決では次を行う必要があります。

* たとえば、 [DDNS](../virtual-network/virtual-networks-name-resolution-ddns.md)経由で、適切なホスト名解決を提供する。  DDNS を使用する場合、Azure の DHCP リースが非常に長く、清掃で時期を早めて DNS レコードを削除する可能性があるため、DNS レコードの清掃を無効にする必要がある場合があることに注意してください。 
* 適切な再帰的解決を提供し、外部ドメイン名の解決を可能にする。
* 対象のクライアントからのアクセスを可能にし (ポート 53 の TCP および UDP)、インターネットへのアクセスを可能にする。
* 外部エージェントによる脅威を軽減するために、インターネットからのアクセスをセキュリティ保護する。

> [!NOTE]
> 最適なパフォーマンスを得るには、DNS サーバーとして Azure VM を使用する場合、IPv6 を無効にして、 [インスタンスレベル パブリック IP](../virtual-network/virtual-networks-instance-level-public-ip.md) を各 DNS サーバーの VM に割り当てる必要があります。  
> 
> 




<!--HONumber=Nov16_HO3-->


