---
title: "Linux での MySQL パフォーマンスの最適化 | Microsoft Docs"
description: "Linux を実行する Azure の仮想マシン (VM) で実行されている MySQL を最適化する方法について説明します。"
services: virtual-machines-linux
documentationcenter: 
author: NingKuang
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 0c1c7fc5-a528-4d84-b65d-2df225f2233f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/15/2015
ms.author: ningk
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: bd313ae585667cc80d44ae50f9d97659b8de62eb
ms.lasthandoff: 03/27/2017


---
# <a name="optimize-mysql-performance-on-azure-linux-vms"></a>Azure Linux VM 上での MySQL のパフォーマンスを最適化する
Azure では、仮想ハードウェアの選択およびソフトウェアの構成の両方で MySQL のパフォーマンスに影響を与える多くの要素があります。 この記事では、ストレージ、システム、およびデータベースの構成でのパフォーマンスの最適化について説明します。

> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、[Azure Resource Manager](../../../resource-manager-deployment-model.md) とクラシックの 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。 Resource Manager モデルを使用した Linux VM の最適化については、「[Azure での Linux VM の最適化](../../virtual-machines-linux-optimization.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。

## <a name="utilize-raid-on-an-azure-virtual-machine"></a>Azure 仮想マシン上での RAID の使用
ストレージは、クラウド環境でのデータベース パフォーマンスに影響を与える重要な要素です。 RAID は、1 つのディスクと比較して、同時実行制御を使用して高速アクセスを提供できます。 詳細については、「[Standard RAID levels](http://en.wikipedia.org/wiki/Standard_RAID_levels)」(標準の RAID レベル) を参照してください。   

Azure でのディスク I/O スループットと I/O 応答時間は、RAID によって改善できます。 ラボ テストでは、RAID のディスクの数が 2 倍 (2 から 4、4 から 8 など) になると、平均でスループットが 2 倍になり I/O 応答時間は半分に短縮されました。 詳細については、 [付録 A](#AppendixA) を参照してください。  

ディスク I/O の他に、RAID レベルを上げると、MySQL のパフォーマンスが向上します。  詳細については、 [付録 B](#AppendixB) を参照してください。  

チャンク サイズの検討が必要になることもあります。 一般に、チャンクのサイズを大きくした場合、特に大量の書き込みに対してオーバーヘッドが低くなります。 ただし、チャンク サイズが大きすぎるとオーバーヘッドが増大し、RAID を活用できなくなる場合があります。 現在の既定のサイズは 512 KB であり、最も一般的な運用環境に最適であることがわかっています。 詳細については、 [付録 C](#AppendixC) を参照してください。   

仮想マシン タイプの種類ごとに、追加できるディスクの数に制限があります。 これらの制限の詳細については、[Azure の仮想マシンおよびクラウド サービスのサイズ](http://msdn.microsoft.com/library/azure/dn197896.aspx)に関するトピックを参照してください。 この記事での RAID 例の説明には 4 つのデータ ディスクを接続する必要がありますが、より少ないディスクで RAID を設定することもできます。  

この記事では、Linux 仮想マシンを既に作成していること、および MYSQL をインストールして構成していることを前提としています。 作業の開始の詳細については、「MySQL を Azure でインストールする方法」を参照してください。  

### <a name="set-up-raid-on-azure"></a>Azure における RAID の設定
次の手順で、Azure クラシック ポータルを使用して Azure に RAID を作成する方法を説明します。 Windows PowerShell スクリプトを使用して RAID を設定することもできます。
この例では、4 つのディスクで RAID 0 を構成します。  

#### <a name="add-a-data-disk-to-your-virtual-machine"></a>仮想マシンにデータ ディスクを追加する
Azure クラシック ポータルの [仮想マシン] ページで、データ ディスクを追加する仮想マシンをクリックします。 この例では、仮想マシンは mysqlnode1 です。  

![仮想マシン][1]

仮想マシンのページで、 **[ダッシュボード]**をクリックします。  

![仮想マシンのダッシュボード][2]

タスク バーで **[ディスクの接続]** をクリックします。

![仮想マシン タスク バー][3]

**[空のディスクの接続]**をクリックします。  

![空のディスクの接続][4]

データ ディスクの場合、**[ホスト キャッシュ設定]** を **[なし]** に設定する必要があります。  

これにより、1 つの空のディスクが仮想マシンに追加されます。 この手順をあと 3 回繰り返して、RAID に 4 つのデータ ディスクを追加します。  

カーネル メッセージ ログを参照して、仮想マシンに追加されたドライブを確認できます。 たとえば、Ubuntu でこれを参照するには、次のコマンドを実行します。  

    sudo grep SCSI /var/log/dmesg

#### <a name="create-raid-with-the-additional-disks"></a>追加のディスクで RAID を作成する
次の手順では、[Linux でのソフトウェア RAID の構成方法](../../virtual-machines-linux-configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)について説明します。

> [!NOTE]
> XFS ファイル システムを使用している場合は、RAID を作成した後に次の手順を実行してください。
>
>

Debian、Ubuntu、または Linux Mint に XFS をインストールするには、次のコマンドを使用します。  

    apt-get -y install xfsprogs  

Fedora、CentOS、または RHEL に XFS をインストールするには、次のコマンドを使用します。  

    yum -y install xfsprogs  xfsdump


#### <a name="set-up-a-new-storage-path"></a>新しいストレージのパスを設定します。
新しいストレージ パスを設定するには、次のコマンドを使用します。  

    root@mysqlnode1:~# mkdir -p /RAID0/mysql

#### <a name="copy-the-original-data-to-the-new-storage-path"></a>新しいストレージ パスに、元のデータをコピーします。
新しいストレージ パスにデータをコピーするには、次のコマンドを使用します。  

    root@mysqlnode1:~# cp -rp /var/lib/mysql/* /RAID0/mysql/

#### <a name="modify-permissions-so-mysql-can-access-read-and-write-the-data-disk"></a>MySQL がデータ ディスクにアクセス (読み取り、書き込み) できるようにアクセス許可を変更します。
次のコマンドを使用してアクセス許可を変更します。  

    root@mysqlnode1:~# chown -R mysql.mysql /RAID0/mysql && chmod -R 755 /RAID0/mysql


## <a name="adjust-the-disk-io-scheduling-algorithm"></a>ディスク I/O のスケジューリング アルゴリズムを調整する
Linux では次の 4 つの種類の I/O スケジューリング アルゴリズムを実装します。  

* NOOP アルゴリズム (操作なし)
* 期限アルゴリズム (期限)
* 完全に均等化されたキューイング アルゴリズム (CFQ)
* 予算期間アルゴリズム (予想)  

パフォーマンスを最適化するためにさまざまなシナリオでさまざまな I/O スケジューラを選択することができます。 完全にランダムなアクセス環境では、CFQ アルゴリズムと期限アルゴリズムにはパフォーマンス上の大幅な差はありません。 安定性のため、MySQL データベース環境を期限アルゴリズムに設定することをお勧めします。 順次入出力が多くがある場合、CFQ ではディスク I/O パフォーマンスが低下する可能性があります。   

SSD およびその他の機器では、NOOP アルゴリズムまたは期限アルゴリズムで既定のスケジューラよりも優れたパフォーマンスを実現できます。   

カーネル 2.5 以前の場合、既定の I/O スケジューリング アルゴリズムは期限アルゴリズムです。 カーネル 2.6.18 以降では、CFQ が既定の I/O スケジューリング アルゴリズムになりました。  カーネルの起動時にこの設定を指定するか、システムの実行中に設定を動的に変更できます。  

次の例に、Debian ディストリビューション ファミリで既定のスケジューラを確認し、NOOP アルゴリズムに設定する方法を示します。  

### <a name="view-the-current-io-scheduler"></a>現在の I/O スケジューラの表示
スケジューラを表示するには、次のコマンドを実行します。  

    root@mysqlnode1:~# cat /sys/block/sda/queue/scheduler

現在のスケジューラを示す次の出力が表示されます。  

    noop [deadline] cfq


### <a name="change-the-current-device-devsda-of-the-io-scheduling-algorithm"></a>I/O スケジューリング アルゴリズムの現在のデバイス (/dev/sda) を変更する
現在のデバイスを変更するには、次のコマンドを実行します。  

    azureuser@mysqlnode1:~$ sudo su -
    root@mysqlnode1:~# echo "noop" >/sys/block/sda/queue/scheduler
    root@mysqlnode1:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
    root@mysqlnode1:~# update-grub

> [!NOTE]
> これを /dev/sda だけに設定するのは役に立ちません。 データベースが存在するすべてのデータ ディスクに設定する必要があります。  
>
>

次の出力では、grub.cfg が正常に再構築され、既定のスケジューラが NOOP に更新されたことを確認できます。  

    Generating grub configuration file ...
    Found linux image: /boot/vmlinuz-3.13.0-34-generic
    Found initrd image: /boot/initrd.img-3.13.0-34-generic
    Found linux image: /boot/vmlinuz-3.13.0-32-generic
    Found initrd image: /boot/initrd.img-3.13.0-32-generic
    Found memtest86+ image: /memtest86+.elf
    Found memtest86+ image: /memtest86+.bin
    done

Redhat ディストリビューション ファミリでは、次のコマンドを実行するだけで済みます。

    echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

## <a name="configure-system-file-operations-settings"></a>システム ファイルの操作設定を構成する
1 つのベスト プラクティスは、ファイル システム上の *atime* ログ機能を無効にすることです。 Atime はファイルの最後のアクセス時刻です。 ファイルがアクセスされるたびに、ファイル システムは、ログにタイムスタンプを記録します。 ただし、この情報はほとんど使用しません。 必要としない場合は、無効にして全体的なディスク アクセス時間を短縮することができます。  

Atime のログ記録を無効にするには、ファイル システムの構成ファイルの /etc/fstab を変更し、 **noatime** オプションを追加する必要があります。  

たとえば、次のサンプルのように vim/etc/fstab ファイルを編集して noatime を追加します。  

    # CLOUD_IMG: This file was created/modified by the Cloud Image build process
    UUID=3cc98c06-d649-432d-81df-6dcd2a584d41       /        ext4   defaults,discard        0 0
    #Add the “noatime” option below to disable atime logging
    UUID="431b1e78-8226-43ec-9460-514a9adf060e"     /RAID0   xfs   defaults,nobootwait, noatime 0 0
    /dev/sdb1       /mnt    auto    defaults,nobootwait,comment=cloudconfig 0       2

次に、以下のコマンドで、ファイル システムを再マウントします。  

    mount -o remount /RAID0

変更された結果をテストします。 テスト ファイルを変更しても、アクセス時間は更新されません。 次の例に、変更前と変更後でコードがどのように表示されるかを示します。

変更前:        

![アクセス変更前のコード][5]

変更後:

![アクセス変更後のコード][6]

## <a name="increase-the-maximum-number-of-system-handles-for-high-concurrency"></a>高い同時実行性を実現するためにシステム処理の最大数を増やす
MySQL は同時実行性の高いデータベースです。 同時実行処理の既定数は、Linuxでは 1024 ですが、必ずしも十分ではありません。 次の手順で、システムの同時実行処理の最大数を増やして、MySQL の高い同時実行性をサポートします。

### <a name="modify-the-limitsconf-file"></a>Limits.conf ファイルを変更します。
許可される同時実行処理の最大数を増やすには、/etc/security/limits.conf ファイルに次の 4 つの行を追加します。 システムがサポートできる最大数は 65536 であることに注意してください。   

    * ソフト nofile 65536
    * ハード nofile 65536
    * ソフト nproc 65536
    * ハード nproc 65536

### <a name="update-the-system-for-the-new-limits"></a>新しい制限値にシステムを更新します。
システムを更新するには、次のコマンドを実行します。  

    ulimit -SHn 65536
    ulimit -SHu 65536

### <a name="ensure-that-the-limits-are-updated-at-boot-time"></a>制限値は起動時に更新されることを確認します。
起動時に有効になるように、/etc/rc.local ファイルに次の起動コマンドを入力します。  

    echo “ulimit -SHn 65536” >>/etc/rc.local
    echo “ulimit -SHu 65536” >>/etc/rc.local

## <a name="mysql-database-optimization"></a>MySQL データベースの最適化
Azure での MySQL の構成では、オンプレミスのマシンの場合と同じパフォーマンス チューニング戦略を用いることができます。  

メインの I/O の最適化のルールは、次のとおりです。   

* キャッシュ サイズを増加する。
* I/O 応答時間を短縮する。  

MySQL サーバーの設定を最適化するために、サーバーとクライアント コンピューターの両方の既定の構成ファイルである my.cnf file を更新することができます。  

次の構成項目は、MySQL のパフォーマンスに影響する主な要素です。  

* **innodb_buffer_pool_size**: バッファー プールには、バッファー内のデータとインデックスが含まれています。 通常、これは物理メモリの 70% に設定します。
* **innodb_log_file_size**: これは、再実行ログのサイズです。 再実行ログを使用して、書き込み操作が高速かつ信頼性が高く、クラッシュ後に回復可能なことを確認します。 これは、書き込み操作を記録するのに十分な領域である 512 MB に設定されています。
* **max_connections**: アプリケーションは適切に接続を終了しない場合があります。 大きな値では、サーバーがアイドル状態の接続をリサイクルするのに時間がかかります。 最大接続数は 10,000 ですが、推奨される最大値は 5,000 です。
* **innodb_file_per_table**: この設定は、個別のファイルにテーブルを保存する InnoDB の機能を有効または無効にします。 このオプションを有効にすると、いくつかの高度な管理操作を効率的に適用できます。 パフォーマンスの観点では、テーブルの領域の転送を高速化し、ごみ管理のパフォーマンスを最適化できます。 このオプションの推奨設定は ON です。</br></br>
MySQL 5.6 以降では既定の設定が ON のため、操作は不要です。 MySQL 5.6 よりも前のバージョンの場合、既定の設定は OFF です。 新しく作成されたテーブルだけが影響を受けるため、この設定はデータを読み込む前に変更する必要があります。
* **innodb_flush_log_at_trx_commit**: 既定値は 1 、設定範囲は 0 - 2 です。 既定値は、スタンドアロン MySQL DB の最も適切なオプションです。 2 に設定すると最大限のデータ整合性が実現されるため、MySQL Cluster のマスターに適しています。 0 に設定すると、信頼性に影響を与えるデータ損失が発生する可能性がありますが、同時にパフォーマンスが改善される場合もあり、MySQL Cluster のスレーブに適してします。
* **innodb_log_buffer_size**: ログ バッファーによって、トランザクションがコミットする前にログをディスクにフラッシュすることなくトランザクションを実行できます。 ただし、ラージ バイナリ オブジェクトまたはテキスト フィールドがある場合、キャッシュが短時間で消費され、頻繁にディスク I/O がトリガーされます。 Innodb_log_waits 状態変数が 0 でない場合は、バッファー サイズを大きくすることが推奨されます。
* **query_cache_size**: 最善のオプションは、最初から無効にすることです。 query_cache_size は 0 (これは MySQL 5.6 では既定の設定です) に設定し、その他のメソッドを使用してクエリを高速化します。  

最適化前後のパフォーマンスの比較については、「[付録 D](#AppendixD)」を参照してください。

## <a name="turn-on-the-mysql-slow-query-log-for-analyzing-the-performance-bottleneck"></a>パフォーマンスのボトルネックを分析するためには、MySQL 低速クエリ ログを有効にします。
MySQL 低速クエリ ログによって、MySQL の低速のクエリを特定できます。 MySQL 低速クエリ ログを有効にすると、 **mysqldumpslow** のような MySQL ツールを使用して、パフォーマンスのボトルネックを特定できます。  

既定では、これは有効ではありません。 低速クエリ ログを有効化すると、CPU リソースが大量に消費される可能性があります。 この機能は、パフォーマンスのボトルネックのトラブルシューティングのために一時的に有効にすることが推奨されます。 低速クエリ ログを有効にするには、次の手順を実行します。

1. 次の行を最後に追加して my.cnf ファイルを変更します。

        long_query_time = 2
        slow_query_log = 1
        slow_query_log_file = /RAID0/mysql/mysql-slow.log

2. MySQL サーバーを再起動します。

        service  mysql  restart

3. **show** コマンドを使用して、設定が有効になっているかどうかを確認します。

![Slow-query-log ON][7]   

![Slow-query-log results][8]

この例では、低速クエリ ログ機能が有効になっています。 **mysqldumpslow** ツールを使用して、パフォーマンスのボトルネックを特定し、インデックスを追加するなど、パフォーマンスを最適化することができます。

## <a name="appendices"></a>付録
以下は、対象となるラボ環境で生成されたサンプル パフォーマンス テストのデータです。 これらのデータは、さまざまなパフォーマンス チューニング方法によるパフォーマンス データの傾向についての一般的な情報を提供するものです。 結果は、環境または製品のバージョンによって異なる場合があります。

### <a name="AppendixA"></a>付録 A  
**さまざまな RAID レベルでのディスク パフォーマンス (IOPS)**

![さまざまな RAID レベルでのディスク IOPS][9]

**テスト コマンド**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=5G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite

> [!NOTE]
> このテストの作業負荷は、RAID の上限に到達するために、64 のスレッドを使用します。
>
>

### <a name="AppendixB"></a>付録 B  
**さまざまな RAID レベルでの MySQL のパフォーマンス (スループット) の比較**   
(XFS ファイル システム)

![さまざまな RAID レベルでの MySQL のパフォーマンスの比較][10]  
![さまざまな RAID レベルでの MySQL のパフォーマンスの比較][11]

**テスト コマンド**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb

**さまざまな RAID レベルでの MySQL のパフォーマンス (OLTP) の比較**  
![さまざまな RAID レベルでの MySQL のパフォーマンス (OLTP) の比較][12]

**テスト コマンド**

    time sysbench --test=oltp --db-driver=mysql --mysql-user=root --mysql-password=0ps.123  --mysql-table-engine=innodb --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-socket=/var/run/mysqld/mysqld.sock --mysql-db=test --oltp-table-size=1000000 prepare

### <a name="AppendixC"></a>付録 C   
**さまざまなチャンク サイズのディスク パフォーマンス (IOPS) の比較**  
(XFS ファイル システム)

![][13]

**テスト コマンド**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=30G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite
    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=1G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite  

このテストに使用したファイルのサイズはそれぞれ 30 GB、1 GB であり、ファイル システムは RAID 0 (4 ディスク) の XFS です。

### <a name="AppendixD"></a>付録 D  
**最適化前後の MySQL のパフォーマンス (スループット) の比較**  
(XFS ファイル システム)

![最適化前後の MySQL のパフォーマンス (スループット) の比較][14]

**テスト コマンド**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb,misam

**既定と最適化の構成設定は、次のとおりです。**

| parameters | 既定値 | 最適化 |
| --- | --- | --- |
| **innodb_buffer_pool_size** |なし |7 GB |
| **innodb_log_file_size** |5 MB |512 MB |
| **max_connections** |100 |5000 |
| **innodb_file_per_table** |0 |1 |
| **innodb_flush_log_at_trx_commit** |1 |2 |
| **innodb_log_buffer_size** |8 MB |128 MB |
| **query_cache_size** |16 MB |0 |

詳細な[最適化構成パラメーター](http://dev.mysql.com/doc/refman/5.6/en/innodb-configuration.html)については、[MySQL の正式な説明](http://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html#sysvar_innodb_flush_method)に関するページを参照してください。  

  **テスト環境**  

| ハードウェア | 詳細 |
| --- | --- |
| CPU |AMD Opteron(tm) プロセッサ 4171 HE/4 コア |
| メモリ |14 GB |
| ディスク |10 GB/ディスク |
| OS |Ubuntu 14.04.1 LTS |

[1]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-01.png
[2]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-02.png
[3]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-03.png
[4]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-04.png
[5]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-05.png
[6]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-06.png
[7]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-07.png
[8]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-08.png
[9]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-09.png
[10]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-10.png
[11]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-11.png
[12]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-12.png
[13]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-13.png
[14]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-14.png

