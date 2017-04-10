---
title: "Mobile Engagement REST API での認証"
description: "Azure Mobile Engagement REST API を使用して認証を行う方法について説明します"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: da82cb36-957a-4e19-a805-b44733cf6597
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 10/05/2016
ms.author: wesmc;ricksal
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 716d2d95b5d68dbb7fdd2ef50d220a8c8c2c9f5e


---
# <a name="authenticate-with-mobile-engagement-rest-apis"></a>Mobile Engagement REST API での認証
## <a name="overview"></a>概要
このドキュメントでは、有効な AAD Oauth トークンを取得して Mobile Engagement REST API で認証を行う方法について説明します。 

有効な Azure サブスクリプションを持っており、いずれかの [開発者チュートリアル](mobile-engagement-windows-store-dotnet-get-started.md)を使用して Mobile Engagement アプリを作成してあることが前提です。

## <a name="authentication"></a>認証
認証には、Microsoft Azure Active Directory ベースの OAuth トークンを使用します。 

API の要求を認証するには、すべての要求に Authorization ヘッダーが追加されている必要があります。次のような形式です。

    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

> [!NOTE]
> Azure Active Directory のトークンは 1 時間で有効期限が切れます。
> 
> 

トークンを入手するにはいくつかの方法があります。 API は一般にクラウド サービスから呼び出されるので、API キーを使用します。 Azure の用語では、API キーはサービス プリンシパル パスワードと呼ばれます。 次の手順では、手動で設定する方法について説明します。

### <a name="one-time-setup-using-script"></a>1 回限りのセットアップ (スクリプトを使用)
以下の説明に従って PowerShell スクリプトを使用してセットアップを実行するとセットアップにかかる時間は最短になりますが、許容できるほとんどの既定値を使用することになります。 あるいは、 [手動セットアップ](mobile-engagement-api-authentication-manual.md) の手順に従って Azure ポータルから直接行えば、さらにきめ細かく構成できます。 

