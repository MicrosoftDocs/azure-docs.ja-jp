---
title: レポート用の Azure Monitor ブック | Microsoft Docs
description: Azure Active Directory レポートに Azure Monitor ブックを使用する方法について説明します。
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/30/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: b84c38b28b51f867160272883465242fc81ff2bf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100588060"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Azure Active Directory レポートに Azure Monitor ブックを使用する方法

> [!IMPORTANT]
> このブック内の基になるクエリを最適化するには、[編集] をクリックし、[設定] アイコンをクリックして、これらのクエリを実行するワークスペースを選択してください。 Workbooks は、既定では、Azure AD ログをルーティングしているすべてのワークスペースを選択します。 

以下を行いたいですか?

- ユーザーのサインイン エクスペリエンスに与える[条件付きアクセス ポリシー](../conditional-access/overview.md)の影響を理解したい。

- サインイン エラーのトラブルシューティングを行って、組織のサインインの正常性をより理解し、問題をすばやく解決したい。

- レガシ認証を使用して環境にサインインしているユーザーを知りたい。 [レガシ認証をブロック](../conditional-access/block-legacy-authentication.md)することで、テナントの保護を向上させることができます。

- テナント内の条件付きアクセス ポリシーの影響を理解する必要がありますか。

- サインイン ログのクエリ、アクセスを付与または拒否されたユーザーの数や、リソースへのアクセス時に条件付きアクセス ポリシーをバイパスしたユーザーの数についてのブック レポートを確認する機能が必要ですか。

- ポリシーの影響をデバイスのプラットフォーム、デバイスの状態、クライアント アプリ、サインイン リスク、場所、アプリケーションなどの条件ごとにコンテキスト化できるように、条件ごとのブックの詳細をより深く理解することに関心がありますか。

- サインイン ログのクエリ、アクセスを付与または拒否されたユーザーの数や、リソースへのアクセス時に条件付きアクセス ポリシーをバイパスしたユーザーの数についてのブック レポートに関するより深い分析情報を取得したいですか。

- こうした疑問の解決を支援するために、Azure Active Directory には、監視のためのブックが用意されています。 [Azure Monitor ブック](../../azure-monitor/visualize/workbooks-overview.md)は、テキスト、分析クエリ、メトリック、パラメーターを組み合わせて内容豊富な対話型レポートを作成します。



この記事の内容は次のとおりです。

- [Monitor ブックを使用した対話型レポートの作成](../../azure-monitor/visualize/workbooks-overview.md)方法に精通していることを前提としています。

- Monitor ブックを使用して、条件付きアクセス ポリシーの影響を把握したり、サインイン エラーをトラブルシューティングしたり、レガシ認証を識別したりする方法について説明します。
 


## <a name="prerequisites"></a>前提条件

Monitor ブックを使用するためには、次のものが必要となります。

- プレミアム (P1 または P2) ライセンスがある Azure Active Directory テナント。 Premium ライセンスの取得方法については、[こちら](../fundamentals/active-directory-get-started-premium.md)をご覧ください。

- [Log Analytics ワークスペース。](../../azure-monitor/logs/quick-create-workspace.md)

