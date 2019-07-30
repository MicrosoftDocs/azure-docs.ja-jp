---
title: クイック スタート - Azure Active Directory Identity Protection を使用して、セッションのリスクが検出されたときにアクセスをブロックする | Microsoft Docs
description: このクイック スタートでは、Azure Active Directory (Azure AD) Identity Protection のサインイン リスク条件付きアクセス ポリシーを使用して、セッションのリスクに基づいてサインインをブロックする方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: quickstart
ms.date: 09/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1bb1e29735a860f5dc3b6ce8996af9fcd4962871
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335308"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-identity-protection"></a>クイック スタート:Azure Active Directory Identity Protection を使用して、セッションのリスクが検出されたときにアクセスをブロックする  

環境を保護された状態で維持するために、疑わしいユーザーによるサインインをブロックすることができます。 Azure Active Directory (Azure AD) Identity Protection では、各サインインを分析して、サインイン試行がユーザー アカウントの正当な所有者によって行われなかった可能性を算出します。 可能性 (低、中、高) は、サインイン リスク レベルと呼ばれる計算値の形で示されます。 サインイン リスクの条件を設定することで、特定のサインイン リスク レベルに応じたサインイン リスク条件付きアクセス ポリシーを構成できます。 

このクイック スタートでは、中以上のサインイン リスク レベルが検出されたときにサインインをブロックするサインイン リスク条件付きアクセス ポリシーを構成する方法を説明します。 

![ポリシーの作成](./media/quickstart-sign-in-risk-policy/1004.png)

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件 

このチュートリアルのシナリオを完了するための要件を次に示します。

- **Azure AD Premium P2 エディションへのアクセス** - Azure AD Identity Protection は Azure AD Premium P2 の機能です。 
- **Identity Protection** - このクイック スタートのシナリオでは Identity Protection を有効にする必要があります。 Identity Protection を有効にする方法がわからない場合は、「[Azure Active Directory Identity Protection の有効化](../identity-protection/enable.md)」を参照してください。
- **Tor Browser** - [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en) は、オンラインでの自身のプライバシー保護に役立つことを目的としています。 Identity Protection は、Tor Browser からのサインインを、中程度のリスク レベルのある**匿名 IP アドレスからのサインイン**として検出します。 詳細については、「[Azure Active Directory リスク イベント](../reports-monitoring/concept-risk-events.md)」を参照してください。  
- **Alain Charon というテスト アカウント** - テスト アカウントの作成方法がわからない場合は、[新しいユーザーの追加](../fundamentals/add-users-azure-active-directory.md#add-a-new-user)に関するページを参照してください。

## <a name="test-your-sign-in"></a>サインインをテストする 

この手順の目的は、Tor Browser を使用してテスト アカウントでテナントにアクセスできることを確認することです。

**サインインをテストするには:**

1. [Azure portal](https://portal.azure.com) に **Alain Charon** としてサインインします。
2. サインアウトします。 

## <a name="create-your-conditional-access-policy"></a>条件付きアクセス ポリシーを作成する 

このクイック スタートのシナリオでは、Tor Browser からのサインインを使用して、検出された**匿名 IP アドレスからのサインイン**というリスク イベントを生成します。 このリスク イベントのリスク レベルは中です。 このリスク イベントに応答するには、サインイン リスクの条件を [中] に設定します。 

このセクションでは、必要なサインイン リスク条件付きアクセス ポリシーを作成する方法について説明します。 ポリシーに以下の内容を設定します。

|Setting |値|
|---     | --- |
| ユーザー  | Alain Charon  |
| 条件 | サインイン リスク、中以上 |
| コントロール | アクセスのブロック |

![ポリシーの作成](./media/quickstart-sign-in-risk-policy/201.png)

**条件付きアクセス ポリシーを構成するには:**

1. [Azure Portal](https://portal.azure.com) に全体管理者としてサインインします。
2. [[Azure AD Identity Protection]](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/Overview) ページに移動します。
3. **[Azure AD Identity Protection]** ページの **[構成]** セクションで、 **[サインインのリスク ポリシー]** をクリックします。
4. ポリシー ページの **[割り当て]** セクションで、 **[ユーザー]** をクリックします。
5. **[ユーザー]** ページで、 **[ユーザーの選択]** をクリックします。
6. **[ユーザーの選択]** ページで **[Alain Charon]** を選択し、 **[選択]** をクリックします。
7. **[ユーザー]** ページで、 **[完了]** をクリックします。 
8. ポリシー ページの **[割り当て]** セクションで、 **[条件]** をクリックします。
9. **[条件]** ページで、 **[サインイン リスク]** をクリックします。
10. **[サインイン リスク]** ページで **[Medium and above]/(中以上/)** を選択し、 **[選択]** をクリックします。 
11. **[条件]** ページで、 **[完了]** をクリックします。
12. ポリシー ページの **[コントロール]** セクションで、 **[アクセス]** をクリックします。
13. **[アクセス]** ページで **[アクセスを許可]** をクリックし、 **[多要素認証を要求する]** を選択して、 **[選択]** をクリックします。
14. ポリシー ページで、 **[保存]** をクリックします。  

## <a name="test-your-conditional-access-policy"></a>条件付きアクセス ポリシーをテストする

ポリシーをテストするには、Tor Browser を使用して、**Alan Charon** として [Azure portal](https://portal.azure.com) にサインインしてみます。 そのサインイン試行は、条件付きアクセス ポリシーによってブロックされる必要があります。

![多要素認証](./media/quickstart-sign-in-risk-policy/203.png)


## <a name="clean-up-resources"></a>リソースのクリーンアップ

不要になったら、テスト ユーザーと Tor Browser を削除し、サインイン リスク条件付きアクセス ポリシーを無効にします。

- Azure AD ユーザーの削除方法がわからない場合は、[新しいユーザーの追加または削除](../fundamentals/add-users-azure-active-directory.md#delete-a-user)に関するページを参照してください。
- Tor Browser を削除する手順については、「[Uninstalling (アンインストール)](https://tb-manual.torproject.org/uninstalling/)」を参照してください。
