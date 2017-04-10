---
title: "C# と Resource Manager テンプレートを使用した VM のデプロイ | Microsoft Docs"
description: "C# および Resource Manager テンプレートを使用して Azure VM をデプロイする方法について説明します。"
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: bfba66e8-c923-4df2-900a-0c2643b81240
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: e96eacd689ead229d0c48d15f53a932b903c5281
ms.openlocfilehash: 9b8d800e39a5a659b5c9ebce7066b56c5b543db6
ms.lasthandoff: 03/02/2017


---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>C# と Resource Manager テンプレートを使用した Azure の仮想マシンのデプロイ
この記事では、C# を使用して Azure Resource Manager テンプレートをデプロイする方法を示します。 この[テンプレート](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json)は、1 つのサブネットを持つ新しい仮想ネットワークに、Windows Server を実行する&1; つの仮想マシンをデプロイします。

仮想マシン リソースの詳細については、「[Virtual machines in an Azure Resource Manager template](virtual-machines-windows-template-description.md)」(Azure Resource Manager テンプレートの仮想マシン) をご覧ください。 テンプレート内のすべてのリソースの詳細については、「[Resource Manager テンプレートのチュートリアル](../resource-manager-template-walkthrough.md)」をご覧ください。

これらの手順を実行するには約 10 分かかります。

## <a name="step-1-create-a-visual-studio-project"></a>手順 1: Visual Studio プロジェクトを作成する

この手順では、Visual Studio がインストールされていることを確認し、テンプレートをデプロイするために使用するコンソール アプリケーションを作成します。

