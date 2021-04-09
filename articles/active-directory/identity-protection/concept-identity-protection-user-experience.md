---
title: Azure AD Identity Protection を使用したユーザー エクスペリエンス
description: Azure AD Identity Protection のユーザー エクスペリエンス
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d4897b9187caab50be4db75bbc0af03e3d35aa4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94835987"
---
# <a name="user-experiences-with-azure-ad-identity-protection"></a>Azure AD Identity Protection を使用したユーザー エクスペリエンス

Azure Active Directory Identity Protection を使用すると、次の操作を行うことができます。

* ユーザーに対して Azure AD Multi-Factor Authentication (MFA) への登録を必須にする
* 危険なサインインと侵害されたユーザーを自動的に修復する

すべての Identity Protection 保護ポリシーは、ユーザーのサインイン エクスペリエンスに影響を与えます。 ユーザーが Azure AD MFA やセルフサービス パスワード リセットなどのツールに登録して使用できるようにすると、影響が軽減される可能性があります。 これらのツールと適切なポリシーを選択することで、ユーザーが必要に応じて自己修復オプションを使用できるようになります。

## <a name="multi-factor-authentication-registration"></a>多要素認証の登録

多要素認証の登録を必要とする Identity Protection 保護ポリシーを有効にし、すべてのユーザーを対象にすると、ユーザーは、Azure AD MFA を使用して今後自己修復を行うことができるようになります。 このポリシーを構成すると、登録することを選択できる 14 日の期間がユーザーに与えられ、この期間の最後に強制的に登録されます。 ユーザーのエクスペリエンスについては、以下でその概要を説明します。 詳細については、エンドユーザー ドキュメントの記事「[2 要素認証と職場または学校アカウントの概要](../user-help/multi-factor-authentication-end-user-first-time.md)」を参照してください。

### <a name="registration-interrupt"></a>登録の割り込み

1. Azure AD 統合アプリケーションにサインインするときに、ユーザーはアカウントを多要素認証に設定することを求める通知を受け取ります。 このポリシーは、新しいデバイスを使用する新しいユーザーを対象とする、Windows 10 のすぐに使えるエクスペリエンスでもトリガーされます。
   
    ![詳細情報が必要](./media/concept-identity-protection-user-experience/identity-protection-experience-more-info-mfa.png)

1. ガイド付きの手順を完了して、Azure AD Multi-Factor Authentication に登録し、サインインを完了します。

## <a name="risky-sign-in-remediation"></a>危険なサインインの修復

管理者がサインイン リスクのポリシーを構成してある場合、影響を受けるユーザーがサインインを試みると、ポリシーのリスク レベルがトリガーされます。 

### <a name="risky-sign-in-self-remediation"></a>危険なサインインの自己修復

1. ユーザーは、新しい場所、デバイス、アプリからのサインインなど、サインインに関して異常が検出されたことの通知を受け取ります。
   
    ![何かが普通でないことを示しているプロンプト](./media/concept-identity-protection-user-experience/120.png)

1. ユーザーは、前に登録した方法のいずれかを使用して Azure AD MFA を完了することで、本人であることを証明する必要があります。 

### <a name="risky-sign-in-administrator-unblock"></a>危険なサインインの管理者によるブロック解除

管理者は、ユーザーのリスク レベルに応じて、サインイン時にユーザーをブロックすることを選択できます。 ブロックを解除するには、エンド ユーザーは、IT スタッフに連絡する必要があります。または、よく使用する場所またはデバイスからサインインを試みることができます。 この場合、多要素認証の実行による自己修復は利用できません。

![サインイン リスク ポリシーによるブロック](./media/concept-identity-protection-user-experience/200.png)

IT スタッフは、「[ユーザーのブロック解除](howto-identity-protection-remediate-unblock.md#unblocking-based-on-sign-in-risk)」セクションの手順に従って、ユーザーによるサインインが再び許可されるようにすることができます。

## <a name="risky-user-remediation"></a>危険なユーザーの修復

ユーザー リスク ポリシーが構成されている場合、ユーザー リスク レベルの侵害の確率を満たしているユーザーは、サインインする前にユーザー侵害回復フローを通過する必要があります。 

### <a name="risky-user-self-remediation"></a>危険なユーザーの自己修復

1. 不審なアクティビティまたは漏洩した資格情報のためにアカウントのセキュリティにリスクがあることが、ユーザーに通知されます。
   
    ![Remediation](./media/concept-identity-protection-user-experience/101.png)

1. ユーザーは、前に登録した方法のいずれかを使用して Azure AD MFA を完了することで、本人であることを証明する必要があります。 
1. 最後に、誰かがアカウントにアクセスした可能性があるため、ユーザーは、セルフサービス パスワード リセット を使用して、パスワードを変更することを強制されます。

## <a name="risky-sign-in-administrator-unblock"></a>危険なサインインの管理者によるブロック解除

管理者は、ユーザーのリスク レベルに応じて、サインイン時にユーザーをブロックすることを選択できます。 ブロック解除するには、エンドユーザーは IT スタッフに連絡する必要があります。 この場合、多要素認証の実行による自己修復とセルフサービス パスワード リセット は利用できません。

![ユーザー リスク ポリシーによるブロック](./media/concept-identity-protection-user-experience/104.png)

IT スタッフは、「[ユーザーのブロック解除](howto-identity-protection-remediate-unblock.md#unblocking-based-on-user-risk)」セクションの手順に従って、ユーザーによるサインインが再び許可されるようにすることができます。

## <a name="see-also"></a>参照

- [リスクを修復してユーザーをブロック解除する](howto-identity-protection-remediate-unblock.md)

- [Azure Active Directory Identity Protection](./overview-identity-protection.md)