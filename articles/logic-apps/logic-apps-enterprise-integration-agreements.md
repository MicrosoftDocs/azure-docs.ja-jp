---
title: 取引先契約を作成して管理する - Azure Logic Apps
description: Azure Logic Apps および Enterprise Integration Pack を使用して取引先間の契約を作成して管理します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 4bfee4ec442c9e7b0351b0fd0c6a2b8e163a2541
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330313"
---
# <a name="create-and-manage-trading-partner-agreements-in-azure-logic-apps"></a>Azure Logic Apps で取引先契約を作成して管理する

[取引先](../logic-apps/logic-apps-enterprise-integration-partners.md) 
*契約*は、組織および企業が、企業間 (B2B) メッセージを交換するときに使用する特定の業界標準プロトコルを定義して互いにシームレスに通信するのに役立ちます。 契約には一般的に次のような利点があります。

* 組織はよく知られている形式を使用して情報を交換することができます。
* B2B トランザクションを実行する際に、効率性が向上します。
* エンタープライズ統合ソリューションを構築する際に、容易に作成、管理、および使用することができます。

この記事では、[Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) および [Azure Logic Apps](../logic-apps/logic-apps-overview.md) を使用して B2B シナリオ用のエンタープライズ統合ソリューションを構築するときに使用できる AS2、EDIFACT、または X12 の契約を作成する方法について説明します。 契約を作成したら、AS2、EDIFACT、または X12 コネクタを使用して B2B メッセージを交換できるようになります。

RosettaNet メッセージを交換するための契約を作成するには、[RosettaNet メッセージの交換](../logic-apps/logic-apps-enterprise-integration-rosettanet.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* ご利用の契約およびその他の B2B 成果物を格納する[統合アカウント](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。 この統合アカウントは、ご利用の Azure サブスクリプションに関連付けられている必要があります。

* ご利用の統合アカウント内で既に作成されている少なくとも 2 つ[取引先](../logic-apps/logic-apps-enterprise-integration-partners.md)。 契約には、ホスト パートナーとゲスト パートナーの両方が必要です。 作成する契約と同じ "ビジネス ID" 修飾子 (AS2、X12、EDIFACT など) が両方のパートナーで使用される必要があります。

* 省略可能:契約を使用するロジック アプリと、ロジック アプリのワークフローを開始するトリガー。 統合アカウントと B2B 成果物を作成するだけの場合、ロジック アプリは必要ありません。 ただし、ロジック アプリで統合アカウントの B2B 成果物を使用するには、事前に統合アカウントをロジック アプリにリンクしておく必要があります。 ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」と[クイック スタートの初めてのロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。

## <a name="create-agreements"></a>契約を作成する

1. [Azure Portal](https://portal.azure.com) にサインインします。
Azure のメイン メニューで、 **[すべてのサービス]** を選びます。 検索ボックスに、フィルターとして「統合」と入力します。 結果から、次のリソースを選択します。**統合アカウント**

   ![統合アカウントの検索](./media/logic-apps-enterprise-integration-agreements/find-integration-accounts.png)

1. **[統合アカウント]** で、契約を作成する統合アカウントを選びます。

   ![契約を作成する統合アカウントの選択](./media/logic-apps-enterprise-integration-agreements/select-integration-account.png)

1. 右側のウィンドウの **[コンポーネント]** で、 **[契約]** タイルを選択します。

   ![[契約] を選びます。](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

1. **[契約]** で **[追加]** を選びます。 **[追加]** ウィンドウで、たとえば、次のように契約に関する情報を指定します。

   ![[追加] の選択](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **Name** | はい | <*agreement-name*> | 契約の名前 |
   | **契約の種類** | はい | **AS2**、**X12**、または **EDIFACT** | ご利用の契約でのプロトコルの種類。 契約ファイルを作成する場合は、そのファイルの内容が契約の種類と一致する必要があります。 | |  
   | **ホスト パートナー** | はい | <*host-partner-name*> | ホスト パートナーは、契約を指定する組織を表します。 |
   | **ホスト ID** | はい | <*host-partner-identifier*> | ホスト パートナーの ID |
   | **ゲスト パートナー** | はい | <*guest-partner-name*> | ゲスト パートナーは、ホスト パートナーと取引している組織を表します。 |
   | **ゲスト ID** | はい | <*guest-partner-identifier*> | ゲスト パートナーの ID |
   | **受信設定** | 多様 | 多様 | これらのプロパティは、ホスト パートナーが契約内のゲスト パートナーからすべての受信メッセージを受信する方法を指定します。 詳細については、それぞれの契約の種類を参照してください。 <p>- [AS2 メッセージの設定](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT メッセージの設定](logic-apps-enterprise-integration-edifact.md) <br>- [X12 メッセージの設定](logic-apps-enterprise-integration-x12.md) |
   | **送信の設定** | 多様 | 多様 | これらのプロパティは、ホスト パートナーが契約内のゲスト パートナーにすべての送信メッセージを送信する方法を指定します。 詳細については、それぞれの契約の種類を参照してください。 <p>- [AS2 メッセージの設定](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT メッセージの設定](logic-apps-enterprise-integration-edifact.md) <br>- [X12 メッセージの設定](logic-apps-enterprise-integration-x12.md) |
   |||||

1. 契約の作成が終了したら、 **[追加]** ページで、 **[OK]** を選択してご自分の統合アカウントに戻ります。

   これで **[契約]** リストにご自分の新しい契約が表示されます。

## <a name="edit-agreements"></a>契約を編集する

1. [Azure portal](https://portal.azure.com) の Azure メイン メニューで、 **[すべてのサービス]** を選びます。

1. 検索ボックスに、フィルターとして「統合」と入力します。 結果から、次のリソースを選択します。**統合アカウント**

1. **[統合アカウント]** で、編集する契約が含まれる統合アカウントを選びます。

1. 右側のウィンドウの **[コンポーネント]** で、 **[契約]** タイルを選択します。

1. **[契約]** で、ご自分の契約を選択し、 **[編集]** を選択します。

1. 変更を加えて、それを保存します。

## <a name="delete-agreements"></a>契約を削除する

1. [Azure portal](https://portal.azure.com) の Azure メイン メニューで、 **[すべてのサービス]** を選びます。

1. 検索ボックスに、フィルターとして「統合」と入力します。 結果から、次のリソースを選択します。**統合アカウント**

1. **[統合アカウント]** で、削除する契約が含まれる統合アカウントを選びます。

1. 右側のウィンドウの **[コンポーネント]** で、 **[契約]** タイルを選択します。

1. **[契約]** で、ご自分の契約を選択し、 **[削除]** を選択します。

1. 選択した契約を削除することを確認します。

## <a name="next-steps"></a>次の手順

* [AS2 メッセージの交換](logic-apps-enterprise-integration-as2.md)
* [EDIFACT メッセージの交換](logic-apps-enterprise-integration-edifact.md)
* [X12 メッセージの交換](logic-apps-enterprise-integration-x12.md)
