---
title: "Azure Function App の設定の構成 | Microsoft Docs"
description: "Azure Function App の設定を構成する方法について説明します。"
services: 
documentationcenter: .net
author: rachelappel
manager: erikre
editor: 
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 10/28/2016
ms.author: rachelap
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: b7334f68e4526b716210b28388afcd0ee6e48940
ms.lasthandoff: 03/29/2017


---
# <a name="how-to-configure-azure-function-app-settings"></a>Azure Function App の設定の構成方法
## <a name="settings-overview"></a>設定の概要
ポータルの左下隅にある **[Function App の設定]** リンクをクリックすると、Azure Function App の設定を管理できます。 Azure Function App の設定は、アプリ内のすべての関数に適用されます。

1. [Azure Portal](http://portal.azure.com) に移動し、Azure アカウントでサインインします。
2. ポータルの左下隅にある **[Function App の設定]** をクリックします。 クリックすると、いくつかの構成オプションが表示されます。 

![Azure Function App の設定](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

## <a name="develop"></a>開発
### <a name="app-service-editor"></a>App Service Editor
App Service Editor は、Json 構成ファイルとコード ファイルを変更するために使用できる高度なポータル内エディターです。 このオプションを選択すると、別のブラウザー タブが基本的なエディターで起動します。 これにより、GitHub との統合、コードの実行とデバッグ、関数アプリの設定変更を行うことができます。

![App Service Editor](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

### <a name="application-settings"></a>アプリケーションの設定
環境変数、Framework バージョン、リモート デバッグ、アプリケーション設定、接続文字列、既定のドキュメントなどを管理します。これらの設定は、Function App に固有です。 

アプリケーション設定を構成するには、**[アプリケーション設定の構成]** リンクをクリックします。 

![[アプリケーション設定の構成]](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-settings.png)

### <a name="dev-console"></a>開発者コンソール
Azure Functions のポータル内コンソールを使用して、DOS 形式のコマンドを実行できます。 一般的なコマンドには、ディレクトリやファイルの作成、ナビゲーション、バッチ ファイルやスクリプトの実行などがあります。 

> [!NOTE]
> スクリプトをアップロードできます。ただし、最初に Azure Function の **[詳細設定]** で FTP クライアントを構成する必要があります。
> 
> 

ポータル内コンソールを開くには、**[開発者コンソールを開く]** をクリックします。

![Function App のメモリ サイズの構成](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

> [!NOTE]
> コンソールでの作業時にこのような ASCII アートを使用すると、見栄えがよくなります。
> 
> 

## <a name="deploy"></a>デプロイ
### <a name="continuous-integration"></a>継続的インテグレーション
Function App と GitHub、Visual Studio Team Services などを統合できます。

1. **[継続的インテグレーションの構成]** リンクをクリックします。 オプションが表示された **[デプロイ]** ウィンドウが開きます。
2. **[デプロイ]** ウィンドウの **[セットアップ]** をクリックし、オプションが 1 つ表示された **[展開元]** ウィンドウを開きます。**[ソースの選択]** をクリックして、使用できるソースを表示します。 
3. Visual Studio Team Services、OneDrive、ローカルの Git リポジトリ、GitHub、Bitbucket、DropBox、外部リポジトリのいずれかをクリックして、使用できるデプロイ ソースを選択します。 
   
    ![Function App の CI の構成](./media/functions-how-to-use-azure-function-app-settings/configure-function-ci.png)
4. 各種のデプロイ ソースによって求められる資格情報とその他の情報を入力します。 求められる資格情報とその他の情報は、選択したソースによって多少異なることがあります。 

CI をセットアップすると、構成済みのソースにプッシュした連結コードは自動的にこの Function App にデプロイされます。

### <a name="kudu"></a>Kudu
Kudu を使用すると、Function App の高度な管理機能にアクセスできます。

Kudu を開くには、**[Kudu に移動]** をクリックします。 Kudu Web 管理機能を持つまったく新しいブラウザー ウィンドウが開きます。

> [!NOTE]
> または次に示すように、関数の URL に "scm" を挿入して **Kudu** を起動することもできます。```https://<YourFunctionName>.scm.azurewebsites.net/```
> 
> 

Kudu の Web ページから、システム情報、アプリケーション設定、環境変数、HTTP ヘッダー、サーバー変数などを表示したり管理したりできます。

![Kudu の構成](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

## <a name="manage-app-service-settings"></a>管理: App Service の設定
他の App Service インスタンスのように Function App を管理します。 このオプションでは、これまでに説明したすべての設定に加えて、さらにいくつかの設定を行うことができます。  

詳細設定を開くには、**[詳細設定]** リンクをクリックします。 

![App Service の設定の構成](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-settings.png)

App Service の各設定を構成する方法の詳細については、[Azure App Service の設定の構成](../app-service-web/web-sites-configure.md)に関するページを参照してください。

## <a name="manage-cors"></a>管理: CORS
通常はセキュリティ上の理由から、ブラウザーからの Ajax 呼び出しなど、外部ソースからホスト (ドメイン) への呼び出しは許可されていません。 そうでなければ、悪意のあるコードが送信され、バックエンドで実行される可能性があります。 最も安全なルートは、信頼できるいくつかの独自のソースを除いて、すべてのコードのソースをブラックリストに登録することです。 クロス オリジン リソース共有 (CORS) を構成することにより、Azure Functions のどのソースからの呼び出しを受け入れるかを構成できます。 CORS を使用すると、Azure Function App で関数を呼び出すことができる JavaScript のソースとなるドメインを一覧表示できます。 

1. CORS を構成するには、**[CORS の構成]** リンクをクリックします。 
2. ホワイトリストに登録するドメインを入力します。

![Function App の CORS の構成](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

## <a name="manage-authenticationauthorization"></a>管理: 認証/承認
HTTP トリガーを使用する関数について、呼び出しに認証を必須とすることができます。

1. 認証を構成するには、**[認証の構成]** リンクをクリックします。
2. **[App Service 認証]** ボタンを **[オン]** に切り替えます。

![Function App の CI の構成](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

ほとんどの認証プロバイダーは API キー/クライアント ID とクライアント シークレットを要求しますが、Microsoft アカウントと Facebook のオプションではスコープ (特定の認証資格情報) を定義することもできます。 Active Directory では、高速または高度な構成設定をいくつか設定できます。

特定の認証プロバイダーの構成の詳細については、[Azure App Service での認証の概要](../app-service/app-service-authentication-overview.md)に関するページを参照してください。

## <a name="manage-api-definition"></a>管理: API の定義
クライアントが HTTP によってトリガーされる関数をもっと簡単に使用できるようになります。

1. API を設定するには、**[API メタデータの構成]** をクリックします。 
2. Swagger json ファイルを指す URL を入力します。

![Function App の API の構成](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-apidef.png)

Swagger を使用した API 定義の作成の詳細については、[Azure での API Apps、ASP.NET、Swagger の使用](../app-service-api/app-service-api-dotnet-get-started.md)に関するページを参照してください。

## <a name="daily-usage-quota"></a>毎日の使用量クォータ

Azure Functions では、毎日の利用量クォータを設定することで、プラットフォームの使用量を予想どおりに制限できます。 毎日の利用量クォータに達すると、Function App は停止されます。 利用量クォータに達した結果として停止した Function App は、毎日の利用量クォータの設定と同じ手順で再び有効にすることができます。 利用量クォータの単位は、課金単位であるギガバイト秒 (GB 秒) です。課金モデルの詳細については、[Azure Functions の価格](http://azure.microsoft.com/pricing/details/functions/)に関するページを参照してください。 

![Function App のメモリ サイズの構成](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-quota.png)

## <a name="next-steps"></a>次のステップ
[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


