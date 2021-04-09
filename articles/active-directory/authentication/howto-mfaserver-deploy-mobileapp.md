---
title: Azure MFA Server モバイル アプリ Web サービス | Azure Active Directory
description: Microsoft Authenticator アプリを使用してプッシュ通知がユーザーに送信されるように MFA サーバーを構成します。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6263491ce5b319c3968c542ebbaf00294c5152cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96742427"
---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>Azure Multi-Factor Authentication Server でモバイル アプリ認証を有効にする

Microsoft Authenticator アプリには、アウトオブバンド認証オプションも用意されています。 Azure Multi-Factor Authentication では、ログイン時にユーザーに自動音声通話または SMS 送信を行うのではなく、ユーザーのスマートフォンまたはタブレットの Microsoft Authenticator アプリにプッシュ通知を行います。 ユーザーはアプリで **[認証]** をタップ (または PIN を入力し、[認証] をタップ) するだけでサインインを完了することができます。

電波の受信の信頼性に欠けるときには、2 段階認証にモバイル アプリを使用することをお勧めします。 アプリを OATH トークン ジェネレーターとして使用する場合、ネットワーク接続やインターネット接続は不要です。

> [!IMPORTANT]
> 2019 年 7 月 1 日より、Microsoft では新しいデプロイに対して MFA Server が提供されなくなりました。 サインイン イベント時に多要素認証が必要な新しいお客様は、クラウドベースの Azure AD Multi-Factor Authentication (MFA) を使用していただく必要があります。
>
> クラウドベースの MFA の使用を開始するには、「[チュートリアル: Azure AD Multi-Factor Authentication を使用してユーザーのサインイン イベントのセキュリティを確保する](tutorial-enable-azure-mfa.md)」を参照してください。
>
> 2019 年 7 月 1 日より前に MFA Server をアクティブ化した既存のお客様は、最新バージョンの今後の更新プログラムをダウンロードし、アクティブ化資格情報を通常どおり生成することができます。

> [!IMPORTANT]
> Azure Multi-Factor Authentication Server v8.x 以降をインストール済みである場合、以下の手順の大半は不要です。 モバイル アプリ認証は、[モバイル アプリの構成](#configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server)に関するセクションの手順に従って設定できます。

## <a name="requirements"></a>必要条件

Microsoft Authenticator アプリを使用するには、Azure Multi-Factor Authentication Server v8.x 以降を実行している必要があります。

## <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Azure Multi-Factor Authentication Server でモバイル アプリ設定を構成する

1. Multi-Factor Authentication Server コンソールで、[ユーザー ポータル] アイコンをクリックします。 ユーザーに認証方法の制御を許可する場合は、[設定] タブで **[ユーザーに認証方法の選択を許可する]** の **[モバイル アプリ]** チェック ボックスをオンにします。 この機能が有効になっていないと、エンド ユーザーはモバイル アプリのアクティブ化を完了するために、ヘルプ デスクに連絡する必要があります。
2. **[ユーザーにモバイル アプリのアクティブ化を許可する]** チェック ボックスをオンにします。
3. **[ユーザー登録を許可する]** チェック ボックスをオンにします。
4. **[モバイル アプリ]** アイコンをクリックします。
5. このアカウントについて、モバイル アプリケーションで表示する会社名または組織名を **[アカウント名]** フィールドに入力します。
   ![MFA Server 構成 Mobile Apps 設定](./media/howto-mfaserver-deploy-mobileapp/mobile.png)

## <a name="next-steps"></a>次のステップ

- [Azure Multi-Factor Authentication とサード パーティ VPN の高度なシナリオ](howto-mfaserver-nps-vpn.md)。
