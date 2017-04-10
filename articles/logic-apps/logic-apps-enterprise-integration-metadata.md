---
title: "統合アカウント アーティファクト メタデータの管理 - Azure Logic Apps | Microsoft Docs"
description: "Azure Logic Apps の統合アカウントからアーティファクト メタデータを追加または取得する"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 11/21/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: ca8537a7e35e44efafb9b0a9d6ddefe069067475
ms.openlocfilehash: de55bf7d9e68146745c90b96f280c8a71e27b2f9
ms.lasthandoff: 03/02/2017

---

# <a name="manage-artifact-metadata-in-integration-accounts-for-logic-apps"></a>ロジック アプリの統合アカウントでアーティファクト メタデータを管理する

統合アカウント内でアーティファクトのカスタム メタデータを定義し、ロジック アプリの実行時にそのメタデータを取得することができます。 たとえば、パートナー、契約、スキーマ、マップなどのアーティファクトに対してメタデータを指定することができます。これらすべてのアーティファクトには、キーと値のペアを使用してメタデータが保存されます。 現在、アーティファクトでは UI を通じてメタデータを作成することはできませんが、REST API を使用してメタデータを作成することができます。 Azure Portal でパートナー、契約、またはスキーマを作成または選択する際にメタデータを追加するには、**[JSON として編集]** を選択します。 ロジック アプリ内でアーティファクト メタデータを取得するには、統合アカウント アーティファクト検索機能を使用できます。

## <a name="add-metadata-to-artifacts-in-integration-accounts"></a>統合アカウント内のアーティファクトにメタデータを追加する

1. [統合アカウント](logic-apps-enterprise-integration-create-integration-account.md)を作成します。

2. 統合アカウントにアーティファクトを追加します (たとえば、[パートナー](logic-apps-enterprise-integration-partners.md#how-to-create-a-partner)、[契約](logic-apps-enterprise-integration-agreements.md#how-to-create-agreements)、[スキーマ](logic-apps-enterprise-integration-schemas.md))。

3.    アーティファクトを選択し、**[JSON として編集]** を選択して、メタデータの詳細を入力します。

    ![メタデータを入力する](media/logic-apps-enterprise-integration-metadata/image1.png)

## <a name="retrieve-metadata-from-artifacts-for-logic-apps"></a>ロジック アプリのアーティファクトからメタデータを取得する

1. [ロジック アプリ](logic-apps-create-a-logic-app.md)を作成します。

2. [ロジック アプリから統合アカウントへのリンク](logic-apps-enterprise-integration-create-integration-account.md#link-an-integration-account-to-a-logic-app)を作成します。 

3. ロジック アプリ デザイナーで、ロジック アプリに*要求*や *HTTP* などのトリガーを追加します。

4.    **[次のステップ]** > **[アクションの追加]** の順に選択します。 *統合*を検索し、**[統合アカウントのアーティファクトの検索]** を選択します。

    ![[統合アカウントのアーティファクトの検索] を選択する](media/logic-apps-enterprise-integration-metadata/image2.png)

5. **アーティファクトの種類**を選択し、**アーティファクトの名前**を指定します。

    ![アーティファクトの種類を選択し、アーティファクト名を指定する](media/logic-apps-enterprise-integration-metadata/image3.png)

## <a name="example-retrieve-partner-metadata"></a>例: パートナーのメタデータを取得する

パートナーのメタデータには、次の `routingUrl` 情報が含まれています。

![パートナーの "routingURL" メタデータを見つける](media/logic-apps-enterprise-integration-metadata/image6.png)

1. ロジック アプリで、トリガー、パートナーの **[統合アカウントのアーティファクトの検索]** アクション、および **HTTP** を追加します。

    ![ロジック アプリに、トリガー、アーティファクト検索、および "HTTP" を追加する](media/logic-apps-enterprise-integration-metadata/image4.png)

2. URI を取得するには、ロジック アプリのコード ビューに移動します。 ロジック アプリの定義は、次の例のようになります。

    ![参照の検索](media/logic-apps-enterprise-integration-metadata/image5.png)


## <a name="next-steps"></a>次のステップ
* [契約についての詳細情報](logic-apps-enterprise-integration-agreements.md "Enterprise Integration の契約についての詳細情報")  

