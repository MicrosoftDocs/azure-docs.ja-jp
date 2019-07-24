---
title: Azure Resource Manager でのデプロイ操作 | Microsoft Docs
description: Azure Resource Manager のデプロイ操作を、ポータル、PowerShell、Azure CLI、および REST API を使用して表示する方法について説明します。
services: azure-resource-manager,virtual-machines
documentationcenter: ''
tags: top-support-issue
author: tfitzmac
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-multiple
ms.workload: infrastructure
ms.date: 09/28/2018
ms.author: tomfitz
ms.openlocfilehash: 9ff6388c72c631dad870a4f52f86749bfd744d85
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58085628"
---
# <a name="view-deployment-operations-with-azure-resource-manager"></a>Azure Resource Manager でのデプロイ操作の表示

デプロイの操作は、Azure ポータルから確認することができます。 最も一般的な確認の対象としては、デプロイ中にエラーが発生したときに実行されていた操作が挙げられます。この記事では、失敗した操作の表示について重点的に取り上げます。 Azure ポータルには、すぐにエラーを見つけ出し、有効と考えられる解決策を特定できるインターフェイスが用意されています。

監査ログまたはデプロイ操作のいずれかを確認して、デプロイのトラブルシューティングを行うことができます。 このトピックでは、両方の方法を説明します。 特定のデプロイ エラーの解決については、 [Azure Resource Manager を使用してリソースを Azure にデプロイするときに発生する一般的なエラーの解決](resource-manager-common-deployment-errors.md)に関するページを参照してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="portal"></a>ポータル

デプロイ操作を表示するには、次の手順に従います。

1. デプロイに含まれるリソース グループには、最後のデプロイの状態を確認します。 この状態を選択して、詳細を取得することができます。
   
    ![デプロイの状態](./media/resource-manager-deployment-operations/deployment-status.png)
2. 最近のデプロイ履歴が表示されます。 失敗したデプロイを選択します。
   
    ![デプロイの状態](./media/resource-manager-deployment-operations/select-deployment.png)
3. なぜデプロイに失敗したかの説明を表示するには、リンクをクリックします。 以下の画像を見ると、DNS レコードの重複が原因であることがわかります。  
   
    ![失敗したデプロイの表示](./media/resource-manager-deployment-operations/view-error.png)
   
    このエラー メッセージは、トラブルシューティングの糸口をつかむうえでは十分でしょう。 しかし、どのようなタスクが実行されたのかについて、もっと詳しい情報が必要である場合は、以降の手順に従って操作を確認することができます。
4. すべてのデプロイ操作を確認できます。 いずれかの操作を選択すると、さらに詳しい情報が表示されます。
   
    ![view operations](./media/resource-manager-deployment-operations/view-operations.png)
   
    このケースでは、ストレージ アカウント、仮想ネットワーク、可用性セットは正しく作成されていることが確認できます。 パブリック IP アドレスはエラーとなり、他のリソースは実行されていません。
5. デプロイのイベントは、 **[イベント]** を選択して表示できます。
   
    ![view events](./media/resource-manager-deployment-operations/view-events.png)
6. デプロイに関するすべてのイベントが表示され、いずれかを選択すると、さらに詳しい情報が表示されます。 関連付け ID にも注目してください。 この値は、デプロイのトラブルシューティングを行うためにテクニカル サポートと共に作業を行うときに有用である可能性があります。
   
    ![see events](./media/resource-manager-deployment-operations/see-all-events.png)

## <a name="powershell"></a>PowerShell
1. **Get-AzResourceGroupDeployment** コマンドを使用すると、デプロイ全体のステータスを取得できます。 

   ```powershell
   Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
   ```

   または、失敗したデプロイのみの結果をフィルター処理できます。

   ```powershell
   Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
   ```
   
2. 相関 ID を取得するには、以下を使用します。

   ```powershell
   (Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName azuredeploy).CorrelationId
   ```

3. 各デプロイには、複数の操作が含まれます。 各操作は、デプロイ プロセスの手順を表します。 デプロイメントの問題を検出するには、通常デプロイメント操作に関する詳細を確認する必要があります。 操作の状態は、**Get-AzResourceGroupDeploymentOperation** で確認できます。

   ```powershell 
   Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName vmDeployment
   ```

    このコマンドレットを実行すると、複数の操作がそれぞれ次の形式で返されます。

   ```powershell
   Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
   OperationId    : A3EB2DA598E0A780
   Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2016-06-14T21:55:15.0156208Z;
                   duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                   serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
   PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                   serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}
   ```

