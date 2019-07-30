---
title: Windows Server での Azure MFA Server と AD FS - Azure Active Directory
description: この記事では、Azure Multi-Factor Authentication と Windows Server 2012 R2 および 2016 の AD FS の使用を開始する方法について説明します。
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
ms.openlocfilehash: 4b38918dc6b80539ef8852aa408cda501958c9b1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057445"
---
# <a name="configure-azure-multi-factor-authentication-server-to-work-with-ad-fs-in-windows-server"></a>Windows Server の AD FS と連携するように Azure Multi-Factor Authentication Server を構成する

Active Directory フェデレーション サービス (AD FS) を使用しており、クラウドまたはオンプレミスのリソースをセキュリティで保護したい場合には、Azure Multi-Factor Authentication Server を構成し、AD FS と連携させることができます。 この構成を行うことで、重要なエンドポイントの 2 段階認証がトリガーされます。

この記事では、Azure Multi-Factor Authentication Server と Windows Server 2012 R2 または Windows Server 2016 の AD FS の使用について説明します。 詳細については、「 [Azure Multi-Factor Authentication Server と AD FS 2.0 を使用したクラウドおよびオンプレミスのリソースのセキュリティ保護](howto-mfaserver-adfs-2.md)」を参照してください。

> [!IMPORTANT]
> 2019 年 7 月 1 日より、Microsoft では新しいデプロイの MFA Server が提供されなくなります。 ユーザーからの多要素認証が必要な新しいお客様は、クラウドベースの Azure Multi-Factor Authentication を使用する必要があります。 7 月 1 日より前に MFA Server をアクティブ化した既存のお客様は、最新バージョンの今後の更新プログラムをダウンロードし、アクティブ化資格情報を通常どおり生成することができます。

## <a name="secure-windows-server-ad-fs-with-azure-multi-factor-authentication-server"></a>Windows Server AD FS を Azure Multi-Factor Authentication Server でセキュリティ保護する

Azure Multi-Factor Authentication Server をインストールする際、次のオプションがあります。

* AD FS と同じサーバーに Azure Multi-Factor Authentication Server をローカル インストールする
* AD FS サーバーに Azure Multi-Factor Authentication アダプターをローカル インストールし、Multi-Factor Authentication Server を別のコンピューターにインストールする

開始する前に、次の情報に注意してください。

* AD FS サーバーに Azure Multi-Factor Authentication Server をインストールする必要はありません。 ただし、AD FS 用の Multi-Factor Authentication アダプターを、AD FS が実行されている Windows Server 2012 R2 または Windows Server 2016 にインストールする必要があります。 このサーバーは、AD FS アダプターを AD FS フェデレーション サーバーに別個にインストールすれば、別のコンピューターにインストールすることができます。 アダプターを別個にインストールする方法については、次の手順を参照してください。
* 所属する組織がテキスト メッセージやモバイル アプリの認証方法を使用している場合、[会社の設定] に定義された文字列にはプレースホルダー <$*application_name*$> が含まれます。 MFA Server v7.1 では、このプレースホルダーを置き換えるアプリケーション名を指定できます。 v7.0 以前では、このプレースホルダーは、AD FS アダプターを使用する際に自動的に置き換えられません。 これらの以前のバージョンでは、AD FS をセキュリティ保護するときに、適切な文字列からプレースホルダーを削除してください。
* サインインに使用するアカウントには、Active Directory サービスでセキュリティ グループを作成するためのユーザー権限が付与されている必要があります。
* Multi-Factor Authentication AD FS アダプターのインストール ウィザードでは、Active Directory のインスタンスに PhoneFactor Admins というセキュリティ グループが作成されます。 その後、フェデレーション サービスの AD FS サービス アカウントがこのグループに追加されます。 PhoneFactor Admins グループがドメイン コントローラー上に作成されていることと、AD FS サービス アカウントがそのグループのメンバーであることを確認します。 必要に応じて、ドメイン コントローラーで AD FS サービス アカウントを PhoneFactor Admins グループに手動で追加します。
* ユーザー ポータルを使用した Web サービス SDK のインストールについては、「[Azure Multi-Factor Authentication Server のユーザー ポータルのデプロイ](howto-mfaserver-deploy-userportal.md)」を参照してください。

