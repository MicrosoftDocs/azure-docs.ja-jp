---
title: Azure Site Recovery サービスを使用してディザスター リカバリーを設定する際にレプリケーションからディスクを除外する | Microsoft Docs
description: Azure へのディザスター リカバリー時にレプリケーションから VM ディスクを除外する方法を説明します。
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 01/19/2019
ms.author: mayg
ms.openlocfilehash: f86ded99ef5280a4e6929c39a9fd323d1b61f6f0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57992343"
---
# <a name="exclude-disks-from-replication"></a>レプリケーションからディスクを除外する
この記事では、ディスクをレプリケーションから除外する方法について説明します。 除外することにより、レプリケーションによる帯域幅の消費や、このようなディスクによって使用されるターゲット側のリソースを最適化できます。

## <a name="supported-scenarios"></a>サポートされるシナリオ

**機能** | **VMware から Azure** | **Hyper-V から Azure** | **Azure から Azure**| **Hyper-V から Hyper-V** 
--|--|--|--|--
ディスクの除外 | はい | はい | いいえ  | いいえ 

## <a name="why-exclude-disks-from-replication"></a>レプリケーションからディスクを除外する理由
レプリケーションからディスクを除外する必要性は、多くの場合、次の理由から生じます。

- 除外対象のディスクに頻繁に変更されるデータが存在するものの重要度が低いか、レプリケートする必要がない。

- そのような変更頻度の高いデータをレプリケートしないことで、ストレージ リソースとネットワーク リソースを節約したい。

## <a name="what-are-the-typical-scenarios"></a>一般的なシナリオ
除外対象の候補として最適な、変更頻度の高いデータの具体的な例を特定できます。 たとえば、ページング ファイル (pagefile.sys) への書き込みと Microsoft SQL Server の tempdb ファイルへの書き込みが該当します。 ワークロードとストレージ サブシステムによっては、ページング ファイルの変更が膨大な量に上る可能性があります。 ただし、そのようなデータをプライマリ サイトから Azure にレプリケートするには、多くのリソースが必要となります。 したがって、1 つの仮想ディスクにオペレーティング システムとページング ファイルの両方を含む仮想マシンのレプリケーションは、次の手順を使用して最適化できます。

1. 1 つの仮想ディスクを 2 つの仮想ディスクに分割します。 1 つの仮想ディスクにオペレーティング システムを格納し、もう 1 つの仮想ディスクにページング ファイルを格納します。
2. ページング ファイル用のディスクをレプリケーションから除外します。

同様に、次の手順を使用して、Microsoft SQL Server の tempdb ファイルとシステム データベース ファイルの両方があるディスクを最適化することができます。

1. システム データベースと tempdb を 2 つの異なるディスクに格納します。
2. tempdb 用のディスクをレプリケーションから除外します。

## <a name="how-to-exclude-disks"></a>ディスクを除外する方法
Azure Site Recovery ポータルで[レプリケーションを有効にする](site-recovery-hyper-v-site-to-azure.md)ためのワークフローに従って、仮想マシンを保護します。 ワークフローの 4 番目の手順で、**[DISK TO REPLICATE (レプリケートするディスク)]** 列を使用して、レプリケーションから除外するディスクを指定します。 既定では、すべてのディスクがレプリケーションの対象として選択されます。 レプリケーションから除外するディスクのチェック ボックスをオフにし、手順に従ってレプリケーションを有効にします。

![レプリケーションから除外するディスクを指定し、Hyper-V から Azure へのフェールバックのためのレプリケーションを有効にする](./media/hyper-v-exclude-disk/enable-replication6-with-exclude-disk.png)

