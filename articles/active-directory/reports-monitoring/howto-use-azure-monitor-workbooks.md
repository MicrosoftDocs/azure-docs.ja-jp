---
title: Azure Active Directory レポートに Azure Monitor ブックを使用する | Microsoft Docs
description: Azure Active Directory レポートに Azure Monitor ブックを使用する方法について説明します。
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 9433714c06dfad09270a6033f38a99471bcd517a
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67513610"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Azure Active Directory レポートに Azure Monitor ブックを使用する方法

以下を行いたいですか?

- ユーザーのサインイン エクスペリエンスに与える[条件付きアクセス ポリシー](../conditional-access/overview.md)の影響を理解したい。

- サインイン エラーのトラブルシューティングを行って、組織のサインインの正常性をより理解し、問題をすばやく解決したい。

- レガシ認証を使用して環境にサインインしているユーザーを知りたい。 [レガシ認証をブロック](../conditional-access/block-legacy-authentication.md)することで、テナントの保護を向上させることができます。

こうした疑問の解決を支援するために、Active Directory には、監視のためのブックが用意されています。 [Azure Monitor ブック](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)は、テキスト、分析クエリ、メトリック、パラメーターを組み合わせて内容豊富な対話型レポートを作成します。 

この記事の内容は次のとおりです。

- [Monitor ブックを使用した対話型レポートの作成](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)方法に精通していることを前提としています。

- Monitor ブックを使用して、条件付きアクセス ポリシーの影響を把握したり、サインイン エラーをトラブルシューティングしたり、レガシ認証を識別したりする方法について説明します。
 


## <a name="prerequisites"></a>前提条件

Monitor ブックを使用するためには、次のものが必要となります。

- プレミアム (P1 または P2) ライセンスがある Active Directory テナント。 Premium ライセンスの取得方法については、[こちら](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium)をご覧ください。

- [Log Analytics ワークスペース。](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

## <a name="workbook-access"></a>ブックへのアクセス 

ブックにアクセスするには:

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. 左側のナビゲーション ウィンドウで **[Azure Active Directory]** を選択します。

3. **[監視]** セクションで **[ブック]** を選択します。 

    ![[Insights] を選択する](./media/howto-use-azure-monitor-workbooks/41.png)

4. レポートまたはテンプレートを選択するか、ツール バーで **[Open]\(開く\)** を選択します。 

    ![[Open]\(開く\) を選択する](./media/howto-use-azure-monitor-workbooks/42.png)


## <a name="sign-in-analysis"></a>サインインの分析

サインインの分析ブックにアクセスするには、 **[使用状況]** セクションで **[サインイン]** を選択します。 

このブックには次のサインインの傾向が示されます。

- すべてのサインイン

- Success

- 保留中のユーザー アクション

- 失敗

それぞれの傾向を、次のカテゴリでフィルター処理することができます。

- 時間範囲

- アプリケーション

- ユーザー

![サインインの分析](./media/howto-use-azure-monitor-workbooks/43.png)


次のカテゴリ別の内訳が傾向ごとに得られます。

- Location

    ![場所ごとのサインイン](./media/howto-use-azure-monitor-workbooks/45.png)

- Device

    ![デバイスごとのサインイン](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>レガシ認証を使用したサインイン 


[レガシ認証](../conditional-access/block-legacy-authentication.md)を使用したサインインのブックにアクセスするには、 **[使用状況]** セクションで **[レガシ認証を使用したサインイン]** を選択します。 

このブックには次のサインインの傾向が示されます。

- すべてのサインイン

- Success


それぞれの傾向を、次のカテゴリでフィルター処理することができます。

- 時間範囲

- アプリケーション

- ユーザー

- プロトコル

![レガシ認証ごとのサインイン](./media/howto-use-azure-monitor-workbooks/47.png)


傾向ごとにアプリとプロトコル別の内訳が得られます。

![アプリとプロトコル別のレガシ認証サインイン](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>条件付きアクセスでのサインイン 


[条件付きアクセス ポリシー](../conditional-access/overview.md)によるサインインに関するブックにアクセスするには、 **[条件付きアクセス]** セクションで **[Sign-ins by Conditional Access]\(条件付きアクセスでのサインイン\)** を選択します。 

このブックには、無効化されたサインインの傾向が示されます。それぞれの傾向を、次のカテゴリでフィルター処理することができます。

- 時間範囲

- アプリケーション

- ユーザー

![条件付きアクセスを使用したサインイン](./media/howto-use-azure-monitor-workbooks/49.png)


無効化されたサインインに対し、条件付きアクセスの状態別の内訳が得られます。

![条件付きアクセスの状態](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)








## <a name="sign-ins-by-grant-controls"></a>許可コントロールによるサインイン

[制御の許可](../conditional-access/controls.md)によるサインインに関するブックにアクセスするには、 **[条件付きアクセス]** セクションで **[Sign-ins by Grant Controls]\(制御の許可でのサインイン\)** を選択します。 

このブックには次の無効化されたサインインの傾向が示されます。

- Require MFA (MFA が必須)
 
- 利用規約が必須

- プライバシーに関する声明が必須

- その他


それぞれの傾向を、次のカテゴリでフィルター処理することができます。

- 時間範囲

- アプリケーション

- ユーザー

![許可コントロールによるサインイン](./media/howto-use-azure-monitor-workbooks/50.png)


傾向ごとにアプリとプロトコル別の内訳が得られます。

![最近のサインインの内訳](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>サインイン エラー分析

**サインイン エラー分析**ブックを使用して、以下のエラーをトラブルシューティングします。

- サインイン
- 条件付きアクセス ポリシー
- レガシ認証 


条件付きアクセスのデータでサインインにアクセスするには、 **[トラブルシューティング]** セクションで **[Sign-ins using Legacy Authentication]\(レガシ認証を使用したサインイン\)** を選択します。 

このブックには次のサインインの傾向が示されます。

- すべてのサインイン

- Success

- 保留中のアクション

- 失敗


それぞれの傾向を、次のカテゴリでフィルター処理することができます。

- 時間範囲

- アプリケーション

- ユーザー

![サインインのトラブルシューティング](./media/howto-use-azure-monitor-workbooks/52.png)


サインインのトラブルシューティングを支援するために、Azure Monitor には、次のカテゴリごとの内訳が表示されます。

- 主なエラー

    ![エラーの概要](./media/howto-use-azure-monitor-workbooks/53.png)

- ユーザー アクションを待機しているサインイン

    ![ユーザー アクションを待機しているサインインの概要](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>次の手順

[Monitor ブックを使用した対話型レポートの作成](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)
