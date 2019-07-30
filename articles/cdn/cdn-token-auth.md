---
title: トークン認証による Azure CDN 資産の保護 | Microsoft Docs
description: トークン認証を使用して、Azure CDN 資産へのアクセスをセキュリティで保護する方法について説明します。
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: 837018e3-03e6-4f9c-a23e-4b63d5707a64
ms.service: azure-cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/17/2017
ms.author: mezha
ms.openlocfilehash: fa71f472294b91baebc2a6075ddb2b50123e545d
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593391"
---
# <a name="securing-azure-cdn-assets-with-token-authentication"></a>トークン認証による Azure CDN 資産の保護

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>概要

トークン認証メカニズムを使用すると、承認されていないクライアントに Azure Content Delivery Network (CDN) が資産を提供できないように指定できます。 トークン認証は、通常、コンテンツの "*ホットリンク*" を防止するために行います。このホットリンクでは、自分の資産が他の Web サイト (掲示板など) によって許可なく使用されます。 ホットリンクは、コンテンツの配信コストに影響を与えることがあります。 CDN でトークン認証を有効にすると、CDN がコンテンツを配信する前に、CDN エッジ サーバーで要求が認証されます。 

## <a name="how-it-works"></a>動作のしくみ

トークン認証では、要求元に関するエンコードされた情報を含むトークン値を要求に求めることで、その要求が、信頼済みサイトによって生成されていることを確認します。 コンテンツが要求元に提供されるのは、そのエンコードされた情報が要件を満たす場合だけです。それ以外の場合、要求は拒否されます。 要件を設定するには、以下のパラメーターを 1 つ以上使用します。

- 国: [国番号](/previous-versions/azure/mt761717(v=azure.100))によって指定された国/地域から送信される要求を許可または拒否します。
- URL:指定した資産またはパスと一致する要求のみを許可します。
- [Host]\(ホスト\):要求ヘッダーで、指定したホストを使用する要求を許可または拒否します。
- 参照元: 指定した参照元からの要求を許可または拒否します。
- IP アドレス:特定の IP アドレスまたは IP サブネットからの要求のみを許可します。
- プロトコル:コンテンツの要求に使用されたプロトコルに基づいて要求を許可または拒否します。
- 有効期限: 限られた期間だけリンクが有効になるように日付と期間を割り当てます。

