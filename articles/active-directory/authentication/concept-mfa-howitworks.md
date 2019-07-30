---
title: Azure Multi-Factor Authentication - そのしくみ - Azure Active Directory
description: Azure Multi-Factor Authentication を使えば、シンプルなサインイン プロセスを好むユーザーのニーズに応えながら、データやアプリケーションへのアクセスを効果的に保護することが可能です。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff8f27d727768f3f1135f8929310d5cfeffe874b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67113536"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>動作のしくみ: Azure Multi-Factor Authentication

2 段階認証のセキュリティは、レイヤー アプローチによってもたらされます。 攻撃者にとって、多要素認証を侵害することはかなり困難です。 攻撃者がユーザーのパスワードを手に入れても、付加的な認証方法がなければ役に立ちません。 多要素認証は次の認証方法のうち 2 つ以上を要求することで機能します。

* ユーザーが知っているもの (通常はパスワード)
* ユーザーが持っているもの (携帯電話など、簡単には複製できない信頼できるデバイス)
* ユーザー自身 (生体認証)

<center>

![認証方法の概念画像](./media/concept-mfa-howitworks/methods.png)</center>

Azure Multi-Factor Authentication (MFA) を利用すれば、データやアプリケーションへのアクセスを保護し、ユーザーには簡単なサインイン プロセスを提供できます。 2 つ目の認証を要求することでセキュリティを追加し、使い勝手の良いさまざまな[認証方法](concept-authentication-methods.md)によって強力な認証を与えます。 ユーザーは、管理者が行う構成上の決定に基づいて、MFA で認証が行われる場合と行われない場合があります。

## <a name="how-to-get-multi-factor-authentication"></a>Multi-Factor Authentication の入手方法

Multi-Factor Authentication は以下のサービスに付属します。

* **Azure Active Directory Premium** または **Microsoft 365 Business** - Azure Multi-Factor Authentication のすべての機能と条件付きアクセス ポリシーを使用して、多要素認証を要求します。

* **Azure AD Free**、**Azure AD Basic**、またはスタンドアロン **Office 365** ライセンス - 作成済みの[条件付きアクセス ベースライン保護ポリシー](../conditional-access/concept-baseline-protection.md)を使用して、ユーザーと管理者に多要素認証を要求します。

* **Azure Active Directory 全体管理者** - Azure Multi-Factor Authentication の一部の機能を集めたものであり、全体管理者アカウントを保護するための手段として利用されます。

> [!NOTE]
> 2018 年 9 月 1 日以降、新しいお客様は、スタンドアロン オファーとして Azure Multi-Factor Authentication を購入できなくなります。 多要素認証認証は、今後も Azure AD Premium ライセンスで利用できます。

## <a name="supportability"></a>サポート

ユーザーの多くはパスワードのみを使用した認証に慣れているため、組織はこのプロセスに関してすべてのユーザーに通達することが重要です。 通達により、ユーザーが MFA 関連の重要でない問題についてヘルプ デスクに問い合わせる可能性が低くなります。 ただし、一部のシナリオでは MFA を一時的に無効にすることが必要になる場合があります。 次のガイドラインに従って、これらのシナリオへの対処方法を理解してください。

* 認証方法にアクセスできないか、認証方法が正しく動作せず、ユーザーがサインインできない状況に対応できるよう、サポート スタッフをトレーニングしてください。
   * サポート スタッフは Azure MFA Service の条件付きアクセス ポリシーを利用することで、MFA を要求するポリシーから除外されるグループにユーザーを追加できます。
* 2 段階認証の要求を最小限に抑える方法として、条件付きアクセスで指定された場所を使用することを検討します。 この機能を使用して、管理者は、新しいユーザーのオンボードで使用されるセキュリティで保護された信頼できるネットワークの場所 (ネットワーク セグメントなど) からサインインしているユーザーの 2 段階認証をバイパスすることができます。
* [Azure AD Identity Protection](../active-directory-identityprotection.md) をデプロイし、リスク イベントに基づいて 2 段階検証をトリガーします。

## <a name="next-steps"></a>次の手順

- [Azure Multi-Factor Authentication のデプロイ手順](howto-mfa-getstarted.md)
