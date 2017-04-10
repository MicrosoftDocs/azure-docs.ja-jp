---
title: "XML の検証 - Azure Logic Apps | Microsoft Docs"
description: "Enterprise Integration Pack を使用して、Azure Logic Apps と B2B シナリオでスキーマに対して XML を検証する"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: d700588f-2d8a-4c92-93eb-e1e6e250e760
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: estfan
translationtype: Human Translation
ms.sourcegitcommit: 03cd3f4edd7bb7895efa02475411d813ef44b8b3
ms.openlocfilehash: ae0bb2cb090402f61bb10299e50024f1f2938489


---
# <a name="validate-xml-for-enterprise-integration"></a>Enterprise Integration での XML の検証

B2B のシナリオでは多くの場合、契約の対象となるパートナーは、データの処理が開始される前に、パートナーの間で交換されるメッセージが有効であることを検証する必要があります。 Enterprise Integration Pack では、XML 検証コネクタを使用して、定義済みのスキーマに対してドキュメントを検証できます。

## <a name="validate-a-document-with-the-xml-validation-connector"></a>XML 検証コネクタを使用してドキュメントを検証する

1. ロジック アプリを作成し、XML データの検証に使用するスキーマが含まれた[統合アカウントにアプリをリンク](../logic-apps/logic-apps-enterprise-integration-accounts.md "ロジック アプリへの統合アカウントのリンクについての詳細情報")します。

2. ロジック アプリに **[Request - When an HTTP request is received (要求 - HTTP 要求を受信したとき)]** トリガーを追加します。

    ![](./media/logic-apps-enterprise-integration-xml/xml-1.png)

3. **[XML の検証]** アクションを追加するには、**[アクションの追加]** を選択します。

4. 検索ボックスに「*xml*」と入力し、すべてのアクションから使用するアクションだけを抽出します。 **[XML の検証]** を選択します。

    ![](./media/logic-apps-enterprise-integration-xml/xml-2.png)

5. 検証する XML コンテンツを指定するには、**[コンテンツ]** を選択します。

    ![](./media/logic-apps-enterprise-integration-xml/xml-1-5.png)

6. 検証する内容として body タグを選択します。

    ![](./media/logic-apps-enterprise-integration-xml/xml-3.png)

7. 前の *[コンテンツ]* で入力した内容を検証するためのスキーマを指定するには、**[スキーマ名]** を選択します。

    ![](./media/logic-apps-enterprise-integration-xml/xml-4.png)

8. 作業内容を保存します。  

    ![](./media/logic-apps-enterprise-integration-xml/xml-5.png)

これで、検証コネクタのセットアップが完了しました。 実際のアプリケーションでは、検証したデータを Salesforce などの業務 (LOB) アプリケーションに保存する必要がある場合があります。 検証済みの出力を Salesforce に送信するには、アクションを追加します。

検証アクションをテストするには、HTTP エンドポイントに要求を送信します。

## <a name="next-steps"></a>次のステップ
[Enterprise Integration Pack についての詳細情報](../logic-apps/logic-apps-enterprise-integration-overview.md "Enterprise Integration Pack についての詳細情報")   




<!--HONumber=Feb17_HO1-->


