---
title: Azure HPC Cache のデータ取り込み - 手動コピー
description: cp コマンドを使用して Azure HPC Cache の Blob Storage ターゲットにデータを移動する方法
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: f96a0fa264124f9d050e667b003d98579da63b77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87092339"
---
# <a name="azure-hpc-cache-data-ingest---manual-copy-method"></a>Azure HPC Cache のデータ取り込み - 手動でコピーする方法

この記事では、Azure HPC Cache で使用する Blob Storage コンテナーに対し、データを手動でコピーする手順を詳しく説明します。 コピー速度を最適化するために、マルチスレッドの並列処理を使用します。

Azure HPC Cache の Blob Storage にデータを移動する方法について詳しくは、「[Azure Blob Storage にデータを移動する](hpc-cache-ingest.md)」を参照してください。

## <a name="simple-copy-example"></a>単純なコピーの例

クライアント上で、マルチスレッドのコピーを手動で作成できます。そのためには、定義済みのファイル セットまたはパスのセットに対して、一度に複数のコピー コマンドをバックグラウンドで実行します。

Linux または UNIX の ``cp`` コマンドには、所有権と mtime メタデータを保持するための引数 ``-p`` があります。 この引数の以下のコマンドへの追加はオプションです。 (引数を追加すると、メタデータを変更するためにクライアントからコピー先ファイルシステムに送信されるファイルシステム呼び出し数が増えます。)

このシンプルな例では、2 つのファイルを並列コピーします。

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

このコマンドの発行後、`jobs` コマンドによって、2 つのスレッドが実行されていることが表示されます。

## <a name="copy-data-with-predictable-file-names"></a>予測可能なファイル名のデータをコピーする

ファイル名が予測可能な場合は、式を使用して並列コピー スレッドを作成できます。

たとえば、ディレクトリに 1,000 個のファイルが含まれていて、それらのファイルに `0001` から `1000` まで順番に番号が付いている場合は、次の式を使用して、それぞれが 100 個のファイルをコピーする並列スレッドを 10 個作成できます。

```bash
cp /mnt/source/file0* /mnt/destination1/ & \
cp /mnt/source/file1* /mnt/destination1/ & \
cp /mnt/source/file2* /mnt/destination1/ & \
cp /mnt/source/file3* /mnt/destination1/ & \
cp /mnt/source/file4* /mnt/destination1/ & \
cp /mnt/source/file5* /mnt/destination1/ & \
cp /mnt/source/file6* /mnt/destination1/ & \
cp /mnt/source/file7* /mnt/destination1/ & \
cp /mnt/source/file8* /mnt/destination1/ & \
cp /mnt/source/file9* /mnt/destination1/
```

## <a name="copy-data-with-unstructured-file-names"></a>体系化されていないファイル名のデータをコピーする

ファイルの命名構造が予測可能でない場合は、ファイルをディレクトリ名別にグループ化できます。

この例では、各ディレクトリ全体を収集し、バックグラウンド タスクとして実行される ``cp`` コマンドに送ります。

```bash
/root
|-/dir1
| |-/dir1a
| |-/dir1b
| |-/dir1c
   |-/dir1c1
|-/dir1d
```

ファイルを収集した後、並列コピー コマンドを実行して、サブディレクトリと各サブディレクトリの内容をすべて再帰的にコピーします。

```bash
cp /mnt/source/* /mnt/destination/
mkdir -p /mnt/destination/dir1 && cp /mnt/source/dir1/* mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ &
cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ & # this command copies dir1c1 via recursion
cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
```

## <a name="when-to-add-mount-points"></a>マウント ポイントを追加するタイミング

1 つのコピー先ファイル システム マウント ポイントに対して十分な数の並列スレッドを作成した後、ある時点から、それ以上スレッドを追加してもスループットが上がらなくなります。 (スループットは、データの種類に応じて、1 秒あたりのファイル数または 1 秒あたりのバイト数の単位で測定されます。) または、さらに悪いことに、スレッド数が過剰になると、スループットが低下する場合もあります。

