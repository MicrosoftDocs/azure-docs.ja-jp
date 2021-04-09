---
title: MFA または 2FA および Privileged Identity Management (PIM) - Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) が多要素認証 (MFA) を検証する方法について説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4344e769cc8466287dab1e98e95cc3fbe705ffbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94835001"
---
# <a name="multi-factor-authentication-and-privileged-identity-management"></a>多要素認証と Privileged Identity Management

すべての管理者に多要素認証 (MFA) を必須にすることをお勧めします。 これにより、パスワードの漏洩による攻撃のリスクが軽減されます。

多要素認証チャレンジは、ユーザーがサインインしたときに完了するよう要求できます。 ユーザーが Azure Active Directory (Azure AD) Privileged Identity Management (PIM) でロールをアクティブ化するときに多要素認証チャレンジを完了することを必須にすることもできます。 この場合は、サインイン時に多要素認証チャレンジを完了していないユーザーには、完了するよう求めるメッセージが Privileged Identity Management に表示されます。

> [!IMPORTANT]
> 現時点では、Azure AD Multi-Factor Authentication は職場または学校のアカウントでのみ機能します。Microsoft アカウント (通常は、Skype、Xbox、Outlook.com などの Microsoft サービスにサインインするために使用される個人アカウント) では機能しません。 このため、個人用アカウントを使用するユーザーは対象管理者にすることはできません。ロールをアクティブ化するために多要素認証を使用することができないからです。 Microsoft アカウントを使用してワークロードの管理を続行する必要がある場合は、ここで永続的な管理者に昇格させてください。

## <a name="how-pim-validates-mfa"></a>PIM が MFA を検証する方法

ユーザーがロールをアクティブ化するときに多要素認証を検証するためのオプションは 2 つあります。

最も簡単な方法は、特権ロールをアクティブ化するユーザーに対して Azure AD Multi-Factor Authentication を使用することです。 これを行うには、先に、そのユーザーにライセンスが付与されていることと (必要な場合)、Azure AD Multi-Factor Authentication に登録されていることを確認する必要があります。 Azure AD Multi-Factor Authentication のデプロイの詳細については、「[クラウドベースの Azure AD Multi-Factor Authentication をデプロイする](../authentication/howto-mfa-getstarted.md)」を参照してください。 ユーザーのサインイン時に多要素認証を適用するように Azure AD を構成することは、必須ではありませんが、お勧めします。 これは、多要素認証チェックが Privileged Identity Management 自体によって行われるためです。

また、ユーザーをオンプレミスで認証する場合は、ID プロバイダーで多要素認証を行うようにすることができます。 たとえば、Azure AD にアクセスする前にスマートカード ベースの認証を要求するように AD フェデレーション サービスを構成した場合は、「[Azure AD Multi-Factor Authentication および AD FS を使用したクラウド リソースのセキュリティ保護](../authentication/howto-mfa-adfs.md)」の手順に従って、Azure AD に要求を送信するように AD FS を構成します。 ユーザーがロールをアクティブ化しようとしたとき、Privileged Identity Management は、適切な要求を受信した後で、ユーザーに対して多要素認証が検証済みであることを受け入れます。

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management で Azure AD ロールの設定を構成する](pim-how-to-change-default-settings.md)
- [Privileged Identity Management で Azure リソース ロールの設定を構成する](pim-resource-roles-configure-role-settings.md)
