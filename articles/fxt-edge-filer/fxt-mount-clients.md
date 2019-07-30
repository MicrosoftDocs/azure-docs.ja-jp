---
title: クライアントを Microsoft Azure FXT Edge Filer クラスターにマウントする
description: NFS クライアント マシンを Azure FXT Edge Filer ハイブリッド ストレージ キャッシュにマウントする方法
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 5471bf4041275d5988414def99dd2130f51fbb80
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67828035"
---
# <a name="tutorial-mount-the-cluster"></a>チュートリアル:クラスターのマウント

このチュートリアルでは、Azure FXT Edge Filer クラスターに NFS クライアントをマウントする方法を説明します。 クライアントは、バックエンド ストレージを追加するときに割り当てた仮想名前空間のパスをマウントします。 

このチュートリアルの内容: 

> [!div class="checklist"]
> * クライアント側の IP アドレスの範囲にわたってクライアントを負荷分散するための戦略
> * クライアント側の IP アドレスおよび名前空間ジャンクションからマウント パスを作成する方法
> * mount コマンドで使用する引数

このチュートリアルの所要時間は約 45 分です。

## <a name="steps-to-mount-the-cluster"></a>クラスターをマウントする手順

クライアント マシンを Azure FXT Edge Filer クラスターに接続するには以下の手順に従います。

