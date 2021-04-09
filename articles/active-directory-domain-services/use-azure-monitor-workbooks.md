---
title: Azure AD Domain Services で Azure Monitor ブックを使用する | Microsoft Docs
description: Azure Monitor ブックを使用して、セキュリティ監査を確認し、Azure Active Directory Domain Services のマネージド ドメインの問題を把握する方法について説明します。
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 9a126fe4676f340fb45677382a3aeeed79291f0b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100574214"
---
# <a name="review-security-audit-events-in-azure-active-directory-domain-services-using-azure-monitor-workbooks"></a>Azure Monitor ブックを使用して Azure Active Directory Domain Services のセキュリティ監査イベントを確認する

Azure Active Directory Domain Services (Azure AD DS) のマネージド ドメインの状態を把握するために、セキュリティ監査イベントを有効にすることができます。 これらのセキュリティ監査イベントは、Azure Monitor ブックを使用して確認できます。このブックでは、テキスト、分析クエリ、およびパラメーターが、豊富な対話型レポートに結合されています。 Azure AD DS には、セキュリティの概要とアカウントのアクティビティに関するブック テンプレートが含まれており、監査イベントを掘り下げて環境を管理することができます。

この記事では、Azure Monitor ブックを使用して Azure AD DS のセキュリティ監査イベントを確認する方法について説明します。

## <a name="before-you-begin"></a>開始する前に

この記事を完了するには、以下のリソースと特権が必要です。

