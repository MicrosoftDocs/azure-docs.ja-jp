---
title: 使用状況と分析情報のレポート | Microsoft Docs
description: Azure Active Directory ポータルの使用状況と分析情報のレポートの概要
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 3fba300d-18fc-4355-9924-d8662f563a1f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 54bce5e839786862a6dac9aeb685dd364547a09a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98685032"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Azure Active Directory ポータルの使用状況と分析情報のレポート

使用状況と分析情報のレポートを使用すると、アプリケーションを中心にしてサインイン データを表示することができます。 次の質問への回答を導き出すことができます。

*   組織内で最もよく使われるアプリケーションはどれか。
*   サインインに最も失敗したアプリケーションはどれか。 
*   各アプリケーションの上位のサインイン エラーは何か。

## <a name="prerequisites"></a>前提条件 

使用状況と分析情報のレポートのデータにアクセスするには、次のものが必要です。

* Azure AD テナント
* サインイン データを表示するための Azure AD Premium (P1/P2) ライセンス
* グローバル管理者、セキュリティ管理者、セキュリティ閲覧者、またはレポート閲覧者のロールに含まれているユーザー。 さらに、任意のユーザー (非管理者) が自分のサインインにアクセス可能。 

## <a name="access-the-usage-and-insights-report"></a>使用状況と分析情報のレポートへのアクセス

1. [Azure Portal](https://portal.azure.com) に移動します。
2. 正しいディレクトリを選択して、 **[Azure Active Directory]** を選択し、 **[エンタープライズ アプリケーション]** を選択します。
3. **[アクティビティ]** セクションで、 **[使用状況と分析情報]** を選択してレポートを開きます。 

![スクリーンショットには、[アクティビティ] セクションで選択された [使用状況と分析情報] が示されています。](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>レポートを使用する

使用状況と分析情報のレポートには、1 回以上サインインが試行されたアプリケーションの一覧が表示され、成功したサインインの数、失敗したサインインの数、および成功率で並べ替えることができます。

一覧の下部にある **[さらに読みこむ]** をクリックすると、ページにその他のアプリケーションを表示できます。 日付範囲を選択して、その範囲内で使用されたすべてのアプリケーションを表示することができます。

![スクリーンショットには、範囲を選択してさまざまなアプリのサインイン アクティビティを表示できるアプリケーション アクティビティの [使用状況と分析情報] が示されています。](./media/concept-usage-insights-report/usage-and-insights-report.png)

また、特定のアプリケーションにフォーカスを設定することもできます。 アプリケーションの一定時間にわたるサインイン アクティビティと、上位のエラーを表示するには、 **[サインイン アクティビティを表示する]** を選択します。  

アプリケーション使用状況グラフ内の日付を選択すると、アプリケーションのサインイン アクティビティの詳細な一覧が表示されます。  

:::image type="content" source="./media/concept-usage-insights-report/usage-and-insights-application-report.png" alt-text="スクリーンショットには、特定のアプリケーションの使用状況と分析情報が表示されます。ここでは、サインイン アクティビティのグラフを確認できます。":::

## <a name="next-steps"></a>次のステップ

* [サインイン レポート](concept-sign-ins.md)