1. クラスター ノード間にクライアント トラフィックの負荷を分散させる方法を決定します。 詳細については、後の「[クライアントの負荷を分散させる](#balance-client-load)」をご覧ください。 
1. マウントするクラスター IP アドレスとジャンクション パスを識別します。
1. マウントのクライアント側パスを決定します。
1. 適切な引数を指定して [mount コマンド](#use-recommended-mount-command-options)を発行します。

## <a name="balance-client-load"></a>クライアントの負荷を分散させる

クライアントの要求をクラスター内のすべてのノード間に分散させるには、クライアント側の IP アドレスの全範囲にクライアントをマウントする必要があります。 このタスクを自動化するには、いくつかの方法があります。

クラスターのラウンド ロビン DNS 負荷分散の詳細については、[Azure FXT Edge Filer クラスター用の DNS の構成](fxt-configure-network.md#configure-dns-for-load-balancing)に関する記事を参照してください。 この方法を使用するには DNS サーバーを管理する必要がありますが、管理についてはこの記事には記載されていません。

小規模インストール用の単純な方法として、スクリプトを使用して、クライアントのマウント時に範囲全体に IP アドレスを割り当てる方法があります。 

大規模なシステや複雑なシステムには、他の負荷分散方法が適しています。 Microsoft の担当者に問い合わせるか、ヘルプの[サポート リクエスト](fxt-support-ticket.md)をオープンしてください。 (現在、Azure Load Balancer は Azure FXT Edge Filer で*サポートされていません*。)

## <a name="create-the-mount-command"></a>mount コマンドを作成する 

クライアントで ``mount`` コマンドを実行すると、Azure FXT Edge Filer クラスター上の仮想サーバー (vserver) が、ローカル ファイル システムのパスにマップされます。 

形式は ``mount <FXT cluster path> <local path> {options}`` です

mount コマンドには 3 つの要素があります。 

* クラスター パス - 後で説明する、IP アドレスと名前空間ジャンクション パスの組み合わせ
* ローカル パス - クライアント上のパス 
* mount コマンドのオプション - (「[推奨される mount コマンド オプションを使用する](#use-recommended-mount-command-options)」で説明)

### <a name="create-the-cluster-path"></a>クラスター パスを作成する

クラスター パスは、vserver の "*IP アドレス*" と、"*名前空間ジャンクション*" へのパスの組み合わせです。 名前空間ジャンクションは、[ストレージ システムを追加した](fxt-add-storage.md#create-a-junction)ときに定義した仮想パスです。

たとえば、名前空間のパスとして ``/fxt/files`` を使用した場合、クライアントは <*IP アドレス*>:/fxt/files をローカルなマウント ポイントにマウントします。 

![[Namespace path]\(名前空間のパス\) フィールドに "/avere/files" が設定された [Add new junction]\(新しいジャンクションの追加\) ダイアログ](media/fxt-mount/fxt-junction-example.png)

IP アドレスは、vserver に対して定義されたクライアント側の IP アドレスの 1 つです。 クライアント側の IP アドレスの範囲は、クラスターの Control Panel の 2 つの場所で確認できます。

* **[VServers]** テーブル ([Dashboard]\(ダッシュボード\) タブ) - 

  ![Control Panel の [Dashboard]\(ダッシュボード\) タブで、グラフの下のデータ テーブルの [VServer] タブが選択され、IP アドレス セクションが囲まれている](media/fxt-mount/fxt-ip-addresses-dashboard.png)

* **[Client Facing Network]\(クライアント側ネットワーク\)** 設定ページ - 

  ![[Settings]\(設定\) > [VServer] > [Client Facing Network]\(クライアント側ネットワーク\) 構成ページで、特定の vserver のテーブルの [Address Range]\(アドレス範囲\) セクションが囲まれている](media/fxt-mount/fxt-ip-addresses-settings.png)

IP アドレスと名前空間パスを結合して、mount コマンドのクラスター パスを作成します。 

クライアントの mount コマンドの例: ``mount 10.0.0.12:/sd-access /mnt/fxt {options}``

### <a name="create-the-local-path"></a>ローカル パスを作成する

mount コマンドのローカル パスは、ユーザーが指定できます。 必要なあらゆるパス構造を、仮想名前空間の一部として設定できます。 クライアント ワークフローにとって便利な名前空間およびローカル パスを設計します。 

クライアント側の名前空間の詳細については、クラスター構成ガイドの[名前空間の概要](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)に関する項目をお読みください。

各クライアントをマウントするときは、パスに加えて、以下で説明する [mount コマンド オプション](#use-recommended-mount-command-options)を指定します。

### <a name="use-recommended-mount-command-options"></a>推奨される mount コマンド オプションを使用する

クライアントのマウントがシームレスに行われるように、mount コマンドで以下の設定と引数を渡します。 

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| 必須の設定 | |
--- | --- 
``hard`` | Azure FXT Edge Filer クラスターに対するソフト マウントは、アプリケーション エラーおよび可能性のあるデータ損失と関連付けられます。 
``proto=netid`` | このオプションは、NFS ネットワーク エラーの適切な処理をサポートします。
``mountproto=netid`` | このオプションは、マウント操作に対するネットワーク エラーの適切な処理をサポートします。
``retry=n`` | 一時的なマウントの障害を回避するため、``retry=30`` を設定します。 (フォアグラウンド マウントの場合は、別の値が推奨されます。)

| 推奨される設定  | |
--- | --- 
``nointr``            | このオプションをサポートする以前の OS カーネル (2008 年 4 月以前) をクライアントが使用している場合は、それを使用します。 オプション "intr" が既定値です。

## <a name="next-steps"></a>次の手順

クライアントをマウントすると、ワークフローをテストしてクラスターの使用を開始できます。

新しいクラウド コア ファイラーにデータを移動する必要がある場合、並列データ取り込みを使用することによってキャッシュ構造を利用します。 [vFXT クラスターへのデータの移動](https://docs.microsoft.com/azure/avere-vfxt/avere-vfxt-data-ingest)に関する記事でいくつかの方法が説明されています。 (Avere vFXT for Azure は、Azure FXT Edge Filer とよく似たキャッシュ技術を使用するクラウド ベースの製品です)。

ハードウェアの問題をトラブルシューティングする必要がある場合は、「[Azure FXT Edge Filer のハードウェア状態を監視する](fxt-monitor.md)」をお読みください。 
