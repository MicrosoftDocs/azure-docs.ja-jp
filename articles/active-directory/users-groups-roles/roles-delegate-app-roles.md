---
title: アプリケーション管理者ロールを委任する - Azure Active Directory | Microsoft Docs
description: Azure Active Directory でアクセス権を付与するためにトールを委任するアプリケーション アクセス管理
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58ca814551d8c7d309328f236052e1d07ac6f035
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58119741"
---
# <a name="delegate-app-administrator-roles-in-azure-active-directory"></a>Azure Active Directory でアプリ管理者ロールを委任する

 Azure AD では、アプリケーション アクセスの管理を組み込みの管理ロールのセットに委任することができます。 グローバル管理者のオーバーヘッドを減らすだけでなく、アプリケーション アクセスのタスクを管理するための特殊な特権を委任すると、セキュリティの体制を強化し、未承認アクセスの可能性を減らすことができます。 委任に関する問題と一般的なガイドラインについては、「[Azure Active Directory での管理の委任](roles-concept-delegation.md)」をご覧ください。

## <a name="delegate-app-administration"></a>アプリの管理を委任する

以下のロールでは、アプリケーションの登録、シングル サインオンの設定、ユーザーおよびグループの割り当てを管理し、委任されたアクセス許可とアプリケーションのアクセス許可 (Microsoft Graph と Azure AD Graph を除く) に同意するためのアクセス許可が付与されます。 唯一の違いは、アプリケーション管理者ロールでは、アプリケーション プロキシの設定を管理するアクセス許可も付与されることです。 いずれのロールでも、条件付きアクセスの設定の管理権限は付与されません。
> [!IMPORTANT]
> このロールが割り当てられたユーザーは、アプリケーションに資格情報を追加し、その資格情報を使用してアプリケーションの ID を偽装することができます。 アプリケーションの ID のこのような偽装は、ユーザーが Azure AD の他のロール割り当てを使用して実行できることを越えた特権の昇格になる可能性があります。 このロールに割り当てられたユーザーは、アプリケーションを偽装している間に、ユーザーや他のオブジェクトを作成または更新する可能性があります。

Azure portal でアプリケーションのアクセスを管理する権限を付与するには:

1. テナントのグローバル管理者ロールに適したアカウントを使用して、[Azure AD テナント](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)にサインインします。
2. 十分なアクセス許可がある場合は、[ロールと管理者のページ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators)を開きします。
3. 次のロールのいずれかを開いて、そのメンバー割り当てを表示します。
   * **アプリケーション管理者**
   * **クラウド アプリケーション管理者**
4. ロールの **[メンバー]** ページで、**[メンバーの追加]** を選択します。
5. ロールに追加する 1 人以上のメンバーを選択します。 <!--Members can be users or groups.-->

これらのロールの説明は、「[使用可能なロール](directory-assign-admin-roles.md#available-roles)」で見ることができます。

## <a name="delegate-app-registration"></a>アプリの登録を委任する

既定では、すべてのユーザーがアプリケーションの登録を作成できますが、アプリケーションの登録を作成するアクセス許可、またはアプリの承認に同意するアクセス許可を、選択的に付与することができます。

1. テナントのグローバル管理者ロールに適したアカウントを使用して、[Azure AD テナント](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)にサインインします。
2. 十分なアクセス許可を取得したら、次の一方または両方を設定します。
   * [テナントの [ユーザー設定] ページ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings)で、**[ユーザーはアプリケーションを登録できる]** を [いいえ] に設定します。
   * [エンタープライズ アプリケーションのユーザー設定](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)で、**[ユーザーはアプリが自身の代わりに会社のデータにアクセスすることを許可できます]** を [いいえ] に設定します。
3. 次に、必要に応じて、このアクセス許可を必要とするユーザーを、アプリケーション開発者ロールのメンバーとして割り当てます。

ユーザーがアプリケーションを登録すると、そのユーザーはアプリケーションの最初の所有者として自動的に追加されます。

## <a name="delegate-app-ownership"></a>アプリの所有権を委任する

アプリの所有者とアプリの登録所有者は、それぞれ、自分が所有するアプリケーションまたはアプリの登録のみを管理できます。 たとえば、Salesforce アプリケーションに所有者を追加すると、その所有者は、Salesforce へのアクセスと Salesforce の構成を管理できますが、他のアプリケーションについては管理できません。 1 つのアプリが多くの所有者を持つことができ、1 人のユーザーが多くのアプリの所有者になることができます。

アプリケーションの所有者は、次のことを行うことができます。

* 名前や、アプリが要求するアクセス許可など、アプリケーションのプロパティを変更する
* 資格情報を管理する
* Configure single sign-on
* ユーザー アクセスを割り当てる
* 他の所有者を追加または削除する
* アプリのマニフェストを編集する
* アプリをアプリ ギャラリーに発行する

> [!IMPORTANT]
> このロールが割り当てられたユーザーは、アプリケーションに資格情報を追加し、その資格情報を使用してアプリケーションの ID を偽装することができます。 アプリケーションの ID のこのような偽装は、ユーザーが Azure AD の他のロール割り当てを使用して実行できることを越えた特権の昇格になる可能性があります。 このロールに割り当てられたユーザーは、アプリケーションを偽装している間に、ユーザーや他のオブジェクトを作成または更新する可能性があります。

アプリ登録の所有者は、アプリの登録を表示および編集することができます。

<!-- ### To assign an enterprise app ownership role to a user

1. Sign in to your [Azure AD tenant](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) with an account that is the Global Administrator for the tenant.
2. On the [Roles and administrators page](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators), open one of the following roles to see its member assignments:
  * **Enterprise Application Owner**
  * **Application Registration Owner**
3. On the **Members** page for the role, select **Add member**.
4. Select one or more members to add to the role. -->

### <a name="to-assign-an-owner-to-an-application"></a>アプリケーションに所有者を割り当てるには

1. テナントのアプリケーション管理者またはクラウド アプリケーション管理者に適したアカウントを使用して、[Azure AD テナント](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)にサインインします。
2. テナントの[アプリ登録ページ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/)で、アプリを選択して、アプリの **[概要]** ページを開きます。
3. **[所有者]** を選択して、アプリの所有者の一覧を表示します。
4. **[追加]** を選択し、アプリに追加する所有者を 1 人以上選択します。

### <a name="to-assign-an-owner-to-an-application-registration"></a>アプリケーション登録に所有者を割り当てるには

1. テナントのアプリケーション管理者ロールまたはクラウド アプリケーション管理者ロールに適したアカウントを使用して、[Azure AD テナント](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)にサインインします。
2. 十分なアクセス許可がある場合は、テナントの [[エンタープライズ アプリケーション] ページ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/)で、アプリの登録を選択して開きます。
3. **[設定]** を選択します。
4. **[設定]** ページで **[所有者]** を選択し、アプリの所有者の一覧を表示します。
5. **[所有者の追加]** を選択し、アプリに追加する所有者を 1 人以上選択します。

## <a name="next-steps"></a>次の手順

* [Azure AD 管理者ロールのリファレンス](directory-assign-admin-roles.md)
