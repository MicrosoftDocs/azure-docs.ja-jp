---
title: 多要素認証 (MFA) と PIM - Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) が多要素認証 (MFA) を検証する方法について説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 08/31/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 686c39c9fb1f1ff7c0ecf068a6612f530620d5dc
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804304"
---
# <a name="multi-factor-authentication-mfa-and-pim"></a>多要素認証 (MFA) と PIM

すべての管理者に多要素認証 (MFA) を必須にすることをお勧めします。 これにより、パスワードの漏洩による攻撃のリスクが軽減されます。

MFA チャレンジは、ユーザーがサインインしたときに完了するよう要求できます。 ユーザーが Azure Active Directory (Azure AD) Privileged Identity Management (PIM) でロールをアクティブ化するときに MFA チャレンジを完了することを必須にすることもできます。 この場合は、サインイン時に MFA チャレンジを完了していないユーザーには、完了するよう求めるメッセージが PIM に表示されます。

> [!IMPORTANT]
> 現時点では、Azure MFA は職場または学校のアカウントでのみ機能します。Microsoft アカウント (通常は、Skype、Xbox、Outlook.com などの Microsoft サービスにサインインするために使用される個人アカウント) では機能しません。 このため、Microsoft アカウントを使用するユーザーは対象管理者にすることはできません。ロールをアクティブ化するために MFA を使用することができないからです。 Microsoft アカウントを使用してワークロードの管理を続行する必要がある場合は、ここで永続的な管理者に昇格させてください。

## <a name="how-pim-validates-mfa"></a>PIM が MFA を検証する方法

ユーザーがロールをアクティブ化するときに MFA を検証するためのオプションは 2 つあります。

最も簡単な方法は、特権ロールをアクティブ化するユーザーに対して Azure MFA を使用することです。 これを行うには、先に、そのユーザーにライセンスが付与されていることと (必要な場合)、Azure MFA に登録されていることを確認する必要があります。 Azure MFA のデプロイの詳細については、「[クラウドベースの Azure Multi-Factor Authentication をデプロイする](../authentication/howto-mfa-getstarted.md)」を参照してください。 ユーザーのサインイン時に MFA を適用するように Azure AD を構成することは、必須ではありませんが、お勧めします。 MFA は PIM 自体で確認されるためです。

また、ユーザーをオンプレミスで認証する場合は、ID プロバイダーで MFA を行うようにすることができます。 たとえば、Azure AD にアクセスする前にスマートカード ベースの認証を要求するように AD フェデレーション サービスを構成した場合は、「 [Azure Multi-Factor Authentication および AD FS を使用したクラウド リソースのセキュリティ保護](../authentication/howto-mfa-adfs.md) 」の手順に従って、Azure AD に要求を送信するように AD FS を構成します。 ユーザーがロールをアクティブ化しようとしたとき、PIM は、適切な要求を受信した後で、ユーザーに対して MFA が検証済みであることを受け入れます。

## <a name="next-steps"></a>次の手順

- [PIM で Azure AD ロールの設定を構成する](pim-how-to-change-default-settings.md)
- [PIM で Azure リソース ロールの設定を構成する](pim-resource-roles-configure-role-settings.md)