4. 失敗した操作についての詳しい情報を得るには、状態が **Failed** である操作のプロパティを取得します。

   ```powershell
   (Get-AzResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
   ```
   
    このコマンドレットを実行すると、失敗したすべての操作がそれぞれ次の形式で返されます。

   ```powershell
   provisioningOperation : Create
   provisioningState     : Failed
   timestamp             : 2016-06-14T21:54:55.1468068Z
   duration              : PT3.1449887S
   trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
   serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
   statusCode            : BadRequest
   statusMessage         : @{error=}
   targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                          Microsoft.Network/publicIPAddresses/myPublicIP;
                          resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}
   ```

    操作の serviceRequestId と trackingId に注目してください。 serviceRequestId は、デプロイのトラブルシューティングを行うためにテクニカル サポートと共に作業を行うときに有用である可能性があります。 trackingId は、次の手順で特定の操作に対象を絞り込むために必要となります。
5. 失敗した特定の操作のステータス メッセージを取得するには、次のコマンドを使用します。

   ```powershell
   ((Get-AzResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
   ```

    次のような結果が返されます。

   ```powershell
   code           message                                                                        details
   ----           -------                                                                        -------
   DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}
   ```
6. Azure でのすべてのデプロイ操作には、要求と応答のコンテンツが含まれます。 要求コンテンツは、デプロイ中に Azure に送信するものです (たとえば、VM、OS ディスク、その他のリソースの作成)。 応答コンテンツは、Azure がデプロイメント要求に対して返信するものです。 デプロイの際に **DeploymentDebugLogLevel** パラメーターを使用して、要求または応答 (あるいは両方) がログに保存されるように指定できます。 

   次の PowerShell コマンドを使用すると、ログから情報を取得して、ローカルに保存できます。

   ```powershell
   (Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.request | ConvertTo-Json |  Out-File -FilePath <PathToFile>

   (Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.response | ConvertTo-Json |  Out-File -FilePath <PathToFile>
   ```

## <a name="azure-cli"></a>Azure CLI

1. **azure group deployment show** コマンドを使用して、デプロイ全体の状態を取得できます。

   ```azurecli
   az group deployment show -g ExampleGroup -n ExampleDeployment
   ```
  
2. 値の 1 つとして **correlationId** が返されます。 この値は、関連するイベントを追跡するために使用されます。また、デプロイのトラブルシューティングを行うためにテクニカル サポートと共に作業を行うときにも有用である可能性があります。

   ```azurecli
   az group deployment show -g ExampleGroup -n ExampleDeployment --query properties.correlationId
   ```

3. デプロイ操作を表示するには、次のコマンドを使用します。

   ```azurecli
   az group deployment operation list -g ExampleGroup -n ExampleDeployment
   ```

## <a name="rest"></a>REST

1. [テンプレート デプロイに関する情報を取得する](https://docs.microsoft.com/rest/api/resources/deployments)操作を行って、デプロイに関する情報を取得します。

   ```http
   GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
   ```

    応答では、特に **provisioningState**、**correlationId**、および **error** の各要素に注目します。 **correlationId** は、関連するイベントを追跡するために使用されます。また、デプロイのトラブルシューティングを行うためにテクニカル サポートと共に作業を行うときにも有用である可能性があります。

   ```json
   { 
    ...
    "properties": {
      "provisioningState":"Failed",
      "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
      ...
      "error":{
        "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.",
        "details":[{"code":"Conflict","message":"{\r\n  \"error\": {\r\n    \"message\": \"Conflict\",\r\n    \"code\": \"Conflict\"\r\n  }\r\n}"}]
      }  
    }
   }
   ```

2. [すべてのテンプレート デプロイを一覧表示する](https://docs.microsoft.com/rest/api/resources/deployments)操作を行って、デプロイに関する情報を取得します。 

   ```http
   GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
   ```
   
    応答には、デプロイ時に **debugSetting** プロパティで指定した内容に基づいて、要求または応答の情報が含まれます。

   ```json
   {
    ...
    "properties": 
    {
      ...
      "request":{
        "content":{
          "location":"West US",
          "properties":{
            "accountType": "Standard_LRS"
          }
        }
      },
      "response":{
        "content":{
          "error":{
            "message":"Conflict","code":"Conflict"
          }
        }
      }
    }
   }
   ```


## <a name="next-steps"></a>次の手順
* 特定のデプロイ エラーの解決については、 [Azure Resource Manager を使用してリソースを Azure にデプロイするときに発生する一般的なエラーの解決](resource-manager-common-deployment-errors.md)に関するページを参照してください。
* アクティビティ ログを使用して、その他の種類のアクションを監視する方法については、「[アクティビティ ログを表示して Azure リソースを管理する](resource-group-audit.md)」を参照してください。
* デプロイを実行する前に検証するには、 [Azure Resource Manager テンプレートを使用したリソース グループのデプロイ](resource-group-template-deploy.md)に関するページを参照してください。