### <a name="install-azure-multi-factor-authentication-server-locally-on-the-ad-fs-server"></a>Azure Multi-Factor Authentication Server を AD FS サーバーにローカル インストールする

1. AD FS サーバーに Azure Multi-Factor Authentication Server をダウンロードしてインストールします。 インストール情報については、「 [Azure Multi-Factor Authentication Server の概要](howto-mfaserver-deploy.md)」を参照してください。
2. Azure Multi-Factor Authentication Server 管理コンソールで、 **[AD FS]** アイコンをクリックします。 **[ユーザー登録を許可する]** および **[ユーザーに認証方法の選択を許可する]** オプションを選択します。
3. 対象の組織について指定するオプションが他にあれば、それを選択します。
4. **[AD FS アダプターのインストール]** をクリックします。

   ![MFA Server コンソールから AD FS アダプターをインストールする](./media/howto-mfaserver-adfs-2012/server.png)

5. [Active Directory] ウィンドウが表示される場合には、 コンピューターがドメインに参加しておらず、AD FS アダプターと Multi-Factor Authentication サービスの間の通信をセキュリティで保護するための Active Directory 構成が完了していないことを意味します。 **[次へ]** をクリックしてこの構成を自動的に完了するか、 **[Active Directory の自動構成をスキップして手動で設定を構成する]** チェック ボックスをオンにします。 **[次へ]** をクリックします。
6. [ローカル グループ] ウィンドウが表示される場合は、 コンピューターがドメインに参加しておらず、AD FS アダプターと Multi-Factor Authentication サービスの間の通信をセキュリティで保護するためのローカル グループ構成が完了していないことを意味します。 **[次へ]** をクリックしてこの構成を自動的に完了するか、 **[ローカル グループの自動構成をスキップして手動で設定を構成する]** チェック ボックスをオンにします。 **[次へ]** をクリックします。
7. インストール ウィザードで **[次へ]** をクリックします。 Azure Multi-Factor Authentication Server によって PhoneFactor Admins グループが作成され、AD FS サービス アカウントがその PhoneFactor Admins グループに追加されます。
8. **[インストーラーの起動]** ページで **[次へ]** をクリックします。
9. Multi-Factor Authentication AD FS アダプター インストーラーで、 **[次へ]** をクリックします。
10. インストールが完了したら、 **[閉じる]** をクリックします。
11. アダプターのインストールが完了したら、AD FS に登録する必要があります。 Windows PowerShell を開き、次のコマンドを実行します。

    `C:\Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1`

12. 新しく登録されたアダプターを使用するために、AD FS のグローバル認証ポリシーを編集します。 AD FS 管理コンソールで、 **認証ポリシー** ノードに移動します。 **[Multi-Factor Authentication]** セクションで、 **[グローバル設定]** セクションの横にある **[編集]** リンクをクリックします。 **[グローバル認証ポリシーの編集]** ウィンドウで、**Multi-Factor Authentication** を追加の認証方式として選択し、 **[OK]** をクリックします。 アダプターが WindowsAzureMultiFactorAuthentication として登録されます。 登録を有効にするには、AD FS サービスを再起動する必要があります。

![グローバル認証ポリシーを編集する](./media/howto-mfaserver-adfs-2012/global.png)

この時点で、Multi-Factor Authentication Server は、AD FS と共に使用する追加の認証プロバイダーとして設定されます。

## <a name="install-a-standalone-instance-of-the-ad-fs-adapter-by-using-the-web-service-sdk"></a>Web サービス SDK を使用して AD FS アダプターのスタンドアロン インスタンスをインストールする

1. Multi-Factor Authentication Server を実行しているサーバーに Web サービス SDK をインストールします。
2. \Program Files\Multi-Factor Authentication Server ディレクトリから、AD FS アダプターのインストール先となる予定のサーバーに次のファイルをコピーします。
   * MultiFactorAuthenticationAdfsAdapterSetup64.msi
   * Register-MultiFactorAuthenticationAdfsAdapter.ps1
   * Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
   * MultiFactorAuthenticationAdfsAdapter.config
3. MultiFactorAuthenticationAdfsAdapterSetup64.msi インストール ファイルを実行します。
4. Multi-Factor Authentication AD FS アダプター インストーラーで **[次へ]** をクリックしてインストールを開始します。
5. インストールが完了したら、 **[閉じる]** をクリックします。

