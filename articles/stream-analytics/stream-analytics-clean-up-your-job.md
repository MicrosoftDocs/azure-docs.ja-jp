---
title: Azure Stream Analytics ジョブの削除
description: この記事では、Azure Stream Analytics ジョブを削除するさまざまな方法を示します。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 6/21/2019
ms.custom: seodec18
ms.openlocfilehash: cb81c73f7946a10bae0470a55dcf1c0d55c2b847
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330046"
---
# <a name="stop-or-delete-your-azure-stream-analytics-job"></a>Azure Stream Analytics ジョブを停止または削除する

Azure Stream Analytics ジョブは、Azure portal、Azure PowerShell、Azure SDK for .Net または REST API を使用して簡単に停止または削除できます。 削除した Stream Analytics ジョブは復旧できません。

>[!NOTE] 
>Azure Stream Analytics ジョブを停止すると、Event Hubs または Azure SQL Database などの入力および出力ストレージのみでデータが永続化されます。 Azure からデータを削除する必要がある場合は、Stream Analytics ジョブの入力と出力のリソースの削除処理を実行してください。

## <a name="stop-a-job-in-azure-portal"></a>Azure Portal でジョブを停止する

ジョブを停止すると、リソースがプロビジョニング解除され、イベントの処理が停止します。 このジョブに関連する課金も停止します。 ただし、構成はすべて保持され、後でジョブを再開できます。 

1. [Azure Portal](https://portal.azure.com) にサインインします。 

2. 実行されている Stream Analytics ジョブを探し､選択します｡

3. Stream Analytics ジョブのページで **[停止]** をクリックします。 

   ![Azure Stream Analytics ジョブを停止する](./media/stream-analytics-clean-up-your-job/stop-stream-analytics-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Azure Portal でジョブを削除する

>[!WARNING] 
>削除した Stream Analytics ジョブは復旧できません。

1. Azure ポータルにサインインします。 

2. Stream Analytics ジョブがある場所を探し､ジョブを選択します｡

3. Stream Analytics ジョブのページで **[削除]** を選択してジョブを削除します。 

   ![Azure Stream Analytics ジョブを削除する](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>PowerShell を使用してジョブを停止または削除する

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

PowerShell を使用してジョブを停止するには、[Stop-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/stop-azstreamanalyticsjob) コマンドレットを使用します。 PowerShell を使用してジョブを削除するには、[Remove-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/Remove-azStreamAnalyticsJob) コマンドレットを使用します。

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Azure SDK for .NET を使用してジョブを停止または削除する

Azure SDK for .NET を使用してジョブを停止するには、[StreamingJobsOperationsExtensions.BeginStop](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet) メソッドを使用します。 Azure SDK for .NET を使用してジョブを削除するには、[StreamingJobsOperationsExtensions.BeginDelete](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet) メソッドを使用します。

## <a name="stop-or-delete-a-job-using-rest-api"></a>REST API を使用してジョブを停止または削除する

REST API を使用してジョブを停止するには、[Stop](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#stop) メソッドを参照してください。 REST API を使用してジョブを削除するには、[Delete](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#delete) メソッドを参照してください。
