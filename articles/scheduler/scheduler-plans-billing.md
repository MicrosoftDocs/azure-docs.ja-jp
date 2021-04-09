---
title: プランと課金
description: Azure Scheduler のプランと課金について説明します
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: e821036ff4ddb5a9786bc4f4537bb81539ab2c87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "78898467"
---
# <a name="plans-and-billing-for-azure-scheduler"></a>Azure Scheduler のプランと課金

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) は、[廃止される予定](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)の Azure Scheduler の後継です。 Scheduler で設定したジョブを使用し続けるには、できるだけ早く [Azure Logic Apps に移行](../scheduler/migrate-from-scheduler-to-logic-apps.md)してください。 
>
> Scheduler は Azure portal で利用できなくなりましたが、現時点では [REST API](/rest/api/scheduler) と [Azure Scheduler PowerShell コマンドレット](scheduler-powershell-reference.md)がまだ使用できるので、お客様はジョブとジョブ コレクションを管理することができます。

## <a name="job-collection-plans"></a>ジョブ コレクション プラン

Azure Scheduler では、ジョブ コレクションには特定の数のジョブが含まれています。 ジョブ コレクションは課金対象のエンティティであり、ここで説明する Standard、P10 Premium、および P20 Premium プランが用意されています。 

| ジョブ コレクション プラン | コレクションあたりの最大ジョブ数 | 最大繰り返し頻度 | サブスクリプションあたりの最大ジョブ コレクション数 | 制限 | 
|:--- |:--- |:--- |:--- |:--- |
| **Standard** | コレクションあたり 50 ジョブ | 1 分に 1 回。 1 分に 1 回より多くジョブを実行することはできません。 | 各 Azure サブスクリプションが持つことのできる Standard ジョブ コレクションは最大 100 個です。 | Scheduler の完全な機能セットにアクセスする | 
| **P10 Premium** | コレクションあたり 50 ジョブ | 1 分に 1 回。 1 分に 1 回より多くジョブを実行することはできません。 | 各 Azure サブスクリプションが持つことのできる P10 Premium ジョブ コレクションは最大 10,000 個です。 それより多くのコレクションが必要な場合は、<a href="mailto:wapteams@microsoft.com">お問い合わせください</a>。 | Scheduler の完全な機能セットにアクセスする |
| **P20 Premium** | コレクションあたり 1,000 ジョブ | 1 分に 1 回。 1 分に 1 回より多くジョブを実行することはできません。 | 各 Azure サブスクリプションが持つことのできる P20 Premium ジョブ コレクションは最大 5,000 個です。 それより多くのコレクションが必要な場合は、<a href="mailto:wapteams@microsoft.com">お問い合わせください</a>。 | Scheduler の完全な機能セットにアクセスする |
|||||| 

## <a name="pricing"></a>価格

料金の詳細については、「[Scheduler 料金](https://azure.microsoft.com/pricing/details/scheduler/)」を参照してください。

## <a name="upgrade-or-downgrade-plans"></a>プランをアップグレードまたはダウングレードする

いつでも、Standard、P10 Premium、P20 Premium の間でジョブ コレクション プランをアップグレードまたはダウングレードできます。

## <a name="active-status-and-billing"></a>アクティブ状態と課金

ジョブ コレクションは、Azure サブスクリプション全体が課金に関する問題のために一時的な無効状態にならない限り、常にアクティブです。 また、1 回の操作でジョブ コレクション内のすべてのジョブを無効にできますが、このアクションを行ってもジョブ コレクションの課金状態は変更されないので、ジョブ コレクションは "*依然として*" 課金されます。 空のジョブ コレクションはアクティブと見なされ、課金の対象となります。

ジョブ コレクションが課金されないようにするには、ジョブ コレクションを削除する必要があります。

## <a name="standard-billable-units"></a>Standard 課金単位

Standard 課金単位には、最大 10 個の Standard ジョブ コレクションを含めることができます。 Standard ジョブ コレクションでは、コレクションあたり最大で 50 個のジョブを含めることができるため、Standard 課金単位 1 単位で Azure サブスクリプションに最大 500 個のジョブを含めることができます (これは、1 か月あたり約 "*2,200 万件*" のジョブ実行に相当します)。 次の一覧では、Standard ジョブ コレクションのさまざまな数に基づいて、どのように課金されるのかを説明します。

* 使用している Standard ジョブ コレクションが 1 個から 10 個の場合、Standard 課金単位 1 単位分が課金されます。 

* 使用している Standard ジョブ コレクションが 11 個から 20 個の場合、Standard 課金単位 2 単位分が課金されます。 

* 使用している Standard ジョブ コレクションが 21 個から 30 個の場合、Standard 課金単位 3 単位分が課金されます。それ以上の数についても同様の計算になります。

## <a name="p10-premium-billable-units"></a>P10 Premium 課金単位

P10 Premium 課金単位には、最大 10,000 個の P10 Premium ジョブ コレクションを含めることができます。 P10 Premium ジョブ コレクションでは、コレクションあたり最大で 50 個のジョブを含めることができるため、P10 Premium 課金単位 1 単位で Azure サブスクリプションに最大 500,000 個のジョブを含めることができます (これは、1 か月あたり約 "*220 億件*" のジョブ実行に相当します)。 

P10 Premium ジョブ コレクションでは、Standard ジョブ コレクションと同じ機能が提供されますが、大量のジョブ コレクションを必要とするアプリのための割引価格があり、より高いスケーラビリティが提供されます。 次の一覧では、P10 Premium ジョブ コレクションのさまざまな数に基づいて、どのように課金されるのかを説明します。

* 使用している P10 Premium ジョブ コレクションが 1 個から 10,000 個の場合、P10 Premium 課金単位 1 単位分が課金されます。 

* 使用している P10 Premium ジョブ コレクションが 10,001 個から 20,000 個の場合、P10 Premium 課金単位 2 単位分が課金されます。それ以上の数についても同様の計算になります。

## <a name="p20-premium-billable-units"></a>P20 Premium 課金単位

P20 Premium 課金単位には、最大 5,000 個の P20 Premium ジョブ コレクションを含めることができます。 P20 Premium ジョブ コレクションでは、ジョブ コレクションあたり最大で 1,000 個のジョブを含めることができるため、P20 Premium 課金単位 1 単位で Azure サブスクリプションに最大 5,000,000 個のジョブを含めることができます (これは、1 か月あたり約 "*2,200 億件*" のジョブ実行に相当します)。

P20 Premium ジョブ コレクションで提供される機能は P10 Premium ジョブ コレクションと同じですが、P10 Premium ジョブ コレクションよりもコレクションあたりでサポートされるジョブの数が多く、ジョブ全体の総数もより多くなるため、スケーラビリティが向上します。

## <a name="plan-comparison"></a>プランの比較

* 100 を超える Standard ジョブ コレクション (Standard 課金単位 10 単位) を使用している場合は、Premium プランにすべてのジョブ コレクションを移行することでよい結果が得られます。

* 1 つの Standard ジョブ コレクションと 1 つの Premium ジョブ コレクションを使用している場合、Standard プランの 1 課金単位 "*および*" Premium プランの 1 課金単位が請求されます。

  Scheduler サービスでは、Standard または Premium どちらかのアクティブなジョブ コレクションの数に基づいて課金されます。

## <a name="next-steps"></a>次のステップ

* [Azure Scheduler の概念、用語集、エンティティ階層構造](scheduler-concepts-terms.md)
* [Azure Scheduler の制限、既定値、エラー コード](scheduler-limits-defaults-errors.md)