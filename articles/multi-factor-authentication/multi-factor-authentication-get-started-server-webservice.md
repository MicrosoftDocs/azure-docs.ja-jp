---
title: "Azure MFA Server モバイル アプリ Web サービス | Microsoft Docs"
description: "Microsoft Authenticator アプリには、アウトオブバンド認証オプションも用意されています。  これにより MFA Server で、ユーザーへのプッシュ通知を許可できます。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 6c8d6fcc-70f4-4da4-9610-c76d66635b8b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/25/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 20afeb3ba290ddf728d2b52c076c7a57fadc77c6
ms.openlocfilehash: 4014bf0217e25ea9bc8473ef2383279e5eb79b87
ms.lasthandoff: 02/28/2017

---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>Azure Multi-Factor Authentication Server でモバイル アプリ認証を有効にする

Microsoft Authenticator アプリには、アウトオブバンド認証オプションも用意されています。 Azure Multi-Factor Authentication では、ログイン時にユーザーに自動音声通話または SMS 送信を行うのではなく、ユーザーのスマートフォンまたはタブレットの Microsoft Authenticator アプリにプッシュ通知を行います。 ユーザーはアプリで **[認証]** をタップ (または PIN を入力し、[認証] をタップ) するだけでサインインを完了することができます。 

電波の受信の信頼性に欠けるときには、2 段階認証にモバイル アプリを使用することをお勧めします。 アプリを OATH トークン ジェネレーターとして使用する場合、ネットワーク接続やインターネット接続は不要です。 

Azure Multi-Factor Authentication Server 以外のサーバーにユーザー ポータルをインストールするには、次の手順が必要です。

1. Web サービス SDK のインストール
2. モバイル アプリ Web サービスのインストール
3. Azure Multi-Factor Authentication Server でモバイル アプリ設定を構成する
4. エンド ユーザーの Microsoft Authenticator アプリをアクティブにする

## <a name="requirements"></a>必要条件

Microsoft Authenticator アプリを使用するには、アプリがモバイル アプリ Web サービスと正常に通信できるように次の要件があります。

