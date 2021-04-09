---
title: チュートリアル - Service Fabric Mesh アプリケーションをデプロイする
description: バックエンド Web サービスと通信する ASP.NET Core Web サイトで構成される Azure Service Fabric Mesh アプリケーションを、Visual Studio を使用して発行する方法を説明します。
author: georgewallace
ms.topic: tutorial
ms.date: 09/18/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: 713f8dcb3d3b3d30fecbea4bb6b50cc4e47d451d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102216753"
---
# <a name="tutorial-deploy-a-service-fabric-mesh-application"></a>チュートリアル:Service Fabric Mesh アプリケーションをデプロイする

> [!IMPORTANT]
> Azure Service Fabric Mesh のプレビューは廃止されました。 Service Fabric Mesh API を介した新しいデプロイは許可されなくなります。 既存のデプロイのサポートは、2021 年 4 月 28 日まで継続されます。
> 
> 詳細については、「[Azure Service Fabric Mesh のプレビューの廃止](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)」を参照してください。

このチュートリアルはシリーズの第 3 部です。ここでは、Azure Service Fabric Mesh Web アプリケーションを Visual Studio から直接 発行する方法について説明します。

このチュートリアルで学習する内容は次のとおりです。
> [!div class="checklist"]
> * Visual Studio を使用してアプリを Azure に発行する。
> * アプリケーションのデプロイの状態を確認する
> * サブスクリプションに現在デプロイされているすべてのアプリケーションを表示する

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * [Visual Studio で Service Fabric Mesh アプリを作成する](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [ローカル開発クラスター内で実行されている Service Fabric Mesh アプリをデバッグする](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * Service Fabric Mesh アプリをデプロイする
> * [Service Fabric Mesh アプリをアップグレードする](service-fabric-mesh-tutorial-upgrade.md)
> * [Service Fabric Mesh リソースをクリーンアップする](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)できます。

* Service Fabric ランタイム、SDK、Docker、Visual Studio 2017 がインストールされた[開発環境の設定](service-fabric-mesh-howto-setup-developer-environment-sdk.md)が済んでいることを確認します。

## <a name="download-the-to-do-sample-application"></a>To Do サンプル アプリケーションをダウンロードする

[このチュートリアル シリーズの第 2 部](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)で To Do サンプル アプリケーションを作成しなかった場合は、ダウンロードできます。 コマンド ウィンドウで、次のコマンドを実行して、サンプル アプリのリポジトリをローカル コンピューターに複製します。

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

アプリケーションは `src\todolistapp` ディレクトリにあります。

## <a name="publish-to-azure"></a>Azure に発行する

Service Fabric Mesh プロジェクトを Azure に発行するには、Visual Studio で **todolistapp** を右クリックし、 **[発行...]** を選択します。

**[Service Fabric アプリケーションの発行]** ダイアログが表示されます。

![Visual Studio: Service Fabric mesh の発行ダイアログ](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Azure アカウントとサブスクリプションを選択します。 **[場所]** を選択します。 この記事では、 **[米国東部]** を使用します。

**[リソース グループ]** で、 **\<Create New Resource Group...>** を選択します。 新しいリソース グループを作成するダイアログが表示されます。 この記事では、場所として **[米国東部]** を使用し、グループに **sfmeshTutorial1RG** という名前を付けます (組織に同じサブスクリプションを使用する複数のユーザーがいる場合は、一意のグループ名を選択します)。  **[作成]** をクリックしてリソース グループを作成し、発行ダイアログに戻ります。

![Visual Studio: Service Fabric mesh の新しいリソース グループ ダイアログ](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-resource-group-dialog.png)

**[Service Fabric アプリケーションの発行]** ダイアログに戻り、 **[Azure Container Registry]** の **\<Create New Container Registry...>** を選択します。 **[コンテナー レジストリの作成]** ダイアログで、 **[コンテナー レジストリ名]** に一意の名前を使用します。 **場所** を指定します (このチュートリアルでは、 **[米国東部]** を使用します)。 前の手順で作成した **リソース グループ** をドロップダウンで選択します (例: **sfmeshTutorial1RG**)。 **[SKU]** を **[Basic]** に設定し、 **[作成]** をクリックして、プライベート Azure コンテナー レジストリを作成し、発行ダイアログに戻ります。

![Visual Studio の Service Fabric Mesh 新規コンテナー レジストリ ダイアログ](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-container-registry-dialog.png)

リソース プロバイダーがサブスクリプションに登録されていないことを示すエラーが表示された場合は、登録できます。 まず、サブスクリプションでリソース プロバイダーが使用可能かどうかを確認します。

```Powershell
Get-AzureRmResourceProvider -ListAvailable
```

コンテナー レジストリ プロバイダー (`Microsoft.ContainerRegistry`) が使用可能な場合は、PowerShell から登録します。

```Powershell
Connect-AzureRmAccount
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ContainerRegistry
```

発行ダイアログで **[発行]** をクリックして、Service Fabric アプリケーションを Azure にデプロイします。

Azure に初めて発行したときは、Docker イメージが Azure Container Registry (ACR) にプッシュされます。イメージのサイズによっては、この処理に時間がかかります。 同じプロジェクトの以降の発行は高速になります。 Visual Studio の **出力** ウィンドウで **[Service Fabric Tools]** ウィンドウを選択することで、デプロイの進行状況を監視できます。 デプロイが完了すると、**Service Fabric Tools** の出力に、アプリケーションの IP アドレスとポートが URL の形式で表示されます。

```
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMeshApp\ToDoService\bin\Any CPU\Release\netcoreapp2.0\ToDoService.dll
Uploading the images to Azure Container Registry...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Web ブラウザーを開き、その URL に移動して、Azure で実行されている Web サイトを表示します。

## <a name="set-up-service-fabric-mesh-cli"></a>Service Fabric Mesh CLI の設定

残りの手順は、Azure Cloud Shell または Azure CLI のローカル インストールを使用して実行できます。 こちらの[手順](service-fabric-mesh-howto-setup-cli.md)に従って、Azure Service Fabric Mesh CLI 拡張モジュールをインストールしてください。

## <a name="check-application-deployment-status"></a>アプリケーションのデプロイの状態を確認する

この時点で、アプリケーションはデプロイされています。 `app show` コマンドを使用して、アプリケーションの状態を確認できます。 

このチュートリアルのアプリのアプリケーション名は `todolistapp` です。 次のコマンドを使用して、アプリケーションの詳細を収集します。

```azurecli-interactive
az mesh app show --resource-group $rg --name todolistapp
```

## <a name="get-the-ip-address-of-your-deployment"></a>デプロイの IP アドレスを取得する

アプリケーションの IP アドレスを取得したい場合は、次のコマンドを使用します。
  
```azurecli-interactive
az mesh gateway show --resource-group myResourceGroup --name todolistappGateway
```

## <a name="see-all-applications-currently-deployed-to-your-subscription"></a>サブスクリプションに現在デプロイされているすべてのアプリケーションを表示する

"app list" コマンドを使用して、サブスクリプションにデプロイされているアプリケーションのリストを取得できます。

```azurecli-interactive
az mesh app list --output table
```

## <a name="next-steps"></a>次のステップ

チュートリアルのこの部分で学習した内容は次のとおりです。
> [!div class="checklist"]
> * アプリを Azure に発行する
> * アプリケーションのデプロイの状態を確認する
> * サブスクリプションに現在デプロイされているすべてのアプリケーションを表示する

次のチュートリアルに進みます。
> [!div class="nextstepaction"]
> [Service Fabric Mesh アプリをアップグレードする](service-fabric-mesh-tutorial-upgrade.md)

[azure-cli-install]: /cli/azure/install-azure-cli
