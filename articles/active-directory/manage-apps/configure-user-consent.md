---
title: アプリケーションに対するユーザーの同意を構成する - Azure Active Directory | Microsoft Docs
description: ユーザーがアプリケーションのアクセス許可に同意する方法を管理する方法について説明します。 管理者の同意を付与することでユーザー エクスペリエンスを簡素化できます。 これらの方法は、Azure Active Directory (Azure AD) テナントのすべてのエンド ユーザーに適用されます。
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: celested
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: d35f8b440fe748f91c9e01003fe83a3a5343c8df
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56203728"
---
# <a name="configure-the-way-end-users-consent-to-an-application-in-azure-active-directory"></a>Azure Active Directory でエンド ユーザーがアプリケーションに同意する方法を構成する
ユーザーがアプリケーションのアクセス許可に同意する方法を構成する方法について説明します。 管理者の同意を付与することでユーザー エクスペリエンスを簡素化できます。 この記事では、ユーザーの同意を構成するさまざまな方法を紹介します。 この方法は、Azure Active Directory (Azure AD) テナントのすべてのエンド ユーザーに適用されます。 

アプリケーションに同意する行為の詳細については、「[Azure Active Directory 同意フレームワーク](../develop/consent-framework.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

管理者の同意を付与するには、全体管理者、アプリケーション管理者、またはクラウド アプリケーション管理者としてサインインする必要があります。

アプリケーションへのアクセスを制限するには、ユーザーの割り当てが必要で、その後、アプリケーションにユーザーまたはグループを割り当てる必要があります。  詳細については、[ユーザーとグループの割り当て方法](methods-for-assigning-users-and-groups.md)に関するページを参照してください。

## <a name="grant-admin-consent-to-enterprise-apps-in-the-azure-portal"></a>Azure portal でエンタープライズ アプリに管理者の同意を付与する

エンタープライズ アプリに管理者の同意を付与するには:

1. 全体管理者、アプリケーション管理者、またはクラウド アプリケーション管理者として [Azure portal](https://portal.azure.com) にサインインします。
2. 左側のナビゲーション メニューの上部にある **[すべてのサービス]** をクリックします。 **[Azure Active Directory 拡張機能]** が開きます。
3. フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。
4. ナビゲーション メニューで **[エンタープライズ アプリケーション]** をクリックします。
5. **[管理者の同意の付与]** をクリックします。 アプリケーションを管理するためにサインインするようプロンプトが表示されます。
6. アプリケーションに対する管理者の同意を付与する権限が与えられているアカウントでサインインします。 
7. アプリケーションのアクセス許可に同意します。

このオプションは、アプリケーションが次の場合にのみ機能します。 

- ご利用のテナントで登録されているか、
- 別の Azure AD テナントで登録されており、かつ、少なくとも 1 人のエンド ユーザーの同意を得ています。 エンド ユーザーがアプリケーションに同意すると、Azure AD によって Azure portal の **[Enterprise apps]\(エンタープライズ アプリ\)** の下にアプリケーションが一覧表示されます。

## <a name="grant-admin-consent-when-registering-an-app-in-the-azure-portal"></a>Azure portal でアプリを登録するとき、管理者の同意を付与する

アプリを登録するときに管理者の同意を付与するには: 

1. [Azure Portal](https://portal.azure.com) にグローバル管理者としてサインインします。
2. **[アプリの登録]** ブレードに移動します。
3. 同意するアプリケーションを選択します。
4. **[必要なアクセス許可]** を選択します。
5. ブレードの上部にある **[アクセス許可の付与]** をクリックします。


## <a name="grant-admin-consent-through-a-url-request"></a>URL 要求を介して管理者の同意を付与する

URL 要求を介して管理者の同意を付与します。

1. アプリ構成を使用して *login.microsoftonline.com* に対する要求を作成し、そこに `&prompt=admin_consent` を追加します。 
2. 管理者の資格情報でサインインしたら、すべてのユーザーに関してアプリに同意が付与されます。


## <a name="force-user-consent-through-a-url-request"></a>URL 要求を介してユーザーの同意を強制する

認証のたびにアプリケーションへの同意をエンド ユーザーに要求するには、認証要求 URL に `&prompt=consent` を追加します。

## <a name="next-steps"></a>次の手順

[同意と Azure AD へのアプリの統合](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)

[Azure AD v2.0 集中型アプリの同意とアクセス許可](../develop/active-directory-v2-scopes.md)

[Azure AD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
