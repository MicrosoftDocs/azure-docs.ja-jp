---
title: Azure portal を使用して新しい推奨事項に合わせて Azure Advisor アラートを作成する
description: 新しい推奨事項に合わせて Azure Advisor アラートを作成する
ms.topic: article
ms.date: 09/09/2019
ms.openlocfilehash: 3c51479821914ef34edcd13d8708344169f17aae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100590117"
---
# <a name="create-azure-advisor-alerts-on-new-recommendations-using-the-azure-portal"></a>Azure portal を使用して新しい推奨事項に合わせて Azure Advisor アラートを作成する 

この記事では、Azure portal を使用して Azure Advisor から新しい推奨事項に合わせてアラートを設定する方法について説明します。 

Azure Advisor でリソースの 1 つに対して新しい推奨事項が検出されるたびに、イベントが [Azure アクティビティ ログ](../azure-monitor/essentials/platform-logs-overview.md)に格納されます。 これらのイベントのアラートは、Azure Advisor から推奨事項固有のアラート作成操作を使用して設定できます。 サブスクリプションを選択し、必要に応じてリソース グループを選択して、アラートを受信するリソースを指定することができます。 

また、次のプロパティを使用して、推奨事項の種類を指定することもできます。

* カテゴリ
* 影響レベル
* 推奨事項の種類

アラートがトリガーされたときに実行されるアクションは、次の方法で構成することもできます。  

* 既存のアクション グループを選択する
* 新しいアクション グループを作成する

アクション グループの詳細については、[アクション グループの作成および管理](../azure-monitor/alerts/action-groups.md)に関するページを参照してください。

> [!NOTE] 
> 現在、Advisor アラートは、高可用性、パフォーマンス、コストに関する推奨事項にのみ使用できます。 セキュリティに関する推奨事項はサポートされていません。 

## <a name="create-alert-rule"></a>アラート ルールの作成
1. **ポータル** で、 **[Azure Advisor]** を選択します。

    ![ポータルの Azure Advisor](./media/advisor-alerts/create1.png)

2. 左側のメニューの **[監視]** セクションで、 **[アラート]** を選択します。 

    ![Advisor のアラート](./media/advisor-alerts/create2.png)

3. **[New Advisor Alert]\(新しい Advisor アラート\)** を選択します。

    ![新しい Advisor アラート](./media/advisor-alerts/create3.png)

4. **[スコープ]** セクションで、アラートを表示するサブスクリプションと、必要に応じてリソース グループを選択します。 

    ![Advisor アラートのスコープ](./media/advisor-alerts/create4.png)

5. **[条件]** セクションで、アラートの構成に使用する方法を選択します。 特定のカテゴリまたは影響レベルのすべての推奨事項についてアラートを表示する場合は、 **[Category and impact level]\(カテゴリと影響レベル\)** を選択します。 特定の種類のすべての推奨事項についてアラートを表示する場合は、 **[推奨事項の種類]** を選択します。

    ![Azure Advisor アラートの条件](./media/advisor-alerts/create5.png)

6. 選択した [構成] オプションによっては、条件を指定できます。 すべての推奨事項を使用する場合は、残りのフィールドを空白のままにします。 

    ![Advisor アラートのアクション グループ](./media/advisor-alerts/create6.png)

7. **[アクション グループ]** セクションで、 **[Add existing]\(既存の追加\)** を選択して既に作成したアクション グループを使用するか、 **[Create new]\(新規作成\)** を選択して新しい [アクション グループ](../azure-monitor/alerts/action-groups.md)を設定します。 

    ![Advisor アラートの既存の追加](./media/advisor-alerts/create7.png)

8. [アラートの詳細] セクションで、アラートの名前と簡単な説明を指定します。 アラートを有効にする場合は、 **[ルールの作成時に有効にする]** を **[はい]** に設定します。 次に、アラートの保存先となるリソース グループを選択します。 これは、推奨事項の対象スコープには影響しません。 

    :::image type="content" source="./media/advisor-alerts/create8.png" alt-text="[アラートの詳細] セクションのスクリーンショット。":::


## <a name="configure-recommendation-alerts-to-use-a-webhook"></a>Webhook を使用するように推奨事項のアラートを構成する
このセクションでは、Webhook を使用して推奨事項データを既存のシステムに送信するように Azure Advisor アラートを構成する方法について説明します。 

リソースの 1 つに関する新しい Advisor の推奨事項がある場合に通知されるようにアラートを設定できます。 これらのアラートでは、メールやテキスト メッセージを使用して通知できますが、Webhook を使用して既存のシステムと統合することもできます。 


### <a name="using-the-advisor-recommendation-alert-payload"></a>Advisor の推奨事項アラート ペイロードの使用
Webhook を使用して独自のシステムに Advisor アラートを統合する場合は、通知から送信される JSON ペイロードを解析する必要があります。 

このアラートのアクション グループを設定するときに、共通のアラート スキーマを使用するかどうかを選択します。 共通のアラート スキーマを選択した場合、ペイロードは次のようになります。 

