---
title: Microsoft Azure FXT Edge Filer クラスターのネットワーク設定を調整する
description: Azure FXT Edge Filer クラスターを作成した後にネットワーク設定をカスタマイズする方法
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 36ed354304cb1c88e48088f4b36c1ad0350af0dc
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543016"
---
# <a name="tutorial-configure-the-clusters-network-settings"></a>チュートリアル:クラスターのネットワーク設定を構成する 

新しく作成した Azure FXT Edge Filer クラスターを使用する前に、いくつかのネットワーク設定を確認し、実際のワークフローに合わせてカスタマイズする必要があります。 

このチュートリアルでは、新しいクラスターのために調整することが必要な場合があるネットワーク設定について説明します。 

学習内容: 

> [!div class="checklist"]
> * クラスターの作成後に更新が必要な可能性のあるネットワーク設定
> * AD サーバーまたは DNS サーバーが必要な Azure FXT Edge Filer のユース ケース 
> * FXT クラスターへのクライアント要求を自動的に負荷分散するようラウンド ロビン DNS (RRDNS) を構成する方法

これらの手順を完了するまでの時間は、ご使用のシステムに必要な構成の変更の数によって異なります。

* このチュートリアルを最後まで読み、いくつかの設定を確認する必要があるだけの場合は、10 分から 15 分かかります。 
* ラウンド ロビン DNS を構成する必要がある場合、そのタスクに 1 時間以上かかることがあります。

## <a name="adjust-network-settings"></a>ネットワーク設定を調整する

ネットワーク関連のいくつかのタスクは、新しい Azure FXT Edge Filer クラスターの設定の一部です。 この一覧を確認し、ご使用のシステムにどれを適用するかを決定します。

クラスターのネットワーク設定の詳細については、クラスター構成ガイドの[ネットワーク サービスの構成](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html)に関する項目をお読みください。

