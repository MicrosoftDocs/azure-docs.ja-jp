---
title: Azure HPC Cache (プレビュー) をマウントする
description: Azure HPC Cache サービスにクライアントを接続する方法
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: rohogue
ms.openlocfilehash: 3257cf92c628650fc50e0a36ec37fcab920aba2f
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254576"
---
# <a name="mount-the-azure-hpc-cache-preview"></a>Azure HPC Cache (プレビュー) をマウントする

作成されたキャッシュには、NFS クライアントから単純なマウント コマンドでアクセスできます。

マウント コマンドは、次の 2 つの要素で構成されます。

* キャッシュのマウント アドレスの 1 つ (キャッシュの概要ページに一覧表示されます)
* ストレージ ターゲットの作成時に設定した仮想名前空間のパス

![Azure HPC Cache インスタンスの概要ページのスクリーンショット (右下に一連のマウント アドレスが枠囲みで強調表示されている)](media/hpc-cache-mount-addresses.png)

> [!NOTE] 
> キャッシュのマウント アドレスは、キャッシュのサブネット内のネットワーク インターフェイスに対応します。 リソース グループでは、これらの NIC が、末尾に `-cluster-nic-` と数字が付いた名前で表示されます。 これらのインターフェイスは変更したり削除したりしないでください。キャッシュが利用できなくなります。

仮想名前空間パスは、 **[ストレージ ターゲット]** ページに表示されます。 個々のストレージ ターゲット名をクリックすると、それに関連付けられている集約された名前空間パスなどの詳細が表示されます。

![キャッシュのストレージ ターゲット パネルのスクリーンショット (テーブルの [パス] 列のエントリが枠囲みで強調表示されている)](media/hpc-cache-view-namespace-paths.png)

## <a name="mount-command-syntax"></a>マウント コマンドの構文

マウント コマンドは、次のように使用します。

> sudo mount *cache_mount_address*:/*namespace_path* *local_path* {*options*}

例:

```
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -orw,tcp,mountproto=tcp,vers3,hard
root@test-client:/tmp# 
```

このコマンドが成功すると、クライアントの ``hpccache`` ディレクトリにストレージ エクスポートの内容が表示されます。

> [!NOTE] 
> キャッシュが置かれている仮想ネットワークとサブネットに、お使いのクライアントからアクセスできることが必要です。 たとえば、同じ仮想ネットワーク内にクライアントの VM を作成するか、または仮想ネットワーク内でエンドポイントやゲートウェイなどのソリューションを使用して外部からアクセスできるようにします。 それ以外は一切、キャッシュのサブネット内でホストすることはできないことに注意してください。

### <a name="mount-command-options"></a>マウント コマンドのオプション

クライアントのマウントが確実に行われるように、mount コマンドで以下の設定と引数を渡します。 

``mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| 推奨される mount コマンドの設定 | |
--- | --- 
``hard`` | Azure HPC Cache に対するソフト マウントは、アプリケーション エラーおよび可能性のあるデータ損失と関連付けられます。 
``proto=netid`` | このオプションは、NFS ネットワーク エラーの適切な処理をサポートします。
``mountproto=netid`` | このオプションは、マウント操作に対するネットワーク エラーの適切な処理をサポートします。
``retry=n`` | 一時的なマウントの障害を回避するため、``retry=30`` を設定します。 (フォアグラウンド マウントの場合は、別の値が推奨されます。)

## <a name="next-steps"></a>次の手順

* キャッシュのストレージ ターゲットを移動する方法については、[新しい Azure Blob Storage へのデータの事前設定](hpc-cache-ingest.md)に関するページを参照してください。
