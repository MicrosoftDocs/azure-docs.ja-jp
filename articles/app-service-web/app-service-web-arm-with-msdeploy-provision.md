---
title: "MSDeploy、ホスト名、SSL 証明書を使用した Web アプリケーションのデプロイ"
description: "Azure リソース マネージャー テンプレートを使用して、カスタム ホスト名と SSL 証明書を設定し、MSDeploy を使用して Web アプリケーションをデプロイします"
services: app-service\web
manager: erikre
documentationcenter: 
author: jodehavi
ms.assetid: 66366a72-cef7-4d75-8779-f4d32ed33cf7
ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2016
ms.author: jodehavi
translationtype: Human Translation
ms.sourcegitcommit: 4fbfb24a2e9d55d718902d468bd25e12f64e7d24
ms.openlocfilehash: f836bffd0610224b5cb69f4f6836dbc55e0721a3


---
# <a name="deploy-a-web-app-with-msdeploy-custom-hostname-and-ssl-certificate"></a>MSDeploy、カスタム ホスト名、SSL 証明書を使用した Web アプリケーションのデプロイ
このガイドでは、MSDeploy を利用し、カスタム ホスト名と SSL 証明書を ARM テンプレートに追加して、Azure Web アプリのエンド ツー エンドのデプロイを作成する手順について説明します。

テンプレートの作成の詳細については、「 [Authoring Azure Resource Manager Templates (Azure リソース マネージャー テンプレートのオーサリング)](../azure-resource-manager/resource-group-authoring-templates.md)」を参照してください。

### <a name="create-sample-application"></a>サンプル アプリケーションの作成
ここでは、ASP.NET Web アプリケーションをデプロイします。 まず、シンプルな Web アプリケーションを作成します (既存の Web アプリケーションを使用することもできます。その場合は、この手順を省略してかまいません)。

Visual Studio 2015 を開き、[ファイル]、[新しいプロジェクト] の順に選択します。 表示されたダイアログで、[Web]、[ASP.NET Web アプリケーション] の順に選択します。 [テンプレート] で [Web] を選択し、MVC テンプレートを選択します。 *[認証の種類の変更]* で *[認証なし]* を選択します。 これは、サンプル アプリケーションをできるだけシンプルにするためです。

この時点で、デプロイ プロセスの一環としていつでも使用できる基本的な ASP.Net Web アプリケーションが用意されます。

### <a name="create-msdeploy-package"></a>MSDeploy パッケージの作成
次に、Web アプリケーションを Azure にデプロイするためのパッケージを作成します。 そのためには、プロジェクトを保存し、コマンド ラインから次のコマンドを実行します。

    msbuild yourwebapp.csproj /t:Package /p:PackageLocation="path\to\package.zip"

これにより、PackageLocation フォルダーの下に圧縮されたパッケージが作成されます。 これで、アプリケーションをデプロイする準備ができました。次に、アプリケーションをデプロイするための Azure リソース マネージャー テンプレートを作成します。

