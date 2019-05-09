---
title: チュートリアル:G Suite を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure AD から G Suite に対してユーザー アカウントを自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea1f4d4a6b60961515826a1ba7409bf149b318e8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59281303"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>チュートリアル:G Suite を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure Active Directory (Azure AD) から G Suite にユーザー アカウントを自動的にプロビジョニング/プロビジョニング解除する方法について説明することです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

G Suite と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- G Suite でのシングル サインオンが有効なサブスクリプション
- Google Apps サブスクリプションまたは Google Cloud Platform サブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="assign-users-to-g-suite"></a>ユーザーを G Suite に割り当てる

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー アカウント プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。

プロビジョニング サービスを構成して有効にする前に、アプリにアクセスする必要がある Azure AD 内のユーザーまたはグループを決定しておく必要があります。 これを決定したら、[エンタープライズ アプリへのユーザーまたはグループの割り当て](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)に関するページの手順に従って、これらのユーザーをアプリに割り当てることができます。

> [!IMPORTANT]
> 単一の Azure AD ユーザーを G Suite に割り当てて、プロビジョニングの構成をテストすることをお勧めします。 後で、追加のユーザーとグループを割り当てることができます。
> 
> ユーザーを G Suite に割り当てるときに、[割り当て] ダイアログ ボックスで **[ユーザー]** または **[グループ]** ロールを選択します。 **[既定のアクセス]** ロールはプロビジョニングでは機能しません。

## <a name="enable-automated-user-provisioning"></a>自動化されたユーザー プロビジョニングを有効にする

このセクションでは、G Suite のユーザー アカウント プロビジョニング API に Azure AD を接続するプロセスを説明します。 Azure AD でのユーザーとグループの割り当てに基づいて、G Suite でのロール割り当て済みユーザー アカウントの作成、更新、および無効化を行うプロビジョニング サービスを構成する際にも役立ちます。

