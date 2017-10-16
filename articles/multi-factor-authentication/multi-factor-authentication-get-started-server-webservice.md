---
title: "Azure MFA Server モバイル アプリ Web サービス | Microsoft Docs"
description: "Microsoft Authenticator アプリには、アウトオブバンド認証オプションも用意されています。  これにより MFA Server で、ユーザーへのプッシュ通知を許可できます。"
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 6c8d6fcc-70f4-4da4-9610-c76d66635b8b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/23/2017
ms.author: joflore
ms.reviewer: alexwe
ms.custom: it-pro
ms.openlocfilehash: bf758d1241f2a56eba4d5c92ace713d6e563df65
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>Azure Multi-Factor Authentication Server でモバイル アプリ認証を有効にする

Microsoft Authenticator アプリには、アウトオブバンド認証オプションも用意されています。 Azure Multi-Factor Authentication では、ログイン時にユーザーに自動音声通話または SMS 送信を行うのではなく、ユーザーのスマートフォンまたはタブレットの Microsoft Authenticator アプリにプッシュ通知を行います。 ユーザーはアプリで **[認証]** をタップ (または PIN を入力し、[認証] をタップ) するだけでサインインを完了することができます。

電波の受信の信頼性に欠けるときには、2 段階認証にモバイル アプリを使用することをお勧めします。 アプリを OATH トークン ジェネレーターとして使用する場合、ネットワーク接続やインターネット接続は不要です。

モバイル アプリ Web サービスは、ご利用の環境に応じて、Azure Multi-Factor Authentication Server と同じサーバーにデプロイするか、またはインターネットに接続された別のサーバーにデプロイしてもかまいません。

## <a name="requirements"></a>必要条件

Microsoft Authenticator アプリを使用するには、アプリがモバイル アプリ Web サービスと正常に通信できるように次の要件があります。

