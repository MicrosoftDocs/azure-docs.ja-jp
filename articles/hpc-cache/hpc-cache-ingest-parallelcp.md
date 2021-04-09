---
title: Azure HPC Cache のデータ取り込み - 並列コピー スクリプト
description: 並列コピー スクリプトを使用して Azure HPC Cache の Blob Storage ターゲットにデータを移動する方法
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: ff7b15a36c5ef19a1fa6ffdca7697dd6ba97c29f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87092356"
---
# <a name="azure-hpc-cache-data-ingest---parallel-copy-script-method"></a>Azure HPC Cache のデータ取り込み - 並列コピー スクリプトを使用した方法

この記事では、``parallelcp`` スクリプトを作成して、Azure HPC Cache で使用する Blob Storage コンテナーに対し、スクリプトでデータを移動する手順を紹介します。

Azure HPC Cache の Blob Storage にデータを移動する方法について詳しくは、「[Azure Blob Storage にデータを移動する](hpc-cache-ingest.md)」を参照してください。

## <a name="create-the-parallelcp-script"></a>parallelcp スクリプトを作成する

次のスクリプトでは、実行可能な `parallelcp` を追加します。 (このスクリプトは Ubuntu 用に作成されています。別のディストリビューションを使用する場合は、``parallel`` を別途インストールする必要があります。)

```bash
sudo touch /usr/bin/parallelcp && sudo chmod 755 /usr/bin/parallelcp && sudo sh -c "/bin/cat >/usr/bin/parallelcp" <<EOM
#!/bin/bash

display_usage() {
    echo -e "\nUsage: \$0 SOURCE_DIR DEST_DIR\n"
}

if [  \$# -le 1 ] ; then
    display_usage
    exit 1
fi

if [[ ( \$# == "--help") ||  \$# == "-h" ]] ; then
    display_usage
    exit 0
fi

SOURCE_DIR="\$1"
DEST_DIR="\$2"

if [ ! -d "\$SOURCE_DIR" ] ; then
    echo "Source directory \$SOURCE_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -d "\$DEST_DIR" ] && ! mkdir -p \$DEST_DIR ; then
    echo "Destination directory \$DEST_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -w "\$DEST_DIR" ] ; then
    echo "Destination directory \$DEST_DIR is not writeable, or is not a directory"
    display_usage
    exit 3
fi

if ! which parallel > /dev/null ; then
    sudo apt-get update && sudo apt install -y parallel
fi

DIRJOBS=225
JOBS=225
find \$SOURCE_DIR -mindepth 1 -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$DIRJOBS -0 "mkdir -p \$DEST_DIR/{}"
find \$SOURCE_DIR -mindepth 1 ! -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$JOBS -0 "cp -P \$SOURCE_DIR/{} \$DEST_DIR/{}"
EOM
```

## <a name="parallel-copy-example"></a>並列コピーの例

この例では、並列コピー スクリプトを使用して ``glibc`` をコンパイルします。Azure HPC Cache にあるソース ファイルを使用しています。

ソース ファイルは Azure HPC Cache のマウント ポイントにキャッシュされ、オブジェクト ファイルはローカル ハード ドライブに格納されています。

この例では、並列コピー スクリプトにオプション ``-j`` と ``make`` を指定して並列化を実現しています。

```bash
sudo apt-get update
sudo apt install -y gcc bison gcc binutils make parallel
cd
wget https://mirrors.kernel.org/gnu/libc/glibc-2.27.tar.bz2
tar jxf glibc-2.27.tar.bz2
ln -s /nfs/cache1 hpccache
time parallelcp glibc-2.27 avere/glibc-2.27
cd
mkdir obj
mkdir usr
cd obj
/home/azureuser/avere/glibc-2.27/configure --prefix=/home/azureuser/usr
time make -j
```
