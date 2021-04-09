---
title: Azure API Management で Active Directory B2C を使用して SPA バックエンドを保護する
description: PKCE 対応 SPA 認証フローを使用して JavaScript SPA から呼び出される Azure Active Directory B2C、Azure API Management、および Easy Auth を使用することによって、OAuth 2.0 で API を保護します。
services: api-management, azure-ad-b2c, app-service
documentationcenter: ''
author: WillEastbury
manager: alberts
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/18/2021
ms.author: wieastbu
ms.custom: fasttrack-new, fasttrack-update, devx-track-js
ms.openlocfilehash: baa6a0a6995e206924d14de25b98700e450f3a0c
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954923"
---
# <a name="protect-spa-backend-with-oauth-20-azure-active-directory-b2c-and-azure-api-management"></a>OAuth 2.0、Azure Active Directory B2C、および Azure API Management を使用して SPA バックエンドを保護する

このシナリオでは、API を保護するように Azure API Management インスタンスを構成する方法について説明します。
トークンを取得するための Azure AD B2C SPA (認証コード + PKCE) フローと、API Management を使用することで、EasyAuth を使用して Azure Functions バックエンドをセキュリティで保護します。

## <a name="aims"></a>目的

ここでは、Azure Functions と Azure AD B2C を使用した簡略化されたシナリオで API Management を使用する方法を確認します。 Azure AD B2C を使用してユーザーをサインインさせる API を呼び出す JavaScript (JS) アプリを作成します。 次に、API Management の validate-jwt、CORS、および Rate Limit By Key ポリシー機能を使用して、バックエンド API を保護します。

多層防御のために、次に EasyAuth を使用してバックエンド API 内でトークンを再び検証し、API Management が Azure Functions バックエンドを呼び出すことができる唯一のサービスであることを確認します。

## <a name="what-will-you-learn"></a>学習内容

> [!div class="checklist"]
> * Azure Active Directory B2C でのシングル ページ アプリとバックエンド API のセットアップ
> * Azure Functions バックエンド API の作成
> * Azure API Management への Azure Functions API のインポート
> * Azure API Management 内での API の保護
> * Microsoft Identity Platform Library (MSAL.js) を使用した Azure Active Directory B2C 承認エンドポイントの呼び出し
> * HTML/Vanilla JS シングル ページ アプリケーションの格納と Azure Blob Storage エンドポイントからの提供

## <a name="prerequisites"></a>前提条件

この記事の手順を実行するには、以下が必要です。

* フロントエンドの JS シングル ページ アプリをホストする Azure (StorageV2) 汎用 V2 ストレージ アカウント。
* Azure API Management インスタンス ("従量課金" を含むすべてのレベルで動作します。ただし、完全なシナリオに適用される一部の機能はこのレベルでは使用できません (rate-limit-by-key および専用の仮想 IP)。これらの制限については下の記事で適宜注意書きされています)。
* 呼び出された API をホストする空の Azure Function アプリ (従量課金プランで V3.1 .NET Core ランタイムを実行)
* サブスクリプションにリンクされている Azure AD B2C テナント。

実際には、同じリージョン内の運用環境のワークロードのリソースを使用しますが、このハウツー記事では、デプロイのリージョンは重要ではありません。

## <a name="overview"></a>概要

使用中のコンポーネントと、このプロセスが完了した後のそれらの間のフローを次に示します。
![使用中とフローのコンポーネント](../api-management/media/howto-protect-backend-frontend-azure-ad-b2c/image-arch.png "使用中とフローのコンポーネント")

手順の概要は次のとおりです。

