---
title: "Azure MFA Server のユーザー ポータル | Microsoft Docs"
description: "Azure MFA およびユーザー ポータルを開始する方法について説明する Azure Multi-Factor Authentication のページです。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 06b419fa-3507-4980-96a4-d2e3960e1772
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/04/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 1222223f8c45249402bfdd04c8754074f877e132
ms.openlocfilehash: 1236489212b2a9c421972599a12511d5bc42efdf
ms.lasthandoff: 02/16/2017


---
# <a name="deploy-the-user-portal-for-the-azure-multi-factor-authentication-server"></a>Azure Multi-Factor Authentication Server のユーザー ポータルのデプロイ
管理者はユーザー ポータルを使用して、Azure Multi-Factor Authentication ユーザー ポータルをインストールおよび構成することができます。 ユーザー ポータルは、ユーザーが Azure Multi-Factor Authentication に登録を行い、アカウントを保守するための IIS Web サイトです。 ユーザーは次のサインイン時に、電話番号の変更、PIN の変更、または 2 段階認証をバイパスを行うことができます。

ユーザーは、通常のユーザー名とパスワードを使用してユーザー ポータルにサインインし、2 段階認証呼び出しを完了するか、セキュリティに関する質問に回答して認証を完了します。 ユーザー登録が許可されている場合、ユーザー ポータルに最初にサインインする際にユーザーは電話番号と PIN を構成します。

ユーザー ポータルの管理者をセットアップして、新しいユーザーを追加する権限と既存のユーザーを更新する権限を付与することができます。

<center>![セットアップ](./media/multi-factor-authentication-get-started-portal/install.png)</center>

## <a name="deploy-the-user-portal-on-the-same-server-as-the-azure-multi-factor-authentication-server"></a>Azure Multi-Factor Authentication Server と同じサーバーにユーザー ポータルをデプロイする
Azure Multi-Factor Authentication Server と同じサーバーにユーザー ポータルをインストールするには、次の前提条件が必要です。

* asp.net と IIS 6 メタベース互換性を含む IIS (IIS 7 以降の場合)
* ログインしているユーザーに、コンピューターとドメイン (該当する場合) に対する管理者権限が必要です。  このアカウントには Active Directory セキュリティ グループを作成するためのアクセス許可が必要だからです。