* Azure Multi-Factor Authentication Server v6.0 以降が必要です。
* Microsoft® [インターネット インフォメーション サービス (IIS) 7.x 以降](http://www.iis.net/)を実行している、インターネットに接続された Web サーバーに、モバイル アプリ Web サービスをインストールします。
* ASP.NET v4.0.30319 がインストールおよび登録され、[許可] に設定されている必要があります。
* 必要なロール サービスには、ASP.NET および IIS 6 メタベース互換が含まれます
* モバイル アプリ Web サービスには、パブリック URL を使用してアクセスできる必要があります。
* モバイル アプリ Web サービスは、SSL 証明書で保護されている必要があります。
* **Azure Multi-Factor Authentication Server がインストールされているサーバー**上の IIS 7.x 以降に、Azure Multi-Factor Authentication Web サービス SDK をインストールします。
* Azure Multi-Factor Authentication Web サービス SDK は、SSL 証明書で保護されている必要があります。
* モバイル アプリ Web サービスは、SSL 経由で Azure Multi-Factor Authentication Web サービス SDK に接続できる必要があります。
* モバイル アプリ Web サービスは、"PhoneFactor Admins" セキュリティ グループのメンバーであるサービス アカウントの資格情報を使用して、Azure MFA Web サービス SDK に対して認証できる必要があります。 Azure Multi-Factor Authentication Server がドメインに参加しているサーバーで実行されている場合、このサービス アカウントとグループは Active Directory に存在します。 このサービス アカウントとグループは、Azure Multi-Factor Authentication Server がドメインに参加していない場合、Azure Multi-Factor Authentication Server のローカルに存在します。

## <a name="install-the-mobile-app-web-service"></a>モバイル アプリ Web サービスのインストール

モバイル アプリ Web サービスをインストールする前に、次の点に注意してください。

* "PhoneFactor Admins" グループに属しているサービス アカウントが必要です。 このアカウントには、ユーザー ポータルのインストールに使用したアカウントを使用できます。
* インターネットに接続された Web サーバーで Web ブラウザーを開き、web.config ファイルに入力された Web サービス SDK の URL に移動することをお勧めします。 ブラウザーが Web サービスを正常に取得できる場合、資格情報が求められます。 web.config ファイルに入力したユーザー名とパスワードを、ファイルに表示されているとおりに入力します。 証明書の警告およびエラーが表示されないことを確認してください。
* リバース プロキシまたはファイアウォールがモバイル アプリ Web サービス Web サーバーの前に配置され、SSL オフロードを実行している場合、モバイル アプリ Web サービスが https ではなく http を使用できるように、モバイル アプリ Web サービスの web.config ファイルを編集できます。 モバイル アプリからファイアウォール/リバース プロキシへの SSL は引き続き必要となります。 \<appSettings\> セクションに次のキーを追加します。

        <add key="SSL_REQUIRED" value="false"/>

### <a name="install-the-web-service-sdk"></a>Web サービス SDK のインストール

どちらのシナリオにおいても、Azure Multi-Factor Authentication (MFA) Server に Azure Multi-Factor Authentication Web サービス SDK がまだインストールされて**いない**場合は、以下の手順を実行してください。

1. Multi-Factor Authentication Server コンソールを開きます。
2. **[Web Service SDK]** に移動し、**[Web サービス SDK のインストール]** を選択します。
3. インストールは既定の設定で行ってください (何らかの理由で変更しなければならない場合を除く)。
4. IIS 内のサイトに SSL 証明書をバインドします。

IIS サーバーに SSL 証明書を構成する方法についてご不明な点があれば、[IIS における SSL のセットアップ方法](https://docs.microsoft.com/en-us/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)に関する記事を参照してください。

Web サービス SDK は、SSL 証明書で保護されている必要があります。 このために自己署名証明書を使用できます。 SSL 接続を開始するときにその証明書が信頼されるように、ユーザー ポータル Web サーバー上のローカル コンピューター アカウントの "信頼されたルート証明機関" ストアに証明書をインポートします。

![MFA Server 構成セットアップ Web サービス SDK](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)

### <a name="install-the-service"></a>サービスのインストール

1. **MFA Server** でインストール パスを参照します。
2. Azure MFA Server がインストールされているフォルダーに移動します (既定では **C:\Program Files\Azure Multi-Factor Authentication**)。
3. インストール ファイル **MultiFactorAuthenticationMobileAppWebServiceSetup64** を探します。 このサーバーがインターネットに接続されて**いない**場合は、インターネットに接続しているサーバーにこのインストール ファイルをコピーします。
4. MFA Server がインターネットに接続されて**いない**場合は、**インターネットに接続しているサーバー**に切り替えます。
5. **MultiFactorAuthenticationMobileAppWebServiceSetup64** インストール ファイルを管理者として実行し、必要な場合はサイトを変更し、仮想ディレクトリを適宜短い名前に変更します。
6. インストールが完了したら、**C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService** (または仮想ディレクトリ名に基づく適切なディレクトリ) に移動し、Web.Config ファイルを編集します。

   * **"WEB_SERVICE_SDK_AUTHENTICATION_USERNAME"** というキーを探して **value=""** を **value="DOMAIN\User"** に変更します。DOMAIN\User には、"PhoneFactor Admins" グループに属しているサービス アカウントを指定してください。
   * **"WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD"** というキーを探して **value=""** を **value="Password"** に変更します。Password には、前の行で入力したサービス アカウントのパスワードを指定してください。
   * **pfMobile App Web Service_pfwssdk_PfWsSdk** という設定を探し、**http://localhost:4898/PfWsSdk.asmx** の値を Web Service SDK の URL (例: https://mfa.contoso.com/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx) に変更します。
   * Web.Config ファイルを保存してメモ帳を閉じます。

   > [!NOTE]
   > この接続には SSL が使用されているため、**IP アドレスではなく****完全修飾ドメイン名 (FQDN)** で Web サービス SDK を参照する必要があります。 SSL 証明書は FQDN に対して発行されており、使用される URL が証明書の名前と一致する必要があります。

7. モバイル アプリ Web サービスがインストールされた Web サイトが公的署名証明書にまだバインドされていない場合は、証明書をサーバーにインストールし、IIS マネージャーを開いて、証明書を Web サイトにバインドします。
8. 任意のコンピューターで Web ブラウザーを開き、モバイル アプリ Web サービスがインストールされた URL に移動します (例: https://mfa.contoso.com/MultiFactorAuthMobileAppWebService)。 証明書の警告およびエラーが表示されないことを確認してください。

## <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Azure Multi-Factor Authentication Server でモバイル アプリ設定を構成する

モバイル アプリ Web サービスがインストールされたため、ポータルから Azure Multi-Factor Authentication Server を構成する必要があります。

1. Multi-Factor Authentication Server コンソールで、[ユーザー ポータル] アイコンをクリックします。 ユーザーに認証方法の制御を許可する場合は、[設定] タブで **[ユーザーに認証方法の選択を許可する]** の **[モバイル アプリ]** チェック ボックスをオンにします。 この機能が有効になっていないと、エンド ユーザーはモバイル アプリのアクティブ化を完了するために、ヘルプ デスクに連絡する必要があります。
2. **[ユーザーにモバイル アプリのアクティブ化を許可する]** チェック ボックスをオンにします。
3. **[ユーザー登録を許可する]** チェック ボックスをオンにします。
4. **[モバイル アプリ]** アイコンをクリックします。
5. MultiFactorAuthenticationMobileAppWebServiceSetup64 のインストール時に作成された仮想ディレクトリで使用する URL (例: https://mfa.contoso.com/MultiFactorAuthMobileAppWebService/) を **[モバイル アプリ Web サービス URL]** フィールドに入力します。
6. このアカウントについて、モバイル アプリケーションで表示する会社名または組織名を **[アカウント名]** フィールドに入力します。
   ![MFA Server 構成 Mobile Apps 設定](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)

## <a name="next-steps"></a>次のステップ

- [Azure Multi-Factor Authentication とサード パーティ VPN の高度なシナリオ](multi-factor-authentication-advanced-vpn-configurations.md)。