---
title: 条件付きアクセスに関する分析情報とレポートのブック - Azure Active Directory
description: Azure AD の条件付きアクセスに関する分析情報とレポートのブックを使用した、ポリシーのトラブルシューティング
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae802038626a1fbf8d533800a0b8eb43c4565e8c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100574183"
---
# <a name="conditional-access-insights-and-reporting"></a>条件付きアクセスに関する分析情報とレポート

条件付きアクセスに関する分析情報とレポートのブックを使用すると、自分の組織内での一定期間にわたる条件付きアクセス ポリシーの影響を把握できます。 サインイン時に 1 つまたは複数の条件付きアクセス ポリシーが適用されて、特定の許可の制御が満たされた場合はアクセスが許可され、そうでない場合はアクセスが拒否されます。 サインインのたびに複数の条件付きアクセス ポリシーが評価される可能性があるため、分析情報とレポートのブックを使用すると、個々のポリシーまたはすべてのポリシーのサブセットの影響を調べることができます。  

## <a name="prerequisites"></a>前提条件

分析情報とレポートのブックを有効にするには、テナントにサインイン ログ データを保持するための Log Analytics ワークスペースが必要です。 条件付きアクセスを使用するには、ユーザーが Azure AD Premium P1 または P2 ライセンスを持っている必要があります。

分析情報とレポートにアクセスできるロールは、次のとおりです。  

- 条件付きアクセス管理者 
- セキュリティ閲覧者 
- セキュリティ管理者 
- グローバル閲覧者 
- 全体管理者 

ユーザーには、次のいずれかの Log Analytics ワークスペース ロールも必要です。  

- Contributor  
- 所有者 

### <a name="stream-sign-in-logs-from-azure-ad-to-azure-monitor-logs"></a>Azure AD から Azure Monitor ログにサインイン ログをストリーム配信する 

Azure AD ログを Azure Monitor ログと統合していない場合は、ブックを読み込む前に以下の手順を実行する必要があります。  

1. [Azure Monitor で Log Analytics ワークスペースを作成する](../../azure-monitor/logs/quick-create-workspace.md)。
1. [Azure AD ログを Azure Monitor ログと統合する](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)。

## <a name="how-it-works"></a>しくみ 

分析情報とレポートのブックにアクセスするには、次の手順に従います。  

1. **Azure portal** にサインインします。
1. **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[条件付きアクセス]**  >  **[分析情報とレポート]** の順に移動します。

### <a name="get-started-select-parameters"></a>作業を開始しましょう。パラメーターを選択する 

分析情報とレポートのダッシュボードを使用すると、指定した期間にわたる 1 つ以上の条件付きアクセス ポリシーの影響を確認できます。 最初に、ブックの先頭の各パラメーターを設定します。 

![Azure portal における条件付きアクセスに関する分析情報とレポートのダッシュボード](./media/howto-conditional-access-insights-reporting/conditional-access-insights-and-reporting-dashboard.png)

**[条件付きアクセス ポリシー]** : 1 つまたは複数の条件付きアクセス ポリシーを選択して、その組み合わせの影響を表示します。 ポリシーは、次の 2 つのグループに分けることができます。有効になっているポリシーとレポート専用ポリシーです。 既定では、有効になっているすべてのポリシーが選択されています。 これらの有効になっているポリシーは、テナントに現在適用されているポリシーです。  

**[時間範囲]** : 4 時間から 90 日前までの時間の範囲を選択します。 Azure AD ログを Azure Monitor と統合したときよりも前までの時間の範囲を選択した場合は、統合時以降のサインインのみが表示されます。  

**[ユーザー]** :既定では、ダッシュボードには、すべてのユーザーに対する選択したポリシーの影響が表示されます。 個々のユーザーでフィルター処理を行うには、テキスト フィールドにユーザーの名前を入力します。 すべてのユーザーでフィルター処理を行うには、テキスト フィールドに「すべてのユーザー」と入力するか、パラメーターを空のままにします。 

**[アプリ]** : 既定では、ダッシュボードには、すべてのアプリに対する、選択したポリシーの影響が表示されます。 個々のアプリでフィルター処理を行うには、テキスト フィールドにアプリの名前を入力します。 すべてのアプリでフィルター処理を行うには、テキスト フィールドに「すべてのアプリ」と入力するか、パラメーターを空のままにします。 

