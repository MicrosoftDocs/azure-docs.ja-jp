---
title: ファイルとフォルダーのバックアップが遅い場合のトラブルシューティング
description: Azure Backup のパフォーマンスに関する問題の原因を診断するのに役立つトラブルシューティングの指針を示します。
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: b3f2ac343ef4a703f347ec8a57f242a636bb32d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88824017"
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

## <a name="cause-backup-job-running-in-unoptimized-mode"></a>原因: 最適化されないモードでバックアップ ジョブが実行されている

* MARS エージェントでは、USN (更新シーケンス番号) の変更ジャーナルを使用して **最適化されたモード** でバックアップ ジョブを実行できます。また、ボリューム全体をスキャンして、ディレクトリまたはファイルの変更を確認すると、**最適化されないモード** で実行されます。
* エージェントでは、ボリューム上のすべてのファイルをスキャンし、メタデータと比較して変更されたファイルを判断する必要があるため、最適化されないモードは低速です。
* これを確認するには、MARS エージェント コンソールから **[ジョブの詳細]** を開き、状態を確認し、次のように "**データを転送しています (最適化されていないため、しばらく時間がかかることがあります)** " と表示されるかどうかを確認します。

    ![最適化されないモードで実行中](./media/backup-azure-troubleshoot-slow-backup-performance-issue/unoptimized-mode.png)

* 次の状況では、バックアップ ジョブが最適化されないモードで実行される可能性があります。
  * 最初のバックアップ (初期レプリケーションとも呼ばれます) は、常に最適化されないモードで実行されます。
  * 前のバックアップ ジョブが失敗した場合、次のスケジュールされたバックアップ ジョブは最適化されないモードで実行されます。

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>原因: コンピューター上のパフォーマンス ボトルネック

バックアップ対象コンピューターにパフォーマンスのボトルネックが存在すると、遅延が生じるおそれがあります。 たとえば、コンピューターのディスク読み取り/書き込み能力や、ネットワーク経由のデータ送信に使用できる帯域幅は、ボトルネックの原因となる可能性があります。

Windows には、これらのボトルネックを検出することができる[パフォーマンス モニター](https://techcommunity.microsoft.com/t5/ask-the-performance-team/windows-performance-monitor-overview/ba-p/375481) (Perfmon) という組み込みのツールが用意されています。

バックアップを最適化するために、パフォーマンス カウンターとボトルネック診断の目安となる範囲を以下に示します。

| カウンター | Status |
| --- | --- |
| Logical Disk(Physical Disk)--%idle |<li> 100% から 50% アイドル = 正常</br><li> 49% から 20% アイドル = 警告または監視</br><li> 19% から 0% アイドル = 重大または基準不適合 |
| Logical Disk(Physical Disk)--%Avg.Disk Sec Read or Write |<li> 0.001 から 0.015 ミリ秒 = 正常</br><li> 0.015 から 0.025 ミリ秒 = 警告または監視</br><li> 0.026 ミリ秒以上 = 重大または基準不適合 |
| Logical Disk(Physical Disk)--Current Disk Queue Length (全インスタンス) |要求数が 80 件の状態が 6 分超 |
| Memory--Pool Non Paged Bytes |<li> プールの 60% 未満を消費 = 正常<br><li> プールの 61% から 80% を消費 = 警告または監視</br><li> プールの 80% 超を消費 = 重大または基準不適合 |
| Memory--Pool Paged Bytes |<li> プールの 60% 未満を消費 = 正常</br><li> プールの 61% から 80% を消費 = 警告または監視</br><li> プールの 80% 超を消費 = 重大または基準不適合 |
| Memory--Available Megabytes |<li> 空きメモリ 50% 以上 = 正常</br><li> 空きメモリ 25% = 監視</br><li>空きメモリ 10% = 警告</br><li> 空きメモリ 100 MB または 5% 未満 = 重大または基準不適合 |
| Processor--\%Processor Time (全インスタンス) |<li> 60% 未満を消費 = 正常</br><li> 61% から 90% を消費 = 監視または注意</br><li> 91% から 100% を消費 = 重大 |

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

大容量ディスクをバックアップする場合は、最初のバックアップ (初期レプリケーション) に [Azure Data Box](./offline-backup-azure-data-box.md) を使用することをお勧めします。  Data Box を使用できない場合、ネットワーク経由での長いデータ転送の間に環境内で一時的なネットワークの問題が発生すると、バックアップが失敗する可能性があります。  このような障害から保護するために、最初のバックアップにいくつかのフォルダーを追加し、すべてのフォルダーが Azure に正常にバックアップされるまで、フォルダーを段階的に追加することができます。  それ以降の増分バックアップは比較的高速になります。

## <a name="next-steps"></a>次のステップ

* [ファイルとフォルダーのバックアップに関する一般的な質問](backup-azure-file-folder-backup-faq.md)
