---
title: "VM とロール インスタンスの解決"
description: "Azure IaaS、ハイブリッド ソリューション、異なるクラウド サービス間、Active Directory、および独自 DNS サーバーの使用に関係する名前解決シナリオです  "
services: virtual-network
documentationcenter: na
author: GarethBradshawMSFT
manager: carmonm
editor: tysonn
ms.assetid: 5d73edde-979a-470a-b28c-e103fcf07e3e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/06/2016
ms.author: telmos
translationtype: Human Translation
ms.sourcegitcommit: b574360cce92350a9bf52c21678bf0e91ceb270e
ms.openlocfilehash: 479cf8cf358d0b242d8ce030d8639b493e4767d8


---
# <a name="name-resolution-for-vms-and-role-instances"></a>VM とロール インスタンスの名前解決
Azure を使用して IaaS、PaaS、ハイブリッド ソリューションをホストする方法によっては、作成する VM とロール インスタンスが互いに通信するように許可することが必要になる場合があります。 IP アドレスを使用してこの通信を行うこともできますが、簡単に記憶できて変更されない名前を使用する方が、はるかに簡単です。 

Azure でホストされているロール インスタンスと VM がドメイン名を内部 IP アドレスに解決する必要がある場合、次の&2; つの方法のいずれかを使用できます。