### <a name="to-deploy-the-user-portal"></a>ユーザー ポータルをデプロイするには
1. Azure Multi-Factor Authentication Server 内で、左側のメニューの **[ユーザー ポータル]** アイコンをクリックし、**[ユーザー ポータルのインストール]** をクリックします。
2. **[次へ]**をクリックします。
3. **[次へ]**をクリックします。
4. コンピューターがドメインに参加していて、ユーザー ポータルと Azure Multi-Factor Authentication サービスの間の通信をセキュリティで保護するように Active Directory が構成されていない場合は、Active Directory の手順が表示されます。 **[次へ]** をクリックして、この構成を自動的に完了します。
5. **[次へ]**をクリックします。
6. **[次へ]**をクリックします。
7. **[閉じる]**をクリックします。
8. 任意のコンピューターで Web ブラウザーを開き、ユーザー ポータルがインストールされた URL に移動します (例: https://www.publicwebsite.com/MultiFactorAuth)。 証明書の警告およびエラーが表示されないことを確認してください。

<center>![セットアップ](./media/multi-factor-authentication-get-started-portal/portal.png)</center>

## <a name="deploy-the-azure-multi-factor-authentication-server-user-portal-on-a-separate-server"></a>別のサーバーに Azure Multi-Factor Authentication Server ユーザー ポータルをデプロイする
Microsoft Authenticator アプリにユーザー ポータルとの通信を許可するには、次の要件を満たしてください。 

* Azure Multi-Factor Authentication Server の V6.0 以降を使用している必要があります。
* Microsoft® インターネット インフォメーション サービス (IIS) 6.x、IIS 7.x 以降を実行している、インターネットに接続された Web サーバーに、ユーザー ポータルがインストールされている必要があります。
* IIS 6.x を使用している場合は、ASP.NET v2.0.50727 がインストールおよび登録され、**[許可]** に設定されていることを確認します。
* IIS 7.x 以降を使用するときに必要な役割サービスには、ASP.NET および IIS 6 メタベース互換が含まれます。
* ユーザー ポータルは、SSL 証明書で保護されている必要があります。
* Azure Multi-Factor Authentication Web サービス SDK が、Azure Multi-Factor Authentication Server がインストールされているサーバー上の IIS 6.x または IIS 7.x 以降にインストールされている必要があります。
* Azure Multi-Factor Authentication Web サービス SDK は、SSL 証明書で保護されている必要があります。
* ユーザー ポータルは、SSL 経由で Azure Multi-Factor Authentication Web サービス SDK に接続できる必要があります。
* ユーザー ポータルは、"PhoneFactor Admins" セキュリティ グループのサービス アカウントの資格情報を使用して、Azure Multi-Factor Authentication Web サービス SDK で認証できる必要があります。 このサービス アカウントとグループは、Azure Multi-Factor Authentication Server がドメインに参加しているサーバーで実行されている場合は、Active Directory に存在します。 このサービス アカウントとグループは、Azure Multi-Factor Authentication Server がドメインに参加していない場合、Azure Multi-Factor Authentication Server のローカルに存在します。

Azure Multi-Factor Authentication Server 以外のサーバーでユーザー ポータルをインストールするには、次の 3 つの手順が必要です。

1. Web サービス SDK のインストール
2. ユーザー ポータルのインストール
3. Azure Multi-Factor Authentication Server のユーザー ポータル設定の構成

### <a name="step-1-install-the-web-service-sdk"></a>手順 1: Web サービス SDK をインストールする
Azure Multi-Factor Authentication Server に Azure Multi-Factor Authentication Web サービス SDK がまだインストールされていない場合、そのサーバーに移動し、Azure Multi-Factor Authentication Server を開きます。 **[Web サービス SDK]** アイコンをクリックし、**[Web サービス SDK のインストール]** をクリックします。 表示される指示に従います。 

Web サービス SDK は、SSL 証明書で保護されている必要があります。 この目的で自己署名証明書を使用できますが、サーバー上のローカル コンピューター アカウントの "信頼されたルート証明機関" ストアに証明書をインポートする必要があります。 これで、SSL 接続の開始時に Web サービス SDK がこの証明書を信頼できるようになります。

<center>![セットアップ](./media/multi-factor-authentication-get-started-portal/sdk.png)</center>

### <a name="step-2-install-the-user-portal"></a>手順 2: ユーザー ポータルをインストールする
別のサーバーにユーザー ポータルをインストールする前に、以下のベスト プラクティスに留意してください。

* インターネットに接続された Web サーバーで Web ブラウザーを開き、web.config ファイルに入力された Web サービス SDK の URL に移動することをお勧めします。 ブラウザーが Web サービスを正常に取得できる場合、資格情報が求められます。 web.config ファイルに入力したユーザー名とパスワードを、ファイルに表示されているとおりに入力します。 証明書の警告およびエラーが表示されないことを確認してください。
* リバース プロキシまたはファイアウォールがユーザー ポータル Web サーバーの前面に配置され、SSL オフロードを実行している場合は、ユーザー ポータルの web.config ファイルを編集し、次のキーを `<appSettings>` セクションに追加することで、ユーザー ポータルで https ではなく http を使用できるようになります。

    `<add key="SSL_REQUIRED" value="false"/>`

#### <a name="to-install-the-user-portal"></a>ユーザー ポータルをインストールするには
1. Azure Multi-Factor Authentication Server サーバーで Windows エクスプ ローラーを開き、Azure Multi-Factor Authentication Server がインストールされているフォルダーに移動します (C:\Program Files\Multi-Factor Authentication Server など)。 ユーザー ポータルがインストールされるサーバーに応じて、MultiFactorAuthenticationUserPortalSetup インストール ファイルの 32 ビットまたは 64 ビット バージョンを選択します。 インターネットに接続されたサーバーにインストール ファイルをコピーします。
2. インターネットに接続された Web サーバーで、セットアップ ファイルを管理者権限で実行する必要があります。 これを最も簡単に行うには、管理者としてコマンド プロンプトを開き、インストール ファイルがコピーされた場所に移動します。
3. MultiFactorAuthenticationUserPortalSetup64 のインストール ファイルを実行し、必要に応じて、サイトと仮想ディレクトリの名前を変更します。
4. ユーザー ポータルのインストールを完了したら、C:\inetpub\wwwroot\MultiFactorAuth (または仮想ディレクトリ名に基づく適切なディレクトリ) に移動し、web.config ファイルを編集します。
5. USE_WEB_SERVICE_SDK キーを見つけて、値を false から true に変更します。 WEB_SERVICE_SDK_AUTHENTICATION_USERNAME キーと WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD キーを見つけ、PhoneFactor Admins セキュリティ グループのサービス アカウントのユーザー名とパスワードに値を設定します (要件のセクションを参照)。 行末尾 (value=””/>) の引用符の間にユーザー名およびパスワードを入力するようにしてください。 修飾されたユーザー名 (domain\username や machine\username など) を使用する必要があります。
6. pfup_pfwssdk_PfWsSdk 設定を見つけ、値を「http://localhost:4898/PfWsSdk.asmx」から Azure Multi-Factor Authentication Server で実行している Web サービス SDK の URL に変更します (https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx など)。 この接続には SSL が使用されているため、SSL 証明書がサーバー名に対して発行された後は、IP アドレスではなくサーバー名を使用して Web サービス SDK を参照してください。 サーバー名がインターネットに接続されているサーバーで IP アドレスに解決されない場合、そのサーバーの hosts ファイルにエントリを追加し、Azure Multi-Factor Authentication Server の名前を IP アドレスにマッピングします。 変更を行ったら、web.config ファイルを保存します。

    config ファイルの編集の詳細については、[Azure Multi-Factor Authentication Server と AD FS を使用したリソースのセキュリティ保護](multi-factor-authentication-get-started-adfs-w2k12.md#edit-the-multifactorauthenticationadfsadapterconfig-file)に関する記事を参照してください。

7. ユーザー ポータルがインストールされた Web サイト (例: 既定の Web サイト) がまだ公的署名証明書にバインドされていない場合、サーバーに証明書をインストールし、IIS マネージャーを開いて証明書を Web サイトにバインドします。
8. 任意のコンピューターで Web ブラウザーを開き、ユーザー ポータルがインストールされた URL に移動します (例: https://www.publicwebsite.com/MultiFactorAuth)。 証明書の警告およびエラーが表示されないことを確認してください。

### <a name="step-3-configure-the-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>手順 3: Azure Multi-Factor Authentication Server のユーザー ポータル設定を構成する
ユーザー ポータルがインストールされたため、Azure Multi-Factor Authentication Server を構成してポータルと連携させる必要があります。

Azure Multi-Factor Authentication Server にはユーザー ポータル用のさまざまなオプションがあります。  次の表に、これらのオプションとそれぞれの使用目的を示します。

| ユーザー ポータル設定 | Description |
|:--- |:--- |
| ユーザー ポータル URL | ポータルがホストされる場所の URL を入力します。 |
| プライマリ認証 | ポータルにサインインするときに使用する認証の種類を指定します。  Windows 認証、Radius 認証または LDAP 認証のいずれかです。 |
| ユーザーにログインを許可する | ユーザー ポータルのサインイン ページにユーザー名とパスワードを入力できるようにします。  これが選択されていない場合、ボックスは淡色表示されます。 |
| ユーザー登録を許可する | 電話番号などの追加情報の入力を求める設定画面に移動して、Multi-Factor Authentication に登録できるようにします。 予備の電話番号には、補助的な電話番号を指定できます。 サード パーティの OATH トークンには、サード パーティの OATH トークンを指定できます。 |
| ワンタイム バイパスの開始をユーザーに許可する | ユーザーがワンタイム バイパスを開始できるようにします。  この設定を有効にした場合は、ユーザーの次回のサインインで有効になります。 プロンプトのバイパス秒を指定するボックスが表示され、ユーザーは既定値である 300 秒を変更できます。 変更しなかった場合、ワンタイム バイパスは 300 秒間のみ有効です。 |
| ユーザーに認証方法の選択を許可する | ユーザーが主要な連絡方法を指定できるようにします。  電話、テキスト メッセージ、モバイル アプリ、または OATH トークンを指定できます。 |
| ユーザーに言語の選択を許可する | 電話、テキスト メッセージ、モバイル アプリ、または OATH トークンに使用される言語をユーザーが変更できるようにします。 |
| ユーザーにモバイル アプリのアクティブ化を許可する | サーバーで使用されるモバイル アプリのアクティブ化プロセスを完了するためのアクティブ化コードをユーザーが生成できるようにします。  アプリをアクティブ化できるデバイスの数 (1 ～ 10) を設定することもできます。 |
| 代替認証にセキュリティの質問を使用する | 2 段階認証が失敗した場合に、セキュリティの質問を使用できます。  正解する必要があるセキュリティの質問の数を指定できます。 |
| ユーザーにサード パーティ製の OATH トークンの関連付けを許可する | ユーザーがサード パーティの OATH トークンを指定できるようにします。 |
| 代替認証に OATH トークンを使用する | 2 段階認証が失敗した場合に、OATH トークンを使用できるようにします。 セッションのタイムアウトを分単位で指定することもできます。 |
| ログの有効化 | ユーザー ポータルでログを有効にします。 ログ ファイルは C:\Program Files\Multi-Factor Authentication Server\Logs に配置されます。 |

これらの設定の大部分は、有効になったユーザーがユーザー ポータルにサインインすると表示されます。

![ユーザー ポータル設定](./media/multi-factor-authentication-get-started-portal/portalsettings.png)

### <a name="to-configure-the-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>Azure Multi-Factor Authentication Server のユーザー ポータル設定を構成するには
1. Azure Multi-Factor Authentication Server で、**[ユーザー ポータル]** アイコンをクリックします。 [設定] タブで、**[ユーザー ポータル URL]** ボックスにユーザー ポータルの URL を入力します。 電子メール機能が有効な場合、この URL は、ユーザーが Azure Multi-Factor Authentication Server にインポートされるときに送信される電子メールに含まれます。
2. ユーザー ポータルで使用する設定を選択します。 たとえば、ユーザーによる認証方法の制御を許可する場合は、ユーザーが選択できる方法と共に、**[ユーザーに認証方法の選択を許可する]** をオンにします。
3. 表示される設定について理解するには、右上隅の **[ヘルプ]** リンクをクリックします。

<center>![セットアップ](./media/multi-factor-authentication-get-started-portal/config.png)</center>


## <a name="administrators-tab"></a>[管理者] タブ
このタブでは、管理者特権を持つユーザーを追加できます。  管理者を追加するときに、管理者が受け取るアクセス許可を微調整できます。 **[追加]** ボタンをクリックし、ユーザーとアクセス許可を選択して、**[追加]** をクリックします。

![ユーザー ポータル管理者](./media/multi-factor-authentication-get-started-portal/admin.png)

## <a name="security-questions"></a>セキュリティの質問
このタブでは、**[代替認証にセキュリティの質問を使用する]** オプションが選択されている場合にユーザーが回答する必要があるセキュリティの質問を指定できます。  Azure Multi-Factor Authentication Server には、既定の質問が用意されています。 質問の順序を変更したり、独自の質問を追加したりできます。  独自の質問を追加する場合は、質問の表示に使用する言語も指定できます。

![ユーザー ポータルのセキュリティの質問](./media/multi-factor-authentication-get-started-portal/secquestion.png)

## <a name="saml"></a>SAML
このタブを使用して、SAML を使用する ID プロバイダーからの要求を受け入れるようにユーザー ポータルを構成します。  タイムアウト セッション、検証証明書、ログアウト リダイレクト URL を指定できます。

![SAML](./media/multi-factor-authentication-get-started-portal/saml.png)

## <a name="trusted-ips"></a>信頼される IP
このタブでは、追加できる単一の IP アドレスまたは IP アドレス範囲を指定できます。これにより、指定した IP アドレスの 1 つからサインインしたユーザーは 2 段階認証を完了する必要がなくなります。

![ユーザー ポータルの信頼できる IP](./media/multi-factor-authentication-get-started-portal/trusted.png)

## <a name="self-service-user-enrollment"></a>セルフサービス ユーザー登録
ユーザーにサインインと登録を許可するには、[設定] タブで **[ユーザーにログインを許可する]** オプションと **[ユーザー登録を許可する]** オプションを選択する必要があります。 選択した設定は、ユーザーのサインイン エクスペリエンスに影響することに注意してください。

たとえば、ユーザーが初めてユーザー ポータルにサインインすると、Azure Multi-Factor Authentication のユーザー設定ページが表示されます。  Azure Multi-Factor Authentication の構成方法によっては、ユーザーは自分の認証方法を選択できる場合があります。  

ユーザーが認証方法として音声通話を選択した場合、またはそのように事前に構成されている場合は、ページに代表電話番号と内線番号 (該当する場合) を入力します。  予備の電話番号を入力できる場合もあります。  

![ユーザー ポータルの信頼できる IP](./media/multi-factor-authentication-get-started-portal/backupphone.png)

ユーザーの認証時に PIN を使用する必要がある場合は、PIN も入力します。  電話番号と PIN (該当する場合) を入力した後、**[今すぐ電話で認証]** ボタンをクリックします。  Azure Multi-Factor Authentication が、ユーザーの代表電話番号に対して電話による認証を実行します。  ユーザーは、自己登録プロセスの次の手順に進むには、電話に応答し、PINを入力する (該当する場合) 必要があります。   

ユーザーが認証方法としてテキスト メッセージを選択した場合、またはそのように事前に構成されている場合は、ページに携帯電話の番号を入力します。  ユーザーの認証時に PIN を使用する必要がある場合は、PIN も入力します。  電話番号と PIN (該当する場合) を入力した後、**[今すぐテキスト メッセージで認証]** ボタンをクリックします。  Azure Multi-Factor Authentication が、ユーザーの携帯電話に対して SMS 認証を実行します。  ユーザーはワンタイム パスコード (OTP) を含むテキスト メッセージを受信し、その OTP と PIN (該当する場合) を使用してそのメッセージに返信します。

![ユーザー ポータルの SMS](./media/multi-factor-authentication-get-started-portal/text.png)   

ユーザーが認証方法としてモバイル アプリを選択した場合、ユーザーのデバイスに Microsoft Authenticator アプリをインストールしてアクティブ化コードを生成するよう求めるメッセージがページに表示されます。  アプリをインストールしたら、[アクティブ化コードの生成] ボタンをクリックします。    

> [!NOTE]
> Microsoft Authenticator アプリを使用するには、ユーザーのデバイスのプッシュ通知を有効にする必要があります。

アクティブ化コード、URL、およびバーコード画像がページに表示されます。  ユーザーの認証時に PIN を使用する必要がある場合は、PIN も入力します。  Microsoft Authenticator アプリにアクティブ化コードと URL を入力するか、バーコード スキャナーを使用してバーコード画像をスキャンした後、[アクティブにする] ボタンをクリックします。    

アクティブ化が完了したら、**[今すぐ認証]** ボタンをクリックします。  Azure Multi-Factor Authentication がユーザーのモバイル アプリに対して認証を実行します。  ユーザーは、自己登録プロセスの次の手順に進むには、モバイル アプリに PINを入力し (該当する場合)、[認証] ボタンを押す必要があります。  

管理者がセキュリティの質問と回答を収集するように Azure Multi-Factor Authentication Server を構成している場合は [セキュリティの質問] ページが表示されます。  ユーザーは、4 つのセキュリティの質問を選択し、選択した質問への回答を入力する必要があります。    

![ユーザー ポータルのセキュリティの質問](./media/multi-factor-authentication-get-started-portal/secq.png)  

これで、ユーザーの自己登録が完了し、ユーザーはユーザー ポータルにサインインします。  管理者によって許可されている場合、ユーザーは、今後いつでもユーザー ポータルに戻って、電話番号、PIN、認証方法、セキュリティの質問を変更できます。


