---
title: Azure HPC Cache をマウントする
description: Azure HPC Cache サービスにクライアントを接続する方法
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: v-erkell
ms.openlocfilehash: 46f221fd7c340b7f321d317f0e7493448d83177c
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71036939"
---
# <a name="mount-the-azure-hpc-cache"></a>Azure HPC Cache をマウントする

作成されたキャッシュには、NFS クライアントから単純なマウント コマンドでアクセスできます。

キャッシュの概要ページに表示される一連のマウント アドレスと仮想名前空間パス (ストレージ ターゲットを作成するときに設定したもの) を使用します。 

![Azure HPC Cache インスタンスの概要ページのスクリーンショット (右下に一連のマウント アドレスが枠囲みで強調表示されている)](media/hpc-cache-mount-addresses.png)

> [!NOTE] 
> キャッシュのマウント アドレスは、キャッシュのサブネット内のネットワーク インターフェイスに対応します。 これらの NIC は、末尾に `-cluster-nic-` と数字が付いた名前でリソース グループに表示されます。 これらのインターフェイスは変更したり削除したりしないでください。キャッシュが利用できなくなります。

仮想名前空間パスは、 **[ストレージ ターゲット]** ページに表示されます。 ターゲット名をクリックすると、それに関連付けられている集約された名前空間パスなど、詳細が表示されます。 
![キャッシュの [ストレージ ターゲット] パネルのスクリーンショット (テーブルの [パス] 列のエントリが枠囲みで強調表示されている)](media/hpc-cache-view-namespace-paths.png)

## <a name="mount-command-syntax"></a>マウント コマンドの構文

マウント コマンドは、次のように使用します。

> sudo mount *cache_mount_address*:/*namespace_path* *local_path* {*options*}

例: 

```
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -orw,tcp,mountproto=tcp,vers3,hard,intr
root@test-client:/tmp# 
```

このコマンドが成功すると、クライアントの ``hpccache`` ディレクトリにストレージ エクスポートの内容が表示されます。 

> [!NOTE] 
> キャッシュが置かれている仮想ネットワークとサブネットに、お使いのクライアントからアクセスできることが必要です。 たとえば、同じ仮想ネットワーク内にクライアントの VM を作成するか、または仮想ネットワーク内でエンドポイントやゲートウェイなどのソリューションを使用して外部からアクセスできるようにします。 それ以外は一切、キャッシュのサブネット内でホストすることはできないことに注意してください。

### <a name="mount-command-options"></a>マウント コマンドのオプション

クライアントのマウントが確実に行われるように、mount コマンドで以下の設定と引数を渡します。 

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| 推奨される mount コマンドの設定 | |
--- | --- 
``hard`` | vFXT クラスターに対するソフト マウントは、アプリケーション エラーおよび可能性のあるデータ損失と関連付けられます。 
``proto=netid`` | このオプションは、NFS ネットワーク エラーの適切な処理をサポートします。
``mountproto=netid`` | このオプションは、マウント操作に対するネットワーク エラーの適切な処理をサポートします。
``retry=n`` | 一時的なマウントの障害を回避するため、``retry=30`` を設定します。 (フォアグラウンド マウントの場合は、別の値が推奨されます。)

## <a name="next-steps"></a>次の手順

* キャッシュのストレージ ターゲットを移動する方法については、[新しい Azure Blob Storage へのデータの事前設定](hpc-cache-ingest.md)に関するページを参照してください。
