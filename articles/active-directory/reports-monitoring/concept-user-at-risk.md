---
title: Azure Active Directory ポータルのリスクのフラグ付きユーザー セキュリティ レポート | Microsoft Docs
description: Azure Active Directory ポータルのリスクのフラグ付きユーザー セキュリティ レポートについて説明します。
services: active-directory
author: markusvi
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/17/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 463f5c2d03cd96089342aa9b22ef85ebc05aa909
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60438132"
---
# <a name="users-flagged-for-risk-report-in-the-azure-portal"></a>Azure portal におけるリスクのフラグ付きユーザー レポート

Azure Active Directory (Azure AD) は、ユーザー アカウントに関連する疑わしい動作を検出します。 検出された動作ごとに、"[リスク イベント](concept-risk-events.md)" と呼ばれるレコードが作成されます。

セキュリティ レポートには、[Azure portal](https://portal.azure.com) から **[Azure Active Directory]** ブレードを選択し、 **[セキュリティ]** セクションに移動することでアクセスできます。 

検出されたリスク イベントは、以下のものの計算に使用されます。

- **リスクの高いサインイン** - リスクの高いサインインは、ユーザー アカウントの正当な所有者ではない人によって行われた可能性があるサインイン試行の指標です。 

- **リスクのフラグ付きユーザー** - リスクの高いユーザーは、侵害された可能性があるユーザー アカウントの指標です。 

これらのリスク イベントをトリガーするポリシーを構成する方法については、[ユーザー リスク ポリシーを構成する方法](../identity-protection/howto-user-risk-policy.md)に関するページを参照してください。 

![リスクの高いサインイン](./media/concept-user-at-risk/10.png)


## <a name="what-azure-ad-license-do-you-need-to-access-the-users-at-risk-report"></a>"危険な状態のユーザー" レポートにアクセスするために必要な Azure AD ライセンス  

"リスクのフラグ付きユーザー" レポートは、Azure Active Directory の全エディションで利用できます。 ただしエディションによってレポートの粒度が異なります。 

- リスクのフラグ付きユーザーは、**Azure Active Directory の Free エディションと Basic エディション**で一覧表示できます。 

- 加えて、**Azure Active Directory Premium 1** エディションでは、各レポートについて検出された、基になるリスク イベントの一部を調べることができます。 

- **Azure Active Directory Premium 2** エディションでは、基になるすべてのリスク イベントについて最も詳しい情報が得られます。また、構成されているリスク レベルに対して自動的に対応するセキュリティ ポリシーを構成することができます。


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

- 検出された[リスク イベントの種類](concept-risk-events.md)に関する集計情報

- レポートをダウンロードするオプション

- [ユーザー リスク修復ポリシー](../identity-protection/howto-user-risk-policy.md)を構成するオプション  

![リスクの高いサインイン](./media/concept-user-at-risk/71.png)

ユーザーを選択すると、そのユーザーの詳細なレポート ビューが表示されます。ここから次の機能を利用できます。

- [All sign-ins (すべてのサインイン)] ビューを開く

- ユーザーのパスワードをリセットする

- すべてのイベントを閉じる

- そのユーザーについて報告されたリスク イベントを調査する 

![リスクの高いサインイン](./media/concept-user-at-risk/324.png)

リスク イベントを調査するには、一覧からリスク イベントを 1 つ選択してその **[詳細]** ブレードを表示します。 **[詳細]** ブレードで、リスク イベントを手動で閉じるか、手動で閉じたリスク イベントを再アクティブ化することができます。 

![リスクの高いサインイン](./media/concept-user-at-risk/325.png)


## <a name="next-steps"></a>次の手順

- [ユーザー リスク ポリシーを構成する方法](../identity-protection/howto-user-risk-policy.md)
- [リスク修復ポリシーを構成する方法](../identity-protection/howto-user-risk-policy.md)
- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