1. スコープが指定された Azure AD B2C 呼び出し (フロントエンド、API Management) および API アプリケーションを作成し、API アクセスを許可します
1. サインアップおよびサインイン ポリシーを作成して、ユーザーが Azure AD B2C でサインインできるようにします 
1. 開発者コンソールで OAuth2 ユーザーの承認を有効にするように新しい Azure AD B2C クライアント ID とキーを使用して API Management を構成します
1. 関数 API を構築します
1. 新しい Azure AD B2C クライアント ID とキーで EasyAuth を有効にし、APIM VIP にロックダウンするように関数 API を構成します
1. API Management で API 定義を構築します
1. API Management API 構成に合わせて Oauth2 を設定します
1. **CORS** ポリシーを設定し、**validate-jwt** ポリシーを追加して、すべての受信要求の OAuth トークンを検証します
1. API を使用する呼び出し元のアプリケーションを構築します
1. JS SPA サンプルをアップロードします
1. 新しい Azure AD B2C クライアント ID とキーを使用してサンプル JS クライアント アプリを構成します 
1. クライアント アプリケーションをテストします

   > [!TIP]
   > このドキュメントでは、かなりの数の情報やキーなどをキャプチャするため、テキスト エディターを開いて次の構成項目を一時的に保存すると便利な場合があります。  
   >
   > B2C BACKEND CLIENT ID:  
   > B2C BACKEND CLIENT SECRET KEY:  
   > B2C BACKEND API SCOPE URI:  
   > B2C FRONTEND CLIENT ID:  
   > B2C USER FLOW ENDPOINT URI:  
   > B2C WELL-KNOWN OPENID ENDPOINT:   
   > B2C POLICY NAME: Frontendapp_signupandsignin FUNCTION URL:  
   > APIM API BASE URL: STORAGE PRIMARY ENDPOINT URL:  

## <a name="configure-the-backend-application"></a>バックエンド アプリケーションを構成する

ポータルで [Azure AD B2C] ブレードを開き、次の手順を実行します。

1. **[アプリの登録]** タブを選択します
1. [新規登録] ボタンをクリックします。
1. [リダイレクト URI] 選択ボックスで [Web] を選択します。
1. 次に [表示名] を設定します。このとき、作成するサービスに関連する一意のものを選択します。 この例では、"Backend Application" という名前を使用します。
1. 応答 URL には 'https://jwt.ms ' (Microsoft 所有のトークン デコード サイト) などのプレースホルダーを使用します。これらの URL は後で更新します。
1. [Accounts in any identity provider or organizational directory (for authenticating users with user flows)]\((ユーザー フローを使用してユーザーを認証するための) 任意の ID プロバイダーまたは組織のディレクトリのアカウント\) オプションを選択します
1. このサンプルでは、現在 offline_access アクセス許可を必要としないため、[管理者の同意の付与] チェックボックスをオフにします。
1. [登録] をクリックします。
1. 後で使用するために Backend Application のクライアント ID をメモします ([アプリケーション (クライアント) ID] の下に表示されます)。
1. *[証明書とシークレット]* タブ ([管理] の下) を選択し、[新しいクライアント シークレット] をクリックして認証キーを生成します (既定の設定をそのまま使用し、[追加] をクリックします)。
1. [追加] をクリックした後、([値] の下にある) キーを安全な場所にコピーし、"バックエンド クライアント シークレット" として後で使用できるようにします。このダイアログは、このキーをコピーする唯一の機会であることに注意してください。
1. 次に、 *[API の公開]* タブ ([管理] の下) を選択します。
1. [AppID URI] を設定するよう求められます。既定値を選択してメモします。
1. 関数 API のスコープを作成して "Hello" という名前を指定します。すべての入力可能なオプションに "Hello" という語句を使用できます。設定された完全なスコープ値の URI をメモし、[スコープの追加] をクリックします。
1. ポータルの左上にある [Azure AD B2C] 階層リンクを選択して、Azure AD B2C ブレードのルートに戻ります。

   > [!NOTE]
   > Azure AD B2C スコープは、他のアプリケーションを使ってアプリケーションから API アクセス ブレードを介してアクセスを要求できる、API 内の事実上のアクセス許可です。実際、ここでは呼び出された API のアプリケーション アクセス許可を作成しました。

## <a name="configure-the-frontend-application"></a>フロントエンド アプリケーションを構成する

1. **[アプリの登録]** タブを選択します
1. [新規登録] ボタンをクリックします。
1. [リダイレクト URI] 選択ボックスで [シングル ページ アプリケーション (SPA)] を選択します。
1. ここで [表示名] と [AppID URI] を設定します。このとき、この AAD B2C アプリ登録を使用するフロントエンド アプリケーションに関連する一意のものを選択します。 この例では、"Frontend Application" を使用できます
1. 最初のアプリの登録に従って、[サポートされているアカウントの種類] の選択を既定のままにします (ユーザー フローでユーザーを認証する)
1. 応答 URL には 'https://jwt.ms ' (Microsoft 所有のトークン デコード サイト) などのプレースホルダーを使用します。これらの URL は後で更新します。
1. [管理者の同意の付与] チェックボックスをオンのままにします
1. [登録] をクリックします。
1. 後で使用するために Frontend Application のクライアント ID をメモします ([アプリケーション (クライアント) ID] の下に表示されます)。
1. *[API のアクセス許可]* タブに切り替えます。
1. バックエンド アプリケーションへのアクセスを許可するには、[アクセス許可の追加]、[自分の API] の順にクリックし、[Backend Application] を選択して [アクセス許可] を選択し、前のセクションで作成したスコープを選択して、[アクセス許可の追加] をクリックします。
1. [{tenant} に管理者の同意を与えます] をクリックし、ポップアップ ダイアログで [はい] をクリックします。 このポップアップでは、前に作成した "Backend Application" で定義されているアクセス許可 "hello" を "Frontend Application" で使用することに同意します。
1. アプリの [すべてのアクセス許可] には、[状態] 列の下に緑色のチェックが表示されます

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>"サインアップとサインイン" ユーザー フローを作成する