* 有効な Azure サブスクリプション
    * Azure サブスクリプションをお持ちでない場合は、[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* ご利用のサブスクリプションに関連付けられた Azure Active Directory テナント (オンプレミス ディレクトリまたはクラウド専用ディレクトリと同期されていること)。
    * 必要に応じて、[Azure Active Directory テナントを作成][create-azure-ad-tenant]するか、[ご利用のアカウントに Azure サブスクリプションを関連付け][associate-azure-ad-tenant]ます。
* Azure AD テナントで有効化され、構成された Azure Active Directory Domain Services のマネージド ドメイン。
    * 必要に応じて、[Azure Active Directory Domain Services マネージド ドメインを作成して構成する][create-azure-ad-ds-instance]チュートリアルを完了します。
* Log Analytics ワークスペースにデータをストリーミングする、マネージド ドメインに対して有効にされたセキュリティ監査イベント。
    * 必要な場合、[Azure AD DS でセキュリティ監査を有効にします][enable-security-audits]。

## <a name="azure-monitor-workbooks-overview"></a>Azure Monitor ブックの概要

Azure AD DS でセキュリティ監査イベントを有効にしたとき、マネージド ドメインでの問題の分析と特定が難しい場合があります。 Azure Monitor を使用すると、これらのセキュリティ監査イベントを集計し、データを照会することができます。 Azure Monitor ブックを使用すると、このデータを視覚化して、問題をすばやく簡単に識別できるようになります。

ブック テンプレートは、複数のユーザーとチームが柔軟に再利用できるよう設計されている、精選されたレポートです。 ブック テンプレートを開くと、Azure Monitor 環境からのデータが読み込まれます。 組織内の他のユーザーに影響を与えずにテンプレートを使用でき、テンプレートに基づいて独自のブックを保存することができます。

Azure AD DS には、次の 2 つのブック テンプレートが含まれています。

* セキュリティの概要レポート
* アカウント アクティビティ レポート

ブックを編集および管理する方法の詳細については、「[Azure Monitor ブックの概要](../azure-monitor/visualize/workbooks-overview.md)」を参照してください。

## <a name="use-the-security-overview-report-workbook"></a>セキュリティの概要レポート ブックを使用する

使用状況を理解し、潜在的なセキュリティ上の脅威を特定できるように、セキュリティの概要レポートでは、サインインのデータが集計されて、確認する必要があるアカウントが特定されます。 特定の日付範囲内のイベントを表示し、不正なパスワードの試行やアカウントの無効化など、特定のサインイン イベントにドリルダウンできます。

セキュリティの概要レポートのブック テンプレートにアクセスするには、次の手順のようにします。

1. Azure portal で、**Azure Active Directory Domain Services** を探して選択します。
1. 目的のマネージド ドメインを選択します (例: *aaddscontoso.com*)。
1. 左側のメニューで、 **[監視] > [ブック]** を選択します。

    ![セキュリティの概要レポートとアカウント アクティビティ レポートを選択する箇所が強調表示されている画面のスクリーンショット。](./media/use-azure-monitor-workbooks/select-workbooks-in-azure-portal.png)

1. **[Security Overview Report]\(セキュリティの概要レポート\)** を選択します。
1. ブックの上部にあるドロップダウン メニューから、Azure サブスクリプションを選択し、Azure Monitor ワークスペースを選択します。

    次の例のスクリーンショットに示すように、"*Last 7 days*" (過去 7 日) などの **[時間の範囲]** を選択します。

    ![Azure portal で [ブック] メニュー オプションを選択する](./media/use-azure-monitor-workbooks/select-query-filters.png)

    **[Tile view]\(タイル ビュー\)** および **[Chart view]\(グラフ ビュー\)** オプションを変更し、必要に応じてデータを分析して視覚化することもできます。

1. 特定のイベントの種類にドリルダウンするには、次の例に示すように、 *[Account Locked Out]\(アカウントのロックアウト\)* などの **[Sign-in result]\(サインイン結果\)** カードのいずれかを選択します。

    ![Azure Monitor ブックで視覚化されたセキュリティの概要レポートのデータの例](./media/use-azure-monitor-workbooks/example-security-overview-report.png)

1. グラフの下にあるセキュリティの概要レポートの下部には、選択したアクティビティの種類の詳細が表示されます。 次のレポート例に示すように、右側で関係するユーザー名を使用してフィルター処理できます。

    [![Azure Monitor ブックでのアカウント ロックアウトの詳細。](./media/use-azure-monitor-workbooks/account-lockout-details-cropped.png)](./media/use-azure-monitor-workbooks/account-lockout-details.png#lightbox)

## <a name="use-the-account-activity-report-workbook"></a>アカウント アクティビティ レポート ブックを使用する

特定のユーザー アカウントの問題のトラブルシューティングに役立つように、アカウント アクティビティ レポートでは、詳細な監査イベント ログ情報が表示されます。 サインイン時に無効なユーザー名またはパスワードが指定されたときと、サインイン試行のソースを確認できます。

アカウント アクティビティ レポートのブック テンプレートにアクセスするには、次の手順のようにします。

1. Azure portal で、**Azure Active Directory Domain Services** を探して選択します。
1. 目的のマネージド ドメインを選択します (例: *aaddscontoso.com*)。
1. 左側のメニューで、 **[監視] > [ブック]** を選択します。
1. **[Account Activity Report]\(アカウント アクティビティ レポート\)** を選択します。
1. ブックの上部にあるドロップダウン メニューから、Azure サブスクリプションを選択し、Azure Monitor ワークスペースを選択します。

    "*過去 30 日*" などの **[時間の範囲]** と、 **[Tile view]\(タイル ビュー\)** でデータを表示する方法を選択します。

    次のレポートの例に示すように、**アカウントのユーザー名** (*felix* など) でフィルター処理できます。

    [![Azure Monitor ブックでのアカウント アクティビティ レポート。](./media/use-azure-monitor-workbooks/account-activity-report-cropped.png)](./media/use-azure-monitor-workbooks/account-activity-report.png#lightbox)

    グラフの下の領域には、アクティビティの結果やソース ワークステーションなどの情報と共に、個々のサインイン イベントが表示されます。 この情報は、アカウントのロックアウトの原因になったり、潜在的な攻撃を示す可能性があるサインイン イベントを繰り返すソースを特定するのに役立ちます。

セキュリティの概要レポートと同様に、レポートの上部にあるさまざまなタイルをドリルダウンし、必要に応じてデータを視覚化して分析することができます。

## <a name="save-and-edit-workbooks"></a>ブックを保存および編集する

Azure AD DS によって提供されている 2 つのテンプレート ブックは、独自のデータ分析を始めるのに適しています。 データのクエリと調査をさらに細かく行う必要がある場合は、独自のブックを保存してクエリを編集することができます。

1. いずれかのブック テンプレートのコピーを保存するには、 **[編集] > [名前を付けて保存] > [共有レポート]** を選択し、名前を指定して保存します。
1. テンプレートの独自のコピーから、 **[編集]** を選択して編集モードに移行します。 レポートの任意の部分の横にある青い **[編集]** ボタンを選択して、その部分を変更できます。

Azure Monitor ブック内のすべてのグラフとテーブルは、Kusto クエリを使用して生成されます。 独自のクエリを作成する方法の詳細については、「[Azure Monitor ログ クエリ][azure-monitor-queries]」および [Kusto クエリのチュートリアル][kusto-queries]に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

パスワードとロックアウトのポリシーを調整する必要がある場合は、「[マネージド ドメインに関するパスワードとアカウントのロックアウト ポリシー][password-policy]」を参照してください。

ユーザーに関する問題については、[アカウントのサインインに関する問題][troubleshoot-sign-in]または[アカウントのロックアウトに関する問題][troubleshoot-account-lockout]をトラブルシューティングする方法を学習してください。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-security-audits]: security-audit-events.md
[password-policy]: password-policy.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-monitor-queries]: /azure/data-explorer/kusto/query/
[kusto-queries]: /azure/kusto/query/tutorial?pivots=azuredataexplorer