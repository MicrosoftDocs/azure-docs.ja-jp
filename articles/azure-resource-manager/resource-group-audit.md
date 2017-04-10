---
title: "リソースを監視する Azure アクティビティ ログの表示 | Microsoft Docs"
description: "アクティビティ ログを使用してユーザーの操作やエラーを確認します。 Azure ポータル、PowerShell、Azure CLI、および REST を表示します。"
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: fcdb3125-13ce-4c3b-9087-f514c5e41e73
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 2a9075f4c9f10d05df3b275a39b3629d4ffd095f
ms.openlocfilehash: 9f90bc80c146c6c2da04aacbc110f7d389c0baa2


---
# <a name="view-activity-logs-to-audit-actions-on-resources"></a>リソースのアクションを監査するアクティビティ ログの表示
アクティビティ ログを使用すると、次の内容を判断することができます。

* サブスクリプション内のリソースで行われた操作
* 操作を開始したユーザー (バックエンド サービスによって開始された操作は、呼び出し側としてユーザーを返しません)
* 操作が発生した時間
* 操作の状態
* 操作を調査するために役立つ可能性のあるその他のプロパティの値

[!INCLUDE [resource-manager-audit-limitations](../../includes/resource-manager-audit-limitations.md)]

ポータル、PowerShell、Azure CLI、Insights REST API、または [Insights .NET Library](https://www.nuget.org/packages/Microsoft.Azure.Insights/)を利用し、アクティビティ ログから情報を取得できます。

## <a name="portal"></a>ポータル
1. ポータルからアクティビティ ログを表示するには、**[監視]** を選択します。
   
    ![select activity logs](./media/resource-group-audit/select-monitor.png)

   また、アクティビティ ログに自動的にフィルターを適用して、特定のリソースまたはリソース グループを見つけるには、このリソース ブレードで **[アクティビティ ログ]** を選択します。 アクティビティ ログが選択したリソースで自動的にフィルター処理されることに注意してください。
   
    ![リソースによるフィルター](./media/resource-group-audit/filtered-by-resource.png)
2. **[アクティビティ ログ]** ブレードで、最近の操作の概要を確認します。
   
    ![アクションの表示](./media/resource-group-audit/audit-summary.png)
3. 表示される操作の数を制限するには、異なる条件を選択します。 たとえば次の画像には、ある特定のユーザーまたはアプリケーションによって前月実行された操作を表示するように変更された **[期間]** フィールドと **[イベント開始者]** フィールドが示されています。 **[適用]** を選択してクエリの結果を表示します。
   
    ![フィルター オプションの設定](./media/resource-group-audit/set-filter.png)

4. 後でもう一度クエリを実行する必要がある場合、 **[保存]** を選択してクエリの名前を入力します。
   
    ![save query](./media/resource-group-audit/save-query.png)
5. すぐにクエリを実行するには、失敗したデプロイなど、組み込みクエリのいずれかを選択します。

    ![クエリの選択](./media/resource-group-audit/select-quick-query.png)

   選択したクエリによって、必要なフィルター値を自動的に設定されます。

    ![デプロイ エラーの表示](./media/resource-group-audit/view-failed-deployment.png)   

6. いずれかの操作を選択して、イベントの概要を表示します。

    ![表示操作](./media/resource-group-audit/view-operation.png)  

## <a name="powershell"></a>PowerShell
1. ログ エントリを取得するには、 **Get-AzureRmLog** コマンドを実行します。 パラメーターを追加し、エントリの一覧を絞り込むことができます。 開始時間と終了時間を指定しない場合は、過去&1; 時間のエントリが返されます。 たとえば、過去&1; 時間のリソース グループの操作を取得するには、次を実行します。

  ```powershell
  Get-AzureRmLog -ResourceGroup ExampleGroup
  ```
   
    次の例は、アクティビティ ログを使用して、指定した時間に行われた操作を調査する方法を示しています。 開始日と終了日は、日付の形式で指定されます。

  ```powershell
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00
  ```

    または、日付関数を使用して、最後の 14 日など、日付の範囲を指定することができます。
   
  ```powershell 
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
  ```

2. 指定した開始時刻によっては、前のコマンドを実行したときに、そのリソース グループの操作が長い一覧で返されることがあります。 検索基準を指定すると、探しものの結果を絞り込むことができます。 たとえば、Web アプリが停止した理由を調査する場合は、次のコマンドを実行します。

  ```powershell
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) | Where-Object OperationName -eq Microsoft.Web/sites/stop/action
  ```

    この例では、someone@contoso.com によって停止アクションが実行されたことが示されています。 

  ```powershell 
  Authorization     :
  Scope     : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
  Action    : Microsoft.Web/sites/stop/action
  Role      : Subscription Admin
  Condition :
  Caller            : someone@contoso.com
  CorrelationId     : 84beae59-92aa-4662-a6fc-b6fecc0ff8da
  EventSource       : Administrative
  EventTimestamp    : 8/28/2015 4:08:18 PM
  OperationName     : Microsoft.Web/sites/stop/action
  ResourceGroupName : ExampleGroup
  ResourceId        : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
  Status            : Succeeded
  SubscriptionId    : xxxxx
  SubStatus         : OK
  ```

3. 存在しなくなったリソース グループであっても、特定のユーザーが行ったアクションを検索できます。

  ```powershell 
  Get-AzureRmLog -ResourceGroup deletedgroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
  ```

4. フィルターを使って、失敗した操作を抽出できます。

  ```powershell
  Get-AzureRmLog -ResourceGroup ExampleGroup -Status Failed
  ```

5. 1 つのエラーに焦点を当てるには、そのエントリのステータス メッセージを確認します。
   
        ((Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties[1].Content["statusMessage"] | ConvertFrom-Json).error
   
    次のような結果が返されます。
   
        code           message                                                                        
        ----           -------                                                                        
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. 


## <a name="azure-cli"></a>Azure CLI
* ログ エントリを取得するには、 **azure group log show** コマンドを実行します。

  ```azurecli
  azure group log show ExampleGroup --json
  ```


## <a name="rest-api"></a>REST API
アクティビティ ログを利用するための REST 操作は [Insights REST API](https://msdn.microsoft.com/library/azure/dn931943.aspx)に含まれています。 アクティビティ ログのイベントを取得するには、 [サブスクリプション内の管理イベントの一覧表示](https://msdn.microsoft.com/library/azure/dn931934.aspx)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
* Azure アクティビティ ログは、サブスクリプション内のアクションに関してさらに洞察を得るために、Power BI で使用できます。 [Power BI などでの Azure アクティビティ ログの表示と分析](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)に関する記事をご覧ください。
* セキュリティ ポリシーを設定する方法については、「[Azure のロールベースのアクセス制御](../active-directory/role-based-access-control-configure.md)」を参照してください。
* デプロイ操作を表示するコマンドについては、[デプロイ操作の表示](resource-manager-deployment-operations.md)に関するページをご覧ください。
* すべてのユーザーのリソースに対する削除を回避する方法については、「 [Azure Resource Manager によるリソースのロック](resource-group-lock-resources.md)」をご覧ください。




<!--HONumber=Jan17_HO4-->