* [Azure で提供される名前解決](#azure-provided-name-resolution)
* [独自の DNS サーバーを使用する名前解決](#name-resolution-using-your-own-dns-server) (Azure で提供される DNS サーバーにクエリを転送する可能性がある) 

どちらの名前解決方法を使用するかは、VM とロール インスタンスが互いに通信するために必要な方法によって決まります。

**次の表では、シナリオと対応する名前解決の方法を示します。**

| **シナリオ** | **ソリューション** | **サフィックス** |
| --- | --- | --- |
| 同じクラウド サービスまたは仮想ネットワーク内に存在するロール インスタンスまたは VM 間での名前解決 |[Azure で提供される名前解決](#azure-provided-name-resolution) |ホスト名または FQDN |
| 異なる仮想ネットワーク内に存在するロール インスタンスまたは VM 間での名前解決 |Azure で解決するために VNet 間でクエリを転送するユーザーが管理する DNS サーバー (DNS プロキシ)。  「 [独自の DNS サーバーを使用する名前解決](#name-resolution-using-your-own-dns-server) |FQDN のみ |
| Azure 内のロール インスタンスまたは VM からのオンプレミスのコンピューターとサービスの名前解決 |ユーザーが管理する DNS サーバー (例: オンプレミスのドメイン コントローラー、ローカルの読み取り専用のドメイン コントローラー、ゾーン転送を使用して同期する DNS セカンダリ)。  See [独自の DNS サーバーを使用する名前解決](#name-resolution-using-your-own-dns-server) |FQDN のみ |
| オンプレミスのコンピューターからの Azure のホスト名解決 |対応する VNet 内のユーザーが管理する DNS プロキシ サーバーにクエリを転送し、プロキシ サーバーが解決するために Azure にクエリを転送します。 See [独自の DNS サーバーを使用する名前解決](#name-resolution-using-your-own-dns-server) |FQDN のみ |
| 内部 IP 用の逆引き DNS |[独自の DNS サーバーを使用する名前解決](#name-resolution-using-your-own-dns-server) |該当なし |
| 仮想ネットワーク内ではなく、異なるクラウド サービスに存在する VM またはロール インスタンス間での名前解決 |適用不可。 異なるクラウド サービス内にある VM とロール インスタンス間の接続は、仮想ネットワークの外側ではサポートされません。 |該当なし |

## <a name="azure-provided-name-resolution"></a>Azure で提供される名前解決
Azure では、パブリック DNS 名の解決と共に、同じ仮想ネットワークまたはクラウド サービス内に存在する VM とロール インスタンス用に内部の名前解決が提供されます。  クラウド サービスで VM またはインスタンスは同じ DNS サフィックスを共有しますが (そのため、ホスト名だけで十分)、従来の仮想ネットワークでは、クラウド サービスごとに DNS サフィックスが異なるため、異なるクラウド サービス間で名前を解決するには、FQDN が必要です。  Resource Manager デプロイ モデルの仮想ネットワークでは、DNS サフィックスは仮想ネットワーク間で共通であり (そのため、FQDN は必要ありません)、DNS 名は NIC と VM に割り当てることができます。 Azure 提供の名前解決ではどのような構成も不要ありませんが、前の表で示すように、すべてのデプロイメント シナリオに適しているわけではありません。

> [!NOTE]
> Web ロールおよび Worker ロールの場合は、Azure Service Management REST API を使用して、ロール名とインスタンス番号に基づいてロール インスタンスの内部 IP アドレスにアクセスすることもできます。 詳細については、「 [サービス管理 REST API リファレンス](https://msdn.microsoft.com/library/azure/ee460799.aspx)」を参照してください。
> 
> 

### <a name="features-and-considerations"></a>機能と注意事項
**機能:**

* 使いやすさ: Azure 提供の名前解決を使用するための構成は必要ありません。
* Azure で提供される名前解決サービスは高可用性を備えているため、独自の DNS サーバーのクラスターを作成、管理する必要はありません。
* オンプレミスと Azure の両方のホスト名を解決するために、独自の DNS サーバーと組み合わせて使用できます。
* 同じクラウド サービス内に存在するロール インスタンスまたは VM の間で、FQDN を必要としない名前解決が提供されます。
* Resource Manager デプロイ モデルが採用された仮想ネットワーク内の VM 間では、FQDN を必要としない名前解決が提供されます。 クラシック デプロイ モデルの仮想ネットワークの場合は、異なるクラウド サービスでの名前の解決時に FQDN が必要になります。 
* 自動生成される名前を使用するのではなく、デプロイの内容がよくわかるホスト名を使用できます。

**考慮事項:**

* Azure によって作成される DNS サフィックスは変更できません。
* 独自のレコードを手動で登録することはできません。
* WINS と NetBIOS はサポートされません  (Windows エクスプローラーで VM を参照することはできません)
* ホスト名は DNS 互換である必要があります (使用できる文字は 0 ～ 9、a ～ z、'-' のみであり、最初または最後の文字として '-' は使用できません。 RFC 3696 セクション 2 を参照してください)。
* DNS クエリ トラフィックは VM ごとに調整されます。 このことは、ほとんどのアプリケーションには影響がありません。  要求の調整が発生した場合は、クライアント側のキャッシュが有効になっていることを確認します。  詳細については、「 [Azure で提供される名前解決から最大限の効果を得る](#Getting-the-most-from-Azure-provided-name-resolution)」を参照してください。
* 最初の 180 のクラウド サービス内の VM だけが、クラシック デプロイ モデルの各仮想ネットワークに対して登録されます。 これは、Resource Manager デプロイ モデルの仮想ネットワークには当てはまりません。

### <a name="getting-the-most-from-azure-provided-name-resolution"></a>Azure で提供される名前解決から最大限の効果を得る
**クライアント側のキャッシュ:**

DNS クエリには、ネットワーク経由で送信する必要がないものもあります。  クライアント側のキャッシュは、ローカル キャッシュから繰り返される DNS クエリを解決することで、待機時間を短縮し、ネットワークの停止に対する復元性を高めることができます。  DNS レコードには、有効期限 (TTL) が含まれています。有効期限により、キャッシュは、レコードの鮮度に影響を与えずに、可能な限り長い時間レコードを格納できます。よって、クライアント側のキャッシュはほとんどの状況に適しています。

既定の Windows DNS クライアントには、組み込みの DNS キャッシュがあります。  Linux ディストリビューションの一部では、既定でキャッシュは含まれていません。(ローカル キャッシュが既に存在していないことを確認後) 各 Linux VM にキャッシュを追加することをお勧めします。

dnsmasq など、多様な DNS キャッシュ パッケージを利用できます。ここでは、最も一般的なディストリビューションに dnsmasq をインストールする手順を示します。

* **Ubuntu (resolvconf を使用)**:
  * dnsmasq パッケージをインストールする ("sudo apt-get install dnsmasq")
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
> 'dnsmasq' パッケージは、Linux で使用可能な多くの DNS キャッシュの&1; つにすぎません。  使用する前に、目的とするニーズに適合するかどうかと、その他のキャッシュがインストールされていないことを確認してください。
> 
> 

**クライアント側の再試行**

DNS では、主に UDP プロトコルが使用されます。  UDP プロトコルでは、メッセージの配信が保証されないため、再試行ロジックは、DNS プロトコル自体で処理されます。  各 DNS クライアント (オペレーティング システム) では、作成者の選択に応じて、再試行ロジックが異なる場合があります。

* Windows オペレーティング システムでは、1 秒後に再試行されます。その後、2 秒後、4 秒後に再試行され、さらにもう一度その 4 秒後に再試行されます。 
* 既定の Linux の設定では、5 秒後に再試行されます。  1 秒間隔で 5 回再試行するよう、この設定を変更することをお勧めします。  

Linux VM の現在の設定を確認するには、次に示す 'cat/etc/resolv.conf' の 'options' 行を確認します。

    options timeout:1 attempts:5

resolv.conf ファイルは、通常は自動生成され、編集する必要はありません。  'options' 行を追加する具体的な手順は、ディストリビューションによって異なります。

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
名前解決のニーズが Azure で提供される機能の範囲を超えるさまざまな状況があります。たとえば、Active Directory ドメインを使用する場合や、仮想ネットワーク (VNet) 間で DNS 解決を必要とする場合です。  これらのシナリオを可能にするために、Azure では独自の DNS サーバーを使用する機能を提供します。  

仮想ネットワーク内の DNS サーバーは、その仮想ネットワーク内のホスト名を解決するために、Azure の再帰的競合回避モジュールに DNS クエリを転送することができます。  たとえば、Azure で実行しているドメイン コントローラー (DC) は、そのドメインに対する DNS クエリに応答し、他のすべてのクエリを Azure に転送することができます。  これにより、VM はオンプレミスのリソース (DC 経由) と Azure で提供されるホスト名 (フォワーダー経由) の両方を参照できます。  Azure の再帰的競合回避モジュールへのアクセスは、仮想 IP 168.63.129.16 を通じて提供されます。

また、DNS の転送は、VNET 間の DNS 解決も可能にし、オンプレミスのマシンが Azure で提供されるホスト名を解決できるようにします。  VM のホスト名を解決するために、DNS サーバー VM は、同じ仮想ネットワークに存在し、Azure にホスト名のクエリを転送するように構成する必要があります。  DNS サフィックスは VNet ごとに異なるため、条件付きの転送ルールを使用し、解決するために正しい VNet に DNS クエリを送信します。  次の図は、2 つの VNet と、このメソッドを使用して VNet 間 DNS 解決を行うオンプレミスのネットワークを示しています。  DNS フォワーダーの例は、[Azure クイック スタート テンプレートのギャラリー](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/)と [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder) で確認できます。

![VNET 間 DNS](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Azure で提供される名前解決を使用すると、内部 DNS サフィックス (*.internal.cloudapp.net) が DHCP を使用して各 VM に提供されます。  これにより、ホスト名のレコードが internal.cloudapp.net ゾーン内にあるため、ホスト名を解決できます。  独自の名前解決のソリューションを使用している場合、IDNS サフィックスは他の DNS アーキテクチャに干渉するため (ドメイン参加シナリオなど)、VM には提供されません。  代わりに、機能していないプレース ホルダー (reddog.microsoft.com) が提供されます。  

必要に応じて、PowerShell または API を使用して、内部 DNS サフィックスを決定できます。

* Resource Manager デプロイメント モデルの仮想ネットワークの場合、サフィックスは[ネットワーク インターフェイス カード](https://msdn.microsoft.com/library/azure/mt163668.aspx) リソースまたは [Get AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) コマンドレットを通じて利用することができます。    
* クラシック デプロイ モデルの場合、サフィックスは [Get Deployment API](https://msdn.microsoft.com/library/azure/ee460804.aspx) 呼び出しまたは [Get-AzureVM -Debug](https://msdn.microsoft.com/library/azure/dn495236.aspx) コマンドレットを通じて利用することができます。

Azure へのクエリの転送がニーズに合わない場合は、独自の DNS ソリューションを提供する必要があります。  DNS 解決では次を行う必要があります。

* 適切なホスト名解決を提供する (例: [DDNS](virtual-networks-name-resolution-ddns.md)経由)。  DDNS を使用する場合、Azure の DHCP リースが非常に長く、清掃で時期を早めて DNS レコードを削除する可能性があるため、DNS レコードの清掃を無効にする必要がある場合があることに注意してください。 
* 適切な再帰的解決を提供し、外部ドメイン名の解決を可能にする。
* 対象のクライアントからのアクセスを可能にし (ポート 53 の TCP および UDP)、インターネットへのアクセスを可能にする。
* 外部エージェントによる脅威を軽減するために、インターネットからのアクセスをセキュリティ保護する。

> [!NOTE]
> 最適なパフォーマンスを得るには、DNS サーバーとして Azure VM を使用する場合、IPv6 を無効にして、 [インスタンスレベル パブリック IP](virtual-networks-instance-level-public-ip.md) を各 DNS サーバーの VM に割り当てる必要があります。  Windows サーバーを DNS サーバーとして使用する場合、 [この記事](http://blogs.technet.com/b/networking/archive/2015/08/19/name-resolution-performance-of-a-recursive-windows-dns-server-2012-r2.aspx) で、追加のパフォーマンス分析と最適化について説明しています。
> 
> 

### <a name="specifying-dns-servers"></a>DNS サーバーの指定
独自の DNS サーバーを使用する場合、Azure では、仮想ネットワークか、ネットワーク インターフェイス (Resource Manage)/クラウド サービス (クラシック) ごとに複数の DNS サーバーを指定できます。  クラウド サービス/ネットワーク インターフェイスに対して指定された DNS サーバーは、仮想ネットワークに対して指定された DNS サーバーよりも優先されます。

> [!NOTE]
> DNS サーバーの IP など、ネットワーク接続のプロパティは、Windows VM から直接編集しないようにしてください。仮想ネットワーク アダプターを交換したときのサービス回復時に消去される可能性があるためです。 
> 
> 

Resource Manager デプロイメント モデルを使用している場合は、ポータル、API/テンプレート ([vnet](https://msdn.microsoft.com/library/azure/mt163661.aspx)、[nic](https://msdn.microsoft.com/library/azure/mt163668.aspx))、または PowerShell ([vnet](https://msdn.microsoft.com/library/mt603657.aspx)、[nic](https://msdn.microsoft.com/library/mt619370.aspx)) で DNS サーバーを指定できます。

クラシック デプロイ モデルを使用している場合は、ポータルか、[*ネットワーク構成*ファイル](https://msdn.microsoft.com/library/azure/jj157100)で仮想ネットワークの DNS サーバーを指定できます。  クラウド サービスの DNS サーバーは、[*サービス構成*ファイル](https://msdn.microsoft.com/library/azure/ee758710)または PowerShell ([New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx)) で指定します。

> [!NOTE]
> 既にデプロイされている仮想ネットワーク/仮想マシンの DNS 設定を変更した場合、変更を有効にするには、関係する各 VM を再起動する必要があります。
> 
> 

## <a name="next-steps"></a>次のステップ
Resource Manager デプロイ モデル:

* [Create or update a virtual network (仮想ネットワークの作成または更新)](https://msdn.microsoft.com/library/azure/mt163661.aspx)
* [Create or update a network interface card (ネットワーク インターフェイス カードの作成または更新)](https://msdn.microsoft.com/library/azure/mt163668.aspx)
* [New-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx)
* [New-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx)

クラシック デプロイ モデル:

* [Azure サービス構成スキーマ](https://msdn.microsoft.com/library/azure/ee758710)
* [仮想ネットワークの構成スキーマ](https://msdn.microsoft.com/library/azure/jj157100)
* [ネットワーク構成ファイルを使用した仮想ネットワークの構成](virtual-networks-using-network-configuration-file.md) 




<!--HONumber=Dec16_HO2-->