1. [Azure AD B2C] 階層リンクを選択して、B2C ブレードのルートに戻ります。
1. [ユーザー フロー] タブ ([ポリシー] の下) に切り替えます。
1. [新しいユーザー フロー] をクリックします
1. [サインアップとサインイン] ユーザー フローの種類を選択し、[推奨] を選択してから [作成] を選択します
1. ポリシーに名前を付け、後で使用できるようにメモします この例では、"Frontendapp_signupandsignin" を使用できます。これには "B2C_1_" というプレフィックスが付けられ、"B2C_1_Frontendapp_signupandsignin" が作成されることに注意してください
1. [ID プロバイダー] と [ローカル アカウント] で、[電子メールのサインアップ] (B2C テナントの構成によっては [User ID sign up]\(ユーザー ID のサインアップ\)) をオンにし、[OK] をクリックします。 この構成を行う理由は、ユーザーの既存のソーシャル メディア アカウントを使用するために別の ID プロバイダー (ソーシャル ID プロバイダーなど) に従うのではなく、ローカルの B2C アカウントを登録するためです。
1. MFA と条件付きアクセスの設定は、既定値のままにしておきます。
1. [ユーザー属性と要求] の下にある [詳細を表示] をクリックします。次に、ユーザーに入力させ、トークンで返すようにする要求オプションを選択します。 少なくとも [表示名] と [電子メール アドレス] を収集し、[表示名] と [電子メール アドレス] を返すように選択し、 [OK] をクリックし、[作成] をクリックしてください。
1. 作成したユーザー フローを一覧からクリックし、[ユーザー フローを実行します] ボタンをクリックします。
1. この操作により、[ユーザー フローを実行します] ブレードが開き、フロントエンド アプリケーションが選択されます。ユーザー フロー エンドポイントをコピーして、後で使用するために保存します。
1. 上部のリンクをコピーして保存し、後で使用できるように '既知の openid 構成エンドポイント' としてメモします。

   > [!NOTE]
   > B2C ポリシーを使用すると、Azure AD B2C ログイン エンドポイントを公開して、さまざまなデータ コンポーネントをキャプチャし、ユーザーのサインインにさまざまな方法を使用できるようになります。
   > 
   > この例では、サインアップまたはサインイン フロー (ポリシー) を構成しました。 これにより、既知の構成エンドポイントも公開されました。どちらの場合も、作成したポリシーは、"p =" クエリ文字列パラメーターによって URL で識別されていました。  
   >
   > これが完了すると、ユーザーを複数のアプリケーションにサインインさせる機能的な B2C ID プラットフォームを利用できるようになります。

## <a name="build-the-function-api"></a>関数 API を構築する

1. Azure portal の標準 Azure AD テナントに切り替えて、サブスクリプションの項目をもう一度構成できるようにします。
1. Azure portal の [Function Apps] ブレードにアクセスし、空の関数アプリを開き、[関数] をクリックして、[追加] をクリックします。
1. 表示されたポップアップで、[Develop in portal]\(ポータルで開発\) を選択し、[テンプレートの選択] で [HTTP トリガー] を選択し、[テンプレートの詳細] で、"hello" という名前を付けて承認レベル [Function]\(関数\) を指定し、[追加] を選択します。
1. [コードとテスト] ブレードに切り替え、下にあるサンプル コードをコピーし、表示されている "*既存のコードに重ねて*" 貼り付けます。
1. [保存] を選択します。

   ```csharp
   
   using System.Net;
   using Microsoft.AspNetCore.Mvc;
   using Microsoft.Extensions.Primitives;
   
   public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
   {
      log.LogInformation("C# HTTP trigger function processed a request.");
      
      return (ActionResult)new OkObjectResult($"Hello World, time and date are {DateTime.Now.ToString()}");
   }
   
   ```

   > [!TIP]
   > 先ほど貼り付けた C# スクリプト関数コードを実行すると、単に 1 行が関数ログに記録され、いくつかの動的データ (日付と時刻) と共に "Hello World" というテキストが返されます。

