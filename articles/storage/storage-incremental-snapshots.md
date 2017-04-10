---
title: "増分スナップショットを使用した非管理 Azure VM ディスクのバックアップと復元 | Microsoft Docs"
description: "増分スナップショットを使用して Azure 仮想マシン ディスクのバックアップと復元を行うカスタム ソリューションを作成します。"
services: storage
documentationcenter: na
author: aungoo-msft
manager: tadb
editor: tysonn
ms.assetid: 3524b987-bd65-4e35-83e7-fbc2136643e5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: aungoo
translationtype: Human Translation
ms.sourcegitcommit: bfb29928783283336b6116f198d70b0b7117750d
ms.openlocfilehash: 880b1c2ca069e3e4a5ce9cb2af881b95e84687f4
ms.lasthandoff: 02/08/2017


---
# <a name="back-up-azure-unmanaged-vm-disks-with-incremental-snapshots"></a>増分スナップショットを使用した Azure 非管理 VM ディスクのバックアップ
## <a name="overview"></a>概要
Azure Storage には、BLOB のスナップショットを作成する機能があります。 スナップショットは、特定の時点における BLOB の状態をキャプチャします。 この記事では、スナップショットを使って仮想マシン ディスクのバックアップを保存する方法について説明します。 この手法は、Azure Backup と Recovery Service を使用せず、仮想マシン ディスク用に独自のバックアップ方法を構築する必要がある場合にご利用ください。

