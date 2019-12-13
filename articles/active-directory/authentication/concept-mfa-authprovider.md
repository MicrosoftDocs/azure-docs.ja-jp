---
title: Azure Multi-Factor 認証プロバイダー - Azure Active Directory
description: どのようなときに Azure MFA 認証プロバイダーを使用するかについて説明します。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57ac23bbb0465be2f15e0a35bb4fd7c331e83988
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848716"
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

### <a name="removing-an-authentication-provider"></a>認証プロバイダーの削除

> [!CAUTION]
> 認証プロバイダーを削除するときに確認は行われません。 **[削除]** の選択は永続的な処理です。

認証プロバイダーは、**Azure portal** >  **[Azure Active Directory]**  >  **[MFA]**  >  **[プロバイダー]** で見つけられます。 一覧表示されたプロバイダーをクリックすると、そのプロバイダーに関連付けられている詳細と構成が表示されます。

認証プロバイダーを削除する前に、プロバイダーで構成されているカスタマイズされた設定を記録しておいてください。 ご利用のプロバイダーから一般的な MFA の設定に移行する必要がある設定を決定し、それらの設定の移行を完了します。 

プロバイダーにリンクされている Azure MFA サーバーを、**Azure portal** >  **[Azure Active Directory]**  >  **[MFA]**  >  **[サーバーの設定]** で生成される資格情報を使って、再アクティブ化する必要があります。 再アクティブ化する前に、環境内の Azure MFA サーバーの `\Program Files\Multi-Factor Authentication Server\Data\` ディレクトリから次のファイルを削除する必要があります。

- caCert
- cert
- groupCACert
- groupKey
- groupName
- licenseKey
- pkey

![Azure portal から認証プロバイダーを削除する](./media/concept-mfa-authprovider/authentication-provider-removal.png)

すべての設定が移行されたことを確認した後、**Azure portal** >  **[Azure Active Directory]**  >  **[MFA]**  >  **[プロバイダー]** を参照し、省略記号 **[...]** を選択して、 **[削除]** を選択できます。

> [!WARNING]
> 認証プロバイダーを削除すると、そのプロバイダーに関連付けられているすべてのレポート情報が削除されます。 プロバイダーを削除する前に、アクティビティ レポートを保存することができます。

> [!NOTE]
> 古いバージョンの Microsoft Authenticator アプリと Azure MFA Server を使用しているユーザーは、アプリの再登録が必要な場合があります。

## <a name="next-steps"></a>次の手順

[Multi-Factor Authentication の設定を構成する](howto-mfa-mfasettings.md)
