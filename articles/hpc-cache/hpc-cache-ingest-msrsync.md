---
title: Azure HPC Cache のデータ取り込み - msrsync
description: msrsync を使用して Azure HPC Cache の Blob Storage ターゲットにデータを移動する方法
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: 323ecd6a2dd001c3c8df1b2ec15f0ae8402ec70c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87092407"
---
# <a name="azure-hpc-cache-data-ingest---msrsync-method"></a>Azure HPC Cache のデータ取り込み - msrsync を使用した方法

この記事では、Azure HPC Cache で使用する Azure Blob Storage コンテナーに対し、``msrsync`` ユーティリティを使用してデータをコピーする手順を詳しく説明します。

Azure HPC Cache の Blob Storage にデータを移動する方法について詳しくは、「[Azure Blob Storage にデータを移動する](hpc-cache-ingest.md)」を参照してください。

``msrsync`` ツールを使用すると、Azure HPC Cache のバックエンド ストレージ ターゲットにデータを移動することができます。 このツールは、複数の ``rsync`` プロセスを並列実行して帯域幅の使用を最適化することを目的に設計されています。 GitHub (https://github.com/jbd/msrsync) で入手できます。

``msrsync`` では、ソース ディレクトリを別々の "バケット" に分割したうえで、個々の ``rsync`` プロセスを各バケットに対して実行します。

4 コアの VM を使用した予備テストでは、64 個のプロセスを使用したときに最適な効率が示されました。 ``msrsync`` の ``-p`` オプションを使用して、プロセスの数を 64 に設定します。

``msrsync`` では、ローカル ボリュームへの書き込み、およびローカル ボリュームからの書き出しのみが可能であることに注意してください。 コピー元とコピー先は、コマンドの発行に使用するワークステーション上のローカル マウントとしてアクセスできることが必要です。

``msrsync`` を使用して Azure HPC Cache の Azure Blob Storage にデータを事前設定するには、次の手順に従います。

1. ``msrsync`` とその前提条件 (``rsync`` および Python 2.6 以降) をインストールします。
1. コピーするファイルおよびディレクトリの合計数を確認します。

   たとえば、「```prime.py --directory /path/to/some/directory```」のように引数を指定して、``prime.py`` ユーティリティを使用します (<https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py> からのダウンロードによって入手可能)。

   ``prime.py`` を使用しない場合は、次のように GNU の ``find`` ツールを使用して項目数を計算できます。

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. 項目の数を 64 で除算してプロセスごとの項目数を決定します。 この数字を ``-f`` オプションに指定して、コマンドを実行するときのバケットのサイズを設定します。

1. ``msrsync`` コマンドを発行してファイルをコピーします。

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   たとえば、このコマンドは /test/source-repository から /mnt/hpccache/repository に 64 プロセスで 11,000 ファイルを移動するように指定されています。

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/hpccache/repository``
