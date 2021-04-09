---
title: Azure Bastion リソース ログを有効にして使用する
description: この記事では、Azure Bastion 診断ログを有効にして使用する方法について説明します。
services: bastion
author: charwen
ms.service: bastion
ms.topic: how-to
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 1e76fffd17ee565d4103ca8a7bf1523bbd16209d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91445390"
---
# <a name="enable-and-work-with-bastion-resource-logs"></a>Bastion リソース ログを有効にして使用する

ユーザーが Azure Bastion を使用してワークロードに接続すると、Bastion ではリモート セッションの診断をログに記録できます。 その後、その診断を使用して、どのユーザーが、どのワークロードに、いつ、どこから接続したかということや、他のそのような関連ログ情報を確認できます。 診断を使用するには、Azure Bastion で診断ログを有効にする必要があります。 この記事では、診断ログを有効にし、ログを表示する方法について説明します。

## <a name="enable-the-resource-log"></a><a name="enable"></a>リソース ログを有効にする

1. [Azure portal](https://portal.azure.com) で、Azure Bastion リソースに移動し、Azure Bastion のページから **[診断設定]** を選択します。

   ![[診断設定] ページを示すスクリーンショット。](./media/diagnostic-logs/1diagnostics-settings.png)
2. **[診断設定]** を選択し、 **[+ 診断設定を追加する]** を選択してログの保存先を追加します。

   ![[診断設定を追加する] ボタンが選択されている [診断設定] ページを示すスクリーンショット。](./media/diagnostic-logs/2add-diagnostic-setting.png)
3. **[診断設定]** ページで、診断ログの保存に使用するストレージ アカウントの種類を選択します。

   ![ストレージの場所を選択するセクションが強調表示されている [診断設定] ページのスクリーンショット。](./media/diagnostic-logs/3add-storage-account.png)
4. 設定が済むと、次の例のようになります。

   ![設定の例](./media/diagnostic-logs/4example-settings.png)

## <a name="view-diagnostics-log"></a><a name="view"></a>診断ログを表示する

診断ログにアクセスするには、診断設定を有効にするときに指定したストレージ アカウントを直接使用できます。

1. お使いのストレージ アカウント リソースに移動し、 **[コンテナー]** に移動します。 ストレージ アカウントの BLOB コンテナーに、**insights-logs-bastionauditlogs** BLOB が作成されていることがわかります。

   ![診断設定](./media/diagnostic-logs/1-navigate-to-logs.png)
2. コンテナー内に移動すると、BLOB の中にあるさまざまなフォルダーが表示されます。 これらのフォルダーでは、Azure Bastion リソースのリソース階層が示されています。

   ![診断設定を追加する](./media/diagnostic-logs/2-resource-h.png)
3. アクセス/表示する診断ログが含まれる Azure Bastion リソースの完全な階層に移動します。 'y='、'm='、'd='、'h='、'm=' は、それぞれ、リソース ログの年、月、日、時、分を示します。

   ![ストレージの場所を選択する](./media/diagnostic-logs/3-resource-location.png)
4. 移動した期間の診断ログ データが含まれる、Azure Bastion によって作成された JSON ファイルを探します。

5. ストレージ BLOB コンテナーから JSON ファイルをダウンロードします。 JSON ファイルのエントリの例を次に示します。

   ```json
   { 
   "time":"2019-10-03T16:03:34.776Z",
   "resourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.NETWORK/BASTIONHOSTS/MYBASTION-BASTION",
   "operationName":"Microsoft.Network/BastionHost/connect",
   "category":"BastionAuditLogs",
   "level":"Informational",
   "location":"eastus",
   "properties":{ 
      "userName":"<username>",
      "userAgent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36",
      "clientIpAddress":"131.107.159.86",
      "clientPort":24039,
      "protocol":"ssh",
      "targetResourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/LINUX-KEY",
      "subscriptionId":"<subscripionID>",
      "message":"Successfully Connected.",
      "resourceType":"VM",
      "targetVMIPAddress":"172.16.1.5",
      "tunnelId":"<tunnelID>"
   },
   "FluentdIngestTimestamp":"2019-10-03T16:03:34.0000000Z",
   "Region":"eastus",
   "CustomerSubscriptionId":"<subscripionID>"
   }
   ```

## <a name="next-steps"></a>次のステップ

[Azure Bastion に関する FAQ](bastion-faq.md) を読む。