**[データ ビュー]** : ユーザー数とサインイン数のどちらの観点からダッシュボードに結果を表示するかを選択します。個々のユーザーは、特定の時間の範囲内に多数のアプリに対して何百回もサインインを行い、その結果はさまざまになる可能性があります。 ユーザー数のデータ ビューを選択すると、ユーザーが成功と失敗の両方のカウントに含まれる可能性があります (たとえば、10 人のユーザーがいる場合、そのうちの 8 人が過去 30 日間に成功したことがあり、そのうちの 9 人が過去 30 日以内に失敗したことがあるかもしれません)。

## <a name="impact-summary"></a>影響の概要 

パラメーターが設定されると、影響の概要が読み込まれます。 概要には、選択したポリシーが評価されたときに、特定の時間の範囲内にどのくらいの数のユーザーまたはサインインが "成功"、"失敗"、"ユーザー アクションが必要です"、または "適用されていません" になったかが表示されます。  

![条件付きアクセスのブックでの影響の概要](./media/howto-conditional-access-insights-reporting/workbook-impact-summary.png)

**Total**: 期間内に、選択したポリシーのうち少なくとも 1 つが評価されたユーザーまたはサインインの数。

**成功**:期間内に、選択したポリシーの結合結果が "成功" または "レポート専用: 成功" であったユーザーまたはサインインの数。

**失敗**:期間内に、選択したポリシーのうち少なくとも 1 つの結果が "失敗" または "レポート専用: 失敗" であったユーザーまたはサインインの数。

**ユーザー アクションが必要です**: 期間内に、選択したポリシーの結合結果が "レポート専用: ユーザー操作が必要です" であったユーザーまたはサインインの数。 レポート専用の条件付きアクセス ポリシーで、多要素認証などの対話型の許可制御が必要な場合は、ユーザー操作が必要です。 対話型の許可制御はレポート専用のポリシーによって強制されないため、成功または失敗を判断できません。  

**適用されていません**:期間内に、選択したポリシーのいずれも適用されなかったユーザーまたはサインインの数。

### <a name="understanding-the-impact"></a>影響の理解 

![ブックでの条件および状態ごとの内訳](./media/howto-conditional-access-insights-reporting/workbook-breakdown-condition-and-status.png)

条件ごとのユーザーまたはサインインの内訳を確認できます。 ブックの上部にある [概要] タイルでの選択により、特定の結果 (成功や失敗など) のサインインをフィルター処理可能です。 条件付きアクセスの条件 (デバイスの状態、デバイス プラットフォーム、クライアント アプリ、場所、アプリケーション、サインイン リスクなど) ごとに、サインインの内訳を確認できます。  

## <a name="sign-in-details"></a>サインインの詳細 

![ブックでのサインインの詳細](./media/howto-conditional-access-insights-reporting/workbook-sign-in-details.png)

また、ダッシュボードの下部でサインインを検索して、特定のユーザーのサインインを調査することもできます。 左側のクエリでは、頻度が最も高いユーザーが表示されます。 ユーザーを選択すると、クエリがフィルター処理されて右側に表示されます。  

> [!NOTE]
> サインイン ログをダウンロードする場合は、条件付きアクセスの レポート専用の結果データを含めるための JSON 形式を選択します。

## <a name="configure-a-conditional-access-policy-in-report-only-mode"></a>レポートのみモードで条件付きアクセス ポリシーを構成する

レポートのみモードで条件付きアクセス ポリシーを構成するには:

1. **Azure portal** に、条件付きアクセス管理者、セキュリティ管理者、またはグローバル管理者としてサインインします。
1. **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[条件付きアクセス]** の順に移動します。
1. 既存のポリシーを選択するか、新しいポリシーを作成します。
1. **[ポリシーの有効化]** の下で、トグルを **[レポートのみ]** モードに設定します。
1. **[保存]** を選びます。

> [!TIP]
> 既存のポリシーの **[ポリシーの有効化]** の状態を **[オン]** から **[レポート専用]** に編集すると、既存のポリシーの適用が無効になります。 

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="why-are-queries-failing-due-to-a-permissions-error"></a>アクセス許可エラーに起因してクエリに失敗するのはなぜですか。

ブックにアクセスするには、適切な Azure AD アクセス許可と Log Analytics ワークスペース アクセス許可が必要です。 サンプルのログ分析クエリを実行し、適切なワークスペース アクセス許可が与えられているかどうかをテストするには:

1. **Azure portal** にサインインします。
1. **[Azure Active Directory]** 、 **[ログ]** の順に移動します。
1. クエリ ボックスに「`SigninLogs`」と入力し、 **[実行]** を選択します。
1. クエリから何の結果も返されない場合、ワークスペースに正しく構成されていない可能性があります。 

![クエリ失敗の問題解決](./media/howto-conditional-access-insights-reporting/query-troubleshoot-sign-in-logs.png)

Azure AD サインイン ログを Log Analytics ワークスペースにストリーミングする方法の詳細については、「[Azure AD ログを Azure Monitor ログと統合する](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)」を参照してください。

### <a name="why-are-the-queries-in-the-workbook-failing"></a>ブック内のクエリがエラーになるのはなぜですか?

お客様は、誤ったワークスペースや複数のワークスペースがブックに関連付けられている場合に、クエリがエラーになることがあるとお気付きになりました。 この問題を解決するには、ブックの最上部にある **[編集]** をクリックして、設定の歯車をクリックします。 ブックに関連付けられていないワークスペースを選択して削除します。 各ブックに関連付けられているワークスペースは 1 つだけになります。

### <a name="why-is-the-conditional-access-policies-parameter-is-empty"></a>条件付きアクセス ポリシー パラメーターが空であるのはなぜですか。

ポリシーの一覧は、最も新しいサインイン イベントに対して評価されたポリシーを調べることで生成されます。 テナントに新しいサインインがないとき、場合によっては、条件付きアクセス ポリシーの一覧がブックに読み込まれるまで数分待つ必要があります。 これは Log Analytics を構成した直後にありえることです。あるいは、テナントに最近、サインイン アクティビティがない場合、もっと時間がかかることがあります。

### <a name="why-is-the-workbook-taking-a-long-time-to-load"></a>ブックの読み込みに長い時間がかかるのはなぜですか。  

選択した時間の範囲とテナントのサイズによっては、膨大な数のサインイン イベントがブックで評価されている可能性があります。 大きなテナントの場合、サインインの回数が Log Analytics のクエリ処理能力を超える可能性があります。 時間の範囲を 4 時間に短縮して、ブックが読み込まれるかどうかを確認してみてください。  

### <a name="after-loading-for-a-few-minutes-why-is-the-workbook-returning-zero-results"></a>数分の読み込み後に、ブックから結果が 1 件も返されないのはなぜですか。 

サインインの回数が Log Analytics のクエリ処理能力を超えると、ブックから結果が 1 件も返されません。 時間の範囲を 4 時間に短縮して、ブックが読み込まれるかどうかを確認してみてください。  

### <a name="can-i-save-my-parameter-selections"></a>パラメーターの選択を保存することはできますか。  

ブックの上部にあるパラメーターの選択を保存するには、 **[Azure Active Directory]**  >  **[ブック]**  >  **[Conditional Access Insights and reporting]\(条件付きアクセスの分析情報とレポート\)** の順に移動します。 ここにはブック テンプレートがあり、ブックを編集したり、パラメーターの選択も含めたコピーをワークスペースの **[個人用レポート]** または **[共有レポート]** に保存したりすることができます。 

### <a name="can-i-edit-and-customize-the-workbook-with-additional-queries"></a>追加のクエリを使用してブックを編集およびカスタマイズすることはできますか。 

ブックを編集およびカスタマイズするには、 **[Azure Active Directory]**  >  **[ブック]**  >  **[Conditional Access Insights and reporting]\(条件付きアクセスの分析情報とレポート\)** の順に移動します。 ここにはブック テンプレートがあり、ブックを編集したり、パラメーターの選択も含めたコピーをワークスペースの **[個人用レポート]** または **[共有レポート]** に保存したりすることができます。 クエリの編集を開始するには、ブックの上部にある **[編集]** をクリックします。  
 
## <a name="next-steps"></a>次のステップ

- [条件付きアクセスのレポート専用モード](concept-conditional-access-report-only.md)

- Azure AD ブックに関する詳細については、[Azure Active Directory レポートに Azure Monitor ブックを使用する方法](../reports-monitoring/howto-use-azure-monitor-workbooks.md)に関する記事を参照してください。

- [Conditional Access common policies](concept-conditional-access-policy-common.md) (条件付きアクセスの一般的なポリシー)
