---
title: セルフサービス サインアップのユーザー フローを追加する - Azure AD
description: 組織が作成したアプリのユーザー フローを作成します。 その後、そのアプリにアクセスしたユーザーは、ユーザー フローで構成されているオプションを利用し、ゲスト アカウントを取得できます。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a59e573c9b24d9a8b5577b55d143fcaca67952f0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102120761"
---
# <a name="add-a-self-service-sign-up-user-flow-to-an-app"></a>セルフサービス サインアップのユーザー フローをアプリに追加する

> [!NOTE]
> この記事で説明する機能の一部は、Azure Active Directory のパブリック プレビュー機能です。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

構築するアプリケーションのために、ユーザーがアプリにサインアップして新しいゲスト アカウントを作成できるようにするユーザー フローを作成できます。 セルフサービス サインアップのユーザー フローでは、サインアップ時にユーザーが従う一連の手順、ユーザーに使用を許可する ID プロバイダー、収集するユーザー属性を定義します。 1 つのユーザー フローに、1 つ以上のアプリケーションを関連付けることができます。

> [!NOTE]
> 組織によって構築されたアプリにユーザー フローを関連付けることができます。 ユーザー フローは、SharePoint や Teams などの Microsoft アプリには使用できません。

## <a name="before-you-begin"></a>開始する前に

### <a name="add-identity-providers-optional"></a>ID プロバイダーを追加する (省略可能)

Azure AD は、セルフサービス サインアップ用の既定の ID プロバイダーです。 これは、ユーザーが既定で Azure AD アカウントを使用してサインアップできることを意味します。 セルフサービス サインアップのユーザー フローには、Google や Facebook、Microsoft アカウント (プレビュー)、電子メール ワンタイム パスコード (プレビュー) などのソーシャル ID プロバイダーを含めることもできます。

- [Microsoft アカウント (プレビュー) ID プロバイダー](microsoft-account.md)
- [電子メール ワンタイム パスコード認証](one-time-passcode.md)
- [ソーシャル ID プロバイダーの一覧に Facebook を追加する](facebook-federation.md)
- [ソーシャル ID プロバイダーの一覧に Google を追加する](google-federation.md)

### <a name="define-custom-attributes-optional"></a>カスタム属性を定義する (省略可能)

ユーザー属性は、セルフサービス サインアップ時にユーザーから収集される値です。 Azure AD には組み込みで一連の属性が付属していますが、ユーザー フローで使用するカスタム属性を作成することもできます。 また、Microsoft Graph API を使用してこれらの属性を読み書きすることもできます。 [ユーザー フローのカスタム属性の定義](user-flow-add-custom-attributes.md)に関する記事をご覧ください。

## <a name="enable-self-service-sign-up-for-your-tenant"></a>テナントのセルフサービス サインアップを有効にする

セルフサービス サインアップのユーザー フローをアプリケーションに追加する前に、テナントに対してこの機能を有効にする必要があります。 これを有効にすると、ユーザー フローでコントロールを使用できるようになり、これによってユーザー フローをアプリケーションに関連付けることができます。