```json
{  
   "schemaId":"azureMonitorCommonAlertSchema",
   "data":{  
      "essentials":{  
         "alertId":"/subscriptions/<subid>/providers/Microsoft.AlertsManagement/alerts/<alerted>",
         "alertRule":"Webhhook-test",
         "severity":"Sev4",
         "signalType":"Activity Log",
         "monitorCondition":"Fired",
         "monitoringService":"Activity Log - Recommendation",
         "alertTargetIDs":[  
            "/subscriptions/<subid>/resourcegroups/<resource group name>/providers/microsoft.dbformariadb/servers/<resource name>"
         ],
         "originAlertId":"001d8b40-5d41-4310-afd7-d65c9d4428ed",
         "firedDateTime":"2019-07-17T23:00:57.3858656Z",
         "description":"A new recommendation is available.",
         "essentialsVersion":"1.0",
         "alertContextVersion":"1.0"
      },
      "alertContext":{  
         "channels":"Operation",
         "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
         "caller":"Microsoft.Advisor",
         "correlationId":"8554b847-2a72-48ef-9776-600aca3c3aab",
         "eventSource":"Recommendation",
         "eventTimestamp":"2019-07-17T22:28:54.1566942+00:00",
         "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
         "eventDataId":"001d8b40-5d41-4310-afd7-d65c9d4428ed",
         "level":"Informational",
         "operationName":"Microsoft.Advisor/recommendations/available/action",
         "properties":{  
            "recommendationSchemaVersion":"1.0",
            "recommendationCategory":"Performance",
            "recommendationImpact":"Medium",
            "recommendationName":"Increase the MariaDB server vCores",
            "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/source/ActivityLog/recommendationTypeId/a5f888e3-8cf4-4491-b2ba-b120e14eb7ce/resourceId/%2Fsubscriptions%<subscription id>%2FresourceGroups%2<resource group name>%2Fproviders%2FMicrosoft.DBforMariaDB%2Fservers%2F<resource name>",
            "recommendationType":"a5f888e3-8cf4-4491-b2ba-b120e14eb7ce"
         },
         "status":"Active",
         "subStatus":"",
         "submissionTimestamp":"2019-07-17T22:28:54.1566942+00:00"
      }
   }
}
  ```

共通スキーマを使用しない場合、ペイロードは次のようになります。 

```json
{  
   "schemaId":"Microsoft.Insights/activityLogs",
   "data":{  
      "status":"Activated",
      "context":{  
         "activityLog":{  
            "channels":"Operation",
            "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
            "caller":"Microsoft.Advisor",
            "correlationId":"3ea7320f-c002-4062-adb8-96d3bd92a5f4",
            "description":"A new recommendation is available.",
            "eventSource":"Recommendation",
            "eventTimestamp":"2019-07-17T20:36:39.3966926+00:00",
            "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
            "eventDataId":"a12b8e59-0b1d-4003-bfdc-3d8152922e59",
            "level":"Informational",
            "operationName":"Microsoft.Advisor/recommendations/available/action",
            "properties":{  
               "recommendationSchemaVersion":"1.0",
               "recommendationCategory":"Performance",
               "recommendationImpact":"Medium",
               "recommendationName":"Increase the MariaDB server vCores",
               "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/source/ActivityLog/recommendationTypeId/a5f888e3-8cf4-4491-b2ba-b120e14eb7ce/resourceId/%2Fsubscriptions%2F<subscription id>%2FresourceGroups%2F<resource group name>%2Fproviders%2FMicrosoft.DBforMariaDB%2Fservers%2F<resource name>",
               "recommendationType":"a5f888e3-8cf4-4491-b2ba-b120e14eb7ce"
            },
            "resourceId":"/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/microsoft.dbformariadb/servers/<resource name>",
            "resourceGroupName":"<resource group name>",
            "resourceProviderName":"MICROSOFT.DBFORMARIADB",
            "status":"Active",
            "subStatus":"",
            "subscriptionId":"<subscription id>",
            "submissionTimestamp":"2019-07-17T20:36:39.3966926+00:00",
            "resourceType":"MICROSOFT.DBFORMARIADB/SERVERS"
         }
      },
      "properties":{  
 
      }
   }
}
```

いずれのスキーマでも、**eventSource** が `Recommendation` であり、**operationName** が `Microsoft.Advisor/recommendations/available/action` であることを確認することで、Advisor の推奨事項イベントを識別できます。

使用することができるその他の重要なフィールドの一部を次に示します。 

* *alertTargetIDs* (共通スキーマ) または *resourceId* (レガシ スキーマ)
* *recommendationType*
* *recommendationName*
* *recommendationCategory*
* *recommendationImpact*
* *recommendationResourceLink*


## <a name="manage-your-alerts"></a>アラートの管理 

Azure Advisor から、推奨事項のアラートを編集、削除、または無効および有効にすることができます。 

1. **ポータル** で、 **[Azure Advisor]** を選択します。

    :::image type="content" source="./media/advisor-alerts/create1.png" alt-text="Azure Advisor が選択されていることを示す Azure portal メニューのスクリーンショット。":::

2. 左側のメニューの **[監視]** セクションで、 **[アラート]** を選択します。

    :::image type="content" source="./media/advisor-alerts/create2.png" alt-text="[アラート] が選択されていることを示す Azure portal メニューのスクリーンショット。":::

3. アラートを編集するには、アラート名をクリックしてアラートを開き、編集するフィールドを編集します。

4. アラートを削除、有効、または無効にするには、行の末尾にある省略記号をクリックし、実行するアクションを選択します。
 

## <a name="next-steps"></a>次のステップ
- [アクティビティ ログ アラートの概要](../azure-monitor/alerts/alerts-overview.md)を把握し、アラートを受信する方法について学習します。
- [アクション グループ](../azure-monitor/alerts/action-groups.md)について学習します。
