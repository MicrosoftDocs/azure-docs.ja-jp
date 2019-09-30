---
title: Azure Active Directory ポータルのリスクのフラグ付きユーザー セキュリティ レポート | Microsoft Docs
description: Azure Active Directory ポータルのリスクのフラグ付きユーザー セキュリティ レポートについて説明します。
services: active-directory
author: cawrites
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/17/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e6b79c7d5c2ed9744dc00eb1588c35f8ea94a76
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127645"
---
# <a name="users-flagged-for-risk-report-in-the-azure-portal"></a>Azure portal におけるリスクのフラグ付きユーザー レポート

Azure Active Directory (Azure AD) は、ユーザー アカウントに関連する疑わしい動作を検出します。 検出された動作ごとに、"[リスク検出](concept-risk-events.md)" と呼ばれるレコードが作成されます。

セキュリティ レポートには、[Azure portal](https://portal.azure.com) から **[Azure Active Directory]** ブレードを選択し、 **[セキュリティ]** セクションに移動することでアクセスできます。 

検出されたリスク検出は、以下のものの計算に使用されます。

- **リスクの高いサインイン** - リスクの高いサインインは、ユーザー アカウントの正当な所有者ではない人によって行われた可能性があるサインイン試行の指標です。 

- **リスクのフラグ付きユーザー** - リスクの高いユーザーは、侵害された可能性があるユーザー アカウントの指標です。 

これらのリスク検出をトリガーするポリシーを構成する方法については、[ユーザー リスク ポリシーを構成する方法](../identity-protection/howto-user-risk-policy.md)に関するページを参照してください。 

![リスクの高いサインイン](./media/concept-user-at-risk/10.png)


## <a name="what-azure-ad-license-do-you-need-to-access-the-users-at-risk-report"></a>"危険な状態のユーザー" レポートにアクセスするために必要な Azure AD ライセンス  

"リスクのフラグ付きユーザー" レポートは、Azure Active Directory の全エディションで利用できます。 ただしエディションによってレポートの粒度が異なります。 

- リスクのフラグ付きユーザーは、**Azure Active Directory の Free エディションと Basic エディション**で一覧表示できます。 

- 加えて、**Azure Active Directory Premium 1** エディションでは、各レポートについて検出された、基になるリスク検出の一部を調べることができます。 

- **Azure Active Directory Premium 2** エディションでは、基になるすべてのリスク検出について最も詳しい情報が得られます。また、構成されているリスク レベルに対して自動的に対応するセキュリティ ポリシーを構成することができます。


## <a name="users-at-risk-report-for-azure-ad-free-and-basic-editions"></a>Azure AD の Free エディションと Basic エディションにおける危険な状態のユーザー レポート

Azure AD の Free および Basic エディションのリスクのフラグ付きユーザー レポートでは、侵害された可能性があるユーザー アカウントの一覧を提供します。 

![リスクの高いサインイン](./media/concept-user-at-risk/03.png)

ユーザーを選択すると、サインイン情報が表示されます。 危険な状態のユーザーに関して、そのユーザーのサインイン履歴を確認し、必要に応じてパスワードをリセットすることができます。

このダイアログから次の操作を行うことができます。

- レポートのダウンロード
- ユーザーの検索

    ![リスクの高いサインイン](./media/concept-user-at-risk/16.png)

より詳細な情報を取得するには、Premium ライセンスが必要です。

## <a name="users-at-risk-report-for-azure-ad-premium-editions"></a>Azure AD Premium エディションにおける危険な状態のユーザー レポート

Azure AD Premium エディションのリスクのフラグ付きユーザー レポートで提供される情報を以下に示します。

- 侵害された可能性のあるユーザー アカウントの一覧 

- 検出された[リスク検出の種類](concept-risk-events.md)に関する集計情報

- レポートをダウンロードするオプション

- [ユーザー リスク修復ポリシー](../identity-protection/howto-user-risk-policy.md)を構成するオプション  

![リスクの高いサインイン](./media/concept-user-at-risk/71.png)

ユーザーを選択すると、そのユーザーの詳細なレポート ビューが表示されます。ここから次の機能を利用できます。

- [All sign-ins (すべてのサインイン)] ビューを開く

- ユーザーのパスワードをリセットする

- すべてのイベントを閉じる

- そのユーザーについて報告されたリスク検出を調査する 

![リスクの高いサインイン](./media/concept-user-at-risk/324.png)

リスク検出を調査するには、一覧からリスク検出を 1 つ選択してその **[詳細]** ブレードを開きます。 **[詳細]** ブレードで、リスク検出を手動で閉じるか、手動で閉じたリスク検出を再アクティブ化することができます。 

![リスクの高いサインイン](./media/concept-user-at-risk/325.png)


## <a name="next-steps"></a>次の手順

- [ユーザー リスク ポリシーを構成する方法](../identity-protection/howto-user-risk-policy.md)
- [リスク修復ポリシーを構成する方法](../identity-protection/howto-user-risk-policy.md)
- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

