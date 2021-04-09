---
title: Microsoft Azure Automation のケースを開くときに収集するデータ | Microsoft Docs
description: この記事では、Microsoft Azure サポートで Azure Automation のケースを開く前に収集する情報について説明します。
services: automation
ms.subservice: ''
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.openlocfilehash: 3d2c0cf780b992d7bb464969c50d60ffa5093e8f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896564"
---
# <a name="data-to-collect-when-opening-a-case-for-microsoft-azure-automation"></a>Microsoft Azure Automation のケースを開くときに収集するデータ

この記事では、Microsoft Azure サポートで Azure Automation のケースを開く前に収集する必要のある情報について説明します。 この情報は、ケースを開くためには必要ありません。 ただし、Microsoft が問題を迅速に解決するのに役立ちます。 また、ケースを開いた後にサポート エンジニアがこのデータを要求する場合もあります。

## <a name="basic-data"></a>基本的なデータ

サポート技術情報の記事「[4034605 - Azure Automation でスクリプト化された診断をキャプチャする方法](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)」で説明されている基本的なデータを収集してください。

## <a name="data-for-update-management-issues-on-linux"></a>Linux での Update Management の問題に関するデータ

1. KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics) で一覧表示されている項目に加えて、次のログ収集ツールを実行します。

   [OMS Linux Agent Log Collector](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. **/var/opt/microsoft/omsagent/run/automationworker/** フォルダーの内容を圧縮してから、圧縮されたファイルを Azure サポートに送信します。
 
3. Linux 用 Log Analytics エージェントの報告先であるワークスペースの ID が、更新プログラムの監視対象となるワークスペースの ID と同じであることを確認します。

## <a name="data-for-update-management-issues-on-windows"></a>Windows での Update Management の問題に関するデータ

1. [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics) に一覧表示されている項目のデータを収集します。

2. 次のイベントログを EVTX 形式でエクスポートします。

   * システム
   * Application
   * Security
   * Operations Manager
   * Microsoft-SMA/Operational

3. エージェントの報告先であるワークスペースの ID が、Windows 更新プログラムの監視対象のワークスペースの ID と同じであることを確認します。

## <a name="data-for-job-issues"></a>ジョブの問題に関するデータ

1. [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics) に一覧表示されている項目のデータを収集します。

2. 問題が発生しているジョブのジョブ ID を収集します。

   1. Azure portal で、 **[Automation アカウント]** に移動します。
   2. トラブルシューティングを行う Automation アカウントを選択し、その名前をメモします。
   3. **[ジョブ]** を選択します。
   4. トラブルシューティングを行うジョブを選択します。
   5. [ジョブの概要] ウィンドウの **[ジョブ ID]** で GUID 値を探します。

   ![ジョブの概要ウィンドウ内のジョブ ID](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

3. 実行中のスクリプトのサンプルを収集します。

4. ログ ファイルを収集するには、次の手順に従います。

   1. Azure portal で、 **[Automation アカウント]** に移動します。
   2. トラブルシューティングを行う Automation アカウントを選択します。
   3. **[ジョブ]** を選択します。
   4. トラブルシューティングを行うジョブを選択します。
   5. **[すべてのログ]** を選択します。
   6. 結果のウィンドウで、データを収集します。

   ![[すべてのログ] の下に表示されるデータ](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

## <a name="data-for-module-issues"></a>モジュールの問題に関するデータ

[基本的なデータ項目](#basic-data)に加えて、次の情報を収集します。

* 問題を再現するためにたどった手順。
* エラー メッセージのスクリーンショット。
* 現在のモジュールとそのバージョン番号のスクリーンショット。

## <a name="next-steps"></a>次のステップ

さらにサポートが必要な場合:

* [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
* [@AzureSupport](https://twitter.com/azuresupport) (Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐことで、カスタマー エクスペリエンスを向上させる Microsoft Azure の公式アカウント) に問い合わせる。
* Azure サポート インシデントを送信する。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。