1. Azure PowerShell の最新バージョンを [こちら](http://aka.ms/webpi-azps)から入手します。 ダウンロードの手順の詳細については、この [リンク](/powershell/azureps-cmdlets-docs)を参照してください。  
2. Azure PowerShell をインストールした後は、次のコマンドを使用して、 **Azure モジュール** がインストールされていることを確認します。
   
    a. 使用可能なモジュールのリストで Azure PowerShell モジュールが使用可能であることを確認します。 
   
        Get-Module –ListAvailable 
   
    ![利用可能な Azure モジュール][1]
   
    b. 上のリストで Azure PowerShell モジュールが見つからない場合は、次を実行する必要があります。
   
        Import-Module Azure 
3. PowerShell で次のコマンドを実行し、Azure アカウントのユーザー名とパスワードを指定して、Azure Resource Manager にログインします。 
   
        Login-AzureRmAccount
4. 複数のサブスクリプションがある場合は、次を実行する必要があります。
   
    a. すべてのサブスクリプションのリストを取得し、使用するサブスクリプションの SubscriptionId をコピーします。 このサブスクリプションが、API を使用して対話する Mobile Engagement アプリのものと同じであることを確認します。 
   
        Get-AzureRmSubscription
   
    b. SubscriptionId を指定して次のコマンドを実行し、使用するサブスクリプションを構成します。
   
        Select-AzureRmSubscription –SubscriptionId <subscriptionId>
5. [New-AzureRmServicePrincipalOwner.ps1](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) スクリプトのテキストをローカル コンピューターにコピーし、PowerShell コマンドレットとして (例: `APIAuth.ps1`) 保存して、`.\APIAuth.ps1` を実行します。 
6. **principalName**の入力を求められます。 Active Directory アプリケーションの作成に使用する適切な名前を指定します (例: APIAuth)。 
7. スクリプトが完了すると、プログラムを使用して AD で認証を行うために必要な次の 4 つの値が表示されるので、それらをコピーしておきます。 
   
    **TenantId**、**SubscriptionId**、**ApplicationId**、**Secret** です。
   
    `{TENANT_ID}` として TenantId を、`{CLIENT_ID}` として ApplicationId を、`{CLIENT_SECRET}` として Secret を使用します。
   
   > [!NOTE]
   > 既定のセキュリティ ポリシーにより、PowerShell スクリプトの実行がブロックされる可能性があります。 その場合は、次のコマンドを使用してスクリプトの実行を許可する実行ポリシーを一時的に構成します。
   > 
   > Set-ExecutionPolicy RemoteSigned
   > 
   > 
8. PS コマンドレットは次のようになります。 
   
    ![][3]
9. Microsoft Azure 管理ポータルの **[表示 - 自分の会社が所有するアプリケーション]** で、スクリプトの **principalName** に入力した名前で新しい AD アプリケーションが作成されたことを確認します。
   
    ![][4]

#### <a name="steps-to-get-a-valid-token"></a>有効なトークンを取得する手順
1. 次のパラメーターで API を呼び出します。TENANT\_ID、CLIENT\_ID、CLIENT\_SECRET を実際の値に置き換えます。
   
   * **要求 URL**: *https://login.microsoftonline.com/{TENANT\_ID}/oauth2/token*
   * **HTTP Content-Type ヘッダー** : *application/x-www-form-urlencoded*
   * **HTTP 要求本文**: *grant\_type=client\_credentials&client_id={CLIENT\_ID}&client_secret={CLIENT\_SECRET}&resource=https%3A%2F%2Fmanagement.core.windows.net%2F*
     
     要求の例を次に示します。
     
       POST /{TENANT_ID}/oauth2/token HTTP/1.1   Host: login.microsoftonline.com   Content-Type: application/x-www-form-urlencoded   grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso   urce=https%3A%2F%2Fmanagement.core.windows.net%2F
     
     次は応答の例です。
     
       HTTP/1.1 200 OK   Content-Type: application/json; charset=utf-8   Content-Length: 1234
     
       {"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144   5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_TOKEN}}
     
     この例に含まれている POST パラメーターの URL エンコードの `resource` 値は、実際には `https://management.core.windows.net/` です。 URL エンコード `{CLIENT_SECRET}` にも注意してください。特殊文字が含まれていることがあります。
     
     > [!NOTE]
     > テストとしては、[Fiddler](http://www.telerik.com/fiddler) や [Chrome Postman extension](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) などの HTTP クライアント ツールを使用できます。 
     > 
     > 
2. すべての API 呼び出しに、次の Authorization 要求ヘッダーを含めます。
   
        Authorization: Bearer {ACCESS_TOKEN}
   
    401 ステータス コードが返される場合は、応答本文を確認します。トークンの有効期限が切れている可能性があります。 その場合は、新しいトークンを取得します。

## <a name="using-the-apis"></a>API の使用
有効なトークンが手に入ったので、API 呼び出しを行う準備ができました。

1. 各 API 要求では、前のセクションで取得した有効な期限が切れていないトークンを渡す必要があります。
2. アプリケーションを示す URI にいくつかのパラメーターを組み込む必要があります。 要求 URI は次のようになります。
   
        https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/
        providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/
   
    パラメーターを取得するには、アプリケーション名をクリックして [ダッシュボード] をクリックすると、次のようなページに 3 つのパラメーターがすべて表示されます。
   
   * **1** `{subscription-id}`
   * **2** `{app-collection}`
   * **3** `{app-resource-name}`
   * **4** リソース グループ名は、新しく作成したのでない限り **MobileEngagement** です。 
     
     ![Mobile Engagement API の URI パラメーター][2]

> [!NOTE]
> <br/>
> 
> 1. API ルート アドレスは以前の API 用であるため、無視してください。<br/>
> 2. Azure クラシック ポータルを使用してアプリを作成した場合は、アプリケーション名自体とは異なるアプリケーション リソース名を使用する必要があります。 Azure ポータルでアプリを作成した場合は、アプリ名自体を使用する必要があります (アプリケーション リソース名と、新しいポータルで作成したアプリのアプリ名に違いはありません)。  
> 
> 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication/azure-module.png
[2]: ./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png
[3]: ./media/mobile-engagement-api-authentication/ps-cmdlets.png
[4]: ./media/mobile-engagement-api-authentication/ad-app-creation.png






<!--HONumber=Feb17_HO3-->