## <a name="edit-the-multifactorauthenticationadfsadapterconfig-file"></a>MultiFactorAuthenticationAdfsAdapter.config ファイルを編集する

次の手順に従って、MultiFactorAuthenticationAdfsAdapter.config ファイルを編集します。

1. **UseWebServiceSdk** ノードを **true** に設定します。  
2. **WebServiceSdkUrl** の値を、Multi-Factor Authentication Web サービス SDK の URL に設定します。 例: *https:\/\/contoso.com/\<certificatename>/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx* (ここで、 *\<certificatename>* にはご自分の証明書の名前が入ります)。  
3. Register-MultiFactorAuthenticationAdfsAdapter.ps1 スクリプトを編集します。具体的には、`-ConfigurationFilePath &lt;path&gt;` を `Register-AdfsAuthenticationProvider` コマンドの末尾に追加します。このときの *&lt;パス&gt;* は、MultiFactorAuthenticationAdfsAdapter.config ファイルへの完全なパスです。

### <a name="configure-the-web-service-sdk-with-a-username-and-password"></a>Web サービス SDK をユーザー名とパスワードを使って構成する

Web サービス SDK の構成には、2 つの選択肢があります。 1 つ目はユーザー名とパスワードを使用する方法で、2 つ目はクライアント証明書を使用する方法です。 最初の方法の場合は次の手順に従い、2 つ目の方法の場合はスキップしてください。  

1. **WebServiceSdkUsername** の値を、PhoneFactor Admins セキュリティ グループのメンバーであるアカウントに設定します。 &lt;ドメイン&gt;&#92;&lt;ユーザー名&gt; の形式を使用します。  
2. **WebServiceSdkPassword** の値を、該当するアカウント パスワードに設定します。

### <a name="configure-the-web-service-sdk-with-a-client-certificate"></a>Web サービス SDK をクライアント証明書を使って構成する

ユーザー名とパスワードを使用しない場合は、次の手順に従って、クライアント証明書で Web サービス SDK を構成します。

