---
title: Azure Security Center FAQ - アクセス許可に関する質問
description: この FAQ では、脅威の防御、検出、対応を可能にする製品である Azure Security Center のアクセス許可に関する質問に回答します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 88d6207bf44d1ec04fde4f74ac9295412ea88fbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87824615"
---
# <a name="permissions"></a>アクセス許可

## <a name="how-do-permissions-work-in-azure-security-center"></a>Azure Security Center のアクセス許可はどのように機能しますか？

Azure Security Center では、[Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) が使用されており、これによって提供される[組み込みロール](../role-based-access-control/built-in-roles.md)は、Azure でユーザー、グループ、サービスに割り当てることができます。

Security Center は、リソースの構成を評価して、セキュリティの問題と脆弱性を特定します。 Security Center では、リソースが属するサブスクリプションまたはリソース グループの所有者、共同作業者、閲覧者のいずれかのロールが割り当てられているときにリソースに関連した情報のみが表示されます。

Security Center のロールと許可されているアクションの詳細については、「[Permissions in Azure Security Center (Azure Security Center のアクセス許可)](security-center-permissions.md)」を参照してください。



## <a name="who-can-modify-a-security-policy"></a>セキュリティ ポリシーを変更できるのは誰ですか。

セキュリティ ポリシーを変更するには、セキュリティ管理者であるか、そのサブスクリプションの所有者または共同作成者である必要があります。

セキュリティ ポリシーを構成する方法については、 [Azure Security Center でのセキュリティ ポリシーの設定](tutorial-security-policy.md) を参照してください。