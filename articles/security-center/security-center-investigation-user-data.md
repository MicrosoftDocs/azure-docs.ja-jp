---
title: Azure Security Center 調査で検出されたユーザー データの管理 | Microsoft Docs
description: " Azure Security Center 調査で検出されたユーザー データを管理する方法について説明します。 "
services: operations-management-suite
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2018
ms.author: memildin
ms.openlocfilehash: 8b6bde69f233fee9fe20b260e392966298f13a9a
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202042"
---
# <a name="manage-user-data-found-in-an-azure-security-center-investigation"></a>Azure Security Center 調査で検出されたユーザー データの管理
この記事では、Azure Security Center 調査機能で検出されたユーザー データの管理方法を説明します。 調査データは [Azure Monitor ログ](../log-analytics/log-analytics-overview.md)に格納され、Azure Security Center で公開されます。 ユーザー データの管理には、データを削除したりエクスポートしたりする機能が含まれます。

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="searching-for-and-identifying-personal-data"></a>個人データの検索と識別
Azure Portal では、Azure Security Center [調査機能](../security-center/security-center-investigation.md)を使用して個人データを検索できます。 調査機能は、 **[セキュリティ アラート]** の中にあります。

調査機能の **[エンティティ]** タブに、すべてのエンティティ、ユーザー情報、およびデータが表示されます。

## <a name="securing-and-controlling-access-to-personal-information"></a>個人情報のセキュリティ保護とアクセス制御
閲覧者、所有者、共同作成者、またはアカウント管理者のロールが割り当てられている Azure Security Center のユーザーは、ツール内の顧客データにアクセスできます。

閲覧者、所有者、共同作成者ロールの詳細については、「[Azure ロールベースのアクセス制御の組み込みロール](../role-based-access-control/built-in-roles.md)」を参照してください。 アカウント管理者ロールの詳細については、[Azure サブスクリプション管理者](../billing/billing-add-change-azure-subscription-administrator.md)に関する記事を参照してください。

## <a name="deleting-personal-data"></a>個人データの削除
閲覧者、所有者、共同作成者、またはアカウント管理者のロールが割り当てられている Azure Security Center のユーザーは、調査情報を削除できます。

調査を削除するには、Azure Resource Manager REST API へ `DELETE` 要求を送信できます。

```HTTP
DELETE
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents/{incidentName}
```

`incidentName` 入力は、`GET` 要求を使用してすべてのインシデントを一覧表示することで見つけられます。

```HTTP
GET
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents
```

## <a name="exporting-personal-data"></a>個人データのエクスポート
閲覧者、所有者、共同作成者、またはアカウント管理者のロールが割り当てられている Azure Security Center のユーザーは、調査情報をエクスポートできます。 調査情報をエクスポートするには、 **[エンティティ]** タブに移動して関連する情報をコピーして貼り付けます。

## <a name="next-steps"></a>次の手順
ユーザー データの管理の詳細については、[Azure Security Center でのユーザー データの管理](security-center-privacy.md)に関する記事を参照してください。
Azure Monitor ログでのプライベート データの削除について詳しくは、「[プライベート データをエクスポートして削除する方法](../azure-monitor/platform/personal-data-mgmt.md#how-to-export-and-delete-private-data)」を参照してください。
