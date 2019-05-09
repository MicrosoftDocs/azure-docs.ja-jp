---
title: Azure Time Series Insights へのアクセスおよび管理用のセキュリティの構成 | Microsoft Docs
description: この記事では、管理アクセス ポリシーとデータ アクセス ポリシーとしてセキュリティとアクセス許可を構成し、Azure Time Series Insights プレビューを保護する方法を説明します。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/26/2018
ms.custom: seodec18
ms.openlocfilehash: 9aea7a9c9dd96bf30ebb3def9354df9e4bd30114
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2018
ms.locfileid: "53558524"
---
# <a name="grant-data-access-to-an-environment"></a>環境へのデータ アクセスの許可

この記事では、Azure Time Series Insights プレビューの 2 つのタイプのアクセス ポリシーについて説明します。

## <a name="grant-data-access"></a>データ アクセスの許可

次の手順に従って、ユーザー プリンシパルにデータ アクセスを許可します。

1. [Azure Portal](https://portal.azure.com/) にサインインします。
1. Time Series Insights 環境を検索します。 **[検索ボックス]** に「`Time Series`」と入力します。 検索結果の **[Time Series Environment]\(Time Series 環境\)** を選択します。
1. リストから Time Series Insights 環境を選択します。
1. **[データ アクセス ポリシー]** を選択し、**[+ 追加]** をクリックします。

    ![Data-access-one][1]

1. **[ユーザーの選択]** を選択します。 ユーザー名または電子メール アドレスを検索して追加するユーザーを探します。 **[選択]** をクリックして選択内容を確定します。

    ![Data-access-two][2]

1. **[ロールの選択]** を選択します。 ユーザーに割り当てる適切なアクセス ロールを選択します。

    * ユーザーに対して、参照データの変更に加え、保存クエリとパースペクティブを環境の他のユーザーと共有することを許可する場合は **[共同作成者]** を選択します。

    * それ以外の場合は **[閲覧者]** を選択し、環境内のデータへのクエリ実行と環境内の個人用 (共有されない) クエリの保存をユーザーに許可します。

   **[OK]** を選び、ロールの選択を確定します。

    ![Data-access-three][3]

1. **[ユーザー ロールの選択]** ページで **[OK]** を選択します。

    ![Data-access-four][4]

1. **[データ アクセス ポリシー]** ページに、ユーザーと各ユーザーのロールが一覧表示されていることを確認します。

    ![Data-access-five][5]

## <a name="provide-guest-access-to-a-user-from-another-azure-active-directory-tenant"></a>別の Azure Active Directory テナントからゲスト アクセスをユーザーに提供する

`Guest` は管理ロールではありません。 あるテナントから別のテナントに招待されたアカウントを示す用語として使用されます。 ゲスト アカウントは、テナントのディレクトリに招待されると、他のアカウントと同様に、同じアクセス制御を付与できます。 [アクセス制御 (IAM)] ブレードを使用して、Time Series Insights 環境に管理アクセスを許可できます。 または、[データ アクセス ポリシー] ブレードで、環境内のデータへのアクセスを許可できます。 Azure Active Directory (Azure AD) テナントのゲスト アクセスの詳細については、「[Azure Portal で Azure Active Directory B2B コラボレーション ユーザーを追加する](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator)」を参照してください。

別のテナントから Azure AD ユーザーに Time Series Insights 環境へのゲスト アクセスを付与するには、以下の手順を実行します。

1. [Azure Portal](https://portal.azure.com/) にサインインします。
1. Time Series Insights 環境を検索します。 **[検索]** ボックスに「**Time Series**」と入力します。 検索結果の **[Time Series Environment]\(Time Series 環境\)** を選択します。
1. リストから Time Series Insights 環境を選択します。
1. **[データ アクセス ポリシー]** を選択し、**[+ 招待]** をクリックします。

    ![Data-access-six][6]

1. 招待するユーザーの電子メール アドレスを入力します。 この電子メール アドレスは、Azure AD に関連付ける必要があります。 招待状に個人的なメッセージを含めることもできます。

    ![Data-access-seven][7]

1. 画面に表示される確認のバブルを探します。

    ![Data-access-eight][8]

1. **[ユーザーの選択]** を選択します。 招待したゲスト ユーザーの電子メール アドレスを検索し、追加するユーザーを見つけます。 **[選択]** をクリックして選択内容を確定します。

    ![Data-access-nine][9]

1. **[ロールの選択]** を選択します。 ゲスト ユーザーに割り当てる適切なアクセス ロールを選択します。

    * ユーザーに対して、参照データの変更に加え、保存クエリとパースペクティブを環境の他のユーザーと共有することを許可する場合は **[共同作成者]** を選択します。

    * それ以外の場合は **[閲覧者]** を選択し、環境内のデータへのクエリ実行と環境内の個人用 (共有されない) クエリの保存をユーザーに許可します。

   **[OK]** を選び、ロールの選択を確定します。

    ![Data-access-ten][10]

1. **[ユーザー ロールの選択]** ページで **[OK]** を選択します。

1. **[データ アクセス ポリシー]** ページに、ゲスト ユーザーと各ゲスト ユーザーのロールが一覧表示されていることを確認します。

    ![Data-access-eleven][11]

1. ゲスト ユーザーは、招待された Azure テナントに用意された環境にアクセスするために、いくつかの手順を踏む必要があります。 まず、先ほど送信した招待を承認します。 この招待は、ステップ 5 で使用した電子メール アドレスにメールで送信されます。 **[はじめに]** を選択して承認します。

    ![Data-access-twelve][12]

1. 次に、ゲスト ユーザーは、管理者の組織に関連付けられているアクセス許可を承認します。

    ![Data-access-thirteen][13]

1. 招待した電子メール アドレスにゲスト ユーザーがサインインして、招待を承認すると、そのユーザーは insights.azure.com に転送されます。 ここで、ユーザーは画面の右上に表示された自分の電子メール アドレスの横にあるアバターを選択します。

    ![Data-access-fourteen][14]

1. 次に、ゲスト ユーザーはディレクトリのドロップダウン メニューから Azure テナントを選択します。 このテナントは、招待したテナントです。

    ![Data-access-fifteen][15]

ゲスト ユーザーがテナントを選択した後に、アクセス権を付与した Time Series Insights 環境がゲスト ユーザーに表示されます。 ユーザーは、ステップ 8 で彼らに提供したロールに関連付けられたすべての能力を持つようになりました。

## <a name="next-steps"></a>次の手順

* [Time Series Insights 環境に Azure Event Hubs のイベント ソースを追加する方法](./time-series-insights-how-to-add-an-event-source-eventhub.md)について説明します。
* イベント ソースに[イベントを送信](./time-series-insights-send-events.md)します。
* [Time Series Insights プレビュー エクスプローラー](./time-series-insights-update-explorer.md)で環境を表示します。

<!-- Images -->
[1]: media/data-access/data-access-one.png
[2]: media/data-access/data-access-two.png
[3]: media/data-access/data-access-three.png
[4]: media/data-access/data-access-four.png
[5]: media/data-access/data-access-five.png
[6]: media/data-access/data-access-six.png
[7]: media/data-access/data-access-seven.png
[8]: media/data-access/data-access-eight.png
[9]: media/data-access/data-access-nine.png
[10]: media/data-access/data-access-ten.png
[11]: media/data-access/data-access-eleven.png
[12]: media/data-access/data-access-twelve.png
[13]: media/data-access/data-access-thirteen.png
[14]: media/data-access/data-access-fourteen.png
[15]: media/data-access/data-access-fifteen.png