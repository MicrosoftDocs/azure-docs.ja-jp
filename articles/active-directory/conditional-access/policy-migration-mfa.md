---
title: 多要素認証による条件付きアクセス ポリシーの移行 - Azure Active Directory
description: この記事では、Azure Portal で多要素認証が必要なクラシック ポリシーを移行する方法を示します。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cc99241f5fa7c09c69db13dcb3f6c3aaa75e376
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87027395"
---
# <a name="migrate-a-classic-policy-in-the-azure-portal"></a>Azure portal でクラシック ポリシーを移行する

この記事では、クラウド アプリの **多要素認証** が必要なクラシック ポリシーを移行する方法を示します。 前提条件ではありませんが、クラシック ポリシーの移行を開始する前に「[Azure Portal でクラシック ポリシーを移行する](policy-migration.md)」を読むことをお勧めします。

![Salesforce アプリの MFA を要求とするクラシック ポリシーの詳細](./media/policy-migration/33.png)

移行プロセスは、次の手順で構成されます。

1. [クラシック ポリシーを開き](#open-a-classic-policy)、構成設定を取得します。
1. 新しい Azure AD 条件付きアクセス ポリシーを作成し、クラシック ポリシーを置き換えます。 
1. クラシック ポリシーを無効にします。

## <a name="open-a-classic-policy"></a>クラシック ポリシーを開く

1. [Azure portal](https://portal.azure.com) で、 **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[条件付きアクセス]** の順に移動します。
1. **[クラシック ポリシー]** を選択します。

   ![クラシック ポリシーのビュー](./media/policy-migration-mfa/12.png)

1. クラシック ポリシーの一覧で、移行するポリシーを選択します。 新しい条件付きアクセス ポリシーを使用して再作成できるように、構成設定をドキュメント化します。

## <a name="create-a-new-conditional-access-policy"></a>新しい条件付きアクセス ポリシーを作成する

1. [Azure portal](https://portal.azure.com) で、 **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[条件付きアクセス]** の順に移動します。
1. 新しい条件付きアクセスポリシーを作成するには、 **[新しいポリシー]** を選択します。
1. **[新規]** ページの **[名前]** ボックスに、ポリシーの名前を入力します。
1. **[割り当て]** セクションで、 **[ユーザーとグループ]** をクリックします。
   1. クラシック ポリシーですべてのユーザーを選択している場合は、 **[すべてのユーザー]** をクリックします。 
   1. クラシック ポリシーでグループを選択している場合は、 **[ユーザーとグループの選択]** をクリックし、必要なユーザーとグループを選択します。
   1. 除外されたグループがある場合は、 **[除外]** タブをクリックし、必要なユーザーとグループを選択します。 
   1. **[完了]** を選択します
1. **[割り当て]** セクションで、 **[クラウド アプリまたは操作]** をクリックします。
1. **[クラウド アプリまたは操作]** ページで、次の手順を行います。
   1. **[アプリを選択]** をクリックします。
   1. **[選択]** をクリックします。
   1. **[選択]** ページでクラウド アプリを選択し、 **[選択]** をクリックします。
   1. **[クラウド アプリ]** ページで、 **[完了]** をクリックします。
1. **[多要素認証を要求する]** を選択している場合:
   1. **[アクセス制御]** セクションで、 **[許可]** をクリックします。
   1. **[許可]** ページで、 **[アクセス権の付与]** をクリックし、 **[多要素認証を要求する]** をクリックします。
   1. **[選択]** をクリックします。
1. **[オン]** をクリックして使用するポリシーを有効にしてから、 **[保存]** を選択します。

   ![条件付きアクセス ポリシーの作成](./media/policy-migration-mfa/conditional-access-policy-migration.png)

## <a name="disable-the-classic-policy"></a>クラシック ポリシーを無効にする

クラシック ポリシーを無効にするには、 **[詳細]** ビューで **[無効]** をクリックします。

![クラシック ポリシーを無効にする](./media/policy-migration-mfa/14.png)

## <a name="next-steps"></a>次のステップ

- クラシック ポリシーの移行について詳しくは、「[Azure Portal でクラシック ポリシーを移行する](policy-migration.md)」をご覧ください。
- [条件付きアクセスのレポート専用モードを使用して、新しいポリシー決定の影響を判断します。](concept-conditional-access-report-only.md)