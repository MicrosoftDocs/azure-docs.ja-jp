---
title: Avere vFXT をマウントする - Azure
description: Avere vFXT for Azure で vFXT クラスターにクライアントを接続する方法と、クラスター ノード間でクライアント トラフィックを負荷分散する方法について説明します。
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/16/2019
ms.author: rohogue
ms.openlocfilehash: 44a4e1293bc4c5a54e1e345d5cf95ba307a7b120
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88272573"
---
# <a name="mount-the-avere-vfxt-cluster"></a>Avere vFXT クラスターをマウントする

クライアント マシンを vFXT クラスターに接続するには以下の手順のようにします。

1. クラスター ノード間にクライアント トラフィックの負荷を分散させる方法を決定します。 詳細については、後の「[クライアントの負荷を分散させる](#balance-client-load)」をご覧ください。
1. マウントする IP アドレスとジャンクション パスを識別します。
1. 適切な引数を指定して [mount コマンド](#mount-command-arguments)を発行します。

## <a name="balance-client-load"></a>クライアントの負荷を分散させる

クライアントの要求をクラスター内のすべてのノード間に分散させるには、クライアント側の IP アドレスの全範囲にクライアントをマウントする必要があります。 このタスクを自動化するには、いくつかの簡単な方法があります。

> [!TIP]
> 他の負荷分散方法は、さらに大規模なシステムまたは複雑なシステムに適しています (ヘルプが必要な場合は、[サポート チケットを開いて](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt)ください)。
>
> サーバー側の自動負荷分散に DNS サーバーを使用したい場合は、Azure 内で独自の DNS サーバーを設定して管理する必要があります。 その場合は、vFXT クラスター用にラウンドロビン DNS を構成することができます。手順については、「[Avere クラスターの DNS 構成](avere-vfxt-configure-dns.md)」を参照してください。

### <a name="sample-balanced-client-mounting-script"></a>負荷分散されたクライアントのマウント スクリプトの例

このコード例では、vFXT クラスターで使用可能なすべての IP アドレスにクライアントを分散させるためのランダム化要素として、クライアントの IP アドレスを使用しています。

```bash
function mount_round_robin() {
    # to ensure the nodes are spread out somewhat evenly the default
    # mount point is based on this node's IP octet4 % vFXT node count.
    declare -a AVEREVFXT_NODES="($(echo ${NFS_IP_CSV} | sed "s/,/ /g"))"
    OCTET4=$((`hostname -i | sed -e 's/^.*\.\([0-9]*\)/\1/'`))
    DEFAULT_MOUNT_INDEX=$((${OCTET4} % ${#AVEREVFXT_NODES[@]}))
    ROUND_ROBIN_IP=${AVEREVFXT_NODES[${DEFAULT_MOUNT_INDEX}]}

    DEFAULT_MOUNT_POINT="${BASE_DIR}/default"

    # no need to write again if it is already there
    if ! grep --quiet "${DEFAULT_MOUNT_POINT}" /etc/fstab; then
        echo "${ROUND_ROBIN_IP}:${NFS_PATH}    ${DEFAULT_MOUNT_POINT}    nfs hard,proto=tcp,mountproto=tcp,retry=30 0 0" >> /etc/fstab
        mkdir -p "${DEFAULT_MOUNT_POINT}"
        chown nfsnobody:nfsnobody "${DEFAULT_MOUNT_POINT}"
    fi
    if ! grep -qs "${DEFAULT_MOUNT_POINT} " /proc/mounts; then
        retrycmd_if_failure 12 20 mount "${DEFAULT_MOUNT_POINT}" || exit 1
    fi
}
```

上記の関数は、[Avere vFXT の例](https://github.com/Azure/Avere#tutorials)のサイトから入手できる Batch の例の一部です。

## <a name="create-the-mount-command"></a>mount コマンドを作成する

> [!NOTE]
> Avere vFXT クラスターを作成するときに新しい BLOB コンテナーを作成しなかった場合は、クライアントをマウントする前に、「[ストレージの構成](avere-vfxt-add-storage.md)」の説明に従ってストレージ システムを追加します。

クライアントで ``mount`` コマンドを実行すると、vFXT クラスター上の仮想サーバー (vserver) が、ローカル ファイル システムのパスにマップされます。 形式は ``mount <vFXT path> <local path> {options}`` です

mount コマンドには次の 3 つの要素があります。

* vFXT パス - P アドレスとクラスターの名前空間ジャンクション パスの組み合わせ (後述)
* ローカル パス - クライアント上のパス
* mount コマンドのオプション - 「[mount コマンドの引数](#mount-command-arguments)」を参照

### <a name="junction-and-ip"></a>ジャンクションと IP

vserver のパスは、その "*IP アドレス*" と、"*名前空間ジャンクション*" へのパスの組み合わせです。 名前空間ジャンクションは、ストレージ システムが追加されるときに定義された仮想パスです。

クラスターが BLOB ストレージで作成された場合、コンテナーの名前空間パスは `/msazure` です

例: ``mount 10.0.0.12:/msazure /mnt/vfxt``

クラスターを作成した後にストレージを追加した場合は、名前空間ジャンクション パスは、ジャンクションを作成するときに **[名前空間のパス]** で設定した値です。 たとえば、名前空間のパスとして ``/avere/files`` を使用した場合、クライアントは <*IP アドレス*>:/avere/files をローカルなマウント ポイントにマウントします。

![[Namespace path]\(名前空間のパス\) フィールドに "/avere/files" が設定された [Add new junction]\(新しいジャンクションの追加\) ダイアログ](media/avere-vfxt-create-junction-example.png) <!-- to do - change example and screenshot to vfxt/files instead of avere -->

IP アドレスは、vserver に対して定義されたクライアント側の IP アドレスの 1 つです。 クライアント側の IP アドレスの範囲は、Avere Control Panel の 2 つの場所で確認できます。

* **[VServers]** テーブル ([Dashboard]\(ダッシュボード\) タブ) -

  ![Avere Control Panel の [Dashboard]\(ダッシュボード\) タブで、グラフの下のデータ テーブルの [VServer] タブが選択され、IP アドレス セクションが囲まれている](media/avere-vfxt-ip-addresses-dashboard.png)

* **[Client Facing Network]\(クライアント側ネットワーク\)** 設定ページ -

  ![[Settings]\(設定\) > [VServer] > [Client Facing Network]\(クライアント側ネットワーク\) 構成ページで、特定の vserver のテーブルの [Address Range]\(アドレス範囲\) セクションが囲まれている](media/avere-vfxt-ip-addresses-settings.png)

各クライアントをマウントするときは、パスに加えて、以下で説明する [mount コマンド引数](#mount-command-arguments)を指定します。

### <a name="mount-command-arguments"></a>mount コマンドの引数

クライアントのマウントがシームレスに行われるように、mount コマンドで以下の設定と引数を渡します。

``mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| 必須の設定 | 説明 |
--- | ---
``hard`` | vFXT クラスターに対するソフト マウントは、アプリケーション エラーおよび可能性のあるデータ損失と関連付けられます。
``proto=netid`` | このオプションは、NFS ネットワーク エラーの適切な処理をサポートします。
``mountproto=netid`` | このオプションは、マウント操作に対するネットワーク エラーの適切な処理をサポートします。
``retry=n`` | 一時的なマウントの障害を回避するため、``retry=30`` を設定します。 (フォアグラウンド マウントの場合は、別の値が推奨されます。)

## <a name="next-steps"></a>次のステップ

クライアントをマウントしたら、それらを使用して、クラスター上の新しい Blob Storage コンテナーにデータをコピーできます。 新しいストレージにデータを取り込む必要がない場合は、追加のセットアップ タスクについて、他のリンクを参照してください。

* [クラスターのコア ファイラーにデータを移動する](avere-vfxt-data-ingest.md) - 複数のクライアントとスレッドを使用して、データを効率的に新しいコア ファイラーにアップロードする方法
* [クラスターのチューニングをカスタマイズする](avere-vfxt-tuning.md) - ワークロードに合わせてクラスターの設定を調整します
* [クラスターを管理する](avere-vfxt-manage-cluster.md) - クラスターの開始または停止およびノードの管理の方法
