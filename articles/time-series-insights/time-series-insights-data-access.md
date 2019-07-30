---
title: Azure Time Series Insights へのアクセスおよび管理用のセキュリティの構成 | Microsoft Docs
description: この記事では、管理アクセス ポリシーとデータ アクセス ポリシーとしてセキュリティとアクセス許可を構成し、Azure Time Series Insights プレビューを保護する方法を説明します。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/01/2019
ms.custom: seodec18
ms.openlocfilehash: 69180e17714b7d7004e63dce0de82a50e1f0b3af
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164624"
---
# <a name="grant-data-access-to-an-environment"></a>環境へのデータ アクセスの許可

この記事では、Azure Time Series Insights プレビューの 2 つのタイプのアクセス ポリシーについて説明します。

## <a name="sign-in-to-time-series-insights"></a>Time Series Insights へのサインイン

1. [Azure Portal](https://portal.azure.com/) にサインインします。
1. Time Series Insights 環境を検索します。 **[検索ボックス]** に「`Time Series`」と入力します。 検索結果の **[Time Series Environment]\(Time Series 環境\)** を選択します。
1. リストから Time Series Insights 環境を選択します。

## <a name="grant-data-access"></a>データ アクセスの許可

次の手順に従って、ユーザー プリンシパルにデータ アクセスを許可します。

1. **[データ アクセス ポリシー]** を選択し、 **[+ 追加]** をクリックします。

    [![Data-access-one](media/data-access/data-access-one.png)](media/data-access/data-access-one.png#lightbox)

1. **[ユーザーの選択]** を選択します。 ユーザー名または電子メール アドレスを検索して追加するユーザーを探します。 **[選択]** を選択して選択内容を確定します。

    [![Data-access-two](media/data-access/data-access-two.png)](media/data-access/data-access-two.png#lightbox)

1. **[ロールの選択]** を選択します。 ユーザーに割り当てる適切なアクセス ロールを選択します。

    * ユーザーに対して、参照データの変更に加え、保存クエリとパースペクティブを環境の他のユーザーと共有することを許可する場合は **[共同作成者]** を選択します。

    * それ以外の場合は **[閲覧者]** を選択し、環境内のデータへのクエリ実行と環境内の個人用 (共有されない) クエリの保存をユーザーに許可します。

   **[OK]** を選び、ロールの選択を確定します。

    [![Data-access-three](media/data-access/data-access-three.png)](media/data-access/data-access-three.png#lightbox)

1. **[ユーザー ロールの選択]** ページで **[OK]** を選択します。

    [![Data-access-four](media/data-access/data-access-four.png)](media/data-access/data-access-four.png#lightbox)

1. **[データ アクセス ポリシー]** ページに、ユーザーと各ユーザーのロールが一覧表示されていることを確認します。

    [![Data-access-five](media/data-access/data-access-five.png)](media/data-access/data-access-five.png#lightbox)

## <a name="provide-guest-access-from-another-aad-tenant"></a>別の AAD テナントからのゲスト アクセスの提供

`Guest` は管理ロールではありません。 あるテナントから別のテナントに招待されたアカウントを示す用語として使用されます。 ゲスト アカウントは、テナントのディレクトリに招待されると、他のアカウントと同様に、同じアクセス制御を付与できます。 [アクセス制御 (IAM)] ブレードを使用して、Time Series Insights 環境に管理アクセスを許可できます。 または、[データ アクセス ポリシー] ブレードで、環境内のデータへのアクセスを許可できます。 Azure Active Directory (Azure AD) テナントのゲスト アクセスの詳細については、「[Azure Portal で Azure Active Directory B2B コラボレーション ユーザーを追加する](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator)」を参照してください。

別のテナントから Azure AD ユーザーに Time Series Insights 環境へのゲスト アクセスを付与するには、以下の手順を実行します。

1. **[データ アクセス ポリシー]** を選択し、 **[+ 招待]** をクリックします。

    [![Data-access-six](media/data-access/data-access-six.png)](media/data-access/data-access-six.png#lightbox)

1. 招待するユーザーの電子メール アドレスを入力します。 この電子メール アドレスは、Azure AD に関連付ける必要があります。 招待状に個人的なメッセージを含めることもできます。

    [![Data-access-seven](media/data-access/data-access-seven.png)](media/data-access/data-access-seven.png#lightbox)

1. 画面に表示される確認のバブルを探します。

    [![Data-access-eight](media/data-access/data-access-eight.png)](media/data-access/data-access-eight.png#lightbox)

1. **[ユーザーの選択]** を選択します。 招待したゲスト ユーザーの電子メール アドレスを検索し、追加するユーザーを見つけます。 次に、 **[選択]** を選択して選択内容を確定します。

    [![Data-access-nine](media/data-access/data-access-nine.png)](media/data-access/data-access-nine.png#lightbox)

1. **[ロールの選択]** を選択します。 ゲスト ユーザーに割り当てる適切なアクセス ロールを選択します。

    * ユーザーに対して、参照データの変更に加え、保存クエリとパースペクティブを環境の他のユーザーと共有することを許可する場合は **[共同作成者]** を選択します。

    * それ以外の場合は **[閲覧者]** を選択し、環境内のデータへのクエリ実行と環境内の個人用 (共有されない) クエリの保存をユーザーに許可します。

   **[OK]** を選び、ロールの選択を確定します。

    [![Data-access-ten](media/data-access/data-access-ten.png)](media/data-access/data-access-ten.png#lightbox)

1. **[ユーザー ロールの選択]** ページで **[OK]** を選択します。

1. **[データ アクセス ポリシー]** ページに、ゲスト ユーザーと各ゲスト ユーザーのロールが一覧表示されていることを確認します。

    [![Data-access-eleven](media/data-access/data-access-eleven.png)](media/data-access/data-access-eleven.png#lightbox)

1. ゲスト ユーザーは、招待された Azure テナントに用意された環境にアクセスするために、いくつかの手順を踏む必要があります。 まず、先ほど送信した招待を承認します。 この招待は、ステップ 5 で使用した電子メール アドレスにメールで送信されます。 **[はじめに]** を選択して承認します。

    [![Data-access-twelve](media/data-access/data-access-twelve.png)](media/data-access/data-access-twelve.png#lightbox)

1. 次に、ゲスト ユーザーは、管理者の組織に関連付けられているアクセス許可を承認します。

    [![Data-access-thirteen](media/data-access/data-access-thirteen.png)](media/data-access/data-access-thirteen.png#lightbox)

1. 招待した電子メール アドレスにゲスト ユーザーがサインインして、招待を承認すると、そのユーザーは insights.azure.com に転送されます。 ここで、ユーザーは画面の右上に表示された自分の電子メール アドレスの横にあるアバターを選択します。

    [![Data-access-fourteen](media/data-access/data-access-fourteen.png)](media/data-access/data-access-fourteen.png#lightbox)

1. 次に、ゲスト ユーザーはディレクトリのドロップダウン メニューから Azure テナントを選択します。 このテナントは、招待したテナントです。

    [![Data-access-fifteen](media/data-access/data-access-fifteen.png)](media/data-access/data-access-fifteen.png#lightbox)

ゲスト ユーザーがテナントを選択した後に、アクセス権を付与した Time Series Insights 環境がゲスト ユーザーに表示されます。 ユーザーは、**手順 5.** で提供されたロールに関連付けられているすべての能力を持つようになりました。

## <a name="next-steps"></a>次の手順

* [Time Series Insights 環境に Azure Event Hubs のイベント ソースを追加する方法](./time-series-insights-how-to-add-an-event-source-eventhub.md)について説明します。

* イベント ソースに[イベントを送信](./time-series-insights-send-events.md)します。

* [Time Series Insights プレビュー エクスプローラー](./time-series-insights-update-explorer.md)で環境を表示します。
