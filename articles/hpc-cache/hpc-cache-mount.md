---
title: Azure HPC Cache をマウントする
description: Azure HPC Cache サービスにクライアントを接続する方法
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 7f1d8d34d6351fc344fdb101ac8e9a96678df9d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91651430"
---
# <a name="mount-the-azure-hpc-cache"></a>Azure HPC Cache をマウントする

作成されたキャッシュには、NFS クライアントから単純な `mount` コマンドでアクセスできます。 このコマンドは、Azure HPC Cache 上の特定のストレージ ターゲット パスを、クライアント マシンのローカル ディレクトリに接続します。

mount コマンドは、次の要素で構成されています。

* キャッシュのマウント アドレスの 1 つ (キャッシュの概要ページに一覧表示されます)
* ストレージ ターゲットに対して設定する仮想名前空間のパス (キャッシュの名前空間ページに一覧表示)
* クライアントで使用するローカル パス
* この種類の NFS マウントの成功を最適化するコマンド パラメーター

キャッシュの **[Mount instructions]\(マウント手順\)** ページでは、情報と推奨されるオプションが収集され、コピーできるプロトタイプの mount コマンドが作成されます。 詳細については、「[マウント手順ユーティリティを使用する](#use-the-mount-instructions-utility)」をお読みください。

## <a name="prepare-clients"></a>クライアントを準備する

このセクションのガイドラインに従って、クライアントが Azure HPC Cache をマウントできることを確認します。

### <a name="provide-network-access"></a>ネットワーク アクセスを提供する

クライアント マシンには、キャッシュの仮想ネットワークとプライベート サブネットへのネットワーク アクセスが必要です。

たとえば、同じ仮想ネットワーク内にクライアントの VM を作成するか、または仮想ネットワーク内でエンドポイントやゲートウェイなどのソリューションを使用して外部からアクセスできるようにします。 (キャッシュ自体以外は、キャッシュのサブネット内でホストする必要があることに注意してください)。

### <a name="install-utilities"></a>ユーティリティをインストールする

NFS の mount コマンドをサポートするために適切な Linux ユーティリティ ソフトウェアをインストールします。

* Red Hat Enterprise Linux または SuSE の場合: `sudo yum install -y nfs-utils`
* Ubuntu または Debian の場合: `sudo apt-get install nfs-common`

### <a name="create-a-local-path"></a>ローカル パスを作成する

各クライアントに、キャッシュに接続するためのローカル ディレクトリ パスを作成します。 マウントする名前空間のパスごとにパスを作成します。

例: `sudo mkdir -p /mnt/hpc-cache-1/target3`

Azure portal の [[マウントに関する指示]](#use-the-mount-instructions-utility) ページに、コピーできるプロトタイプ コマンドがあります。

クライアント マシンをキャッシュに接続するときに、このパスを、ストレージ ターゲット エクスポートを表す仮想名前空間のパスに関連付けます。 クライアントが使用する仮想名前空間のパスごとに、ディレクトリを作成します。

## <a name="use-the-mount-instructions-utility"></a>マウント手順ユーティリティを使用する

Azure portal の **[マウントに関する指示]** ページを使用して、コピー可能な mount コマンドを作成できます。 このページは、ポータルのキャッシュ ビューの **[構成]** セクションから開きます。

クライアントでコマンドを使用する前に、クライアントが前提条件を満たしており、前述の「[クライアントを準備する](#prepare-clients)」で説明したように、NFS `mount` コマンドを使用するために必要なソフトウェアがあることを確認してください。

![[構成] > [Mount instructions]\(マウント手順\) ページが読み込まれている、ポータルの Azure HPC Cache インスタンスのスクリーンショット](media/mount-instructions.png)

mount コマンドを作成するには、次の手順に従います。

1. **[クライアント パス]** フィールドをカスタマイズします。 このフィールドは、クライアントでローカル パスを作成するために使用できるコマンドの例を示します。 クライアントは、このディレクトリで Azure HPC Cache からローカルにコンテンツにアクセスします。

   フィールドをクリックし、コマンドを編集して目的のディレクトリ名を含めます。 その名前は、`sudo mkdir -p` の後の文字列の末尾に配置します

   ![カーソルが末尾に置かれた [クライアント パス] フィールドのスクリーンショット](media/mount-edit-client.png)

   フィールドの編集が完了すると、ページの下部にある mount コマンドが新しいクライアント パスで更新されます。

1. 一覧から **キャッシュのマウント アドレス** を選択します。 このメニューには、キャッシュの[クライアント マウント ポイント](#find-mount-command-components)がすべて一覧表示されます。

   キャッシュのパフォーマンスを向上させるために、使用可能なすべてのマウント アドレスでクライアントの負荷を分散させます。

   ![[キャッシュのマウント アドレス] フィールドのスクリーンショット (選択できる 3 つの IP アドレスがセレクターに表示されている)](media/mount-select-ip.png)

1. クライアントに使用する **仮想名前空間のパス** を選択します。 これらのパスは、バックエンド ストレージ システム上のエクスポートにリンクします。

   ![セレクターが開かれている [仮想名前空間のパス] フィールドを示すスクリーンショット。](media/mount-select-target.png)

   仮想名前空間のパスは、 **[名前空間]** ポータル ページで表示および変更できます。 方法については、「[集約された名前空間を設定する](add-namespace-paths.md)」を参照してください。

   Azure HPC Cache の集約された名前空間の機能の詳細については、「[集約された名前空間を計画する](hpc-cache-namespace.md)」を参照してください。

1. 手順 3 の **[Mount command]\(mount コマンド\)** フィールドには、前のフィールドで設定したマウント アドレス、仮想名前空間のパス、クライアント パスを使用する、カスタマイズされた mount コマンドが自動的に入力されます。

   フィールドの右側にあるコピー記号をクリックすると、自動的にクリップボードにコピーされます。

   ![[クリップボードにコピー] ボタンのホバー テキストが表示されているプロトタイプ マウント コマンド フィールドのスクリーンショット](media/mount-command-copy.png)

1. コピーした mount コマンドをクライアント マシンで使用して、それを Azure HPC Cache に接続します。 コマンドをクライアントのコマンド ラインから直接発行することも、クライアントのセットアップ スクリプトまたはテンプレートに mount コマンドを含めることもできます。

## <a name="understand-mount-command-syntax"></a>mount コマンドの構文について

mount コマンドの形式は次のとおりです。

> sudo mount {*options*} *cache_mount_address*:/*namespace_path* *local_path*

例:

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount -o hard,proto=tcp,mountproto=tcp,retry=30 10.0.0.28:/blob-demo-0722 hpccache
root@test-client:/tmp#
```

このコマンドが成功すると、ストレージ エクスポートの内容がクライアントの ``hpccache`` ディレクトリに表示されます。

### <a name="mount-command-options"></a>マウント コマンドのオプション

クライアントのマウントが確実に行われるように、mount コマンドで以下の設定と引数を渡します。

> mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}

| 推奨される mount コマンドの設定 | 説明 |
--- | ---
``hard`` | Azure HPC Cache に対するソフト マウントは、アプリケーション エラーおよび可能性のあるデータ損失と関連付けられます。
``proto=tcp`` | このオプションは、NFS ネットワーク エラーの適切な処理をサポートします。
``mountproto=tcp`` | このオプションは、マウント操作に対するネットワーク エラーの適切な処理をサポートします。
``retry=<value>`` | 一時的なマウントの障害を回避するため、``retry=30`` を設定します。 (フォアグラウンド マウントの場合は、別の値が推奨されます。)

### <a name="find-mount-command-components"></a>mount コマンドのコンポーネントを見つける

**[マウントに関する指示]** ページを使用せずに mount コマンドを作成する場合は、キャッシュの **[概要]** ページでマウント アドレスを、 **[名前空間]** ページで仮想名前空間のパスを確認できます。

![Azure HPC Cache インスタンスの概要ページのスクリーンショット (右下に一連のマウント アドレスが枠囲みで強調表示されている)](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> キャッシュのマウント アドレスは、キャッシュのサブネット内のネットワーク インターフェイスに対応します。 リソース グループでは、これらの NIC が、末尾に `-cluster-nic-` と数字が付いた名前で表示されます。 これらのインターフェイスは変更したり削除したりしないでください。キャッシュが利用できなくなります。

仮想名前空間のパスは、キャッシュの **[名前空間]** 設定ページに表示されます。

![テーブルの以下の最初の列に強調のボックスが表示されている、ポータルの [設定] > [名前空間] ページのスクリーンショット: "名前空間のパス"](media/view-namespace-paths.png)

## <a name="next-steps"></a>次のステップ

* キャッシュのストレージ ターゲットを移動する方法については、[新しい Azure Blob Storage へのデータの事前設定](hpc-cache-ingest.md)に関するページを参照してください。
