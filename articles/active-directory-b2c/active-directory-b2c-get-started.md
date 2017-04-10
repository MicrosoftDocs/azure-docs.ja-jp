---
title: "Azure Active Directory B2C: Azure Active Directory B2C テナントを作成する | Microsoft Docs"
description: "Azure Active Directory B2C テナントの作成方法に関するトピック"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: eec4d418-453f-4755-8b30-5ed997841b56
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 01/26/2017
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 02bb7286f6cd42f86e6cc920742a86d57ed93b60
ms.lasthandoff: 03/09/2017


---
# <a name="azure-active-directory-b2c-create-an-azure-ad-b2c-tenant"></a>Azure Active Directory B2C: Azure AD B2C テナントを作成する
Microsoft Azure Active Directory (Azure AD) B2C の使用を開始するには、この記事で説明する&4; つの手順に従います。

## <a name="step-1-sign-up-for-an-azure-subscription"></a>手順 1: Azure サブスクリプションにサインアップする
既に Azure サブスクリプションがある場合は、この手順を省略できます。 ない場合は、 [Azure サブスクリプション](../active-directory/sign-up-organization.md) にサインアップし、Azure AD B2C にアクセスできるようにします。

## <a name="step-2-create-an-azure-ad-b2c-tenant"></a>手順 2: Azure AD B2C テナントを作成する
次の手順に従って、新しい Azure AD B2C テナントを作成します。 現在、既存のテナントで B2C の機能を有効にすることはできません。

1. サブスクリプション管理者として [Azure Portal](https://portal.azure.com/) にサインインします。 これは、Azure へのサインアップに使用したものと同じ職場/学校アカウント、または同じ Microsoft アカウントです。
2. **[新規]** をクリックします (折りたたまれている場合は [+] ボタンをクリックします)。**[Marketplace を検索 ]** フィールドに「Azure Active Directory B2C」と入力します。
   
    ![Azure AD B2C オプションを検索する画面のスクリーンショット](./media/active-directory-b2c-get-started/find-azure-ad-b2c.png)
3. 結果から **[Azure Active Directory B2C]** をクリックします。

    ![Azure Active Directory B2C の結果のスクリーンショット](./media/active-directory-b2c-get-started/find-azure-ad-b2c-result.png)
4. B2C の詳細を示すページが表示されます。  新しい Azure Active Directory B2C テナントの構成を開始するには、画面の下部にある **[作成]** をクリックします。
5. **[Create a new Azure AD B2C Tenant (新しい Azure AD B2C テナントの作成)]** をクリックします。
6. テナントの**組織名、ドメイン名、および国/リージョン**を選択します。

    ![新しいテナント作成用フォームのスクリーンショット](./media/active-directory-b2c-get-started/create-new-b2c-tenant.png)
7. **[作成]** をクリックして、新しいテナントを作成します。  この操作は数分間かかることがあります。完了すると、通知が表示されます。

8. テナントが作成され、Active Directory 拡張機能に表示されます。 また、現在のユーザーがテナントのグローバル管理者になります。 必要に応じて、他のグローバル管理者を追加できます。
   
   > [!IMPORTANT]
   > B2C テナントを運用アプリ向けに使用する予定がある場合は、 [運用スケールとプレビュー B2C テナントの比較](active-directory-b2c-reference-tenant-type.md)に関する記事を参照してください。 既存の B2C テナントを削除し、同じドメイン名でそれを再作成する場合に、既知の問題があることに注意してください。 B2C テナントは異なるドメイン名で作成する必要があります。
   > 
   > 

## <a name="step-3-navigate-to-the-b2c-features-blade-on-the-azure-portal"></a>手順 3: Azure Portal で B2C 機能ブレードに移動する
1. 左側にあるナビゲーション バーの下にある **[その他のサービス]** を展開します。
2. **Azure AD B2C** を検索し、結果をクリックします (今後、簡単にアクセスできるようにお気に入りとして設定できます)。

    ![Azure AD B2C のナビゲーション ウィンドウでの検索のスクリーンショット](./media/active-directory-b2c-get-started/navigate-to-azure-ad-b2c.png)

3. 新しいブラウザー タブまたはウィンドウで、B2C 機能ブレードが含まれた Azure Portal が開きます。
   
4. アクセスしやすいように、このブレードをスタート画面にピン留めします (ピン留めツールは、機能ブレードの右上にあります)。
   
    ![B2C 機能ブレードとピン留めボタンのスクリーンショット](./media/active-directory-b2c-get-started/b2c-pin-tenant.png)

## <a name="step-4-link-your-azure-ad-b2c-tenant-to-your-azure-subscription"></a>手順 4: Azure AD B2C テナントを Azure サブスクリプションにリンクする
運用アプリで B2C テナントを使用する場合、利用料金を支払うために、Azure AD B2C テナントを Azure サブスクリプションにリンクする必要があります。 この方法については、[こちらの記事](active-directory-b2c-how-to-enable-billing.md)をご覧ください。

   > [!IMPORTANT]
   > Azure AD B2C テナントを Azure サブスクリプションにリンクしていない場合、Azure Portal の B2C 機能ブレードに警告メッセージ ("No Subscription linked to this B2C tenant or the Subscription needs your attention. (この B2C テナントにはサブスクリプションがリンクされていません。または、このサブスクリプションには注意が必要です。)") が表示されます。 アプリを運用環境に移行する前に、この手順を実行することが重要です。
   > 
   > 

## <a name="easy-access-to-the-b2c-features-blade-on-the-azure-portal"></a>Azure Portal の B2C 機能ブレードへの簡単アクセス
Azure Portal の B2C 機能ブレードへのショートカットを追加し、アクセスしやすくしました。

1. B2C テナントの全体管理者として Azure Portal にサインインします。 既に別のテナントにサインインしている場合は、テナントを切り替えます (右上隅)。
2. 左側のナビゲーションで **[参照]** をクリックします。
3. **[Azure AD B2C]** をクリックして B2C 機能ブレードにアクセスします。
   
    ![Screen shot of Browse to B2C features blade](./media/active-directory-b2c-get-started/b2c-browse.png)

## <a name="next-steps"></a>次のステップ
「 [Azure Active Directory B2C: アプリケーションを登録する](active-directory-b2c-app-registration.md)」を読み、Azure AD B2C にアプリケーションを登録する方法と、クイック スタート アプリケーションを構築する方法を学習します。


