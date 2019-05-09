---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/11/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 9c59b98fb615266c193f997c01c83922c18d4408
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2019
ms.locfileid: "56890997"
---
*キャッシュのウォームアップ*  
ReadOnly ホスト キャッシュを使用するディスクでは、ディスクの制限を超えた高 IOPS を実現できます。 ホスト キャッシュからこの最大の読み取りパフォーマンスを得るには、まず、このディスクのキャッシュをウォームアップする必要があります。 これにより、ベンチマーク ツールが CacheReads ボリュームで促進する読み取り IO が、直接ディスクにヒットするのではなく、実際にキャッシュにヒットするようになります。 キャッシュ ヒットにより、キャッシュが有効化された 1 つのディスクから追加の IOPS が得られます。

> [!IMPORTANT]
>  VM を再起動するたびに、キャッシュをウォームアップしてからベンチマークを実行する必要があります。

## <a name="tools"></a>ツール

### <a name="iometer"></a>Iometer

[Iometer ツールをダウンロード](http://sourceforge.net/projects/iometer/files/iometer-stable/2006-07-27/iometer-2006.07.27.win32.i386-setup.exe/download) します。

#### <a name="test-file"></a>テスト ファイル

Iometer では、ベンチマーク テストを実行するボリュームに格納されたテスト ファイルを使用します。 Iometer は、このテスト ファイルでの読み取りと書き込みを促進して、ディスクの IOPS とスループットを測定します。 このテスト ファイルを用意していない場合は、Iometer によって作成されます。 CacheReads ボリュームと NoCacheWrites ボリュームに iobw.tst という名前の 200 GB のテスト ファイルを作成します。

#### <a name="access-specifications"></a>アクセス仕様

IO 要求サイズ、% 読み取り/書き込み、% ランダム/順次の各仕様は、Iometer の [Access Specifications] タブを使用して構成します。 以下に示すシナリオごとにアクセス仕様を作成します。 アクセス仕様を作成したら、適切な名前 (例: RandomWrites\_8K、RandomReads\_8K) を付けて保存します。 テスト シナリオを実行するときに、対応する仕様を選択します。

最大書き込み IOPS シナリオのアクセス仕様の例を次に示します。  
    ![最大書き込み IOPS のアクセス仕様の例](../articles/virtual-machines/linux/media/premium-storage-performance/image8.png)

#### <a name="maximum-iops-test-specifications"></a>最大 IOPS テストの仕様

 最大 IOPS に達するように、小さな要求サイズを使用します。 8K の要求サイズを使用し、ランダム書き込み/読み取りの仕様を作成します。

| アクセス仕様 | 要求サイズ | ランダム % | 読み取り % |
| --- | --- | --- | --- |
| RandomWrites\_8 K |8 K |100 |0 |
| RandomReads\_8 K |8 K |100 |100 |

#### <a name="maximum-throughput-test-specifications"></a>最大スループット テストの仕様

 最大スループットに達するように、大きな要求サイズを使用します。 64 K の要求サイズを使用し、ランダム書き込み/読み取りの仕様を作成します。

| アクセス仕様 | 要求サイズ | ランダム % | 読み取り % |
| --- | --- | --- | --- |
| RandomWrites\_64 K |64 K |100 |0 |
| RandomReads\_64 K |64 K |100 |100 |

#### <a name="run-the-iometer-test"></a>Iometer テストの実行

 次の手順を実行して、キャッシュをウォームアップします。

1. 次に示す値を使用して、2 つのアクセス仕様を作成します。

   | Name | 要求サイズ | ランダム % | 読み取り % |
   | --- | --- | --- | --- |
   | RandomWrites\_1 MB |1 MB |100 |0 |
   | RandomReads\_1 MB |1 MB |100 |100 |
1. 次のパラメーターを使用して、キャッシュ ディスクの初期化の Iometer テストを実行します。 ターゲット ボリュームに 3 つのワーカー スレッドを使用し、キューの深さとして 128 を使用します。 [Test Setup]\(テストの設定\) タブでテストの [Run time]\(実行時間\) を 2 時間に設定します。

   | シナリオ | ターゲット ボリューム | Name | duration |
   | --- | --- | --- | --- |
   | キャッシュ ディスクの初期化 |CacheReads |RandomWrites\_1 MB |2 時間 |
1. 次のパラメーターを使用して、キャッシュ ディスクのウォームアップの Iometer テストを実行します。 ターゲット ボリュームに 3 つのワーカー スレッドを使用し、キューの深さとして 128 を使用します。 [Test Setup]\(テストの設定\) タブでテストの [Run time]\(実行時間\) を 2 時間に設定します。

   | シナリオ | ターゲット ボリューム | Name | 時間 |
   | --- | --- | --- | --- |
   | キャッシュ ディスクのウォームアップ |CacheReads |RandomReads\_1 MB |2 時間 |

キャッシュ ディスクをウォームアップしたら、下記のテスト シナリオを実行します。 Iometer テストを実行するために、ターゲット ボリューム **ごとに** 3 つ以上のワーカー スレッドを使用します。 次の表に従って、ワーカー スレッドごとに、ターゲット ボリュームを選択してキューの深さを設定し、保存済みのテストの仕様のいずれかを選択して、対応するテスト シナリオを実行します。 表では、各テストを実行したときの IOPS とスループットの予想される結果も示しています。 すべてのシナリオで、8 KB の小さな IO サイズと 128 の大きなキューの深さが使用されます。

| テスト シナリオ | ターゲット ボリューム | Name | 結果 |
| --- | --- | --- | --- |
| 最大 読み取り IOPS |CacheReads |RandomWrites\_8 K |50,000 IOPS  |
| 最大 書き込み IOPS |NoCacheWrites |RandomReads\_8 K |64,000 IOPS |
| 最大 合計 IOPS |CacheReads |RandomWrites\_8 K |100,000 IOPS |
| NoCacheWrites |RandomReads\_8 K | &nbsp; | &nbsp; |
| 最大 読み取り MB/秒 |CacheReads |RandomWrites\_64 K |524 MB/秒 |
| 最大 書き込み MB/秒 |NoCacheWrites |RandomReads\_64 K |524 MB/秒 |
| 合計 MB/秒 |CacheReads |RandomWrites\_64 K |1000 MB/秒 |
| NoCacheWrites |RandomReads\_64 K | &nbsp; | &nbsp; |

次のスクリーンショットは、合計 IOPS と合計スループットのシナリオでの Iometer テストの結果を示しています。

#### <a name="combined-reads-and-writes-maximum-iops"></a>読み取りと書き込みの合計最大 IOPS

![読み取りと書き込みの合計最大 IOPS](../articles/virtual-machines/linux/media/premium-storage-performance/image9.png)

#### <a name="combined-reads-and-writes-maximum-throughput"></a>読み取りと書き込みの合計最大スループット

![読み取りと書き込みの合計最大スループット](../articles/virtual-machines/linux/media/premium-storage-performance/image10.png)

### <a name="fio"></a>fio

FIO は、Linux VM でストレージのベンチマークを実行する一般的なツールです。 FIO では、さまざまな IO サイズ、順次読み取り/書き込み、ランダム読み取り/書き込みを柔軟に選択できます。 FIO は、ワーカー スレッドまたはワーカー プロセスを起動して、指定された I/O 操作を実行します。 各ワーカー スレッドがジョブ ファイルを使用して実行する必要がある I/O 操作の種類を指定できます。 以下の例に示すシナリオごとに、ジョブ ファイルを 1 つ作成しました。 これらのジョブ ファイルで仕様を変更して、Premium Storage で実行されるさまざまなワークロードのベンチマークを実行できます。 各例では、 **Ubuntu**を実行する Standard DS 14 VM を使用しています。 ベンチマークに関するセクションの最初に記載されている設定を使用し、ベンチマーク テストを実行する前にキャッシュをウォームアップします。

開始する前に、 [FIO をダウンロード](https://github.com/axboe/fio) し、仮想マシンにインストールします。

Ubuntu に対して次のコマンドを実行します。

```
apt-get install fio
```

ディスクでの書き込み操作を促進するための 4 つのワーカー スレッドと、読み取り操作を促進するための 4 つのワーカー スレッドを使用します。 書き込みワーカーは、キャッシュが "None" に設定された 10 個のディスクを含む "nocache" ボリュームのトラフィックを促進します。 読み取りワーカーは、キャッシュが "ReadOnly" に設定された 1 つのディスクを含む "readcache" ボリュームのトラフィックを促進します。

#### <a name="maximum-write-iops"></a>最大書き込み IOPS

 最大書き込み IOPS を得るために、次の仕様でジョブ ファイルを作成します。 ファイル名を "fiowrite.ini" にします。

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[writer1]
rw=randwrite
directory=/mnt/nocache
[writer2]
rw=randwrite
directory=/mnt/nocache
[writer3]
rw=randwrite
directory=/mnt/nocache
[writer4]
rw=randwrite
directory=/mnt/nocache
```

これまでのセクションで説明した設計ガイドラインに沿った次の重要事項に注意してください。 IOPS を最大限に高めるために、これらの仕様が不可欠となります。  

* 256 の大きなキューの深さ。  
* 8 KB の小さなブロック サイズ。  
* ランダム書き込みを実行する複数のスレッド。

次のコマンドを実行して、FIO テストを 30 秒間実行します。  

```
sudo fio --runtime 30 fiowrite.ini
```

テストの実行中、VM と Premium ディスクが提供している書き込み IOPS の数を確認できます。 次のサンプルに示すように、DS14 VM は書き込み IOPS の上限である 50,000 IOPS を実現しています。  
    ![VM と Premium ディスクが提供している書き込み IOPS の数](../articles/virtual-machines/linux/media/premium-storage-performance/image11.png)

#### <a name="maximum-read-iops"></a>最大読み取り IOPS

 最大読み取り IOPS を得るために、次の仕様でジョブ ファイルを作成します。 ファイル名を "fioread.ini" にします。

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache
```

これまでのセクションで説明した設計ガイドラインに沿った次の重要事項に注意してください。 IOPS を最大限に高めるために、これらの仕様が不可欠となります。

* 256 の大きなキューの深さ。  
* 8 KB の小さなブロック サイズ。  
* ランダム書き込みを実行する複数のスレッド。

次のコマンドを実行して、FIO テストを 30 秒間実行します。

```
sudo fio --runtime 30 fioread.ini
```

テストの実行中、VM と Premium ディスクが提供している読み取り IOPS の数を確認できます。 次のサンプルに示すように、DS14 VM は 64,000 を超える読み取り IOPS を実現しています。 これは、ディスクとキャッシュのパフォーマンスの合計です。  
    ![](../articles/virtual-machines/linux/media/premium-storage-performance/image12.png)

#### <a name="maximum-read-and-write-iops"></a>最大読み取り/書き込み IOPS

 読み取りと書き込みの最大合計 IOPS を得るために、次の仕様でジョブ ファイルを作成します。 ファイル名を "fioreadwrite.ini" にします。

```ini
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer2]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer3]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer4]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
```

これまでのセクションで説明した設計ガイドラインに沿った次の重要事項に注意してください。 IOPS を最大限に高めるために、これらの仕様が不可欠となります。

* 128 の大きなキューの深さ。  
* 4 KB の小さなブロック サイズ。  
* ランダム読み取り/書き込みを実行する複数のスレッド。

次のコマンドを実行して、FIO テストを 30 秒間実行します。

```
sudo fio --runtime 30 fioreadwrite.ini
```

テストの実行中、VM と Premium ディスクが提供している読み取りと書き込みの合計 IOPS を確認できます。 次のサンプルに示すように、DS14 VM は 100,000 を超える読み取りと書き込みの合計 IOPS を実現しています。 これは、ディスクとキャッシュのパフォーマンスの合計です。  
    ![合計読み取り/書き込み IOPS](../articles/virtual-machines/linux/media/premium-storage-performance/image13.png)

#### <a name="maximum-combined-throughput"></a>最大スループットの合計

 読み取りと書き込みの最大合計スループットを得るには、大きなブロック サイズ、大きなキューの深さ、読み取り/書き込みを実行する複数のスレッドを使用します。 ブロック サイズとして 64 KB、キューの深さとして 128 を使用します。
