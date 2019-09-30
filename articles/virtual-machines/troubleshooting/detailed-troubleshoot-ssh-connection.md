---
title: Azure VM の SSH の詳細なトラブルシューティング | Microsoft Docs
description: Azure 仮想マシンへの接続問題に関する SSH の詳細なトラブルシューティングの手順
keywords: 拒否されたSSH 接続,SSH エラー,Azure SSH,失敗した SSH 接続
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: b8e8be5f-e8a6-489d-9922-9df8de32e839
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 3807f713065d16d4c6743c65f6a770d158ac7191
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058494"
---
# <a name="detailed-ssh-troubleshooting-steps-for-issues-connecting-to-a-linux-vm-in-azure"></a>Azure での Linux VM 接続問題に関する SSH の詳細なトラブルシューティングの手順
SSH クライアントは、さまざまな理由で VM 上の SSH サービスに到達できない可能性があります。 [SSH のトラブルシューティングの一般的な手順](troubleshoot-ssh-connection.md)に従った場合は、接続の問題について詳細なトラブルシューティングを行う必要があります。 この記事では、詳細なトラブルシューティング手順を説明し、SSH 接続に失敗した場所の特定とその解決方法を確認します。

## <a name="take-preliminary-steps"></a>準備作業を行う
次の図は、関係しているコンポーネントを示しています。

