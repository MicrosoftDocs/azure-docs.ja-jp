---
title: "Azure Active Directory Premium の概要"
description: "このトピックでは、ボリューム ライセンスの Web サイトを通じて Azure Active Directory Premium Edition にサインアップする方法について説明します。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
ms.assetid: 391764e5-c4eb-4ae6-b8a9-3d09f0de04a6
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/24/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 73cbbce9ae3d727632b8cac2a29c2b1b92332ae7
ms.lasthandoff: 12/08/2016


---
# <a name="getting-started-with-azure-active-directory-premium"></a>Azure Active Directory Premium の概要
Active Directory Premium にはいくつかの方法でサインアップできます。 

**Azure または Office 365** - Azure または Office 365 のサブスクライバーである場合、Active Directory Premium をオンラインで購入できます。 詳細な手順については、「[How to Purchase Azure Active Directory Premium - Existing Customers (Azure Active Directory Premium を購入する方法 - 既存のお客様)](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-Existing-Customer)」または「[How to Purchase Azure Active Directory Premium - New Customers (Azure Active Directory Premium を購入する方法 - 新規のお客様)](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-New-Customers)」をご覧ください。  

**Enterprise Mobility + Security** - Enterprise Mobility + Security (以前の Enterprise Mobility Suite) は、Active Directory Premium、Azure Rights Management、および Microsoft Intune のサービスを 1 つのライセンス プランでまとめて使用できる、組織にとってコスト効果の高い方法です。 詳細については、[Enterprise Mobility + Security](https://www.microsoft.com/en-us/server-cloud/enterprise-mobility/overview.aspx) の Web サイトをご覧ください。 30 日間の無料試用を開始するには、 [こちら](https://portal.office.com/Signup/Signup.aspx?OfferId=2E63A04D-BE0B-4A0F-A8CF-407C1C299221&dl=EMS&ali=1#0)をクリックしてください。

**マイクロソフト ボリューム ライセンス** - Azure Active Directory Premium は、[Microsoft Enterprise Agreement](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx) (250 個以上のライセンス) または [Open Volume License](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx) (5 ～ 250 個のライセンス) プログラムで購入できます。

このトピックでは、ボリューム ライセンス プログラムで購入した Azure Active Directory Premium の使用を開始する方法について説明します。 Azure Active Directory のエディションの違いについてまだよくご存知でない方は、「 [Azure Active Directory のエディション](active-directory-editions.md)」を参照してください。  

> [!NOTE]
> 中国のお客様は、Azure Active Directory の世界中のインスタンスを使用して、Azure Active Directory Premium および Basic エディションを利用できます。 中国の 21Vianet が運営する Microsoft Azure サービスでは、Azure Active Directory Premium および Basic エディションは現在サポートされていません。 詳細については、 [Azure Active Directory フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory/)からお問い合わせください。
> 
> 

## <a name="step-1-sign-up-for-active-directory-premium"></a>手順 1: Active Directory Premium にサインアップする
サインアップするには、[ボリューム ライセンスを通じて購入する方法](http://www.microsoft.com/en-us/licensing/how-to-buy/how-to-buy.aspx)に関するページをご覧ください。

## <a name="step-2-activate-your-license-plan"></a>手順 2: ライセンス プランをアクティブ化する
Microsoft から Enterprise Volume Licensing プログラムを通じてライセンス プランを購入するのが初めての場合、
購入が完了したら確認メールが届きます。
最初のライセンス プランをアクティベートするにはそのメールが必要になります。

このディレクトリでの 2 回目以降の購入では、ライセンスは同じディレクトリで自動的にアクティブ化されます。

**ライセンス プランをアクティブ化するには、次の手順のいずれかを行います。**

1. アクティブ化を開始するには、**[サインイン]** または **[サインアップ]** をクリックします。
   
    ![[サインイン]][1]

    - 既存のテナントがある場合は、 **[サインイン]** をクリックし、既存の管理者アカウントでサインインします。 ライセンスをアクティブ化する必要があるディレクトリからグローバル管理者の資格情報でサインインする必要があります。

    - 自分のライセンス プランで使用する新しい Azure Active Directory テナントを作成する場合は、**[サインアップ]** をクリックして、**[アカウント プロファイルの作成]** ダイアログを開きます。

        ![[アカウント プロファイルの作成]][2]

完了すると、テナントのライセンス プランのアクティベーションの確定として次のダイアログが表示されます。

![確認][3]

## <a name="step-3-activate-your-azure-active-directory-access"></a>手順 3: Azure Active Directory のアクセスをアクティブ化する
以前に Microsoft Azure を使用したことがある場合は、 [手順 4.](#step-4-assign-license-to-user-accounts)に進みます。 

ライセンスがディレクトリにプロビジョニングされると、 **ウェルカム メール** が届きます。 そのメールで、Azure Active Directory Premium または Enterprise Mobility Suite のライセンスと機能の管理を開始できることが確認されます。 

ウェルカム メールを受け取る前に Azure Active Directory へのアクセスをアクティブ化しようとすると、次のエラー メッセージが表示されます。 

![Access is not available][9]

電子メールを受け取った後、少ししてからもう一度やり直してください。

サブスクリプションの新しい管理者も、このリンクを介して Azure クラシック ポータルへのアクセスをアクティブ化できます。

**Azure Active Directory へのアクセスをアクティブ化するには、次の手順を行います。**

1. **ウェルカム メール**の **[サインイン]** をクリックします。 
   
    ![ウェルカム メール][4]
2. サインインできたら、携帯電話確認のフォームで第 2 の要素の認証を完了する必要があります。
   
    ![Mobile verification][5]

アクティベーションには数分かかることがあります。 アクセスがアクティブになると、茶色のバーが消え、 **[ポータル]**をクリックできます。

![Please wait while we set up][6]

この例では、Azure へのアクセスは Azure Active Directory に制限されています。

![Azure capabilities][7]

以前の使用により、Azure に既にアクセスできる状態である場合があります。さらに、追加の Azure サブスクリプションをアクティブ化することによって、Azure Active Directory へのアクセスを完全な Azure アクセスにアップグレードできます。 このような場合、Azure クラシック ポータルで使用できる機能が増えます。

![Azure capabilities][8]

## <a name="step-4-assign-license-to-user-accounts"></a>手順 4: ユーザー アカウントにライセンスを割り当てる
購入したプランの使用を開始する前に、組織内のユーザーが Premium で提供される豊富な機能を使用できるように、ユーザー アカウントにライセンスを手動で割り当てる必要があります。 以下の手順を使用して、ユーザーにライセンスを割り当て、ユーザーが Azure Active Directory Premium の機能を使用できるようにします。

**ユーザーにライセンスを割り当てるには、次の手順を実行します。**

1. カスタマイズするディレクトリのグローバル管理者として、Azure クラシック ポータルにサインインします。
2. **[Active Directory]**をクリックし、ライセンスを割り当てるディレクトリを選択します。
3. **[ライセンス]** タブを選択し、**[Active Directory Premium]** または **[Enterprise Mobility Suite]** を選択して、**[割り当て]** をクリックします。
   
    ![License plans][10]
4. ダイアログ ボックスで、ライセンスを割り当てるユーザーを選択し、チェック マーク アイコンをクリックして変更を保存します。
   
    ![ライセンスを割り当てる][11]

### <a name="license-restrictions"></a>ライセンスの制限
一部のライセンス プランは、他のライセンス プランのサブセットまたはスーパーセットです。 通常、ユーザーに既に割り当てられているライセンス プランをユーザーに割り当てることはできません。 スーパーセットであるライセンス プランを割り当てる場合は、先にサブセットのライセンス プランを削除する必要があります。

### <a name="license-requirements"></a>ライセンスの要件
ユーザーにライセンスを割り当てるときは、アカウントのプロパティで主な利用場所を指定できます。 利用場所を指定しないと、テナントの場所が自動的にユーザーに割り当てられます。

![ユーザーの配置][12]

Microsoft クラウド サービスで利用できるサービスと機能は、国または地域によって異なります。 Voice over Internet Protocol (VoIP) などのサービスは、ある国または地域では利用できても、他の国または地域では利用できないことがあります。 サービスに含まれる機能は、法的な理由から特定の国や地域では制限される場合があります。 サービスまたは機能を利用するときの制限の有無を確認するには、サービスのライセンス制限サイトで国または地域を検索してください。

## <a name="whats-next"></a>参照トピック
* [サインイン ページとアクセス パネル ページに対する会社のブランドの追加](active-directory-add-company-branding.md)
* [アクセスおよび使用状況レポートの表示](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-get-started-premium/MOLSEmail.png
[2]: ./media/active-directory-get-started-premium/MOLSAccountProfile.png
[3]: ./media/active-directory-get-started-premium/MOLSThankYou.png
[4]: ./media/active-directory-get-started-premium/AADEmail.png
[5]: ./media/active-directory-get-started-premium/SignUppage.png
[6]: ./media/active-directory-get-started-premium/Subscriptionspage.png
[7]: ./media/active-directory-get-started-premium/Premiuminportal.png
[8]: ./media/active-directory-get-started-premium/Premiuminportal_large.png
[9]: ./media/active-directory-get-started-premium/Signuppage_oops.png
[10]: ./media/active-directory-get-started-premium/contosolicenseplan.png
[11]: ./media/active-directory-get-started-premium/Assignlicensespicker.png
[12]: ./media/active-directory-get-started-premium/Usagelocation.png