>[!NOTE]
>
> * レプリケーションから除外できるのは、ベーシック ディスクだけです。 オペレーティング システム ディスクは除外できません。 ダイナミック ディスクは除外しないことをお勧めします。 Azure Site Recovery では、ゲスト仮想マシンのどの仮想ハード ディスク (VHD) がベーシック ディスクまたはダイナミック ディスクであるかを識別できません。  依存するダイナミック ボリューム ディスクすべてが除外されていない場合、保護されたダイナミック ディスクはフェールオーバー仮想マシン上で障害が発生したディスクになり、そのディスク上のデータにアクセスすることができなくなります。
> * レプリケーションを有効にした後は、レプリケートするディスクを追加または削除することはできません。 ディスクを追加または除外する場合は、仮想マシンの保護を無効にし、再度有効にする必要があります。
> * アプリケーションが動作するために必要なディスクを除外した場合、Azure へのフェールオーバー後、レプリケートされたアプリケーションを実行できるように、Azure でディスクを手動で作成する必要があります。 別の方法として、Azure Automation を復旧計画に組み込んで、マシンのフェールオーバー時にディスクを作成することもできます。
> * Azure に手動で作成したディスクはフェールバックされません。 たとえば、3 つのディスクをフェールオーバーし、Azure Virtual Machines に直接 2 つのディスクを作成した場合、フェールオーバーされた 3 つのディスクだけが Azure から Hyper-V にフェールバックされます。 Hyper-V から Azure へのフェールバックまたはレプリケーションの反転に、手動で作成されたディスクを含めることはできません。

## <a name="end-to-end-scenarios-of-exclude-disks"></a>ディスク除外のエンド ツー エンド シナリオ
ディスク除外機能について理解するために、2 つのシナリオを考えてみましょう。

- SQL Server の tempdb 用ディスク
- ページング ファイル (pagefile.sys) 用ディスク

## <a name="example-1-exclude-the-sql-server-tempdb-disk"></a>例 1:SQL Server の tempdb 用ディスクを除外する
除外できる tempdb が存在する SQL Server 仮想マシンを考えてみましょう。

仮想ディスクの名前は SalesDB です。

ソース仮想マシン上のディスクは、次のとおりです。


**ディスク名** | **ゲスト オペレーティング システム ディスク番号** | **ドライブ文字** | **ディスクに格納されているデータの種類**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | オペレーティング システム ディスク
DB-Disk1| Disk1 | D:\ | SQL システム データベースとユーザー データベース 1
DB-Disk2 (保護対象から除外するディスク) | Disk2 | E:\ | 一時ファイル
DB-Disk3 (保護対象から除外するディスク) | Disk3 | F:\ | SQL tempdb データベース (フォルダーのパスは F:\MSSQL\Data\) <br /> <br />フェールオーバー前にフォルダー パスをメモします。
DB-Disk4 | Disk4 |G:\ |ユーザー データベース 2

仮想マシンの 2 つのディスク上のデータの変更は一時的であるため、SalesDB 仮想マシンを保護する一方で、Disk2 と Disk3 をレプリケーションから除外します。 Azure Site Recovery は、これらのディスクをレプリケートしません。 フェールオーバーが発生した場合、これらのディスクは Azure 上のフェールオーバー仮想マシンにも存在しません。

フェールオーバー後の Azure 仮想マシンには、次のディスクが存在します。

**ゲスト オペレーティング システム ディスク番号** | **ドライブ文字** | **ディスクに格納されているデータの種類**
--- | --- | ---
DISK0 | C:\ | オペレーティング システム ディスク
Disk1 | E:\ | 一時ストレージ<br /> <br />このディスクは Azure によって追加され、利用可能な最初のドライブ文字が割り当てられます。
Disk2 | D:\ | SQL システム データベースとユーザー データベース 1
Disk3 | G:\ | ユーザー データベース 2

Disk2 と Disk3 は SalesDB 仮想マシンから除外されたため、一覧の利用可能な最初のドライブ文字は E: となります。 一時的な記憶域のボリュームには、Azure によって E: が割り当てられます。 レプリケート対象のディスクについては、いずれもドライブ文字は変化しません。

SQL の tempdb ディスクとして使用されていた Disk3 (tempdb フォルダーのパスは F:\MSSQL\Data\) はレプリケーションから除外されています。 このディスクは、フェールオーバー仮想マシンでは利用できません。 その結果、SQL サービスは停止状態となります。SQL サービスには F:\MSSQL\Data パスが必要であるためです。

このパスを作成する方法は 2 つあります。

- 新しいディスクを追加し、tempdb フォルダーのパスを割り当てます。
- 既存の一時記憶域ディスクを tempdb フォルダー パスに使用します。

### <a name="add-a-new-disk"></a>新しいディスクを追加する場合