![SSH サービスのコンポーネントを示す図](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

次の手順は、エラーの原因を分離し、解決策や回避策を見つけ出すのに役立ちます。

1. ポータルで VM の状態を確認します。
   [[Azure Portal ]](https://portal.azure.com) で **[仮想マシン]**  >  *[VM 名]* の順に選択します。

   VM の状態ウィンドウには、" **実行中**" と表示されます。 コンピューティング、ストレージ、およびネットワーク リソースの最近のアクティビティを確認するには、下にスクロールします。

2. エンドポイント、IP アドレス、ネットワーク セキュリティ グループなどの設定を確認するには、 **[設定]** を選択します。

   VM には、 **[エンドポイント]** または **[[ネットワーク セキュリティ グループ]](../../virtual-network/security-overview.md)** で確認できる SSH トラフィック用に定義されたエンドポイントが必要です。 Resource Manager を使用して作成された VM のエンドポイントは、ネットワーク セキュリティ グループに格納されています。 ネットワーク セキュリティ グループにルールが適用され、サブネットで参照されていることを確認します。

ネットワーク接続を確認するには、構成されているエンドポイントを確認します。また、HTTP などの別のプロトコルや他のサービスを使用して、VM に接続できるかどうかを確認します。

以上の手順を完了したら、SSH 接続を再試行してください。

## <a name="find-the-source-of-the-issue"></a>問題のソースを見つける
お使いのコンピューター上の SSH クライアントは、以下の領域に問題または誤構成があるために Azure VM の SSH サービスに接続できないことがあります。

* [SSH クライアント コンピューター](#source-1-ssh-client-computer)
* [組織の境界デバイス](#source-2-organization-edge-device)
* [クラウド サービス エンドポイントとアクセス制御リスト (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [ネットワーク セキュリティ グループ](#source-4-network-security-groups)
* [Linux ベースの Azure VM](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>ソース 1: SSH クライアント コンピューター
エラーのソースであるコンピューターを排除するには、そのコンピューターから他のオンプレミスの Linux コンピューターに SSH 接続できることを確認します。

![SSH クライアント コンピューターのコンポーネントを示す図](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

接続できない場合は、そのコンピューターで以下の問題を確認してください。

* 受信または送信の SSH トラフィック (TCP 22) をブロックしているローカル ファイアウォールの設定
* SSH 接続を妨げている、ローカルでインストールされたクライアント プロキシのソフトウェア
* SSH 接続を妨げている、ローカルでインストールされたネットワーク監視ソフトウェア
* トラフィックを監視する、あるいは特定種類のトラフィックを許可または禁止する別の種類のセキュリティ ソフトウェア

これらの条件のいずれかに該当する場合は、ソフトウェアを一時的に無効にし、オンプレミス コンピューターへの SSH 接続を試して、ご使用のコンピューターで接続がブロックされている理由を調べます。 次に、ネットワーク管理者とともに、SSH 接続が可能になるようにソフトウェアの設定を修正してください。

証明書認証を使用している場合は、ホーム ディレクトリの .ssh フォルダーに対してこれらのアクセス許可があることを確認します。

* Chmod 700 ~/.ssh
* Chmod 644 ~/.ssh/\*.pub
* Chmod 600 ~/.ssh/id_rsa (またはプライベート キーが格納されている他のファイル)
* Chmod 644 ~/.ssh/known_hosts (SSH 経由で接続したホストが含まれます)

## <a name="source-2-organization-edge-device"></a>ソース 2: 組織の境界デバイス
エラーのソースである組織のエッジ デバイスを排除するには、インターネットに直接接続されているコンピューターから Azure VM に SSH 接続できることを確認します。 サイト間 VPN または Azure ExpressRoute 接続経由で VM にアクセスしている場合は、「[ソース 4: ネットワーク セキュリティ グループ](#nsg)」に進みます。

![組織の境界デバイスを示す図](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

インターネットに直接接続されているコンピューターがない場合は、新しい Azure VM を独自のリソース グループまたはクラウド サービスに作成し、その新しい VM を使用します。 詳細については、「[Azure 上で Linux を実行する仮想マシンの作成](../linux/quick-create-cli.md)」を参照してください。 テストの完了後に、そのリソース グループまたは VM と、クラウド サービスを削除します。

インターネットに直接接続されているコンピューターとの SSH 接続を作成できる場合は、組織のエッジ デバイスで以下を確認してください。

* インターネットでの SSH トラフィックをブロックしている内部ファイアウォール
* SSH 接続を妨げているプロキシ サーバー
* SSH 接続を妨げている、エッジ ネットワーク内のデバイスで実行されている侵入検出またはネットワーク監視ソフトウェア

ネットワーク管理者と協力して、インターネットでの SSH トラフィックを許可するように組織のエッジ デバイスの設定を修正します。

## <a name="source-3-cloud-service-endpoint-and-acl"></a>ソース 3: クラウド サービス エンドポイントと ACL
> [!NOTE]
> このソースは、クラシック デプロイ モデルを使用して作成された VM にのみ適用されます。 Resource Manager を使用して作成された VM の場合は、「[ソース 4: ネットワーク セキュリティ グループ](#nsg)」に進みます。

エラーのソースであるクラウド サービス エンドポイントと ACL を排除するには、同じ仮想ネットワーク内の別の Azure VM から SSH を使用して接続できることを確認します。

![クラウド サービス エンドポイントと ACL を示す図](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

同じ仮想ネットワーク内に別の VM がない場合、VM を簡単に作成することができます。 詳細については、「[CLI を使用した Azure での Linux VM の作成](../linux/quick-create-cli.md)」をご覧ください。 テストの完了後に、追加した VM を削除してください。

同じ仮想ネットワーク内にある VM に対して SSH 接続を作成できる場合は、次の点を確認します。

* **ターゲットの VM での SSH トラフィック向けエンドポイントの構成。** エンドポイントのプライベート TCP ポートは、VM 上の SSH サービスがリッスンする TCP ポートと一致する必要があります (既定のポートは 22 です)。 Azure Portal で **[仮想マシン]**  >  *[VM 名]*  >  **[設定]**  >  **[エンドポイント]** を選択して、SSH TCP ポート番号を確認します。
* **ターゲットの仮想マシンでの、SSH トラフィック向けエンドポイントの ACL。** ACL を使用すると、発信元 IP アドレスに基づいて、インターネットからの受信トラフィックを許可または拒否するかを指定できます。 ACL が正しく構成されていないと、そのエンドポイントへの SSH 受信トラフィックを受け取れない場合があります。 プロキシまたは他のエッジ サーバーのパブリック IP アドレスからの受信トラフィックが ACL で許可されていることを確認します。 詳細については、 [ネットワーク アクセス制御リスト (ACL) の概要](../../virtual-network/virtual-networks-acl.md)に関するページを参照してください。

問題のソースであるエンドポイントを排除するには、現在のエンドポイントを削除し、別のエンドポイントを作成して、SSH 名を指定します (パブリックとプライベートのポート番号には TCP ポート 22)。 詳細については、「[Azure での仮想マシンに対するエンドポイントの設定](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)」をご覧ください。

<a id="nsg"></a>

## <a name="source-4-network-security-groups"></a>ソース 4: ネットワーク セキュリティ グループ
ネットワーク セキュリティ グループでは、許可された受信トラフィックと送信トラフィックをより細かく制御できます。 Azure 仮想ネットワーク内のサブネットまたはクラウド サービスの全体に適用されるルールを作成することができます。 ネットワーク セキュリティ グループ ルールで、インターネットからの SSH トラフィックが許可されていることを確認します。
詳細については、「 [ネットワーク セキュリティ グループについて](../../virtual-network/security-overview.md)」をご覧ください。

NSG 構成の検証に IP Verify を使用することもできます。 詳細については、「[Azure のネットワーク監視の概要](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)」を参照してください。 

## <a name="source-5-linux-based-azure-virtual-machine"></a>ソース 5: Linux ベースの Azure 仮想マシン
最後に考えられる問題のソースは、Azure 仮想マシン自体に関連するものです。

![Linux ベースの Azure 仮想マシンを示す図](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

[Linux ベースの仮想マシンのパスワードをリセットする](../linux/reset-password.md)手順をまだ実行していない場合は、仮想マシンでその手順に従います。

もう一度、コンピューターから接続を試みてください。 引き続き接続できない場合は、以下の問題が考えられます。

* SSH サービスがターゲット仮想マシンで実行されていない。
* SSH サービスが TCP ポート 22 をリッスンしていない。 テストするには、ローカル コンピューターに telnet クライアントをインストールし、"telnet *cloudServiceName*.cloudapp.net 22" を実行します。 この手順で、SSH エンドポイントに対する受信方向と送信方向の通信が仮想マシンで許可されているかどうかを確認できます。
* ターゲット仮想マシンのローカル ファイアウォールに、受信または送信の SSH トラフィックを妨げているルールがある。
* Azure 仮想マシンで実行されている侵入検出ソフトウェアまたはネットワーク監視ソフトウェアが、SSH 接続を妨げている。

## <a name="additional-resources"></a>その他のリソース
アプリケーションへのアクセスのトラブルシューティングに関する詳細については、「[Azure 仮想マシンで実行されているアプリケーションへのアクセスに関するトラブルシューティング](../linux/troubleshoot-app-connection.md)」を参照してください。
