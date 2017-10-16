---
title: "Azure AD アプリケーション プロキシ用 PingAccess を使用したヘッダーベースの認証 | Microsoft Docs"
description: "ヘッダーベースの認証に対応するには、PingAccess とアプリケーション プロキシを使ってアプリケーションを発行します。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 58034ab8830cf655199875b448948ea14dc04a70
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>アプリケーション プロキシと PingAccess を使用したシングル サインオン用のヘッダーベースの認証

Azure Active Directory アプリケーション プロキシと PingAccess の連携によって、Azure Active Directory でアクセスできるアプリケーションの幅が一層広がっています。 PingAccess は[既存のアプリケーション プロキシ サービス](active-directory-application-proxy-get-started.md)を拡張し、ヘッダーを使って認証を行うアプリケーションへのシングル サインオン アクセスに対応します。

## <a name="what-is-pingaccess-for-azure-ad"></a>PingAccess for Azure AD とは

PingAccess for Azure Active Directory は、認証用のヘッダーを使用するアプリケーションにユーザーがシングル サインオンでアクセスできるようにする PingAccess のサービスです。 これらのアプリは、アプリケーション プロキシによって他のアプリと同じように扱われます。Azure AD を使ってアクセスが認証され、コネクタ サービスを通じてトラフィックが受け渡されます。 PingAccess は、アプリケーションが読み取り可能な形式で認証を受け取れるよう、アプリケーションの手前で Azure AD からのアクセス トークンをヘッダーに変換します。

社内のアプリにサインインするユーザーからは、何が起こっているかは見えません。 ユーザーはいつもどおり、任意のデバイスでどこからでも作業を行うことができます。 

アプリケーション プロキシ コネクタは、その認証の種類に関係なくすべてのアプリへのリモート トラフィックを誘導します。そのため負荷分散も引き続き自動的に行われます。

## <a name="how-do-i-get-access"></a>利用方法

このシナリオは Azure Active Directory と PingAccess の連携によって実現されるため、その両方のサービスのライセンスが必要となります。 ただし、Azure Active Directory Premium サブスクリプションには、最大 20 のアプリケーションをカバーする基本的な PingAccess ライセンスが含まれています。 20 を超えるヘッダー ベースのアプリケーションを公開する必要がある場合は、PingAccess から追加のライセンスを購入できます。 

詳細については、「[Azure Active Directory のエディション](active-directory-editions.md)」をご覧ください。

## <a name="publish-your-application-in-azure"></a>アプリケーションを Azure に発行する

