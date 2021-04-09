---
title: 条件付きアクセス - 統合されたセキュリティ情報 - Azure Active Directory
description: セキュリティ情報登録のためのカスタム条件付きアクセス ポリシーを作成する
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e99f7466bd3b7ed5517157ca3fa45e7c3241217
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98599752"
---
# <a name="conditional-access-securing-security-info-registration"></a>条件付きアクセス:セキュリティ情報登録のセキュリティ保護

Azure AD Multi-Factor Authentication とセルフサービス パスワード リセットの登録をユーザーがいつどのように行うかについてのセキュリティ保護が、条件付きアクセス ポリシーのユーザー アクションを使用して可能になりました。 このプレビュー機能は、[統合登録プレビュー](../authentication/concept-registration-mfa-sspr-combined.md)を有効にしている組織で使用することができます。 この機能は、信頼できるネットワークの場所などの条件を使用して、Azure AD Multi-Factor Authentication およびセルフサービス パスワード リセット (SSPR) への登録へのアクセスを制限したい組織で有効にすることができます。 使用可能な条件の詳細については、「[条件付きアクセス: 条件](concept-conditional-access-conditions.md)」を参照してください。

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>信頼できる場所からの登録を要求するポリシーを作成する

次のポリシーは、統合された登録エクスペリエンスを使用して登録を試みるすべての選択ユーザーに適用され、信頼されたネットワークとマークされている場所から接続している場合を除き、アクセスをブロックします。

1. **Azure portal** で、 **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[条件付きアクセス]** に移動します。
1. **[新しいポリシー]** を選択します。
1. [名前] に、このポリシーの名前を入力します。 たとえば、「**信頼されたネットワーク上の統合されたセキュリティ情報の登録**」など。
1. **[割り当て]** から **[ユーザーとグループ]** を選択し、このポリシーを適用するユーザーとグループを選択します。

   > [!WARNING]
   > [[統合された登録]](../authentication/howto-registration-mfa-sspr-combined.md) に対してユーザーを有効にする必要があります。

   1. **[除外]** で、 **[ユーザーとグループ]** を選択し、組織の緊急アクセス用または非常用アカウントを選択します。 
   1. **[Done]** を選択します。
1. **[クラウド アプリまたはアクション]** から **[ユーザー アクション]** を選択し、 **[セキュリティ情報の登録]** をオンにします。
1. **[条件]**  >  **[場所]** で
   1. **[はい]** を構成します。
   1. **[任意の場所]** を含めます。
   1. **[すべての信頼できる場所]** を除外します。
   1. [場所] ブレードで **[完了]** を選択します。
   1. [条件] ブレードで **[完了]** を選択します。
1. **[条件]**  >  **[クライアント アプリ (プレビュー)]** で、 **[構成する]** を **[はい]** に設定し、 **[完了]** を選択します。
1. **[アクセス制御]**  >  **[付与]** で
   1. **[アクセスのブロック]** を選択します。
   1. **[選択]** をクリックします。
1. **[ポリシーを有効にする]** を **[オン]** に設定します。
1. 次に、 **[保存]** を選択します。

このポリシーの手順 6 では、組織には選択肢が用意されています。 上記のポリシーでは、信頼できるネットワークの場所からの登録が必要となります。 組織は、 **[場所]** の代わりに、利用可能な任意の条件を使用することを選択できます。 このポリシーはブロックのためのポリシーであるため、含まれているものはすべてブロックされ、含まれているものに一致しないものはすべて許可されることに注意してください。 

上記の手順 6 で、場所ではなくデバイスの状態を使用することもできます。

6. **[条件]**  >  **[デバイスの状態 (プレビュー)]** で
   1. **[はい]** を構成します。
   1. **[すべてのデバイスの状態]** を含めます。
   1. **[ハイブリッド Azure AD 参加済みのデバイス]** と **[デバイスは準拠としてマーク済み]** の両方またはどちらかを除外します。
   1. [場所] ブレードで **[完了]** を選択します。
   1. [条件] ブレードで **[完了]** を選択します。

> [!WARNING]
> ポリシー内で条件としてデバイスの状態を使用すると、ディレクトリのゲスト ユーザーに影響を与える可能性があります。 [レポート専用モード](concept-conditional-access-report-only.md)は、ポリシーの決定の影響を判断するのに役立ちます。
> "ユーザー アクション" を範囲とする条件付きアクセス ポリシーにはレポート専用モードは適用されないことに注意してください。

## <a name="next-steps"></a>次のステップ

[Conditional Access common policies](concept-conditional-access-policy-common.md) (条件付きアクセスの一般的なポリシー)

[条件付きアクセスのレポート専用モードを使用した影響を判断する](howto-conditional-access-insights-reporting.md)

[Simulate sign in behavior using the Conditional Access What If tool](troubleshoot-conditional-access-what-if.md) (条件付きアクセスの What If ツールを使用したサインイン動作のシミュレート)
