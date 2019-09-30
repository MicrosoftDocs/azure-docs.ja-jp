---
title: Azure Active Directory へのパスワードレスのサインイン (プレビュー)
description: FIDO2 セキュリティ キーまたは Microsoft Authenticator アプリを使用する Azure AD へのパスワードレスのサインイン (プレビュー)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc633780d8b816d8fc2e313bb1955a5719979efe
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240867"
---
# <a name="what-is-passwordless"></a>パスワードレスとは

多要素認証 (MFA) は、組織をセキュリティで保護する優れた方法ですが、ユーザーはパスワードを覚えておく必要があるのに加え、層が増えることに不満を感じます。 パスワードが削除され、ユーザーが持っているものとユーザー自身または知っているものに置き換えられるため、パスワードレスの認証はより便利な方法です。

|   | ユーザーが持っているもの | ユーザー自身またはユーザーが知っているもの |
| --- | --- | --- |
| パスワードレス | 電話またはセキュリティ キー | 生体認証または PIN |

認証については、組織ごとにさまざまなニーズがあります。 現在、Microsoft では Windows PC 用に Windows Hello が提供されています。 Microsoft Authenticator アプリと FIDO2 のセキュリティ キーをパスワードレスファミリに追加しています。

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator アプリ

従業員の電話をパスワードレスの認証方法として使用できるようにします。 パスワードに加え、便利な多要素認証オプションとして Microsoft Authenticator アプリを既に使用されているかもしれません。 しかし、ここでは、パスワードレスのオプションとして使用できます。

![Microsoft Authenticator アプリを使用して Microsoft Edge にサインインする](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

あらゆる iOS や Android フォンを、強力なパスワードレスの資格情報に変えます。その場合、ユーザーが、自分の電話で通知を受け取り、画面に表示される番号と電話の番号を照合してから、生体認証 (指紋または顔) あるいは PIN を使用して確認することで、プラットフォームまたはブラウザーにサインインできるようにします。

## <a name="fido2-security-keys"></a>FIDO2 セキュリティ キー

FIDO2 セキュリティ キーは、フォーム ファクターとして提供される可能性のある、フィッシングできない標準ベースのパスワードレス認証方法です。 Fast Identity Online (FIDO) は、パスワードレス認証のオープン標準です。 ユーザーおよび組織が標準を利用し、外部のセキュリティ キーまたはデバイスに組み込まれているプラットフォーム キーを使用して、ユーザー名やパスワードレスでリソースにサインインできるようにします。

パブリック プレビューでは、従業員は外部のセキュリティ キーを使用して、Azure Active Directory 参加済みの Windows 10 コンピューター (バージョン 1809 以降を実行している) にサインインし、クラウド リソースへのシングル サインオンを行うことができます。 サポートされているブラウザーにサインインすることもできます。

![セキュリティ キーを使用して Microsoft Edge にサインインする](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

FIDO Alliance によって FIDO2 認定されたキーが多数存在する場合、Microsoft では、最大限のセキュリティと最適なエクスペリエンスを保証するために、ベンダーによって実装される FIDO2 CTAP 仕様の一部の省略可能な拡張機能が必要になります。

セキュリティ キーでは、Microsoft との互換性を維持するために、FIDO2 CTAP プロトコルの以下の機能と拡張機能を実装する**必要があります**。

| # | 機能 / 拡張機能 (信頼) | この機能または拡張機能が必要な理由 |
| --- | --- | --- |
| 1 | Resident Key | この機能により、セキュリティ キーが可搬となり、セキュリティキー内に資格情報が保存されます。 |
| 2 | Client pin | この機能では、2 番目の認証要素で資格情報を保護できます。これは、ユーザー インターフェイスがないセキュリティ キーに適用されます。 |
| 3 | hmac-secret | この拡張機能では、オフライン時または機内モード時に確実にデバイスにサインインできます。 |
| 4 | RP あたりの複数のアカウント | この機能を利用すれば、Microsoft アカウントや Azure Active Directory などの複数のサービスで同じセキュリティ キーを使用できるようになります。 |

次のプロバイダーでは、パスワードレスのエクスペリエンスと互換性があると認識されている、さまざまなフォーム ファクターの FIDO2 セキュリティキーが提供されます。 Microsoft では、FIDO Alliance だけでなく、ベンダーにも連絡し、これらのキーのセキュリティ プロパティを評価することをお客様にお勧めしています。

| プロバイダー | Contact |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://ensurity.com/contact-us.html](https://ensurity.com/contact-us.html) |
| eWBM | [https://www.ewbm.com/page/sub1_5](https://www.ewbm.com/page/sub1_5) |

ベンダーとしてこのリストにデバイスを載せたい場合は、[Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com) にご連絡ください。

FIDO2 セキュリティ キーは、セキュリティに非常に敏感であるか、2 番目のファクターとしての電話の使用を望まない、あるいは使用できないシナリオまたは従業員が存在する企業向けの優れたオプションです。

## <a name="what-scenarios-work-with-the-preview"></a>プレビューで動作するシナリオ

- 管理者は、テナントに対してパスワードレスの認証方法を有効にすることができます
- 管理者は、すべてのユーザーをターゲットにすることも、方法ごとにテナント内のユーザー/グループを選ぶこともできます
- エンド ユーザーは、アカウント ポータルでこれらのパスワードレスの認証方法を登録して管理することができます
- エンド ユーザーは、これらのパスワードレスの認証方法でサインインすることができます
   - Microsoft Authenticator アプリ:すべてのブラウザー間で、Windows 10 の Out Of Box (OOBE) セットアップ時に、また、オペレーティング システム上の統合されたモバイル アプリでなど、Azure AD 認証が使用されるシナリオで動作します。
   - セキュリティ キー:Windows 10 バージョン 1809 以降のロック画面、および Microsoft Edge などのサポートされているブラウザーの Web で動作します。

## <a name="next-steps"></a>次の手順

[組織内で FIDO2 セキュリティ キーのパスワードレスオプションを有効にする](howto-authentication-passwordless-security-key.md)

[組織内で電話ベースのパスワードレスオプションを有効にする](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>外部リンク

[FIDO Alliance](https://fidoalliance.org/)

[FIDO2 CTAP の仕様](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
