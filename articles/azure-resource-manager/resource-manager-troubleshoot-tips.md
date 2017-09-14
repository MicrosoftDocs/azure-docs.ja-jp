---
title: "Azure のデプロイ エラーを理解する | Microsoft Docs"
description: "Azure のデプロイ エラーを理解する方法について説明します。"
services: azure-resource-manager
documentationcenter: 
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: "デプロイのエラー、Azure へのデプロイ、Azure へのデプロイ"
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: b67bb30fa259fa08e37e11afec724c8b8c3eb633
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---

# <a name="understand-azure-deployment-errors"></a>Azure のデプロイ エラーを理解する
このトピックでは、デプロイ エラー、およびエラーに関する詳細情報を見つける方法について説明します。 一般的なデプロイ エラーの解決方法については、「[Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング](resource-manager-common-deployment-errors.md)」を参照してください。

## <a name="two-types-of-errors"></a>2 種類のエラー
発生する可能性のあるエラーには、次の 2 種類があります。

* 検証エラー
* デプロイ エラー

次の図は、サブスクリプションのアクティビティ ログを示しています。 2 つのデプロイを表しています。 1 つのデプロイでは、テンプレートは検証に失敗し (**Validate**)、続行できませんでした。 もう 1 つのデプロイでは、テンプレートは検証を通過しましたが、リソースの作成時に失敗しました (**Write Deployments**)。 

![エラー コードの表示](./media/resource-manager-troubleshoot-tips/show-activity-log.png)

検証エラーは、デプロイ前に判断できるシナリオで発生します。 これには、テンプレートに構文エラーがある場合や、サブスクリプション クォータを超えるリソースをデプロイしようとしている場合などがあります。 デプロイ エラーは、デプロイ プロセスで発生する状況に起因します。 これには、並列でデプロイされているリソースにアクセスしようとしている場合などがあります。

この 2 種類のエラーでは、デプロイのトラブルシューティングに使用するエラー コードが返されます。 どちらの種類のエラーも[アクティビティ ログ](resource-group-audit.md)に表示されます。 ただし、検証エラーは、デプロイが開始されていないため、デプロイ履歴には表示されません。

## <a name="determine-error-code"></a>エラー コードの判断

エラー メッセージとエラー コードを調べると、エラーについて理解できます。 「[Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング](resource-manager-common-deployment-errors.md)」の記事では、エラー コード別の解決方法を一覧しています。 このトピックでは、Azure ポータルを使用して、エラー コードを検出する方法について示します。

### <a name="validation-errors"></a>検証エラー

ポータルでのデプロイ時に、値を送信した後に検証エラーを確認します。

![ポータルの検証エラーの表示](./media/resource-manager-troubleshoot-tips/validation-error.png)

詳細については、メッセージを選択します。 次の図では、**InvalidTemplateDeployment** エラーとポリシーがデプロイをブロックしていることを示すメッセージが示されます。

![検証の詳細の表示](./media/resource-manager-troubleshoot-tips/validation-details.png)

### <a name="deployment-errors"></a>デプロイ エラー

操作で検証を通過しますが、デプロイ中に失敗した場合、通知でエラーが表示されます。 通知を選択します。

![通知エラー](./media/resource-manager-troubleshoot-tips/notification.png)

デプロイに関する詳細を表示します。 オプションを選択して、エラーの詳細を確認します。

![デプロイの失敗](./media/resource-manager-troubleshoot-tips/deployment-failed.png)

エラー メッセージとエラー コードを表示します。 2 つのエラー コードがあることを確認します。 最初のエラー コード (**DeploymentFailed**) は、解決する必要があるエラーの詳細が示されない一般的なエラーです。 2 つ目のエラー コード (**StorageAccountNotFound**) は、必要な詳細が示されます。 

![エラーの詳細](./media/resource-manager-troubleshoot-tips/error-details.png)

## <a name="enable-debug-logging"></a>デバッグ ログの有効化
問題が発生した原因を調べるには、要求と応答の詳細が必要な場合があります。 PowerShell または Azure CLI を使用して、デプロイ時に追加情報をログに記録することを要求できます。

