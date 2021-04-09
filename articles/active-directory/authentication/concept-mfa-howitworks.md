---
title: Azure AD Multi-Factor Authentication の概要
description: Azure AD Multi-Factor Authentication を使用して、シンプルなサインイン プロセスを好むユーザーの要望に応えながら、データやアプリケーションへのアクセスを保護する方法について説明します。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1cc5227cb4b5de02cba65a60dd469da93d3767e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96744059"
---
# <a name="how-it-works-azure-ad-multi-factor-authentication"></a>動作のしくみ: Azure AD Multi-Factor Authentication

多要素認証は、携帯電話にコードを入力する、指紋スキャンを行うなど、サインイン プロセス中に追加で本人確認できるものをユーザーに求めるプロセスです。

ユーザーの認証にパスワードのみを使用する場合、不安な攻撃ベクトルが残ります。 パスワードの強度が低いか、どこかで流出していたとしたら、そのユーザー名とパスワードでサインインしようとしているユーザーは本人なのか、攻撃者なのかわかりません。 2 つ目の認証形式を義務付ければ、その二次的な要素は攻撃者が容易に取得したり複製したりできるようなものではないため、セキュリティが向上します。

![さまざまな形式の多要素認証の概念図](./media/concept-mfa-howitworks/methods.png)

Azure AD Multi-Factor Authentication は、次の認証方法のうち 2 つ以上を要求することで機能します。

* ユーザーが知っているもの (通常はパスワード)。
* ユーザーが持っているもの (携帯電話やハードウェア キーのように、簡単には複製できない信頼できるデバイスなど)。
* ユーザー自身 (指紋スキャンや顔面認識などの生体認証)。

オンボーディング エクスペリエンスを簡略化するために、セルフサービス パスワード リセットと Azure AD Multi-Factor Authentication の両方にユーザー自身が 1 回のステップで登録できるようになっています。 どのような形式のセカンダリ認証を使用できるかは、管理者が定義できます。 Azure AD Multi-Factor Authentication は、ユーザーがセルフサービス パスワード リセットを実行する場合に、さらにそのプロセスのセキュリティを高めるために義務付けることもできます。

![サインイン画面で使用されている認証方法](media/concept-authentication-methods/overview-login.png)

Azure AD Multi-Factor Authentication を利用すれば、データやアプリケーションへのアクセスを保護すると同時に、ユーザーの利便性を維持できます。 2 つ目の認証を要求することでセキュリティを追加し、使い勝手の良いさまざまな[認証方法](concept-authentication-methods.md)によって強力な認証を与えます。 ユーザーは、管理者が行う構成上の決定に基づいて、MFA で認証が行われる場合と行われない場合があります。

Azure AD Multi-Factor Authentication を使用するために、アプリケーションまたはサービスを変更する必要はありません。 検証プロンプトは Azure AD サインイン イベントの一部であり、必要に応じて MFA チャレンジを自動的に要求して処理します。

## <a name="available-verification-methods"></a>使用可能な検証方法

ユーザーがアプリケーションまたはサービスにサインインすると、MFA プロンプトが表示され、登録されているいずれかの追加の検証形式を選択できます。 管理者は、これらの Azure AD Multi-Factor Authentication の検証方法の登録を要求することができます。または、ユーザーが自分の [[マイ プロファイル]](https://myprofile.microsoft.com) にアクセスして検証方法を編集または追加できます。

Azure AD Multi-Factor Authentication では、次のような追加の検証形式を使用できます。

* Microsoft Authenticator アプリ
* OATH ハードウェア トークン
* SMS
* 音声通話

## <a name="how-to-enable-and-use-azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication を有効にして使用する方法

サインイン イベント時に追加の検証を要求するように、Azure AD Multi-Factor Authentication に対してユーザーとグループを有効にすることができます。 すべてのユーザーが Microsoft Authenticator アプリをすぐに使用できるように、すべての Azure AD テナントで[セキュリティの既定値群](../fundamentals/concept-fundamentals-security-defaults.md)を使用できます。

より詳細な制御を行うために、[条件付きアクセス](../conditional-access/overview.md) ポリシーを使用して、MFA を必要とするイベントやアプリケーションを定義できます。 これらのポリシーにより、ユーザーが企業ネットワークまたは登録済みデバイスを使用中の場合は通常のサインイン イベントを許可しますが、リモートまたは個人用デバイスを使用中の場合は追加の検証要素を求めることができます。

![条件付きアクセスによってサインイン プロセスにセキュリティを確保するしくみを示す概要図](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

## <a name="next-steps"></a>次のステップ

ライセンスの詳細については、「[Azure AD Multi-Factor Authentication の機能とライセンス](concept-mfa-licensing.md)」を参照してください。

MFA の動作を確認するには、次のチュートリアルで一連のテスト ユーザーに対して Azure AD Multi-Factor Authentication を有効にします。

> [!div class="nextstepaction"]
> [Azure AD の Multi-Factor Authentication を有効にする](./tutorial-enable-azure-mfa.md)