1. まだ [Visual Studio](https://www.visualstudio.com/) をインストールしていない場合は、インストールを実行します。
2. Visual Studio で、**[ファイル]** > **[新規]** > **[プロジェクト]**をクリックします。
3. **[テンプレート]** > の **[Visual C#]** で **[コンソール アプリケーション]** を選択し、プロジェクトの名前と場所を入力して、**[OK]** をクリックします。

## <a name="step-2-install-libraries"></a>手順 2: ライブラリをインストールする

NuGet パッケージを使用すると、手順を完了するために必要なライブラリを簡単にインストールできます。 リソースを作成するには、Azure Resource Manager ライブラリと Active Directory 認証ライブラリをインストールする必要があります。 Visual Studio でこれらのライブラリを入手するには、次の手順に従います。

1. ソリューション エクスプローラーでプロジェクト名を右クリックし、**[NuGet パッケージの管理]**をクリックし、**[参照]** をクリックします。
2. 検索ボックスに「*Microsoft.IdentityModel.Clients.ActiveDirectory*」と入力し、**[インストール]** をクリックします。指示に従ってパッケージをインストールします。
3. ページの上部で、 **[リリース前のパッケージを含める]**を選択します。 検索ボックスに「*Microsoft.Azure.Management.ResourceManager*」と入力し、**[インストール]** をクリックします。指示に従ってパッケージをインストールします。

これで、ライブラリを使用してアプリケーションの作成を開始する準備が整いました。

## <a name="step-3-create-credentials-used-to-authenticate-requests"></a>手順 3: 要求を認証するために使用される資格情報を作成する

この手順を開始する前に、[Active Directory サービス プリンシパル](../azure-resource-manager/resource-group-authenticate-service-principal.md)にアクセスできることを確認します。 このサービス プリンシパルから、Azure Resource Manager への要求を認証するためのトークンを取得します。

1. 作成したプロジェクトの Program.cs ファイルを開き、次の using ステートメントをファイルの先頭に追加します。

    ```
    using Microsoft.Azure;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.Rest;
    using System.IO;
    ```

2. 資格情報の作成に必要なトークンを取得するために、次のメソッドを Program クラスに追加します。

    ```
    private static async Task<AuthenticationResult> GetAccessTokenAsync()
    {
      var cc = new ClientCredential("{client-id}", "{client-secret}");
      var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
      var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
      if (token == null)
      {
        throw new InvalidOperationException("Could not get the token.");
      } 
      return token;
    }
    ```

    次の値を置き換えます。
    
    - *{client-id}*: Azure Active Directory アプリケーションの ID。 この ID は、AD アプリケーションの [プロパティ] ブレードで確認できます。 Azure Portal で AD アプリケーションを見つけるには、リソース メニューの **[Azure Active Directory]** をクリックし、**[アプリの登録]** をクリックします。
    - *{client-secret}*: AD アプリケーションのアクセス キー。 この ID は、AD アプリケーションの [プロパティ] ブレードで確認できます。
    - *{tenant-id}*: サブスクリプションのテナント ID。 テナント ID は、Azure Portal で Azure Active Directory の [プロパティ] ブレードで確認できます。 *[ディレクトリ ID]* というラベルが付いています。

3. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。

    ```
    var token = GetAccessTokenAsync();
    var credential = new TokenCredentials(token.Result.AccessToken);
    ```

4. Program.cs ファイルを保存します。

## <a name="step-4-create-a-resource-group"></a>手順 4: リソース グループの作成

テンプレートからリソース グループを作成できますが、ギャラリーから使用するテンプレートはリソース グループを作成しません。 この手順では、リソース グループを作成するコードを追加します。

1. アプリケーションの値を指定するには、Program クラスの Main メソッドに変数を追加します。

    ```
    var groupName = "myResourceGroup";
    var subscriptionId = "subsciptionId";
    var deploymentName = "deploymentName;
    var location = "location";
    ```

    次の値を置き換えます。
    
    - *myResourceGroup*: 作成するリソース グループの名前。
    - *subscriptionId*: サブスクリプション ID。 サブスクリプション ID は、Azure Portal の [サブスクリプション] ブレードで確認できます。
    - *deploymentName*: デプロイの名前。
    - *location*: リソースを作成する [Azure リージョン](https://azure.microsoft.com/regions/)。

2. リソース グループを作成するために、次のメソッドを Program クラスに追加します。

    ```
    public static async Task<ResourceGroup> CreateResourceGroupAsync(
      TokenCredentials credential,
      string groupName,
      string subscriptionId,
      string location)
    {
      var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };

      Console.WriteLine("Creating the resource group...");
      var resourceGroup = new ResourceGroup { Location = location };
      return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(
        groupName, 
        resourceGroup);
    }
    ```

3. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。

    ```
    var rgResult = CreateResourceGroupAsync(
      credential,
      groupName,
      subscriptionId,
      location);
    Console.WriteLine(rgResult.Result.Properties.ProvisioningState);
    Console.ReadLine();
    ```

## <a name="step-5-create-a-parameters-file"></a>手順 5: パラメーター ファイルの作成

テンプレートに定義されているリソース パラメーターの値を指定するには、値を含むパラメーター ファイルを作成します。 パラメーター ファイルは、テンプレートをデプロイするときに使用されます。 ギャラリーから使用しているテンプレートでは、*adminUserName*、*adminPassword*、および *dnsLabelPrefix*パラメーターの値が必要です。

Visual Studio で、次の手順を実行します。

1. ソリューション エクスプローラーでプロジェクト名を右クリックし、**[追加]** > **[新しいアイテム]** をクリックします。
2. **[Web]** をクリックして **[JSON ファイル]** を選択し、[名前] に「*Parameters.json*」と入力して **[追加]**をクリックします。
3. Parameters.json ファイルを開き、次の JSON コンテンツを追加します。

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUserName": { "value": "mytestacct1" },
        "adminPassword": { "value": "mytestpass1" },
        "dnsLabelPrefix": { "value": "mydns1" }
      }
    }
    ```

    パラメーター値を、使用する環境で動作する値に置き換えます。

4. Parameters.json ファイルを保存します。

## <a name="step-6-deploy-a-template"></a>手順 6: テンプレートをデプロイする

この例では、Azure テンプレート ギャラリーのテンプレートをデプロイし、作成したローカル ファイルからパラメーター値を指定します。 

1. テンプレートをデプロイするには、Program クラスに次のメソッドを追加します。

    ```
    public static async Task<DeploymentExtended> CreateTemplateDeploymentAsync(
      TokenCredentials credential,
      string groupName,
      string deploymentName,
      string subscriptionId)
    {
    
      var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };

      Console.WriteLine("Creating the template deployment...");
      var deployment = new Deployment();
      deployment.Properties = new DeploymentProperties
        {
          Mode = DeploymentMode.Incremental,
          TemplateLink = new TemplateLink("https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json"),
          Parameters = File.ReadAllText("..\\..\\Parameters.json")
        };
      
      return await resourceManagementClient.Deployments.CreateOrUpdateAsync(
        groupName,
        deploymentName,
        deployment
      );
    }
    ```

    TemplateLink プロパティではなく Template プロパティを使用して、ローカル フォルダーからテンプレートをデプロイすることもできます。

2. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。

    ```
    var dpResult = CreateTemplateDeploymentAsync(
      credential,
      groupName,
      deploymentName,
      subscriptionId);
    Console.WriteLine(dpResult.Result.Properties.ProvisioningState);
    Console.ReadLine();
    ```

## <a name="step-7-delete-the-resources"></a>手順 7: リソースを削除する

Azure で使用されるリソースに対して課金されるため、不要になったリソースは削除することを常にお勧めします。 リソース グループから各リソースを個別に削除する必要はありません。 リソース グループを削除すると、そのすべてのリソースが自動的に削除されます。

1. リソース グループを削除するために、次のメソッドを Program クラスに追加します。

   ```
   public static async void DeleteResourceGroupAsync(
     TokenCredentials credential,
     string groupName,
     string subscriptionId)
   {
     Console.WriteLine("Deleting resource group...");
     var resourceManagementClient = new ResourceManagementClient(credential)
       { SubscriptionId = subscriptionId };
     await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
   }
   ```

2. 追加したメソッドを呼び出すために、次のコードを Main メソッドに追加します。

   ```
   DeleteResourceGroupAsync(
     credential,
     groupName,
     subscriptionId);
   Console.ReadLine();
   ```

## <a name="step-8-run-the-console-application"></a>手順 8: コンソール アプリケーションを実行する

このコンソール アプリケーションが実行を開始してから完全に終了するまでには、約&5; 分かかります。 

1. コンソール アプリケーションを実行するために、Visual Studio で **[開始]** をクリックし、サブスクリプションで使用するのと同じ資格情報を使用して Azure AD にサインインします。

2. "*[成功しました]*" 状態が表示されたら、**Enter** キーを押します。 

    Azure Portal では、リソース グループの [概要] ブレードで、[デプロイ] の下に **[1 Succeeded (1 つ成功しました)]** も表示されます。

3. **Enter** キーを押してリソースの削除を開始する前に、Azure Portal でリソースの作成状況を確認することもできます。 デプロイに関する情報を参照するには、デプロイ状態をクリックします。

## <a name="next-steps"></a>次のステップ
* デプロイに問題がある場合は、次の手順として、「[Troubleshoot common Azure deployment errors with Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)」(Azure Resource Manager を使用した Azure のデプロイで発生する一般的なエラーのトラブルシューティング) を参照してください。
* 「[C# を使用した Azure 仮想マシンのデプロイ](virtual-machines-windows-csharp.md)」で、仮想マシンとそれをサポートするリソースのデプロイ方法を確認します。
* 「[Azure Resource Manager と C# を使用した Azure Virtual Machines の管理](virtual-machines-windows-csharp-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」で、作成した仮想マシンを管理する方法を確認します。

