---
title: Azure Scheduler とは
description: Azure の内部または外部のサービスを呼び出す自動ジョブを作成、スケジュール、および実行する
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 66ec285554299214122a4093837d3506bf642b13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92368130"
---
# <a name="what-is-azure-scheduler"></a>Azure Scheduler とは

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) は、[廃止される予定](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)の Azure Scheduler の後継です。 Scheduler で設定したジョブを使用し続けるには、できるだけ早く [Azure Logic Apps に移行](../scheduler/migrate-from-scheduler-to-logic-apps.md)してください。 
>
> Scheduler は Azure portal で利用できなくなりましたが、現時点では [REST API](/rest/api/scheduler) と [Azure Scheduler PowerShell コマンドレット](scheduler-powershell-reference.md)がまだ使用できるので、お客様はジョブとジョブ コレクションを管理することができます。

[Azure Scheduler](https://azure.microsoft.com/services/scheduler/) は、宣言でアクションを記述することによって、クラウド内で実行される[ジョブ](../scheduler/scheduler-concepts-terms.md)を作成するのに役立ちます。 その後、サービスはそれらのアクションを自動的にスケジュールして実行します。 たとえば、Azure の内部および外部のサービスを呼び出すことができ (HTTP または HTTPS エンドポイントの呼び出しなど)、Azure Storage キューおよび Azure Service Bus キューまたはトピックにメッセージを送信することもできます。 すぐに、または後で、ジョブを実行することができます。 Scheduler は、[複雑なスケジュールおよび高度な定期実行](../scheduler/scheduler-advanced-complexity.md)に容易に対応できます。 Scheduler は、ジョブを実行するタイミングを指定し、ユーザーが確認できるジョブの結果の履歴を保持し、実行するワークロードを予期したとおり確実にスケジュールします。

Azure App Service の [Web Apps](https://azure.microsoft.com/services/app-service/web/) 機能である [Azure WebJobs](../app-service/webjobs-create.md) など、他の Azure スケジュール機能も Scheduler をバックグラウンドで使用します。 これらのアクションの通信の管理に役立つ [Scheduler REST API](/rest/api/scheduler/) を使用して、これらのアクションの通信を管理できます。

Scheduler が役に立ついくつかのシナリオを次に示します。

* アプリの定期的なアクションを実行する: たとえば、Twitter からフィードに定期的にデータを収集します。

* 毎日のメンテナンスを実行する: 日常的なログの除去、バックアップの実行、その他のメンテナンス タスクなどです。

  たとえば、管理者は、今後 9 か月間毎日午前 1 時にデータベースをバックアップする、といったことがあります。

Scheduler を使用してスケジュールされたワークロードを作成、管理、および実行できますが、Scheduler がワークロードをホストしたりコードを実行したりすることはありません。 このサービスは、他の場所 (Azure、オンプレミス、別のプロバイダーなど) でホストされているサービスまたはコードを "*呼び出す*" だけです。 Scheduler は、HTTP、HTTPS、Storage キュー、Service Bus キュー、Service Bus トピックを介して呼び出すことができます。

ジョブおよび[ジョブ コレクション](../scheduler/scheduler-concepts-terms.md)を作成、スケジュール、管理、更新、または削除するには、コード、[Scheduler REST API](/rest/api/scheduler/) または [Azure Scheduler PowerShell コマンドレット](scheduler-powershell-reference.md)を使用できます。

## <a name="next-steps"></a>次のステップ

* [Azure Scheduler の概念、用語集、エンティティ階層構造](scheduler-concepts-terms.md)
* [Azure Scheduler のプランと課金](scheduler-plans-billing.md)
* [Azure Scheduler で複雑なスケジュールと高度な繰り返しを構築する](scheduler-advanced-complexity.md)
* [Azure Scheduler REST API リファレンス](/rest/api/scheduler)
* [Azure Scheduler PowerShell コマンドレット リファレンス](scheduler-powershell-reference.md)