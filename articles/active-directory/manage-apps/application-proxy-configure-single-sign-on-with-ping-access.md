---
title: Azure AD アプリケーション プロキシ用 PingAccess を使用したヘッダーベースの認証 | Microsoft Docs
description: ヘッダーベースの認証に対応するには、PingAccess とアプリケーション プロキシを使ってアプリケーションを発行します。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/11/2017
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8eef15098eed8959655ae2904bf41a8c3dffc9f4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58882792"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>アプリケーション プロキシと PingAccess を使用したシングル サインオン用のヘッダーベースの認証

Azure Active Directory アプリケーション プロキシと PingAccess の連携によって、Azure Active Directory でアクセスできるアプリケーションの幅が一層広がっています。 PingAccess は[既存のアプリケーション プロキシ サービス](application-proxy.md)を拡張し、ヘッダーを使って認証を行うアプリケーションへのシングル サインオン アクセスに対応します。

## <a name="what-is-pingaccess-for-azure-ad"></a>PingAccess for Azure AD とは

PingAccess for Azure Active Directory は、認証用のヘッダーを使用するアプリケーションにユーザーがシングル サインオンでアクセスできるようにする PingAccess のサービスです。 これらのアプリは、アプリケーション プロキシによって他のアプリと同じように扱われます。Azure AD を使ってアクセスが認証され、コネクタ サービスを通じてトラフィックが受け渡されます。 PingAccess は、アプリケーションが読み取り可能な形式で認証を受け取れるよう、アプリケーションの手前で Azure AD からのアクセス トークンをヘッダーに変換します。

社内のアプリにサインインするユーザーからは、何が起こっているかは見えません。 ユーザーはいつもどおり、任意のデバイスでどこからでも作業を行うことができます。 

アプリケーション プロキシ コネクタは、その認証の種類に関係なくすべてのアプリへのリモート トラフィックを誘導します。そのため負荷分散も引き続き自動的に行われます。

## <a name="how-do-i-get-access"></a>利用方法

このシナリオは Azure Active Directory と PingAccess の連携によって実現されるため、その両方のサービスのライセンスが必要となります。 ただし、Azure Active Directory Premium サブスクリプションには、最大 20 のアプリケーションをカバーする基本的な PingAccess ライセンスが含まれています。 20 を超えるヘッダー ベースのアプリケーションを公開する必要がある場合は、PingAccess から追加のライセンスを購入できます。 

詳細については、「 [Azure Active Directory のエディション](../fundamentals/active-directory-whatis.md)」をご覧ください。

## <a name="publish-your-application-in-azure"></a>アプリケーションを Azure に発行する

