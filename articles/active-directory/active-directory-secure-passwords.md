---
title: "Azure AD におけるパスワードの保護と Smart Password Lockout によってロックされたパスワードのリセット | Microsoft Docs"
description: "Azure AD テナントの概要、および Azure Active Directory で Azure を管理する方法を説明します。"
services: active-directory
documentationcenter: 
author: markvi
writer: v-lorisc
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/02/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 8e625a346c9495d436a99fcf9eadf8ffeffcfdff
ms.lasthandoff: 03/28/2017


---
# <a name="secure-passwords--in-azure-ad-and-reset-passwords-that-get-blocked-by-smart-password-lockout"></a>Azure AD におけるパスワードの保護と Smart Password Lockout によってロックされたパスワードのリセット
この記事では、Azure Active Directory (Azure AD) と Microsoft アカウント サービスのアカウントを保護するために皆さんがユーザーとして、または管理者として実践できるベスト プラクティスについて説明します。 

 >[!NOTE]
 >Azure AD の管理者は、該当するディレクトリ名をクリックすることでユーザーのパスワードをリセットすることができます。 [Azure 管理ポータル](https://manage.windowsazure.com)から [ユーザー] ページを選択し、ユーザーの名前をクリックして、[パスワードのリセット] をクリックします。 
 >

パスワードのセキュリティを確保するための一般的な手段として、Azure AD には次のアプローチが導入されています。
 *    パスワードの長さの要件
 *    パスワードの "複雑さ" の要件
 *    パスワードの有効期限 (定期的に変更を強制) 

パスワード管理機能については、「[Azure Active Directory のパスワードを管理する](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-manage-passwords)」を参照してください。 

## <a name="azure-ad-password-protection"></a>Azure AD のパスワード保護
ユーザーと管理者のパスワードを確実に保護するために、Azure AD と Microsoft アカウント システムでは業界で広く認められている手法が使われています。 

このセクションでは、次の方法を使った Azure AD のパスワード保護について説明します。
 *    パスワードの動的禁止
 *    Smart Password Lockout

最新の研究に基づくパスワード管理については、ホワイトペーパー「[Password Guidance (パスワード ガイダンス)](http://aka.ms/passwordguidance)」を参照してください。 

### <a name="dynamically-banned-passwords"></a>パスワードの動的禁止
Azure AD と Microsoft アカウント システムでは、パスワードを確実に保護するために、よく使われているパスワードが動的に禁止されています。 Azure ID Identity Protection チームは、禁止パスワード リストを定期的に分析し、ありきたりのパスワードをユーザーが選択できないようにしています。 このサービスは、Azure AD と Microsoft アカウント サービスのユーザーが利用できます。 

管理者は、パスワードを作成するユーザーに、アルファベットや数字、文字を不規則に組み合わせた、ありきたりでないパスワード フレーズを奨励することが重要です。 そうすることでユーザーのパスワードが侵害される可能性を限りなくゼロに近づけることができます。 

**侵害リスト**

Azure AD は常にサイバー犯罪者の一歩先を行く対策を講じています。 そうした対策の 1 つとして、最新の攻撃リストに登録されているパスワードをユーザーが作成できないようになっています。

Azure AD Identity Protection チームでは、よく使われるパスワードを絶えず分析しています。 サイバー犯罪者も、パスワード ハッシュをクラッキングするための "[レインボー テーブル](https://en.wikipedia.org/wiki/Rainbow_table)" を構築するなど、同様の手法を使って見破ったパスワードを攻撃に利用しています。 

Microsoft は[データの侵害](https://www.privacyrights.org/data-breaches)を絶えず分析し、禁止パスワード リストを動的に更新することで、見破られやすいパスワードの使用を確実に禁止し、Azure AD の利用者に実際の脅威が及ぶのを未然に防いでいます。 セキュリティに関する最新の取り組みについて詳しくは、「[Microsoft Security Intelligence Report (Microsoft セキュリティ インテリジェンス レポート)](https://www.microsoft.com/security/sir/default.aspx)」を参照してください。 

### <a name="smart-password-lockout"></a>Smart Password Lockout

ユーザーのパスワードがサイバー犯罪者によってハッキングされようとしている可能性があることを Azure AD が検出すると、Microsoft は、Smart Password Lockout を使ってそのユーザー アカウントをロックします。 Azure AD は、特定のログイン セッションについてのリスクを判別できるように設計されています。 

ロックアウトのセマンティクスは、最新のセキュリティ データを使ってサイバー脅威に対して適用されます。 そのためネットワークに対するユーザー パスワードがサイバー犯罪者によってハッキングされた場合でも、ユーザーがロックアウトされることはありません。

ユーザーが Azure AD からロックアウトされた場合、次のような画面が表示されます。

  ![Azure AD からロックアウト](./media/active-directory-secure-passwords/locked-out-azuread.png)
  
さらに別の Microsoft アカウントでは、次のような画面が表示されます。

  ![Microsoft アカウントからロックアウト](./media/active-directory-secure-passwords/locked-out-ms-accounts.png)

Azure Active Directory のパスワード管理については、[パスワード管理のしくみ](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-how-it-works)に関するページを参照してください。

  >![NOTE] Azure AD の管理者の方は、従来のパスワードをユーザーが一切作成できないように、[Windows Hello](https://www.microsoft.com/en-us/windows/windows-hello) を導入することもご検討ください。
  >

## <a name="next-steps"></a>次のステップ
[自分のパスワードを更新する方法](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-update-your-own-password)<br>
[Azure ID 管理の基礎](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals-identity)<br>
[パスワード管理レポートで運用情報を把握する方法](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-get-insights#view-password-reset-activity)



