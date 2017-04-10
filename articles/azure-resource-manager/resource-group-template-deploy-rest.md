---
title: "REST API とテンプレートを使用してリソースをデプロイする | Microsoft Docs"
description: "Azure Resource Manager と Resource Manager REST API を使用してリソースを Azure にデプロイします。 リソースは Resource Manager テンプレートで定義されます。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 1d8fbd4c-78b0-425b-ba76-f2b7fd260b45
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/10/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 593f2d35730bcc03fb9d925be36c4c1423a31a49
ms.openlocfilehash: 23b634a6acde9837308815d9973e5da80fcde767
ms.lasthandoff: 01/14/2017


---
# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>Resource Manager テンプレートと Resource Manager REST API を使用したリソースのデプロイ
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-template-deploy.md)
> * [Azure CLI](resource-group-template-deploy-cli.md)
> * [ポータル](resource-group-template-deploy-portal.md)
> * [REST API](resource-group-template-deploy-rest.md)
> 
> 

この記事では、Resource Manager REST API と Resource Manager テンプレートを使用して Azure にリソースをデプロイする方法について説明します。  

> [!TIP]
> デプロイ時のエラーのデバッグについては、以下を参照してください。
> 
> * 「[デプロイ操作の表示](resource-manager-deployment-operations.md)」。エラーのトラブルシューティングに役立つ情報を掲載しています。
> * [Azure Resource Manager を使用してリソースを Azure にデプロイするときに発生する一般的なエラーをトラブルシューティングする](resource-manager-common-deployment-errors.md)」で、一般的なデプロイ エラーを解決する方法を知る
> 
> 

テンプレートは、ローカル ファイルまたは URI を通じて利用できる外部ファイルのいずれも使用できます。 テンプレートがストレージ アカウントに存在する場合は、テンプレートへのアクセスを制限し、デプロイ時に Shared Access Signature (SAS) トークンを設定できます。

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

## <a name="deploy-with-the-rest-api"></a>REST API でデプロイする
1. [一般的なパラメーターおよびヘッダー](https://docs.microsoft.com/rest/api/index) (認証トークンを含む) を設定します。
2. 既存のリソース グループがない場合は、リソース グループを作成します。 ソリューションに必要なサブスクリプション ID、新しいリソース グループの名前、場所を指定します。 詳細については、「[リソース グループの作成](https://docs.microsoft.com/rest/api/resources/resourcegroups#ResourceGroups_CreateOrUpdate)」を参照してください。
   
        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
          <common headers>
          {
            "location": "West US",
            "tags": {
               "tagname1": "tagvalue1"
            }
          }
3. [テンプレート デプロイを検証する](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_Validate)操作を実行して、デプロイを実行する前に検証します。 デプロイをテストする場合、(次の手順で示すように) デプロイの実行時に必要なパラメーターを正確に指定します。
4. デプロイを作成します。 サブスクリプション ID、リソース グループの名前、デプロイの名前、テンプレートへのリンクを指定します。 テンプレート ファイルについては、「[パラメーター ファイル](#parameter-file)」を参照してください。 リソース グループを作成する REST API の詳細については、「[テンプレートのデプロイを作成する](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_CreateOrUpdate)」を参照してください。 **[モード]** が **[増分]** に設定されていることに注意してください。 完全デプロイメントを実行するには、**[モード]** を **[完全]** に設定します。 テンプレートにないリソースを誤って削除する可能性があるため、完全モードを使用する際は注意してください。
   
        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
          <common headers>
          {
            "properties": {
              "templateLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
                "contentVersion": "1.0.0.0"
              },
              "mode": "Incremental",
              "parametersLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
                "contentVersion": "1.0.0.0"
              }
            }
          }
   
      応答の内容、要求の内容、またはその両方を記録するには、要求に **debugSetting** を含めます。
   
        "debugSetting": {
          "detailLevel": "requestContent, responseContent"
        }
   
      ストレージ アカウントを設定して、Shared Access Signature (SAS) トークンを使用することができます。 詳細については、「[Delegating Access with a Shared Access Signature (Shared Access Signature を使用したアクセスの委任)](https://docs.microsoft.com/rest/api/storageservices/fileservices/delegating-access-with-a-shared-access-signature)」を参照してください。
5. テンプレートのデプロイの状態を取得します。 詳細については、「[テンプレートのデプロイに関する情報を取得](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_Get)」を参照してください。
   
          GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
           <common headers>

## <a name="parameter-file"></a>パラメーター ファイル

[!INCLUDE [resource-manager-parameter-file](../../includes/resource-manager-parameter-file.md)]

## <a name="next-steps"></a>次のステップ
* 非同期 REST 操作の処理の詳細については、「[Track asynchronous Azure operations (非同期の Azure 操作の追跡)](resource-manager-async-operations.md)」を参照してください。
* .NET クライアント ライブラリを使用したリソースのデプロイの例については、「 [Deploy resources using .NET libraries and a template](../virtual-machines/virtual-machines-windows-csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」 (.NET ライブラリとテンプレートを使用した Azure リソースのデプロイ) を参照してください。
* テンプレートのパラメーターの定義については、 [テンプレートの作成](resource-group-authoring-templates.md#parameters)に関する記事を参照してください。
* ソリューションを別の環境にデプロイする方法については、「 [Microsoft Azure の開発環境とテスト環境](solution-dev-test-environments.md)」を参照してください。
* 企業が Resource Manager を使用してサブスクリプションを効果的に管理する方法については、「[Azure enterprise scaffold - prescriptive subscription governance (Azure エンタープライズ スキャフォールディング - サブスクリプションの規範的な管理)](resource-manager-subscription-governance.md)」を参照してください。
* デプロイの自動化についての&4; 回シリーズの解説については、「[Automating application deployments to Azure Virtual Machines (Azure Virtual Machines へのアプリケーションのデプロイの自動化)](../virtual-machines/virtual-machines-windows-dotnet-core-1-landing.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。 このシリーズでは、アプリケーションのアーキテクチャ、アクセスとセキュリティ、可用性と拡張性、およびアプリケーションのデプロイについて説明しています。


