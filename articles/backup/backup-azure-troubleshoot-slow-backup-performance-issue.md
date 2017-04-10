---
title: "Azure Backup でファイルやフォルダーのバックアップが遅い場合のトラブルシューティング | Microsoft Docs"
description: "Azure Backup のパフォーマンスに関する問題の原因を診断するのに役立つトラブルシューティングの指針を示します。"
services: backup
documentationcenter: 
author: genlin
manager: jimpark
editor: 
ms.assetid: e379180a-db13-4e0c-90e4-28e5dd6f5b14
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: ac68ae449a03b6d9e77e6093531a47c905654f16


---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Azure Backup でファイルとフォルダーのバックアップが遅い場合のトラブルシューティング
この記事では、ファイルとフォルダーのバックアップに関して、Azure Backup の使用中にパフォーマンスが低下している原因を診断するためのトラブルシューティングの指針を紹介します。 Azure Backup エージェントを使用してファイルをバックアップするとき、予想以上にバックアップ処理に時間がかかる場合があります。 次のいずれかまたは複数の事柄が、この遅延の原因として考えられます。

* [バックアップ対象コンピューターにパフォーマンスのボトルネックが存在する。](#cause1)
* [別のプロセスまたはウイルス対策ソフトウェアが Azure Backup プロセスの妨げとなっている。](#cause2)
* [Azure 仮想マシン (VM) で Backup エージェントが実行されている。](#cause3)  
* [大量 (数百万) のファイルをバックアップしようとしている。](#cause4)

問題のトラブルシューティングを開始する前に、 [最新の Azure Backup エージェント](http://aka.ms/azurebackup_agent)をダウンロードしてインストールすることをお勧めします。 Backup エージェントは、さまざまな問題の解決、機能の追加、パフォーマンスの改善を目的として絶えず更新されています。

また、一般的な構成の問題ではないことを確認するために、「 [Azure Backup サービス - FAQ](backup-azure-backup-faq.md) 」に目を通すよう強くお勧めします。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>原因: コンピューターのパフォーマンス ボトルネック
バックアップ対象コンピューターにパフォーマンスのボトルネックが存在すると、遅延が生じるおそれがあります。 たとえば、コンピューターのディスク読み取り/書き込み能力や、ネットワーク経由のデータ送信に使用できる帯域幅は、ボトルネックの原因となる可能性があります。

Windows には、これらのボトルネックを検出するための、 [パフォーマンス モニター](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Perfmon) という組み込みのツールが用意されています。

バックアップを最適化するために、パフォーマンス カウンターとボトルネック診断の目安となる範囲を以下に示します。

| カウンター | 状態 |
| --- | --- |
| Logical Disk(Physical Disk)--%idle |• 100 ～ 50% アイドル = 正常</br>• 49 ～ 20% アイドル = 警告または監視</br>• 19 ～ 0% アイドル = 重大または基準不適合 |
| Logical Disk(Physical Disk)--%Avg. Disk Sec Read or Write |• 0.001 ～ 0.015 ミリ秒 = 正常</br>• 0.015 ～ 0.025 ミリ秒 = 警告または監視</br>• 0.026 ミリ秒以上 = 重大または基準不適合 |
| Logical Disk(Physical Disk)--Current Disk Queue Length (全インスタンス) |要求数が 80 件の状態が 6 分超 |
| Memory--Pool Non Paged Bytes |• プールの 60% 未満を消費 = 正常<br>• プールの 61 ～ 80% を消費 = 警告または監視</br>• プールの 80% 超を消費 = 重大または基準不適合 |
| Memory--Pool Paged Bytes |• プールの 60% 未満を消費 = 正常</br>• プールの 61 ～ 80% を消費 = 警告または監視</br>• プールの 80% 超を消費 = 重大または基準不適合 |
| Memory--Available Megabytes |• 空きメモリ 50% 以上 = 正常</br>• 空きメモリ 25% = 監視</br>• 空きメモリ 10% = 警告</br>• 空きメモリ 100 MB または 5% 未満 = 重大または基準不適合 |
| Processor--\%Processor Time (全インスタンス) |• 60% 未満を消費 = 正常</br>• 61 ～ 90% を消費 = 監視または注意</br>• 91 ～ 100% を消費 = 重大 |

> [!NOTE]
> インフラストラクチャが原因であることがわかった場合は、パフォーマンス向上のために定期的にディスクを最適化することをお勧めします。
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>原因: Azure Backup の妨げとなっている別のプロセスまたはウイルス対策ソフトウェア
Windows システム内の他のプロセスが Azure Backup エージェントのプロセスのパフォーマンスに悪影響を及ぼすいくつかのケースが確認されています。 たとえば Azure Backup エージェントと別のプログラムを両方使用してデータをバックアップしている場合や、ウイルス対策ソフトウェアが実行されていてバックアップ対象ファイルがロックされている場合、ファイルに対する複数のロックが競合している可能性があります。 この状況ではバックアップが失敗するか、ジョブに予想以上の時間がかかる可能性があります。

この状況で最も推奨される対策は、他のバックアップ プログラムを無効にして、Azure Backup エージェントのバックアップ時間が変化するかどうかを確認することです。 通常、複数のバックアップ ジョブが同時に実行されないようにすることで、互いに干渉する状況を十分回避できます。

ウイルス対策プログラムの場合は、次のファイルと場所を除外するようお勧めします。

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe (プロセスとして除外)
* C:\Program Files\Microsoft Azure Recovery Services Agent\ のフォルダー
* スクラッチ場所 (標準的な場所を使用していない場合)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>原因: Azure 仮想マシンで実行されている Backup エージェント
Backup エージェントを VM で実行している場合、パフォーマンスは物理マシンで実行した場合よりも低くなります。 これは IOPS の制限によるものと考えられます。  ただし、バックアップ対象のデータ ドライブを Azure Premium Storage に切り替えることによってパフォーマンスを最適化できます。 Microsoft は現在この問題の解決に取り組んでおり、将来のリリースで修正プログラムが公開される予定です。

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>原因: 大量 (数百万) のファイルのバックアップ
少量のデータを移動するよりも大量のデータを移動する方が時間がかかります。 場合によっては、データのサイズだけでなくファイルやフォルダーの数が、バックアップにかかる時間に関係します。 これは特に、数百万個の小さなファイル (数バイトから数キロバイト) がバックアップされている場合に当てはまります。

この動作が発生するのは、データをバックアップして Azure にそのデータを移行している間に、Azure によるファイルのカタログ化が並行して行われているためです。 まれなシナリオではあるものの、カタログ化の処理には予想以上の時間がかかる場合があります。

ボトルネックを把握し、それに応じた対策を講じるのに役立つ指標を次に示します。

* **データ転送の進行状況が UI に表示されている**。 データは依然として転送中です。 ネットワーク帯域幅またはデータ サイズが原因で、遅延が生じている可能性があります。
* **データ転送の進行状況が UI に表示されていない**。 C:\Microsoft Azure Recovery Services Agent\Temp にあるログを開き、FileProvider::EndData というログ エントリが記録されているかどうかを確認します。 このエントリは、データ転送が完了し、カタログ化の処理が進行中であることを表します。 バックアップ ジョブを取り消さないようにしてください。 そのまま、カタログ化の処理が完了するまでしばらく待ちます。 問題が解決しない場合は、 [Azure サポート](https://portal.azure.com/#create/Microsoft.Support)にお問い合わせください。



<!--HONumber=Nov16_HO3-->