- PowerShell

   PowerShell では、**DeploymentDebugLogLevel** パラメーターを All、ResponseContent、または RequestContent に設定します。

  ```powershell
  New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile c:\Azure\Templates\storage.json -DeploymentDebugLogLevel All
  ```

   次のコマンドレットを使用して、要求の内容を確認します。

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.request | ConvertTo-Json
  ```

   次のコマンドレットを使用して、応答の内容を確認します。

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.response | ConvertTo-Json
  ```

   この情報は、テンプレートの値が正しく設定されているかどうかを確認するのに役立ちます。

- Azure CLI

   次のコマンドでデプロイ操作を確認します。

  ```azurecli
  az group deployment operation list --resource-group ExampleGroup --name vmlinux
  ```

- 入れ子になったテンプレート

   入れ子になったテンプレートのデバッグ情報をログに記録するには、**debugSetting** 要素を使用します。

  ```json
  {
      "apiVersion": "2016-09-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri": "{template-uri}",
              "contentVersion": "1.0.0.0"
          },
          "debugSetting": {
             "detailLevel": "requestContent, responseContent"
          }
      }
  }
  ```


## <a name="create-a-troubleshooting-template"></a>トラブルシューティング用テンプレートの作成
場合によっては、テンプレートのトラブルシューティングの最も簡単な方法は、テンプレートの一部をテストすることです。 エラーの原因と考えられる部分に注目できる簡略化されたテンプレートを作成できます。 たとえば、リソースの参照時にエラーが発生しているとします。 この場合、テンプレート全体を処理するのではなく、問題の原因と考えられる部分を返すテンプレートを作成します。 これにより、適切なパラメーターを渡しているかどうか、テンプレート関数を正しく使用しているかどうか、求めているリソースを取得しているかどうかを確認できます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
        "type": "string"
    },
    "storageResourceGroup": {
        "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "exampleOutput": {
        "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
        "type" : "object"
    }
  }
}
```

または、設定の誤った依存関係に関連すると思われるデプロイ エラーが発生していると仮定します。 テンプレートをテストするには、これを簡略化されたテンプレートに分解します。 最初に、1 つのリソース (SQL Server など) のみをデプロイするテンプレートを作成します。 そのリソースを正しく定義したことを確認したら、それに依存するリソース (SQL Database など) を追加します。 これら 2 つのリソースを正しく定義したことを確認したら、その他の依存リソース (監査ポリシーなど) を追加します。 各テスト デプロイの合間に、リソース グループを削除して、依存関係を適切にテストしていることを確認します。 

## <a name="check-deployment-sequence"></a>デプロイの順序の確認

リソースが予期しない順序でデプロイされると、多数のデプロイ エラーが発生します。 これらのエラーは、依存関係が正しく設定されていない場合に発生します。 必要な依存関係がないと、あるリソースが別のリソースの値を使用しようとしても、そのリソースがまだ存在しません。 リソースが見つからなかったことを示すエラー メッセージが表示されます。 リソースのデプロイ時間はそれぞれ異なるため、この種のエラーは断続的に発生する可能性があります。 たとえば、最初のリソースのデプロイの試行は成功しました。これは、必要なリソースがランダムで時間内に完了したためです。 しかし、2 回目の試行は失敗しました。これは、必要なリソースが時間内に完了しなかったためです。 

また、不要な依存関係を設定するのは望ましくありません。 不要な依存関係があると、相互に依存していないリソースを並行してデプロイすることを妨げるため、デプロイ時間が長くなります。 さらに、循環依存関係が作成されてデプロイがブロックされる恐れがあります。 [reference](resource-group-template-functions-resource.md#reference) 関数は、リソースが同じテンプレートにデプロイされる場合、参照されたリソースに対する暗黙的な依存関係を作成します。 このため、**dependsOn** プロパティで指定したよりも多くの依存関係が作成されることがあります。 [resourceId](resource-group-template-functions-resource.md#resourceid) 関数は暗黙的な依存関係を作成しません。また、リソースの存在を検証することもしません。

依存関係の問題が発生した場合は、リソースのデプロイ順序を把握する必要があります。 デプロイ操作の順序を確認するには、次の手順に従います。

1. リソース グループのデプロイ履歴を選択します。

   ![デプロイ履歴の選択](./media/resource-manager-troubleshoot-tips/select-deployment.png)

2. 履歴からデプロイを選択し、**[イベント]** を選択します。

   ![デプロイ イベントの選択](./media/resource-manager-troubleshoot-tips/select-deployment-events.png)

3. 各リソースのイベントの順序を調べます。 各操作の状態に注意してください。 たとえば、次の図には、並列でデプロイされた 3 つのストレージ アカウントが示されています。 3 つとも同時に開始されていることがわかります。

   ![並列デプロイ](./media/resource-manager-troubleshoot-tips/deployment-events-parallel.png)

   次の図には、並列でデプロイされていない 3 つのストレージ アカウントが示されています。 2 つ目のストレージ アカウントは最初のストレージ アカウントに依存し、3 つ目のストレージ アカウントは 2 つ目のストレージ アカウントに依存します。 したがって、最初のストレージ アカウントの開始、受け入れ、完了の後に次のストレージ アカウントが開始されます。

   ![連続デプロイ](./media/resource-manager-troubleshoot-tips/deployment-events-sequence.png)

複雑なシナリオでも、各リソースでデプロイが開始および完了したタイミングは同じ手法を使用して検出できます。 デプロイ イベントの順序が想定と異なっていないかを確認します。 異なる場合は、そのリソースの依存関係を調べてください。

Resource Manager では、テンプレートの検証中に循環依存関係を識別します。 具体的に循環依存関係が存在することを示すエラー メッセージが返されます。 循環依存関係を解決するには:

1. テンプレートで、循環依存関係が検出されたリソースを検索します。 
2. **dependsOn** プロパティと**参照**関数の使用を確認し、そのリソースが依存しているリソースを調べます。 
3. それらのリソースが依存しているリソースを確認します。 元のリソースに依存するリソースを見つけるまで依存関係を辿ります。
5. 循環依存関係に関連するリソースについては、すべての **dependsOn** プロパティを調べ、不要な依存関係を特定します。 それら不要な依存関係を削除します。 依存関係が必要であるかどうか確証がない場合は、削除してみてください。 
6. テンプレートを再デプロイします。

**dependsOn** プロパティから値を削除すると、テンプレートをデプロイするときにエラーが発生することがあります。 エラーが発生した場合は、テンプレートの依存関係を元に戻します。 

このアプローチで循環依存関係が解決しない場合は、デプロイ ロジックの一部 (拡張機能や構成設定など) を子リソースに移行することを検討してください。 それらの子リソースを、循環依存関係に関連するリソースの後にデプロイするように構成します。 たとえば、2 つの仮想マシンをデプロイする場合を考えてみます。それぞれ互いに参照するプロパティを設定する必要があるとします。 この仮想マシンは、次の順序でデプロイできます。

1. vm1
2. vm2
3. vm1 の拡張機能は vm1 と vm2 に依存します。 拡張機能は vm2 から取得した値を vm1 に設定します。
4. vm2 の拡張機能は vm1 と vm2 に依存します。 拡張機能は vm1 から取得した値を vm2 に設定します。

このアプローチは App Service アプリでも有効です。 構成値をアプリ リソースの子リソースに移動することを検討してください。 2 つの Web アプリは次の順序でデプロイできます。

1. webapp1
2. webapp2
3. webapp1 の config は webapp1 と webapp2 に依存します。 アプリの設定には webapp2 からの値が含まれています。
4. webapp2 の config は webapp1 と webapp2 に依存します。 アプリの設定には webapp1 からの値が含まれています。


## <a name="next-steps"></a>次のステップ
* 一般的なデプロイ エラーの解決方法については、「[Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング](resource-manager-common-deployment-errors.md)」を参照してください。
* 監査アクションについては、「 [リソース マネージャーの監査操作](resource-group-audit.md)」をご覧ください。
* デプロイ時にエラーが発生した場合の対応については、 [デプロイ操作の確認](resource-manager-deployment-operations.md)に関するページを参照してください。

