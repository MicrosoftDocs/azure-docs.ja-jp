---
title: テンプレートを使用した Web アプリのデプロイ - Azure Cosmos DB
description: Azure Resource Manager テンプレートを使用して、Azure Cosmos DB アカウント、Azure App Service Web Apps、サンプル Web アプリケーションをデプロイする方法について説明します。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: sngun
ms.openlocfilehash: 1a63de4642850e425cc785e0d89197178ceec1a6
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54041018"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-web-apps-using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用した Azure Cosmos DB と Azure App Service Web Apps のデプロイ
このチュートリアルでは、Azure Resource Manager テンプレートを使用して、[Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)、[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) Web アプリ、サンプル Web アプリケーションをデプロイおよび統合する方法について説明します。

Azure Resource Manager テンプレートを使用して、Azure リソースのデプロイと構成を簡単に自動化できます。  このチュートリアルでは、Web アプリケーションをデプロイし、Azure Cosmos DB アカウントの接続情報を自動的に構成する方法を説明します。

このチュートリアルを完了すると、次の項目について説明できるようになります。  

* Azure Resource Manager テンプレートを使用して、Azure Cosmos DB アカウントと Azure App Service の Web アプリをデプロイおよび統合する方法
* Azure Resource Manager テンプレートを使用して、Azure Cosmos DB アカウント、App Service Web Apps の Web アプリ、WebDeploy アプリケーションをデプロイおよび統合する方法

<a id="Prerequisites"></a>

## <a name="prerequisites"></a>前提条件
> [!TIP]
> このチュートリアルは、Azure Resource Manager テンプレートや JSON の使用経験があるユーザーを対象にしているわけではありませんが、参照するテンプレートやデプロイメント オプションに変更を加える場合は、これらの領域に関する知識が必要になります。
> 
> 