1. Web サービス SDK を実行しているサーバーの証明機関からクライアント証明書を取得します。 方法については、 [クライアント証明書の取得](https://technet.microsoft.com/library/cc770328.aspx)に関するページを参照してください。  
2. クライアント証明書を、Web サービス SDK を実行しているサーバー上のローカル コンピューターの個人用証明書ストアにインポートします。 証明機関の公開証明書が、信頼されたルート証明書の証明書ストアにあることを確認します。  
3. クライアント証明書の公開キーと秘密キーを .pfx ファイルにエクスポートします。  
4. 公開キーを Base64 形式で .cer ファイルにエクスポートします。  
5. サーバー マネージャーで、Web Server (IIS)\Web Server\Security\IIS Client Certificate Mapping Authentication 機能がインストールされていることを確認します。 インストールされていない場合は、 **[役割と機能の追加]** を選択して、この機能を追加します。  
6. IIS マネージャーで、Web サービス SDK 仮想ディレクトリが含まれている Web サイトの **[構成エディター]** をダブルクリックします。 仮想ディレクトリではなく、Web サイトを選択することが重要です。  
7. **system.webServer/security/authentication/iisClientCertificateMappingAuthentication** セクションに移動します。  
8. [enabled] を **true** に設定します。  
9. [oneToOneCertificateMappingsEnabled] を **true** に設定します。  
10. [oneToOneMappings] の横にある **[...]** ボタンをクリックし、 **[追加]** リンクをクリックします。  
11. 前にエクスポートした Base64 .cer ファイルを開きます。 *-----BEGIN CERTIFICATE-----* 、 *-----END CERTIFICATE-----* 、およびすべての改行を削除します。 結果の文字列をコピーします。  
12. 証明書を、前の手順でコピーした文字列に設定します。  
13. [enabled] を **true** に設定します。  
14. [userName] を、PhoneFactor Admins セキュリティ グループのメンバーであるアカウントに設定します。 &lt;ドメイン&gt;&#92;&lt;ユーザー名&gt; の形式を使用します。  
15. パスワードとして適切なアカウント パスワードを設定して、構成エディターを閉じます。  
16. **[適用]** リンクをクリックします。  
17. Web サービス SDK 仮想ディレクトリで、 **[認証]** をダブルクリックします。  
18. [ASP.NET 偽装] と [基本認証] が **[有効]** に設定され、他のすべての項目が **[無効]** に設定されていることを確認します。  
19. Web サービス SDK 仮想ディレクトリで、 **[SSL 設定]** をダブルクリックします。  
20. [クライアント証明書] を **[受理]** に設定し、 **[適用]** をクリックします。  
21. 前にエクスポートした .pfx ファイルを、AD FS アダプターを実行しているサーバーにコピーします。  
22. .pfx ファイルをローカル コンピューターの個人用証明書ストアにインポートします。  
23. 右クリックして **[秘密キーの管理]** を選択し、AD FS サービスへのサインインに使用するアカウントに読み取りアクセス権を付与します。  
24. クライアント証明書を開いて、 **[詳細]** タブの拇印をコピーします。  
25. MultiFactorAuthenticationAdfsAdapter.config ファイルの **[WebServiceSdkCertificateThumbprint]** に、前の手順でコピーした文字列を設定します。  

最後に、アダプターを登録するために、\Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1 スクリプトを PowerShell で実行します。 アダプターが WindowsAzureMultiFactorAuthentication として登録されます。 登録を有効にするには、AD FS サービスを再起動する必要があります。

## <a name="secure-azure-ad-resources-using-ad-fs"></a>AD FS を使って Azure AD リソースのセキュリティを確保する

クラウド リソースをセキュリティで保護するには、ユーザーが 2 段階認証の実行に成功したときに、Active Directory フェデレーション サービスが multipleauthn 要求を出力するよう要求規則を設定します。 この要求は、Azure AD に渡されます。 以下では、その手順を説明します。

1. AD FS 管理を開きます。
2. 左側で、 **[証明書利用者信頼]** を選択します。
3. **[Microsoft Office 365 ID プラットフォーム]** を右クリックし、 **[要求規則の編集…]** を選択します

   ![AD FS コンソールで要求規則を編集する](./media/howto-mfaserver-adfs-2012/trustedip1.png)

4. [発行変換規則] で、 **[規則の追加]** をクリックします。

   ![AD FS コンソールで変換規則を編集する](./media/howto-mfaserver-adfs-2012/trustedip2.png)

5. 変換要求規則追加ウィザードで、ドロップダウンから **[入力方向の要求をパス スルーまたはフィルター処理]** を選択し、 **[次へ]** をクリックします。

   ![変換要求規則の追加ウィザード](./media/howto-mfaserver-adfs-2012/trustedip3.png)

6. 規則に名前を付けます。
7. 受信要求の種類として **[認証方法の参照]** を選択します。
8. **[すべての要求値をパススルーする]** を選択します。

    ![Add Transform Claim Rule Wizard](./media/howto-mfaserver-adfs-2012/configurewizard.png)

9. **[完了]** をクリックします。 AD FS 管理コンソールを閉じます。

## <a name="troubleshooting-logs"></a>トラブルシューティング ログ

MFA Server の AD FS アダプターの問題をトラブルシューティングするには、以下の手順で追加のログを有効にすると役に立ちます。

1. MFA Server インターフェイスで AD FS セクションを開き、 **[ログを有効にする]** をオンにします。
2. 各 AD FS サーバーで、**regedit.exe** を使用して文字列値レジストリ キー `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Positive Networks\PhoneFactor\InstallPath` を作成し、値を `C:\Program Files\Multi-Factor Authentication Server\` (または他の任意のディレクトリ) に設定します。  **末尾の円記号が重要であることに注意してください。**
3. `C:\Program Files\Multi-Factor Authentication Server\Logs` ディレクトリ (または**手順 2.** で指定した他のディレクトリ内のディレクトリ) を作成します。
4. Logs ディレクトリに対する変更アクセス許可を AD FS サービス アカウントに付与します。
5. AD FS サービスを再起動します。
6. Logs ディレクトリに `MultiFactorAuthAdfsAdapter.log` ファイルが作成されていることを確認します。

## <a name="related-topics"></a>関連トピック

トラブルシューティングのヘルプについては、「 [Azure Multi-Factor Authentication についてよく寄せられる質問 (FAQ)](multi-factor-authentication-faq.md)
