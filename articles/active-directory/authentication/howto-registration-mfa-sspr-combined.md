---
title: 統合されたセキュリティ情報の登録の有効化 - Azure Active Directory
description: Azure AD Multi-Factor Authentication とセルフサービス パスワード リセットの統合された登録によってエンドユーザー エクスペリエンスを簡略化する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 01/27/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: dcfaef2f518028762958477a5b0d326acc237d1f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98938442"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>Azure Active Directory での統合されたセキュリティ情報の登録の有効化

統合された登録の前、ユーザーは Azure AD Multi-Factor Authentication (MFA) とセルフサービス パスワード リセット (SSPR) の認証方法を別々に登録しました。 ユーザーは Azure AD Multi-Factor Authentication と SSPR に同様の方法が使用されることに混乱していましたが、どちらの機能も登録する必要がありました。 現在では、統合された登録を使用することで、ユーザーは 1 回登録して Azure AD Multi-Factor Authentication と SSPR の両方の利点を得ることができます。

> [!NOTE]
> 2020 年 8 月 15 日以降は、新しい Azure AD テナントで統合されたすべての登録が自動的に有効になります。 

この新しいエクスペリエンスを有効にする前にその機能と効果を確実に把握するには、[統合されたセキュリティ情報の登録の概念](concept-registration-mfa-sspr-combined.md)に関する記事をご覧ください。

![統合されたセキュリティ情報登録の強化エクスペリエンス](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>統合された登録の有効化

統合された登録を有効にするには、次の手順に従います。

1. Azure portal にユーザー管理者または全体管理者としてサインインします。
2. **[Azure Active Directory]**  >  **[ユーザー設定]**  >  **[ユーザー機能プレビュー設定の管理]** に移動します。
3. **[ユーザーはセキュリティ情報の登録と管理のためにプレビュー機能を使用できます]** で、 **[選択済み]** のユーザーのグループまたは **[すべて]** のユーザーを選択して有効にします。

   ![ユーザーに対して統合されたセキュリティ情報のエクスペリエンスを有効にする](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info.png)

> [!NOTE]
> 統合された登録を有効にすると、新しいエクスペリエンスで電話番号やモバイル アプリを登録または確認したユーザーは、Azure AD Multi-Factor Authentication と SSPR ポリシーでこれらの方法が有効な場合、これらを Azure AD Multi-Factor Authentication と SSPR に使用できます。
>
> このエクスペリエンスを無効にすると、以前の SSPR 登録ページ (`https://aka.ms/ssprsetup`) にアクセスするユーザーは、ページにアクセスする前に多要素認証を実行する必要があります。

Internet Explorer で *サイトとゾーンの割り当て一覧* を構成した場合、以下のサイトは同じゾーン内に存在する必要があります。

* *[https://login.microsoftonline.com](https://login.microsoftonline.com)*
* *[https://mysignins.microsoft.com](https://mysignins.microsoft.com)*
* *[https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)*

## <a name="conditional-access-policies-for-combined-registration"></a>統合登録の条件付きアクセス ポリシー

ユーザーが Azure AD Multi-Factor Authentication とセルフサービス パスワード リセットの登録を実行するタイミングと方法をセキュリティで保護するため、条件付きアクセス ポリシーのユーザー アクションを使用できます。 この機能では、HR オンボード中に信頼できるネットワークの場所などの一元化された場所から Azure AD Multi-Factor Authentication と SSPR の登録をユーザーに行わせたい組織で有効にすることができます。

> [!NOTE]
> このポリシーは、ユーザーが統合登録ページにアクセスした場合にのみ適用されます。 このポリシーは、ユーザーが他のアプリケーションにアクセスしたときに MFA 登録を強制しません。
>
> MFA 登録ポリシーを作成するには、[Azure Identity Protection - MFA ポリシーの構成](../identity-protection/howto-identity-protection-configure-mfa-policy.md)を使用します。

条件付きアクセスでの信頼できる場所の作成について詳しくは、[Azure Active Directory 条件付きアクセスの場所の条件の概要](../conditional-access/location-condition.md#named-locations)に関する記事をご覧ください

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>信頼できる場所からの登録を要求するポリシーを作成する

次の手順に従って、統合された登録エクスペリエンスを使用して登録しようとするすべての選択ユーザーに適用され、信頼されたネットワークとしてマークされている場所から接続していない場合はアクセスをブロックするポリシーを作成します。

1. **Azure portal** で、 **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[条件付きアクセス]** に移動します。
1. **[新しいポリシー]** を選択します。
1. このポリシーの名前を入力します。たとえば、"*信頼されたネットワーク上の統合されたセキュリティ情報の登録*" などです。
1. **[割り当て]** で、 **[ユーザーとグループ]** を選択します。 このポリシーを適用するユーザーとグループを選択し、 **[完了]** を選択します。

   > [!WARNING]
   > 統合された登録に対してユーザーを有効にする必要があります。

1. **[クラウド アプリまたはアクション]** で、 **[ユーザー操作]** を選択します。 **[セキュリティ情報の登録]** をオンにし、 **[完了]** を選択します。

    ![セキュリティ情報の登録を制御する条件付きアクセスポリシーを作成する](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. **[条件]**  >  **[場所]** で、次のオプションを構成します。
   1. **[はい]** を構成します。
   1. **[任意の場所]** を含めます。
   1. **[すべての信頼できる場所]** を除外します。
1. *[場所]* ウィンドウで **[完了]** を選択し、 *[条件]* ウィンドウで **[完了]** を選択します。
1. **[アクセス制御]**  >  **[許可]** で、 **[アクセスのブロック]** 、 **[選択]** の順に選択します。
1. **[ポリシーを有効にする]** を **[オン]** に設定します。
1. ポリシーを完了するには、 **[作成]** を選択します。

## <a name="next-steps"></a>次のステップ

ヘルプが必要な場合は、「[統合されたセキュリティ情報の登録のトラブルシューティング](howto-registration-mfa-sspr-combined-troubleshoot.md)」または[Azure AD 条件付きアクセスの場所の条件の概要](../conditional-access/location-condition.md)に関する記事を参照してください。

統合された登録に対してユーザーを有効にした後は、[セルフサービス パスワード リセットを有効に](tutorial-enable-sspr.md)したり、[Azure AD Multi-Factor Authentication を有効に](tutorial-enable-azure-mfa.md)したりできます。

必要な場合は、[ユーザーに認証方法の再登録を強制する](howto-mfa-userdevicesettings.md#manage-user-authentication-options)方法を学習します。
