---
title: "Logic Apps B2B の UNH2.5 を使用した EDIFACT デコードの解決 - Azure Logic Apps | Microsoft Docs"
description: "Azure Logic Apps B2B の UNH2.5 を使用した EDIFACT デコードの解決"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 62ad8183cc6e9f56255b2729a04ee7710d00a21a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-handle-edifact-documents-having-unh25-segment"></a>UNH2.5 セグメントを持つ EDIFACT ドキュメントを処理する方法
EDIFACT ドキュメント内に UNH2.5 がある場合は、スキーマの検索に使用します。 

例: UNH フィールドが**EAN008** EDIFACT メッセージに  
UNH + SSDD1 + ORDERS: D: 03B: 解除:**EAN008**'  

メッセージを処理する手順は次のとおりです。 
1. スキーマを更新する
2. アグリーメントの設定を確認する  

## <a name="update-the-schema"></a>スキーマを更新する
メッセージを処理するには、UNH2.5 のルート ノード名を持つスキーマをデプロイする必要があります。  たとえば、スキーマのルート名は **EFACT_D03B_ORDERS_EAN008** のようになります。  

UNH2.5 セグメントが異なる D03B_ORDERS ごとに、個々のスキーマをデプロイする必要があります。  

## <a name="add-schema-to-the-edifact-agreement"></a>EDIFACT アグリーメントにスキーマを追加する
### <a name="edifact-decode"></a>EDIFACT デコード
受信メッセージをデコードするには、EDIFACT アグリーメントの受信設定でスキーマを構成します。
1. スキーマを統合アカウントに追加します。    
2. EDIFACT アグリーメントの受信設定でスキーマを構成します。 
3. EDIFACT アグリーメントを選択して **[JSON として編集]** をクリックします。  UNH2.5 の値を受信契約 **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png) に追加します。

### <a name="edifact-encode"></a>EDIFACT エンコード
受信メッセージをエンコードするには、EDIFACT アグリーメントの送信設定でスキーマを構成します。
1. スキーマを統合アカウントに追加します。    
2. EDIFACT アグリーメントの送信設定でスキーマを構成します。 
3. EDIFACT アグリーメントを選択して **[JSON として編集]** をクリックします。  UNH2.5 の値を送信契約 **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png) に追加します。

## <a name="next-steps"></a>次のステップ
* [統合アカウント契約についての詳細情報](../logic-apps/logic-apps-enterprise-integration-agreements.md "エンタープライズ統合契約についての詳細情報")  