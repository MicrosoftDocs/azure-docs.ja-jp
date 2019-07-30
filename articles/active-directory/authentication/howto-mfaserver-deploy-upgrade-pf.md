---
title: PhoneFactor から Azure MFA Server へのアップグレード | Azure Active Directory
description: 古い phonefactor エージェントからアップグレードする場合の Azure MFA Server の概要。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcb1bf4996e381db933eac91135093a101e504bb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055986"
---
# <a name="upgrade-the-phonefactor-agent-to-azure-multi-factor-authentication-server"></a>PhoneFactor エージェントから Azure Multi-Factor Authentication Server へのアップグレード

PhoneFactor エージェント v5.x 以前から Azure Multi-factor Authentication Server にアップグレードするには、PhoneFactor エージェントと関連コンポーネントを最初にアンインストールします。 その後で、Multi-factor Authentication Server とその関連コンポーネントをインストールすることができます。

> [!IMPORTANT]
> 2019 年 7 月 1 日より、Microsoft では新しいデプロイに対して MFA Server が提供されなくなります。 ユーザーからの多要素認証が必要な新しいお客様は、クラウドベースの Azure Multi-Factor Authentication を使用していただく必要があります。 7 月 1 日より前に MFA Server をアクティブ化した既存のお客様は、最新バージョンの今後の更新プログラムをダウンロードし、アクティブ化資格情報を通常どおり生成することができます。

## <a name="uninstall-the-phonefactor-agent"></a>PhoneFactor エージェントのアンインストール

1. まず、PhoneFactor データ ファイルをバックアップします。 既定のインストール先は C:\Program Files\PhoneFactor\Data\Phonefactor.pfdata です。

2. ユーザー ポータルがインストールされている場合は、
   1. インストール フォルダーに移動し、web.config ファイルをバックアップします。 既定のインストール先は C:\inetpub\wwwroot\PhoneFactor です。

   2. カスタム テーマをポータルに追加した場合は、C:\inetpub\wwwroot\PhoneFactor\App_Themes ディレクトリ以下にカスタム フォルダーをバックアップします。

   3. ユーザー ポータルを PhoneFactor エージェント (PhoneFactor エージェントと同じサーバーにインストールされている場合にのみ使用可能) または Windows の [プログラムと機能] よりアンインストールします。

3. モバイル アプリ Web サービスがインストールされている場合は、次の手順を実行します。

   1. インストール フォルダーに移動し、web.config ファイルをバックアップします。 既定のインストール先は C:\inetpub\wwwroot\PhoneFactorPhoneAppWebService です。

   2. Windows の [プログラムと機能] よりモバイル アプリ Web サービスをアンインストールします。

4. Web サービス SDK がインストールされている場合、PhoneFactor エージェントまたは Windows の [プログラムと機能] よりアンインストールします。

5. Windows の [プログラムと機能] より PhoneFactor エージェントをアンインストールします。

## <a name="install-the-multi-factor-authentication-server"></a>Multi-Factor Authentication Server のインストール

インストール パスは以前の PhoneFactor エージェントのインストールのレジストリから取得されるため、同じ場所 (C:\Program Files\PhoneFactor など) にインストールされます。 新しくインストールする場合は、異なる既定のインストール パス (C:\Program Files\Multi-Factor Authentication Server など) が作成されます。 以前の PhoneFactor エージェントにより残されたデータ ファイルはインストール時にアップグレードされるため、ユーザーおよび設定は新しい Multi-Factor Authentication Server のインストール後もそこに残す必要があります。

1. プロンプトが表示される場合は、Multi-Factor Authentication Server をアクティブ化し、適切なレプリケーション グループに割り当てられるようにします。

2. Web サービス SDK が既にインストールされている場合は、Multi-Factor Authentication Server のユーザー インターフェイスより新しい Web サービス SDK をインストールします。

   既定の仮想ディレクトリ名は、**PhoneFactorWebServiceSdk** ではなく **MultiFactorAuthWebServiceSdk** になります。 以前の名前を使用する場合は、インストール時に仮想ディレクトリ名を変更する必要があります。 そのようにしない場合、インストールで新しい既定の名前の使用を許可する場合は、(ユーザー ポータルやモバイル アプリ Web サービスなど) Web サービス SDK を参照する任意のアプリケーションの URL を正しい場所を示すように変更する必要があります。

3. ユーザー ポータルが既に PhoneFactor エージェント サーバーにインストールされている場合は、Multi-Factor Authentication Server のユーザー インターフェイスより、新しい Multi-Factor Authentication ユーザー ポータルをインストールします。

   既定の仮想ディレクトリ名は、**PhoneFactor** ではなく **MultiFactorAuth** になります。 以前の名前を使用する場合は、インストール時に仮想ディレクトリ名を変更する必要があります。 そのようにしない場合、インストールで新しい既定の名前の使用を許可する場合は、Multi-Factor Authentication Server の [ユーザー ポータル] アイコンをクリックし、[設定] タブのユーザー ポータル URL を更新する必要があります。

4. 過去にユーザー ポータルまたはモバイル アプリ Web サービスが、PhoneFactor エージェントとは別のサーバーにインストールされていた場合は、次の手順を実行します。

   1. インストール場所 (例: C:\Program Files\PhoneFactor) に移動して、インストーラーをもう一方のサーバーにコピーします。 ユーザー ポータルとモバイル アプリ Web サービスには、32 ビットおよび 64 ビットのインストーラーがあります。 MultiFactorAuthenticationUserPortalSetupXX.msi および MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi という名前です。

   2. Web サーバー上にユーザー ポータルをインストールするには、コマンド プロンプトを管理者として開き、MultiFactorAuthenticationUserPortalSetupXX.msi を実行します。

      既定の仮想ディレクトリ名は、**PhoneFactor** ではなく **MultiFactorAuth** になります。 以前の名前を使用する場合は、インストール時に仮想ディレクトリ名を変更する必要があります。 そのようにしない場合、インストールで新しい既定の名前の使用を許可する場合は、Multi-Factor Authentication Server の [ユーザー ポータル] アイコンをクリックし、[設定] タブのユーザー ポータル URL を更新する必要があります。既存のユーザーに、新しい URL を通知する必要があります。

   3. ユーザー ポータルのインストール先 (C:\inetpub\wwwroot\MultiFactorAuth など) に移動し、web.config ファイルを編集します。 新しい web.config ファイルへのアップグレード前にバックアップされた元の web.config ファイルから、appSettings および applicationSettings セクションの値をコピーします。 Web サービス SDK をインストールするときに新しい既定の仮想ディレクトリ名が保持された場合は、正しい場所を示すように applicationSettings セクションの URL を変更します。 他の任意の既定値が以前の web.config ファイルで変更された場合、その同様の変更を新しい web.config ファイルに適用します。

> [!NOTE]
> 8\.0 よりも前のバージョンから Azure MFA Server 8.0 以降にアップグレードするときは、アップグレード後にモバイル アプリ Web サービスをアンインストールできます

## <a name="next-steps"></a>次の手順

- Azure Multi-Factor Authentication Server の[ユーザー ポータルをインストールする](howto-mfaserver-deploy-userportal.md)。

- アプリケーションの [Windows 認証を構成する](howto-mfaserver-windows.md)。 
