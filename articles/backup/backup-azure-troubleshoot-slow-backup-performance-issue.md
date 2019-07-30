---
title: Azure Backup でファイルとフォルダーのバックアップが遅い場合のトラブルシューティング
description: Azure Backup のパフォーマンスに関する問題の原因を診断するのに役立つトラブルシューティングの指針を示します。
author: saurabhsensharma
manager: saurabhsensharma
ms.service: backup
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.author: saurse
ms.openlocfilehash: 3a39d39412c8b64d1851ea0fc9511d116f3b232a
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465342"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Azure Backup でファイルとフォルダーのバックアップが遅い場合のトラブルシューティング
この記事では、ファイルとフォルダーのバックアップに関して、Azure Backup の使用中にパフォーマンスが低下している原因を診断するためのトラブルシューティングの指針を紹介します。 Azure Backup エージェントを使用してファイルをバックアップするとき、予想以上にバックアップ処理に時間がかかる場合があります。 次のいずれかまたは複数の事柄が、この遅延の原因として考えられます。

* [バックアップ対象コンピューターにパフォーマンスのボトルネックが存在する。](#cause1)
* [別のプロセスまたはウイルス対策ソフトウェアが Azure Backup プロセスの妨げとなっている。](#cause2)
* [Azure 仮想マシン (VM) で Backup エージェントが実行されている。](#cause3)  
* [大量 (数百万) のファイルをバックアップしようとしている。](#cause4)

問題のトラブルシューティングを開始する前に、 [最新の Azure Backup エージェント](https://aka.ms/azurebackup_agent)をダウンロードしてインストールすることをお勧めします。 Backup エージェントは、さまざまな問題の解決、機能の追加、パフォーマンスの改善を目的として絶えず更新されています。

また、一般的な構成の問題ではないことを確認するために、「 [Azure Backup サービス - FAQ](backup-azure-backup-faq.md) 」に目を通すよう強くお勧めします。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>原因: コンピューター上のパフォーマンス ボトルネック
バックアップ対象コンピューターにパフォーマンスのボトルネックが存在すると、遅延が生じるおそれがあります。 たとえば、コンピューターのディスク読み取り/書き込み能力や、ネットワーク経由のデータ送信に使用できる帯域幅は、ボトルネックの原因となる可能性があります。

Windows には、これらのボトルネックを検出するための、 [パフォーマンス モニター](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Perfmon) という組み込みのツールが用意されています。

バックアップを最適化するために、パフォーマンス カウンターとボトルネック診断の目安となる範囲を以下に示します。

| カウンター | Status |
| --- | --- |
| Logical Disk(Physical Disk)--%idle |• 100 ～ 50% アイドル = 正常</br>• 49 ～ 20% アイドル = 警告または監視</br>• 19 ～ 0% アイドル = 重大または基準不適合 |
| Logical Disk(Physical Disk)--%Avg.Disk Sec Read or Write |• 0.001 ～ 0.015 ミリ秒 = 正常</br>• 0.015 ～ 0.025 ミリ秒 = 警告または監視</br>• 0.026 ミリ秒以上 = 重大または基準不適合 |
| Logical Disk(Physical Disk)--Current Disk Queue Length (全インスタンス) |要求数が 80 件の状態が 6 分超 |
| Memory--Pool Non Paged Bytes |• プールの 60% 未満を消費 = 正常<br>• プールの 61 ～ 80% を消費 = 警告または監視</br>• プールの 80% 超を消費 = 重大または基準不適合 |
| Memory--Pool Paged Bytes |• プールの 60% 未満を消費 = 正常</br>• プールの 61 ～ 80% を消費 = 警告または監視</br>• プールの 80% 超を消費 = 重大または基準不適合 |
| Memory--Available Megabytes |• 空きメモリ 50% 以上 = 正常</br>• 空きメモリ 25% = 監視</br>• 空きメモリ 10% = 警告</br>• 空きメモリ 100 MB または 5% 未満 = 重大または基準不適合 |
| Processor--\%Processor Time (all instances) |• Less than 60% consumed = Healthy</br>• 61% to 90% consumed = Monitor or Caution</br>• 91% to 100% consumed = Critical |

> [!NOTE]
> If you determine that the infrastructure is the culprit, we recommend that you defragment the disks regularly for better performance.
>
>

<a id="cause2"></a>

## Cause: Another process or antivirus software interfering with Azure Backup
We've seen several instances where other processes in the Windows system have negatively affected performance of the Azure Backup agent process. For example, if you use both the Azure Backup agent and another program to back up data, or if antivirus software is running and has a lock on files to be backed up, the multiple locks on files might cause contention. In this situation, the backup might fail, or the job might take longer than expected.

The best recommendation in this scenario is to turn off the other backup program to see whether the backup time for the Azure Backup agent changes. Usually, making sure that multiple backup jobs are not running at the same time is sufficient to prevent them from affecting each other.

For antivirus programs, we recommend that you exclude the following files and locations:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe as a process
* C:\Program Files\Microsoft Azure Recovery Services Agent\ folders
* Scratch location (if you're not using the standard location)

<a id="cause3"></a>

## Cause: Backup agent running on an Azure virtual machine
If you're running the Backup agent on a VM, performance will be slower than when you run it on a physical machine. This is expected due to IOPS limitations.  However, you can optimize the performance by switching the data drives that are being backed up to Azure Premium Storage. We're working on fixing this issue, and the fix will be available in a future release.

<a id="cause4"></a>

## Cause: Backing up a large number (millions) of files
Moving a large volume of data will take longer than moving a smaller volume of data. In some cases, backup time is related to not only the size of the data, but also the number of files or folders. This is especially true when millions of small files (a few bytes to a few kilobytes) are being backed up.

This behavior occurs because while you're backing up the data and moving it to Azure, Azure is simultaneously cataloging your files. In some rare scenarios, the catalog operation might take longer than expected.

The following indicators can help you understand the bottleneck and accordingly work on the next steps:

* **UI is showing progress for the data transfer**. The data is still being transferred. The network bandwidth or the size of data might be causing delays.
* **UI is not showing progress for the data transfer**. Open the logs located at C:\Program Files\Microsoft Azure Recovery Services Agent\Temp, and then check for the FileProvider::EndData entry in the logs. This entry signifies that the data transfer finished and the catalog operation is happening. Don't cancel the backup jobs. Instead, wait a little longer for the catalog operation to finish. If the problem persists, contact [Azure support](https://portal.azure.com/#create/Microsoft.Support).Processor--\`Processor Time (全インスタンス)es and folders in Azure Backup
description: Provides troubleshooting guidance to help you diagnose the cause of Azure Backup performance issues

author: saurabhsensharma
manager: saurabhsensharma
ms.service: backup
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.author: saurse
---
# Troubleshoot slow backup of files and folders in Azure Backup
This article provides troubleshooting guidance to help you diagnose the cause of slow backup performance for files and folders when you're using Azure Backup. When you use the Azure Backup agent to back up files, the backup process might take longer than expected. This delay might be caused by one or more of the following:

* [There are performance bottlenecks on the computer that’s being backed up.](#cause1)
* [Another process or antivirus software is interfering with the Azure Backup process.](#cause2)
* [The Backup agent is running on an Azure virtual machine (VM).](#cause3)  
* [You're backing up a large number (millions) of files.](#cause4)

Before you start troubleshooting issues, we recommend that you download and install the [latest Azure Backup agent](https://aka.ms/azurebackup_agent). We make frequent
updates to the Backup agent to fix various issues, add features, and improve performance.

We also strongly recommend that you review the [Azure Backup service FAQ](backup-azure-backup-faq.md) to make sure you're not experiencing any of the common configuration issues.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## Cause: Performance bottlenecks on the computer
Bottlenecks on the computer that's being backed up can cause delays. For example, the computer's ability to read or write to disk, or available bandwidth to send data over the network, can cause bottlenecks.

Windows provides a built-in tool that's called [Performance Monitor](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Perfmon) to detect these bottlenecks.

Here are some performance counters and ranges that can be helpful in diagnosing bottlenecks for optimal backups.

| Counter | Status |
| --- | --- |
| Logical Disk(Physical Disk)--%idle |• 100% idle to 50% idle = Healthy</br>• 49% idle to 20% idle = Warning or Monitor</br>• 19% idle to 0% idle = Critical or Out of Spec |
| Logical Disk(Physical Disk)--%Avg. Disk Sec Read or Write |• 0.001 ms to 0.015 ms  = Healthy</br>• 0.015 ms to 0.025 ms = Warning or Monitor</br>• 0.026 ms or longer = Critical or Out of Spec |
| Logical Disk(Physical Disk)--Current Disk Queue Length (for all instances) |80 requests for more than 6 minutes |
| Memory--Pool Non Paged Bytes |• Less than 60% of pool consumed = Healthy<br>• 61% to 80% of pool consumed = Warning or Monitor</br>• Greater than 80% pool consumed = Critical or Out of Spec |
| Memory--Pool Paged Bytes |• Less than 60% of pool consumed = Healthy</br>• 61% to 80% of pool consumed = Warning or Monitor</br>• Greater than 80% pool consumed = Critical or Out of Spec |
| Memory--Available Megabytes |• 50% of free memory available or more = Healthy</br>• 25% of free memory available = Monitor</br>• 10% of free memory available = Warning</br>• Less than 100 MB or 5% of free memory available = Critical or Out of Spec |
| Processor--\%Processor Time (all instances) |• 60% 未満を消費 = 正常</br>• 61 ～ 90% を消費 = 監視または注意</br>• 91 ～ 100% を消費 = 重大 |

> [!NOTE]
> インフラストラクチャが原因であることがわかった場合は、パフォーマンス向上のために定期的にディスクを最適化することをお勧めします。
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>原因: Azure Backup の妨げになっている別のプロセスまたはウイルス対策ソフトウェア
Windows システム内の他のプロセスが Azure Backup エージェントのプロセスのパフォーマンスに悪影響を及ぼすいくつかのケースが確認されています。 たとえば Azure Backup エージェントと別のプログラムを両方使用してデータをバックアップしている場合や、ウイルス対策ソフトウェアが実行されていてバックアップ対象ファイルがロックされている場合、ファイルに対する複数のロックが競合している可能性があります。 この状況ではバックアップが失敗するか、ジョブに予想以上の時間がかかる可能性があります。

この状況で最も推奨される対策は、他のバックアップ プログラムを無効にして、Azure Backup エージェントのバックアップ時間が変化するかどうかを確認することです。 通常、複数のバックアップ ジョブが同時に実行されないようにすることで、互いに干渉する状況を十分回避できます。

ウイルス対策プログラムの場合は、次のファイルと場所を除外するようお勧めします。

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe (プロセスとして除外)
* C:\Program Files\Microsoft Azure Recovery Services Agent\ のフォルダー
* スクラッチ場所 (標準的な場所を使用していない場合)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>原因: Azure 仮想マシン上で実行されている Backup エージェント
Backup エージェントを VM で実行している場合、パフォーマンスは物理マシンで実行した場合よりも低くなります。 これは IOPS の制限によるものと考えられます。  ただし、バックアップ対象のデータ ドライブを Azure Premium Storage に切り替えることによってパフォーマンスを最適化できます。 Microsoft は現在この問題の解決に取り組んでおり、将来のリリースで修正プログラムが公開される予定です。

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>原因: 大量 (数百万) のファイルのバックアップ
少量のデータを移動するよりも大量のデータを移動する方が時間がかかります。 場合によっては、データのサイズだけでなくファイルやフォルダーの数が、バックアップにかかる時間に関係します。 これは特に、数百万個の小さなファイル (数バイトから数キロバイト) がバックアップされている場合に当てはまります。

この動作が発生するのは、データをバックアップして Azure にそのデータを移行している間に、Azure によるファイルのカタログ化が並行して行われているためです。 まれなシナリオではあるものの、カタログ化の処理には予想以上の時間がかかる場合があります。

ボトルネックを把握し、それに応じた対策を講じるのに役立つ指標を次に示します。

* **データ転送の進行状況が UI に表示されている**。 データは依然として転送中です。 ネットワーク帯域幅またはデータ サイズが原因で、遅延が生じている可能性があります。
* **データ転送の進行状況が UI に表示されていない**。 C:\Program Files\Microsoft Azure Recovery Services Agent\Temp にあるログを開き、ログ内に FileProvider::EndData エントリがあるかどうかを確認します。 このエントリは、データ転送が完了し、カタログ化の処理が進行中であることを表します。 バックアップ ジョブを取り消さないようにしてください。 そのまま、カタログ化の処理が完了するまでしばらく待ちます。 問題が解決しない場合は、 [Azure サポート](https://portal.azure.com/#create/Microsoft.Support)にお問い合わせください。
