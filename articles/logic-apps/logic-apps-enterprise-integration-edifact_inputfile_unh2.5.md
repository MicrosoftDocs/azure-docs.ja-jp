---
title: UNH 2.5 セグメントで EDIFACT メッセージを処理する - Azure Logic Apps | Microsoft Docs
description: Enterprise Integration Pack を備えた Azure Logic Apps で UNH2.5 セグメントによる EDIFACT ドキュメントの解決を行います
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.date: 04/27/2017
ms.openlocfilehash: 926c9ebe8675d8b50d4544be813ae0b15492ae35
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60681674"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>Azure Logic Apps で UNH 2.5 セグメントを使用して EDIFACT メッセージを処理する

EDIFACT ドキュメント内に UNH2.5 がある場合は、スキーマの検索に使用します。 

例:EDIFACT メッセージの UNH フィールドは **EAN008** です  
UNH+SSDD1+ORDERS:D:03B:UN:**EAN008**'  

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

## <a name="next-steps"></a>次の手順
* [統合アカウント契約についての詳細情報](../logic-apps/logic-apps-enterprise-integration-agreements.md "エンタープライズ統合契約についての詳細情報")  