---
title: Azure Scheduler とは | Microsoft Docs
description: Azure の内部または外部のサービスを呼び出す自動ジョブを作成、スケジュール、および実行する方法について説明します
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 52aa6ae1-4c3d-43fb-81b0-6792c84bcfae
ms.topic: conceptual
ms.date: 09/17/2018
ms.openlocfilehash: 21204e85adf1c68264ea448360c9e1120567ef3f
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57764576"
---
# <a name="what-is-azure-scheduler"></a>Azure Scheduler とは

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) は、廃止される予定の Azure Scheduler の後継です。 ジョブをスケジュールするには、[Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) を代わりにお使いください。 

[Azure Scheduler](https://azure.microsoft.com/services/scheduler/) は、宣言でアクションを記述することによって、クラウド内で実行される[ジョブ](../scheduler/scheduler-concepts-terms.md)を作成するのに役立ちます。 その後、サービスはそれらのアクションを自動的にスケジュールして実行します。 たとえば、Azure の内部および外部のサービスを呼び出すことができ (HTTP または HTTPS エンドポイントの呼び出しなど)、Azure Storage キューおよび Azure Service Bus キューまたはトピックにメッセージを送信することもできます。 すぐに、または後で、ジョブを実行することができます。 Scheduler は、[複雑なスケジュールおよび高度な定期実行](../scheduler/scheduler-advanced-complexity.md)に容易に対応できます。 Scheduler は、ジョブを実行するタイミングを指定し、ユーザーが確認できるジョブの結果の履歴を保持し、実行するワークロードを予期したとおり確実にスケジュールします。

Scheduler を使用してスケジュールされたワークロードを作成、管理、および実行できますが、Scheduler がワークロードをホストしたりコードを実行したりすることはありません。 このサービスは、他の場所 (Azure、オンプレミス、別のプロバイダーなど) でホストされているサービスまたはコードを "*呼び出す*" だけです。 Scheduler は、HTTP、HTTPS、Storage キュー、Service Bus キュー、Service Bus トピックを介して呼び出すことができます。 ジョブを作成、管理、およびスケジュールするには、[Azure portal](../scheduler/scheduler-get-started-portal.md)、コード、[Scheduler REST API](https://docs.microsoft.com/rest/api/scheduler/)、または [Azure Scheduler PowerShell コマンドレット リファレンス](scheduler-powershell-reference.md)を使用できます。 たとえば、スクリプトを使用したり Azure portal 内で、ジョブおよび[ジョブ コレクション](../scheduler/scheduler-concepts-terms.md)をプログラムにより作成、表示、更新、管理、削除できます。

Azure App Service の [Web Apps](https://azure.microsoft.com/services/app-service/web/) 機能である [Azure WebJobs](../app-service/webjobs-create.md) など、他の Azure スケジュール機能も Scheduler をバックグラウンドで使用します。 [Scheduler REST API](https://docs.microsoft.com/rest/api/scheduler/) を使用することで、これらのアクションに対する通信を管理できます。 これらのアクションにおける通信を管理します。

Scheduler が役に立ついくつかのシナリオを次に示します。

* **アプリの定期的なアクションを実行する**:たとえば、Twitter からフィードに定期的にデータを収集します。

* **毎日のメンテナンスを実行する**:日常的なログの除去、バックアップの実行、その他のメンテナンス タスクなどです。 

  たとえば、管理者は、今後 9 か月間毎日午前 1 時にデータベースをバックアップする、といったことがあります。

## <a name="next-steps"></a>次の手順

* [Azure portal で Scheduler の使用を始める](scheduler-get-started-portal.md)
* [Azure Scheduler のプランと課金](scheduler-plans-billing.md)を確認する
* [Azure Scheduler で複雑なスケジュールと高度な繰り返しを構築する方法](scheduler-advanced-complexity.md)を学習する