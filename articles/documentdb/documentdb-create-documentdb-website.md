---
title: "テンプレートを使用して Web アプリをデプロイする - Azure DocumentDB | Microsoft Docs"
description: "Azure リソース マネージャーのテンプレートを使用して DocumentDB アカウント、Azure App Service Web Apps、サンプルの Web アプリケーションをデプロイする方法を説明します。"
services: documentdb, app-service\web
author: h0n
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 087d8786-1155-42c7-924b-0eaba5a8b3e0
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2016
ms.author: hawong
translationtype: Human Translation
ms.sourcegitcommit: a6aadaae2a9400dc62ab277d89d9a9657833b1b7
ms.openlocfilehash: 7bef38eaea90d950c195dee0d741750cf65870df


---
# <a name="deploy-documentdb-and-azure-app-service-web-apps-using-an-azure-resource-manager-template"></a>Azure リソース マネージャーのテンプレートを使用した DocumentDB と Azure App Service Web Apps のデプロイ
このチュートリアルでは、Azure Resource Manager のテンプレートを使用して [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)、[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web アプリ、サンプルの Web アプリケーションをデプロイおよび統合する方法を示します。

Azure Resource Manager テンプレートを使用して、Azure リソースのデプロイと構成を簡単に自動化できます。  このチュートリアルでは、Web アプリケーションをデプロイして、DocumentDB アカウント接続情報を自動的に構成する方法を示します。

このチュートリアルを完了すると、次の項目について説明できるようになります。  

* Azure リソース マネージャーのテンプレートを使用して、DocumentDB アカウントと Azure App Service の Web アプリをデプロイおよび統合する方法
* Azure リソース マネージャーのテンプレートを使用して、DocumentDB アカウント、App Service Web Apps の Web アプリ、Web デプロイ アプリケーションをデプロイおよび統合する方法

<a id="Prerequisites"></a>

## <a name="prerequisites"></a>前提条件
> [!TIP]
> このチュートリアルは、Azure Resource Manager テンプレートや JSON の使用経験があるユーザーを対象にしているわけではありませんが、参照するテンプレートやデプロイメント オプションに変更を加える場合は、これらの領域に関する知識が必要になります。
> 
> 

このチュートリアルの手順を実行する前に、次のものを備えておく必要があります。

* Azure サブスクリプション。 Azure はサブスクリプション方式のプラットフォームです。  サブスクリプションの入手方法の詳細については、[購入オプション](https://azure.microsoft.com/pricing/purchase-options/)、[メンバー プラン](https://azure.microsoft.com/pricing/member-offers/)、または[無料試用版](https://azure.microsoft.com/pricing/free-trial/)に関するページをご覧ください。

## <a name="a-idcreatedbastep-1-download-the-template-files"></a><a id="CreateDB"></a>手順 1: テンプレート ファイルをダウンロードする
このチュートリアルで使用するサンプル ファイルをダウンロードすることから始めましょう。

1. [DocumentDB アカウントと Web Apps の作成およびデモ アプリケーションのデプロイのサンプル](https://portalcontent.blob.core.windows.net/samples/DocDBWebsiteTodo.json)のテンプレートを、ローカルのフォルダー (C:\DocumentDBTemplates など) にダウンロードします。 このテンプレートでは、DocumentDB アカウント、App Service Web アプリ、Web アプリケーションをデプロイします。  また、Web アプリケーションをこの DocumentDB アカウントに接続するように自動的に構成します。
2. [DocumentDB アカウントと Web Apps の作成のサンプル](https://portalcontent.blob.core.windows.net/samples/DocDBWebSite.json)のテンプレートをローカルのフォルダー (C:\DocumentDBTemplates など) にダウンロードします。 このテンプレートでは、DocumentDB アカウントと App Service Web アプリをデプロイし、サイトのアプリケーション設定を変更して DocumentDB の接続情報を容易に表示できるようにしますが、Web アプリケーションは含まれていません。  

<a id="Build"></a>

## <a name="step-2-deploy-the-documentdb-account-app-service-web-app-and-demo-application-sample"></a>手順 2: DocumentDB アカウント、App Service Web アプリ、デモ アプリケーション サンプルをデプロイする
では、最初のテンプレートをデプロイしましょう。

> [!TIP]
> このテンプレートでは、ここで入力する Web アプリ名と DocumentDB アカウント名が a) 有効であること、および b) 使用可能であることについては確認されません。  デプロイメントの送信前に、指定する予定の名前の可用性を確認しておくことを強くお勧めします。
> 
> 

1. [Azure ポータル](https://portal.azure.com)にログインし、[New] (新規) をクリックし、「テンプレートのデプロイ」を検索します。
    ![テンプレートのデプロイメント UI のスクリーンショット](./media/documentdb-create-documentdb-website/TemplateDeployment1.png)
2. [テンプレートのデプロイ] を選択し、**[作成]** 
     ![[テンプレートのデプロイメント UI のスクリーンショット]](./media/documentdb-create-documentdb-website/TemplateDeployment2.png) をクリックします。
3. **[テンプレートの編集]** をクリックし、DocDBWebsiteTodo.json テンプレート ファイルの内容を貼り付けて、**[保存]** をクリックします。
   ![テンプレートのデプロイメント UI のスクリーンショット](./media/documentdb-create-documentdb-website/TemplateDeployment3.png)
4. **[パラメーターの編集]** をクリックし、必須パラメーターの値を指定して、**[OK]** をクリックします。  パラメーターは、次のとおりです。
   
   1. SITENAME: App Service Web アプリの名前を指定します。これは、Web アプリへのアクセスに使用する URL を作成するときに使用されます (たとえば、"mydemodocdbwebapp" を指定した場合、Web アプリへのアクセスに使用する URL は mydemodocdbwebapp.azurewebsites.net になります)。
   2. HOSTINGPLANNAME: 作成する App Service ホスティング プランの名前を指定します。
   3. LOCATION: DocumentDB と Web アプリのリソースを作成する Azure の場所を指定します。
   4. DATABASEACCOUNTNAME: 作成する DocumentDB アカウントの名前を指定します。   
      
      ![テンプレートのデプロイメント UI のスクリーンショット](./media/documentdb-create-documentdb-website/TemplateDeployment4.png)
5. 既存のリソース グループを選択するか、名前を指定して新しいリソース グループを作成し、リソース グループの場所を選択します。

    ![テンプレートのデプロイメント UI のスクリーンショット](./media/documentdb-create-documentdb-website/TemplateDeployment5.png)
6. **[法律条項を確認してください]**、**[購入]**、**[作成]** の順にクリックして、デプロイを開始します。  **[ダッシュボードにピン留めする]** を選択すると、Azure ポータルのホーム ページで生成されたデプロイメントが見つけやすくなります。
   ![テンプレートのデプロイメント UI のスクリーンショット](./media/documentdb-create-documentdb-website/TemplateDeployment6.png)
7. デプロイメントが完了したら、リソース グループ ブレードが開きます。
   ![リソース グループ ブレードのスクリーンショット](./media/documentdb-create-documentdb-website/TemplateDeployment7.png)  
8. アプリケーションを使用するには、単純にその Web アプリの URL にアクセスします (上の例では URL は http://mydemodocdbwebapp.azurewebsites.net)。  次のような Web アプリケーションが表示されます。
   
   ![Todo アプリケーションのサンプル](./media/documentdb-create-documentdb-website/image2.png)
9. ここで、Web アプリでいくつかのタスクを作成してから、Azure ポータルのリソース グループ ブレードに戻ります。 リソースの一覧で DocumentDB アカウント リソースをクリックし、 **クエリ エクスプローラー**をクリックします。
    ![myotherdocumentdbwebapp Web アプリを強調表示した [概要] レンズのスクリーン ショット](./media/documentdb-create-documentdb-website/TemplateDeployment8.png)  
10. 既定のクエリ "SELECT * FROM c" を実行し、その結果を調べます。このクエリでは、前の手順 7. で作成した Todo 項目の JSON 表現を取得しました。クエリを自由に試してください。たとえば、SELECT * FROM c WHERE c.isComplete = true を実行してみると、完了済みとしてマークされているすべての Todo 項目が返されます。
    
    ![クエリ結果を示す [クエリ エクスプローラー] ブレードと [結果] ブレードのスクリーン ショット](./media/documentdb-create-documentdb-website/image5.png)
11. DocumentDB ポータルの操作やサンプル Todo アプリケーションの変更を自由に試してみてください。  準備ができたら、別のテンプレートをデプロイしましょう。

<a id="Build"></a> 

## <a name="step-3-deploy-the-document-account-and-web-app-sample"></a>手順 3: Document アカウントと Web アプリのサンプルのデプロイ
では、2 番目のテンプレートをデプロイしましょう。  このテンプレートは、アカウント エンドポイントやマスター キーなどの DocumentDB 接続情報を、アプリケーション設定またはカスタム接続文字列として Web アプリに導入する方法を理解するのに役立ちます。 たとえば、DocumentDB アカウントを使用してデプロイする独自の Web アプリケーションがあり、接続情報をデプロイ時に自動的に設定するような場合があります。

> [!TIP]
> このテンプレートでは、ここで入力する Web アプリ名と DocumentDB アカウント名が a) 有効であること、および b) 使用可能であることについては確認されません。  デプロイメントの送信前に、指定する予定の名前の可用性を確認しておくことを強くお勧めします。
> 
> 

1. [Azure ポータル](https://portal.azure.com)で、[新規] をクリックし、「テンプレートのデプロイ」を検索します。
    ![テンプレートのデプロイメント UI のスクリーンショット](./media/documentdb-create-documentdb-website/TemplateDeployment1.png)
2. [テンプレートのデプロイ] を選択し、**[作成]** 
     ![[テンプレートのデプロイメント UI のスクリーンショット]](./media/documentdb-create-documentdb-website/TemplateDeployment2.png) をクリックします。
3. **[テンプレートの編集]** をクリックし、DocDBWebSite.json テンプレート ファイルの内容を貼り付けて、**[保存]** をクリックします。
   ![テンプレートのデプロイメント UI のスクリーンショット](./media/documentdb-create-documentdb-website/TemplateDeployment3.png)
4. **[パラメーターの編集]** をクリックし、必須パラメーターの値を指定して、**[OK]** をクリックします。  パラメーターは、次のとおりです。
   
   1. SITENAME: App Service Web アプリの名前を指定します。これは、Web アプリへのアクセスに使用する URL を作成するときに使用されます (たとえば、"mydemodocdbwebapp" を指定した場合、Web アプリへのアクセスに使用する URL は mydemodocdbwebapp.azurewebsites.net になります)。
   2. HOSTINGPLANNAME: 作成する App Service ホスティング プランの名前を指定します。
   3. LOCATION: DocumentDB と Web アプリのリソースを作成する Azure の場所を指定します。
   4. DATABASEACCOUNTNAME: 作成する DocumentDB アカウントの名前を指定します。   
      
      ![テンプレートのデプロイメント UI のスクリーンショット](./media/documentdb-create-documentdb-website/TemplateDeployment4.png)
5. 既存のリソース グループを選択するか、名前を指定して新しいリソース グループを作成し、リソース グループの場所を選択します。

    ![テンプレートのデプロイメント UI のスクリーンショット](./media/documentdb-create-documentdb-website/TemplateDeployment5.png)
6. **[法律条項を確認してください]**、**[購入]**、**[作成]** の順にクリックして、デプロイを開始します。  **[ダッシュボードにピン留めする]** を選択すると、Azure ポータルのホーム ページで生成されたデプロイメントが見つけやすくなります。
   ![テンプレートのデプロイメント UI のスクリーンショット](./media/documentdb-create-documentdb-website/TemplateDeployment6.png)
7. デプロイメントが完了したら、リソース グループ ブレードが開きます。
   ![リソース グループ ブレードのスクリーンショット](./media/documentdb-create-documentdb-website/TemplateDeployment7.png)  
8. リソースの一覧で Web アプリのリソースをクリックし、**[アプリケーション設定]** 
     ![[リソース グループのスクリーンショット]](./media/documentdb-create-documentdb-website/TemplateDeployment9.png) をクリックします。  
9. DocumentDB エンドポイントおよびそれぞれの DocumentDB マスター キーに関するアプリケーションの設定がどのように表示されているかを確認します。

    ![アプリケーションの設定のスクリーンショット](./media/documentdb-create-documentdb-website/TemplateDeployment10.png)  
10. 引き続き、Azure ポータルの操作を確認したり、DocumentDB の [サンプル](http://go.microsoft.com/fwlink/?LinkID=402386) に従って独自の DocumentDB アプリケーションを作成したり、自由に試してみてください。

<a name="NextSteps"></a>

## <a name="next-steps"></a>次のステップ
ご利用ありがとうございます。 Azure リソース マネージャーのテンプレートを使用して、DocumentDB、App Service Web アプリ、サンプルの Web アプリケーションをデプロイしました。

* DocumentDB の詳細については、 [ここ](http://azure.com/docdb)をクリックしてください。
* Azure App Service Web Apps の詳細については、 [ここ](http://go.microsoft.com/fwlink/?LinkId=325362)をクリックしてください。
* Azure リソース マネージャーのテンプレートの詳細については、 [ここ](https://msdn.microsoft.com/library/azure/dn790549.aspx)をクリックしてください。

## <a name="whats-changed"></a>変更内容
* Websites から App Service への変更ガイドについては、「 [Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)
* 以前のポータルから新しいポータルへの変更ガイドについては、「 [Azure クラシック ポータル内の移動に関するリファレンス](http://go.microsoft.com/fwlink/?LinkId=529715)

> [!NOTE]
> Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、「[Azure App Service アプリケーションの作成](http://go.microsoft.com/fwlink/?LinkId=523751)」を参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
> 
> 




<!--HONumber=Jan17_HO4-->