このチュートリアルの手順を実行する前に、Azure サブスクリプションを取得する必要があります。 Azure はサブスクリプション方式のプラットフォームです。  サブスクリプションの入手方法の詳細については、[購入オプション](https://azure.microsoft.com/pricing/purchase-options/)、[メンバー オファー](https://azure.microsoft.com/pricing/member-offers/)、または[無料試用版](https://azure.microsoft.com/pricing/free-trial/)に関するページをご覧ください。

## <a id="CreateDB"></a>手順 1: テンプレート ファイルをダウンロードする
このチュートリアルに必要なサンプル ファイルをダウンロードすることから始めましょう。

1. [Azure Cosmos DB アカウント と Web Apps の作成およびデモ アプリケーションのデプロイのサンプル](https://portalcontent.blob.core.windows.net/samples/DocDBWebsiteTodo.json) テンプレートを、ローカル フォルダー (C:\Azure Cosmos DBTemplates など) にダウンロードします。 このテンプレートでは、Azure Cosmos DB アカウント、App Service Web アプリ、Web アプリケーションをデプロイします。  また、Azure Cosmos DB アカウントに接続するように Web アプリケーションを自動的に構成します。
2. [Azure Cosmos DB アカウントと Web Apps の作成のサンプル](https://portalcontent.blob.core.windows.net/samples/DocDBWebSite.json) テンプレートを、ローカル フォルダー (C:\Azure Cosmos DBTemplates など) にダウンロードします。 このテンプレートでは、Azure Cosmos DB アカウントと App Service Web アプリをデプロイし、サイトのアプリケーション設定を変更して Azure Cosmos DB の接続情報を簡単に表示できるようにします。ただし、Web アプリケーションは含まれていません。  

<a id="Build"></a>

## <a name="step-2-deploy-the-azure-cosmos-db-account-app-service-web-app-and-demo-application-sample"></a>手順 2: Azure Cosmos DB アカウント、App Service Web アプリ、デモ アプリケーションのサンプルをデプロイする
では、最初のテンプレートをデプロイしましょう。

> [!TIP]
> このテンプレートでは、次のテンプレートで入力する Web アプリ名と Azure Cosmos DB アカウント名が a) 有効であり、b) 使用可能であることを検証しません。  デプロイメントの送信前に、指定する予定の名前の可用性を確認しておくことを強くお勧めします。
> 
> 

1. [Azure Portal](https://portal.azure.com) にログインし、[New] (新規) をクリックし、「テンプレートのデプロイ」を検索します。
    ![テンプレートのデプロイメント UI のスクリーンショット](./media/create-website/TemplateDeployment1.png)
2. [テンプレートのデプロイ] を選択し、**[作成]**![テンプレートのデプロイ UI のスクリーンショット](./media/create-website/TemplateDeployment2.png) をクリックします
3. **[テンプレートの編集]** をクリックし、DocDBWebsiteTodo.json テンプレート ファイルの内容を貼り付けて、**[保存]** をクリックします。
   ![テンプレートのデプロイメント UI のスクリーンショット](./media/create-website/TemplateDeployment3.png)
4. **[パラメーターの編集]** をクリックし、必須パラメーターの値を指定して、**[OK]** をクリックします。  パラメーターは、次のとおりです。
   
   1. SITENAME: App Service Web アプリの名前を指定します。これは、Web アプリへのアクセスに使用する URL を作成するときに使用されます (たとえば、"mydemodocdbwebapp" を指定した場合、Web アプリへのアクセスに使用する URL は mydemodocdbwebapp.azurewebsites.net になります)。
   2. HOSTINGPLANNAME: 作成する App Service ホスティング プランの名前を指定します。
   3. LOCATION: Azure Cosmos DB リソースと Web アプリ リソースを作成する Azure の場所を指定します。
   4. DATABASEACCOUNTNAME: 作成する Azure Cosmos DB アカウントの名前を指定します。   
      
      ![テンプレートのデプロイメント UI のスクリーンショット](./media/create-website/TemplateDeployment4.png)
5. 既存のリソース グループを選択するか、名前を指定して新しいリソース グループを作成し、リソース グループの場所を選択します。

    ![テンプレートのデプロイメント UI のスクリーンショット](./media/create-website/TemplateDeployment5.png)
6. **[法律条項を確認してください]**、**[購入]**、**[作成]** の順にクリックして、デプロイを開始します。  **[ダッシュボードにピン留めする]** を選択すると、Azure ポータルのホーム ページで生成されたデプロイメントが見つけやすくなります。
   ![テンプレートのデプロイメント UI のスクリーンショット](./media/create-website/TemplateDeployment6.png)
7. デプロイメントが完了したら、リソース グループ ウィンドウが開きます。
   ![リソース グループ ウィンドウのスクリーンショット](./media/create-website/TemplateDeployment7.png)  
8. アプリケーションを使用するには、その Web アプリの URL にアクセスします (上の例では URL は http://mydemodocdbwebapp.azurewebsites.net))。  次のような Web アプリケーションが表示されます。
   
   ![Todo アプリケーションのサンプル](./media/create-website/image2.png)
9. ここで、Web アプリでいくつかのタスクを作成してから、Azure ポータルのリソース グループ ウィンドウに戻ります。 リソースの一覧で Azure Cosmos DB アカウント リソースをクリックし、**データ エクスプローラー**をクリックします。
10. 既定のクエリである "SELECT * FROM c" を実行し、その結果を調べます。  このクエリでは、前の手順 7. で作成した Todo 項目の JSON 表現を取得しました。  クエリを自由に試してください。たとえば、SELECT * FROM c WHERE c.isComplete = true を実行してみると、完了としてマークされているすべての Todo 項目が返されます。
11. Azure Cosmos DB ポータルの操作やサンプル Todo アプリケーションの変更を自由に試してください。  準備ができたら、別のテンプレートをデプロイしましょう。

<a id="Build"></a> 

## <a name="step-3-deploy-the-document-account-and-web-app-sample"></a>手順 3: Document アカウントと Web アプリのサンプルのデプロイ
では、2 番目のテンプレートをデプロイしましょう。  このテンプレートは、アカウント エンドポイントやマスター キーなどの Azure Cosmos DB 接続情報を、アプリケーション設定またはカスタム接続文字列として Web アプリに挿入する方法を理解するのに役立ちます。 たとえば、Azure Cosmos DB アカウントを使用してデプロイする独自の Web アプリケーションがあり、デプロイ時に接続情報を自動的に設定する場合があります。

> [!TIP]
> このテンプレートでは、ここで入力する Web アプリ名と Azure Cosmos DB アカウント名が a) 有効であり、b) 使用可能であることを検証しません。  デプロイメントの送信前に、指定する予定の名前の可用性を確認しておくことを強くお勧めします。
> 
> 

1. [Azure Portal](https://portal.azure.com) で、[新規] をクリックし、「テンプレートのデプロイ」を検索します。
    ![テンプレートのデプロイメント UI のスクリーンショット](./media/create-website/TemplateDeployment1.png)
2. [テンプレートのデプロイ] を選択し、**[作成]**![テンプレートのデプロイ UI のスクリーンショット](./media/create-website/TemplateDeployment2.png) をクリックします
3. **[テンプレートの編集]** をクリックし、DocDBWebSite.json テンプレート ファイルの内容を貼り付けて、**[保存]** をクリックします。
   ![テンプレートのデプロイメント UI のスクリーンショット](./media/create-website/TemplateDeployment3.png)
4. **[パラメーターの編集]** をクリックし、必須パラメーターの値を指定して、**[OK]** をクリックします。  パラメーターは、次のとおりです。
   
   1. SITENAME: App Service Web アプリの名前を指定します。これは、Web アプリへのアクセスに使用する URL を作成するときに使用されます (たとえば、"mydemodocdbwebapp" を指定した場合、Web アプリへのアクセスに使用する URL は mydemodocdbwebapp.azurewebsites.net になります)。
   2. HOSTINGPLANNAME: 作成する App Service ホスティング プランの名前を指定します。
   3. LOCATION: Azure Cosmos DB リソースと Web アプリ リソースを作成する Azure の場所を指定します。
   4. DATABASEACCOUNTNAME: 作成する Azure Cosmos DB アカウントの名前を指定します。   
      
      ![テンプレートのデプロイメント UI のスクリーンショット](./media/create-website/TemplateDeployment4.png)
5. 既存のリソース グループを選択するか、名前を指定して新しいリソース グループを作成し、リソース グループの場所を選択します。

    ![テンプレートのデプロイメント UI のスクリーンショット](./media/create-website/TemplateDeployment5.png)
6. **[法律条項を確認してください]**、**[購入]**、**[作成]** の順にクリックして、デプロイを開始します。  **[ダッシュボードにピン留めする]** を選択すると、Azure ポータルのホーム ページで生成されたデプロイメントが見つけやすくなります。
   ![テンプレートのデプロイメント UI のスクリーンショット](./media/create-website/TemplateDeployment6.png)
7. デプロイメントが完了したら、リソース グループ ウィンドウが開きます。
   ![リソース グループ ウィンドウのスクリーンショット](./media/create-website/TemplateDeployment7.png)  
8. リソースの一覧で Web アプリのリソースをクリックし、**[アプリケーション設定]**![リソース グループのスクリーンショット](./media/create-website/TemplateDeployment9.png) をクリックします  
9. Azure Cosmos DB エンドポイントとそれぞれの Azure Cosmos DB マスター キーに関するアプリケーション設定がどのように表示されているのかを確認します。

    ![アプリケーションの設定のスクリーンショット](./media/create-website/TemplateDeployment10.png)  
10. Azure Portal の操作を引き続き確認したり、Azure Cosmos DB の[サンプル](https://go.microsoft.com/fwlink/?LinkID=402386)に従って独自の Azure Cosmos DB アプリケーションを作成したり、自由に試してみてください。

<a name="NextSteps"></a>

## <a name="next-steps"></a>次の手順
お疲れさまでした。 Azure Resource Manager テンプレートを使用して、Azure Cosmos DB、App Service Web アプリ、サンプル Web アプリケーションをデプロイしました。

* Azure Cosmos DB の詳細については、[こちら](https://azure.microsoft.com/services/cosmos-db/)をご覧ください。
* Azure App Service Web Apps の詳細については、 [ここ](https://go.microsoft.com/fwlink/?LinkId=325362)をクリックしてください。
* Azure リソース マネージャーのテンプレートの詳細については、 [ここ](https://msdn.microsoft.com/library/azure/dn790549.aspx)をクリックしてください。

## <a name="whats-changed"></a>変更内容
* Web サイトから App Service に変更するためのガイドについては、次を参照してください。[Azure App Service と既存の Azure サービス](https://go.microsoft.com/fwlink/?LinkId=529714)

> [!NOTE]
> Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、「[Azure App Service アプリケーションの作成](https://go.microsoft.com/fwlink/?LinkId=523751)」を参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
> 
> 

