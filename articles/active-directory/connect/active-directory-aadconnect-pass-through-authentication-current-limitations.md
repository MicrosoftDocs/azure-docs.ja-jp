---
title: "Azure AD Connect: パススルー認証 - 現在の制限事項 | Microsoft Docs"
description: "この記事では、Azure Active Directory (Azure AD) パススルー認証の現在の制限事項について説明します。"
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
ms.date: 09/18/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 6e193efe391c614a17d2861d4ba7d7776a7d441c
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---

# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Azure Active Directory パススルー認証: 現在の制限事項

>[!IMPORTANT]
>Azure AD のパススルー認証は無料の機能であり、それを使用するために Azure AD の有料エディションは必要ありません。 パススルー認証は、世界中の Azure AD のインスタンスでのみ使用できます。[Microsoft Cloud Germany](http://www.microsoft.de/cloud-deutschland) と [Microsoft Azure Government クラウド](https://azure.microsoft.com/features/gov/)では使用できません。

## <a name="supported-scenarios"></a>サポートされるシナリオ

次のシナリオは完全にサポートされています。

- すべての Web ブラウザー ベースのアプリケーションへのユーザー サインイン。
- [最新の認証](https://aka.ms/modernauthga)をサポートする Office 365 クライアント アプリケーションへのユーザー サインイン。
- Windows 10 デバイスの Azure AD Join。
- Exchange ActiveSync のサポート。

## <a name="unsupported-scenarios"></a>サポートされていないシナリオ

次のシナリオはサポートされて_いません_。

- 従来の Office クライアント アプリケーション (Office 2013 以前) へのユーザー サインイン。 組織は、可能であれば最新の認証に切り替えることが推奨されます。 最新の認証により、パススルー認証のサポートが可能になるだけでなく、Multi-Factor Authentication (MFA) などの[条件付きアクセス](../active-directory-conditional-access.md)機能を使用してユーザー アカウントをセキュリティで保護することもできます。
- Skype for Business クライアント アプリケーション (Skype for Business 2016 を含む) へのユーザー サインイン。
- PowerShell v1.0 へのユーザー サインイン。 代わりに、PowerShell v2.0 を使用することをお勧めします。
- MFA のためのアプリ パスワード。
- [資格情報が漏洩した](../active-directory-reporting-risk-events.md#leaked-credentials)ユーザーの検出。

>[!IMPORTANT]
>サポートされていないシナリオに対処するために、Azure AD Connect ウィザードの [[オプション機能]](active-directory-aadconnect-get-started-custom.md#optional-features) ページでパスワード ハッシュ同期を有効にします。 パスワード ハッシュ同期は、前に示したシナリオのフォールバックとして "_のみ_" 機能します (パススルー認証への汎用フォールバックとしては機能 "_しません_")。 パスワード ハッシュの同期を有効にすると、オンプレミスのインフラストラクチャが中断された場合に (Microsoft サポート経由で) 認証をフェールオーバーするオプションも提供されます。

## <a name="next-steps"></a>次のステップ
- [**クイック スタート**](active-directory-aadconnect-pass-through-authentication-quick-start.md) - Azure AD パススルー認証を起動および実行します。
- [**技術的な詳細**](active-directory-aadconnect-pass-through-authentication-how-it-works.md) - この機能のしくみを確認します。
- [**よく寄せられる質問**](active-directory-aadconnect-pass-through-authentication-faq.md) - よく寄せられる質問と回答です。
- [**トラブルシューティング**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) - 機能に関する一般的な問題を解決する方法を確認します。
- [**Azure AD シームレス SSO**](active-directory-aadconnect-sso.md) - この補完的な機能の詳細を確認します。
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 新しい機能の要求を提出します。

