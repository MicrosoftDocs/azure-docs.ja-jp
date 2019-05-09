---
title: チュートリアル:Box を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory と Box の間にシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 1c959595-6e57-4954-9c0d-67ba03ee212b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd7826455624ca4a84d668455f522cbde411ac8b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56163189"
---
# <a name="tutorial-configure-box-for-automatic-user-provisioning"></a>チュートリアル:Box を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、Azure AD から Box にユーザー アカウントを自動的にプロビジョニング/プロビジョニング解除するうえで Box と Azure AD で実行する必要がある手順について説明します。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Box と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD テナント
- ボックス ビジネス プランまたは進化版

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を*使用しない*ことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従います。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="assigning-users-to-box"></a>Box へのユーザーの割り当て 

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー アカウント プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。

プロビジョニング サービスを構成して有効にする前に、Box アプリへのアクセスが必要なユーザーを表す Azure AD 内のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順でこれらのユーザーを Box アプリに割り当てることができます。

[エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

## <a name="assign-users-and-groups"></a>ユーザーとグループの割り当て
Azure Portal の **[Box] > [ユーザーとグループ]** タブでは、Box へのアクセスを許可するユーザーとグループを指定できます。 ユーザーまたはグループを割り当てると、次の処理が実行されます。

* Azure AD により、(直接割り当てまたはグループのメンバーシップによって) 割り当てられたユーザーに Box への認証が許可されます。 割り当てられていないユーザーには Box へのサインインが許可されず、Azure AD のサインイン ページでエラーが返されます。
* Box のアプリ タイルがユーザーの [アプリケーション起動プログラム](../manage-apps/end-user-experiences.md)に追加されます。
* 自動プロビジョニングが有効になっている場合、割り当てられたユーザーまたはグループはプロビジョニング キューに追加され、自動的にプロビジョニングされます。
  
  * ユーザー オブジェクトのみをプロビジョニングするよう構成した場合は、直接割り当てられたすべてのユーザーがプロビジョニング キューに配置され、さらに、割り当てられたグループのメンバーであるユーザーもすべてプロビジョニング キューに配置されます。 
  * グループ オブジェクトをプロビジョニングするよう構成した場合は、割り当てられたすべてのグループ オブジェクトと、それらのグループのメンバーであるユーザーもすべて Box にプロビジョニングされます。 Box への書き込み時に、グループとユーザーのメンバーシップは保持されます。

**[属性] の [シングル サインオン]** タブを使用すると、SAML ベースの認証時に Box に提示するユーザーの属性 (または要求) を構成できます。また、**[属性] の [プロビジョニング]** タブを使用すると、プロビジョニング操作時の Azure AD から Box へのユーザーとグループの属性のフロー方法を構成できます。

### <a name="important-tips-for-assigning-users-to-box"></a>ユーザーを Box に割り当てる際の重要なヒント 

*   単一の Azure AD ユーザーを Box に割り当てて、プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

*   Box にユーザーを割り当てるときに、有効なユーザー ロールを選択する必要があります。 "既定のアクセス" ロールはプロビジョニングでは使えません。

## <a name="enable-automated-user-provisioning"></a>自動化されたユーザー プロビジョニングを有効にする

このセクションでは、Azure AD を Box のユーザー アカウント プロビジョニング API に接続する手順のほか、プロビジョニング サービスを構成して、Azure AD のユーザーとグループの割り当てに基づいてロール割り当て済みユーザーのアカウントを Box で作成、更新、無効化する手順を説明します。

自動プロビジョニングが有効になっている場合、割り当てられたユーザーまたはグループはプロビジョニング キューに追加され、自動的にプロビジョニングされます。
    
 * ユーザー オブジェクトのみがプロビジョニングされるように構成した場合は、直接割り当てられたすべてのユーザーがプロビジョニング キューに配置され、さらに、割り当てられたグループのメンバーであるユーザーもすべてプロビジョニング キューに配置されます。 
    
 * グループ オブジェクトをプロビジョニングするよう構成した場合は、割り当てられたすべてのグループ オブジェクトと、それらのグループのメンバーであるユーザーもすべて Box にプロビジョニングされます。 Box への書き込み時に、グループとユーザーのメンバーシップは保持されます。

> [!TIP] 
> Box では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Azure Portal](https://portal.azure.com) で説明されている手順に従ってください。 シングル サインオンは自動プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-account-provisioning"></a>自動ユーザー アカウント プロビジョニングを構成するには:

このセクションでは、Box への Active Directory ユーザー アカウントのプロビジョニングを有効にする方法を説明します。

1. [Azure Portal](https://portal.azure.com) で、**[Azure Active Directory]、[エンタープライズ アプリ]、[すべてのアプリケーション]** セクションの順に移動します。

2. シングル サインオンのために Box を既に構成している場合は、検索フィールドで Box のインスタンスを検索します。 構成していない場合は、**[追加]** を選択してアプリケーション ギャラリーで **Box** を検索します。 検索結果から Box を選択してアプリケーションの一覧に追加します。

3. Box のインスタンスを選択してから、**[プロビジョニング]** タブを選択します。

4. **[プロビジョニング モード]** を **[自動]** に設定します。 

    ![プロビジョニング](./media/box-userprovisioning-tutorial/provisioning.png)

5. **[管理者資格情報]** セクションの **[承認する]** をクリックして、新しいブラウザー ウィンドウで Box のログイン ダイアログを開きます。

6. **[Box へのアクセスを許可するにはログインしてください]** ページで、必要な資格情報を入力し、**[認証する]** をクリックします。 
   
    ![Enable automatic user provisioning](./media/box-userprovisioning-tutorial/IC769546.png "Enable automatic user provisioning")

7. **[Box のアクセス許可]** をクリックしてこの操作を承認し、Azure Portal に戻ります。 
   
    ![Enable automatic user provisioning](./media/box-userprovisioning-tutorial/IC769549.png "Enable automatic user provisioning")

8. Azure Portal で、**[テスト接続]** をクリックして Azure AD が Box アプリに接続できることを確認します。 接続が失敗した場合、使用中の Box アカウントに Team Admin アクセス許可があることを確認して、**"承認"** の手順をもう一度試してください。

9. プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを **[通知用メール]** フィールドに入力して、下のチェック ボックスをオンにします。

10. **[保存]** をクリックします。

11. [マッピング] セクションの **[Synchronize Azure Active Directory Users to Slack (Azure Active Directory ユーザーを Box に同期する)]** を選択します。

12. **[属性マッピング]** セクションで、Azure AD から Box に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Box のユーザー アカウントとの照合に使用されます。 [保存] ボタンをクリックして変更をコミットします。

13. Box に対して Azure AD プロビジョニング サービスを有効にするには、[設定] セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

14. **[保存]** をクリックします。

[ユーザーとグループ] セクションで Box に割り当てたユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ ログを取得できます。このログには、プロビジョニング サービスによって Box アプリに対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../manage-apps/check-status-user-account-provisioning.md)」をご覧ください。

Box テナントでは、同期済みのユーザーは、**[管理コンソール]** の **[管理対象のユーザー]** に表示されます。

![Integration status](./media/box-userprovisioning-tutorial/IC769556.png "Integration status")


## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
* [シングル サインオンの構成](box-tutorial.md)