- Log Analytics ワークスペースへの[アクセス](../../azure-monitor/logs/manage-access.md#manage-access-using-workspace-permissions)
- Azure Active Directory の次のロール (Azure Active Directory ポータルで Log Analytics にアクセスする場合)
    - セキュリティ管理者
    - セキュリティ閲覧者
    - レポート閲覧者
    - 全体管理者

## <a name="roles"></a>ロール
ブックを管理するには、次のいずれかのロールが割り当てられ、さらに[基の Log Analytics ワークスペースにアクセスできる](../../azure-monitor/logs/manage-access.md#manage-access-using-azure-permissions)必要があります。
-   全体管理者
-   セキュリティ管理者
-   セキュリティ閲覧者
-   レポート閲覧者
-   アプリケーション管理者

## <a name="workbook-access"></a>ブックへのアクセス 

ブックにアクセスするには:

1. [Azure portal](https://portal.azure.com) にサインインします。

1. **[Azure Active Directory]**  >  **[監視]**  >  **[Workbooks] (ブック)** に移動します。 

1. レポートまたはテンプレートを選択するか、ツール バーで **[Open]\(開く\)** を選択します。 

![Azure AD で Azure Monitor ブックを見つける](./media/howto-use-azure-monitor-workbooks/azure-monitor-workbooks-in-azure-ad.png)

## <a name="sign-in-analysis"></a>サインインの分析

サインインの分析ブックにアクセスするには、 **[使用状況]** セクションで **[サインイン]** を選択します。 

このブックには次のサインインの傾向が示されます。

- すべてのサインイン

- Success

- 保留中のユーザー アクション

- 障害

それぞれの傾向を、次のカテゴリでフィルター処理することができます。

- 時間の範囲

- アプリケーション

- ユーザー

![サインインの分析](./media/howto-use-azure-monitor-workbooks/43.png)


次のカテゴリ別の内訳が傾向ごとに得られます。

- 場所

    ![場所ごとのサインイン](./media/howto-use-azure-monitor-workbooks/45.png)

- Device

    ![デバイスごとのサインイン](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>レガシ認証を使用したサインイン 


[レガシ認証](../conditional-access/block-legacy-authentication.md)を使用したサインインのブックにアクセスするには、 **[使用状況]** セクションで **[レガシ認証を使用したサインイン]** を選択します。 

このブックには次のサインインの傾向が示されます。

- すべてのサインイン

- Success


それぞれの傾向を、次のカテゴリでフィルター処理することができます。

- 時間の範囲

- アプリケーション

- ユーザー

- プロトコル

![レガシ認証ごとのサインイン](./media/howto-use-azure-monitor-workbooks/47.png)


傾向ごとにアプリとプロトコル別の内訳が得られます。

![アプリとプロトコル別のレガシ認証サインイン](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>条件付きアクセスでのサインイン 


[条件付きアクセス ポリシー](../conditional-access/overview.md)によるサインインに関するブックにアクセスするには、 **[条件付きアクセス]** セクションで **[Sign-ins by Conditional Access]\(条件付きアクセスでのサインイン\)** を選択します。 

このブックには、無効化されたサインインの傾向が示されます。それぞれの傾向を、次のカテゴリでフィルター処理することができます。

- 時間の範囲

- アプリケーション

- ユーザー

![条件付きアクセスを使用したサインイン](./media/howto-use-azure-monitor-workbooks/49.png)


無効化されたサインインに対し、条件付きアクセスの状態別の内訳が得られます。

![[条件付きアクセスの状態] と [最近のサインイン] を示すスクリーンショット。](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)


## <a name="conditional-access-insights"></a>分析情報への条件付きアクセス

### <a name="overview"></a>概要

Workbooks には、IT 管理者がそのテナント内の条件付きアクセス ポリシーの影響を監視するために役立つサインイン ログのクエリが含まれています。 アクセスを付与または拒否されたユーザーの数に関して報告する機能があります。 ブックには、サインインの時点での各ユーザーの属性に基づいて条件付きアクセス ポリシーをバイパスしたユーザーの数に関する分析情報が含まれています。 ポリシーの影響をデバイスのプラットフォーム、デバイスの状態、クライアント アプリ、サインイン リスク、場所、アプリケーションなどの条件ごとにコンテキスト化できるように、これには条件ごとの詳細が含まれています。

### <a name="instructions"></a>Instructions 
分析情報への条件付きアクセスに関するブックにアクセスするには、[条件付きアクセス] セクションで **[分析情報への条件付きアクセス]** ブックを選択します。 このブックには、テナント内の各条件付きアクセス ポリシーの予測される影響が表示されます。 ドロップダウン リストから 1 つ以上の条件付きアクセス ポリシーを選択し、次のフィルターを適用してブックの範囲を絞り込みます。 

- **[時間の範囲]**

- **User**

- **Apps (アプリ)**

- **[Data View] (データ ビュー)**

![[条件付きアクセス] ウィンドウを示すスクリーンショット。ここでは、[条件付きアクセス ポリシー] を選択できます。](./media/howto-use-azure-monitor-workbooks/access-insights.png)


[影響の概要] には、選択されたポリシーが特定の結果をもたらしたユーザーまたはサインインの数が表示されます。 [合計] は、選択されたポリシーが、選択された [時間の範囲] で評価されたユーザーまたはサインインの数です。 タイルをクリックすると、ブック内のデータがその結果の種類でフィルター処理されます。 

![[合計]、[成功]、[エラー] などの結果のフィルター処理に使用されるタイルを示すスクリーンショット。](./media/howto-use-azure-monitor-workbooks/impact-summary.png)

このブックにはまた、次の 6 つの各条件で分類された、選択されたポリシーの影響も表示されます。 
- **[デバイスの状態]**
- **[デバイスのプラットフォーム]**
- **[クライアント アプリ]**
- **[サインイン リスク]**
- **場所**
- **アプリケーション**

![[合計] をサインインでフィルター処理した結果の詳細を示すスクリーンショット。](./media/howto-use-azure-monitor-workbooks/device-platform.png)

ブックで選択されたパラメーターでフィルター処理された個々のサインインを調査することもできます。 サインインの頻度で並べ替えられた個々のユーザーを検索し、対応するサインイン イベントを表示します。 

![確認できる個々のサインインを示すスクリーンショット。](./media/howto-use-azure-monitor-workbooks/filtered.png)

## <a name="sign-ins-by-grant-controls"></a>許可コントロールによるサインイン

[制御の許可](../conditional-access/controls.md)によるサインインに関するブックにアクセスするには、 **[条件付きアクセス]** セクションで **[Sign-ins by Grant Controls]\(制御の許可でのサインイン\)** を選択します。 

このブックには次の無効化されたサインインの傾向が示されます。

- Require MFA (MFA が必須)
 
- 利用規約が必須

- プライバシーに関する声明が必須

- その他


それぞれの傾向を、次のカテゴリでフィルター処理することができます。

- 時間の範囲

- アプリケーション

- ユーザー

![許可コントロールによるサインイン](./media/howto-use-azure-monitor-workbooks/50.png)


傾向ごとにアプリとプロトコル別の内訳が得られます。

![最近のサインインの内訳](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>サインイン エラー分析

**サインイン エラー分析** ブックを使用して、以下のエラーをトラブルシューティングします。

- サインイン
- 条件付きアクセス ポリシー
- レガシ認証 


条件付きアクセスのデータでサインインにアクセスするには、 **[トラブルシューティング]** セクションで **[Sign-ins using Legacy Authentication]\(レガシ認証を使用したサインイン\)** を選択します。 

このブックには次のサインインの傾向が示されます。

- すべてのサインイン

- Success

- 保留中のアクション

- 障害


それぞれの傾向を、次のカテゴリでフィルター処理することができます。

- 時間の範囲

- アプリケーション

- ユーザー

![サインインのトラブルシューティング](./media/howto-use-azure-monitor-workbooks/52.png)


サインインのトラブルシューティングを支援するために、Azure Monitor には、次のカテゴリごとの内訳が表示されます。

- 主なエラー

    ![エラーの概要](./media/howto-use-azure-monitor-workbooks/53.png)

- ユーザー アクションを待機しているサインイン

    ![ユーザー アクションを待機しているサインインの概要](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>次のステップ

[Monitor ブックを使用した対話型レポートの作成](../../azure-monitor/visualize/workbooks-overview.md)