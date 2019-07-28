---
title: Azure Active Directory ポータルのリスクの高いサインイン レポート | Microsoft Docs
description: Azure Active Directory ポータルのリスクの高いサインイン レポートについて説明します。
services: active-directory
author: markusvi
manager: daveba
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40e125f8e1e7909c5866a03c0571f49ec42d690a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60287524"
---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Azure Active Directory ポータルのリスクの高いサインイン レポート

Azure Active Directory (Azure AD) は、ユーザー アカウントに関連する疑わしい動作を検出します。 検出された動作ごとに、"**リスク イベント**" と呼ばれるレコードが作成されます。 詳細については、「[Azure AD リスク イベント](concept-risk-events.md)」を参照してください。 

セキュリティ レポートには、[Azure portal](https://portal.azure.com) から **[Azure Active Directory]** ブレードを選択し、 **[セキュリティ]** セクションに移動することでアクセスできます。 

リスク イベントに基づいて計算されるセキュリティ レポートとして、次に示す異なる 2 つのレポートがあります。

- **リスクの高いサインイン** - リスクの高いサインインは、ユーザー アカウントの正当な所有者ではない人によって行われた可能性があるサインイン試行の指標です。

- **リスクのフラグ付きユーザー** - リスクの高いユーザーは、侵害された可能性があるユーザー アカウントの指標です。 

![リスクの高いサインイン](./media/concept-risky-sign-ins/10.png)

これらのリスク イベントをトリガーするポリシーを構成する方法については、[ユーザー リスク ポリシーを構成する方法](../identity-protection/howto-user-risk-policy.md)に関するページを参照してください。  

## <a name="who-can-access-the-risky-sign-ins-report"></a>リスクの高いサインイン レポートにアクセスできるユーザー

リスクの高いサインイン レポートは、次の役割のユーザーが利用できます。

- セキュリティ管理者
- グローバル管理者
- セキュリティ閲覧者

Azure Active Directory でユーザーに管理者ロールを割り当てる方法については、[Azure Active Directory での管理者ロールの表示と割り当て](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-manage-roles-portal)に関するページを参照してください。

## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>セキュリティ レポートにアクセスするために必要な Azure AD ライセンス  

"危険なサインイン" レポートは、Azure AD の全エディションで利用できます。 ただしエディションによってレポートの粒度が異なります。 

- **Azure Active Directory Free エディションと Basic エディション**では、危険なサインインの一覧を取得できます。 

- 加えて、**Azure Active Directory Premium 1** エディションでは、各レポートについて検出された、基になるリスク イベントの一部を調べることができます。 

- **Azure Active Directory Premium 2** エディションでは、基になるすべてのリスク イベントについて最も詳しい情報が得られます。また、構成されているリスク レベルに対して自動的に対応するセキュリティ ポリシーを構成することができます。

## <a name="risky-sign-ins-report-for-azure-ad-free-and-basic-edition"></a>Azure AD の Free エディションと Basic エディションにおける危険なサインイン レポート

Azure AD の Free および Basic エディションでは、管理対象ユーザーに関して検出された、リスクの高いサインインの一覧を提供します。 各レコードには、次の属性が含まれています。

- **ユーザー** - サインイン操作中に使用されたユーザーの名前。
- **IP** - Azure Active Directory への接続に使用されたデバイスの IP アドレス。
- **場所** - Azure Active Directory への接続に使用された場所。 これは、トレース、レジストリ データ、逆引き参照、およびその他の情報に基づくベスト エフォートに似ています。
- **サインイン時刻** - サインインが実行された時刻
- **状態** - サインインの状態

![リスクの高いサインイン](./media/concept-risky-sign-ins/01.png)

リスクの高いサインインの調査に基づき、次のアクションを実行して Azure AD にフィードバックを提出できます。

- 解決
- 誤検知に設定
- 無視
- 再度有効にする

![リスクの高いサインイン](./media/concept-risky-sign-ins/21.png)

また、このレポートでは、次の操作も利用できます。

- リソースの検索
- レポート データのダウンロード

![リスクの高いサインイン](./media/concept-risky-sign-ins/93.png)

## <a name="risky-sign-ins-report-for-azure-ad-premium-editions"></a>Azure AD Premium エディションにおける危険なサインイン レポート

Azure AD Premium エディションのリスクの高いサインイン レポートでは、次の情報を提供しています。

- 検出された[リスク イベントの種類](concept-risk-events.md)に関する集計情報。 **Azure AD Premium P1 エディション**では、ライセンスに含まれない検出は、**追加のリスクが検出されたサインイン**というリスク イベントとして表示されます。 **Azure AD Premium P2 エディション**では、基になるすべての検出に関する最も詳細な情報を取得できます。

- レポートをダウンロードするオプション

![リスクの高いサインイン](./media/concept-risky-sign-ins/456.png)

リスク イベントを選択すると、そのリスク イベントの詳細なレポート ビューが表示されます。ここから次の機能を利用できます。

- [ユーザー リスク修復ポリシー](../identity-protection/howto-user-risk-policy.md)を構成するオプション  

- リスク イベントの検出のタイムラインを確認する  

- このリスク イベントが検出されたユーザーの一覧を確認する

- リスク イベントを手動で閉じる。 

![リスクの高いサインイン](./media/concept-risky-sign-ins/457.png)

> [!IMPORTANT]
> 場合によっては、[サインイン レポート](concept-sign-ins.md)に対応するサインイン エントリがないリスク イベントが見つかることがあります。 これは、Identity Protection が **対話型**と**非対話型**の両方のサインインのリスクを評価する一方、サインイン レポートには対話型サインインのみが表示されるためです。

ユーザーを選択すると、そのユーザーの詳細なレポート ビューが表示されます。ここから次の機能を利用できます。

- [All sign-ins (すべてのサインイン)] ビューを開く

- ユーザーのパスワードをリセットする

- すべてのイベントを閉じる

- そのユーザーについて報告されたリスク イベントを調査する 

![リスクの高いサインイン](./media/concept-risky-sign-ins/324.png)

リスク イベントを調査するには、一覧からリスク イベントを 1 つ選択します。  
このリスク イベントの **[詳細]** ブレードが開きます。 **[詳細]** ブレードで、リスク イベントを手動で閉じるか、手動で閉じたリスク イベントを再アクティブ化することができます。 

![リスクの高いサインイン](./media/concept-risky-sign-ins/325.png)

## <a name="next-steps"></a>次の手順

- [ユーザー リスク ポリシーを構成する方法](../identity-protection/howto-user-risk-policy.md)
- [リスク修復ポリシーを構成する方法](../identity-protection/howto-user-risk-policy.md)
- [リスク イベントの種類](concept-risk-events.md)
