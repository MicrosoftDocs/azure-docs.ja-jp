---
title: Azure Active Directory B2C および Azure API Management を使用して OAuth 2.0 で SPA バックエンドを保護します。
description: JavaScript SPA から呼び出される Azure Active Directory B2C、Azure API Management、および Easy Auth を使用して、OAuth 2.0 で API を保護します。
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
ms.date: 02/20/2020
ms.author: wieastbu
ms.custom: fasttrack-new
ms.openlocfilehash: 60177dd00dc6326aae4cfdc0b658c85f2635f8c0
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2020
ms.locfileid: "86253696"
---
# <a name="protect-spa-backend-with-oauth-20-azure-active-directory-b2c-and-azure-api-management"></a>OAuth 2.0、Azure Active Directory B2C、および Azure API Management を使用して SPA バックエンドを保護する

このシナリオでは、API を保護するように Azure API Management インスタンスを構成する方法について説明します。
EasyAuth を使用して Azure Functions バックエンドをセキュリティで保護するために、API Management と共に Azure AD B2C で OAuth 2.0 プロトコルを使用します。

## <a name="aims"></a>目的
ここでは、Azure Functions と Azure AD B2C を使用した簡略化されたシナリオで API Management を使用する方法を確認します。 Azure AD B2C を使用してユーザーをサインインさせる API を呼び出す JavaScript (JS) アプリを作成します。 次に、API Management の validate-jwt ポリシー機能を使用して、バックエンド API を保護します。

多層防御のために、EasyAuth を使用して、バックエンド API 内でトークンを再び検証します。

## <a name="prerequisites"></a>前提条件
この記事の手順を実行するには、以下が必要です。
* フロントエンドの JS シングル ページ アプリをホストする Azure (StorageV2) 汎用 V2 ストレージ アカウント
* Azure API Management インスタンス 
* 呼び出された API をホストする空の Azure Function アプリ (Windows 従量課金プランで V2 .NET Core ランタイムを実行)
* サブスクリプションにリンクされている Azure AD B2C テナント 

実際には、同じリージョン内の運用環境のワークロードのリソースを使用しますが、このハウツー記事では、デプロイのリージョンは重要ではありません。

## <a name="overview"></a>概要
使用中のコンポーネントと、それらの間のこのプロセスが完了した後のフローを次に示します。
![使用中とフローのコンポーネント](../api-management/media/howto-protect-backend-frontend-azure-ad-b2c/image-arch.png "使用中とフローのコンポーネント")

手順の概要は次のとおりです。

1. スコープが指定された Azure AD B2C 呼び出し (フロントエンド、API Management) および API アプリケーションを作成し、API アクセスを許可します
1. サインアップまたはサインイン ポリシーを作成して、ユーザーが Azure AD B2C でサインインできるようにします 
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

## <a name="configure-azure-ad-b2c"></a>Azure AD B2C を構成する 
ポータルで [Azure AD B2C] ブレードを開き、次の手順を実行します。
1. **[アプリケーション]** タブを選択します 
1. [追加] ボタンをクリックし、次の目的のために 3 つのアプリケーションを作成します
   * フロントエンド クライアント。
   * バックエンド関数 API。
   * [省略可能] API Management 開発者ポータル (従量課金レベルで Azure API Management を実行している場合を除き、このシナリオについては後で詳しく説明します)。
1. 3 つすべてのアプリケーションに対して WebApp/Web API を設定し、フロントエンド クライアントのみに対して [暗黙的フローを許可する] を [はい] に設定します。
1. 次に、アプリ ID URI を設定し、作成するサービスに固有で関連性のあるものを選択します。
1. ここでは https://localhost などの応答 URL のプレースホルダーを使用します。これらの URL は後で更新します。
1. [作成] をクリックし、前述の 3 つのアプリのそれぞれについて手順 2 から 5 を繰り返し、AppID URI、名前、およびアプリケーション ID をメモして、3 つのアプリすべてで後で使用できるようにします。
1. アプリケーションの一覧から API Management 開発者ポータル アプリケーションを開き、 *[キー]* タブ ([全般] の下) を選択し、[キーの生成] をクリックして認証キーを生成します
1. [保存] をクリックするときに、後で使用できるようにキーをメモして安全な場所に保管します。このキーを表示してコピーできるのは、ここだけです。
1. *[Published Scopes]\(公開済みスコープ\)* タブ ([API アクセス] の下) を選択します
1. 関数 API のスコープを作成して名前を付け、[スコープ] と設定された [完全なスコープ値] をメモして [保存] をクリックします。
   > [!NOTE]
   > Azure AD B2C スコープは、他のアプリケーションを使ってアプリケーションから API アクセス ブレードを介してアクセスを要求できる、API 内の事実上のアクセス許可です。実際、ここでは呼び出された API のアプリケーション アクセス許可を作成しました。
