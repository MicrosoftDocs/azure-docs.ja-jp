---
title: PIM で Azure リソース ロールに対するセキュリティ アラートを構成する - Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure リソース ロールに対するセキュリティの警告を構成する方法を説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c7ce8b79644a9ffc9481ba825ec5623a9268983
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476328"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-pim"></a>PIM で Azure リソース ロールに対するセキュリティの警告を構成する
環境内で疑わしいアクティビティや危険なアクティビティが行われると、Azure Active Directory (Azure AD) Privileged Identity Management (PIM) によりアラートが生成されます。 アラートがトリガーされると、[アラート] ページに表示されます。 

![アラート、リスク レベル、および数を一覧表示する [Azure リソース] - [アラート] ページ](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>アラートを確認する
アラートを選択して、アラートをトリガーしたユーザーまたはロールの他に解決策のアドバイスを示すレポートを表示します。

![最終スキャン時刻、説明、軽減手順、タイプ、重大度、セキュリティへの影響、および次回の防止方法を示すアラート レポート](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>アラート
| アラート: | Severity | トリガー | 推奨 |
| --- | --- | --- | --- |
| **リソースに割り当てられている所有者が多すぎます** |Medium |所有者ロールを持っているユーザーが多すぎます。 |リストに表示されているユーザーを見直して、数人のユーザーに低い特権ロールを再割り当てします。 |
| **リソースに割り当てられている永続的な所有者が多すぎます** |Medium |ロールに永続的に割り当てられているユーザーが多すぎます。 |リストに表示されているユーザーを見直して、数人のユーザーにロールを使用するにはアクティブ化が必要であるように再割り当てします。 |
| **重複したロールが作成されました** |Medium |複数のロールが同じ条件を持っています。 |これらのロールのいずれかのみを使用します。 |


### <a name="severity"></a>Severity
* **高**: ポリシー違反のため直ちに対処が必要です。 
* **中**: 早急の対処は必要ありませんが、ポリシー違反の可能性が通知されています。
* **低**: 早急な対処は必要ありませんが、望ましいポリシー変更が提案されています。

## <a name="configure-security-alert-settings"></a>セキュリティ アラート設定の構成
[アラート] ページから **[設定]** に移動します。

![[設定] が強調表示されている [アラート] ページ](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

環境で機能し、セキュリティの目標に合うようにさまざまなアラートの設定をカスタマイズします。

![設定を有効化および構成するための、アラートの [設定] ページ](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>次の手順

- [PIM で Azure リソース ロールの設定を構成する](pim-resource-roles-configure-role-settings.md)
