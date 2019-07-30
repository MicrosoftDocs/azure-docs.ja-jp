---
title: Azure Active Directory へのパスワードなしのサインイン (プレビュー)
description: FIDO2 セキュリティ キーまたは Microsoft Authenticator アプリを使用する Azure AD へのパスワードなしのサインイン (プレビュー)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d80b359be0a6249327ba1ba1d51ffbc330bb073
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711968"
---
# <a name="what-is-passwordless"></a>パスワードなしとは

多要素認証 (MFA) は、組織をセキュリティで保護する優れた方法ですが、ユーザーはパスワードを覚えておく必要があるのに加え、層が増えることに不満を感じます。 パスワードが削除され、ユーザーが持っているものとユーザー自身または知っているものに置き換えられるため、パスワードなしの認証はより便利な方法です。

|   | ユーザーが持っているもの | ユーザー自身またはユーザーが知っているもの |
| --- | --- | --- |
| パスワードなし | 電話またはセキュリティ キー | 生体認証または PIN |

各組織には認証についてさまざまなニーズがあることを認識しています。 Microsoft では現在、Windows PC 用のプレミア パスワードなしエクスペリエンスである、Windows Hello を提供しています。 新しい資格情報をパスワードなしのファミリに追加しています:Microsoft Authenticator アプリと FIDO2 セキュリティ キー。

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator アプリ

従業員の電話をパスワードなしの認証方法として使用できるようにします。 パスワードに加え、便利な多要素認証オプションとして Microsoft Authenticator アプリを既に使用されているかもしれません。 しかし、ここでは、パスワードなしのオプションとして使用できます。

あらゆる iOS や Android フォンを、強力なパスワードなしの資格情報に変えます。その場合、ユーザーが、自分の電話で通知を受け取り、画面に表示される番号と電話の番号を照合してから、生体認証 (指紋または顔) あるいは PIN を使用して確認することで、プラットフォームまたはブラウザーにサインインできるようにします。

## <a name="fido2-security-keys"></a>FIDO2 セキュリティ キー

FIDO2 セキュリティ キーは、フォーム ファクターとして提供される可能性のある、フィッシングできない標準ベースのパスワードなし認証方法です。 Fast Identity Online (FIDO) は、パスワードなし認証のオープン標準です。 ユーザーおよび組織が標準を利用し、外部のセキュリティ キーまたはデバイスに組み込まれているプラットフォーム キーを使用して、ユーザー名やパスワードなしでリソースにサインインできるようにします。

パブリック プレビューでは、従業員は外部のセキュリティ キーを使用して、Azure Active Directory 参加済みの Windows 10 コンピューター (バージョン 1809 以降を実行している) にサインインし、クラウド リソースへのシングル サインオンを行うことができます。 サポートされているブラウザーにサインインすることもできます。

FIDO Alliance によって FIDO2 認定されたキーが多数存在する場合、Microsoft では、最大限のセキュリティと最適なエクスペリエンスを保証するために、ベンダーによって実装される FIDO2 CTAP 仕様の一部の省略可能な拡張機能が必要になります。

セキュリティ キーでは、Microsoft との互換性を維持するために、FIDO2 CTAP プロトコルの以下の機能と拡張機能を実装する**必要があります**。

| # | 機能 / 拡張機能 (信頼) | これが必要な理由 |
| --- | --- | --- |
| 1 | 常駐キー | 資格情報がセキュリティ キーに格納されている場合、この機能により、セキュリティ キーの移植が可能になります。 |
| 2 | クライアント PIN | この機能では、2 番目のファクターで資格情報を保護できます。これは、ユーザー インターフェイスがないセキュリティ キーに適用されます。 |
| 3 | HMAC シークレット | この拡張機能では、オフライン時または機内モード時に確実にデバイスにサインインできます。 |
| 4 | RP あたりの複数のアカウント | この機能を利用すれば、Microsoft アカウントや Azure Active Directory などの複数のサービスで確実に同じセキュリティ キーを使用できるようになります。 |

次のプロバイダーでは、パスワードなしのエクスペリエンスと互換性があると認識されている、さまざまなフォーム ファクターの FIDO2 セキュリティキーが提供されます。 Microsoft では、FIDO Alliance だけでなく、ベンダーにも連絡し、これらのキーのセキュリティ プロパティを評価することをお客様にお勧めしています。

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

1. 管理者は、テナントに対してパスワードなしの認証方法を有効にすることができます
1. 管理者は、すべてのユーザーをターゲットにすることも、方法ごとにテナント内のユーザー/グループを選ぶこともできます
1. エンド ユーザーは、アカウント ポータルでこれらのパスワードなしの認証方法を登録して管理することができます
1. エンド ユーザーは、これらのパスワードなしの認証方法でサインインすることができます
   1. Microsoft Authenticator アプリ:すべてのブラウザー間で、Windows 10 の Out Of Box (OOBE) セットアップ時に、また、オペレーティング システム上の統合されたモバイル アプリでなど、Azure AD 認証が使用されるすべてのシナリオで動作します。
   1. セキュリティ キー:Windows 10 バージョン 1809 以降のロック画面、および Microsoft Edge などのサポートされているブラウザーの Web で動作します。

## <a name="next-steps"></a>次の手順

[組織でパスワードなしのオプションを有効にする](howto-authentication-passwordless-enable.md)

### <a name="external-links"></a>外部リンク

[FIDO Alliance](https://fidoalliance.org/)

[FIDO2 CTAP の仕様](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
