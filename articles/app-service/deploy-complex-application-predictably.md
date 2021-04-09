---
title: ARM で予測したアプリを展開する
description: Azure リソース管理テンプレートと PowerShell スクリプトを使用して、複数の Azure App Service アプリを 1 つのユニットとして予測どおりにデプロイする方法について説明します。
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 01/06/2016
ms.custom: seodec18
ms.openlocfilehash: aec23c28e075dd38fa65f1315f9abd9e21cdc9cb
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104951472"
---
# <a name="provision-and-deploy-microservices-predictably-in-azure"></a>Azure でマイクロサービスを予測どおりにデプロイする
このチュートリアルでは、[Azure App Service](https://azure.microsoft.com/services/app-service/) の[マイクロサービス](https://en.wikipedia.org/wiki/Microservices)で構成されるアプリケーションを、JSON リソース グループ テンプレートと PowerShell スクリプトを使用して、1 つのユニットとして予測どおりにプロビジョニングしてデプロイする方法を示します。 

高度に分離されたマイクロサービスで構成される拡張性の高いアプリケーションのプロビジョニングとデプロイを成功させるには、再現性と予測性が非常に重要です。 [Azure App Service](https://azure.microsoft.com/services/app-service/) を使用して、Web アプリ、モバイル バック エンド、および API アプリを含むマイクロサービスを作成できます。 [Azure Resource Manager](../azure-resource-manager/management/overview.md) を使用して、すべてのマイクロサービスを、データベースやソース管理設定などのリソースの依存関係と共に 1 つのユニットとして管理できます。 さらに、このようなアプリケーションを、JSON テンプレートと単純な PowerShell スクリプトを使用してデプロイすることもできます。 

## <a name="what-you-will-do"></a>学習内容
このチュートリアルでは、次のものを含むアプリケーションをデプロイします。

* 2 つの App Service アプリ (つまり 2 つのマイクロサービス)
* バックエンド SQL Database
* アプリ設定、接続文字列、ソース管理
* Application Insights、アラート、自動スケールの設定

## <a name="tools-you-will-use"></a>使用するツール
このチュートリアルでは、次のツールを使用します。 ここはツールについて包括的に説明するための場所ではないため、簡単な説明と詳細情報へのリンクを紹介するにとどめて、エンド ツー エンドのシナリオに準拠します。 

### <a name="azure-resource-manager-templates-json"></a>Azure リソース マネージャー テンプレート (JSON)
たとえば、Azure App Service でアプリを作成するたびに、Azure Resource Manager では、JSON テンプレートを使用して、リソース グループ全体とコンポーネント リソースが作成されます。 [Azure Marketplace](../marketplace/index.yml) の複雑なテンプレートには、データベース、ストレージ アカウント、App Service プラン、アプリ自体、アラート ルール、アプリ設定、自動スケールの設定などを含めることができます。また、これらのすべてのテンプレートは、PowerShell を介して利用できます。 Azure リソース マネージャー テンプレートの詳細については、 [Azure リソース マネージャーのテンプレートの作成](../azure-resource-manager/templates/template-syntax.md)

### <a name="azure-sdk-26-for-visual-studio"></a>Azure SDK 2.6 for Visual Studio
最新の SDK では、JSON エディターにおけるリソース マネージャー テンプレートのサポートが強化されました。 これを使用すると、リソース グループ テンプレートを一からすばやく作成したり、既存の JSON テンプレート (ダウンロードしたギャラリー テンプレートなど) を開いて変更したりできます。また、パラメーター ファイルを設定したり、Azure リソース グループのソリューションから直接リソース グループをデプロイしたりすることもできます。

詳細については、 [Azure SDK 2.6 for Visual Studio](https://azure.microsoft.com/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/)に関するページをご覧ください。

### <a name="azure-powershell-080-or-later"></a>Azure PowerShell 0.8.0 以降
Version 0.8.0 以降の Azure PowerShell のインストールには、Azure モジュールに加えて Azure リソース マネージャー モジュールが含まれています。 この新しいモジュールを使用して、リソース グループをデプロイするスクリプトを作成できます。

詳細については、 [Azure リソース マネージャーでの Windows PowerShell の使用](../azure-resource-manager/management/manage-resources-powershell.md)

### <a name="azure-resource-explorer"></a>Azure Resource Explorer
この[プレビュー ツール](https://resources.azure.com)を使用して、サブスクリプション内のすべてのリソース グループの JSON 定義と個々のリソースを調べることができます。 このツールでは、リソースの JSON 定義の編集、リソースの階層全体の削除、新しいリソースの作成を実行できます。  このツールですぐに利用できる情報は、特定の種類のリソースに設定する必要のあるプロパティ、適切な値などを示すため、テンプレートの作成に非常に役立ちます。[Azure Portal](https://portal.azure.com/) でリソース グループを作成し、エクスプローラー ツールで JSON 定義を調べてリソース グループのテンプレート化に利用できます。

### <a name="deploy-to-azure-button"></a>[Azure にデプロイ] ボタン
ソース管理に GitHub を使用する場合、README.MD に [[Azure にデプロイ]](../azure-resource-manager/templates/deploy-to-azure-button.md) ボタンを配置できます。これにより、設定することなく Azure にデプロイする UI が実現します。 任意の単純なアプリでこの処理が可能ですが、これを拡張すると、リポジトリのルートに azuredeploy.json ファイルを配置することでリソース グループ全体をデプロイできるようになります。 この JSON ファイルは、リソース グループ テンプレートを含んでおり、[Azure にデプロイ] ボタンでリソース グループを作成するために使用されます。 例については、このチュートリアルで使用する [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) サンプルを参照してください。

## <a name="get-the-sample-resource-group-template"></a>リソース グループ テンプレートのサンプルを入手する
それでは早速本題に入りましょう。

1. [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) App Service のサンプルに移動します。
2. readme.md の **[Azure にデプロイ]** をクリックします。
3. [deploy-to-azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure-appservice-samples%2FToDoApp%2Fmaster%2Fazuredeploy.json) サイトが表示され、デプロイメント パラメーターの入力が求められます。 ほとんどのフィールドにはリポジトリ名が設定されていますが、一部にランダムな文字列が設定されていることに注意してください。 必要に応じてすべてのフィールドを変更できますが、入力する必要があるのは SQL Server の管理用のログインとパスワードだけです。 **[次へ]** をクリックします。
   
   ![deploy-to-azure サイトの入力デプロイ パラメーターが示されています。](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)
4. 次に、 **[デプロイ]** をクリックしてデプロイメント プロセスを開始します。 プロセスの実行が完了したら、 http://todoapp*XXXX*.azurewebsites.net リンクをクリックし、デプロイされたアプリケーションを参照します。 
   
   ![アプリケーションのデプロイ プロセスが示されています。](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)
   
   UI は、初めて参照するときに少し時間がかかります。これは、アプリがちょうど起動中であるためですが、十分な機能を備えたアプリケーションと考えてください。
5. [デプロイ] ページに戻り、 **[管理]** リンクをクリックすると、Azure Portal に新しいアプリケーションが表示されます。
6. **[要点]** ボックスの一覧で、リソース グループのリンクをクリックします。 **[外部プロジェクト]** で、アプリが既に GitHub リポジトリに接続されていることにも注意してください。 
   
   ![[要点] ボックスのセクションのリソース グループのリンクが示されています。](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)
7. リソース グループのブレードを見ると、リソース グループ内には 2 つのアプリと 1 つの SQL Database が既に存在します。
   
   ![リソース グループで使用可能なリソースが示されています。](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)

このわずか数分間に、2 つのマイクロサービス アプリケーションが完全にデプロイされ、コンポーネント、依存関係、設定、データベース、継続的な発行が、Azure リソース マネージャーで自動化されたオーケストレーションによって設定されることを確認しました。 これはすべて、次の 2 つで行われました。

* [Azure にデプロイ] ボタン
* リポジトリのルートにある azuredeploy.json

これと同じアプリケーションは、何度でもデプロイでき、毎回まったく同じ構成にすることができます。 この手法が持つ反復性と予測可能性によって、拡張性の高いアプリケーションを簡単に自信を持ってデプロイできます。

## <a name="examine-or-edit-azuredeployjson"></a>AZUREDEPLOY.JSON を確認 (または編集) する
次に、GitHub リポジトリがどのように設定されたかを見てみましょう。 Azure .NET SDK で JSON エディターを使用します。[Azure .NET SDK 2.6](https://azure.microsoft.com/downloads/) をまだインストールしていない場合は、今すぐインストールしてください。

1. お気に入りの git ツールを使用して [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) リポジトリを複製します。 次のスクリーンショットでは、Visual Studio 2013 のチーム エクスプローラーでこれを実行します。
   
   ![git ツールを使用して ToDoApp リポジトリを複製する方法が示されています。](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)
2. リポジトリのルートから、azuredeploy.json を Visual Studio で開きます。 [JSON アウトライン] ウィンドウが表示されない場合は、Azure .NET SDK をインストールする必要があります。
   
   ![Visual Studio の [JSON アウトライン] ウィンドウが示されています。](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

JSON 形式について詳しく説明する予定はありませんが、「 [その他のリソース](#resources) 」セクションには、リソース グループ テンプレートの言語を習得するためのリンクが用意されています。 ここでは、アプリをデプロイするための独自のカスタム テンプレートを作成するのに役立つ、興味深い機能のみについて説明します。

### <a name="parameters"></a>パラメーター
パラメーター セクションを見ると、そこにあるパラメーターのほとんどが、 **[Azure にデプロイ]** ボタンによって入力が求められる内容であることがわかります。 **[Azure にデプロイ]** ボタンで表示されるサイトでは、入力用の UI に、azuredeploy.json で定義されたパラメーターを使用して値が設定されます。 これらのパラメーターは、リソース名、プロパティの値など、リソースの定義全体で使用されます。

### <a name="resources"></a>リソース
リソース ノードを見ると、SQL Server のインスタンス、App Service プラン、2 つのアプリの、4 つの最上位レベルのリソースが定義されていることがわかります。 

#### <a name="app-service-plan"></a>App Service プラン
まず、JSON の単純なルート レベルのリソースから始めましょう。 [JSON アウトライン] で、 **[hostingPlanName]** という名前の App Service プランをクリックして、対応する JSON コードを強調表示します。 

![JSON コードの [hostingPlanName] セクションが示されています。](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

`type` 要素で App Service プラン (かなり前にはサーバー ファームと呼ばれていました) の文字列を指定し、その他の要素とプロパティは、JSON ファイルで定義されているパラメーターを使用して設定されます。このリソースには、入れ子になったリソースは存在しません。

> [!NOTE]
> `apiVersion` の値によって JSON リソース定義を使用する REST API のバージョンが Azure に指示され、`{}` 内でのリソースの形式に影響する可能性があることにも注意してください。 
> 
> 

#### <a name="sql-server"></a>SQL Server
次に、[JSON アウトライン] で **SQLServer** という名前の SQL Server リソースをクリックします。

![[JSON アウトライン] の SQLServer という名前の SQL Server リソースが示されています。](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)

強調表示された JSON コードに関して、次の点に注意してください。

* パラメーターの使用により、作成されたリソースは互いに一貫性を持つように名前が付けられ、構成されます。
* SQL Server リソースには、入れ子になったリソースが 2 つあり、それぞれで `type` に異なる値が設定されています。
* データベースとファイアウォール規則が定義されている、`“resources”: […]` 内で入れ子になったリソースには、ルート レベルの SQL Server リソースのリソース ID を指定する `dependsOn` 要素があります。 これは、Azure リソース マネージャーに対して、"このリソースを作成する前に、その他のリソースが既に存在する必要があり、その他のリソースがテンプレートで定義されている場合は、そのリソースを最初に作成すること" を指示します。
  
  > [!NOTE]
  > `resourceId()`関数の使用方法の詳細については、「[Azure リソース マネージャーのテンプレートの関数](../azure-resource-manager/templates/template-functions-resource.md#resourceid)」を参照してください。
  > 
  > 
* `dependsOn` 要素のおかげで、Azure リソース マネージャーでは、並行して作成できるリソースと順番に作成する必要があるリソースを認識できます。 

#### <a name="app-service-app"></a>App Service アプリ
次に、少し複雑な実際のアプリ自体を見てみましょう。 [JSON アウトライン] で [variables('apiSiteName')] アプリをクリックして、その JSON コードを強調表示します。 さらに興味深くなっていることに気が付きます。 このために、機能を 1 つずつ説明します。

##### <a name="root-resource"></a>ルート リソース
アプリは、2 つの異なるリソースに依存します。 つまり、Azure Resource Manager でアプリが作成されるのは、App Service プランと SQL Server インスタンスの両方が作成された後だけです。

![App Service プランと SQL Server インスタンスのアプリの依存関係が示されています。](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### <a name="app-settings"></a>アプリケーション設定
アプリ設定は、入れ子になったリソースとしても定義されます。

![JSON コードで入れ子になったリソースとして定義されているアプリ設定が示されています。](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

`config/appsettings` の `properties` 要素には、`"<name>" : "<value>"` 形式のアプリ設定が 2 つ含まれています。

* `PROJECT` は、Visual Studio のマルチプロジェクト ソリューションで使用するプロジェクトを Azure のデプロイメントに伝える [KUDU 設定](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) です。 ソース管理の構成方法については後で説明しますが、ToDoApp コードは Visual Studio のマルチ プロジェクト ソリューションに含まれているため、この設定は必要です。
* `clientUrl` は、単に、アプリケーション コードで使用されるアプリ設定です。

##### <a name="connection-strings"></a>Connection strings
接続文字列は、入れ子になったリソースとしても定義されます。

![接続文字列が JSON コードで入れ子になったリソースとしてどのように定義されるかが示されています。](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

`config/connectionstrings` の `properties` 要素では、各接続文字列が `"<name>" : {"value": "…", "type": "…"}` という特定の形式で、名前と値のペアとしても定義されています。 `type` 要素に指定できる値は、`MySql`、`SQLServer`、`SQLAzure`、および `Custom` です。

> [!TIP]
> 接続文字列の種類の最終的な一覧については、Azure PowerShell で次のコマンドを実行します:\[Enum]::GetNames("Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.DatabaseType")
> 
> 

##### <a name="source-control"></a>ソース管理
ソース管理の設定は、入れ子になったリソースとしても定義されます。 Azure リソース マネージャーは、このリソースを使用して継続的な発行 (後で `IsManualIntegration` の注意点をご確認ください) を構成し、さらに、JSON ファイルの処理中にアプリケーション コードのデプロイメントを自動的に開始します。

![ソース管理の設定が、JSON コードで入れ子になったリソースとしてどのように定義されているかが示されています。](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl` と `branch` は、非常に直感的で、Git リポジトリと、発行元となるブランチの名前を指しています。 繰り返しになりますが、これらの情報は入力パラメーターで定義されています。 

`dependsOn` 要素では、`sourcecontrols/web` がアプリのリソース自体に加え、`config/appsettings` と `config/connectionstrings` にも依存していることに注意してください。 これは、 `sourcecontrols/web` が構成されると、Azure のデプロイメント プロセスが、アプリケーション コードのデプロイ、ビルド、開始を自動的に試みるためです。 したがって、この依存関係を挿入すると、アプリケーション コードが実行される前に、アプリケーションから必要なアプリ設定と接続文字列にアクセスできることを確認できます。 

> [!NOTE]
> `IsManualIntegration` が `true` に設定されていることにも注意してください。 このチュートリアルでこのプロパティが必要なのは、実際には GitHub リポジトリを所有していないためです。したがって、実際には、[ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) からの継続的な発行 (Azure へのリポジトリの自動更新のプッシュ) を構成するためのアクセス許可を Azure に付与することはできません。 [Azure Portal](https://portal.azure.com/)で事前に所有者の GitHub の資格情報を構成している場合のみ、指定されたリポジトリの既定値 `false` を使用できます。 つまり、以前に、ユーザーの資格情報を使用して、[Azure Portal](https://portal.azure.com/) で任意のアプリのソース管理に GitHub または BitBucket を設定した場合、Azure では資格情報が記憶されるため、今後は GitHub または BitBucket からアプリをデプロイするたびにその資格情報が使用されます。 ただし、まだ設定していない場合は、Azure Resource Manager がアプリのソース管理の設定を構成しようとしたときに、JSON テンプレートのデプロイメントが失敗します。その原因は、リポジトリの所有者の資格情報で GitHub や BitBucket にログインできないからです。
> 
> 

## <a name="compare-the-json-template-with-deployed-resource-group"></a>JSON テンプレートとデプロイ済みのリソース グループを比較する
ここでは、[Azure Portal](https://portal.azure.com/)内のアプリのすべてのブレードを確認できます。別のツールでも詳細をある程度まで確認できます。 [Azure Resource Explorer](https://resources.azure.com) プレビュー ツールに移動します。Azure バックエンドに実際に存在するサブスクリプション内のすべてのリソースグループの JSON 表現が表示されます。 また、Azure のリソース グループの JSON 階層が、リソース グループの作成に使用されたテンプレート ファイル内の階層にどのように対応しているかも確認できます。

たとえば、 [Azure リソース エクスプローラー](https://resources.azure.com) ツールに移動し、エクスプローラーでノードを展開すると、リソース グループと、それぞれのリソースの種類で収集されるルート レベルのリソースが表示されます。

![展開された Azure リソース エクスプローラー ツールで、リソース グループとルート レベルのリソースを表示します。](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

アプリにドリルダウンすると、下のスクリーンショットのように、アプリの構成の詳細を表示できます。

![ドリルダウンして、アプリの構成の詳細を表示します。](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

ここでも、入れ子になったリソースには、JSON テンプレート ファイルのリソースによく似た階層が必要です。また、JSON ウィンドウには、アプリ設定、接続文字列などが正しく反映されていることがわかります。 ここに設定が存在しない場合は、JSON ファイルに問題があることを示す可能性があり、JSON テンプレート ファイルのトラブルシューティングに役立ちます。

## <a name="deploy-the-resource-group-template-yourself"></a>リソース グループ テンプレートを自分でデプロイする
**[Azure へのデプロイ]** ボタンは優れていますが、azuredeploy.json を GitHub に既にプッシュしている場合にのみ、azuredeploy.json でリソース グループ テンプレートをデプロイできます。 Azure .NET SDK には、ローカル コンピューターから直接 JSON テンプレート ファイルをデプロイするためのツールも用意されています。 これを行うには、次の手順に従います。

1. Visual Studio で、 **[ファイル]**  >  **[新規]**  >  **[プロジェクト]** をクリックします。
2. **[Visual C#]**  >  **[クラウド]**  >  **[Azure リソース グループ]** をクリックした後、 **[OK]** をクリックします。
   
   ![Azure .NET SDK で Azure リソース グループとして新しいプロジェクトを作成します。](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)
3. **[Azure テンプレートの選択]** で、 **[空白のテンプレート]** を選択し、 **[OK]** をクリックします。
4. azuredeploy.json を新しいプロジェクトの **[テンプレート]** フォルダーにドラッグします。
   
   ![azuredeploy.json ファイルをプロジェクトの [テンプレート] フォルダーにドラッグした結果が示されています。](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)
5. ソリューション エクスプローラーで、コピーした azuredeploy.json を開きます。
6. デモで使用する目的のみで、 **[リソースの追加]** をクリックして、標準の Application Insights リソースをいくつか JSON ファイルに追加してみます。 JSON ファイルのデプロイのみに興味がある場合は、デプロイの手順に進んでください。
   
   ![JSON ファイルに標準的な Application insights リソースを追加するために使用できる [リソースの追加] ボタンが示されています。](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)
7. **[Web アプリの Application Insights]** を選択し、既存の App Service プランとアプリが選択されていることを確認して、 **[追加]** をクリックします。
   
   ![Web アプリ、名前、App Service プラン、および Web アプリに対する Application Insights の選択が示されています。](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)
   
   リソースとその機能に応じて、App Service プランまたはアプリへの依存関係を持つ新しいリソースがいくつか表示されます。 これらのリソースは既存の定義では有効になっていないため、変更します。
   
   ![App Service プランまたはアプリに依存関係がある新しいリソースを表示します。](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)
8. [JSON アウトライン] で、 **appInsights AutoScale** をクリックし、その JSON コードを強調表示します。 これは App Service プランのスケーリング設定です。
9. 強調表示された JSON コードで、`location` プロパティと `enabled` プロパティを見つけて次のように設定します。
   
   ![appInsights の自動スケールの JSON コード内の location プロパティと enabled プロパティ、およびそれらに設定する必要がある値が示されています。](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)
10. [JSON アウトライン] で、 **CPUHigh appInsights** をクリックし、その JSON コードを強調表示します。 これはアラートです。
11. `location` プロパティと `isEnabled` プロパティを見つけて次のように設定します。 他の 3 つのアラート (紫色の電球) についても同様の操作を行います。
    
    ![CPUHigh appInsights JSON コードの location プロパティと isEnabled プロパティ、およびそれらに設定する必要がある値が示されています。](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)
12. これで、デプロイする準備が整いました。 プロジェクトを右クリックして **[デプロイ]**  > **New [デプロイ]ment** に関するページをご覧ください。
    
    ![新しいプロジェクトをデプロイする方法が示されています。](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)
13. ログインしていない場合は、Azure アカウントにログインします。
14. サブスクリプション内の既存のリソース グループを選択するか、 **[azuredeploy.json]** を選択してから **[パラメーターの編集]** をクリックして新しいリソース グループを作成します。
    
    ![azuredeploy.json ファイルのパラメーターを編集する方法が示されています。](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)
    
    これで、便利なテーブルで、テンプレート ファイルで定義されているすべてのパラメーターを編集できるようになりました。 既定値を定義するパラメーターには既定値が既に設定されています。また、使用できる値の一覧を定義するパラメーターはドロップダウン リストとして表示されます。
    
    ![使用できる値のリストをドロップダウン リストとして定義するパラメーターが示されています。](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)
15. 空のパラメーターすべてに値を設定します。 **[repoUrl]** には [GitHub リポジトリの ToDoApp](https://github.com/azure-appservice-samples/ToDoApp.git) のアドレスを使用します。 その後、 **[保存]** をクリックします。
    
    ![azuredeploy.json ファイルに新しく入力されたパラメーターが示されています。](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)
    
    > [!NOTE]
    > 自動スケールは、**Standard** レベル以上で提供される機能です。プラン レベルのアラートは、**Basic** レベル以上で提供される機能です。AppInsights の新しいリソースすべてが点灯されたことがわかるように、**sku** パラメーターを **Standard** または **Premium** に設定する必要があります。
    > 
    > 
16. **[デプロイ]** をクリックします。 **[パスワードの保存]** を選択した場合、パスワードはパラメーター ファイルに **プレーン テキストで** 保存されます。 それ以外の場合は、デプロイメント プロセス中にデータベースのパスワードを入力するように求められます。

これで終了です。 後は、[Azure Portal](https://portal.azure.com/) と [Azure Resource Explorer](https://resources.azure.com) ツールに移動して、JSON でデプロイされたアプリケーションに追加された新しいアラートと自動スケールの設定を表示するだけです。

このセクションの手順では、主に次の操作を実行しました。

1. テンプレート ファイルを準備する
2. テンプレート ファイルに合うパラメーター ファイルを作成する
3. パラメーター ファイルを使用してテンプレート ファイルをデプロイする

最後の手順は、PowerShell コマンドレットで簡単に実行されます。 Visual Studio でアプリケーションをデプロイしたときに実行された内容を確認するには、Scripts\Deploy-AzureResourceGroup.ps1 を開きます。 大量のコードが存在しますが、パラメーター ファイルを使用してテンプレート ファイルをデプロイするために必要な関連するすべてのコードのみを強調表示しています。

![パラメーター ファイルを使用してテンプレート ファイルをデプロイするために使用する必要がある、スクリプト内の関連するコードが示されています。](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

最後のコマンドレット `New-AzureResourceGroup`で、実際にアクションが実行されます。 これまでの内容から、ツールを利用すれば、クラウド アプリケーションを予測どおりにデプロイするのは比較的簡単です。 同じパラメーター ファイルを使用して同じテンプレートでコマンドレットを実行するたびに、同じ結果が得られます。

## <a name="summary"></a>まとめ
DevOps では、反復性と予測可能性が、マイクロサービスで構成される拡張性の高いアプリケーションのデプロイメントに成功するための鍵になります。 このチュートリアルでは、Azure リソース マネージャーのテンプレートを使用して、2 つのマイクロサービス アプリケーションを 1 つのリソース グループとして Azure にデプロイしました。 Azure のアプリケーションをテンプレートに変換するために必要な知識を習得することで、そのアプリケーションを予測どおりにプロビジョニングしてデプロイできるようになることを願っています。 

<a name="resources"></a>

## <a name="more-resources"></a>その他のリソース
* [Azure リソース マネージャー テンプレートの言語](../azure-resource-manager/templates/template-syntax.md)
* [Azure リソース マネージャーのテンプレートの作成](../azure-resource-manager/templates/template-syntax.md)
* [Azure Resource Manager テンプレートの関数](../azure-resource-manager/templates/template-functions.md)
* [Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](../azure-resource-manager/templates/deploy-powershell.md)
* [Azure リソース マネージャーでの Windows PowerShell の使用](../azure-resource-manager/management/manage-resources-powershell.md)
* [Azure でのリソース グループのデプロイのトラブルシューティング](../azure-resource-manager/templates/common-deployment-errors.md)

## <a name="next-steps"></a>次のステップ

この記事でデプロイしたリソース タイプの JSON 構文およびプロパティについては、次をご覧ください。

* [Microsoft.Sql/servers](/azure/templates/microsoft.sql/servers)
* [Microsoft.Sql/servers/databases](/azure/templates/microsoft.sql/servers/databases)
* [Microsoft.Sql/servers/firewallRules](/azure/templates/microsoft.sql/servers/firewallrules)
* [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)
* [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)
* [Microsoft.Web/sites/slots](/azure/templates/microsoft.web/sites/slots)
* [Microsoft.Insights/autoscalesettings](/azure/templates/microsoft.insights/autoscalesettings)