1. フェールオーバーの前に、SQL の tempdb.mdf と tempdb.ldf のパスをメモします。
2. Azure Portal から、フェールオーバー仮想マシンに新しいディスクを追加します。レプリケーション元の SQL tempdb ディスク (Disk3) と同じかそれ以上のサイズを確保してください。
3. Azure 仮想マシンにサインインします。 ディスク管理 (diskmgmt.msc) コンソールから、新しく追加したディスクを初期化してフォーマットします。
4. SQL tempdb ディスクに使用されていたものと同じドライブ文字を割り当てます (F:)。
5. F: ボリューム (F:\MSSQL\Data) に tempdb フォルダーを作成します。
6. サービス コンソールから SQL サービスを開始します。

### <a name="use-an-existing-temporary-storage-disk-for-the-sql-tempdb-folder-path"></a>既存の一時記憶域ディスクを SQL tempdb フォルダー パスに使用する場合:

1. コマンド プロンプトを開きます。
2. コマンド プロンプトから SQL Server を回復モードで実行します。

        Net start MSSQLSERVER /f / T3608

3. 次の sqlcmd を実行して、tempdb のパスを新しいパスに変更します。

        sqlcmd -A -S SalesDB        **Use your SQL DBname**
        USE master;     
        GO      
        ALTER DATABASE tempdb       
        MODIFY FILE (NAME = tempdev, FILENAME = 'E:\MSSQL\tempdata\tempdb.mdf');
        GO      
        ALTER DATABASE tempdb       
        MODIFY FILE (NAME = templog, FILENAME = 'E:\MSSQL\tempdata\templog.ldf');       
        GO


4. Microsoft SQL Server サービスを停止します。

        Net stop MSSQLSERVER
5. Microsoft SQL Server サービスを開始します。

        Net start MSSQLSERVER

一時記憶域ディスクについては、次の Azure ガイドラインを参照してください。