1. [Azure Portal](https://portal.azure.com) に Azure AD 管理者としてサインインします。
2. **[Azure サービス]** で **[Azure Active Directory]** を選択します。
3. **[ユーザー設定]** を選択し、 **[外部ユーザー]** で、 **[外部コラボレーションの設定を管理します]** を選択します。
4. **[Enable guest self-service sign up via user flows]\(ユーザー フローによるゲスト セルフサービス サインアップを有効にする\)** を **[はい]** に切り替えます。

   ![ゲスト セルフサービス サインアップを有効にする](media/self-service-sign-up-user-flow/enable-self-service-sign-up.png)
5. **[保存]** を選択します。
## <a name="create-the-user-flow-for-self-service-sign-up"></a>セルフサービス サインアップのユーザー フローを作成する

次に、セルフサービス サインアップのユーザー フローを作成し、アプリケーションに追加します。

1. [Azure Portal](https://portal.azure.com) に Azure AD 管理者としてサインインします。
2. **[Azure サービス]** で **[Azure Active Directory]** を選択します。
3. 左側のメニューで、 **[External Identities]** を選択します。
4. **[ユーザー フロー]** を選択し、 **[新しいユーザー フロー]** を選択します。

   ![新しいユーザー フローの追加ボタン](media/self-service-sign-up-user-flow/new-user-flow.png)

5. **[作成]** ページで、ユーザー フローの **[名前]** を入力します。 名前の先頭には自動的に **B2X_1_** が付けられることに注意してください。
6. **[ID プロバイダー]** の一覧から、外部ユーザーがアプリケーションにログインするために使用できる ID プロバイダーを 1 つ以上選択します。 既定では **[Azure Active Directory サインアップ]** が選択されています。 (ID プロバイダーを追加する方法については、この記事の前半の「[開始する前に](#before-you-begin)」を参照してください。)
7. **[ユーザー属性]** で、ユーザーから収集する属性を選択します。 その他の属性については、 **[さらに表示]** を選択します。 たとえば、 **[Show more]\(さらに表示\)** を選択し、 **[国/リージョン]** 、 **[表示名]** 、 **[郵便番号]** の属性と要求を選択します。 **[OK]** を選択します。

   ![新しいユーザー フローの作成ページ](media/self-service-sign-up-user-flow/create-user-flow.png)

> [!NOTE]
> 初回のみ、ユーザーの新規登録時に属性を収集できます。 ユーザーの新規登録後は、ユーザー フローを変更する場合であっても、属性情報の収集は求められなくなります。

8. **［作成］** を選択します
9. 新しいユーザー フローは **[ユーザー フロー]** の一覧に表示されます。 必要に応じて、ページを更新してください。

## <a name="select-the-layout-of-the-attribute-collection-form"></a>属性コレクション フォームのレイアウトを選択する

サインアップ ページで属性を表示する順序を選択できます。 

1. [Azure Portal](https://portal.azure.com) で、 **[Azure Active Directory]** を選択します。
2. **[外部 ID]** を選択し、 **[ユーザー フロー]** を選択します。
3. 一覧から、セルフサービス サインアップのユーザー フローを選択します。
4. **[カスタマイズ]** で **[ページ レイアウト]** を選択します。
5. 収集することを選択した属性が一覧表示されます。 表示順序を変更するには、属性を選択した後、 **[上へ移動]** 、 **[下へ移動]** 、 **[Move to the top]\(一番上に移動\)** 、または **[Move to the bottom]\(一番下に移動\)** を選択します。
6. **[保存]** を選択します。

## <a name="add-applications-to-the-self-service-sign-up-user-flow"></a>セルフサービス サインアップのユーザー フローにアプリケーションを追加する

これで、アプリケーションをユーザー フローに関連付けることができます。

1. [Azure Portal](https://portal.azure.com) に Azure AD 管理者としてサインインします。
2. **[Azure サービス]** で **[Azure Active Directory]** を選択します。
3. 左側のメニューで、 **[External Identities]** を選択します。
4. **[セルフサービス サインアップ]** で、 **[ユーザー フロー]** を選択します。
5. 一覧から、セルフサービス サインアップのユーザー フローを選択します。
6. 左側のメニューで、 **[使用]** の下の **[アプリケーション]** を選択します。
7. **[アプリケーションの追加]** をクリックします。

   ![アプリケーションをユーザー フローに割り当てる](media/self-service-sign-up-user-flow/assign-app-to-user-flow.png)

8. 一覧からアプリケーションを選択します。 または、検索ボックスを使用してアプリケーションを検索し、それを選択します。
9. **[選択]** をクリックします。

## <a name="next-steps"></a>次のステップ

- [ソーシャル ID プロバイダーの一覧に Google を追加する](google-federation.md)
- [ソーシャル ID プロバイダーの一覧に Facebook を追加する](facebook-federation.md)
- [API コネクタを使用して、Web API 経由でユーザー フローをカスタマイズおよび拡張する](api-connectors-overview.md)
- [ユーザー フローにカスタム承認ワークフローを追加する](self-service-sign-up-add-approvals.md)