* Azure Multi-Factor Authentication Server v6.0 以降が必要です。
* Microsoft® [インターネット インフォメーション サービス (IIS) 7.x 以降](http://www.iis.net/)を実行している、インターネットに接続された Web サーバーに、モバイル アプリ Web サービスをインストールします。
* ASP.NET v4.0.30319 がインストールおよび登録され、[許可] に設定されている必要があります。
* 必要なロール サービスには、ASP.NET および IIS 6 メタベース互換が含まれます
* モバイル アプリ Web サービスには、パブリック URL を使用してアクセスできる必要があります。
* モバイル アプリ Web サービスは、SSL 証明書で保護されている必要があります。
* Azure Multi-Factor Authentication Server がインストールされているサーバー上の IIS 7.x 以降に、Azure Multi-Factor Authentication Web サービス SDK をインストールします。
* Azure Multi-Factor Authentication Web サービス SDK は、SSL 証明書で保護されている必要があります。
* モバイル アプリ Web サービスは、SSL 経由で Azure Multi-Factor Authentication Web サービス SDK に接続できる必要があります。
* モバイル アプリ Web サービスは、"PhoneFactor Admins" セキュリティ グループのメンバーであるサービス アカウントの資格情報を使用して、Azure MFA Web サービス SDK に対して認証できる必要があります。。 Azure Multi-Factor Authentication Server がドメインに参加しているサーバーで実行されている場合、このサービス アカウントとグループは Active Directory に存在します。 このサービス アカウントとグループは、Azure Multi-Factor Authentication Server がドメインに参加していない場合、Azure Multi-Factor Authentication Server のローカルに存在します。


## <a name="install-the-web-service-sdk"></a>Web サービス SDK のインストール
Azure Multi-Factor Authentication (MFA) Server に Azure Multi-Factor Authentication Web サービス SDK がまだインストールされていない場合は、そのサーバーに移動し、Azure MFA Server を開きます。 

1. [Web サービス SDK] アイコンをクリックします。
2. **[Web サービス SDK のインストール]** をクリックし、表示された手順に従います。 

Web サービス SDK は、SSL 証明書で保護されている必要があります。 このために自己署名証明書を使用できます。 SSL 接続を開始するときにその証明書が信頼されるように、ユーザー ポータル Web サーバー上のローカル コンピューター アカウントの "信頼されたルート証明機関" ストアに証明書をインポートします。

![[Setup]](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)

## <a name="install-the-mobile-app-web-service"></a>モバイル アプリ Web サービスのインストール
モバイル アプリ Web サービスをインストールする前に、次の点に注意してください。

* Azure MFA ユーザー ポータルが、インターネットに接続されたサーバーに既にインストールされている場合、Web サービス SDK のユーザー名、パスワード、URL は、ユーザー ポータルの web.config ファイルからコピーできます。
* インターネットに接続された Web サーバーで Web ブラウザーを開き、web.config ファイルに入力された Web サービス SDK の URL に移動することをお勧めします。 ブラウザーが Web サービスを正常に取得できる場合、資格情報が求められます。 web.config ファイルに入力したユーザー名とパスワードを、ファイルに表示されているとおりに入力します。 証明書の警告およびエラーが表示されないことを確認してください。
* リバース プロキシまたはファイアウォールがモバイル アプリ Web サービス Web サーバーの前に配置され、SSL オフロードを実行している場合、モバイル アプリ Web サービスが https ではなく http を使用できるように、モバイル アプリ Web サービスの web.config ファイルを編集できます。 モバイル アプリからファイアウォール/リバース プロキシへの SSL は引き続き必要となります。 \<appSettings\> セクションに次のキーを追加します。 

        <add key="SSL_REQUIRED" value="false"/>

### <a name="install-the-service"></a>サービスのインストール

1. Azure Multi-Factor Authentication Server でエクスプローラーを開き、Azure MFA Server がインストールされているフォルダー (通常は C:\Program Files\Azure Multi-Factor Authentication) に移動します。 Azure Multi-Factor AuthenticationPhoneAppWebServiceSetup インストール ファイルの 32 ビットまたは 64 ビット バージョンを選択します。 インターネットに接続されたサーバーにインストール ファイルをコピーします。

2. インターネットに接続された Web サーバーで、セットアップ ファイルを管理者権限で実行します。 管理者としてコマンド プロンプトを開き、インストール ファイルをコピーした場所に移動します。

3. Multi-Factor AuthenticationMobileAppWebServiceSetup インストール ファイルを実行します。必要に応じてサイトを変更し、仮想ディレクトリを "PA" などの短い名前に変更します。

  ユーザーはアクティブ化の際にモバイル アプリ Web サービス URL をモバイル デバイスに入力する必要があるため、短い仮想ディレクトリ名をお勧めします。

4. Azure Multi-Factor AuthenticationMobileAppWebServiceSetup のインストールを完了したら、C:\inetpub\wwwroot\PA (または仮想ディレクトリ名に基づく適切なディレクトリ) に移動し、web.config ファイルを編集します。 

5. WEB_SERVICE_SDK_AUTHENTICATION_USERNAME キーと WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD キーを見つけます。 これらの値を、PhoneFactor Admins セキュリティ グループのメンバーであるサービス アカウントのユーザー名とパスワードに設定します。 既にインストールされている場合は、Azure Multi-Factor Authentication ユーザー ポータルの ID として使用されるのと同じアカウントである可能性があります。 行末尾 (value=””/>) の引用符の間にユーザー名およびパスワードを入力するようにしてください。 修飾されたユーザー名 (domain\username や machine\username など) を使用します。  

6. pfMobile App Web Service_pfwssdk_PfWsSdk 設定を見つけます。 値を *http://localhost:4898/PfWsSdk.asmx* から Azure Multi-Factor Authentication Server で実行されている Web サービス SDK の URL (例: https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx) に変更します。 

  この接続には SSL が使用されているため、IP アドレスではなくサーバー名で Web サービス SDK を参照する必要があります。 SSL 証明書はサーバー名に対して発行されており、使用される URL が証明書の名前と一致する必要があります。 サーバー名がインターネットに接続されたサーバーの IP アドレスに解決されない場合があります。 この場合、そのサーバーの hosts ファイルにエントリを追加して、Azure Multi-Factor Authentication Server の名前を IP アドレスにマップします。 変更を行ったら、web.config ファイルを保存します。

7. モバイル アプリ Web サービスがインストールされた Web サイトが公的署名証明書にまだバインドされていない場合は、証明書をサーバーにインストールし、IIS マネージャーを開いて、証明書を Web サイトにバインドします。

8. 任意のコンピューターで Web ブラウザーを開き、モバイル アプリ Web サービスがインストールされた URL (https://www.publicwebsite.com/PA など) に移動します。 証明書の警告およびエラーが表示されないことを確認してください。

### <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Azure Multi-Factor Authentication Server でモバイル アプリ設定を構成する
モバイル アプリ Web サービスがインストールされたため、ポータルから Azure Multi-Factor Authentication Server を構成する必要があります。

1. Azure MFA Server で、[ユーザー ポータル] アイコンをクリックします。 ユーザーに認証方法の制御を許可する場合は、[設定] タブで **[ユーザーに認証方法の選択を許可する]** の **[モバイル アプリ]** チェック ボックスをオンにします。 この機能が有効になっていないと、エンドユーザーはモバイル アプリのアクティブ化を完了するために、ヘルプ デスクに連絡する必要があります。
2. **[ユーザーにモバイル アプリのアクティブ化を許可する]** チェック ボックスをオンにします。
3. **[ユーザー登録を許可する]** チェック ボックスをオンにします。
4. [モバイル アプリ] アイコンをクリックします。
5. Azure Multi-Factor AuthenticationMobileAppWebServiceSetup のインストール時に作成された仮想ディレクトリで使用する URL を入力します。 所定の場所にアカウント名を入力することがあります。 この会社名はモバイル アプリケーションに表示されます。 空欄のままにすると、Azure クラシック ポータルで作成された Multi-Factor Auth Provider の名前が表示されます。

<center>![セットアップ](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)</center>