* [Azure VM で SSD を使用した SQL Server TempDB とバッファー プール拡張機能の保存](https://blogs.technet.microsoft.com/dataplatforminsider/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/)
* [Azure Virtual Machines における SQL Server のパフォーマンスに関するベスト プラクティス](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance)

## <a name="failback-from-azure-to-an-on-premises-host"></a>フェールバック (Azure からオンプレミスのホスト)
Azure からオンプレミスの Hyper-V ホストにフェールオーバーしたときにどのディスクがレプリケートされるのかを整理してみましょう。 Azure に手動で作成したディスクはレプリケートされません。 たとえば、3 つのディスクをフェールオーバーし、Azure Virtual Machines に直接 2 つのディスクを作成した場合、フェールオーバーされた 3 つのディスクだけがフェールバックされます。 オンプレミスから Azure へのフェールバックまたは再保護に、手動で作成したディスクを含めることはできません。 また、一時記憶域ディスクもオンプレミス ホストにはレプリケートされません。

### <a name="failback-to-original-location-recovery"></a>元の場所へのフェールバック

前の例では、Azure 仮想マシンのディスク構成は次のようになります。

**ゲスト オペレーティング システム ディスク番号** | **ドライブ文字** | **ディスクに格納されているデータの種類**
--- | --- | ---
DISK0 | C:\ | オペレーティング システム ディスク
Disk1 | E:\ | 一時ストレージ<br /> <br />このディスクは Azure によって追加され、利用可能な最初のドライブ文字が割り当てられます。
Disk2 | D:\ | SQL システム データベースとユーザー データベース 1
Disk3 | G:\ | ユーザー データベース 2

元の場所へのフェールバックの場合、フェールバック仮想マシンのディスク構成は、Hyper-V で使用されていた元の仮想マシンのディスク構成と同じになります。 VMware から Azure へのレプリケーションで除外されたディスクは、フェールバック仮想マシンで利用できます。

Azure からオンプレミスの Hyper-V に対して計画されたフェールオーバーを実行した場合、Hyper-V 仮想マシン (元の場所) のディスク構成は次のようになります。

**ディスク名** | **ゲスト オペレーティング システム ディスク番号** | **ドライブ文字** | **ディスクに格納されているデータの種類**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 |   C:\ | オペレーティング システム ディスク
DB-Disk1 | Disk1 | D:\ | SQL システム データベースとユーザー データベース 1
DB-Disk2 (除外されたディスク) | Disk2 | E:\ | 一時ファイル
DB-Disk3 (除外されたディスク) | Disk3 | F:\ | SQL tempdb データベース (フォルダーのパスは F:\MSSQL\Data\)
DB-Disk4 | Disk4 | G:\ | ユーザー データベース 2

## <a name="example-2-exclude-the-paging-file-pagefilesys-disk"></a>例 2:ページング ファイル (pagefile.sys) 用ディスクを除外する

除外できるページング ファイル ディスクが存在する仮想マシンを考えてみましょう。
次の 2 つのケースがあります。

### <a name="case-1-the-paging-file-is-configured-on-the-d-drive"></a>ケース 1:ページング ファイルが D: ドライブ上で構成されている
ディスク構成を次に示します。

**ディスク名** | **ゲスト オペレーティング システム ディスク番号** | **ドライブ文字** | **ディスクに格納されているデータの種類**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | オペレーティング システム ディスク
DB-Disk1 (保護対象から除外するディスク) | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | ユーザー データ 1
DB-Disk3 | Disk3 | F:\ | ユーザー データ 2

ソース仮想マシンにおけるページング ファイルの設定を次に示します。

![ソース仮想マシンにおけるページング ファイルの設定](./media/hyper-v-exclude-disk/pagefile-on-d-drive-sourceVM.png)

Hyper-V から Azure に仮想マシンをフェールオーバーした後の Azure 仮想マシンのディスク構成を次に示します。

**ディスク名** | **ゲスト オペレーティング システム ディスク番号** | **ドライブ文字** | **ディスクに格納されているデータの種類**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | オペレーティング システム ディスク
DB-Disk1 | Disk1 | D:\ | 一時ストレージ<br /> <br />pagefile.sys
DB-Disk2 | Disk2 | E:\ | ユーザー データ 1
DB-Disk3 | Disk3 | F:\ | ユーザー データ 2

Disk1 (D:) は除外されているため、一覧の利用可能な最初のドライブ文字は D: となります。 一時的な記憶域のボリュームには、Azure によって D: が割り当てられます。 Azure 仮想マシンで D: を使用できるため、仮想マシンのページング ファイルの設定は変わりません。

Azure 仮想マシンにおけるページング ファイルの設定を次に示します。

![Azure 仮想マシンにおけるページング ファイルの設定](./media/hyper-v-exclude-disk/pagefile-on-Azure-vm-after-failover.png)

### <a name="case-2-the-paging-file-is-configured-on-another-drive-other-than-d-drive"></a>ケース 2:ページング ファイルが他のドライブ (D: 以外のドライブ) 上で構成されている

ソース仮想マシンのディスク構成を次に示します。

**ディスク名** | **ゲスト オペレーティング システム ディスク番号** | **ドライブ文字** | **ディスクに格納されているデータの種類**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | オペレーティング システム ディスク
DB-Disk1 (保護対象から除外するディスク) | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | ユーザー データ 1
DB-Disk3 | Disk3 | F:\ | ユーザー データ 2

オンプレミスの仮想マシンにおけるページング ファイルの設定を次に示します。

![オンプレミスの仮想マシンにおけるページング ファイルの設定](./media/hyper-v-exclude-disk/pagefile-on-g-drive-sourceVM.png)

Hyper-V から Azure に仮想マシンをフェールオーバーした後の Azure 仮想マシンのディスク構成を次に示します。

**ディスク名** | **ゲスト オペレーティング システム ディスク番号** | **ドライブ文字** | **ディスクに格納されているデータの種類**
--- | --- | --- | ---
DB-Disk0-OS | DISK0  |C:\ |オペレーティング システム ディスク
DB-Disk1 | Disk1 | D:\ | 一時ストレージ<br /> <br />pagefile.sys
DB-Disk2 | Disk2 | E:\ | ユーザー データ 1
DB-Disk3 | Disk3 | F:\ | ユーザー データ 2

利用可能な最初のドライブ文字は D: であるため、一時記憶域ボリュームには Azure によって D: が割り当てられます。 レプリケート対象のディスクについては、いずれもドライブ文字は変化しません。 G: ディスクは利用できないため、ページ ファイルには C: ドライブが使用されます。

Azure 仮想マシンにおけるページング ファイルの設定を次に示します。

![Azure 仮想マシンにおけるページング ファイルの設定](./media/hyper-v-exclude-disk/pagefile-on-Azure-vm-after-failover-2.png)

## <a name="next-steps"></a>次の手順
デプロイをセットアップし、実行状態にできたら、各種フェールオーバーの [詳細を確認](site-recovery-failover.md) します。
