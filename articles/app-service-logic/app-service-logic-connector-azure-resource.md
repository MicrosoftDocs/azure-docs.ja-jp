---
title: "ロジックアプリでの Azure リソース コネクタの使用 | Microsoft Docs"
description: "Azure リソース コネクタまたは API アプリを作成、構成して、Azure App Service のロジック アプリで使用する方法"
services: logic-apps
documentationcenter: .net,nodejs,java
author: stepsic-microsoft-com
manager: erikre
editor: 
ms.assetid: a32e5a5c-34d7-4422-b0f7-c5cf7b8abffa
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/01/2016
ms.author: stepsic
translationtype: Human Translation
ms.sourcegitcommit: 5b1b65e3d1066bea6958fa6461a157ee39fbe7dc
ms.openlocfilehash: d230450535613e85c607ef120929ea61bc2085bc
ms.lasthandoff: 02/15/2017


---
# <a name="get-started-with-the-azure-resource-connector-and-add-it-to-your-logic-app"></a>Azure リソース コネクタの使用開始とロジック アプリへの追加
> [!NOTE]
> 本記事は、ロジック アプリの 2014-12-01-preview スキーマ バージョンを対象としています。
> 
> 

Azure リソース コネクタを使用して、Logic App 内の Azure リソースを簡単に管理できます。

## <a name="create-the-azure-resource-connector"></a>Azure リソース コネクタの作成
Azure リソース コネクタ API アプリを使用するにはまず、そのインスタンスを作成する必要があります。 インスタンスの作成には、ロジック アプリを作成するときにインラインで行う方法と、Azure Marketplace から Azure Resource Manger Connector API アプリを選択して行う方法とがあります。

インスタンスを構成するには、Azure での作業に必要な権限を持ったサービス プリンシパルを設定する必要があります。 すべての呼び出しは、ここで設定したサービス プリンシパルの下で実行されます。 これによってコネクタの動作を作業上必要な範囲内に限定することができます。

David Ebbo の [ブログ記事](http://blog.davidebbo.com/2014/12/azure-service-principal.html) に、この設定方法が詳しく解説されています。 解説されている手順に従って、**テナント ID**、**クライアント ID**、**シークレット**を入手してください。 その&3; つのフィールドと **サブスクリプション ID**が、コネクタを構成するうえで必要となります。

## <a name="using-the-azure-resource-connector-in-logic-app-designer"></a>ロジック アプリ デザイナーでの Azure リソース コネクタの使用
### <a name="trigger"></a>トリガー
Azure リソース コネクタでは、2 つのトリガーがサポートされています。

| 名前 | Description |
| --- | --- |
| Event occurs |ご利用のサブスクリプション内のリソースに対してイベントが発生したときに作用するトリガーです。 |
| Metric crosses threshold |メトリックが特定のしきい値を満たしたときに作用するトリガーです。 |

### <a name="action"></a>アクション
同様に、ご利用の Azure サブスクリプション内で多数のアクションを指定することができます。

**リソース グループ** に関しては、次のアクションを実行できます。

| 名前 | Description |
| --- | --- |
| List resource groups |サブスクリプションに含まれているすべてのリソース グループをリストします。 |
| Get resource group |リソース グループをその ID で取得します。 |
| Create resource group |リソース グループを作成または更新します。 |
| Delete resource group |リソース グループを削除します。 |

**リソース** に関しては、次のアクションを実行できます。

| 名前 | Description |
| --- | --- |
| List resources |サブスクリプションに含まれているリソースを各種フィルターでリストします。 |
| Get resource |単一のリソースをそのリソース ID で取得します。 |
| Create or update resource |リソースを作成したり、既存のリソースを更新したりします。 対象となるリソースのすべてのプロパティを指定する必要があります。 |
| Resource action |その他のアクションをリソースに対して実行します。 アクションの名前とそのアクションに渡されるペイロード (存在する場合) を把握しておく必要があります。 |
| Delete resource |リソースを削除します。 |

**リソース プロバイダー** に関しては、次のアクションを実行できます。

| 名前 | Description |
| --- | --- |
| List resource providers |サブスクリプション内の利用可能なリソース プロバイダーをすべてリストします。 |

**リソース グループ デプロイ** に関しては、次のアクションを実行できます。

| 名前 | Description |
| --- | --- |
| List deployments |リソース グループに含まれているデプロイをすべてリストします。 |
| Get deployment |テンプレート デプロイをその ID で取得します。 |
| Create deployment |テンプレートを指定して新しいリソース グループ デプロイを作成します。 |

リソースについての **イベント** に関しては、次のアクションを実行できます。

| 名前 | Description |
| --- | --- |
| Get events |サブスクリプション内のイベントやリソースのイベントを取得します。 |

リソースについての **メトリック** に関しては、次のアクションを実行できます。

| 名前 | Description |
| --- | --- |
| Get metrics |リソース ID のメトリックを取得します。 |

## <a name="do-more-with-your-connector"></a>コネクタでできること
これでコネクタが作成されたため、ロジック アプリを使用してこのコネクタをビジネス フローに追加することができます。 「 [Logic Apps とは](../logic-apps/logic-apps-what-are-logic-apps.md)」を参照してください。

> [!NOTE]
> Azure アカウントにサインアップする前に Azure Logic Apps の使用を開始する場合は、[ロジック アプリの試用](https://azure.microsoft.com/try/app-service/logic/)に関するページを参照してください。そこでは、App Service で有効期間の短いスターター ロジック アプリをすぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
> 
> 

「 [Connectors and API アプリReference (コネクタと API アプリのリファレンス)](http://go.microsoft.com/fwlink/p/?LinkId=529766)」で Swagger REST API のリファレンスを参照してください。

<!--References -->

<!--Links -->
[Creating a Logic app]: app-service-logic-create-a-logic-app.md