* クライアント側ネットワークに対してラウンド ロビン DNS を構成する (省略可能)

  [FXT Edge Filer クラスター用の DNS の構成](#configure-dns-for-load-balancing)に関するセクションの説明に従って DNS システムを構成して、クラスターのトラフィックを負荷分散します。

* NTP 設定を確認する

* Active Directory およびユーザー名とグループ名のダウンロードを構成する (必要な場合)

  ご使用のネットワーク ホストが Active Directory または他の種類の外部ディレクトリ サービスを使用している場合、クラスターのディレクトリ サービスの構成を変更して、クラスターがユーザー名とグループ情報をダウンロードする方法を設定する必要があります。 詳細については、クラスター構成ガイドの**クラスター** > **ディレクトリ サービス**に関する項目をお読みください。

  SMB サポートが必要な場合、AD サーバーが必要です。 SMB の設定を開始する前に AD を構成します。

* VLAN を定義する (省略可能)
  
  ご自分のクラスターの VServer とグローバル名前空間を定義する前に、必要な追加の VLAN を構成します。 詳細については、クラスター構成ガイドの [VLAN の操作](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview)に関する項目をお読みください。

* プロキシ サーバーを構成する (必要な場合)

  ご自分のクラスターで外部アドレスに到達するためにプロキシ サーバーを使用する場合は、これらの手順に従って設定します。

  1. **[プロキシの構成]** 設定ページで、プロキシ サーバーを定義します。
  1. **[クラスター]**  >  **[全般設定]** ページまたは **[Core Filer Details]\(コア ファイラーの詳細\)** ページを使用して、プロキシ サーバーの構成を適用します。
  
  詳細については、クラスター構成ガイドの [Web プロキシの使用](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/proxy_overview.html)に関する項目をお読みください。

* クラスターで使用するために[暗号化証明書](#encryption-certificates)をアップロードする (省略可能)

### <a name="encryption-certificates"></a>暗号化証明書

FXT Edge Filer クラスターでは、これらの機能に対して X.509 証明書を使用します。

* クラスターの管理トラフィックを暗号化する

* クライアントの代理としてサードパーティの KMIP サーバーに対して認証する

* クラウド プロバイダーのサーバー証明書を検証する

クラスターに証明書をアップロードする必要がある場合、 **[クラスター]**  >  **[証明書]** 設定ページを使用します。 詳細については、クラスター構成ガイドの[クラスター > 証明書](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_certificates.html)のページをお読みください。

クラスター管理の通信を暗号化するには、 **[クラスター]**  >  **[全般設定]** 設定ページを使用して、管理向けの SSL に使用する証明書を選択します。

> [!Note] 
> クラウド サービスのアクセス キーは、 **[Cloud Credentials]\(クラウド資格情報\)** 構成ページを使用して格納します。 上の「[コア ファイラーを追加する](fxt-add-storage.md#add-a-core-filer)」セクションでは例を示しています。詳細については、クラスター構成ガイドの[クラウド資格情報](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_credentials.html)に関するセクションをお読みください。 

## <a name="configure-dns-for-load-balancing"></a>負荷分散のために DNS を構成する

このセクションでは、ご自分の FXT Edge Filer クラスター内のすべてのクライアント側 IP アドレスに対してクライアントの負荷を分散するためのラウンド ロビン DNS (RRDNS) システムの構成の基礎について説明します。 

### <a name="decide-whether-or-not-to-use-dns"></a>DNS を使用するかどうかを決定する

常に負荷分散をお勧めしますが、必ずしも DNS を使用する必要はありません。 たとえば、クライアント ワークフローの種類によっては、クラスターをマウントするときに、スクリプトを使用して、クライアントに対して均等にクラスター IP アドレスを割り当てることが理にかなっている場合があります。 「[クラスターのマウント](fxt-mount-clients.md)」に、いくつかの方法が記載されています。 

DNS サーバーを使用するかどうかを決定する際は、以下の点に注意してください。 

* ご使用のシステムにアクセスするのが NFS クライアントのみの場合、DNS は必要ありません。 数値 IP アドレスを使用して、すべてのネットワーク アドレスを指定できます。 

* システムが SMB (CIFS) アクセスをサポートする場合、Active Directory サーバーの DNS ドメインを指定する必要があるため DNS が必要になります。

* Kerberos 認証を使用する場合、DNS が必要です。

### <a name="round-robin-dns-configuration-details"></a>ラウンド ロビン DNS の構成の詳細

クライアントがクラスターにアクセスするとき、RRDNS (ラウンドロビン DNS) は使用可能なすべてのインターフェイスの間で要求を自動的に分散します。

最適なパフォーマンスを得るには、次の図に示すように、クライアントに接続するクラスター アドレスを処理するように DNS サーバーを構成します。

クラスター vserver が左側に示され、IP アドレスが中央と右側に示されています。 図に示すように、A レコードとポインターを使用して各クライアントのアクセス ポイントを構成します。

![クラスターのラウンド ロビン DNS のダイアグラム - 画像の後に詳細の代替テキスト リンクが続く](media/fxt-cluster-config/fxt-rrdns-diagram.png) 
[詳細なテキストの説明](https://azure.github.io/Avere/legacy/Azure-FXT-EdgeFilerDNSconfiguration-alt-text.html)

クライアントに接続する IP アドレスには、クラスターによる内部使用のための一意の名前がそれぞれ必要です。 (この図では、わかりやすくするためにクライアント IP に vs1-client-IP-* という名前が付いていますが、運用環境では client* のような簡潔なものがおそらく使用されます。)

クライアントはサーバーの引数として vserver 名を使用してクラスターをマウントします。 

DNS サーバーの ``named.conf`` ファイルを変更して、vserver へのクエリの循環の順序を設定します。 このオプションにより、使用可能なすべての値が循環して使用されます。 次のようなステートメントを追加します。

```
options {
    rrset-order {
        class IN A name "vserver1.example.com" order cyclic;
    };
};
```

次の ``nsupdate`` コマンドは、DNS を正しく構成した例を示しています。

```
update add vserver1.example.com. 86400 A 10.0.0.10
update add vserver1.example.com. 86400 A 10.0.0.11
update add vserver1.example.com. 86400 A 10.0.0.12
update add vs1-client-IP-10.example.com. 86400 A 10.0.0.10
update add vs1-client-IP-11.example.com. 86400 A 10.0.0.11
update add vs1-client-IP-12.example.com. 86400 A 10.0.0.12
update add 10.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-10.example.com
update add 11.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-11.example.com
update add 12.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-12.example.com
```

### <a name="enable-dns-in-the-cluster"></a>クラスター内で DNS を有効にする 

クラスターが使用する DNS サーバーを、 **[クラスター]**  >  **[Administrative Network]\(管理ネットワーク\)** 設定ページで指定します。 このページでは、以下の内容を設定します。

* DNS サーバー アドレス
* DNS ドメイン名
* DNS 検索ドメイン

詳細については、クラスター構成ガイドの [DNS 設定](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>)に関する項目をお読みください。

## <a name="next-steps"></a>次の手順

これは、Azure FXT Edge Filer クラスターの基本的な構成の最後の手順です。 

* システムの LED およびその他のインジケーターについては、[ハードウェアの状態の監視](fxt-monitor.md)に関するページを参照してください。
* クライアントが FXT Edge Filer クラスターをマウントする方法の詳細については、「[クラスターのマウント](fxt-mount-clients.md)」を参照してください。 
* FXT Edge Filer クラスターの運用と管理の詳細については、[クラスター構成ガイド](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html)を参照してください。 