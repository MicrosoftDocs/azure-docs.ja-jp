---
title: アクセス レビューに使用するプログラムとコントロールを管理する - Azure Active Directory | Microsoft Docs
description: 社内のコンプライアンスへの取り組みやリスク管理、ガバナンスごとに追加のプログラムを作成することで、Azure Active Directory のアクセス レビューをコントロールとしてまとめ、体系化する方法を説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43c0f1c041bfed1b041a9926efd869d167c6f1e9
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2019
ms.locfileid: "58577265"
---
# <a name="manage-programs-and-controls-for-azure-ad-access-reviews"></a>Azure AD アクセス レビューに使用するプログラムとコントロールを管理する

Azure Active Directory (Azure AD) には、グループ メンバーのアクセス レビューとアプリケーション アクセスのアクセス レビューが備わっています。 これらのレビューは、組織のグループ メンバーシップとアプリケーションに対するアクセス権の所在管理を徹底するコントロールの例です。 こうしたコントロールによって、企業はそのガバナンス、リスク管理、コンプライアンス要件に効率よく対応することができます。

## <a name="create-and-manage-programs-and-their-controls"></a>プログラムとそのコントロールを作成、管理する
目的の異なるさまざまなアクセス レビューは、プログラムにまとめることにより、その追跡と収集を省力化することができます。 それぞれのアクセス レビューを特定のプログラムに関連付けることができるので、 それにより、監査担当者へのレポートを準備する場合は、特定の取り組みの範囲内のアクセス レビューに焦点を絞ることができます。  プログラムおよびアクセス レビューの結果は、グローバル管理者、ユーザー管理者、セキュリティ管理者、またはセキュリティ閲覧者の各役割にあるユーザーに表示されます。

プログラムの一覧を表示するには、[アクセス レビュー ページ](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)に移動し、**[プログラム]** を選択します。

"**既定のプログラム**" というプログラムが常に存在します。 グローバル管理者またはユーザー管理者ロールのユーザーであれば、新たにプログラムを作成することができます。 たとえば、コンプライアンスの取り組みやビジネス目標ごとにプログラムを 1 つ用意することができます。

不要になったためコントロールが関連付けられていないプログラムは削除することができます。

## <a name="next-steps"></a>次の手順

- [グループまたはアプリケーションのアクセス レビューを作成する](create-access-review.md)
- [グループまたはアプリケーションに対するアクセス レビューを取得する](retrieve-access-review.md)
