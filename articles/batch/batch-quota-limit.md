---
title: "Azure Batch 用のサービスのクォータと制限 | Microsoft Docs"
description: "既定の Azure Batch のクォータ、制限、および制約と、クォータの引き上げを要求する方法について説明します"
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 6b6c548ca1001587e2b40bbe9ee2fcb298f40d72
ms.openlocfilehash: a0f47a19f7ef1832e64e9a0bdc4bda3434f77aa2
ms.lasthandoff: 02/28/2017


---
# <a name="batch-service-quotas-and-limits"></a>Batch サービスのクォータと制限

他の Azure サービスと同様に、Batch サービスに関連付けられている特定のリソースにも制限があります。 これらの制限の多くは、Azure によって、サブスクリプションまたはアカウント レベルで適用される既定のクォータです。 ここでは、これらの既定の設定と、クォータの引き上げを要求する方法について説明します。

Batch で実稼働ワークロードを実行する予定がある場合は、1 つまたは複数のクォータについて既定値から増やすことが必要になる場合があります。 クォータを増やすには、オンライン [カスタマー サポートに申請](#increase-a-quota) (無料) してください。

> [!NOTE]
> クォータは、容量の保証ではなく、クレジット制限です。 大規模な容量が必要な場合は、Azure サポートにお問い合わせください。
> 
> 

## <a name="resource-quotas"></a>リソース クォータ
[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## <a name="other-limits"></a>その他の制限
| **リソース** | **上限** |
| --- | --- |
| [同時実行タスク数](batch-parallel-node-tasks.md)  |ノードのコア数 x&4; |
| [アプリケーション](batch-application-packages.md) 数 |20 |
| アプリケーションあたりのアプリケーション パッケージ数 |40 |
| 各アプリケーション パッケージのサイズ |約 195 GB<sup>1</sup> |

<sup>1</sup> 最大ブロック BLOB サイズに関する Azure Storage の制限

## <a name="view-batch-quotas"></a>Batch クォータの確認
Batch アカウントのクォータは、[Azure Portal][portal] で確認します。

1. ポータルで **[Batch アカウント]** を選択し、目的の Batch アカウントを選択します。
2. Batch アカウントのメニュー ブレードで **プロパティ** を選択します。
3. **[プロパティ]** ブレードに、Batch アカウントに現在適用されているクォータが表示されます。
   
    ![Batch アカウントのクォータ][account_quotas]

## <a name="increase-a-quota"></a>クォータを増やす
[Azure Portal][portal] を使用してクォータの引き上げを要求するには、次の手順に従います。

1. ポータルのダッシュボードで **[ヘルプとサポート]** タイルを選択します。または、ポータルの右上隅にある疑問符 (**[?]**) を選択します。
2. **[新しいサポート要求]** > **[基本]** の順にクリックします。
3. **[基本]** ブレードで、次の操作を行います。
   
    a. **[問題の種類]** > **[クォータ]**
   
    b. サブスクリプションを選択します。
   
    c. **[クォータの種類]** > **バッチ**
   
    d. **[サポート プラン]** > **[Quota Support - Included (クォータのサポート - 含む)]**
   
    **[次へ]**をクリックします。
4. **[Problem (問題)]** ブレードで、次の操作を行います。
   
    a.この問題では、ターゲット (またはクラス) ラベルは "tip_amount" です。 [ビジネスへの影響][support_sev]に従って **[重要度]** を選択します。
   
    b. **[詳細]** で、変更する個々のクォータ、Batch アカウント名、および新しい制限値を指定します。
   
    **[次へ]**をクリックします。
5. **[連絡先情報]** ブレードで、次の操作を行います。
   
    a. **希望連絡方法**を選択します。
   
    b. 必要な連絡先情報を確認および入力します。
   
    **[作成]** をクリックしてサポート要求を送信します。

サポート要求を送信した後は、Azure サポートからの連絡を待ちます。 要求を完了するには最大で 2 営業日かかります。

## <a name="related-topics"></a>関連トピック
* [Azure Portal で Azure Batch アカウントを作成して管理する](batch-account-create-portal.md)
* [Azure Batch 機能の概要](batch-api-basics.md)
* [Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: http://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.PNG

