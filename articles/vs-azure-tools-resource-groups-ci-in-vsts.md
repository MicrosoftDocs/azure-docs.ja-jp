---
title: "Azure リソース グループのプロジェクトを使用した VS Team Services での継続的インテグレーション | Microsoft Docs"
description: "Visual Studio で Azure リソース グループのデプロイメント プロジェクトを使用して、Visual Studio Team Services での継続的インテグレーションを設定する方法を説明します。"
services: visual-studio-online
documentationcenter: na
author: mlearned
manager: erickson-doug
editor: 
ms.assetid: b81c172a-be87-4adc-861e-d20b94be9e38
ms.service: azure-resource-manager
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: mlearned
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: b178359d2f55e9137b39f42f5562e7bb8a642c57


---
# <a name="continuous-integration-in-visual-studio-team-services-using-azure-resource-group-deployment-projects"></a>Azure リソース グループのデプロイメント プロジェクトを使用した Visual Studio Team Services での継続的インテグレーション
Azure テンプレートをデプロイするには、さまざまな段階 (ビルド、テスト、Azure へのコピー ("ステージング" とも呼ばれる)、テンプレートのデプロイ) を通してタスクを実行する必要があります。  Visual Studio Team Services (VS Team Services) でテンプレートをデプロイする方法は 2 つあります。 どちらの方法でも同じ結果になるため、ワークフローに最適な方法を選択します。

1. Azure リソース グループ デプロイメント プロジェクト (Deploy-AzureResourceGroup.ps1) に含まれている PowerShell スクリプトを実行するビルド定義にシングル ステップを追加します。 このスクリプトではアーティファクトをコピーして、テンプレートをデプロイします。
2. それぞれが単一のタスクを実行する、複数の VS Team Services のビルド ステップを追加します。

この記事では、両方のオプションについて説明します。  最初のオプションの場合、Visual Studio で開発者が同じスクリプトを使用できるため、ライフサイクル全体で一貫性が確保されるという利点があります。  2 番目のオプションでは、組み込みのスクリプトに代わる便利な方法が提供されます。  両方の手順では、Visual Studio デプロイメント プロジェクトが VS Team Services にチェックインしていることを前提としています。

## <a name="copy-artifacts-to-azure"></a>Azure へのアーティファクトのコピー
シナリオに関係なく、テンプレート デプロイメントに必要なアーティファクトがある場合、このアーティファクトに Azure Resource Manager のアクセス権を付与する必要があります。 これらのアーティファクトには、次のファイルを含めることができます。

* 入れ子になったテンプレート
* 構成スクリプトと DSC スクリプト
* アプリケーションのバイナリ