この記事は、このシナリオで初めてアプリを発行する方を対象としています。 発行手順に加え、アプリケーションと PingAccess の基本的な利用方法についても説明しています。 両方のサービスは構成済みであり、公開手順の再確認が目的の場合は、コネクタのインストールをスキップして「[アプリケーション プロキシを使って Azure AD にアプリを登録する](#add-your-app-to-Azure-AD-with-Application-Proxy)」に進むことができます。

>[!NOTE]
>このシナリオは Azure AD と PingAccess を連携させることが前提となるため、一部の手順は Ping Identity のサイトでご覧ください。

### <a name="install-an-application-proxy-connector"></a>アプリケーション プロキシ コネクタのインストール

既にアプリケーション プロキシを有効にし、コネクタをインストールしている場合は、このセクションをスキップして「[アプリケーション プロキシを使って Azure AD にアプリを登録する](#add-your-app-to-azure-ad-with-application-proxy)」に進むことができます。

アプリケーション プロキシ コネクタは、遠隔にいる従業員からのトラフィックを発行済みアプリに誘導する Windows Server サービスです。 詳しいインストール手順については、「[Azure Portal でアプリケーション プロキシを有効にする](active-directory-application-proxy-enable.md)」を参照してください。

1. [Azure Portal](https://portal.azure.com) にグローバル管理者としてサインインします。
2. **[Azure Active Directory]** > **[アプリケーション プロキシ]** の順に選択します。
3. **[Download Connector (コネクタのダウンロード)]** を選択してアプリケーション プロキシ コネクタのダウンロードを開始します。 画面の指示に従ってインストールしてください。

   ![アプリケーション プロキシの有効化とコネクタのダウンロード](./media/application-proxy-ping-access/install-connector.png)

4. 通常、コネクタをダウンロードすれば、ディレクトリに対してアプリケーション プロキシが自動的に有効になります。有効にならなかった場合は、**[アプリケーション プロキシの有効化]** を選択してください。


### <a name="add-your-app-to-azure-ad-with-application-proxy"></a>アプリケーション プロキシを使って Azure AD にアプリを登録する

Azure ポータルで実行する必要がある操作は 2 つあります。 最初に、アプリケーション プロキシを使用してアプリケーションを発行する必要があります。 次に、アプリに関する情報をいくつか収集する必要があります。その情報が PingAccess の手順で必要になります。

アプリを公開するには、次の手順に従います。 手順 1 ～ 8 の詳細については、「[Azure AD アプリケーション プロキシを使用してアプリケーションを発行する](application-proxy-publish-azure-portal.md)」を参照してください。

1. 直前のセクションの手順を省略した場合は、[Azure Portal](https://portal.azure.com) にグローバル管理者としてサインインしてください。
2. **[Azure Active Directory]** > **[エンタープライズ アプリケーション]** の順に選択します。
3. ブレード上部の **[追加]** を選択します。
4. **[オンプレミスのアプリケーション]** を選択します。
5. 新しいアプリに関する情報を必須フィールドに入力します。 次のガイダンスに従って設定してください。
   - **[内部 URL]**: 通常は、社内ネットワークにおけるアプリのサインイン ページへの URL を指定します。 このシナリオでは、コネクタは PingAccess プロキシをアプリケーションの最初のページとして処理する必要があります。 次の形式を使用します。`https://<host name of your PA server>:<port>` ポートは既定では 3000 ですが、PingAccess で設定できます。
   - **[事前認証方法]**: Azure Active Directory
   - **[ヘッダーの URL を変換する]**: いいえ

   >[!NOTE]
   >これが最初のアプリケーションの場合は、ポート 3000 の使用から始めてください。PingAccess 構成を変更した場合は、この設定を更新してください。 2 つ目以降のアプリの場合、PingAccess で構成したリスナーと一致する必要があります。 詳細については、[PingAccess のリスナー](https://documentation.pingidentity.com/pingaccess/pa31/index.shtml#Listeners.html)に関するページを参照してください。

6. ブレード下部の **[追加]** を選択します。 アプリケーションが追加されて、クイック スタート メニューが表示されます。
7. クイック スタート メニューで **[テスト用のユーザーを割り当てる]** を選択し、少なくとも 1 ユーザーをアプリケーションに追加します。 このテスト アカウントでオンプレミスのアプリケーションにアクセスできることを確認します。
8. **[割り当て]** を選択して、テスト ユーザーの割り当てを保存します。
9. アプリの管理ブレードで **[シングル サインオン]** を選択します。
10. ドロップダウン メニューから **[Header-based sign-on (ヘッダーベースのサインオン)]** を選択します。 [ **保存**] を選択します。

   >[!TIP]
   >ヘッダー ベースのシングル サインオンを初めて使用する場合は、PingAccess をインストールする必要があります。 Azure サブスクリプションを PingAccess のインストールに自動的に関連付けるには、このシングル サインオン ページのリンクを使用して PingAccess をダウンロードします。 今すぐダウンロード サイトを開くことも、後でこのページに戻ることもできます。 

   ![ヘッダーに基づくサインオンを選択](./media/application-proxy-ping-access/sso-header.PNG)

11. [エンタープライズ アプリケーション] ブレードを閉じるか、左側いっぱいにスクロールして、Azure Active Directory メニューに戻ります。
12. **[アプリの登録]** を選択します。

   ![[アプリの登録] を選択する](./media/application-proxy-ping-access/app-registrations.png)

13. 先ほど追加したアプリを選択し、**[応答 URL]** を選択します。

   ![[応答 URL] を選択する](./media/application-proxy-ping-access/reply-urls.png)

14. 手順 5. でアプリに割り当てた外部 URL が [応答 URL] の一覧に含まれているかどうかを確認します。 含まれていない場合は、ここで追加してください。
15. アプリの設定ブレードで **[必要なアクセス許可]** を選択します。

  ![[必要なアクセス許可] を選択する](./media/application-proxy-ping-access/required-permissions.png)

16. **[追加]**を選択します。 API に **[Windows Azure Active Directory]** を選び、**[選択]** を選択します。 アクセス許可に **[Read and write all applications] \(すべてのアプリケーションの読み取りと書き込み)** と **[サインインとユーザー プロファイルの読み取り]** を選び、**[選択]** と **[完了]** を選択します。  

  ![アクセス許可を選択](./media/application-proxy-ping-access/select-permissions.png)

### <a name="collect-information-for-the-pingaccess-steps"></a>PingAccess の手順で使う情報の収集

1. アプリの設定ブレードで **[プロパティ]** を選択します。 

  ![[プロパティ] を選択する](./media/application-proxy-ping-access/properties.png)

2. **[アプリケーション ID]** の値を保存します。 PingAccess を構成するときのクライアント ID には、この ID が使用されます。
3. [設定] ブレードで **[キー]** を選択します。

  ![[キー] を選択する](./media/application-proxy-ping-access/Keys.png)

4. キーの説明を入力し、ドロップダウン メニューから有効期限を選択して、キーを作成します。
5. [ **保存**] を選択します。 **[値]** フィールドに GUID が表示されます。

  この値は、このウィンドウを閉じると確認できなくなります。ここで保存してください。

  ![新しいキーを作成する](./media/application-proxy-ping-access/create-keys.png)

6. [アプリの登録] ブレードを閉じるか、左側いっぱいにスクロールして、Azure Active Directory メニューに戻ります。
7. **[プロパティ]**を選択します。
8. **[ディレクトリ ID]** の GUID を保存します。

### <a name="optional---update-graphapi-to-send-custom-fields"></a>省略可能 - GraphAPI を更新してカスタム フィールドを送信する

Azure AD が認証のために送信するセキュリティ トークンの一覧については、「[Azure AD のトークン リファレンス](./develop/active-directory-token-and-claims.md)」を参照してください。 他のトークンを送信するカスタム要求が必要な場合は、GraphAPI を使用して、アプリのフィールド *acceptMappedClaims* を**True**に設定します。 この構成を行うには、Azure AD Graph Explorer または MS Graph のいずれかを使用できます。 

次の例では、Graph Explorer を使用しています。

```
PATCH https://graph.windows.net/myorganization/applications/<object_id_GUID_of_your_application> 

{
  "acceptMappedClaims":true
}
```

## <a name="download-pingaccess-and-configure-your-app"></a>PingAccess をダウンロードしてアプリを構成する

これで、Azure Active Directory のすべてのセットアップ手順が完了したので、PingAccess の構成に進むことができます。 

このシナリオの PingAccess に関連した領域の詳しい手順については、Ping Identity のサイトで、[PingAccess for Azure AD の構成](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html)に関するドキュメントを参照してください。

記載されている作業手順に従い、PingAccess アカウントを取得して (まだ所有していない場合)、PingAccess サーバーをインストールし、Azure Portal からコピーした Directory ID で Azure AD OIDC プロバイダー接続を作成してください。 そのうえで、アプリケーションの ID とキーの値を使って PingAccess に Web セッションを作成します。 その後、ID のマッピングをセットアップし、仮想ホスト、サイト、アプリケーションを作成することができます。

### <a name="test-your-app"></a>アプリケーションをテストする

以上の手順がすべて完了すれば、アプリは正常に動作します。 正しく動作するか確認するために、ブラウザーを起動し、Azure にアプリを発行したときに作成した外部 URL にアクセスしてみてください。 サインインには、アプリに割り当てたテスト アカウントを使用します。

## <a name="next-steps"></a>次のステップ

- [PingAccess for Azure AD を構成する](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html)
- [Azure AD アプリケーション プロキシを使用したシングル サインオンの提供](application-proxy-sso-overview.md)
- [アプリケーション プロキシのトラブルシューティング](active-directory-application-proxy-troubleshoot.md)
