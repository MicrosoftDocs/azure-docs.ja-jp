---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/29/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 25404837d5bc66ff415be8d8670eb6650475c30f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99094630"
---
## <a name="warm-up-the-cache"></a>キャッシュのウォーム アップ

ReadOnly ホスト キャッシュを使用するディスクでは、ディスクの制限を超えた高 IOPS を実現できます。 ホスト キャッシュからこの最大の読み取りパフォーマンスを得るには、まず、このディスクのキャッシュをウォームアップする必要があります。 これにより、ベンチマーク ツールが CacheReads ボリュームで促進する読み取り IO が、直接ディスクにヒットするのではなく、実際にキャッシュにヒットするようになります。 キャッシュ ヒットにより、キャッシュが有効化された 1 つのディスクからさらに IOPS が得られます。

> [!IMPORTANT]
> VM を再起動するたびに、キャッシュをウォームアップしてからベンチマークを実行する必要があります。

## <a name="diskspd"></a>DISKSPD

VM に [DISKSP ツールをダウンロード](https://github.com/Microsoft/diskspd)します。 DISKSPD は、独自の統合ワークロードを作成するためにカスタマイズできるツールです。 ここで説明したのと同じセットアップを使用して、ベンチマークテストを実行します。 さまざまなワークロードをテストするように仕様を変更できます。

この例では、次のベースライン パラメーターのセットを使用します。

- -c200G:テストで使用されるサンプル ファイルを作成 (または再作成) します。 これは、バイト、KiB、MiB、GiB、またはブロックで設定できます。 この場合、メモリのキャッシュを最小限に抑えるために、200-GiB ターゲット ファイルの大きなファイルが使用されます。
- -w100:操作における書き込み要求の割合を指定します (-w0 は 100% 読み取りに相当します)。
- -b4K:ブロック サイズをバイト、KiB、MiB、または GiB で示します。 ここでは、ランダム I/O テストをシミュレートするために 4K ブロック サイズが使用されます。
- -F4:4 つのスレッドの合計を設定します。
- -r:ランダム I/O テストを示します (-s パラメーターはオーバーライドされます)。
- -o128:スレッドごとのターゲットあたりの未処理 I/O 要求の数を示します。 これは、キューの深さとも呼ばれます。 この場合、CPU の負荷を計算するために 128 が使用されます。
- -W7200:測定が開始されるまでのウォームアップ時間の長さを指定します。
- -d30:ウォームアップを含まない、テストの期間を指定します。
- -Sh:ソフトウェアおよびハードウェアの書き込みキャッシュを無効にします (-Suw に相当します)。

パラメーターの完全な一覧については、[GitHub リポジトリ](https://github.com/Microsoft/diskspd/wiki/Command-line-and-parameters)を参照してください。

### <a name="maximum-write-iops"></a>最大書き込み IOPS
書き込み操作の実行に、高いキューの深さ 128、小さなブロック サイズ 8 KB、4 つのワーカー スレッドを使用します。 書き込みワーカーは、キャッシュが "None" に設定された 3 個のディスクを含む "NoCacheWrites" ボリュームのトラフィックを促進します。

次のコマンドを実行して、30 秒のウォームアップと 30 秒の測定を実行します。

`diskspd -c200G -w100 -b8K -F4 -r -o128 -W30 -d30 -Sh testfile.dat`

結果として、Standard_D8ds_v4 VM が最大書き込み IOPS の上限である 12,800 を提供していることがわかります。

:::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/disks-benchmarks-diskspd-max-write-io-per-second.png" alt-text="合計バイト数 3208642560 の場合は、最大合計 I/O が 391680、合計 101.97 MiB/秒、および 1 秒あたり I/O の合計が 13052.65 です。":::

### <a name="maximum-read-iops"></a>最大読み取り IOPS

読み取り操作の実行に、高いキューの深さ 128、小さなブロック サイズ 4 KB、4 つのワーカー スレッドを使用します。 読み取りワーカーは、キャッシュが "ReadOnly" に設定された 1 つのディスクを含む "CacheReads" ボリュームのトラフィックを促進します。

次のコマンドを実行して、2 時間のウォームアップと 30 秒の測定を実行します。

`diskspd -c200G -b4K -F4 -r -o128 -W7200 -d30 -Sh testfile.dat`

結果として、Standard_D8ds_v4 VM が最大読み取り IOPS の上限である 77,000 を提供していることがわかります。

:::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/disks-benchmarks-diskspd-max-read-io-per-second.png" alt-text="合計バイト数が 9652785152 の場合、合計 I/O が 2356637、合計 MiB/秒 306.72、および 1 秒あたり I/O の合計が 78521.23 でした。":::

### <a name="maximum-throughput"></a>最大スループット

読み取りと書き込みのスループットを最大限にするには、サイズが 64 KB の大きなブロック サイズに変更できます。
## <a name="fio"></a>fio

FIO は、Linux VM でストレージのベンチマークを実行する一般的なツールです。 FIO では、さまざまな IO サイズ、順次読み取り/書き込み、ランダム読み取り/書き込みを柔軟に選択できます。 FIO は、ワーカー スレッドまたはワーカー プロセスを起動して、指定された I/O 操作を実行します。 各ワーカー スレッドがジョブ ファイルを使用して実行する必要がある I/O 操作の種類を指定できます。 以下の例に示すシナリオごとに、ジョブ ファイルを 1 つ作成しました。 これらのジョブ ファイルで仕様を変更して、Premium Storage で実行されるさまざまなワークロードのベンチマークを実行できます。 各例では、**Ubuntu** を実行する Standard_D8ds_v4 を使用しています。 ベンチマークに関するセクションの最初に記載されている設定を使用し、ベンチマーク テストを実行する前にキャッシュをウォームアップします。

開始する前に、 [FIO をダウンロード](https://github.com/axboe/fio) し、仮想マシンにインストールします。

Ubuntu に対して次のコマンドを実行します。

```
apt-get install fio
```

ディスクでの書き込み操作を促進するための 4 つのワーカー スレッドと、読み取り操作を促進するための 4 つのワーカー スレッドを使用します。 書き込みワーカーは、キャッシュが "None" に設定された 3 個のディスクを含む "nocache" ボリュームのトラフィックを促進します。 読み取りワーカーは、キャッシュが "ReadOnly" に設定された 1 つのディスクを含む "readcache" ボリュームのトラフィックを促進します。

### <a name="maximum-write-iops"></a>最大書き込み IOPS

最大書き込み IOPS を得るために、次の仕様でジョブ ファイルを作成します。 ファイル名を "fiowrite.ini" にします。

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=4k
numjobs=4

[writer1]
rw=randwrite
directory=/mnt/nocache
```

これまでのセクションで説明した設計ガイドラインに沿った次の重要事項に注意してください。 IOPS を最大限に高めるために、これらの仕様が不可欠となります。  

* 256 の大きなキューの深さ。  
* 4 KB の小さなブロック サイズ。  
* ランダム書き込みを実行する複数のスレッド。

次のコマンドを実行して、FIO テストを 30 秒間実行します。  

```
sudo fio --runtime 30 fiowrite.ini
```

テストの実行中、VM と Premium ディスクが提供している書き込み IOPS の数を確認できます。 次のサンプルに示すように、Standard_D8ds_v4 VM は書き込み IOPS の上限である 12,800 IOPS を実現しています。  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-uncached-writes-1.jpg" alt-text="書き込み IOPS VM と Premium SSD が提供する数は、書き込みが 13.1k IOPS であることを示しています。":::

### <a name="maximum-read-iops"></a>最大読み取り IOPS

最大読み取り IOPS を得るために、次の仕様でジョブ ファイルを作成します。 ファイル名を "fioread.ini" にします。

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=4k
numjobs=4

[reader1]
rw=randread
directory=/mnt/readcache
```

これまでのセクションで説明した設計ガイドラインに沿った次の重要事項に注意してください。 IOPS を最大限に高めるために、これらの仕様が不可欠となります。

* 256 の大きなキューの深さ。  
* 4 KB の小さなブロック サイズ。  
* ランダム書き込みを実行する複数のスレッド。

次のコマンドを実行して、FIO テストを 30 秒間実行します。

```
sudo fio --runtime 30 fioread.ini
```

テストの実行中、VM と Premium ディスクが提供している読み取り IOPS の数を確認できます。 次のサンプルに示すように、Standard_D8ds_v4 VM は 77,000 を超える読み取り IOPS を実現しています。 これは、ディスクとキャッシュのパフォーマンスの合計です。  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-cached-reads-1.jpg" alt-text="書き込み IOPS VM と Premium SSD が提供する数が、読み取りが 78.6k であることを示しているスクリーンショット。":::

### <a name="maximum-read-and-write-iops"></a>最大読み取り/書き込み IOPS

 読み取りと書き込みの最大合計 IOPS を得るために、次の仕様でジョブ ファイルを作成します。 ファイル名を "fioreadwrite.ini" にします。

```ini
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k
numjobs=4

[reader1]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=3200
```

これまでのセクションで説明した設計ガイドラインに沿った次の重要事項に注意してください。 IOPS を最大限に高めるために、これらの仕様が不可欠となります。

* 128 の大きなキューの深さ。  
* 4 KB の小さなブロック サイズ。  
* ランダム読み取り/書き込みを実行する複数のスレッド。

次のコマンドを実行して、FIO テストを 30 秒間実行します。

```
sudo fio --runtime 30 fioreadwrite.ini
```

テストの実行中、VM と Premium ディスクが提供している読み取りと書き込みの合計 IOPS を確認できます。 次のサンプルに示すように、Standard_D8ds_v4 VM は 90,000 を超える読み取りと書き込みの合計 IOPS を実現しています。 これは、ディスクとキャッシュのパフォーマンスの合計です。  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-both-1.jpg" alt-text="読み取りと書き込みの合計 IOPS は、読み取りが 78.3k で書き込みが 12.6k IOPS であることを示しています。":::

### <a name="maximum-combined-throughput"></a>最大スループットの合計

 読み取りと書き込みの最大合計スループットを得るには、大きなブロック サイズ、大きなキューの深さ、読み取り/書き込みを実行する複数のスレッドを使用します。 ブロック サイズとして 64 KB、キューの深さとして 128 を使用します。