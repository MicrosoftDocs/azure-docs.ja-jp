---
title: Microsoft Azure Automation のケースを開くときに収集するデータ | Microsoft Docs
description: この記事では、Microsoft Azure サポートで Azure Automation のケースを開く前に収集する必要のある基本的な情報について説明します。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.author: v-miegge
ms.openlocfilehash: 4839ce7a0188c782656fd3a4c42cbdd116b165e9
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849379"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>Microsoft Azure Automation のケースを開くときに収集するデータ

この記事では、Microsoft Azure サポートで Azure Automation のケースを開く前に収集する必要のある基本的な情報について説明します。 この情報は、ケースを開くためには必要ありません。 ただし、Microsoft が問題を迅速に解決するのに役立ちます。 また、ケースを開いた後にサポート エンジニアがこのデータを要求する場合もあります。

## <a name="collect-more-information"></a>より多くの情報を収集する

Microsoft Azure Automation サポートのケースを開く前に、次の情報を収集することをお勧めします。

### <a name="basic-data-collection"></a>基本データの収集

次のサポート技術情報の記事で説明されているデータを収集します。

* [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics) Azure Automation でスクリプト化された診断をキャプチャする方法

## <a name="collect-data-depending-on-issue"></a>問題の内容に応じて収集するデータ
 
### <a name="for-issues-that-affect-update-management-on-linux"></a>Linux での Update Management に影響する問題の場合

1. KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics) で一覧表示されている項目に加えて、次のログ収集ツールを実行します。

   [OMS Linux Agent Log Collector](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. 次のフォルダーの内容を圧縮し、圧縮ファイルを Azure サポートに送信します。

   ``/var/opt/microsoft/omsagent/run/automationworker/``
 
3. OMS Linux エージェントから報告されているワークスペース ID が、更新プログラムの監視対象のワークスペースと同じであることを確認します。

### <a name="for-issues-that-affect-update-management-on-windows"></a>Windows での Update Management に影響する問題の場合

[4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics) に記載されている項目に加えて、次の手順を実行します。

1. 次のイベントログを EVTX 形式でエクスポートします。

   * システム
   * Application
   * セキュリティ
   * Operations Manager
   * Microsoft-SMA/Operational

2. エージェントから報告されているワークスペース ID が、Windows 更新プログラムの監視対象のワークスペースと同じであることを確認します。

### <a name="for-issues-that-affect-a-job"></a>ジョブに影響する問題の場合

[4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics) に記載されている項目に加えて、次の情報を収集します。

1. 問題が発生しているジョブの **JobID** 番号を収集します。

   1. Azure Portal で、 **[Automation アカウント]** ブレードに移動します。
   2. トラブルシューティングを行う **[Automation アカウント]** を選択します。
   3. **[ジョブ]** を選択します。
   4. トラブルシューティングを行うジョブを選択します。
   5. **[ジョブの概要]** から、 **[ジョブ ID]** (GUID) を探します。

   ![ジョブの概要ウィンドウ内のジョブ ID](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

2. アカウント名を取得するには、次の手順に従います。

   1. Azure Portal で、 **[Automation アカウント]** ブレードに移動します。
   2. トラブルシューティングを行う **[Automation アカウント]** の名前を取得します。

3. 実行中のスクリプトのサンプルを収集します。

4. ログ ファイルを収集するには、次の手順に従います。

   1. Azure Portal で、 **[Automation アカウント]** ブレードに移動します。
   2. トラブルシューティングを行う **[Automation アカウント]** を選択します。
   3. **[ジョブ]** を選択します。
   4. トラブルシューティングを行うジョブを選択します。
   5. **[すべてのログ]** を選択します。
   6. 結果のブレードで、データを収集します。

   ![[すべてのログ] の下に表示されるデータ](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

### <a name="for-issues-that-affect-modules"></a>モジュールに影響する問題の場合

「基本データの収集」の項目に加えて、次の情報を収集します。

* 問題を再現するための手順。
* エラー メッセージのスクリーンショット。
* 現在のモジュールとそのバージョン番号のスクリーンショット。

## <a name="next-steps"></a>次の手順

この記事についてさらにヘルプが必要な場合は、[MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートにお問い合わせください。

または、Azure サポート インシデントを送信してください。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。
