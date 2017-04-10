---
title: "Azure リソース エクスプローラー | Microsoft Docs"
description: "Azure リソース エクスプローラーについて説明すると共に、Azure リソース エクスプローラーから Azure リソース マネージャーを介してデプロイを表示し更新する方法についても示します。"
services: azure-resource-manager
documentationcenter: na
author: stuartleeks
manager: ankodu
editor: 
ms.assetid: 950f2298-b5dd-4343-b925-8ef28493cacf
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: stuartle;tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: 4fec9500470237ee38e404f637dcf08b2cf0394a


---
# <a name="use-azure-resource-explorer-to-view-and-modify-resources"></a>Azure リソース エクスプローラーを使用してリソースを表示および変更する
[Azure リソース エクスプローラー](https://resources.azure.com)は、サブスクリプションで作成済みのリソースを調べるための優れたツールです。 このツールを使用すると、リソースがどのような構成になっているかを把握し、各リソースに割り当てられているプロパティを確認することができます。 リソースの種類で使用できる REST API 操作および PowerShell コマンドレットについて説明します。インターフェイスを介してコマンドを発行することもできます。 既存のリソースのプロパティを表示できるため、リソース エクスプローラーはリソース マネージャーのテンプレートを作成する際に特に有用です。

リソース エクスプローラー ツールのソースは、[GitHub](https://github.com/projectkudu/ARMExplorer) で提供されています。GitHub では、独自のアプリケーションで同様の動作を実装する必要がある場合に役立つリファレンスを入手できます。

## <a name="view-resources"></a>リソースの表示
[https://resources.azure.com](https://resources.azure.com) に移動し、[Azure Portal](https://portal.azure.com) の場合と同じ資格情報を使用してサインインします。

読み込まれたら、左側にあるツリービューで、サブスクリプションとリソース グループにドリル ダウンすることができます。

![ツリー ビュー](./media/resource-manager-resource-explorer/are-01-treeview.png)

リソース グループがドリルダウンしたら、そのリソース グループに含まれているリソースに対応するプロバイダーを確認できます。

![プロバイダー](./media/resource-manager-resource-explorer/are-02-treeview-providers.png)

そこから、リソース インスタンスへのドリル ダウンを開始できます。 次のスクリーン ショットでは、ツリービューに `sltest` という SQL Server インスタンスが表示されているのを確認できます。 右側には、そのリソースで使用できる REST API 要求に関する情報が表示されています。 リソースのノードに移動すると、リソース エクスプローラーによって、そのリソースに関する情報を取得するための GET 要求が自動的に発行されます。 URL の下の広いテキスト領域には、API からの応答が表示されます。 

Resource Manager テンプレートに精通するに従って、本文の内容を理解しやすくなります。 応答の **properties** セクションは、テンプレートの **properties** セクションで指定できる値に対応します。

![SQL Server](./media/resource-manager-resource-explorer/are-03-sqlserver-with-response.png)

リソース エクスプローラーでは、ドリルダウンを続けることで、子リソースを探索できます。SQL Database Server の場合は、データベースやファイアウォール ルールなどに対して子リソースがあります。

データベースを探索すると、そのデータベースのプロパティが表示されます。 次のスクリーン ショットでは、データベース `edition` は `Standard` であり、`serviceLevelObjective` (またはデータベース レベル) は `S1` であることがわかります。

![SQL Database](./media/resource-manager-resource-explorer/are-04-database-get.png)

## <a name="change-resources"></a>リソースの変更
リソースに移動すると、[編集] ボタンを選択することで、JSON コンテンツを編集可能にすることができます。 次に、リソース エクスプローラーを使用して JSON を編集し、PUT 要求を送信してリソースを変更することができます。 たとえば、次の図は、 `S0`に変更されたデータベース層を示しています。

![データベース - PUT 要求](./media/resource-manager-resource-explorer/are-05-database-put.png)

**PUT** を選択することで、要求を送信します。 

要求が送信されると、リソース エクスプローラーは GET 要求を再発行して状態を更新します。 この例では、`requestedServiceObjectiveId` は更新済みであり、スケール操作が進行中であることを示す `currentServiceObjectiveId` とは異なることがわかります。 GET ボタンをクリックすれば、状態を手動で更新することができます。

![データベース - GET 要求 2](./media/resource-manager-resource-explorer/are-06-database-get2.png)

## <a name="performing-actions-on-resources"></a>リソースに対する操作の実行
**[アクション]** タブでは、追加の REST 操作を確認および実行できます。 たとえば、Web サイト リソースを選択すると、[アクション] タブに、使用可能な操作を掲載した長いリストが表示されます。その一部を以下に示します。

![Web - POST 要求](./media/resource-manager-resource-explorer/are-web-post.png)

## <a name="invoking-the-api-via-powershell"></a>PowerShell による API の呼び出し
リソース エクスプローラーの [PowerShell] タブには、現在探索しているリソースとやり取りするために使用できるコマンドレットが表示されます。 表示される PowerShell スクリプトは、選択したリソースの種類に応じて、単純なコマンドレット (`Get-AzureRmResource` や `Set-AzureRmResource` など) から、より複雑なコマンドレット (Web サイトでのスロットのスワップなど) までさまざまです。 

![PowerShell](./media/resource-manager-resource-explorer/are-07-powershell.png)

Azure PowerShell コマンドレットの詳細については、「 [Azure Resource Manager での Azure PowerShell の使用](powershell-azure-resource-manager.md)

## <a name="summary"></a>概要
Resource Manager を使用する場合は、リソース エクスプローラーを使用すると非常に便利です。 PowerShell を使用してどのようにクエリを実行し変更を行うかを考える場合に有効です。 REST API を使用する場合は、コードの記述を開始する前に、起動して API 呼び出しを迅速にテストすることができます。 テンプレートを作成する場合は、リソースの階層を把握し、構成を配置する場所を特定するのに便利です。ポータルで変更を加え、リソース エクスプローラーで対応するエントリを見つけることができます。

詳細については、「 [Channel 9 video with Scott Hanselman and David Ebbo (Scott Hanselman と David Ebbo によるチャネル 9 ビデオ)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Resource-Manager-Explorer-with-David-Ebbo)




<!--HONumber=Nov16_HO3-->


