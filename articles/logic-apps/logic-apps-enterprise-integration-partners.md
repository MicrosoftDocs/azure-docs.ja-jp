---
title: B2B 統合の取引先を追加する - Azure Logic Apps
description: Azure Logic Apps で使用する統合アカウントに取引先を作成します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 681f16132c1de2ec5f3b27f80633d32879b0746c
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330264"
---
# <a name="add-trading-partners-to-integration-accounts-for-azure-logic-apps"></a>Azure Logic Apps の統合アカウントに取引先を追加する

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) では、対象のロジック アプリで[統合アカウント](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)を使用して、自動化された企業間 (B2B) 統合ワークフローを作成できます。 自身の組織と別の組織を表すには、取引先を作成して統合アカウントの成果物として追加します。 パートナーとは、企業間 (B2B) のトランザクションに参加し、互いにメッセージを交換するエンティティです。

これらのパートナーを作成する前に、相手が送信するメッセージを識別して検証する方法について、必ずパートナーと話し合い、情報を共有してください。 これらの詳細に同意できたら、統合アカウントにパートナーを作成する準備が整います。

## <a name="partner-roles-in-integration-accounts"></a>統合アカウントのパートナーの役割

パートナーと交換するメッセージの詳細を定義するには、[契約](../logic-apps/logic-apps-enterprise-integration-agreements.md)を作成して統合アカウントに成果物として追加します。 契約には、統合アカウントに 2 つ以上のパートナーが必要です。 自身の組織は、常に契約の "*ホスト パートナー*" になります。 自身の組織組織とメッセージを交換する組織は、"*ゲスト パートナー*" です。 ゲスト パートナーには、別の会社のほか、自身の組織内の部署も指定できます。 これらのパートナーを追加したら、契約を作成できます。

契約には、受信メッセージと送信メッセージを処理するための詳細を、ホスト パートナーの観点から指定します。 受信メッセージについては、 **[受信設定]** を使用して、ホスト パートナーが契約内のゲスト パートナーからメッセージを受信する方法を指定します。 送信メッセージについては、 **[送信設定]** を使用して、ホスト パートナーがゲスト パートナーにメッセージを送信する方法を指定します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* 対象のパートナー、ご利用の契約、およびその他の B2B 成果物を格納するための[統合アカウント](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。 この統合アカウントは、ご利用の Azure サブスクリプションに関連付けられている必要があります。

## <a name="create-partner"></a>パートナーの作成

1. [Azure Portal](https://portal.azure.com) にサインインします。

1. Azure のメイン メニューで、 **[すべてのサービス]** を選びます。 検索ボックスに「統合」と入力し、 **[統合アカウント]** を選びます。

   ![[統合アカウント] を選択する](./media/logic-apps-enterprise-integration-partners/find-integration-accounts.png)

1. **[統合アカウント]** で、パートナーを追加する統合アカウントを選びます。

   ![統合アカウントを選択する](./media/logic-apps-enterprise-integration-partners/select-integration-account.png)

1. **[パートナー]** タイルを選びます。

   ![[パートナー] タイルを選ぶ](./media/logic-apps-enterprise-integration-partners/choose-partners.png)

1. **[パートナー]** で **[追加]** を選びます。 **[パートナーの追加]** で、次の表の説明に従ってパートナーの詳細を入力します。

   ![[追加] を選択し、パートナーの詳細を入力する](./media/logic-apps-enterprise-integration-partners/add-partners.png)

   | プロパティ | 必須 | 説明 |
   |----------|----------|-------------|
   | **Name** | はい | パートナーの名前 |
   | **修飾子** | はい | 組織に固有のビジネス ID を提供する認証機関。たとえば、**D-U-N-S (Dun & Bradstreet)** 。 <p>パートナーは、相互に定義されたビジネス ID を選ぶことができます。 これらのシナリオでは、EDIFACT に対して **[相互定義]** を選び、X12 に対して **[相互定義 (X12)]** を選びます。 <p>RosettaNet の場合は、標準の **[DUNS]** のみを選びます。 |
   | **値** | はい | 自分のロジック アプリが受け取るドキュメントを識別する値。 <p>RosettaNet の場合、この値は DUNS 番号に対応する 9 桁の数字である必要があります。 |
   ||||

   > [!NOTE]
   > RosettaNet を使用するパートナーの場合は、最初にこれらのパートナーを作成し、[それを後で編集する](#edit-partner)ことによって、追加情報を指定することができます。

1. 完了したら、 **[OK]** を選びます。

   新しいパートナーが **[パートナー]** の一覧に表示されます。 さらに、 **[パートナー]** タイル上の現在のパートナー数が更新されます。

   ![新しいパートナー](./media/logic-apps-enterprise-integration-partners/new-partner.png)

<a name="edit-partner"></a>

## <a name="edit-partner"></a>パートナーを編集する

1. [Azure ポータル](https://portal.azure.com)で、統合アカウントを検索して選択します。
**[パートナー]** タイルを選びます。

   ![[パートナー] タイルを選ぶ](./media/logic-apps-enterprise-integration-partners/edit.png)

1. **[パートナー]** で、編集するパートナーを選び、 **[編集]** を選びます。 **[編集]** で、変更を加えます。

   ![変更して保存する](./media/logic-apps-enterprise-integration-partners/edit-partner.png)

   RosettaNet の場合、 **[RosettaNet パートナーのプロパティ]** で、次の追加情報を指定できます。

   | プロパティ | 必須 | 説明 |
   |----------|----------|-------------|
   | **パートナーの分類** | いいえ | パートナーの組織の種類 |
   | **サプライ チェーン コード** | いいえ | パートナーのサプライ チェーン コード。たとえば、"情報技術"、"電子部品" |
   | **Contact Name (連絡先の名前)** | いいえ | パートナーの連絡先の名前 |
   | **電子メール** | いいえ | パートナーのメール アドレス |
   | **Fax** | いいえ | パートナーの fax 番号 |
   | **電話** | いいえ | パートナーの電話番号 |
   ||||

1. 完了したら、 **[OK]** を選択して変更を保存します。

## <a name="delete-partner"></a>パートナーを削除する

1. [Azure ポータル](https://portal.azure.com)で、統合アカウントを検索して選択します。 **[パートナー]** タイルを選びます。

   ![[パートナー] タイルを選ぶ](./media/logic-apps-enterprise-integration-partners/choose-partners-to-delete.png)

1. **[パートナー]** で、削除するパートナーを選びます。 **[削除]** を選択します。

   ![パートナーを削除する](./media/logic-apps-enterprise-integration-partners/delete-partner.png)

## <a name="next-steps"></a>次の手順

* [契約](../logic-apps/logic-apps-enterprise-integration-agreements.md)の詳細について学習します