---
title: チュートリアル:LucidChart を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを LucidChart に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: daveba
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: asmalser-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc181625ead251480bb107fc59e3aae46afab1ee
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59257690"
---
# <a name="tutorial-configure-lucidchart-for-automatic-user-provisioning"></a>チュートリアル:LucidChart を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、Azure AD から LucidChart にユーザー アカウントを自動的にプロビジョニング/プロビジョニング解除するうえで LucidChart と Azure AD で実行する必要がある手順について説明します。 

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* Azure Active Directory テナント
* [Enterprise プラン](https://www.lucidchart.com/user/117598685#/subscriptionLevel)以上の有効な LucidChart テナント
* Admin アクセス許可がある LucidChart のユーザー アカウント

## <a name="assigning-users-to-lucidchart"></a>LucidChart へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー アカウント プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。

プロビジョニング サービスを構成して有効にする前に、LucidChart アプリへのアクセスが必要なユーザーを表す Azure AD 内のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順でこれらのユーザーを LucidChart アプリに割り当てることができます。

[エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-lucidchart"></a>ユーザーを LucidChart に割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを LucidChart に割り当てて、プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* LucidChart にユーザーを割り当てるときは、**ユーザー** ロールまたは別の有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選ぶ必要があります。 **[既定のアクセス]** ロールはプロビジョニングでは使うことができず、このロールのユーザーはスキップされます。

## <a name="configuring-user-provisioning-to-lucidchart"></a>LucidChart へのユーザー プロビジョニングの構成

このセクションでは、Azure AD を LucidChart のユーザー アカウント プロビジョニング API に接続する手順のほか、プロビジョニング サービスを構成して、Azure AD のユーザーとグループの割り当てに基づいて割り当て済みユーザーのアカウントを LucidChart で作成、更新、無効化する手順を説明します。

> [!TIP]
> LucidChart では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Azure Portal](https://portal.azure.com) で説明されている手順に従ってください。 シングル サインオンは自動プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="configure-automatic-user-account-provisioning-to-lucidchart-in-azure-ad"></a>Azure AD で LucidChart への自動ユーザー アカウント プロビジョニングを構成する

1. [Azure Portal](https://portal.azure.com) で、**[Azure Active Directory]、[エンタープライズ アプリ]、[すべてのアプリケーション]** セクションの順に移動します。

2. シングル サインオンのために LucidChart を既に構成している場合は、検索フィールドで LucidChart のインスタンスを検索します。 構成していない場合は、**[追加]** を選択してアプリケーション ギャラリーで **LucidChart** を検索します。 検索結果から LucidChart を選択してアプリケーションの一覧に追加します。

3. LucidChart のインスタンスを選択してから、**[プロビジョニング]** タブを選択します。

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![LucidChart のプロビジョニング](./media/lucidchart-provisioning-tutorial/LucidChart1.png)

5. **[管理者資格情報]** セクションで、LucidChart のアカウントによって生成された **[シークレット トークン]** を入力します (トークンは、LucidChart アカウントの **[チーム]** > **[アプリの統合]** > **[SCIM]** で確認できます)。

    ![LucidChart のプロビジョニング](./media/lucidchart-provisioning-tutorial/LucidChart2.png)

6. Azure Portal で、**[テスト接続]** をクリックして Azure AD が LucidChart アプリに接続できることを確認します。 接続できない場合は、使用中の LucidChart アカウントに管理者アクセス許可があることを確認してから、手順 5 をもう一度試します。

7. プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを **[通知用メール]** フィールドに入力して、[エラーが発生したときにメール通知を送信します] チェック ボックスをオンにします。

8. **[Save]** をクリックします。

9. [マッピング] セクションの **[Synchronize Azure Active Directory Users to LucidChart]\(Azure Active Directory ユーザーを LucidChart に同期する\)** を選びます。

10. **[属性マッピング]** セクションで、Azure AD から LucidChart に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で LucidChart のユーザー アカウントとの照合に使用されます。 [保存] ボタンをクリックして変更をコミットします。

11. LucidChart に対して Azure AD プロビジョニング サービスを有効にするには、**[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

12. **[Save]** をクリックします。

これにより、[ユーザーとグループ] セクションで LucidChart に割り当てたユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ ログを取得できます。このログには、プロビジョニング サービスによって実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../manage-apps/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次の手順

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../manage-apps/check-status-user-account-provisioning.md)