1. 他の 2 つのアプリケーションを開き、 *[API アクセス]* タブを確認します。
1. バックエンド API スコープへのアクセス権と、既に存在する既定のアクセス権 ([Access the user's profile]\(ユーザーのプロファイルへのアクセス\)) を付与します。
1. それぞれにキーを生成するには、[全般] の下の *[キー]* タブを選択し、認証キーを生成して、後で使用できるようにキーをメモして安全な場所に保管します。

## <a name="create-a-sign-up-or-sign-in-user-flow"></a>[サインアップまたはサインイン] ユーザー フローを作成する
1. Azure AD B2C ブレードのルート (つまり [概要]) に戻ります 
1. 次に、[ユーザー フロー (ポリシー)] を選択し、[新しいユーザー フロー] をクリックします
1. [サインアップまたはサインイン] ユーザー フローの種類を選択します
1. ポリシーに名前を付け、後で使用できるようにメモします
1. 次に、[ID プロバイダー] の下の [User ID sign up]\(ユーザー ID のサインアップ\) ([Email sign up]\(電子メールのサインアップ\) と表示されている場合もある) をオンにし、[OK] をクリックします。 
1. [ユーザー属性と要求] の下にある [詳細を表示] をクリックします。次に、ユーザーに入力させ、トークンで返すようにする要求オプションを選択します。 収集して返す対象として少なくとも [表示名] と [メール アドレス] をオンにして、[OK] をクリックしてから [作成] をクリックします。
1. 一覧から作成したポリシーを選択し、[ユーザー フローを実行します] ボタンをクリックします。
1. この操作で、ユーザー フローの実行ブレードが開き、フロントエンド アプリケーションが選択されるので、[ドメインの選択] のドロップダウンの下に表示される b2clogin.com ドメインのアドレスをメモします。
1. 上部のリンクをクリックして [well-known openid configuration endpoint]\(既知の openid 構成エンドポイント\) を開き、authorization_endpoint 値、token_endpoint 値、およびリンク自体の値を既知の openid 構成エンドポイントとしてメモします。

   > [!NOTE]
   > B2C ポリシーを使用すると、Azure AD B2C ログイン エンドポイントを公開して、さまざまなデータ コンポーネントをキャプチャし、ユーザーのサインインにさまざまな方法を使用できるようになります。 この場合、サインアップまたはサインイン エンドポイントを構成し、既知の構成エンドポイントを公開しました。具体的には、この作成したポリシーは URL の p= パラメーターによって識別されました。
   > 
   > これが完了すると、ユーザーを複数のアプリケーションにサインインさせる機能的な B2C ID プラットフォームを利用できるようになります。 
   > 必要に応じて、[ユーザー フローを実行します] ボタンをクリックし (サインアップまたはサインイン プロセスを進め)、実際の動作を確認できますが、アプリはまだデプロイされていないため、最後のリダイレクト手順は失敗します。

## <a name="build-the-function-api"></a>関数 API を構築する
1. Azure portal の標準 Azure AD テナントに切り替えて、サブスクリプションの項目をもう一度構成できるようにします。 
1. Azure portal の [関数アプリ] ブレードにアクセスし、関数アプリを開き、クイックスタートを使用してポータル内の新しい [webhook + API] 関数を作成します。
1. Run.csx の表示される既存のコードに、次のサンプル コードを貼り付けます。

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

   > [!NOTE]
   > 先ほど貼り付けた C# スクリプト関数コードを実行すると、単に 1 行が関数ログに記録され、いくつかの動的データ (日付と時刻) と共に "Hello World" というテキストが返されます。

3. 左側のブレードから [統合] を選択し、ペインの右上隅にある [詳細エディター] を選択します。
4. 既存の json に次のサンプル コードを貼り付けます。

   ```json
   {
      "bindings": [
       {
        "authLevel": "function",
        "name": "req",
        "type": "httpTrigger",
        "direction": "in",
        "methods": [
           "get"
        ],
        "route": "hello"
       },
       {
         "name": "$return",
         "type": "http",
         "direction": "out"
       }
     ]
   }
   ```

5. [HttpTrigger1] タブに戻り、[関数の URL の取得] をクリックして、表示される URL をコピーします。

   > [!NOTE]
   > 先ほど作成したバインドによって、単に先ほどコピーした URL に匿名の HTTP GET 要求で応答するように Functions に指示されます (`https://yourfunctionappname.azurewebsites.net/api/hello?code=secretkey`)。これで、非常に単純なペイロードを返す機能を持つスケーラブルなサーバーレス HTTPS API ができました。
   > 上記の URL を使用して Web ブラウザーからこの API の呼び出しをテストできるようになりました。URL の ?code=secret 部分を削除して、Azure Functions から 401 エラーが返されることを証明することもできます。

## <a name="configure-and-secure-the-function-api"></a>関数 API の構成とセキュリティ保護
1. 関数アプリの領域をさらに 2 つ構成する必要があります (認証とネットワークの制限)。
1. まず、認証と承認を構成しましょう。関数アプリの名前 (&lt;Z&gt; 関数アイコンの横) をクリックして、概要ページを表示します。
1. 次に、[プラットフォーム機能] タブを選択し、[認証/承認] を選択します。
1. App Service 認証機能を有効にします。
1. [認証プロバイダー] で [Azure Active Directory] を選択し、[管理モード] スイッチから [詳細設定] を選択します。
1. バックエンド関数 API のアプリケーション ID を (Azure AD B2C から [クライアント ID] ボックスに) 貼り付けます。
1. 既知の open-id 構成エンドポイントをサインアップまたはサインイン ポリシーから [発行者 URL] ボックスに貼り付けます (この構成は以前の手順でメモしました)。
1. [OK] を選択します。
1. [要求が認証されない場合に実行するアクション] ドロップダウンを [Azure Active Directory でのログイン] に設定し、[保存] をクリックします。

   > [!NOTE]
   > これで、関数 API がデプロイされたので、正しいキーが指定されないと 401 応答がスローされ、有効な要求が提示されるとデータが返されます。
   > 認証されていない要求を処理するための [Login With Azure AD]\(Azure AD でのログイン\) オプションを構成することで、EasyAuth に多層防御セキュリティを追加しました。 これにより、バックエンドの Function App とフロントエンド SPA の間の未承認要求の動作が変更されることに注意してください。EasyAuth は、401 許可されていませんという応答ではなく、302 AAD へのリダイレクトを発行します。これは、後で API Management を使用して修正します。
   > IP セキュリティはまだ適用されていません。有効なキーと OAuth2 トークンがあれば、誰でもこれをどこからでも呼び出すことができます。すべての要求が API Management を経由するよう強制するのが理想的です。
   > API Management の従量課金レベルを使用している場合は、このレベル専用の静的 IP がないため、VIP によるこのロックダウンを実行できません。共有シークレット関数キーを使用して API 呼び出しをロックダウンする方法を利用する必要があります。したがって、手順 11 から 13 は実行できません。

1. [認証/承認] ブレードを閉じます 
1. [ネットワーク] を選択し、[アクセス制限] を選択します
1. 次に、許可されている関数アプリの IP を API Management インスタンス VIP にロックダウンします。 この VIP は、ポータルの [API 管理 - 概要] セクションに表示されます。
1. 関数ポータルとの対話を続行し、以下の省略可能な手順を実行する場合は、独自のパブリック IP アドレスまたは CIDR 範囲を追加する必要があります。
1. 一覧に許可エントリがあると、Azure によって暗黙的な拒否ルールが追加され、他のすべてのアドレスがブロックされます。 

IP 制限パネルに CIDR 形式のアドレス ブロックを追加する必要があります。 API Management VIP などの単一のアドレスを追加する必要がある場合は、xx.xx.xx.xx の形式で追加する必要があります。

   > [!NOTE]
   > これで、関数 API は、API 管理または自分のアドレス以外の場所から呼び出すことができなくなります。
   
## <a name="import-the-function-app-definition"></a>関数アプリの定義をインポートする
1. "*API Management ブレード*" を開き、次に "*インスタンス*" を開きます。
1. インスタンスの [API Management] セクションから [API] ブレードを選択します。
1. [Add a New API]\(新しい API の追加\) ペインから [関数アプリ] を選択し、ポップアップの上部から [完全] を選択します。
1. [参照] をクリックして、内部で API をホストしている関数アプリを選択し、[選択] をクリックします。
1. API に API Management 内部で使用するための名前と説明を付けて、"無制限" の製品に追加します。
1. 後で使用できるようにベース URL を必ずメモし、[作成] をクリックします。

## <a name="configure-oauth2-for-api-management"></a>API Management 用に Oauth2 を構成する

1. 次に、[セキュリティ] タブから [Oauth 2.0] ブレードを選択し、[追加] をクリックします。
1. 追加された Oauth エンドポイントの *[表示名]* と *[説明]* の値を入力します (これらの値は、次の手順で Oauth2 エンドポイントとして表示されます)。
1. [クライアント登録ページの URL] の値は使用されないため、任意の値を入力できます。
1. *[Implicit Auth]\(暗黙的な認証\)* の付与の種類をオンにし、[承認コード] の付与の種類をオンのままにします。
1. *[承認]* および *[トークン]* エンドポイント フィールドに移動し、以前に既知の構成 XML ドキュメントからキャプチャした値を入力します。
1. 下にスクロールし、"resource" という "*追加の本文パラメーター*" に、Azure AD B2C アプリ登録からのバックエンド関数の API クライアント ID を設定します
1. [クライアントの資格情報] を選択し、クライアント ID を開発者コンソール アプリのアプリ ID に設定します。従量課金 API Management モデルを使用している場合は、この手順をスキップします。
1. [クライアント シークレット] を以前の手順でメモしたキーに設定します。従量課金 API Management モデルを使用している場合は、この手順をスキップします。
1. 最後に、後で使用できるように API Management からの認証コード付与の redirect_uri をメモします。

## <a name="set-up-oauth2-for-your-api"></a>API の Oauth2 を設定する
1. ポータルの左側の [すべての API] セクションに API が表示されます。これをクリックして開きます。
1. [設定] タブを選択します。
1. ユーザーの承認ラジオ ボタンから [Oauth 2.0] を選択して設定を更新します。
1. 以前の手順で定義した Oauth サーバーを選択します。
1. [Override scope]\(スコープのオーバーライド\) チェックボックスをオンにして、以前の手順でバックエンド API 呼び出し用にメモしたスコープを入力します。

   > [!NOTE]
   > これで、Azure AD B2C からアクセス トークンを取得して要求を承認する方法を確認し、Oauth2 Azure Active Directory B2C 構成を把握できる API Management インスタンスを用意できました。

## <a name="set-up-the-cors-and-validate-jwt-policies"></a>**[CORS]** および **[validate-jwt]** ポリシーを設定する

> 使用されている APIM レベルに関係なく、以下のセクションに従うようにします。 

1. [デザイン] タブに戻り、[すべての API] を選択し、[コード ビュー] ボタンをクリックしてポリシー エディターを表示します。
1. [受信] セクションを編集し、次のように XML を貼り付けます。

   ```xml
   <inbound>
      <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
         <openid-config url="https://tenant.b2clogin.com/tenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_MyDefaultPolicy" />
         <required-claims>
            <claim name="aud">
               <value>your-backend-api-application-client-id</value>
            </claim>
         </required-claims>
      </validate-jwt>
      <cors>
         <allowed-origins>
            <origin>*</origin>
         </allowed-origins>
         <allowed-methods>
           <method>GET</method>
         </allowed-methods>
         <allowed-headers>
            <header>*</header>
         </allowed-headers>
         <expose-headers>
           <header>*</header>
         </expose-headers>
       </cors>
   </inbound>
   ```
1. サインアップまたはサインイン ポリシーの既知の Azure AD B2C エンドポイントと一致するように openid-config URL を編集します。
1. 有効なアプリケーション ID (バックエンド API アプリケーションのクライアント ID とも呼ばれます) と一致するように要求値を編集して保存します。

   > [!NOTE]
   > これで API Management は JS SPA アプリへのクロス オリジン要求に応答できるようになりました。また、要求を関数 API に転送する前に渡される JWT 認証トークンの調整、レート制限、事前検証が実行されます。

   > [!NOTE]
   > 次のセクションは省略可能であり、開発者ポータルをサポートしない**従量課金**レベルには適用されません。
   > 開発者ポータルを使用しない場合、または従量課金レベルを使用しているため使用できない場合は、この手順をスキップして、「[API を使用する JavaScript SPA を構築する](#build-the-javascript-spa-to-consume-the-api)」に進んでください。

## <a name="optional-configure-the-developer-portal"></a>[省略可能] 開発者ポータルを構成する

1. [Azure AD B2C] ブレードを開き、開発者ポータルのアプリケーション登録に移動します。
1. [応答 URL] エントリを、以前の手順で API Management で認証コード付与の redirect_uri を設定したときにメモした値に設定します。

   `Echo API` で OAuth 2.0 ユーザー承認が有効にすると、Developer Console は API を呼び出す前にユーザーのアクセス トークンを取得するようになります。

1. 開発者ポータルの `Echo API` の下にある操作を参照し、 **[使ってみる]** を選択して開発者コンソールに移動します。
1. **[承認]** セクションの新しい項目は、先ほど追加した承認サーバーに対応しています。
1. 承認ドロップダウン リストから **[承認コード]** を選択します。Azure AD テナントにサインインするように求められます。 既にアカウントでサインインしている場合、確認メッセージは表示されません。
1. サインインに成功すると、Base64 でエンコードされた Azure AD B2C のアクセス トークンを使用する要求に `Authorization: Bearer` ヘッダーが追加されます。 
1. **[送信]** を選択すると、API を適切に呼び出すことができます。

   > [!NOTE]
   > これで、API Management を使って、開発者ポータルのトークンを取得し、API をテストし、開発者ポータルでその定義を理解し、適切なテスト ページを表示できるようになりました。

1. API Management ポータルの [概要] ブレードから [開発者ポータル] をクリックし、API の管理者としてサインインします。
1. ここでは、自分とその他の選択された API コンシューマーは、コンソールからそれらをテストして呼び出すことができます。
1. [製品] を選択し、[無制限] を選択してから、前に作成した API を選択して [使ってみる] をクリックします
1. API サブスクリプション キーを再表示し、それをコピーして、後で必要になる要求 URL と共に安全な場所に保管します。
1. また、oauth 認証ドロップダウンから [暗黙] を選択します。必要に応じて、ここのポップアップで認証します。
1. [送信] をクリックします。問題がない場合、関数アプリからは、API Management を介して 200 OK メッセージと何らかの JSON と共に hello メッセージで応答します。

   > [!NOTE]
   > これで、Azure AD B2C、API Management、Azure Functions を連携させて、API を発行し、セキュリティで保護し、さらに使用できるようになりました。 実際のところ、API はこの方法を使用して 2 回セキュリティで保護されていることに注目してください。1 回は API Management の Ocp-Subscription-Key ヘッダー、もう 1 回は Authorization です。ベアラー JWT。
   > この例は JavaScript シングル ページ アプリケーションなので、これは正しい方法です。ここでは、レート制限と請求の呼び出しにのみ API Management キーを使用します。
   > 実際の承認と認証は、Azure AD B2C によって処理され、JWT にカプセル化されます。これは 2 回検証されます (API Management による 1 回の後に Azure Functions)。

## <a name="build-the-javascript-spa-to-consume-the-api"></a>API を使用する JavaScript SPA を構築する
1. Azure portal で [ストレージ アカウント] ブレードを開きます 
1. 作成したアカウントを選択し、[設定] セクションから [静的な Web サイト] ブレードを選択します ([静的な Web サイト] オプションが表示されない場合は、V2 アカウントを作成したことを確認します)。
1. 静的 Web ホスティング機能を [有効] に設定し、インデックス ドキュメント名を "index.html" に設定してから [保存] をクリックします。
1. この場所はフロントエンド サイトがホストされる場所なので、[プライマリ エンドポイント] の内容をメモしておきます。 

   > [!NOTE]
   > Azure Blob Storage + CDN 書き換え、または Azure App Service のいずれかを使用できますが、BLOB Storage の静的 Web サイト ホスティング機能には、Azure Storage から静的 Web コンテンツ、html、js、および css を提供する既定のコンテナーが用意されているので、作業なしの既定のページが想定されます。

## <a name="upload-the-js-spa-sample"></a>JS SPA サンプルをアップロードする
1. ストレージ アカウント ブレードで、[Blob service] セクションから [BLOB] ブレードを選択し、右側のペインに表示される $web コンテナーをクリックします。
1. 次のコードをマシン上のローカルのファイルに index.html という名前で保存し、ファイル index.html を $web コンテナーにアップロードします。

   ```html
   <!doctype html>
   <html lang="en">
   <head>
        <meta charset="utf-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <title>Sample JS SPA</title>
        <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
   </head>
   <body>
        <div class="container-fluid">
            <div class="row">
                <div class="col-md-12">
                    <nav class="navbar navbar-expand-lg navbar-light bg-light navbar-dark bg-dark">
                        <a class="navbar-brand" href="#">Sample Code</a>
                        <ul class="navbar-nav ml-md-auto">
                            <li class="nav-item dropdown">
                                <a class="btn btn-large btn-success" onClick="login()">Sign In</a>
                            </li>
                        </ul>
                    </nav>
                </div>
            </div>
            <div class="row">
                <div class="col-md-12">
                    <div class="jumbotron">
                        <h2>
                    <div id="message">Hello, world!</div>
                    </h2>
                        <p>
                            <a class="btn btn-primary btn-large" onClick="GetAPIData()">Call API</a>
                        </p>
                    </div>
                </div>
            </div>
        </div>
        <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
        <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
        <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
        <script lang="javascript">
            var applicationConfig = {
                clientID: "clientidgoeshere",
                authority: "https://tenant.b2clogin.com/tfp/tenant/policy",
                b2cScopes: ["https://tenant/app/scope"],
                webApi: 'http://functionurl',
                subKey: 'apimkeygoeshere'
            };
            var msalConfig = {
                auth: {
                        clientId: applicationConfig.clientID, 
                        authority:  applicationConfig.authority,
                        validateAuthority: false
                },
                cache: {
                        cacheLocation: "localStorage",
                        storeAuthStateInCookie: true
                }
            };
            var clientApplication = new Msal.UserAgentApplication(msalConfig);
            function login() {
                var loginRequest = {
                    scopes: applicationConfig.b2cScopes
                };
                clientApplication.loginPopup(loginRequest).then(function (loginResponse) {
                    var tokenRequest = {
                        scopes: applicationConfig.b2cScopes
                    };
                    clientApplication.acquireTokenSilent(tokenRequest).then(function (tokenResponse) {
                        document.getElementById("signinbtn").innerHTML = "Logged in as: " + clientApplication.account.name;
                        document.getElementById("callapibtn").hidden = false
                        }).catch(function (error) {
                            clientApplication.acquireTokenPopup(tokenRequest).then(function (tokenResponse) {
                                }).catch (function (error) {
                                    console.log("Error acquiring the popup:\n" + error);
                                });
                        })
                    }).catch (function (error) {
                        console.log("Error during login:\n" + error);
                });
            }
            function GetAPIData() {
                var tokenRequest = {
                    scopes: applicationConfig.b2cScopes
                }
                clientApplication.acquireTokenSilent(tokenRequest).then(function (tokenResponse) {
                    callApiWithAccessToken(tokenResponse.accessToken);
                    }).catch(function (error) {
                        clientApplication.acquireTokenPopup(tokenRequest).then(function (tokenResponse) {
                            callApiWithAccessToken(tokenResponse.accessToken);
                            
                        }).catch(function (error) {
                            console.log("Error acquiring the access token to call the Web api:\n" + error);
                        });
                    })
            }
            function callApiWithAccessToken(token)
            {
                console.log("calling "  + applicationConfig.webApi +  " with " + token);
                    // Make the api call here
                $.ajax({
                    type: "get",
                    headers: {'Authorization': 'Bearer ' + token, 'Ocp-Apim-Subscription-Key': applicationConfig.subKey},                   url: applicationConfig.webApi
                }
                ).done(function (body) {
                    document.getElementById("message").innerHTML = "The API Said " + body;
                });
            }
        </script>
    </body>
   </html>
   
   ```

1. 以前に前のセクションで保存した静的な Web サイトのプライマリ エンドポイントを参照してください。

   > [!NOTE]
   > これで、JavaScript シングル ページ アプリが Azure Storage にデプロイされました。API のキーを使用して JS アプリを構成していないか、Azure AD B2C の詳細を使用して JS アプリを構成していないため、ページを開いてもまだ機能しません。

## <a name="configure-the-js-spa-for-azure-ad-b2c"></a>Azure AD B2C 用に JS SPA を構成する
1. これですべての場所がわかるようになりました。適切な API Management API アドレスと正しい Azure AD B2C アプリケーションおよびクライアント ID を使用して SPA を構成できます
1. Azure portal の [ストレージ] ブレードに戻り、index.html をクリックして、[BLOB の編集] を選択します。 
1. "b2cScopes" 値は API バックエンド用であることに注意して、以前に B2C に登録したフロントエンド アプリケーションと一致するように認証の詳細を更新します。
1. webApi キーと API URL は、API 操作の API Management のテスト ペインにあります。
1. [API Management] に戻って [API Management] ブレードを開き、[サブスクリプション] を選択し、[サブスクリプションの追加] をクリックし、レコードを保存することで、APIM サブスクリプション キーを作成します。 作成された行の横にある省略記号 ([...]) をクリックすると、キーが表示されるので、主キーをコピーできます。
1. 次のコードのようになります。  

   ```javascript
   var applicationConfig =
      clientID: "{aadb2c-clientid-goeshere}",
      authority: "https://{tenant}.b2clogin.com/{tenant}/{policy}",
      b2cScopes: ["https://{tenant}/{app}/{scope}"], 
      webApi: 'http://{apim-url-for-your-function}',
      subKey: '{apim-subscription-key-goes-here}'
   };
   ```

1. [保存] をクリックします。

## <a name="set-the-redirect-uris-for-the-azure-ad-b2c-frontend-app"></a>Azure AD B2C フロントエンド アプリのリダイレクト URI を設定する
1. [Azure AD B2C] ブレードを開き、JavaScript フロントエンド アプリケーションのアプリケーション登録に移動します
1. リダイレクト URL を、上記の静的な Web サイトのプライマリ エンドポイントを以前に設定したときにメモした値に設定します

   > [!NOTE] 
   > この構成の結果、フロントエンド アプリケーションのクライアントは、Azure AD B2C から適切な要求を含むアクセス トークンを受け取ります。
   > SPA を使って、バックエンド API への呼び出しの https ヘッダーにベアラー トークンとしてこれを追加できます。
   > 要求を介して受信側の Azure 関数 API に渡される前に、API Management によってトークンが事前に検証され、サブスクライバー キーによるエンドポイントの呼び出しのレートが制限されます。
   > SPA によって、ブラウザーに応答が表示されます。

   > *Azure AD B2C、Azure API Management、Azure Functions、Azure App Service Authorization が完全に調和するように構成されました。*

   > [!NOTE]
   > これで、セキュリティで保護されたシンプルな API を使用するシンプルなアプリができました。テストしてみましょう。

## <a name="test-the-client-application"></a>クライアント アプリケーションをテストする
1. 以前の手順で作成したストレージ アカウントからメモしたサンプル アプリの URL を開きます
1. 右上隅の [サインイン] をクリックします。このクリックで、Azure AD B2C のサインアップまたはサインイン プロファイルがポップアップ表示されます。
1. サインイン後、画面の [次の名前でログインしています] セクションが JWT から設定されます。
1. [Call Web Api]\(Web Api を呼び出す\) をクリックすると、セキュリティで保護された API から返された値でページが更新されます。

## <a name="and-were-done"></a>完了です
Azure AD B2C と API Management のさまざまな用途に合わせて、上記の手順を調整および編集できます。

## <a name="next-steps"></a>次のステップ
* [Azure Active Directory と OAuth2.0](../active-directory/develop/authentication-vs-authorization.md) の詳細を確認します。
* API Management についてのその他の [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=api-management) をご覧ください。
* バックエンド サービスを保護するその他の方法については、「[相互証明書認証](api-management-howto-mutual-certificates.md)」を参照してください。
* [API Management サービス インスタンスの作成](get-started-create-service-instance.md)。
* [Azure API Management での最初の API の管理](import-and-publish.md)。
