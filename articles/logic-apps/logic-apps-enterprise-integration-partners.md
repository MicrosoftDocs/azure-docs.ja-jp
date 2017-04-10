---
title: "企業間 (B2B) メッセージのパートナーを作成する - Azure Logic Apps | Microsoft Docs"
description: "Enterprise Integration Pack と Logic Apps を使用してパートナーを統合アカウントに追加する方法について説明します"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: b179325c-a511-4c1b-9796-f7484b4f6873
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: estfan
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 5913c81088724ef946ae147f4f3154fa6aefd22e
ms.openlocfilehash: 2a886d1eaf1253b55d8c95786139a2bbdc6f145e
ms.lasthandoff: 03/01/2017


---
# <a name="add-or-update-partners-in-business-to-business-agreements-in-your-workflow"></a>ワークフロー内の企業間契約のパートナーを追加または更新する

パートナーとは、企業間 (B2B) のトランザクションとメッセージング交換に参加するエンティティです。 これらのトランザクションで自身の組織と別の組織を表すパートナーを作成するには、まず交換するメッセージを識別して検証するための情報を共有する必要があります。 これらの詳細について話し合い、取引関係を開始する準備が整ったら、統合アカウントに両者を表すパートナーを作成できます。

## <a name="what-roles-do-partners-have-in-your-integration-account"></a>統合アカウントのパートナーの役割

パートナー間で交換するメッセージの詳細を定義するには、それらのパートナー間の契約を作成します。 ただし、契約を作成する前に、少なくとも&2; つのパートナーを統合アカウントに追加しておく必要があります。 自身の組織は、契約の**ホスト パートナー**にする必要があります。 もう一方の組織、すなわち**ゲスト パートナー**は、自身の組織とメッセージを交換する組織です。 ゲスト パートナーには、別の会社のほか、自身の組織内の部署も指定できます。

これらのパートナーを追加したら、契約を作成できます。

受信と送信の設定は、ホスト パートナー側から見た方向になります。 たとえば、契約の受信設定は、ホスト パートナーがゲスト パートナーからメッセージを受信する方法を決めるものです。 同様に、契約の送信設定は、ホスト パートナーがゲスト パートナーにメッセージを送信する方法を示すものです。

## <a name="how-to-create-a-partner"></a>パートナーを作成する方法

1. Azure Portal で **[参照]** を選択します。

    ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)

2. フィルター検索ボックスに「**統合**」と入力し、結果リストから **[統合アカウント]** を選択します。

    ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)

3. パートナーの追加先となる統合アカウントを選択します。

    ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. **[パートナー]** タイルを選択します。

    ![](./media/logic-apps-enterprise-integration-partners/partner-1.png)

5. [パートナー] ブレードで **[追加]** を選択します。

    ![](./media/logic-apps-enterprise-integration-partners/partner-2.png)

6. パートナーの名前を入力してから、**[Qualifier (修飾子)]** を選択します。 最後に**値**を入力します。この値は、アプリに届くドキュメントを識別するために使用されます。

    ![](./media/logic-apps-enterprise-integration-partners/partner-3.png)

7. パートナー作成プロセスの進捗状況を確認するには、"*ベル*" の通知アイコンを選択します。

    ![](./media/logic-apps-enterprise-integration-partners/partner-4.png)

8. 新しいパートナーが正常に追加されたことを確認するには、**[パートナー]** タイルを選択します。

    ![](./media/logic-apps-enterprise-integration-partners/partner-5.png)

    [パートナー] タイルを選択すると、[パートナー] ブレードにも、新しく追加されたパートナーが表示されます。

    ![](./media/logic-apps-enterprise-integration-partners/partner-6.png)

## <a name="how-to-edit-existing-partners-in-your-integration-account"></a>統合アカウント内の既存のパートナーを編集する方法

1. **[パートナー]** タイルを選択します。
2. [パートナー] ブレードが開いたら、編集するパートナーを選択します。
3. **[パートナーの更新]** タイルで、必要な変更を行います。
4. 変更が完了したら、**[保存]** を選択します。変更をキャンセルするには、**[破棄]** を選択します。

    ![](./media/logic-apps-enterprise-integration-partners/edit-1.png)

## <a name="how-to-delete-a-partner"></a>パートナーを削除する方法

1. **[パートナー]** タイルを選択します。
2. [パートナー] ブレードが開いたら、削除するパートナーを選択します。
3. **[削除]** を選択します。

    ![](./media/logic-apps-enterprise-integration-partners/delete-1.png)

## <a name="next-steps"></a>次のステップ
* [契約についての詳細情報](../logic-apps/logic-apps-enterprise-integration-agreements.md "Enterprise Integration の契約についての詳細情報")  