>[!TIP]
>G Suites では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、説明されている手順を [Azure portal](https://portal.azure.com) で実行します。 シングル サインオンは自動プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="configure-automatic-user-account-provisioning"></a>自動ユーザー アカウント プロビジョニングを構成する

> [!NOTE]
> G Suite へのユーザー プロビジョニングを自動化するための別の実行可能なオプションは、[Google Apps Directory Sync (GADS)](https://support.google.com/a/answer/106368?hl=en) を使用することです。 GADS は、オンプレミスの Active Directory の ID を G Suite にプロビジョニングします。 このチュートリアルでは Azure Active Directory (クラウド) のユーザーとメールが有効なグループを G Suite にプロビジョニングします。 

1. [Google Apps の管理コンソール](https://admin.google.com/)に Administrator アカウントでサインインし、**[セキュリティ]** をクリックします。 このリンクが表示されていない場合、画面下部の **[その他の設定]** メニューに隠れていることがあります。

    ![[セキュリティ] を選択する][10]

1. **[セキュリティ]** ページで、**[API リファレンス]** を選択します。

    ![API リファレンスを選択する][15]

1. **[API アクセスを有効にする]** を選択します。

    ![API リファレンスを選択する][16]

   > [!IMPORTANT]
   > G Suite にプロビジョニングするすべてのユーザーは、Azure Active Directory でのユーザー名がカスタム ドメインに関連付けられている "*必要があります*"。 たとえば G Suite では、bob@contoso.onmicrosoft.com のようなユーザー名は使用できませんが、 bob@contoso.com のようなユーザー名は使用できます。 Azure AD でプロパティを編集することによって、既存のユーザーのドメインを変更できます。 Azure Active Directory と G Suite の両方でカスタム ドメインを設定する方法は、以下の手順に含まれています。

1. まだ Azure Active Directory にカスタム ドメイン名を追加していない場合は、次の手順に従います。
  
    a. [Azure ポータル](https://portal.azure.com)の左側のナビゲーション ウィンドウで、**[Active Directory]** を選択します。 ディレクトリの一覧で、ディレクトリを選択します。

    b. 左のナビゲーション ウィンドウで **[ドメイン名]** を選択し、**[追加]** を選択します。

    ![Domain](./media/google-apps-provisioning-tutorial/domain_1.png)

    ![ドメインを追加](./media/google-apps-provisioning-tutorial/domain_2.png)

    c. **[ドメイン名]** フィールドにドメイン名を入力します。 ドメイン名は、G Suite で使用するのと同じドメイン名にする必要があります。 その後、**[ドメインの追加]** ボタンを選択します。

    ![ドメイン名](./media/google-apps-provisioning-tutorial/domain_3.png)

    d. **[次へ]** を選択し、確認ページに移動します。 このドメインを所有していることを確認するために、このページに表示されている値に従ってドメインの DNS レコードを編集します。 **[レコードの種類]** オプションでの選択内容に応じて、**MX レコード**と **TXT レコード**のどちらを使用して確認するかを選択します。

    Azure AD でのドメイン名の確認に関する包括的な手順の詳細については、[Azure AD への独自のドメイン名の追加](https://go.microsoft.com/fwLink/?LinkID=278919&clcid=0x409)に関するトピックを参照してください。

    ![Domain](./media/google-apps-provisioning-tutorial/domain_4.png)

    e. ディレクトリに追加するすべてのドメインに対して、前述の手順を繰り返します。

    > [!NOTE]
    > ユーザー プロビジョニングでは、カスタム ドメインがソースの Azure AD のドメイン名と一致する必要があります。 ドメイン名が一致しない場合は、属性マッピングのカスタマイズを実装することにより、問題が解決する場合があります。

1. すべてのドメインを Azure AD で確認したので、Google Apps でも確認する必要があります。 Google にまだ登録されていない各ドメインに対して、次の手順を実行します。

    a. [Google Apps の管理コンソール](https://admin.google.com/)で、**[ドメイン]** をクリックします。

    ![ドメインを選択する][20]

    b. **[ドメインやドメイン エイリアスを追加]** を選択します。

    ![Add a new domain][21]

    c. **[別のドメインを追加]** を選択し、追加するドメインの名前を入力します。

    ![Type in your domain name][22]

    d. **[Continue and verify domain ownership]\(続行してドメインの所有権を確認\)** を選択します。 次に、手順に従って、ドメイン名を所有していることを確認します。 Google でドメインを確認する包括的な手順については、[Google Apps でサイトの所有者であることを確認する](https://support.google.com/webmasters/answer/35179)方法に関するページをご覧ください。

    e. Google Apps に追加するすべての追加ドメインに対して、前述の手順を繰り返します。

    > [!WARNING]
    > G Suite テナントのプライマリ ドメインを変更する場合、Azure AD でシングル サインオンが構成済みである場合は、「手順 2:シングル サインオンを有効にする」の手順 3 を繰り返す必要があります。

1. [Google Apps の管理コンソール](https://admin.google.com/)で、**[管理者ロール]** を選択します。

    ![Google Apps を選択する][26]

1. ユーザー プロビジョニングの管理にどの管理者アカウントを使用するかを決定します。 使用するアカウントの **[管理者の役割]** で、役割の **[権限]** を編集します。 このアカウントをプロビジョニングに使用できるように、**[Admin API Privileges]\(管理 API の権限\)** がすべて有効になっていることを確認します。

    ![Google Apps を選択する][27]

    > [!NOTE]
    > 運用環境を構成している場合のベスト プラクティスは、特にこの手順で G Suite の管理者アカウントを作成することです。 管理者アカウントには、必要な API 特権を持つ管理者ロールが関連付けられている必要があります。

1. [Azure Portal](https://portal.azure.com) で、**[Azure Active Directory]** > **[エンタープライズ アプリ]** > **[すべてのアプリケーション]** セクションの順に移動します。

1. シングル サインオンのために G Suite を既に構成している場合は、検索フィールドで G Suite のインスタンスを検索します。 構成していない場合は、**[追加]** を選択してアプリケーション ギャラリーで **G Suite** または **Google Apps** を検索します。 検索結果からアプリを選択してアプリケーションの一覧に追加します。

1. G Suite のインスタンスを選択してから、**[プロビジョニング]** タブを選択します。

1. **[プロビジョニング モード]** を **[自動]** に設定します。 

    ![プロビジョニング](./media/google-apps-provisioning-tutorial/provisioning.png)

1. **[管理者資格情報]** セクションにある **[承認する]** を選択します。 これで、ブラウザーの新しいウィンドウで Google 認可ダイアログ ボックスが開きます。

1. G Suite のテナントに対して変更を行うためのアクセス許可を Azure Active Directory に付与することを確認します。 **[Accept]\(承認\)** を選択します。

    ![Confirm permissions.][28]

1. Azure portal で、**[テスト接続]** を選択して Azure AD がアプリに接続できることを確認します。 接続が失敗した場合は、使用中の G Suite アカウントに Team Admin アクセス許可があることを確認します。 その後、**承認**手順を再び試します。

1. プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを **[通知用メール]** フィールドに入力し、 チェック ボックスをオンにします。

1. **[保存]** を選択します。

1. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Google Apps]\(Azure Active Directory ユーザーを Google Apps に同期する\)** を選択します。

1. **[属性マッピング]** セクションで、Azure AD から G Suite に同期されるユーザー属性を確認します。 **[照合]** プロパティである属性を使用して、G Suite 内の更新操作用のユーザー アカウントが照合されます。 すべての変更をコミットするには、**[保存]** を選択します。

1. G Suite に対して Azure AD プロビジョニング サービスを有効にするには、**[設定]** で **[プロビジョニングの状態]** を **[オン]** に変更します。

1. **[保存]** を選択します。

このプロセスは、[ユーザーとグループ] セクションで G Suite に割り当てたユーザーまたはグループの初期同期を開始します。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、サービスが実行されている間、約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用して、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ ログを取得できます。 これらのログには、アプリでプロビジョニング サービスによって実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../manage-apps/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
* [シングル サインオンの構成](google-apps-tutorial.md)

<!--Image references-->

[10]: ./media/google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/google-apps-provisioning-tutorial/gapps-auth.png
