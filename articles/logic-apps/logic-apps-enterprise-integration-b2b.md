---
title: B2B エンタープライズ統合の作成 - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps と Enterprise Integration Pack で B2B データを受信します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 20fc3722-6f8b-402f-b391-b84e9df6fcff
ms.date: 07/08/2016
ms.openlocfilehash: 05368f627c5e9482a43d5e30b0e16b1d47f6217c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58074724"
---
# <a name="receive-b2b-data-with-azure-logic-apps-and-enterprise-integration-pack"></a>Azure Logic Apps と Enterprise Integration Pack で B2B データを受信する

パートナーと契約を含む統合アカウントを作成した後、[Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)を使用してロジック アプリの企業間 (B2B) ワークフローを作成できます。

## <a name="prerequisites"></a>前提条件

AS2 と X12 のアクションを使用するには、エンタープライズ統合アカウントが必要です。 「[統合アカウントの概要](../logic-apps/logic-apps-enterprise-integration-accounts.md)」を参照してください。

## <a name="create-a-logic-app-with-b2b-connectors"></a>B2B コネクタを使用するロジック アプリを作成する

次の手順に従って、AS2 と X12 のアクションを使用して取引先からデータを受信する B2B ロジック アプリを作成します。

1. ロジック アプリを作成した後、[アプリを統合アカウントにリンク](../logic-apps/logic-apps-enterprise-integration-accounts.md)します。

2. ロジック アプリに **[Request - When an HTTP request is received (要求 - HTTP 要求を受信したとき)]** トリガーを追加します。

    ![](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)

3. **AS2 のデコード** アクションを追加するには、**[アクションの追加]** を選択します。

    ![](./media/logic-apps-enterprise-integration-b2b/transform-2.png)

4. すべてのアクションから目的のアクションを抽出するには、検索ボックスに「**as2**」と入力します。

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-5.png)

5. **[AS2 - Decode AS2 message (AS2 - AS2 メッセージのデコード)]** アクションを選択します。

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-6.png)

6. 入力として使用する **[本文]** を追加します。 
   この例では、ロジック アプリをトリガーする HTTP 要求の本文を選択します。 または、ヘッダーを入力する式を **[ヘッダー]** フィールドに入力します。

    @triggerOutputs()['headers']

7. AS2 で必要な **[ヘッダー]** を追加します。これは HTTP 要求ヘッダーで見つけることができます。 
   この例では、ロジック アプリをトリガーする HTTP 要求のヘッダーを選択します。

8. 次に、X12 メッセージのデコード アクションを追加します。 **[アクションの追加]** を選択します。

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-9.png)

9. すべてのアクションから目的のアクションを抽出するには、検索ボックスに「**x12**」と入力します。

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-10.png)

10. **[X12 - Decode X12 message (X12 - X12 メッセージのデコード)]** アクションを選択します。

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-as2message.png)

11. 次に、この操作に対する入力を指定する必要があります。 
    この入力は、前の AS2 アクションからの出力です。

    実際のメッセージの内容は JSON オブジェクトであり、base64 でエンコードされているため、入力として式を指定する必要があります。 
    次の式を **[X12 FLAT FILE MESSAGE TO DECODE (デコードする X12 フラット ファイル メッセージ)]** 入力フィールドに入力します。
    
    @base64ToString(body('Decode_AS2_message')?['AS2Message']?['Content'])

    次に、取引先から受信した X12 データをデコードし、項目を JSON オブジェクトに出力する手順を追加します。 
    データを受信したことをパートナーに知らせるために、HTTP 応答アクションで AS2 Message Disposition Notification (MDN) を含む応答を返すことができます。

12. **[応答]** アクションを追加するには、**[アクションの追加]** を選択します。

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-14.png)

13. すべてのアクションから目的のアクションを抽出するには、検索ボックスに「**応答**」と入力します。

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-15.png)

14. **[応答]** アクションを選択します。

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-16.png)

15. **[Decode X12 message (X12 メッセージのデコード)]** アクションの出力から MDN にアクセスするには、次の式を使用して、応答の **[本文]** フィールドを設定します。

    @base64ToString(body('Decode_AS2_message')?['OutgoingMdn']?['Content'])

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-17.png)  

16. 作業内容を保存します。

    ![](./media/logic-apps-enterprise-integration-b2b/transform-5.png)  

これで、B2B ロジック アプリの設定が完了しましました。 実際のアプリケーションでは、デコードした X12 データを基幹業務 (LOB) アプリやデータストアに保存できます。 独自の LOB アプリに接続してロジック アプリでこれらの API を使用するために、さらにアクションを追加したり、カスタム API を記述したりできます。

## <a name="features-and-use-cases"></a>機能とユース ケース

* AS2 と X12 のデコード アクションとエンコード アクションにより、ロジック アプリで業界標準のプロトコルを使用して取引先とデータ交換できます。
* 取引先とのデータ交換は、互いに AS2 と X12 を使用して行うことも、使用しないで行うこともできます
* B2B アクションにより、統合アカウントにパートナーと契約を簡単に作成して、ロジック アプリでこれらを使用できます。
* 他のアクションでロジック アプリを拡張することにより、SalesForce などの他のアプリや サービス間でデータを送受信できます。

## <a name="learn-more"></a>詳細情報
[Enterprise Integration Pack についての詳細情報](logic-apps-enterprise-integration-overview.md)