詳細については、「[トークン認証の設定](#setting-up-token-authentication)」で各パラメーターの詳しい構成例を参照してください。

>[!IMPORTANT] 
> このアカウントのいずれかのパスでトークン承認が有効になっている場合、クエリ文字列キャッシュに使用できるモードは standard-cache モードだけです。 詳細については、「[クエリ文字列による Azure CDN キャッシュ動作の制御](cdn-query-string-premium.md)」を参照してください。

## <a name="reference-architecture"></a>参照アーキテクチャ

次のワークフロー図は、CDN がトークン認証を使用して Web アプリと連携するようすを示しています。

![CDN トークン認証のワークフロー](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>CDN エンドポイントのトークン検証のロジック
    
次のフローチャートは、CDN エンドポイントでトークン認証が構成されている場合に、Azure CDN がクライアント要求を検証する方法を示しています。

![CDN トークン検証のロジック](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>トークン認証の設定

1. [Azure Portal](https://portal.azure.com) で CDN プロファイルを参照し、 **[管理]** を選択して、補助ポータルを起動します。

    ![[CDN プロファイル] の [管理] ボタン](./media/cdn-token-auth/cdn-manage-btn.png)

2. **[HTTP Large]\(HTTP ラージ\)** にポインターを置いて、ポップアップで **[Token Auth]\(トークン認証\)** をクリックします。 その後、次のように、暗号化キーと暗号化パラメーターを設定できます。

   1. 1 つまたは複数の暗号化キーを作成します。 暗号化キーは、大文字と小文字が区別され、任意の組み合わせの英数字を含めることができます。 スペースなどのその他の種類の文字は使用できません。 最大長は 250 文字です。 暗号化キーが確実にランダムになるようにするには、[OpenSSL ツール](https://www.openssl.org/)を使用して作成することをお勧めします。 

      OpenSSL ツールの構文は次のとおりです。

      ```rand -hex <key length>```

      例:

      ```OpenSSL> rand -hex 32``` 

      ダウンタイムを避けるために、主キーとバックアップ キーの両方を作成します。 バックアップ キーは、主キーが更新されるときに、コンテンツへのアクセスが中断しないようにします。
    
   2. **[主キー]** ボックスに一意の暗号化キーを入力し、必要に応じて、 **[Backup Key]\(バックアップ キー\)** ボックスにバックアップ キーを入力します。

   3. 各キーの最小の暗号化バージョンを **[Minimum Encryption Version]\(最小暗号化バージョン\)** リストから選び、 **[更新]** をクリックします。
      - **V2**: キーをバージョン 2.0 と 3.0 のトークンの生成に使用できることを示します。 このオプションは、従来のバージョン 2.0 暗号化キーからバージョン 3.0 キーに切り替える場合にのみ使用します。
      - **V3**: (推奨) キーをバージョン 3.0 のトークンの生成にのみ使用できることを示します。

      ![CDN トークン認証のセットアップ キー](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
   4. 暗号化ツールを使用して、暗号化パラメーターを設定し、トークンを生成します。 暗号化ツールを使用して、有効期限、国/地域、参照元、プロトコル、クライアント IP アドレスなどに基づいて (それらを任意に組み合わせて)、要求を許可または拒否できます。 トークンを作成するために組み合わせることのできるパラメーターの数と組み合わせに制限はありませんが、トークンの合計の長さは 512 文字に制限されます。 

      ![CDN 暗号化ツール](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

      **[暗号化ツール]** セクションで、次の 1 つ以上の暗号化パラメーターに対して値を入力します。 

      > [!div class="mx-tdCol2BreakAll"] 
      > <table>
      > <tr>
      >   <th>パラメーター名</th> 
      >   <th>説明</th>
      > </tr>
      > <tr>
      >    <td><b>ec_expire</b></td>
      >    <td>トークンが期限切れになるまでの有効期限を割り当てます。 有効期限の後に送信された要求は拒否されます。 このパラメーターでは、Unix タイムスタンプが使用されます。これは、`1/1/1970 00:00:00 GMT` の標準 Unix エポックからの秒数に基づいています (標準時間と Unix 時間は、オンライン ツールを使用して変換できます)。> 
      >    たとえば、トークンが `12/31/2016 12:00:00 GMT` に期限切れになるよう設定する場合は、Unix タイムスタンプの値として「`1483185600`」と入力します。 
      > </tr>
      > <tr>
      >    <td><b>ec_url_allow</b></td> 
      >    <td>特定の資産またはパスに合わせてトークンを調整できます。 特定の相対パスで始まる URL を持つ要求へのアクセスを制限します。 URL は大文字と小文字が区別されます。 複数のパスを入力するには、各パスをコンマで区切ります。スペースを入れないでください。 要件に応じてさまざまな値を設定して、さまざまなレベルのアクセスを提供できます。> 
      >    たとえば、URL `http://www.mydomain.com/pictures/city/strasbourg.png` について、入力値と、許可される要求を次に示します。 
      >    <ul>
      >       <li>入力値 `/`:すべての要求が許可されます。</li>
      >       <li>入力値 `/pictures`: 以下の要求が許可されます。 <ul>
      >          <li>`http://www.mydomain.com/pictures.png`</li>
      >          <li>`http://www.mydomain.com/pictures/city/strasbourg.png`</li>
      >          <li>`http://www.mydomain.com/picturesnew/city/strasbourgh.png`</li>
      >       </ul></li>
      >       <li>入力値 `/pictures/`:`/pictures/` パスを含む要求のみが許可されます。 たとえば、「 `http://www.mydomain.com/pictures/city/strasbourg.png` 」のように入力します。</li>
      >       <li>入力値 `/pictures/city/strasbourg.png`:この特定のパスと資産に対する要求のみが許可されます。</li>
      >    </ul>
      > </tr>
      > <tr>
      >    <td><b>ec_country_allow</b></td> 
      >    <td>1 つまたは複数の指定された国/地域から発信される要求のみを許可します。 その他のすべての国/地域から送信される要求は拒否されます。 各国の 2 文字の [ISO 3166 国コード](/previous-versions/azure/mt761717(v=azure.100))を使用し、それぞれをコンマで区切ります。スペースを追加しないでください。 たとえば、米国とフランスからのアクセスのみを許可する場合は、「`US,FR`」と入力します。</td>
      > </tr>
      > <tr>
      >    <td><b>ec_country_deny</b></td> 
      >    <td>1 つまたは複数の指定された国/地域から発信される要求のみを拒否します。 その他のすべての国/地域から送信される要求は許可されます。 実装は、<b>ec_country_allow</b> パラメーターと同じです。 国コードが <b>ec_country_allow</b> と <b>ec_country_deny</b> の両方のパラメーターに存在する場合、<b>ec_country_allow</b> パラメーターが優先されます。</td>
      > </tr>
      > <tr>
      >    <td><b>ec_ref_allow</b></td>
      >    <td>指定した参照元からの要求のみを許可します。 要求されているリソースにリンクされた Web ページの URL が、参照元により特定されます。 パラメーター値には、プロトコルを含めないでください。> 
      >    許可されている入力の種類は次のとおりです。
      >    <ul>
      >       <li>ホスト名、またはホスト名とパス。</li>
      >       <li>複数の参照元。 複数の参照元を追加するには、各参照元をコンマで区切ります。スペースを追加しないでください。 参照元の値を指定したにもかかわらず、ブラウザー構成のせいで参照元の情報が要求で送信されない場合、要求は既定で拒否されます。</li> 
      >       <li>参照元情報がないか空白の要求。 既定では、<b>ec_ref_allow</b> パラメーターを指定すると、これらの種類の要求をブロックします。 これらの要求を許可するには、「missing」というテキストか、(末尾のコンマを使用して) 空の値を入力します。</li> 
      >       <li>サブドメイン。 サブドメインを許可するには、アスタリスク (\*) を入力します。 たとえば、`contoso.com` のすべてのサブドメインを許可するには、「`*.contoso.com`」と入力します。</li>
      >    </ul> 
      >    たとえば、`www.contoso.com`、`contoso2.com` のすべてのサブドメインからの要求と、参照元が空白か不明な要求にアクセスを許可するには、「`www.contoso.com,*.contoso.com,missing`」と入力します。</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_ref_deny</b></td>
      >    <td>指定した参照元からの要求を拒否します。 実装は、<b>ec_ref_allow</b> パラメーターと同じです。 参照元が <b>ec_ref_allow</b> と <b>ec_ref_deny</b> パラメーターの両方に存在する場合は、<b>ec_ref_allow</b> パラメーターが優先されます。</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_proto_allow</b></td> 
      >    <td>指定したプロトコルからの要求のみを許可します。 有効な値は、`http`、`https`、または `http,https` です。</td>
      > </tr>
      > <tr>
      >    <td><b>ec_proto_deny</b></td>
      >    <td>指定したプロトコルからの要求を拒否します。 実装は、<b>ec_proto_allow</b> パラメーターと同じです。 プロトコルが <b>ec_proto_allow</b> と <b>ec_proto_deny</b> パラメーターの両方に存在する場合は、<b>ec_proto_allow</b> パラメーターが優先されます。</td>
      > </tr>
      > <tr>
      >    <td><b>ec_clientip</b></td>
      >    <td>指定した要求元の IP アドレスへのアクセスを制限します。 IPV4 と IPV6 の両方がサポートされています。 1 つの要求 IP アドレスまたは特定のサブネットに関連付けられている IP アドレスのいずれかを指定することができます。 たとえば、`11.22.33.0/22` では、IP アドレス 11.22.32.1 から 11.22.35.254 の要求を許可します。</td>
      > </tr>
      > </table>

   5. 暗号化パラメーター値の入力が完了したら、 **[Key To Encrypt]\(暗号化キー\)** の一覧から暗号化キーを選びます (主キーとバックアップ キーの両方を作成した場合)。
    
   6. **[Encryption Version]\(暗号化バージョン\)** の一覧から暗号化バージョンを選びます。**V2** はバージョン 2 用、**V3** はバージョン 3 用 (推奨) です。 

   7. **[暗号化]** を選択して、トークンを生成します。

      トークンが生成されると、そのトークンが **[Generated Token]\(生成されたトークン)** ボックスに表示されます。 トークンを使用するには、そのトークンをクエリ文字列として URL パスのファイルの末尾に追加します。 たとえば、「 `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b` 」のように入力します。
        
   8. 必要に応じて、トークンのパラメーターを表示できるように、暗号化解除ツールでトークンをテストします。 **[Token to Decrypt]\(暗号化を解除するトークン\)** ボックスに、トークンの値を貼り付けます。 **[Key To Decrypt]\(暗号化を解除するキー\)** リストで暗号化キーを選び、 **[暗号化解除]** をクリックします。

      トークンが暗号化解除されると、そのパラメーターが **[元のパラメーター]** ボックスに表示されます。

   9. 必要に応じて、要求が拒否されたときに返される応答コードの種類をカスタマイズします。 **[有効]** を選択し、 **[応答コード]** リストから応答コードを選択します。 **[ヘッダー名]** は、自動的に **[場所]** に設定されます。 **[保存]** を選択して新しい応答コードを実装します。 特定の応答コードについては、 **[ヘッダー値]** ボックスにエラー ページの URL も入力する必要があります。 既定では、**403** 応答コード (許可されていません) が選択されています。 

3. **[HTTP Large]\(HTTP ラージ\)** で **[ルール エンジン]** を選択します。 ルール エンジンを使用して、機能を適用するパスを定義したり、トークン認証機能や、追加のトークン認証関連機能を有効にしたりします。 詳細については、[ルール エンジンのリファレンス](cdn-rules-engine-reference.md)を参照してください。

   1. 既存のルールを選択するか、新しいルールを作成して、トークン認証を適用する資産またはパスを定義します。 
   2. ルールでトークン認証を有効にするには、 **[機能]** リストから **[[Token Auth]\(トークン認証\)](cdn-verizon-premium-rules-engine-reference-features.md#token-auth)** を選択し、 **[有効]** を選択します。 ルールを更新する場合は **[更新]** を、ルールを作成する場合は **[追加]** を選択します。
        
      ![CDN ルール エンジンのトークン認証を有効にする例](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. ルール エンジンで、追加のトークン認証関連機能を有効にすることもできます。 次の機能のいずれかを有効にするには、 **[機能]** リストからその機能を選択し、 **[有効]** を選択します。
    
   - **[トークン認証拒否コード](cdn-verizon-premium-rules-engine-reference-features.md#token-auth-denial-code)** :要求が拒否されたときにユーザーに返される応答の種類を決定します。 トークン ベースの認証ページの **[Custom Denial Handling]\(カスタム拒否の処理\)** セクションで設定された応答コードは、ここで設定されたルールでオーバーライドされます。

   - **[トークン認証の URL 大文字と小文字の無視](cdn-verizon-premium-rules-engine-reference-features.md#token-auth-ignore-url-case)** :トークンの検証に使用される URL で大文字と小文字が区別されるかどうかを決定します。

   - **[トークン認証パラメーター](cdn-verizon-premium-rules-engine-reference-features.md#token-auth-parameter)** :要求された URL に表示されるトークン認証クエリ文字列パラメーターの名前を変更します。 
        
     ![CDN ルール エンジンのトークン認証設定の例](./media/cdn-token-auth/cdn-rules-engine2.png)

5. トークンをカスタマイズするには、[GitHub](https://github.com/VerizonDigital/ectoken) でソース コードにアクセスします。
   使用可能な言語は次のとおりです。
    
   - C
   - C#
   - PHP
   - Perl
   - Java
   - Python 

## <a name="azure-cdn-features-and-provider-pricing"></a>Azure CDN の機能とプロバイダーの価格

機能については、[Azure CDN 製品の機能](cdn-features.md)に関する記事をご覧ください。 価格については、「[Content Delivery Network の価格](https://azure.microsoft.com/pricing/details/cdn/)」をご覧ください。