Azure Storage には、Azure 仮想マシン ディスクがページ BLOB として格納されます。 この記事のテーマは仮想マシン ディスクのバックアップ方法であるため、スナップショットという言葉は、ページ BLOB のコンテキストにおける意味合いを指しています。 スナップショットの詳細については、「 [BLOB のスナップショットの作成](https://msdn.microsoft.com/library/azure/hh488361.aspx)」を参照してください。

## <a name="what-is-a-snapshot"></a>スナップショットとは
BLOB のスナップショットとは、ある時点でキャプチャされた BLOB の読み取り専用版です。 作成されたスナップショットは、読み取り、コピー、削除はできますが、変更はできません。 スナップショットを使用すると、BLOB をある時点での表示内容のままバックアップできます。 REST バージョン 2015-04-05 までは、スナップショット全体をコピーすることができました。 REST バージョン 2015-07-08 以降では、増分スナップショットをコピーすることもできます。

## <a name="full-snapshot-copy"></a>スナップショット全体のコピー
スナップショットは別のストレージ アカウントに BLOB としてコピーすることができます。こうすることでスナップショットの作成元となった BLOB (ベース BLOB) のバックアップを保存できます。 逆に、スナップショットをそのベース BLOB に上書きコピーして、以前のバージョンの BLOB を復元することもできます。 2 つのストレージ アカウント間でスナップショットをコピーすると、そのスナップショットの作成元となったページ BLOB と同じだけの記憶域が消費されます。 したがって&2; つのストレージ アカウント間でスナップショット全体をコピーした場合、時間がかかるうえに、コピー先のストレージ アカウントで大量の領域が消費されることになります。

> [!NOTE]
> ベース BLOB を別の場所にコピーしても、その BLOB のスナップショットまではコピーされません。 同様に、ベース BLOB に上書きコピーしても、そのベース BLOB に関連付けられているスナップショットは影響を受けず、元のベース BLOB 由来として維持されます。
> 
> 

### <a name="back-up-disks-using-snapshots"></a>スナップショットを使用したディスクのバックアップ
仮想マシン ディスクのバックアップを行う一つの方法として、ディスクやページ BLOB のスナップショットを定期的に作成し、[Copy Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) 操作や [AzCopy](storage-use-azcopy.md) などのツールを使ってそれらを別のストレージ アカウントにコピーする手法が考えられます。 コピー先のページ BLOB には、スナップショットを別の名前でコピーできます。 このときコピー先に生成されるのは、書き込み可能なページ BLOB であってスナップショットではありません。 スナップショットを使って仮想マシン ディスクのバックアップを作成する手順については、この記事の後半で説明します。

### <a name="restore-disks-using-snapshots"></a>スナップショットを使用したディスクの復元
以前バックアップ スナップショットとしてキャプチャした安定した状態のディスクを復元する必要が生じた場合は、スナップショットをそのベースとなったページ BLOB に上書きコピーします。 ベース ページ BLOB に昇格された後も、そのスナップショットは同じ状態で保たれますが、その作成元は、読み取りと書き込みが可能なコピーによって上書きされます。 以前のバージョンのディスクをそのスナップショットから復元する手順については、この記事の後半で説明します。

### <a name="implementing-full-snapshot-copy"></a>スナップショット全体のコピーの実行
スナップショット全体のコピーは、次の手順で実行できます。

* まず、 [Snapshot Blob](https://msdn.microsoft.com/library/azure/ee691971.aspx) 操作を使用してベース BLOB のスナップショットを作成します。
* 次に、 [Copy Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx)を使って目的のストレージ アカウントにスナップショットをコピーします。
* ベース BLOB のバックアップ コピーを維持するには、このプロセスを繰り返します。

## <a name="incremental-snapshot-copy"></a>増分スナップショットのコピー
[GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx) API の新機能により、従来よりも効率良くページ BLOB やディスクのスナップショットをバックアップできるようになりました。 この API は、ベース BLOB とスナップショットとを比較して得られる変更のリストを返します。 これによってバックアップ アカウントで使用される記憶域の量を減らすことができます。 この API は、Premium Storage だけでなく Standard Storage 上のページ BLOB もサポートします。 この API を使用することで、従来よりも高速に効率よく Azure VM をバックアップできるソリューションを構築できます。 この API が利用できるのは、REST バージョン 2015-07-08 以上となります。

Incremental Snapshot Copy を使用すると、以下の差分をストレージ アカウント間でコピーできます。

* ベース BLOB とそのスナップショット
* 同じベース BLOB から作成された&2; つのスナップショット

このとき、次の条件を満たしている必要があります。

* BLOB が&2016; 年&1; 月&1; 日以降に作成されている。
* 2 つのスナップショット間で [PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) または [Copy Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) により BLOB が上書きされていない。

**注**: この機能は、Premium と Standard の Azure ページ BLOB に対して利用できます。

スナップショットを使用した独自のバックアップ方法を利用している場合、スナップショットを&2; つのストレージ アカウント間でコピーすると、ひどく時間がかかるうえに記憶領域が大量に消費されます。 バックアップ ページ BLOB には、連続するスナップショットの差分だけを書き込むことができます。そうすれば、バックアップ ストレージ アカウントにスナップショット全体をコピーする必要はありません。 そうすることでコピーにかかる時間とバックアップで消費される記憶域を大幅に減らすことができます。

### <a name="implementing-incremental-snapshot-copy"></a>増分スナップショットのコピーの実行
増分スナップショットのコピーは、次の手順で実行できます。

* [Snapshot Blob](https://msdn.microsoft.com/library/azure/ee691971.aspx)を使用してベース BLOB のスナップショットを作成します。
* [Copy Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx)を使って目的のバックアップ ストレージ アカウントにスナップショットをコピーします。 これが、バックアップ ページ BLOB になります。 このバックアップ ページ BLOB のスナップショットを作成し、バックアップ アカウントに格納します。
* Snapshot Blob を使用してベース BLOB の別のスナップショットを作成します。
* [GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx) を使用して、ベース BLOB の最初のスナップショットと&2; つ目のスナップショットの差分を取得します。 新しいパラメーター **prevsnapshot** を使用して、差分の取得対象となるスナップショットの DateTime 値を指定します。 このパラメーターが存在する場合、REST 応答には、対象のスナップショットと以前のスナップショットとの間で変更 (ページの消去を含む) されたページだけが含まれます。
* [PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) を使用してこれらの変更をバックアップ ページ BLOB に適用します。
* 最後に、バックアップ ページ BLOB のスナップショットを作成し、バックアップ ストレージ アカウントに格納します。

Incremental Snapshot Copy を使用してディスクのバックアップを保持する方法については、次のセクションで詳しく説明します。

## <a name="scenario"></a>シナリオ
このセクションでは、スナップショットを使った独自の方法で仮想マシン ディスクをバックアップするシナリオを取り上げます。

Premium Storage P30 ディスクが接続された DS シリーズの Azure VM について考えてみましょう。 この P30 ディスク (*mypremiumdisk*) の格納先は Premium Storage アカウント (*mypremiumaccount*) です。 *mypremiumdisk* のバックアップには、Standard Storage アカウント (*mybackupstdaccount*) が使用されます。 12 時間ごとの *mypremiumdisk* のスナップショットを維持したいと考えています。

ストレージ アカウントとディスクの作成について詳しくは、「 [Azure ストレージ アカウントについて](storage-create-storage-account.md)」を参照してください。

Azure VM のバックアップについて詳しくは、 [Azure での VM バックアップの計画](../backup/backup-azure-vms-introduction.md)に関する記事を参照してください。

## <a name="steps-to-maintain-backups-of-a-disk-using-incremental-snapshots"></a>増分スナップショットを使用してディスクのバックアップを保存する手順
以下の手順は、*mypremiumdisk* のスナップショットを作成して、*mybackupstdaccount* にバックアップを保存する方法を説明しています。 バックアップは、 *mybackupstdpageblob*という標準的なページ BLOB です。 バックアップ ページ BLOB は常に *mypremiumdisk*の最後のスナップショットと同じ状態を反映します。

1. 最初に、Premium Storage ディスクのバックアップ ページ BLOB を作成します。 これを行うには、*mypremiumdisk* のスナップショットを作成します (*mypremiumdisk_ss1*)。
2. このスナップショットを mybackupstdaccount に、ページ BLOB ( *mybackupstdpageblob*) としてコピーします。
3. [Snapshot Blob](https://msdn.microsoft.com/library/azure/ee691971.aspx) を使用して、*mybackupstdpageblob* のスナップショットを *mybackupstdpageblob_ss1* という名前で作成し、*mybackupstdaccount* に保存します。
4. バックアップの時間帯に、*mypremiumdisk* の別のスナップショット (例: *mypremiumdisk_ss2*) を作成して *mypremiumaccount* に保存します。
5. *mypremiumdisk_ss2* で [GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx) を使用して、2 つのスナップショット *mypremiumdisk_ss2* と *mypremiumdisk_ss1* の変更の増分を取得します。このとき、**prevsnapshot** パラメーターは *mypremiumdisk_ss1* のタイムスタンプに設定します。 これらの変更の増分を *mybackupstdaccount* のバックアップ ページ BLOB (*mybackupstdpageblob*) に書き込みます。 削除された範囲が増分に含まれている場合、バックアップ ページ BLOB からそれらを消去する必要があります。 [PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) を使用して、変更の増分をバックアップ ページ BLOB に書き込みます。
6. バックアップ ページ BLOB (*mybackupstdpageblob*) のスナップショットを *mybackupstdpageblob_ss2* という名前で作成します。 Premium Storage アカウントから以前のスナップショット *mypremiumdisk_ss1* を削除します。
7. バックアップの時間帯に毎回、この手順 4. ～ 手順 6. を繰り返します。 このようにして、*mypremiumdisk* のバックアップを Standard Storage アカウントに保存することができます。

![増分スナップショットを使用したバックアップ ディスク](./media/storage-incremental-snapshots/storage-incremental-snapshots-1.png)

## <a name="steps-to-restore-a-disk-from-snapshots"></a>スナップショットからディスクを復元する手順
以下の手順は、Premium ディスク *mypremiumdisk* を、バックアップ ストレージ アカウント *mybackupstdaccount* に保存されている以前のスナップショットに復元する方法を説明しています。

1. Premium ディスクをどの時点まで復元するかを決めます。 たとえば *mybackupstdpageblob_ss2* を復元するとしましょう。このスナップショットは、バックアップ ストレージ アカウント *mybackupstdaccount* に保存されています。
2. mybackupstdaccount で、スナップショット *mybackupstdpageblob_ss2* を *mybackupstdpageblobrestored* という名前の新しいバックアップ ベース ページ BLOB として昇格させます。
3. この復元されたバックアップ ページ BLOB のスナップショットを *mybackupstdpageblobrestored_ss1* という名前で作成します。
4. *mybackupstdaccount* から、復元されたベース ページ BLOB (*mybackupstdpageblobrestored*) を新しい Premium ディスク *mypremiumdiskrestored* として *mypremiumaccount* にコピーします。
5. *mypremiumdiskrestored* のスナップショットを *mypremiumdiskrestored_ss1* という名前で作成し、将来の増分バックアップを作成します。
6. DS シリーズ VM の接続先ディスクを *mypremiumdiskrestored* (復元済みのディスク) に切り替えて、以前の *mypremiumdisk* は VM から切断します。
7. 復元済みのディスク *mypremiumdiskrestored* に対して前セクションで説明したバックアップ プロセスを開始します。バックアップ ページ BLOB には *mybackupstdpageblobrestored* を使用します。

![Restore disk from snapshots](./media/storage-incremental-snapshots/storage-incremental-snapshots-2.png)

## <a name="next-steps"></a>次のステップ
以下のリンクでは、BLOB のスナップショットの作成と、VM のバックアップ インフラストラクチャの計画について説明します。

* [BLOB のスナップショットの作成](https://msdn.microsoft.com/library/azure/hh488361.aspx)
* [VM のバックアップ インフラストラクチャの計画](../backup/backup-azure-vms-introduction.md)


