---
title: "PhoneFactor から Azure MFA Server へのアップグレード | Microsoft Docs"
description: "古い phonefactor エージェントからアップグレードする場合の Azure MFA Server の概要。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 42838ff7-bdf2-4d06-bacc-b3839a00cd76
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/06/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 4547a805c1827a703bf0ef118387882e45c3f241
ms.openlocfilehash: 5bbd7088f5d4986a5b1c9f5695a5defb678307a9


---
# <a name="upgrade-the-phonefactor-agent-to-azure-multi-factor-authentication-server"></a>PhoneFactor エージェントから Azure Multi-Factor Authentication Server へのアップグレード
PhoneFactor エージェント v5.x 以前から Azure Multi-factor Authentication Server にアップグレードするには、PhoneFactor エージェントと関連コンポーネントを最初にアンインストールします。 その後で、Multi-factor Authentication Server とその関連コンポーネントをインストールすることができます。

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

  既定の仮想ディレクトリ名は、"PhoneFactorWebServiceSdk" ではなく "MultiFactorAuthWebServiceSdk" になります。 以前の名前を使用する場合は、インストール時に仮想ディレクトリ名を変更する必要があります。 そのようにしない場合、インストールで新しい既定の名前の使用を許可する場合は、ユーザー ポータルやモバイル アプリ Web サービスなどの Web サービス SDK を参照する任意のアプリケーションの URL を正しい場所を示すように変更する必要があります。

3. ユーザー ポータルが既に PhoneFactor エージェント サーバーにインストールされている場合は、Multi-Factor Authentication Server のユーザー インターフェイスより、新しい Multi-Factor Authentication ユーザー ポータルをインストールします。

  既定の仮想ディレクトリ名は、"PhoneFactor" ではなく "MultiFactorAuth" になります。 以前の名前を使用する場合は、インストール時に仮想ディレクトリ名を変更する必要があります。 そのようにしない場合、インストールで新しい既定の名前の使用を許可する場合は、Multi-Factor Authentication Server の [ユーザー ポータル] アイコンをクリックし、[設定] タブのユーザー ポータル URL を更新する必要があります。

4. 過去にユーザー ポータルまたはモバイル アプリ Web サービスが、PhoneFactor エージェントとは別のサーバーにインストールされていた場合は、次の手順を実行します。

  1. インストール場所 (例: C:\Program Files\PhoneFactor) に移動して適切なインストーラーをもう一方のサーバーにコピーします。 ユーザー ポータルとモバイル アプリ Web サービスには、32 ビットおよび 64 ビットのインストーラーがあります。 それぞれ、MultiFactorAuthenticationUserPortalSetupXX.msi と MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi という名前です。

  2. Web サーバー上にユーザー ポータルをインストールするには、コマンド プロンプトを管理者として開き、MultiFactorAuthenticationUserPortalSetupXX.msi を実行します。

    既定の仮想ディレクトリ名は、"PhoneFactor" ではなく "MultiFactorAuth" になります。 以前の名前を使用する場合は、インストール時に仮想ディレクトリ名を変更する必要があります。 そのようにしない場合、インストールで新しい既定の名前の使用を許可する場合は、Multi-Factor Authentication Server の [ユーザー ポータル] アイコンをクリックし、[設定] タブのユーザー ポータル URL を更新する必要があります。 既存のユーザーに、新しい URL を通知する必要があります。

  3. ユーザー ポータルのインストール先 (C:\inetpub\wwwroot\MultiFactorAuth など) に移動し、web.config ファイルを編集します。 新しい web.config ファイルへのアップグレード前にバックアップされた元の web.config ファイルから、appSettings および applicationSettings セクションの値をコピーします。 Web サービス SDK をインストールするときに新しい既定の仮想ディレクトリ名が保持された場合は、正しい場所を示すように applicationSettings セクションの URL を変更します。 他の任意の既定値が以前の web.config ファイルで変更された場合、その同様の変更を新しい web.config ファイルに適用します。

  4. モバイル アプリ Web サービスを Web サーバーにインストールするには、コマンド プロンプトを管理者として開き、MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi を実行します。

    既定の仮想ディレクトリ名は、"PhoneFactorPhoneAppWebService" ではなく "MultiFactorAuthMobileAppWebService" になります。 以前の名前を使用する場合は、インストール時に仮想ディレクトリ名を変更する必要があります。 エンドユーザーがモバイル デバイスに簡単に入力できるように、短い名前を選択できます。 そのようにしない場合、インストールで新しい既定の名前の使用を許可する場合は、Multi-Factor Authentication Server の [モバイル アプリ] アイコンをクリックし、モバイル アプリ Web サービス URL を更新する必要があります。

  5. モバイル アプリ Web サービスのインストール先 (C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService など) に移動し、web.config ファイルを編集します。 新しい web.config ファイルへのアップグレード前にバックアップされた元の web.config ファイルから、appSettings および applicationSettings セクションの値をコピーします。 Web サービス SDK をインストールするときに新しい既定の仮想ディレクトリ名が保持された場合は、正しい場所を示すように applicationSettings セクションの URL を変更します。 他の任意の既定値が以前の web.config ファイルで変更された場合、その同様の変更を新しい web.config ファイルに適用します。

## <a name="next-steps"></a>次のステップ

- Azure Multi-Factor Authentication Server の[ユーザー ポータルをインストールする](multi-factor-authentication-get-started-portal.md)。

- アプリケーションの [Windows 認証を構成する](multi-factor-authentication-get-started-server-windows.md)。 



<!--HONumber=Feb17_HO1-->


