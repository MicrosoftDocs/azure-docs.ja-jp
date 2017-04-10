---
title: "Azure Active Directory ポータルの危険な状態のユーザー セキュリティ レポート - プレビュー | Microsoft Docs"
description: "Azure Active Directory ポータルの危険な状態のユーザー セキュリティ レポート - プレビューについて"
services: active-directory
author: MarkusVi
manager: femila
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/21/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 349109e0c12a1394f96529a94ab884eeb451d242
ms.openlocfilehash: 48c504a9ed5bc4ef9f0bff889df031962c5bf6e8
ms.lasthandoff: 02/22/2017


---
# <a name="users-at-risk-security-report-in-the-azure-active-directory-portal---preview"></a>Azure Active Directory ポータルの危険な状態のユーザー セキュリティ レポート - プレビュー

Azure Active Directory [プレビュー](active-directory-preview-explainer.md)のセキュリティ レポートでは、環境内でユーザー アカウントが侵害されている確率を調べることができます。 

Azure Active Directory は、ユーザー アカウントに関連する疑わしい動作を検出します。 検出された動作ごとに、"*リスク イベント*" と呼ばれるレコードが作成されます。 詳細については、「[Azure Active Directory risk events (Azure Active Directory リスク イベント)](active-directory-identity-protection-risk-events.md)」を参照してください。 

検出されたリスク イベントは、以下のものの計算に使用されます。

- **リスクの高いサインイン** - リスクの高いサインインは、ユーザー アカウントの正当な所有者ではない人によって行われた可能性があるサインイン試行の指標です。 詳細については、「[Risky sign-ins (リスクの高いサインイン)](active-directory-identityprotection.md#risky-sign-ins)」を参照してください。 

- **リスクのフラグ付きユーザー** - リスクの高いユーザーは、侵害された可能性があるユーザー アカウントの指標です。 詳細については、「[Users flagged for risk (リスクのフラグ付きユーザー)](active-directory-identityprotection.md#users-flagged-for-risk)」を参照してください。  

Azure Portal では、**[Azure Active Directory]** ブレードの **[セキュリティ]** セクションで、セキュリティ レポートを確認できます。  

![リスクの高いサインイン](./media/active-directory-reporting-security-user-at-risk/10.png)

## <a name="azure-active-directory-free-and-basic-edition"></a>Azure Active Directory の Free および Basic エディション

Azure Active Directory の Free および Basic エディションの危険な状態のユーザー レポートでは、侵害された可能性があるユーザー アカウントの一覧を提供します。 


![リスクの高いサインイン](./media/active-directory-reporting-security-user-at-risk/03.png)

ユーザーを選択すると、関連するユーザー データ ブレードが開きます。
危険な状態のユーザーに関して、そのユーザーのサインイン履歴を確認し、必要に応じてパスワードをリセットすることができます。

![リスクの高いサインイン](./media/active-directory-reporting-security-user-at-risk/46.png)

## <a name="azure-active-directory-premium-editions"></a>Azure Active Directory Premium エディション

Azure Active Directory Premium エディションの危険な状態のユーザー レポートで提供される情報を以下に示します。

- 侵害された可能性のある[ユーザー アカウントの一覧](active-directory-identityprotection.md#users-flagged-for-risk) 

- 検出された[リスク イベントの種類](active-directory-identity-protection-risk-events.md)に関する集計情報

- レポートをダウンロードするオプション

- [ユーザー リスク修復ポリシー](active-directory-identityprotection.md#user-risk-security-policy)を構成するオプション  


![リスクの高いサインイン](./media/active-directory-reporting-security-user-at-risk/71.png)

ユーザーを選択すると、そのユーザーの詳細なレポート ビューが表示されます。ここから次の機能を利用できます。

- [All sign-ins (すべてのサインイン)] ビューを開く

- ユーザーのパスワードをリセットする

- すべてのイベントを閉じる

- そのユーザーについて報告されたリスク イベントを調査する 


![リスクの高いサインイン](./media/active-directory-reporting-security-user-at-risk/324.png)


リスク イベントを調査するには、一覧からリスク イベントを&1; つ選択します。  
このリスク イベントの **[詳細]** ブレードが開きます。 **[詳細]** ブレードで、[リスク イベントを手動で閉じる](active-directory-identityprotection.md#closing-risk-events-manually)か、手動で閉じたリスク イベントを再アクティブ化することができます。 


![リスクの高いサインイン](./media/active-directory-reporting-security-user-at-risk/325.png)



## <a name="next-steps"></a>次のステップ

- Azure Active Directory Identity Protection の詳細については、「[Azure Active Directory Identity Protection](active-directory-identityprotection.md)」を参照してください。


