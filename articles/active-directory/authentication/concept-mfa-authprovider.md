---
title: Azure Multi-Factor Auth プロバイダーをいつどのように使用するか - Azure Active Directory
description: どのようなときに Azure MFA 認証プロバイダーを使用するかについて説明します。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b1461999679935587370f66349a440d588465cd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052540"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>Azure Multi-Factor Authentication プロバイダーをいつ使用するか

Azure Active Directory を持つグローバル管理者と Office 365 ユーザーは、既定で 2 段階認証を使用できます。 ただし、[高度な機能](howto-mfa-mfasettings.md)が必要な場合は、通常版の Multi-Factor Authentication (MFA) をご購入ください。

Azure Multi-Factor Authentication プロバイダーは、Azure Multi-Factor Authentication が提供する機能を、**ライセンスを持っていない**ユーザーが利用できるようにするために使用されます。

> [!NOTE]
> 2018 年 9 月 1 日以降、新しい認証プロバイダーを作成できなくなります。 既存の認証プロバイダーは引き続き使用および更新できますが、移行はもうできません。 多要素認証は、今後も Azure AD Premium ライセンスの一機能としてご利用いただけます。

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>Azure MFA SDK に関する注意事項

SDK は廃止となり、2018 年 11 月 14 日までのみの作動となることにご注意ください。 その後は、SDK への呼び出しは失敗します。

## <a name="what-is-an-mfa-provider"></a>MFA プロバイダーとは

2 種類の認証プロバイダーがあり、違いは Azure サブスクリプションの課金方法です。 認証ごとのオプションは、1 か月間にテナントに対して実行された認証の数を計算します。 このオプションは、ときどき認証を行うユーザーが多数いる場合に最適です。 ユーザーごとのオプションは、1 か月間に 2 段階認証を実行するテナント内の個人の数を計算します。 このオプションは、ライセンスを持つユーザーはいますが、ライセンス制限を超えてより多くのユーザーに MFA を拡張する必要がある場合に最適です。

## <a name="manage-your-mfa-provider"></a>MFA プロバイダーの管理

MFA プロバイダーの作成後に使用モデル (有効化されたユーザーごと、または認証ごと) を変更することはできません。

MFA が有効化されているすべてのユーザーに対応できる、十分な数のライセンスを購入している場合は、MFA プロバイダーをすべて削除することもできます。

MFA プロバイダーが Azure AD テナントにリンクされていない場合、または新しい MFA プロバイダーを別の Azure AD テナントにリンクする場合、ユーザー設定と構成オプションは転送されません。 また、既存の Azure MFA サーバーは、MFA プロバイダーによって生成されるアクティブ化資格情報を使用して再アクティブ化する必要があります。 MFA Server を MFA プロバイダーにリンクするために再アクティブ化しても、電話呼び出しやテキスト メッセージによる認証には影響ありませんが、モバイル アプリ通知は、各ユーザーがモバイル アプリを再アクティブ化するまで機能しなくなります。

## <a name="next-steps"></a>次の手順

[Multi-Factor Authentication の設定を構成する](howto-mfa-mfasettings.md)
