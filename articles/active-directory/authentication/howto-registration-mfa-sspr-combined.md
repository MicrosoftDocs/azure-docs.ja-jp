---
title: Azure AD SSPR と Multi-Factor Authentication のための統合された登録の概要 (プレビュー) - Azure Active Directory
description: Azure AD Multi-Factor Authentication とセルフサービスのパスワード リセットの統合された登録の有効化 (プレビュー)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3baf2690ae07b87bb4d5dba30fcd20f62a1a4506
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2019
ms.locfileid: "59280572"
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
2. **[Azure Active Directory]** > **[ユーザー設定]** > **[アクセス パネル プレビュー機能の設定を管理]** の順に移動します。
3. **[ユーザーはセキュリティ情報の登録と管理のためにプレビュー機能を使用できます – 更新]** で、**[選択済み]** のユーザーのグループまたは **[すべて]** のユーザーのうちのどちらを有効にするかを選択します。

   ![すべてのユーザーに対して統合されたセキュリティ情報のプレビュー エクスペリエンスを有効にする](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!IMPORTANT]
> 2019 年 3 月以降、無料/試用版の Azure AD テナントの Multi-Factor Authentication および SSPR ユーザーは、音声通話オプションを利用できなくなります。 この変更は、SMS メッセージには影響しません。 有料の Azure AD テナントのユーザーは、引き続き音声通話オプションを利用できます。

> [!NOTE]
> 統合された登録を有効にすると、新しいエクスペリエンスで電話番号やモバイル アプリを登録または確認したユーザーは、Multi-Factor Authentication と SSPR ポリシーでこれらの方法が有効な場合、これらを Multi-Factor Authentication と SSPR に使用できます。 このエクスペリエンスを無効にすると、以前の SSPR 登録ページ (`https:/aka.ms/ssprsetup`) にアクセスするユーザーは、ページにアクセスする前に多要素認証を実行する必要があります。

Internet Explorer でサイトとゾーンの割り当て一覧を構成した場合、以下のサイトは同じゾーン内に存在する必要があります。

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="next-steps"></a>次の手順

[Multi-Factor Authentication と SSPR で使用可能な方法](concept-authentication-methods.md)

[セルフサービスのパスワードのリセットを構成する](howto-sspr-deployment.md)

[Azure Multi-Factor Authentication の構成](howto-mfa-getstarted.md)

[統合されたセキュリティ情報の登録のトラブルシューティング](howto-registration-mfa-sspr-combined-troubleshoot.md)