この記事は、このシナリオで初めてアプリを発行する方を対象としています。 発行手順に加え、アプリケーションと PingAccess の基本的な利用方法についても説明しています。 両方のサービスは構成済みであり、公開手順の再確認が目的の場合は、コネクタのインストールをスキップして「[アプリケーション プロキシを使って Azure AD にアプリを登録する](#add-your-app-to-azure-ad-with-application-proxy)」に進むことができます。

>[!NOTE]
>このシナリオは Azure AD と PingAccess を連携させることが前提となるため、一部の手順は Ping Identity のサイトでご覧ください。

### <a name="install-an-application-proxy-connector"></a>アプリケーション プロキシ コネクタのインストール

既にアプリケーション プロキシを有効にし、コネクタをインストールしている場合は、このセクションをスキップして「[アプリケーション プロキシを使って Azure AD にアプリを登録する](#add-your-app-to-azure-ad-with-application-proxy)」に進むことができます。

アプリケーション プロキシ コネクタは、遠隔にいる従業員からのトラフィックを発行済みアプリに誘導する Windows Server サービスです。 詳しいインストール手順については、「[Azure Portal でアプリケーション プロキシを有効にする](application-proxy-add-on-premises-application.md)」を参照してください。

1. アプリケーション管理者として [Azure portal](https://portal.azure.com) にサインインします。
2. **[Azure Active Directory]** > **[アプリケーション プロキシ]** の順に選択します。
3. **[Download Connector (コネクタのダウンロード)]** を選択してアプリケーション プロキシ コネクタのダウンロードを開始します。 画面の指示に従ってインストールしてください。

   ![アプリケーション プロキシの有効化とコネクタのダウンロード](./media/application-proxy-configure-single-sign-on-with-ping-access/install-connector.png)

4. 通常、コネクタをダウンロードすれば、ディレクトリに対してアプリケーション プロキシが自動的に有効になります。有効にならなかった場合は、**[アプリケーション プロキシの有効化]** を選択してください。


### <a name="add-your-app-to-azure-ad-with-application-proxy"></a>アプリケーション プロキシを使って Azure AD にアプリを登録する

Azure ポータルで実行する必要がある操作は 2 つあります。 最初に、アプリケーション プロキシを使用してアプリケーションを発行する必要があります。 次に、アプリに関する情報をいくつか収集する必要があります。その情報が PingAccess の手順で必要になります。

アプリを公開するには、次の手順に従います。 手順 1 ～ 8 の詳細については、「[Azure AD アプリケーション プロキシを使用してアプリケーションを発行する](application-proxy-add-on-premises-application.md)」を参照してください。

1. 直前のセクションの手順を省略した場合は、アプリケーション管理者として [Azure portal](https://portal.azure.com) にサインインしてください。
2. **[Azure Active Directory]** > **[エンタープライズ アプリケーション]** の順に選択します。
3. ブレード上部の **[追加]** を選択します。
4. **[オンプレミスのアプリケーション]** を選択します。
5. 新しいアプリに関する情報を必須フィールドに入力します。 次のガイダンスに従って設定してください。
   - **[内部 URL]**: 通常は、社内ネットワークにおけるアプリのサインイン ページへの URL を指定します。 このシナリオでは、コネクタは PingAccess プロキシをアプリケーションの最初のページとして処理する必要があります。 次の形式を使用します。`https://<host name of your PA server>:<port>` ポートは既定では 3000 ですが、PingAccess で設定できます。

     > [!WARNING]
     > この種の SSO の場合、内部 URL では HTTPS を使用する必要があります。HTTP は使用できません。

   - **事前認証方法**:Azure Active Directory
   - **ヘッダーの URL を変換する**:いいえ 

   >[!NOTE]
   >これが最初のアプリケーションの場合は、ポート 3000 の使用から始めてください。PingAccess 構成を変更した場合は、この設定を更新してください。 2 つ目以降のアプリの場合、PingAccess で構成したリスナーと一致する必要があります。 詳細については、[PingAccess のリスナー](https://documentation.pingidentity.com/pingaccess/pa31/index.shtml#Listeners.html)に関するページを参照してください。

6. ブレード下部の **[追加]** を選択します。 アプリケーションが追加されて、クイック スタート メニューが表示されます。
7. クイック スタート メニューで **[テスト用のユーザーを割り当てる]** を選択し、少なくとも 1 ユーザーをアプリケーションに追加します。 このテスト アカウントでオンプレミスのアプリケーションにアクセスできることを確認します。
8. **[割り当て]** を選択して、テスト ユーザーの割り当てを保存します。
9. アプリの管理ブレードで **[シングル サインオン]** を選択します。
10. ドロップダウン メニューから **[Header-based sign-on (ヘッダーベースのサインオン)]** を選択します。 **[保存]** を選択します。

    >[!TIP]
    >ヘッダー ベースのシングル サインオンを初めて使用する場合は、PingAccess をインストールする必要があります。 Azure サブスクリプションを PingAccess のインストールに自動的に関連付けるには、このシングル サインオン ページのリンクを使用して PingAccess をダウンロードします。 今すぐダウンロード サイトを開くことも、後でこのページに戻ることもできます。 

    ![ヘッダーに基づくサインオンを選択](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.PNG)

11. [エンタープライズ アプリケーション] ブレードを閉じるか、左側いっぱいにスクロールして、Azure Active Directory メニューに戻ります。
12. **[アプリの登録]** を選択します。

    ![[アプリの登録] を選択する](./media/application-proxy-configure-single-sign-on-with-ping-access/app-registrations.png)

13. 先ほど追加したアプリを選択し、**[応答 URL]** を選択します。

    ![[応答 URL] を選択する](./media/application-proxy-configure-single-sign-on-with-ping-access/reply-urls.png)

14. 手順 5. でアプリに割り当てた外部 URL が [応答 URL] の一覧に含まれているかどうかを確認します。 含まれていない場合は、ここで追加してください。
15. アプリの設定ブレードで **[必要なアクセス許可]** を選択します。

    ![[必要なアクセス許可] を選択する](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

16. **[追加]** を選択します。 API に **[Windows Azure Active Directory]** を選び、**[選択]** を選択します。 アクセス許可に **[Read and write all applications] \(すべてのアプリケーションの読み取りと書き込み)** と **[サインインとユーザー プロファイルの読み取り]** を選び、**[選択]** と **[完了]** を選択します。  

    ![アクセス許可を選択](./media/application-proxy-configure-single-sign-on-with-ping-access/select-permissions.png)

17. アクセス許可を付与してから、アクセス許可画面を閉じてください。 
    ![アクセス許可を付与する](./media/application-proxy-configure-single-sign-on-with-ping-access/grantperms.png)

### <a name="collect-information-for-the-pingaccess-steps"></a>PingAccess の手順で使う情報の収集

1. アプリの設定ブレードで **[プロパティ]** を選択します。 

   ![[プロパティ] を選択する](./media/application-proxy-configure-single-sign-on-with-ping-access/properties.png)

2. **[アプリケーション ID]** の値を保存します。 PingAccess を構成するときのクライアント ID には、この ID が使用されます。
3. [設定] ブレードで **[キー]** を選択します。

   ![[キー] を選択する](./media/application-proxy-configure-single-sign-on-with-ping-access/Keys.png)

4. キーの説明を入力し、ドロップダウン メニューから有効期限を選択して、キーを作成します。
5. **[保存]** を選択します。 **[値]** フィールドに GUID が表示されます。

   この値は、このウィンドウを閉じると確認できなくなります。ここで保存してください。

   ![新しいキーを作成する](./media/application-proxy-configure-single-sign-on-with-ping-access/create-keys.png)

6. [アプリの登録] ブレードを閉じるか、左側いっぱいにスクロールして、Azure Active Directory メニューに戻ります。
7. **[プロパティ]** を選択します。
8. **[ディレクトリ ID]** の GUID を保存します。

### <a name="optional---update-graphapi-to-send-custom-fields"></a>省略可能 - GraphAPI を更新してカスタム フィールドを送信する

Azure AD が認証のために送信するセキュリティ トークンの一覧については、「[Azure AD のトークン リファレンス](../develop/v1-id-and-access-tokens.md)」を参照してください。 他のトークンを送信するカスタム クレームが必要な場合は、Graph エクスプローラー、または Azure Portal 内のアプリケーションのマニフェストを使用して、アプリのフィールド *acceptMappedClaims* を **True** に設定します。    

次の例では、Graph Explorer を使用しています。

```
PATCH https://graph.windows.net/myorganization/applications/<object_id_GUID_of_your_application> 

{
  "acceptMappedClaims":true
}
```
次の例では、[Azure portal](https://portal.azure.com) を使用して *acceptedMappedClaims* フィールドを更新しています。
1. アプリケーション管理者として [Azure portal](https://portal.azure.com) にサインインします。
2. **[Azure Active Directory]** > **[アプリの登録]** の順に選択します。
3. ご使用のアプリケーション > **[マニフェスト]** の順に選択します。
4. **[編集]** を選択し、*acceptedMappedClaims* フィールドを検索して、値を **true** に変更します。
![アプリ マニフェスト](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-ping-access-manifest.PNG)
1. **[保存]** を選択します。

>[!NOTE]
>カスタム要求を使用するには、カスタム ポリシーも定義し、アプリケーションに割り当てる必要があります。  このポリシーには、すべての必要なカスタム属性を含めます。
>
>ポリシーの定義と割り当てには、PowerShell、Azure AD Graph Explorer、または MS Graph を使用できます。  PowerShell でこの処理を実行するには、必要に応じてまず `New-AzureADPolicy` を使用してから、`Set-AzureADServicePrincipalPolicy` でアプリケーションに割り当てます。  詳細については、[Azure AD ポリシーのドキュメント](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment)を参照してください。

### <a name="optional---use-a-custom-claim"></a>省略可能 - カスタム要求を使用する
アプリケーションからカスタム要求を使用し、追加のフィールドを含めるには、[カスタム要求のマップ ポリシーも作成し、そのポリシーをアプリケーションに割り当てる](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment)必要があります。

## <a name="download-pingaccess-and-configure-your-app"></a>PingAccess をダウンロードしてアプリを構成する

これで、Azure Active Directory のすべてのセットアップ手順が完了したので、PingAccess の構成に進むことができます。 

このシナリオの PingAccess に関連した領域の詳しい手順については、Ping Identity のサイトで、[PingAccess for Azure AD の構成](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html)に関するドキュメントを参照してください。

記載されている作業手順に従い、PingAccess アカウントを取得して (まだ所有していない場合)、PingAccess サーバーをインストールし、Azure Portal からコピーした Directory ID で Azure AD OIDC プロバイダー接続を作成してください。 そのうえで、アプリケーションの ID とキーの値を使って PingAccess に Web セッションを作成します。 その後、ID のマッピングをセットアップし、仮想ホスト、サイト、アプリケーションを作成することができます。

### <a name="test-your-app"></a>アプリをテストする

以上の手順がすべて完了すれば、アプリは正常に動作します。 正しく動作するか確認するために、ブラウザーを起動し、Azure にアプリを発行したときに作成した外部 URL にアクセスしてみてください。 サインインには、アプリに割り当てたテスト アカウントを使用します。

## <a name="next-steps"></a>次の手順

- [PingAccess for Azure AD を構成する](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html)
- [Azure AD アプリケーション プロキシを使用したシングル サインオンの提供](application-proxy-single-sign-on.md)
- [アプリケーション プロキシのトラブルシューティング](application-proxy-troubleshoot.md)
