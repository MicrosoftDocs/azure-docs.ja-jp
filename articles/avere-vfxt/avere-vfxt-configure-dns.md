---
title: Avere vFXT DNS - Azure
description: Azure の Avere vFXT を使用してラウンド ロビン負荷分散のために DNS サーバーを構成する
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 9fd9eaf1e62d063026e0e656346baaaade87064f
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2019
ms.locfileid: "54187146"
---
# <a name="avere-cluster-dns-configuration"></a>Avere クラスターの DNS 構成

このセクションでは、Avere vFXT クラスターを負荷分散するための DNS システムの構成の基礎について説明します。 

このドキュメントには、Azure 環境内で DNS サーバーを設定して管理するための手順は*含まれていません*。 

Azure で vFXT クラスターを負荷分散するためにラウンドロビン DNS を使用する代わりに、クライアントをマウントするときに、手動による方法を使用して IP アドレスをクライアント間で均等に割り当てることを検討してください。 [Avere クラスターのマウント](avere-vfxt-mount-clients.md)に関する記事に、いくつかの方法が記載されています。 

DNS サーバーを使用するかどうかを決定する際は、以下の点に注意してください。 

* NFS クライアントのみがシステムにアクセスする場合、DNS を使用する必要はありません。数値 IP アドレスを使用して、すべてのネットワーク アドレスを指定できます。 

* システムが SMB (CIFS) アクセスをサポートする場合、Active Directory サーバーの DNS ドメインを指定する必要があるため DNS が必要になります。

* Kerberos 認証を使用する場合、DNS が必要です。

## <a name="load-balancing"></a>負荷分散

全体的に負荷を分散するには、クライアントに接続する IP アドレスのラウンド ロビン負荷分散を使用するよう DNS ドメインを構成します。

## <a name="configuration-details"></a>構成の詳細

クライアントがクラスターにアクセスするとき、RRDNS (ラウンドロビン DNS) は使用可能なすべてのインターフェイスの間で要求を自動的に分散します。

最適なパフォーマンスを得るには、次の図に示すように、クライアントに接続するクラスター アドレスを処理するように DNS サーバーを構成します。

クラスター vserver が左側に示され、IP アドレスが中央と右側に示されています。 図に示すように、A レコードとポインターを使用して各クライアントのアクセス ポイントを構成します。

![Avere クラスターのラウンドロビン DNS 図](media/avere-vfxt-rrdns-diagram.png) 
<!--- separate text description file provided  [diagram text description](avere-vfxt-rrdns-alt-text.md) -->

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

次の nsupdate コマンドは、DNS を正しく構成した例を示しています。

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

## <a name="cluster-dns-settings"></a>クラスター DNS 設定

vFXT クラスターが使用する DNS サーバーを、**[クラスター]** > **[Administrative Network]\(管理ネットワーク)** 設定ページで指定します。 このページでは、以下の内容を設定します。

* DNS サーバー アドレス
* DNS ドメイン名
* DNS 検索ドメイン

このページの使用についての詳細は、Avere クラスター構成ガイドの[DNS 設定](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>)に関するページをお読みください。


