---
title: イベント レプリケーションのタスクとアプリケーション - Azure Event Hubs | Microsoft Docs
description: この記事では、Azure Functions を使用したイベント レプリケーション タスクとアプリケーションの構築の概要について説明します。
ms.topic: article
ms.date: 12/01/2020
ms.openlocfilehash: a65815c7da400af8b5b6d46358e6bca6edbd7543
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97663522"
---
# <a name="event-replication-tasks-and-applications-with-azure-functions"></a>Azure Functions を使用したイベント レプリケーションのタスクとアプリケーション

> [!TIP]
> イベントのペイロードを考慮し、それらを変換、集計、エンリッチ、または削減する必要があるすべてのステートフル レプリケーション タスクには、Azure Functions の代わりに [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) を使用します。

[イベント レプリケーションとクロスリージョン フェデレーション](event-hubs-federation-overview.md)に関する記事で説明されているように、Event Hubs のペア間、および Event Hubs とその他のストリームのソースとターゲット間でのステートレス レプリケーションは、Azure Functions に依存しています。

[Azure Functions](../azure-functions/functions-overview.md) は、イベント レプリケーションとフェデレーション タスクを含む、サーバーレス アプリケーションを構成および実行するためのスケーラブルで信頼性の高い実行環境です。

この概要では、このようなアプリケーション用の Azure Functions の組み込み機能、変換タスクに適応して変更できるコード ブロック、および Event Hub と他の Azure メッセージング サービスと理想的に統合されるように Azure Functions アプリケーションを構成する方法について説明します。 詳細については、この記事で紹介する Azure Functions のドキュメントを参照してください。

[!INCLUDE [messaging-replicator-functions](../../includes/messaging-replicator-functions.md)]