この場合、同じリモート ファイルシステム マウント パスを使用して、クライアント側のマウント ポイントを他の Azure HPC Cache マウント アドレスに追加できます。

```bash
10.1.0.100:/nfs on /mnt/sourcetype nfs (rw,vers=3,proto=tcp,addr=10.1.0.100)
10.1.1.101:/nfs on /mnt/destination1type nfs (rw,vers=3,proto=tcp,addr=10.1.1.101)
10.1.1.102:/nfs on /mnt/destination2type nfs (rw,vers=3,proto=tcp,addr=10.1.1.102)
10.1.1.103:/nfs on /mnt/destination3type nfs (rw,vers=3,proto=tcp,addr=10.1.1.103)
```

クライアント側のマウント ポイントを追加することで、追加のコピー コマンドを追加の `/mnt/destination[1-3]` マウント ポイントにフォークすることができるため、さらに高い並列度を実現できます。

たとえば、ファイルが非常に大きい場合は、個別のコピー先パスを使用するようにコピー コマンドを定義して、コピーを実行するクライアントから、より多くのコマンドを同時に送信します。

```bash
cp /mnt/source/file0* /mnt/destination1/ & \
cp /mnt/source/file1* /mnt/destination2/ & \
cp /mnt/source/file2* /mnt/destination3/ & \
cp /mnt/source/file3* /mnt/destination1/ & \
cp /mnt/source/file4* /mnt/destination2/ & \
cp /mnt/source/file5* /mnt/destination3/ & \
cp /mnt/source/file6* /mnt/destination1/ & \
cp /mnt/source/file7* /mnt/destination2/ & \
cp /mnt/source/file8* /mnt/destination3/ & \
```

上記の例では、3 つのコピー先マウント ポイントすべてが、クライアントのファイル コピー プロセスのターゲットになっています。

## <a name="when-to-add-clients"></a>クライアントを追加するタイミング

最後に、クライアントの機能の上限に達した場合、コピー スレッドまたはマウント ポイントをそれ以上追加しても、1 秒あたりのファイル数または 1 秒あたりのバイト数がさらに増加することはありません。 そのような状況では、同じマウント ポイント セットを持つ別のクライアントをデプロイして、そのクライアント独自のファイル コピー プロセスのセットを実行できます。

例:

```bash
Client1: cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ &
Client1: cp -R /mnt/source/dir2/dir2a /mnt/destination/dir2/ &
Client1: cp -R /mnt/source/dir3/dir3a /mnt/destination/dir3/ &

Client2: cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ &
Client2: cp -R /mnt/source/dir2/dir2b /mnt/destination/dir2/ &
Client2: cp -R /mnt/source/dir3/dir3b /mnt/destination/dir3/ &

Client3: cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ &
Client3: cp -R /mnt/source/dir2/dir2c /mnt/destination/dir2/ &
Client3: cp -R /mnt/source/dir3/dir3c /mnt/destination/dir3/ &

Client4: cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
Client4: cp -R /mnt/source/dir2/dir2d /mnt/destination/dir2/ &
Client4: cp -R /mnt/source/dir3/dir3d /mnt/destination/dir3/ &
```

## <a name="create-file-manifests"></a>ファイル マニフェストの作成

上記のアプローチ (コピー先あたり複数のコピー スレッド、クライアントあたり複数のコピー先、ネットワーク アクセス可能なソース ファイル システムあたり複数のクライアント) を理解したら、この推奨事項を検討してください。つまり、ファイル マニフェストを作成し、コピー コマンドを使用して作成したマニフェストを複数のクライアント全体で使用します。

このシナリオでは UNIX の ``find`` コマンドを使用して、ファイルまたはディレクトリのマニフェストを作成します。

```bash
user@build:/mnt/source > find . -mindepth 4 -maxdepth 4 -type d
./atj5b55c53be6-01/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-01/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-01/support/trace/rolling
./atj5b55c53be6-03/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-03/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-03/support/trace/rolling
./atj5b55c53be6-02/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-02/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-02/support/trace/rolling
```

この結果を次のようにしてファイルにリダイレクトします: `find . -mindepth 4 -maxdepth 4 -type d > /tmp/foo`