### <a name="nested-templates-and-configuration-scripts"></a>入れ子になったテンプレートと構成スクリプト
Visual Studio が提供する (または、Visual Studio のスニペットでビルドされた) テンプレートを使用すると、PowerShell スクリプトはアーティファクトをステージングするだけでなく、さまざまなデプロイメントのリソースに対する URI もパラメーター化します。 次に、スクリプトはアーティファクトを Azure のセキュリティで保護されたコンテナーにコピーし、そのコンテナーの SaS トークンを作成して、テンプレート デプロイメントにその情報を渡します。 入れ子になったテンプレートの詳細については、「 [テンプレート デプロイメントの作成](https://msdn.microsoft.com/library/azure/dn790564.aspx) 」を参照してください。  VS Team Services のタスクを使用する場合は、テンプレートのデプロイメントに適したタスクを選択する必要があります。必要に応じて、ステージング手順からテンプレートのデプロイメントにパラメーター値を渡してください。

## <a name="set-up-continuous-deployment-in-vs-team-services"></a>VS Team Services での継続的なデプロイメントの設定
VS Team Services で PowerShell スクリプトを呼び出すには、ビルド定義を更新する必要があります。 簡単に言うと、手順は次のとおりです。 

1. ビルド定義を編集します。
2. VS Team Services で Azure 認証を設定します。
3. Azure リソース グループ デプロイメント プロジェクトで PowerShell スクリプトを参照する Azure PowerShell のビルド ステップを追加します。
4. *-ArtifactsStagingDirectory* パラメーターの値を設定し、VS Team Services に組み込まれているプロジェクトを操作します。

### <a name="detailed-walkthrough-for-option-1"></a>オプション 1 の詳細なチュートリアル
次の手順では、PowerShell スクリプトをプロジェクトで実行する 1 つのタスクを使用して VS Team Services で継続的なデプロイメントを構成するために必要な手順を説明します。 

1. VS Team Services のビルド定義を編集し、Azure PowerShell のビルド ステップを追加します。 **[ビルド定義]** カテゴリでビルド定義を選択し、**[編集]** リンクを選択します。
   
   ![ビルド定義の編集][0]
2. 新しい **Azure PowerShell** のビルド ステップをビルド定義に追加して、**[ビルド ステップの追加...]**  ボタンを選択します。
   
   ![ビルド ステップの追加][1]
3. **[タスクのデプロイ]** カテゴリを選択し、**Azure PowerShell** タスクを選択して、**[追加]** ボタンをクリックします。
   
   ![タスクの追加][2]
4. **Azure PowerShell** のビルド ステップを選択して、その値を入力します。
   
   1. 既に Azure サービス エンドポイントが VS Team Services に追加されている場合、 **[Azure Subscription]** ドロップダウン リスト ボックスでサブスクリプションを選択して、次のセクションにスキップします。 
      
      VS Team Services に Azure サービス エンドポイントがない場合、1 つ追加する必要があります。 この項では、プロセスを示します。 Azure アカウントが Microsoft アカウント (Hotmail など) を使用する場合、サービス プリンシパルの認証を取得するために、次の手順を実行する必要があります。
   2. **[Azure サブスクリプション]** ドロップ ダウン リスト ボックスの横にある **[管理]** リンクを選択します。
      
      ![Azure サブスクリプションの管理][3]
   3. **[新しいサービス エンドポイント]** ドロップダウン リスト ボックスで **[Azure]** を選択します。
      
      ![[新しいサービス エンドポイント]][4]
   4. **[Azure サブスクリプションの追加]** ダイアログ ボックスで、**[サービス プリンシパル]** オプションを選択します。
      
      ![[サービス プリンシパル] オプション][5]
   5. Azure サブスクリプション情報を **[Azure サブスクリプションの追加]** ダイアログ ボックスに追加します。 次の項目を指定する必要があります。
      
      * サブスクリプション ID
      * サブスクリプション名
      * サービス プリンシパル ID
      * サービス プリンシパル キー
      * テナント ID
   6. 任意の名前を **[サブスクリプション名]** ボックスに追加します。 この値は、後で VS Team Services の **[Azure サブスクリプション]** ドロップダウン リストに表示されます。 
   7. Azure サブスクリプション ID がわからない場合は、次のいずれかのコマンドを使用して取得することができます。
      
      PowerShell スクリプトでは、次を使用します。
      
      `Get-AzureRmSubscription`
      
      Azure CLI では、次を使用します。
      
      `azure account show`
   8. サービス プリンシパル ID、サービス プリンシパル キー、およびテナント ID を取得するには、[Active Directory のアプリケーションとサービス プリンシパルをポータルで作成する方法](resource-group-create-service-principal-portal.md)に関するページか、[サービス プリンシパルを Azure Resource Manager で認証する方法](resource-group-authenticate-service-principal.md)に関するページの手順を行います。
   9. **[Azure サブスクリプションの追加]** ダイアログ ボックスにサービス プリンシパル ID、サービス プリンシパル キー、およびテナント ID 値を追加し、**[OK]** ボタンをクリックします。
      
      これで、Azure PowerShell スクリプトを実行するために使用する有効なサービス プリンシパルがあります。
5. ビルド定後を編集して、 **Azure PowerShell** のビルド ステップを選択します。 **[Azure サブスクリプション** ] ドロップダウン リスト ボックスでサブスクリプションを選択します。 (サブスクリプションが表示されない場合は、**[管理]** リンクの横の **[最新の情報に更新]** ボタンをクリックします。) 
   
   ![Azure PowerShell のビルドの構成タスク][8]
6. Deploy-AzureResourceGroup.ps1 のPowerShell スクリプトのパスを指定します。 これを行うには、**[スクリプト パス]** ボックスの横にある省略記号 (...) ボタンを選択して、プロジェクトの **[スクリプト]** フォルダーの Deploy-AzureResourceGroup.ps1 PowerShell スクリプトに移動し、それを選択して、**[OK]** ボタンをクリックします。    
   
   ![スクリプトのパスの選択][9]
7. スクリプトを選択した後、Build.StagingDirectory ( *ArtifactsLocation* の設定と同じディレクトリ) から実行できるように、スクリプトのパスを更新します。 “$(Build.StagingDirectory)/” をスクリプト パスの先頭に追加して、これを実行することができます。
   
    ![スクリプトのパスの編集][10]
8. **[スクリプトの引数]** ボックスに、次のパラメーター (1 行) を入力します。 Visual Studio でスクリプトを実行すると、 **[出力]** ウィンドウで　VS がパラメーターを使用する方法を確認することができます。 ビルド ステップでパラメーター値を設定するための出発点として、この方法を使用できます。
   
   | パラメーター | 説明 |
   | --- | --- |
   | -ResourceGroupLocation |**eastus** や **'East US'** など、リソース グループが配置される geo ロケーションの値です。 (名前にスペースが含まれる場合は、一重引用符を追加します)。詳細については、「[Azure Regions (Azure リージョン)](https://azure.microsoft.com/en-us/regions/)」を参照してください。 |
   | -ResourceGroupName |このデプロイメントに使用するリソース グループの名前。 |
   | -UploadArtifacts |このパラメーターは、存在する場合、アーティファクトがローカル システムから Azure にアップロードする必要があることを指定します。 テンプレート デプロイメントに、PowerShell スクリプト (構成スクリプトや入れ子になったテンプレートなど) を使用してステージングする追加のアーティファクトが必要な場合にのみ、このスイッチを設定する必要があります。 |
   | -StorageAccountName |このデプロイメントのアーティファクトをステージングするためのストレージ アカウントの名前。  このパラメーターは、デプロイメントのアーティファクトをステージングする場合にのみ使用します。 このパラメーターを指定すると、以前のデプロイメントの際にスクリプトによってストレージ アカウントが作成されていない場合に、新しいストレージ アカウントが作成されます。  このパラメーターが指定される場合は、ストレージ アカウントが既に存在している必要があります。 |
   | -StorageAccountResourceGroupName |ストレージ アカウントに関連付けられているリソース グループの名前。 このパラメーターは、StorageAccountName パラメーターの値を指定する場合にのみ必須です。 |
   | -TemplateFile |Azure リソース グループのデプロイメント プロジェクト内のテンプレート ファイルへのパス。 柔軟性を高めるには、絶対パスの代わりに、PowerShell スクリプトの場所に関連するこのパラメーターのパスを使用します。 |
   | -TemplateParametersFile |Azure リソース グループのデプロイメント プロジェクト内のパラメーター ファイルへのパス。 柔軟性を高めるには、絶対パスの代わりに、PowerShell スクリプトの場所に関連するこのパラメーターのパスを使用します。 |
   | -ArtifactStagingDirectory |このパラメーターを使用すると、PowerShell スクリプトが、プロジェクトのバイナリ ファイルがコピーされる必要のあるフォルダーを認識することができます。 この値は、PowerShell スクリプトが使用する既定値を上書きします。 VS Team Services を使用する場合、次の値を設定します: -ArtifactStagingDirectory $(Build.StagingDirectory) |
   
   スクリプト引数の例を次に示します (読みやすくするために改行しています)。
   
   ```    
   -ResourceGroupName 'MyGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\azuredeploy.json' 
   -TemplateParametersFile '..\templates\azuredeploy.parameters.json' -UploadArtifacts -StorageAccountName 'mystorageacct' 
   –StorageAccountResourceGroupName 'Default-Storage-EastUS' -ArtifactStagingDirectory '$(Build.StagingDirectory)'    
   ```
   
   完了すると、**[スクリプトの引数]** ボックスは次のようになります。
   
   ![[スクリプトの引数]][11]
9. Azure PowerShell のビルドのステップにすべての必須項目を追加したら、 **[キュー]** ビルド ボタンを選択してプロジェクトをビルドします。 **[ビルド]** 画面には、PowerShell スクリプトからの出力が表示されます。

### <a name="detailed-walkthrough-for-option-2"></a>オプション 2 の詳細なチュートリアル
次の手順では、組み込みのタスクを使用して VS Team Services で継続的なデプロイメントを構成するために必要な手順を説明します。

1. VS Team Services のビルド定義を編集して、2 つの新しいビルド ステップを追加します。 **[ビルド定義]** カテゴリでビルド定義を選択し、**[編集]** リンクを選択します。
   
   ![ビルド定義の編集][12]
2. **[ビルド ステップの追加...]** ボタンを使用して、ビルド定義に新しいビルド ステップを追加します。  ボタンを選択します。
   
   ![ビルド ステップの追加][13]
3. **[タスクのデプロイ]** カテゴリを選択し、**[Azure ファイル コピー]** タスクを選択して、**[追加]** ボタンをクリックします。
   
   ![[Azure ファイル コピー] タスクの追加][14]
4. **[Azure リソース グループの配置]** タスクを選択し、その **[追加]** ボタンをクリックしてから**タスク カタログ**を**閉じます**。
   
   ![[Azure リソース グループの配置] タスクの追加][15]
5. **[Azure ファイル コピー]** タスクを選択し、その値を入力します。
   
   既に Azure サービス エンドポイントが VS Team Services に追加されている場合、**[Azure サブスクリプション]** ドロップダウン リスト ボックスでサブスクリプションを選択します。  サブスクリプションがない場合は、[オプション 1](#detailed-walkthrough-for-option-1) の VS Team Services におけるサブスクリプションの設定手順を参照してください。
   
   * ソース - 「**$(Build.StagingDirectory)**」と入力
   * Azure 接続の種類 - **[Azure Resource Manager]** を選択
   * Azure RM サブスクリプション - 使用するストレージ アカウントのサブスクリプションを **[Azure サブスクリプション]** ドロップダウン リスト ボックスで選択。 サブスクリプションが表示されない場合は、**[管理]** リンクの横にある **[更新]** ボタンを選択します。
   * 宛先の種類 - **[Azure BLOB]** を選択
   * RM ストレージ アカウント - アーティファクトのステージングに使用するストレージ アカウントを選択
   * コンテナー名 - ステージングに使用するコンテナーの名前を入力 (任意の有効なコンテナー名を使用できますが、このビルド定義に専用の名前を使用してください)
   
   次の出力値を指定します。
   
   * ストレージ コンテナーの URI - 「**artifactsLocation**」と入力
   * ストレージ コンテナーの SAS トークン - 「**artifactsLocationSasToken**」と入力
   
   ![[Azure ファイル コピー] タスクの構成][16]
6. **[Azure リソース グループの配置]** のビルド ステップを選択して、その値を入力します。
   
   * Azure 接続の種類 - **[Azure Resource Manager]** を選択
   * Azure RM サブスクリプション - デプロイメントのサブスクリプションを **[Azure サブスクリプション]** ドロップダウン リスト ボックスで選択。 通常、これは前の手順で使用したのと同じサブスクリプションになります。
   * アクション - **[リソース グループの作成または更新]** を選択
   * リソース グループ - リソース グループを選択するか、デプロイメントの新しいリソース グループの名前を入力
   * 場所 - リソース グループの場所を選択
   * テンプレート - デプロイするテンプレートのパスと名前を入力 (先頭に **$(Build.StagingDirectory)** を追加します。例: **$(Build.StagingDirectory/DSC-CI/azuredeploy.json)**)
   * テンプレート パラメーター - 使用するパラメーターのパスと名前を入力 (先頭に **$(Build.StagingDirectory)** を追加します。例: **$(Build.StagingDirectory/DSC-CI/azuredeploy.parameters.json)**)
   * テンプレート パラメーターのオーバーライド - 次のコードを入力またはコピーして貼り付け
     
     ```    
     -_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken (ConvertTo-SecureString -String "$(artifactsLocationSasToken)" -AsPlainText -Force)
     ```
     ![[Azure リソース グループの配置] タスクの構成][17]
7. 必要な項目をすべて追加したら、ビルド定義を保存して、上部の **[新しいビルドをキューに配置]** を選択します。

## <a name="next-steps"></a>次のステップ
Azure Resource Manager と Azure リソース グループの詳細については、「[Azure リソース マネージャーの概要](azure-resource-manager/resource-group-overview.md)」を参照してください。

[0]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough1.png
[1]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough2.png
[2]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough3.png
[3]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough4.png
[4]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough5.png
[5]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough6.png
[8]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough9.png
[9]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough10.png
[10]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough11b.png
[11]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough12.png
[12]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough13.png
[13]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough14.png
[14]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough15.png
[15]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough16.png
[16]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough17.png
[17]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough18.png



<!--HONumber=Dec16_HO2-->


