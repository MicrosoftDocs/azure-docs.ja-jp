---
title: "Azure AD 階層型パスワード セキュリティ | Microsoft Docs"
description: "Azure AD が強力なパスワードを強制し、サイバー犯罪からユーザーのパスワードを守るしくみについて説明します。"
services: active-directory
documentationcenter: 
author: barlanmsft
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: barlan
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: de9d41cfb157c71f324f5662c1c037ff515e7108
ms.contentlocale: ja-jp
ms.lasthandoff: 08/29/2017

---
# <a name="a-multi-tiered-approach-to-azure-ad-password-security"></a>多層を利用した Azure AD パスワード セキュリティ

この記事では、Azure Active Directory (Azure AD) または Microsoft アカウントを保護するために皆さんがユーザーとして、または管理者として実践できるベスト プラクティスについて説明します。

 > [!NOTE]
 > **サインインに問題がありますか?** その場合は、[自分のパスワードを変更してリセットする方法をここから参照してください](active-directory-passwords-update-your-own-password.md)にお進みください。
 >
 > Azure AD 管理者は「[Azure Active Directory でのユーザー パスワードのリセット](active-directory-users-reset-password-azure-portal.md)」という記事にある手引きでユーザー パスワードをリセットできます。
 >

## <a name="password-requirements"></a>パスワード要件

パスワードのセキュリティを確保するための一般的な手段として、Azure AD には次のアプローチが導入されています。

* パスワードの長さの要件
* パスワードの複雑さの要件
* パスワードの有効期限 (定期的に変更を強制)

Azure Active Directory のパスワード リセットに関する詳細については、「[IT プロフェッショナルにとっての Azure AD のセルフサービスによるパスワードのリセット](active-directory-passwords.md)」というトピックを参照してください。

## <a name="azure-ad-password-protections"></a>Azure AD のパスワード保護

ユーザーと管理者のパスワードを確実に保護するために、Azure AD と Microsoft アカウント システムでは業界で広く認められている次のような手法が使われています。

* パスワードの動的禁止
* Smart Password Lockout

最新の研究に基づくパスワード管理については、ホワイトペーパー「[Password Guidance](http://aka.ms/passwordguidance)」 (パスワード ガイダンス) を参照してください。

### <a name="dynamically-banned-passwords"></a>パスワードの動的禁止

Azure AD と Microsoft アカウントでは、パスワードを確実に保護するために、よく使われているパスワードが動的に禁止されています。 Azure ID Identity Protection チームは、禁止パスワード リストを定期的に分析し、ありきたりのパスワードをユーザーが選択できないようにしています。 このサービスは、Azure AD と Microsoft アカウント サービスのユーザーが利用できます。

管理者は、パスワードを作成するユーザーに、アルファベットや数字、文字、単語を不規則に組み合わせたパスワード フレーズを奨励することが重要です。 この手法では、ユーザーにとって覚えやすいが、見抜かれる危険がほとんどないパスワードを作成できます。

#### <a name="password-breaches"></a>パスワード侵害

Microsoft は常にサイバー犯罪者の一歩先を行く対策を講じています。

Azure AD Identity Protection チームでは、よく使われるパスワードを絶えず分析しています。 サイバー犯罪者も、パスワード ハッシュをクラッキングするための "[レインボー テーブル](https://en.wikipedia.org/wiki/Rainbow_table)" を構築するなど、同様の手法を使って見破ったパスワードを攻撃に利用しています。

Microsoft は[データの侵害](https://www.privacyrights.org/data-breaches)を絶えず分析し、禁止パスワード リストを動的に更新することで、見破られやすいパスワードの使用を確実に禁止し、Azure AD の利用者に実際の脅威が及ぶのを未然に防いでいます。 セキュリティに関する最新の取り組みについて詳しくは、「[Microsoft Security Intelligence Report (Microsoft セキュリティ インテリジェンス レポート)](https://www.microsoft.com/security/sir/default.aspx)」を参照してください。

### <a name="smart-password-lockout"></a>Smart Password Lockout

ユーザーのパスワードがサイバー犯罪者によってハッキングされようとしている可能性があることを Azure AD が検出すると、Microsoft は、Smart Password Lockout を使ってそのユーザー アカウントをロックします。 Azure AD は、特定のログイン セッションについてのリスクを判別できるように設計されています。 サイバー脅威を止めるために、最新のセキュリティ データを利用してロックアウト セマンティクスを適用します。

ユーザーが Azure AD からロックアウトされた場合、次のような画面が表示されます。

  ![Azure AD からロックアウト](./media/active-directory-secure-passwords/locked-out-azuread.png)

別の Microsoft アカウントでは、次のような画面が表示されます。

  ![Microsoft アカウントからロックアウト](./media/active-directory-secure-passwords/locked-out-ms-accounts.png)

Azure Active Directory のパスワード リセットに関する詳細については、「[IT プロフェッショナルにとっての Azure AD のセルフサービスによるパスワードのリセット](active-directory-passwords.md)」というトピックを参照してください。

  >[!NOTE]
  >Azure AD の管理者の方は、従来のパスワードをユーザーが一切作成できないように、[Windows Hello](https://www.microsoft.com/windows/windows-hello) を導入することもご検討ください。
  >

## <a name="next-steps"></a>次のステップ

* [自分のパスワードを更新する方法](active-directory-passwords-update-your-own-password.md)
* [Azure ID 管理の基礎](fundamentals-identity.md)
* [パスワード リセット アクティビティに関するレポート](active-directory-passwords-reporting.md)