### <a name="create-arm-template"></a>ARM テンプレートの作成
まず、Web アプリケーションとホスティング プランを作成する基本的な ARM テンプレートから始めましょう (簡潔にするために、パラメーターと変数は示されていません)。

    {
        "name": "[parameters('appServicePlanName')]",
        "type": "Microsoft.Web/serverfarms",
        "location": "[resourceGroup().location]",
        "apiVersion": "2014-06-01",
        "dependsOn": [ ],
        "tags": {
            "displayName": "appServicePlan"
        },
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "sku": "[parameters('appServicePlanSKU')]",
            "workerSize": "[parameters('appServicePlanWorkerSize')]",
            "numberOfWorkers": 1
        }
    },
    {
        "name": "[variables('webAppName')]",
        "type": "Microsoft.Web/sites",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-08-01",
        "dependsOn": [
            "[concat('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        ],
        "tags": {
            "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]": "Resource",
            "displayName": "webApp"
        },
        "properties": {
            "name": "[variables('webAppName')]",
            "serverFarmId": "[resourceId('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        }
    }

次に、入れ子になった MSDeploy リソースを取得するために、Web アプリケーション リソースを変更する必要があります。 これにより、前に作成したパッケージを参照することが可能となり、MSDeploy を使用してパッケージを Azure Web アプリにデプロイするよう Azure リソース マネージャーに指示することができます。 入れ子になった MSDeploy リソースを含む Microsoft.Web/sites リソースを次に示します。

    {
        "name": "[variables('webAppName')]",
        "type": "Microsoft.Web/sites",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-08-01",
        "dependsOn": [
            "[concat('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        ],
        "tags": {
            "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]": "Resource",
            "displayName": "webApp"
        },
        "properties": {
            "name": "[variables('webAppName')]",
            "serverFarmId": "[resourceId('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        },
        "resources": [
            {
                "name": "MSDeploy",
                "type": "extensions",
                "location": "[resourceGroup().location]",
                "apiVersion": "2015-08-01",
                "dependsOn": [
                    "[concat('Microsoft.Web/sites/', variables('webAppName'))]"
                ],
                "tags": {
                    "displayName": "webDeploy"
                },
                "properties": {
                    "packageUri": "[concat(parameters('_artifactsLocation'), '/', parameters('webDeployPackageFolder'), '/', parameters('webDeployPackageFileName'), parameters('_artifactsLocationSasToken'))]",
                    "dbType": "None",
                    "connectionString": "",
                    "setParameters": {
                        "IIS Web Application Name": "[variables('webAppName')]"
                    }
                }
            }
        ]
    }

MSDeploy リソースは、次のように定義された **packageUri** プロパティを受け取ることがわかります。

    "packageUri": "[concat(parameters('_artifactsLocation'), '/', parameters('webDeployPackageFolder'), '/', parameters('webDeployPackageFileName'), parameters('_artifactsLocationSasToken'))]"

この **packageUri** は、パッケージ zip ファイルのアップロード先となるストレージ アカウントを参照するストレージ アカウント URI を取得します。 テンプレートのデプロイ時に、Azure リソース マネージャーは [Shared Access Signature](../storage/storage-dotnet-shared-access-signature-part-1.md) を利用して、ストレージ アカウントからパッケージをローカルに取得します。 このプロセスは、パッケージをアップロードし、Azure Management API を呼び出して必要なキーを作成し、それらのキーをパラメーター (*_artifactsLocation* および *_artifactsLocationSasToken*) としてテンプレートに渡す、PowerShell スクリプトによって自動化されます。 ストレージ コンテナーの下に、パッケージのアップロード先のフォルダーとファイル名のパラメーターを定義する必要があります。

次に、もう 1 つの入れ子になったリソースを追加して、カスタム ドメインを利用するようにホスト名のバインドを設定する必要があります。 まず、ホスト名を所有していることを確認し、Azure によって所有者が確認されるようにホスト名を設定する必要があります。[Azure App Service のカスタム ドメイン名の構成](web-sites-custom-domain-name.md)に関するページを参照してください。 この作業が完了したら、テンプレートの Microsoft.Web/sites リソース セクションの下に、次コードを追加します。

    {
        "apiVersion": "2015-08-01",
        "type": "hostNameBindings",
        "name": "www.yourcustomdomain.com",
        "dependsOn": [
            "[concat('Microsoft.Web/sites/', variables('webAppName'))]"
        ],
        "properties": {
            "domainId": null,
            "hostNameType": "Verified",
            "siteName": "variables('webAppName')"
        }
    }

最後に、もう 1 つのトップ レベルのリソースである Microsoft.Web/certificates を追加する必要があります。 このリソースには SSL 証明書が含まれます。このリソースは、Web アプリケーションおよびホスティング プランと同じレベルに存在します。

    {
        "name": "[parameters('certificateName')]",
        "apiVersion": "2014-04-01",
        "type": "Microsoft.Web/certificates",
        "location": "[resourceGroup().location]",
        "properties": {
            "pfxBlob": "pfx base64 blob",
            "password": "some pass"
        }
    }

このリソースを設定するには、有効な SSL 証明書が必要です。 有効な証明書を入手したら、pfx バイトを base64 文字列として抽出する必要があります。 これを抽出する 1 つの方法として、次の PowerShell コマンドを使用します。

    $fileContentBytes = get-content 'C:\path\to\cert.pfx' -Encoding Byte

    [System.Convert]::ToBase64String($fileContentBytes) | Out-File 'pfx-bytes.txt'

これを ARM デプロイ テンプレートにパラメーターとして渡すことができました。

この時点で、ARM テンプレートの準備が整いました。

### <a name="deploy-template"></a>テンプレートのデプロイ
最後の手順では、すべてをまとめて完全なエンド ツー エンドのデプロイを作成します。 デプロイを容易にするために、Visual Studio で Azure リソース グループ プロジェクトの作成時に追加された **Deploy-AzureResourceGroup.ps1** PowerShell スクリプトを利用して、テンプレートに必要なアーティファクトをアップロードできます。 使用するストレージ アカウントを事前に作成しておく必要があります。 この例では、アップロードする package.zip の共有ストレージ アカウントを作成しました。 スクリプトでは、AzCopy を利用してパッケージをストレージ アカウントにアップロードします。 アーティファクト フォルダーの場所を渡すと、そのディレクトリ内のすべてのファイルが指定のストレージ コンテナーに自動的にアップロードされます。 Deploy-AzureResourceGroup.ps1 の呼び出し後、SSL バインドを更新して、カスタム ホスト名を SSL 証明書と関連付ける必要があります。

次の PowerShell は、Deploy-AzureResourceGroup.ps1 を呼び出す完全なデプロイを示しています。

    #Set resource group name
    $rgName = "Name-of-resource-group"

    #call deploy-azureresourcegroup script to deploy web app

    .\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation "East US" `
                                    -ResourceGroupName $rgName `
                                    -UploadArtifacts `
                                    -StorageAccountName "name-of-storage-acct-for-package" `
                                    -StorageAccountResourceGroupName "resource-group-name-storage-acct" `
                                    -TemplateFile "web-app-deploy.json" `
                                    -TemplateParametersFile "web-app-deploy-parameters.json" `
                                    -ArtifactStagingDirectory "C:\path\to\packagefolder\"

    #update web app to bind ssl certificate to hostname. This has to be done after creation above.

    $cert = Get-PfxCertificate -FilePath C:\path\to\certificate.pfx

    $ar = Get-AzureRmResource -Name nameofwebsite -ResourceGroupName $rgName -ResourceType Microsoft.Web/sites -ApiVersion 2014-11-01

    $props = $ar.Properties

    $props.HostNameSslStates[2].'SslState' = 1
    $props.HostNameSslStates[2].'thumbprint' = $cert.Thumbprint
    $props.hostNameSslStates[2].'toUpdate' = $true

    Set-AzureRmResource -ApiVersion 2014-11-01 -Name nameofwebsite -ResourceGroupName $rgName -ResourceType Microsoft.Web/sites -PropertyObject $props

この時点でアプリケーションがデプロイされているので、https://www.yourcustomdomain.com から参照できます。




<!--HONumber=Dec16_HO3-->


