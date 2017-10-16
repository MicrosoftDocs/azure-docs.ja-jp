---
title: "Azure AD Connect: パススルー認証 - しくみ | Microsoft Docs"
description: "この記事では、Azure Active Directory のパススルー認証のしくみについて説明します。"
services: active-directory
keywords: "Azure AD Connect パススルー認証, Active Directory のインストール, Azure AD に必要なコンポーネント, SSO, シングル サインオン"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: a6bba6b3b924564fe7ae16fa1265dd4d93bd6b94
ms.openlocfilehash: 9507a718ba982eb8b3381a6ef70156f075729161
ms.contentlocale: ja-jp
ms.lasthandoff: 09/28/2017

---

# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Azure Active Directory パススルー認証: 技術的な詳細
次の記事では、Azure AD パススルー認証のしくみを概説します。  詳細な技術およびセキュリティの情報については、[**「Azure AD Pass-through Authentication Security Deep Dive」**](active-directory-aadconnect-pta-security-deep-dive.md)(Azure AD パススルー認証のセキュリティの詳細) を参照してください。

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Azure Active Directory パススルー認証のしくみ

ユーザーが Azure Active Directory (AD) で保護されているアプリケーションにサインインしようとし、テナントでパススルー認証が有効になっている場合、次の手順が発生します。

1. ユーザーが、(https://outlook.office365.com/owa/ の Outlook Web App などの) アプリケーションへのアクセスを試行します。
2. まだサインインしていない場合、ユーザーは、Azure AD のサインイン ページにリダイレクトされます。
3. ユーザーが Azure AD のサインイン ページにユーザー名とパスワードを入力し、[サインイン] ボタンをクリックします。
4. サインイン要求を受け取った Azure AD は、(公開キーを使用して暗号化された) ユーザー名とパスワードをキューに配置します。
5. オンプレミスのパススルー認証エージェントが送信呼び出しをキューに実行し、ユーザー名と暗号化されたパスワードが取得されます。
6. エージェントがその秘密キーを使用してパスワードを復号化します。
7. エージェントは、標準の Windows API を使用して Active Directory に対してユーザー名とパスワードを検証します (Active Directory フェデレーション サービスで使用されているものと同様のメカニズム)。 ユーザー名には、オンプレミスの既定のユーザー名 (通常は `userPrincipalName`) または Azure AD Connect で構成された別の属性 (`Alternate ID` として知られています) を指定できます。
8. その後、オンプレミスの Active Directory ドメイン コントローラー (DC) が要求を評価し、適切な応答をエージェントに返します (成功、失敗、パスワードの期限切れ、またはユーザーがロックアウト)。
9. エージェントは代わりにこの応答を Azure AD に返します。
10. Azure AD は、応答を評価し、必要に応じてユーザーに応答します。たとえば、ユーザーを直ちにサインインさせるか、Multi-factor Authentication (MFA) を要求します。
11. ユーザーのサインインが成功すると、アプリケーションにアクセスできるようになります。

次の図に、すべてのコンポーネントと必要な手順を示します。

![パススルー認証](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

## <a name="next-steps"></a>次のステップ
- [**現在の制限**](active-directory-aadconnect-pass-through-authentication-current-limitations.md) - サポートされているシナリオと、サポートされていないシナリオを確認します。
- [**クイック スタート**](active-directory-aadconnect-pass-through-authentication-quick-start.md) - Azure AD パススルー認証を起動および実行します。
- [**よく寄せられる質問**](active-directory-aadconnect-pass-through-authentication-faq.md) - よく寄せられる質問と回答です。
- [**トラブルシューティング**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) - 機能に関する一般的な問題を解決する方法を確認します。
- [**Azure AD シームレス SSO**](active-directory-aadconnect-sso.md) - この補完的な機能の詳細を確認します。
- [**セキュリティの詳細** ](active-directory-aadconnect-pta-security-deep-dive.md) - Azure AD パススルー認証に関する追加の詳細な技術情報です。
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 新しい機能の要求を提出します。

