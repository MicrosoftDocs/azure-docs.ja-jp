---
title: Azure AD SSPR と Multi-Factor Authentication のための統合された登録の概要 (プレビュー) - Azure Active Directory
description: Azure AD Multi-Factor Authentication とセルフサービスのパスワード リセットの統合された登録の有効化 (プレビュー)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: d006bd36cc8f8c84fb13bae43702a3e472f8876a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67113290"
---
# <a name="enable-combined-security-information-registration-preview"></a>統合されたセキュリティ情報の登録の有効化 (プレビュー)

この新しいエクスペリエンスを有効にする前に、記事「[統合されたセキュリティ情報の登録 (プレビュー)](concept-registration-mfa-sspr-combined.md)」を参照し、この機能の効果と影響を必ず理解してください。

![統合されたセキュリティ情報登録の強化エクスペリエンス](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| Azure Multi-Factor Authentication と Azure Active Directory (Azure AD) セルフサービスのパスワード リセットのための統合されたセキュリティ情報の登録は、Azure AD のパブリック プレビュー機能です。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。|
|     |

## <a name="enable-combined-registration"></a>統合された登録の有効化

統合された登録を有効にするには、次の手順に従います。

1. Azure portal にユーザー管理者または全体管理者としてサインインします。
2. **[Azure Active Directory]**  >  **[ユーザー設定]**  >  **[アクセス パネル プレビュー機能の設定を管理]** の順に移動します。
3. **[ユーザーはセキュリティ情報の登録と管理のためにプレビュー機能を使用できます – 更新]** で、 **[選択済み]** のユーザーのグループまたは **[すべて]** のユーザーのうちのどちらを有効にするかを選択します。

   ![すべてのユーザーに対して統合されたセキュリティ情報のプレビュー エクスペリエンスを有効にする](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!IMPORTANT]
> 2019 年 3 月以降、無料/試用版の Azure AD テナントの Multi-Factor Authentication および SSPR ユーザーは、音声通話オプションを利用できなくなります。 この変更は、SMS メッセージには影響しません。 有料の Azure AD テナントのユーザーは、引き続き音声通話オプションを利用できます。

> [!NOTE]
> 統合された登録を有効にすると、新しいエクスペリエンスで電話番号やモバイル アプリを登録または確認したユーザーは、Multi-Factor Authentication と SSPR ポリシーでこれらの方法が有効な場合、これらを Multi-Factor Authentication と SSPR に使用できます。 このエクスペリエンスを無効にすると、以前の SSPR 登録ページ (`https://aka.ms/ssprsetup`) にアクセスするユーザーは、ページにアクセスする前に多要素認証を実行する必要があります。

Internet Explorer でサイトとゾーンの割り当て一覧を構成した場合、以下のサイトは同じゾーン内に存在する必要があります。

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>統合登録の条件付きアクセス ポリシー

Azure Multi-Factor Authentication とパスワード リセットのセルフサービスの登録をユーザーがいつどのように行うかについてのセキュリティ保護が、条件付きアクセス ポリシーのユーザー アクションを使用して可能になりました。 このプレビュー機能は、[統合登録プレビュー](../authentication/concept-registration-mfa-sspr-combined.md)を有効にしている組織で使用することができます。 この機能は、HR オンボード中に信頼できるネットワークの場所などの一元化された場所から Azure Multi-Factor Authentication および SSPR の登録をユーザーに行わせたい組織で有効にすることができます。 条件付きアクセスでの信頼できる場所の作成について詳しくは、[Azure Active Directory 条件付きアクセスの場所の条件の概要](../conditional-access/location-condition.md#named-locations)に関する記事をご覧ください。

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>信頼できる場所からの登録を要求するポリシーを作成する

次のポリシーは、統合された登録エクスペリエンスを使用して登録を試みるすべての選択ユーザーに適用され、信頼されたネットワークとマークされている場所から接続している場合を除き、アクセスをブロックします。

![セキュリティ情報の登録を制御する CA ポリシーを作成する](media/howto-registration-mfa-sspr-combined/conditional-access-register-security-info.png)

1. **Azure portal** で、 **[Azure Active Directory]**  >  **[条件付きアクセス]** に移動します
1. **[新しいポリシー]** を選択します
1. [名前] に、このポリシーの名前を入力します。 たとえば、「**信頼されたネットワーク上の統合されたセキュリティ情報の登録**」など
1. **[割り当て]** から **[ユーザーとグループ]** をクリックし、このポリシーを適用するユーザーとグループを選択します

   > [!WARNING]
   > [[統合された登録プレビュー]](../authentication/howto-registration-mfa-sspr-combined.md) に対してユーザーを有効にする必要があります。

1. **[クラウド アプリまたはアクション]** から **[ユーザー アクション]** を選択し、 **[セキュリティ情報の登録 (プレビュー)]** をオンにします
1. **[条件]**  >  **[場所]** で
   1. **[はい]** を構成します
   1. **[任意の場所]** を含めます
   1. **[すべての信頼できる場所]** を除外します
   1. [場所] ブレードで **[完了]** をクリックします
   1. [条件] ブレードで **[完了]** をクリックします
1. **[アクセス制御]**  >  **[付与]** で
   1. **[アクセスのブロック]** をクリックします
   1. 次に、 **[選択]** をクリックします
1. **[ポリシーを有効にする]** を **[オン]** に設定します
1. 次に、 **[作成]** をクリックします

## <a name="next-steps"></a>次の手順

[Multi-Factor Authentication と SSPR で使用可能な方法](concept-authentication-methods.md)

[セルフサービスのパスワード リセットを構成する](howto-sspr-deployment.md)

[Azure Multi-Factor Authentication を構成する](howto-mfa-getstarted.md)

[統合されたセキュリティ情報の登録のトラブルシューティング](howto-registration-mfa-sspr-combined-troubleshoot.md)

[Azure Active Directory 条件付きアクセスの場所の条件の概要](../conditional-access/location-condition.md)