1. 左側のブレードで [統合] を選択し、[トリガー] ボックス内の http (req) リンクをクリックします。
1. [選択された HTTP メソッド] ドロップダウンで、http POST メソッドをオフにして、[GET] のみが選択されるようにし、[保存] をクリックします。
1. [コードとテスト] タブに戻り、[関数の URL の取得] をクリックし、表示された URL をコピーし、後で使用できるように保存します。

   > [!NOTE]
   > 先ほど作成したバインドによって、単に先ほどコピーした URL (`https://yourfunctionappname.azurewebsites.net/api/hello?code=secretkey`) に匿名の HTTP GET 要求で応答するように Functions に指示されます。 これで、非常に単純なペイロードを返す機能を持つスケーラブルなサーバーレス HTTPS API ができました。
   >
   > 先ほどコピーして保存したものと同じバージョンの URL を使用して、この API を Web ブラウザーから呼び出すことをテストできるようになりました。 また、URL のクエリ文字列パラメーター "?code=secretkey" の部分を削除し、もう一度テストして、Azure Functions が 401 エラーを返すことを証明することもできます。

## <a name="configure-and-secure-the-function-api"></a>関数 API の構成とセキュリティ保護

1. 関数アプリの領域をさらに 2 つ構成する必要があります (承認とネットワークの制限)。
1. まず、[認証/承認] を構成します。階層リンクを使用して関数アプリのルート ブレードに戻ります。
1. 次に、([設定] の下の) [認証/承認] を選択します。
1. App Service 認証機能を有効にします。
1. [要求が認証されない場合に実行するアクション] ドロップダウンを [Azure Active Directory でのログイン] に設定します。
1. [認証プロバイダー] の下の [Azure Active Directory] を選択します。
1. [管理モード] スイッチで [詳細] を選択します。
1. バックエンド アプリケーションの [アプリケーション] クライアント ID を (Azure AD B2C から) [クライアント ID] ボックスに貼り付けます
1. 既知の open-id 構成エンドポイントを、サインアップおよびサインイン ポリシーから [発行者 URL] ボックスに貼り付けます (この構成は以前の手順でメモしました)。
1. [シークレットの表示] をクリックし、バックエンド アプリケーションのクライアント シークレットを適切なボックスに貼り付けます。
1. [OK] を選択すると、ID プロバイダーの選択ブレード/画面に戻ります。
1. 詳細設定で、[[トークン ストア]](../app-service/overview-authentication-authorization.md#token-store) を有効のままにします (既定値)。
1. (ブレードの左上にある) [保存] をクリックします。

   > [!IMPORTANT]
   > これで、関数 API がデプロイされたので、正しい JWT が Authorization: Bearer ヘッダーとして指定されないと 401 応答がスローされ、有効な要求が提示されるとデータが返されます。  
   > 認証されていない要求を処理するための [Login With Azure AD]\(Azure AD でのログイン\) オプションを構成することで、EasyAuth に多層防御セキュリティを追加しました。 これにより、バックエンドの Function App とフロントエンド SPA の間の未承認要求の動作が変更されることに注意してください。EasyAuth は、401 許可されていませんという応答ではなく、302 AAD へのリダイレクトを発行します。これは、後で API Management を使用して修正します。  
   >
   > IP セキュリティはまだ適用されていません。有効なキーと OAuth2 トークンがあれば、誰でもこれをどこからでも呼び出すことができます。すべての要求が API Management を経由するよう強制するのが理想的です。  
   > 
   > APIM の従量課金レベルを使用している場合は、アクセス制限の機能を持つ許可リストに対する[専用の Azure API Management 仮想 IP はありません](./api-management-howto-ip-addresses.md#ip-addresses-of-consumption-tier-api-management-service)。 Azure API Management Standard SKU 以降では、[VIP はシングル テナントであり、リソースの有効期間が対象です](./api-management-howto-ip-addresses.md#changes-to-the-ip-addresses)。 Azure API Management の従量課金レベルでは、上記でコピーした URI の部分にある共有シークレット関数キーを使用して、API 呼び出しをロックダウンできます。 また、従量課金レベルの場合、下の手順 12 から 17 は該当しません。

1. [認証/承認] ブレードを閉じます 
1. "*ポータルの API Management ブレード*" を開き、次に "*インスタンス*" を開きます。
1. [概要] タブに表示されるプライベート VIP をメモします。
1. "*ポータルの Azure Functions ブレード*" に戻り、"*インスタンス*" を再度開きます。
1. [ネットワーク] を選択し、[アクセス制限を構成する] を選択します
1. [ルールの追加] をクリックし、上記の手順 3 でコピーした VIP を「xx.xx.xx.xx/32」の形式で入力します。
1. 関数ポータルとの対話を続行し、以下の省略可能な手順を実行する場合は、独自のパブリック IP アドレスまたは CIDR 範囲を追加する必要があります。
1. 一覧に許可エントリがあると、Azure によって暗黙的な拒否ルールが追加され、他のすべてのアドレスがブロックされます。

IP 制限パネルに CIDR 形式のアドレス ブロックを追加する必要があります。 API Management VIP などの単一のアドレスを追加する必要がある場合は、xx.xx.xx.xx/32 の形式で追加する必要があります。

   > [!NOTE]
   > これで、関数 API は、API 管理または自分のアドレス以外の場所から呼び出すことができなくなります。

1. "*API Management ブレード*" を開き、次に "*インスタンス*" を開きます。
1. API ブレード ([API] の下) を選択します。
1. [Add a New API]\(新しい API の追加\) ペインから [関数アプリ] を選択し、ポップアップの上部から [完全] を選択します。
1. [参照] をクリックして、内部で API をホストしている関数アプリを選択し、[選択] をクリックします。 次に [選択] を再びクリックします。
1. API に API Management 内部で使用するための名前と説明を付けて、"無制限" の製品に追加します。
1. API の [ベース URL] をコピーしてメモし、[作成] をクリックします。
1. [設定] タブをクリックし、[サブスクリプション] の下にある [サブスクリプションは必須です] チェックボックスをオフにします (ここでは Oauth JWT トークンをレート制限に使用するため)。 従量課金レベルを使用している場合、これは運用環境でも必要になることに注意してください。 

   > [!TIP]
   > APIM の従量課金レベルを使用している場合は、無制限の製品をすぐに使用することはできません。 代わりに、[API] の下にある [製品] に移動し、[追加] をクリックします。  
   > 製品の名前および説明として "Unlimited" と入力し、画面の左下にある API コールアウト "+" から、追加した API を選択します。 [公開済み] チェックボックスを選択します。 残りは既定値のままにします。 最後に、[作成] ボタンをクリックします。 これにより、"unlimited" の製品が作成され、API に割り当てられました。 後で新しい製品をカスタマイズできます。

## <a name="configure-and-capture-the-correct-storage-endpoint-settings"></a>正しいストレージ エンドポイント設定を構成してキャプチャする

1. Azure portal で [ストレージ アカウント] ブレードを開きます 
1. 作成したアカウントを選択し、[設定] セクションから [静的な Web サイト] ブレードを選択します ([静的な Web サイト] オプションが表示されない場合は、V2 アカウントを作成したことを確認します)。
1. 静的 Web ホスティング機能を [有効] に設定し、インデックス ドキュメント名を "index.html" に設定してから [保存] をクリックします。
1. [プライマリ エンドポイント] の内容をメモしておきます。この場所はフロントエンド サイトがホストされる場所であるためです。

   > [!TIP]
   > Azure Blob Storage + CDN 書き換え、または SPA をホストするための Azure App Service のいずれかを使用できますが、BLOB Storage の静的 Web サイト ホスティング機能には、Azure Storage から静的 Web コンテンツ、html、js、および css を提供する既定のコンテナーが用意されているので、作業なしの既定のページが想定されます。

## <a name="set-up-the-cors-and-validate-jwt-policies"></a>**[CORS]** および **[validate-jwt]** ポリシーを設定する

> 使用されている APIM レベルに関係なく、以下のセクションに従うようにします。 ストレージ アカウント URL は、この記事の上部にある前提条件から使用できるようにするストレージ アカウントのものです。
1. ポータルの API Management ブレードに切り替え、インスタンスを開きます。
1. [API] を選択し、[すべての API] を選択します。
1. [受信処理] で、コード ビュー ボタン [</>] をクリックしてポリシー エディターを表示します。
1. [受信] セクションを編集し、次のように XML を貼り付けます。
1. ポリシー内で次のパラメーターを置き換えます
1. {PrimaryStorageEndpoint} (前のセクションでコピーした 'Primary Storage Endpoint')、{b2cpolicy-well-known-openid} (以前コピーした '既知の openid 構成エンドポイント') および正しい値が上記で保存された {backend-api-application-client-id} (**バックエンド API** に対する B2C アプリケーション/クライアント ID)。
1. API Management の従量課金レベルを使用している場合は、両方の rate-limit-by-key ポリシーを削除する必要があります。これは、Azure API Management の従量課金レベルを使用するときにこのポリシーを利用できないためです。

   ```xml
   <inbound>
      <cors allow-credentials="true">
            <allowed-origins>
                <origin>{PrimaryStorageEndpoint}</origin>
            </allowed-origins>
            <allowed-methods preflight-result-max-age="120">
                <method>GET</method>
            </allowed-methods>
            <allowed-headers>
                <header>*</header>
            </allowed-headers>
            <expose-headers>
                <header>*</header>
            </expose-headers>
        </cors>
      <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid." require-expiration-time="true" require-signed-tokens="true" clock-skew="300">
         <openid-config url="{b2cpolicy-well-known-openid}" />
         <required-claims>
            <claim name="aud">
               <value>{backend-api-application-client-id}</value>
            </claim>
         </required-claims>
      </validate-jwt>
      <rate-limit-by-key calls="300" renewal-period="120" counter-key="@(context.Request.IpAddress)" />
      <rate-limit-by-key calls="15" renewal-period="60" counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
   </inbound>
   ```

   > [!NOTE]
   > これで Azure API Management は JavaScript SPA アプリからのクロス オリジン要求に応答できるようになりました。また、要求を関数 API に転送する前に渡される JWT 認証トークンの調整、レート制限、事前検証が実行されます。
   > 
   > これで、Azure AD B2C、API Management、Azure Functions を連携させて、API を発行し、セキュリティで保護し、さらに使用できるようになりました。

   > [!TIP]
   > API Management の従量課金レベルを使用している場合、JWT サブジェクトまたは受信 IP アドレスによるレート制限ではなく (キー ポリシーによる呼び出しレートの制限は "従量課金" レベルでは現在サポートされていません)、呼び出しレートのクォータによって制限できます。[こちら](./api-management-access-restriction-policies.md#LimitCallRate)を参照してください。  
   > この例は JavaScript シングル ページ アプリケーションなので、ここではレート制限と請求の呼び出しにのみ API Management キーを使用します。 実際の承認と認証は、Azure AD B2C によって処理され、JWT にカプセル化されます。これは 2 回検証されます (API Management による 1 回の後にバックエンドの Azure Functions)。

## <a name="upload-the-javascript-spa-sample-to-static-storage"></a>JavaScript SPA サンプルを静的ストレージにアップロードする

1. ストレージ アカウント ブレードで、[Blob Service] セクションから [Containers]\(コンテナー\) ブレードを選択し、右側のペインに表示される $web コンテナーをクリックします。
1. 次のコードをマシン上のローカルのファイルに index.html という名前で保存し、ファイル index.html を $web コンテナーにアップロードします。

   ```html
    <!doctype html>
    <html lang="en">
    <head>
         <meta charset="utf-8">
         <meta http-equiv="X-UA-Compatible" content="IE=edge">
         <meta name="viewport" content="width=device-width, initial-scale=1">
         <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta2/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-BmbxuPwQa2lc/FVzBcNJ7UAyJxM6wuqIj61tLrc4wSX0szH/Ev+nYRRuWlolflfl" crossorigin="anonymous">
         <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.11.1/js/msal-browser.min.js"></script>
    </head>
    <body>
         <div class="container-fluid">
             <div class="row">
                 <div class="col-md-12">
                    <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
                        <div class="container-fluid">
                            <a class="navbar-brand" href="#">Azure Active Directory B2C with Azure API Management</a>
                            <div class="navbar-nav">
                                <button class="btn btn-success" id="signinbtn"  onClick="login()">Sign In</a>
                            </div>
                        </div>
                    </nav>
                 </div>
             </div>
             <div class="row">
                 <div class="col-md-12">
                     <div class="card" >
                        <div id="cardheader" class="card-header">
                            <div class="card-text"id="message">Please sign in to continue</div>
                        </div>
                        <div class="card-body">
                            <button class="btn btn-warning" id="callapibtn" onClick="getAPIData()">Call API</a>
                            <div id="progress" class="spinner-border" role="status">
                                <span class="visually-hidden">Loading...</span>
                            </div>
                        </div>
                     </div>
                 </div>
             </div>
         </div>
         <script lang="javascript">
                // Just change the values in this config object ONLY.
                var config = {
                    msal: {
                        auth: {
                            clientId: "{CLIENTID}", // This is the client ID of your FRONTEND application that you registered with the SPA type in AAD B2C
                            authority:  "{YOURAUTHORITYB2C}", // Formatted as https://{b2ctenantname}.b2clogin.com/tfp/{b2ctenantguid or full tenant name including onmicrosoft.com}/{signuporinpolicyname}
                            redirectUri: "{StoragePrimaryEndpoint}", // The storage hosting address of the SPA, a web-enabled v2 storage account - recorded earlier as the Primary Endpoint.
                            knownAuthorities: ["{B2CTENANTDOMAIN}"] // {b2ctenantname}.b2clogin.com
                        },
                        cache: {
                            cacheLocation: "sessionStorage",
                            storeAuthStateInCookie: false 
                        }
                    },
                    api: {
                        scopes: ["{BACKENDAPISCOPE}"], // The scope that we request for the API from B2C, this should be the backend API scope, with the full URI.
                        backend: "{APIBASEURL}/hello" // The location that we will call for the backend api, this should be hosted in API Management, suffixed with the name of the API operation (in the sample this is '/hello').
                    }
                }
                document.getElementById("callapibtn").hidden = true;
                document.getElementById("progress").hidden = true;
                const myMSALObj = new msal.PublicClientApplication(config.msal);
                myMSALObj.handleRedirectPromise().then((tokenResponse) => {
                    if(tokenResponse !== null){
                        console.log(tokenResponse.account);
                        document.getElementById("message").innerHTML = "Welcome, " + tokenResponse.account.name;
                        document.getElementById("signinbtn").hidden = true;
                        document.getElementById("callapibtn").hidden = false;
                    }}).catch((error) => {console.log("Error Signing in:" + error);
                });
                function login() {
                    try {
                        myMSALObj.loginRedirect({scopes: config.api.scopes});
                    } catch (err) {console.log(err);}
                }
                function getAPIData() {
                    document.getElementById("progress").hidden = false; 
                    document.getElementById("message").innerHTML = "Calling backend ... "
                    document.getElementById("cardheader").classList.remove('bg-success','bg-warning','bg-danger');
                    myMSALObj.acquireTokenSilent({scopes: config.api.scopes, account: getAccount()}).then(tokenResponse => {
                        const headers = new Headers();
                        headers.append("Authorization", `Bearer ${tokenResponse.accessToken}`);
                        fetch(config.api.backend, {method: "GET", headers: headers})
                            .then(async (response)  => {
                                if (!response.ok)
                                {
                                    document.getElementById("message").innerHTML = "Error: " + response.status + " " + JSON.parse(await response.text()).message;
                                    document.getElementById("cardheader").classList.add('bg-warning');
                                }
                                else
                                {
                                    document.getElementById("cardheader").classList.add('bg-success');
                                    document.getElementById("message").innerHTML = await response.text();
                                }
                                }).catch(async (error) => {
                                    document.getElementById("cardheader").classList.add('bg-danger');
                                    document.getElementById("message").innerHTML = "Error: " + error;
                                });
                    }).catch(error => {console.log("Error Acquiring Token Silently: " + error);
                        return myMSALObj.acquireTokenRedirect({scopes: config.api.scopes, forceRefresh: false})
                    });
                    document.getElementById("progress").hidden = true;
             }
            function getAccount() {
                var accounts = myMSALObj.getAllAccounts();
                if (!accounts || accounts.length === 0) {
                    return null;
                } else {
                    return accounts[0];
                }
            }
        </script>
     </body>
    </html>
   ```

1. 以前に前のセクションで保存した静的な Web サイトのプライマリ エンドポイントを参照してください。

   > [!NOTE]
   > これで、JavaScript シングル ページ アプリが Azure Storage Static コンテンツ ホスティングにデプロイされました。  
   > Azure AD B2C の詳細を使用して JS アプリをまだ構成していないため、このページを開いても、まだ機能しません。

## <a name="configure-the-javascript-spa-for-azure-ad-b2c"></a>Azure AD B2C 用に JavaScript SPA を構成する

1. すべての場所がわかるようになったら、適切な API Management API アドレスと正しい Azure AD B2C アプリケーションおよびクライアント ID を使用して SPA を構成できます。
1. Azure portal ストレージ ブレードに戻ります 
1. [Containers]\(コンテナー\) を選択します ([設定] の下) 
1. 一覧から '$web' コンテナーを選択します
1. 一覧から index.html BLOB を選択します 
1. [編集] をクリックします 
1. 前に B2C で登録した "*フロントエンド*" アプリケーションと一致するように、msal config セクションの auth 値を更新します。 config 値がどのように表示されるかに関するヒントについては、コードのコメントを使用します。
*authority* の値は、次の形式にする必要があります: https://{b2ctenantname}.b2clogin.com/tfp/{b2ctenantname}.onmicrosoft.com}/{signupandsigninpolicyname}。サンプル名を使用し、b2c テナントを "contoso" とする場合、authority は 'https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com}/Frontendapp_signupandsignin ' となることが想定されます。
1. バックエンド アドレスに一致するように api 値を設定します (前にメモした API ベースの Url。'b2cScopes' の値は以前 "*バックエンド アプリケーション*" 用にメモされています)。
1. [保存] をクリックします。

## <a name="set-the-redirect-uris-for-the-azure-ad-b2c-frontend-app"></a>Azure AD B2C フロントエンド アプリのリダイレクト URI を設定する

1. [Azure AD B2C] ブレードを開き、JavaScript フロントエンド アプリケーションのアプリケーション登録に移動します。
1. [リダイレクト URI] をクリックし、前に入力したプレースホルダー 'https://jwt.ms ' を削除します。
1. プライマリ (ストレージ) エンドポイントの新しい URI を追加します (末尾のスラッシュを除く)。

   > [!NOTE]
   > この構成の結果、フロントエンド アプリケーションのクライアントは、Azure AD B2C から適切な要求を含むアクセス トークンを受け取ります。  
   > SPA を使って、バックエンド API への呼び出しの https ヘッダーにベアラー トークンとしてこれを追加できます。  
   > 
   > API Management は、トークンを事前検証し、Azure ID (ユーザー) と呼び出し元の IP アドレスによって発行された JWT の両方のサブジェクトによってエンドポイントの呼び出しをレート制限してから (API Management のサービス レベルによって異なります。上記の注記を参照)、要求を受信側の Azure Function API に渡し、関数セキュリティ キーを追加します。  
   > SPA によって、ブラウザーに応答が表示されます。
   >
   > *Azure AD B2C、Azure API Management、Azure Functions、Azure App Service Authorization が完全に調和するように構成されました。*

これで、セキュリティで保護されたシンプルな API を使用するシンプルなアプリができました。テストしてみましょう。

## <a name="test-the-client-application"></a>クライアント アプリケーションをテストする

1. 以前の手順で作成したストレージ アカウントからメモしたサンプル アプリの URL を開きます。
1. 右上隅の [サインイン] をクリックします。このクリックで、Azure AD B2C のサインアップまたはサインイン プロファイルがポップアップ表示されます。
1. アプリは B2C プロファイル名を使用して開始されます。
1. [API の呼び出し] をクリックすると、セキュリティで保護された API から返された値でページが更新されます。
1. [API の呼び出し] ボタンを "*繰り返し*" クリックしていて、Developer レベル以上の API Management で実行されている場合は、ソリューションによって API のレート制限が開始され、この機能は適切なメッセージを使用してアプリで報告されることに注意してください。

## <a name="and-were-done"></a>完了です

Azure AD B2C と API Management のさまざまな用途に合わせて、上記の手順を調整および編集できます。

## <a name="next-steps"></a>次のステップ

* [Azure Active Directory と OAuth2.0](../active-directory/develop/authentication-vs-authorization.md) の詳細を確認します。
* API Management についてのその他の [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=api-management) をご覧ください。
* バックエンド サービスを保護するその他の方法については、「[相互証明書認証](api-management-howto-mutual-certificates.md)」を参照してください。
* [API Management サービス インスタンスの作成](get-started-create-service-instance.md)。
* [Azure API Management での最初の API の管理](import-and-publish.md)。