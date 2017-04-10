---
title: "Azure AD アプリケーション プロキシ用 PingAccess | Microsoft Docs"
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
ms.date: 03/21/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: 173607c481d0ba7ceece6310fcd131ff622a0677
ms.lasthandoff: 03/23/2017

---

# <a name="publish-applications-that-support-header-based-authentication-with-pingaccess-for-azure-ad"></a>PingAccess for Azure AD を使ってヘッダーベースの認証に対応したアプリケーションを発行する

Azure Active Directory アプリケーション プロキシと PingAccess の連携によって、Azure Active Directory でアクセスできるアプリケーションの幅が一層広がっています。 PingAccess は[既存のアプリケーション プロキシ サービス](active-directory-application-proxy-get-started.md)を拡張し、ヘッダーを使って認証を行うアプリケーションへのリモート アクセスに対応します。 

## <a name="what-is-pingaccess-for-azure-ad"></a>PingAccess for Azure AD とは

ヘッダーを使って認証を行うアプリへのアクセスをユーザーに提供するためには、アプリケーション プロキシと PingAccess の両方に、リモート アクセスの対象アプリを発行します。 これらのアプリは、アプリケーション プロキシによって他のアプリと同じように扱われます。Azure AD を使ってアクセスが認証され、コネクタ サービスを通じてトラフィックが受け渡されます。 PingAccess は、アプリケーションが読み取り可能な形式で認証を受け取れるよう、アプリケーションの手前で Azure AD からのアクセス トークンをヘッダーに変換します。 

社内のアプリにサインインするユーザーからは、何が起こっているかは見えません。 ユーザーはいつもどおり、任意のデバイスでどこからでも作業を行うことができます。 ユーザーが職場にいる場合は、アプリケーション プロキシも PingAccess もトラフィックを遮断することはないため、ユーザーはいつもと同じエクスペリエンスを得ます。

アプリケーション プロキシ コネクタは、その認証の種類に関係なくすべてのアプリへのリモート トラフィックを誘導します。そのため負荷分散も引き続き自動的に行われます。 

## <a name="how-do-i-get-access"></a>利用方法

このシナリオは Azure Active Directory と PingAccess の連携によって実現されるため、その両方のサービスのライセンスが必要となります。 Azure Active Directory Premium サブスクリプションには、PingAccess のスターター ライセンスが付属します。このフローで最大 20 個のアプリケーションを構成することが可能です。 

詳細については、「 [Azure Active Directory のエディション](active-directory-editions.md)」をご覧ください。

## <a name="publish-your-first-application"></a>最初のアプリケーションの発行

この記事は、このシナリオで初めてアプリを発行する方を対象としています。 発行手順に加え、アプリケーションと PingAccess の基本的な利用方法についても説明しています。 既に両方のサービスの構成が済んでいて、単に発行手順を再確認したいということであれば、2 つの登録セクションは読み飛ばしてかまいません。

>[!NOTE]
>このシナリオは Azure AD と PingAccess を連携させることが前提となるため、一部の手順は Ping Identity のサイトでご覧ください。 

### <a name="install-an-application-proxy-connector"></a>アプリケーション プロキシ コネクタのインストール

既にアプリケーション プロキシを有効にしていて、コネクタがインストールされている場合は、次の手順を省略できます。

アプリケーション プロキシ コネクタは、遠隔にいる従業員からのトラフィックを発行済みアプリに誘導する Windows Server サービスです。 詳しいインストール手順については、「[Azure Portal でアプリケーション プロキシを有効にする](active-directory-application-proxy-enable.md)」を参照してください。

