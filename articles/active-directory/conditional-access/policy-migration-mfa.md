---
title: Azure Portal での多要素認証が必要なクラシック ポリシーの移行
description: この記事では、Azure Portal で多要素認証が必要なクラシック ポリシーを移行する方法を示します。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: tutorial
ms.date: 06/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4819c283a136057ad7c3ffd755fd9e157d99a1bf
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509458"
---
# <a name="migrate-a-classic-policy-that-requires-multi-factor-authentication-in-the-azure-portal"></a>Azure Portal での多要素認証が必要なクラシック ポリシーの移行

このチュートリアルでは、クラウド アプリの**多要素認証**が必要なクラシック ポリシーを移行する方法を示します。 前提条件ではありませんが、クラシック ポリシーの移行を開始する前に「[Azure Portal でクラシック ポリシーを移行する](policy-migration.md)」を読むことをお勧めします。

## <a name="overview"></a>概要

この記事のシナリオでは、クラウド アプリの**多要素認証**が必要なクラシック ポリシーを移行する方法を示します。

![Azure Active Directory](./media/policy-migration/33.png)

移行プロセスは、次の手順で構成されます。

1. [クラシック ポリシーを開き](#open-a-classic-policy)、構成設定を取得します。
1. 新しい Azure AD 条件付きアクセス ポリシーを作成し、クラシック ポリシーを置き換えます。 
1. クラシック ポリシーを無効にします。

## <a name="open-a-classic-policy"></a>クラシック ポリシーを開く

1. [Azure Portal](https://portal.azure.com) の左側のナビゲーション バーで、 **[Azure Active Directory]** をクリックします。

   ![Azure Active Directory](./media/policy-migration-mfa/01.png)

1. **[Azure Active Directory]** ページの **[管理]** セクションで、 **[条件付きアクセス]** をクリックします。

   ![条件付きアクセス](./media/policy-migration-mfa/02.png)

1. **[管理]** セクションで、 **[クラシック ポリシー (プレビュー)]** をクリックします。

   ![クラシック ポリシー](./media/policy-migration-mfa/12.png)

1. クラシック ポリシーの一覧で、クラウド アプリの**多要素認証**が必要なポリシーをクリックします。

   ![クラシック ポリシー](./media/policy-migration-mfa/13.png)

## <a name="create-a-new-conditional-access-policy"></a>新しい条件付きアクセス ポリシーを作成する

1. [Azure Portal](https://portal.azure.com) の左側のナビゲーション バーで、 **[Azure Active Directory]** をクリックします。

   ![Azure Active Directory](./media/policy-migration/01.png)

1. **[Azure Active Directory]** ページの **[管理]** セクションで、 **[条件付きアクセス]** をクリックします。

   ![条件付きアクセス](./media/policy-migration/02.png)

1. **[条件付きアクセス]** ページで **[新規]** ページを開くには、上部のツール バーの **[追加]** をクリックします。

   ![条件付きアクセス](./media/policy-migration/03.png)

1. **[新規]** ページの **[名前]** ボックスに、ポリシーの名前を入力します。

   ![条件付きアクセス](./media/policy-migration/29.png)

1. **[割り当て]** セクションで、 **[ユーザーとグループ]** をクリックします。

   ![条件付きアクセス](./media/policy-migration/05.png)

   1. クラシック ポリシーですべてのユーザーを選択している場合は、 **[すべてのユーザー]** をクリックします。 

      ![条件付きアクセス](./media/policy-migration/35.png)

   1. クラシック ポリシーでグループを選択している場合は、 **[ユーザーとグループの選択]** をクリックし、必要なユーザーとグループを選択します。

      ![条件付きアクセス](./media/policy-migration/36.png)

   1. 除外されたグループがある場合は、 **[除外]** タブをクリックし、必要なユーザーとグループを選択します。 

      ![条件付きアクセス](./media/policy-migration/37.png)

1. **[新規]** ページで **[クラウド アプリ]** ページを開くには、 **[割り当て]** セクションで **[クラウド アプリ]** をクリックします。
1. **[クラウド アプリ]** ページで、次の手順を実行します。
   1. **[アプリを選択]** をクリックします。
   1. **[選択]** をクリックします。
   1. **[選択]** ページでクラウド アプリを選択し、 **[選択]** をクリックします。
   1. **[クラウド アプリ]** ページで、 **[完了]** をクリックします。
1. **[多要素認証を要求する]** を選択している場合:

   ![条件付きアクセス](./media/policy-migration/26.png)

   1. **[アクセス制御]** セクションで、 **[許可]** をクリックします。

      ![条件付きアクセス](./media/policy-migration/27.png)

   1. **[許可]** ページで、 **[アクセス権の付与]** をクリックし、 **[多要素認証を要求する]** をクリックします。
   1. **[選択]** をクリックします。
1. **[オン]** をクリックしてポリシーを有効にします。

   ![条件付きアクセス](./media/policy-migration/30.png)

## <a name="disable-the-classic-policy"></a>クラシック ポリシーを無効にする

クラシック ポリシーを無効にするには、 **[詳細]** ビューで **[無効]** をクリックします。

![クラシック ポリシー](./media/policy-migration-mfa/14.png)

## <a name="next-steps"></a>次の手順

- クラシック ポリシーの移行について詳しくは、「[Azure Portal でクラシック ポリシーを移行する](policy-migration.md)」をご覧ください。
- 条件付きアクセス ポリシーの構成方法を把握するには、「[Azure Active Directory の条件付きアクセスを使用して特定のアプリケーションに対して MFA を必要にする](app-based-mfa.md)」を参照してください。
- 環境に適用する条件付きアクセス ポリシーを構成する準備ができたら、「[Azure Active Directory の条件付きアクセスのベスト プラクティス](best-practices.md)」をご覧ください。
