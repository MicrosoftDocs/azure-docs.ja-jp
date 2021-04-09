---
title: Azure AD アプリを一連のユーザーに制限する | Azure
titleSuffix: Microsoft identity platform
description: Azure AD に登録されたアプリへのアクセスを選択したユーザーのセットに制限する方法について説明します。
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 71cfe08da42b8eec9ddbd0e4246ba1b72f895414
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103199592"
---
# <a name="how-to-restrict-your-azure-ad-app-to-a-set-of-users-in-an-azure-ad-tenant"></a>方法:Azure AD アプリを Azure AD テナントの一連のユーザーに制限する

Azure Active Directory (Azure AD) テナントに登録されたアプリケーションは、既定ではテナントの正常に認証されたすべてのユーザーが利用できます。

同様に、[マルチテナント](howto-convert-app-to-be-multi-tenant.md) アプリの場合、このアプリがプロビジョニングされている Azure AD テナントのすべてのユーザーは、それぞれのテナントで正常に認証されると、このアプリケーションにアクセスできるようになります。

テナントの管理者と開発者には、多くの場合、アプリを特定のユーザーのセットに制限しなければならない要件があります。 開発者は Azure ロール ベースのアクセス制御 (Azure RBAC) のような一般的な承認パターンを使用して同じことを実現できますが、この方法では開発者に相当の負担がかかります。

Azure AD のこの組み込み機能も使用することにより、テナントの管理者と開発者が、テナント内のユーザーまたはセキュリティ グループの特定のセットにアプリを制限できるようになります。

## <a name="supported-app-configurations"></a>サポートされているアプリの構成

テナントのユーザーまたはセキュリティ グループの特定のセットにアプリを制限するオプションは、次の種類のアプリケーションで動作します。

- SAML ベースの認証を使用したフェデレーション シングル サインオン用に構成されたアプリケーション。
- Azure AD 事前認証を使用するアプリケーション プロキシのアプリケーション。
- ユーザーまたは管理者がそのアプリケーションに同意した後に OAuth 2.0/OpenID Connect 認証を使用する Azure AD アプリケーション プラットフォームに直接構築されたアプリケーション。

     > [!NOTE]
     > この機能は、Web アプリ/Web API およびエンタープライズ アプリケーションでのみ使用できます。 [ネイティブ](./quickstart-register-app.md)として登録されているアプリは、テナント内のユーザーまたはセキュリティ グループのセットに制限することができません。

## <a name="update-the-app-to-enable-user-assignment"></a>アプリを更新してユーザーの割り当てを有効にする

ユーザーの割り当てが有効なアプリケーションを作成するには、2 つの方法があります。 1 つ目には **グローバル管理者** ロールが必要ですが、2 つ目には必要ありません。

### <a name="enterprise-applications-requires-the-global-administrator-role"></a>エンタープライズ アプリケーション (グローバル管理者ロールが必要)

1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> に **グローバル管理者** としてサインインします。
1. 複数のテナントにアクセスできる場合は、トップ メニューの **[ディレクトリとサブスクリプション]** フィルター:::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::を使用して、アプリケーションを登録するテナントを選択します。
1. **Azure Active Directory** を検索して選択します。
1. **[管理]** で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** を選択します。
1. ユーザーまたはセキュリティ グループを割り当てるアプリケーションを一覧から選びます。 
    ウィンドウの上部にあるフィルターを使用して、特定のアプリケーションを検索します。
1. アプリケーションの **[概要]** ページの **[管理]** の下で **[プロパティ]** を選択します。
1. **[ユーザーの割り当てが必要ですか?]** の設定を探し、 **[はい]** に設定します。 このオプションを **[はい]** に設定すると、テナント内のユーザーは、まずこのアプリケーションに割り当てられることが必要になります。そうしないと、アプリケーションにアクセスできません。
1. **[保存]** を選択します。

### <a name="app-registration"></a>アプリの登録

1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインします。
1. 複数のテナントにアクセスできる場合は、トップ メニューの **[ディレクトリとサブスクリプション]** フィルター:::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::を使用して、アプリケーションを登録するテナントを選択します。
1. **Azure Active Directory** を検索して選択します。
1. **[管理]** の **[アプリの登録]** を選択します。
1. 管理するアプリを作成または選択します。 ご自分がこのアプリケーションの **所有者** である必要があります。
1. アプリケーションの **[概要]** ページで、 **[Essentials]** セクションにある **[ローカル ディレクトリでのマネージド アプリケーション]** リンクを選択します。
1. **[管理]** の下で、 **[プロパティ]** を選択します。
1. **[ユーザーの割り当てが必要ですか?]** の設定を探し、 **[はい]** に設定します。 このオプションを **[はい]** に設定すると、テナント内のユーザーは、まずこのアプリケーションに割り当てられることが必要になります。そうしないと、アプリケーションにアクセスできません。
1. **[保存]** を選択します。

## <a name="assign-users-and-groups-to-the-app"></a>アプリにユーザーとグループを割り当てる

ユーザーの割り当てを有効にするようにアプリを構成したら、次はユーザーとグループをそのアプリに割り当てることができます。

1. **[管理]** で、 **[ユーザーとグループ]**  >  **[ユーザー/グループの追加]** を選択します。
1. **[ユーザー]** セレクターを選択します。 

     ユーザーとセキュリティ グループの一覧と一緒に、特定のユーザーまたはグループを検索して探すためのテキスト ボックスが表示されます。 この画面では、一度に複数のユーザーとグループを選択できます。

1. ユーザーとグループの選択が完了したら、 **[選択]** を選択します。
1. (オプション) アプリケーションでアプリ ロールを定義している場合は、 **[ロールの選択]** オプションを使用して、選択したユーザーとグループをいずれかのアプリケーションのロールに割り当てることができます。 
1. **[割り当て]** を選択して、アプリへのユーザーとグループの割り当てを完了します。 
1. 追加したユーザーとグループが、更新された **[ユーザーとグループ]** のリストに表示されていることを確認します。

## <a name="more-information"></a>詳細情報

- [方法: ご利用のアプリケーションにアプリ ロールを追加する](./howto-add-app-roles-in-azure-ad-apps.md)
- [アプリ ロールおよびロール要求を使用して ASP.NET Core Web アプリに承認を追加する](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [アプリでのセキュリティ グループとアプリケーション ロールの使用 (ビデオ)](https://www.youtube.com/watch?v=LRoc-na27l0)
- [グループ要求とアプリケーション ロールが追加された Azure Active Directory](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Azure Active Directory のアプリ マニフェスト](./reference-app-manifest.md)
