---
title: インクルード ファイル
description: インクルード ファイル
services: batch
author: JnHs
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: eaa76153fe96b5fd41166b20770e0a969aa9260d
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/22/2020
ms.locfileid: "83797170"
---
Azure Batch で実行するタスクによって、実行時に出力データが生成されることがあります。 タスクの出力データは、多くの場合、ジョブ内の他のタスク、またはそのジョブを実行したクライアント アプリケーション (あるいは両方) が取得できるように格納される必要があります。 タスクは Batch コンピューティング ノードのファイル システムに出力データを書き込みますが、再イメージ化されるとき、またはノードがプールを終了するときに、ノードのすべてのデータが失われます。 タスクには、ファイル リテンション期間が設定されている場合もあります。この期間が過ぎると、タスクによって作成されたファイルが削除されます。 これらの理由から、後で必要になるタスク出力をデータ ストア ([Azure Storage](https://docs.microsoft.com/azure/storage/) など) に永続化することが重要です。

Batch のストレージ アカウント オプションについては、「[Batch アカウントと Azure Storage アカウント](../articles/batch/accounts.md#azure-storage-accounts)」を参照してください。