その後は、マニフェストを通して反復処理ができます。BASH コマンドを使用してファイルを数え、サブディレクトリのサイズを決定します。

```bash
ben@xlcycl1:/sps/internal/atj5b5ab44b7f > for i in $(cat /tmp/foo); do echo " `find ${i} |wc -l` `du -sh ${i}`"; done
244    3.5M    ./atj5b5ab44b7f-02/support/gsi/2018-07-18T00:07:03EDT
9      172K    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.8M    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T01:01:00UTC
131    13M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    6.2M    ./atj5b5ab44b7f-02/support/gsi/2018-07-20T21:59:41UTC
134    12M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T22:22:55UTC_hpccache_catchup
7      16K     ./atj5b5ab44b7f-02/support/pcap/2018-07-18T17:12:19UTC
8      83K     ./atj5b5ab44b7f-02/support/pcap/2018-07-18T17:17:17UTC
575    7.7M    ./atj5b5ab44b7f-02/support/cores/armada_main.2000.1531980253.gsi
33     4.4G    ./atj5b5ab44b7f-02/support/trace/rolling
281    6.6M    ./atj5b5ab44b7f-01/support/gsi/2018-07-18T00:07:03EDT
15     182K    ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-18T05:01:00UTC
244    17M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-19T01:01:01UTC
299    31M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T01:01:00UTC
256    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T21:59:41UTC_partial
889    7.7M    ./atj5b5ab44b7f-01/support/gsi/2018-07-20T21:59:41UTC
262    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T22:22:55UTC_hpccache_catchup
11     248K    ./atj5b5ab44b7f-01/support/pcap/2018-07-18T17:12:19UTC
11     88K     ./atj5b5ab44b7f-01/support/pcap/2018-07-18T17:17:17UTC
645    11M     ./atj5b5ab44b7f-01/support/cores/armada_main.2019.1531980253.gsi
33     4.0G    ./atj5b5ab44b7f-01/support/trace/rolling
244    2.1M    ./atj5b5ab44b7f-03/support/gsi/2018-07-18T00:07:03EDT
9      158K    ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.3M    ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T01:01:00UTC
131    12M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    8.4M    ./atj5b5ab44b7f-03/support/gsi/2018-07-20T21:59:41UTC
134    14M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T22:25:58UTC_hpccache_catchup
7      159K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:12:19UTC
7      157K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:17:17UTC
576    12M     ./atj5b5ab44b7f-03/support/cores/armada_main.2013.1531980253.gsi
33     2.8G    ./atj5b5ab44b7f-03/support/trace/rolling
```

最後に、クライアントへの実際のファイル コピー コマンドを作成する必要があります。

4 つのクライアントがある場合は、このコマンドを使用します。

```bash
for i in 1 2 3 4 ; do sed -n ${i}~4p /tmp/foo > /tmp/client${i}; done
```

5 つのクライアントがある場合は、次のようにします。

```bash
for i in 1 2 3 4 5; do sed -n ${i}~5p /tmp/foo > /tmp/client${i}; done
```

6 つの場合は...必要に応じて推定してください。

```bash
for i in 1 2 3 4 5 6; do sed -n ${i}~6p /tmp/foo > /tmp/client${i}; done
```

結果として *N* 個のファイルを取得できます (`find` コマンドの出力の一部として取得したレベル 4 のディレクトリへのパス名を持つ、*N* 個のクライアントごとに 1 つずつ)。

各ファイルを使用してコピー コマンドを作成します。

```bash
for i in 1 2 3 4 5 6; do for j in $(cat /tmp/client${i}); do echo "cp -p -R /mnt/source/${j} /mnt/destination/${j}" >> /tmp/client${i}_copy_commands ; done; done
```

上記のようにして *N* 個のファイルを取得できます。各ファイルには 1 行あたり 1 個のコピー コマンドが含まれ、それらをクライアント上で BASH スクリプトとして実行できます。

目標は、これらのスクリプトのスレッドを複数のクライアント上で、クライアントごとに並行して複数同時に実行することです。