1. [Azure Portal](https://portal.azure.com) にグローバル管理者としてサインインします。 
2. **[Azure Active Directory]** > **[アプリケーション プロキシ]** の順に選択します。
3. **[Download Connector (コネクタのダウンロード)]** を選択してアプリケーション プロキシ コネクタのダウンロードを開始します。 画面の指示に従ってインストールしてください。 
4. 通常、コネクタをダウンロードすれば、ディレクトリに対してアプリケーション プロキシが自動的に有効になります。有効にならなかった場合は、**[アプリケーション プロキシの有効化]** を選択してください。 

![アプリケーション プロキシの有効化とコネクタのダウンロード](./media/application-proxy-ping-access/install-connector.png)

### <a name="add-your-app-to-azure-ad-with-application-proxy"></a>アプリケーション プロキシを使って Azure AD にアプリを登録する

このセクションは、2 つの作業に分かれています。 まず、Azure AD にアプリを発行する必要があります。 次に、アプリに関する情報をいくつか収集する必要があります。その情報が PingAccess の手順で必要になります。 

#### <a name="publish-the-app"></a>アプリの発行

1. 直前のセクションの手順を省略した場合は、[Azure Portal](https://portal.azure.com) にグローバル管理者としてサインインしてください。 
2. **[Azure Active Directory]** > **[エンタープライズ アプリケーション]** の順に選択します。 
3. ブレード上部の **[追加]** を選択します。 
4. **[オンプレミスのアプリケーション]** を選択します。
5. 新しいアプリに関する情報を必須フィールドに入力します。 次のガイダンスに従って設定してください。
  - **[内部 URL]**: 通常は、社内ネットワークにおけるアプリのサインイン ページへの URL を指定します。 このパートナーシップでは、コネクタは PingAccess プロキシをアプリケーションの最初のページとして処理する必要があります。 次の形式を使用します。`https://<host name of your PA server>:<port>/<App path name>` ポートは既定では 3000 ですが、PingAccess で設定できます。
  - **[事前認証方法]**: Azure Active Directory
  - **[ヘッダーの URL を変換する]**: いいえ
6. ブレード下部の **[追加]** を選択します。 アプリケーションが追加されて、クイック スタート メニューが表示されます。 
7. クイック スタート メニューで **[テスト用のユーザーを割り当てる]** を選択し、少なくとも 1 ユーザーをアプリケーションに追加します。 このテスト アカウントでオンプレミスのアプリケーションにアクセスできることを確認します。 
8. **[割り当て]** を選択して、テスト ユーザーの割り当てを保存します。 
9. アプリの管理ブレードで **[シングル サインオン]** を選択します。 
10. ドロップダウン メニューから **[Header-based sign-on (ヘッダーベースのサインオン)]** を選択します。 [ **保存**] を選択します。

  ![ヘッダーに基づくサインオンを選択](./media/application-proxy-ping-access/sso-header.PNG)

11. [エンタープライズ アプリケーション] ブレードを閉じるか、左側いっぱいにスクロールして、Azure Active Directory メニューに戻ります。 
12. **[アプリの登録]** を選択します。
13. 先ほど追加したアプリを選択し、**[応答 URL]** を選択します。 
14. 手順 5. でアプリに割り当てた外部 URL が [応答 URL] の一覧に含まれているかどうかを確認します。 含まれていない場合は、ここで追加してください。 
15. アプリの設定ブレードで **[必要なアクセス許可]** を選択します。 
16. **[追加]**を選択します。 API に **[Windows Azure Active Directory]** を選び、**[選択]** を選択します。 アクセス許可に **[Read and write all applications] (すべてのアプリケーションの読み取りと書き込み)** と **[サインインとユーザー プロファイルの読み取り]** を選び、**[選択]** と **[完了]** を選択します。  

  ![アクセス許可を選択](./media/application-proxy-ping-access/select-permissions.png) 

#### <a name="collect-information-for-the-pingaccess-steps"></a>PingAccess の手順で使う情報の収集

1. 引き続きアプリの設定ブレードで **[プロパティ]** を選択します。 **[アプリケーション ID]** の値を保存します。 PingAccess を構成するときのクライアント ID には、この ID が使用されます。
2. [設定] ブレードで **[キー]** を選択します。 
3. キーの説明を入力し、ドロップダウン メニューから有効期限を選択して、キーを作成します。 
4. [ **保存**] を選択します。 **[値]** フィールドに GUID が表示されます。 

  この値は、このウィンドウを閉じると確認できなくなります。ここで保存してください。 

5. [アプリの登録] ブレードを閉じるか、左側いっぱいにスクロールして、Azure Active Directory メニューに戻ります。
6. **[プロパティ]**を選択します。
7. **[ディレクトリ ID]** の GUID を保存します。 

### <a name="download-pingaccess-and-configure-your-app"></a>PingAccess をダウンロードしてアプリを構成する

このシナリオの PingAccess に関連した領域の詳しい手順については、Ping Identity のサイトで、[PingAccess for Azure AD の構成](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html)に関するドキュメントを参照してください。

記載されている作業手順に従い、PingAccess アカウントを取得して (まだ所有していない場合)、PingAccess サーバーをインストールし、Azure Portal からコピーした Directory ID で Azure AD OIDC プロバイダー接続を作成してください。 そのうえで、アプリケーションの ID とキーの値を使って PingAccess に Web セッションを作成します。 その後、ID のマッピングをセットアップし、仮想ホスト、サイト、アプリケーションを作成することができます。

### <a name="test-your-app"></a>アプリケーションをテストする

以上の手順がすべて完了すれば、アプリは正常に動作します。 正しく動作するか確認するために、ブラウザーを起動し、Azure にアプリを発行したときに作成した外部 URL にアクセスしてみてください。 サインインには、アプリに割り当てたテスト アカウントを使用します。 

## <a name="next-steps"></a>次のステップ

- [アプリケーション プロキシのトラブルシューティング](active-directory-application-proxy-troubleshoot.md)

