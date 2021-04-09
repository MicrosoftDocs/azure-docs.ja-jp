---
title: 高可用性と信頼性
description: Azure Scheduler での高可用性と信頼性について説明します
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/16/2016
ms.openlocfilehash: 98a6672af7e74fdd0732f3ba03264d2f674eb44f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92368147"
---
# <a name="high-availability-and-reliability-for-azure-scheduler"></a>Azure Scheduler の高可用性と信頼性

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) は、[廃止される予定](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)の Azure Scheduler の後継です。 Scheduler で設定したジョブを使用し続けるには、できるだけ早く [Azure Logic Apps に移行](../scheduler/migrate-from-scheduler-to-logic-apps.md)してください。 
>
> Scheduler は Azure portal で利用できなくなりましたが、現時点では [REST API](/rest/api/scheduler) と [Azure Scheduler PowerShell コマンドレット](scheduler-powershell-reference.md)がまだ使用できるので、お客様はジョブとジョブ コレクションを管理することができます。

Azure Scheduler では、[高可用性](/azure/architecture/framework/#resiliency)と信頼性の両方がジョブに提供されます。 詳しくは、「[Scheduler の SLA](https://azure.microsoft.com/support/legal/sla/scheduler)」をご覧ください。

## <a name="high-availability"></a>高可用性

Azure Scheduler では、"高可用性" に加え、geo 冗長サービスのデプロイと geo リージョン ジョブ レプリケーションの両方が使用されます。

### <a name="geo-redundant-service-deployment"></a>geo 冗長サービスのデプロイ

Azure Scheduler は、[現在 Azure でサポートされているほぼすべての地理的リージョン](https://azure.microsoft.com/global-infrastructure/regions/#services)で使用できます。 したがって、ホストされているリージョンの Azure データ センターが使用できなくなった場合でも、サービスのフェールオーバー機能により Scheduler は別のデータ センターから使用できるようになるため、Azure Scheduler を引き続き使用できます。

### <a name="geo-regional-job-replication"></a>geo リージョンのジョブ レプリケーション

Azure Scheduler 内の自分のジョブは、Azure リージョン間でレプリケートされます。 したがって、1 つのリージョンで障害が発生した場合、Azure Scheduler はフェールオーバーして、ペアの地理的リージョン内の別のデータ センターでジョブを実行できるようにします。

たとえば、米国中南部でジョブを作成した場合は、Azure Scheduler は、このジョブを自動的に米国中北部にレプリケートします。 米国中南部で障害が発生した場合、Azure Scheduler は米国中北部でジョブを実行します。 

![geo リージョンのジョブ レプリケーション](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png)

さらに、Azure Scheduler により、Azure 内で障害が発生した場合に備えて、データは同じリージョン内のさらに広い地理的リージョンに確実に留められます。 そのため、高可用性のためだけにジョブを複製する必要はありません。 Azure Scheduler では、ジョブの高可用性は自動的に提供されます。

## <a name="reliability"></a>[信頼性]

Azure Scheduler では、独自の高可用性を保証し、ユーザーが作成したジョブに対して別のアプローチを採用しています。 たとえば、使用できない HTTP エンドポイントをジョブが呼び出したとします。 Azure Scheduler はそれでも、障害に対処するための代わりの手段を提供することにより、ジョブが正常に実行されるようにします。 

* 再試行ポリシーを設定します。
* 代替エンドポイントを設定します。

<a name="retry-policies"></a>

### <a name="retry-policies"></a>再試行ポリシー

Azure Scheduler では、再試行ポリシーを設定できます。 ジョブが失敗した場合、既定では Scheduler により 30 秒間隔でさらに 4 回ジョブの実行が試みられます。 この再試行ポリシーを、より積極的にすることも (たとえば、30 秒間隔で 10 回)、またはより控えめにすることも (たとえば、1 日おきに 2 回) できます。

たとえば、HTTP エンドポイントを呼び出す週単位のジョブを作成するものとします。 ジョブの実行時に HTTP エンドポイントが数時間使用できなくなった場合、このケースでは既定の再試行ポリシーが機能しないため、ジョブの再実行が翌週になりますが、それまでは待っていられない場合があります。 このような場合、30 秒ごとではなく、たとえば 3 時間ごとに再試行が行われるように、標準再試行ポリシーを変更できます。 

再試行ポリシーの設定方法については、「[retryPolicy](scheduler-concepts-terms.md#retrypolicy)」をご覧ください。

### <a name="alternate-endpoints"></a>代替エンドポイント

Azure Scheduler ジョブにより到達不可能なエンドポイントが呼び出された場合は、再試行ポリシーが実行された後でも、Scheduler により、そのようなエラーを処理できる代替エンドポイントにフォールバックされます。 したがって、このエンドポイントを設定すると、Scheduler によりそのエンドポイントが呼び出され、ジョブはエラーの発生に対して高可用性になります。

たとえば、次の図は、ニューヨークの Web サービスが呼び出されるときに Scheduler が再試行ポリシーに従うしくみを示しています。 再試行が失敗した場合、Scheduler により代替エンドポイントがチェックされます。 エンドポイントが存在する場合、Scheduler により代替エンドポイントへの要求の送信が開始されます。 元のアクションと代替アクションの両方に、同じ再試行ポリシーが適用されます。

![再試行ポリシーおよび代替エンドポイントがある Scheduler の動作](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png)

代替アクションのアクションの種類は、元のアクションと異なっていてもかまいません。 たとえば、元のアクションは HTTP エンドポイントを呼び出していても、代替アクションでは、ストレージ キュー、Service Bus キュー、または Service Bus トピック アクションを使用して、エラーをログに記録できます。

代替エンドポイントを設定する方法については、「[errorAction](scheduler-concepts-terms.md#error-action)」をご覧ください。

## <a name="next-steps"></a>次のステップ

* [概念、用語集、エンティティ階層構造](scheduler-concepts-terms.md)
* [Azure Scheduler REST API リファレンス](/rest/api/scheduler)
* [Azure Scheduler PowerShell コマンドレット リファレンス](scheduler-powershell-reference.md)
* [制限、クォータ、既定値、エラー コード](scheduler-limits-defaults-errors.md)