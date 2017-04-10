---
title: "Azure Logic Apps でフラット ファイルをエンコードまたはデコードする | Microsoft Docs"
description: "ロジック アプリで Enterprise Integration Pack のファイル エンコーダーまたはデコーダーを使用する方法"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 82152dab-c7ad-43df-b721-596559703be8
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 2f407a428aa176cc5c2a3b6bb236b522bda5ab64
ms.openlocfilehash: b9b0f1970a5c7edeb9986c0d60c8bc249c50f2ac


---
# <a name="overview-of-enterprise-integration-with-flat-files"></a>フラット ファイルでのエンタープライズ統合の概要

企業間 (B2B) シナリオでのビジネス パートナーに送信する前に、XML の内容をエンコードする必要がある場合があります。 ロジック アプリでは、フラット ファイル エンコーディング コネクタを使用してこれを実行できます。 作成するロジック アプリは、HTTP 要求トリガー、別のアプリケーション、さらには多くの [コネクタ](../connectors/apis-list.md)の&1; つなど、さまざまなソースから XML の内容を取得できます。 Logic Apps の詳細については、[Logic Apps のドキュメント](logic-apps-what-are-logic-apps.md "Logic Apps についての詳細情報")を参照してください。  

## <a name="create-the-flat-file-encoding-connector"></a>コネクタをエンコードするフラット ファイルの作成
フラット ファイル エンコーディング コネクタをロジック アプリに追加するには、次の手順に従います。

1. ロジック アプリを作成し、[統合アカウントにリンク](logic-apps-enterprise-integration-accounts.md "ロジック アプリへの統合アカウントのリンクについての詳細情報")します。 このアカウントには、XML データをエンコードする際に使用するスキーマが存在します。  
2. ロジック アプリに **[Request - When an HTTP request is received (要求 - HTTP 要求を受信したとき)]** トリガーを追加します。  
   ![Screenshot of trigger to select](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)    
3. 次の手順でフラット ファイルのエンコード アクションを追加します。
   
    a. **プラス記号**を選択します。
   
    b. プラス記号を選択すると表示される **[アクションの追加]** リンクを選択します。
   
    c. 検索ボックスに「*Flat*」と入力し、すべてのアクションから使用するアクションだけを抽出します。
   
    d. リストから **[フラット ファイルのエンコード]** オプションを選択します。   
   ![Screenshot of Flat File Encoding option](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   
4. **[フラット ファイルのエンコード]** ダイアログ ボックスの **[コンテンツ]** ボックスを選択します。  
   ![Screenshot of Content text box](media/logic-apps-enterprise-integration-flatfile/flatfile-3.png)  
5. エンコードする内容として body タグを選択します。 [コンテンツ] フィールドに body タグが入力されます。     
   ![Screenshot of body tag](media/logic-apps-enterprise-integration-flatfile/flatfile-4.png)  
6. **[スキーマ名]** ボックスを選択し、コンテンツの入力内容をエンコードするために使用するスキーマを選択します。    
   ![Screenshot of Schema Name list box](media/logic-apps-enterprise-integration-flatfile/flatfile-5.png)  
7. 作業内容を保存します。   
   ![Screenshot of Save icon](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)  

この時点で、コネクタをエンコードするフラット ファイルの設定が終了します。 実際のアプリケーションでは、エンコードされたデータを Salesforce などの業務アプリケーションに保存することができます。 または、そのエンコード済みのデータを取引先に送信することもできます。 提供されるその他のコネクタのいずれかを使用して、Salesforce または取引先にエンコードするアクションの出力を送信するアクションを簡単に追加することができます。

ここでコネクタをテストするには、HTTP エンドポイントへの要求を作成し、要求の本文に XML の内容を含めます。  

## <a name="create-the-flat-file-decoding-connector"></a>コネクタをデコードするフラット ファイルの作成

> [!NOTE]
> 次の手順を完了するには、スキーマ ファイルが統合アカウントに既にアップロードされている必要があります。

1. ロジック アプリに **[Request - When an HTTP request is received (要求 - HTTP 要求を受信したとき)]** トリガーを追加します。  
   ![Screenshot of trigger to select](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)    
2. 次の手順でフラット ファイルのデコード アクションを追加します。
   
    a. **プラス記号**を選択します。
   
    b. プラス記号を選択すると表示される **[アクションの追加]** リンクを選択します。
   
    c. 検索ボックスに「*Flat*」と入力し、すべてのアクションから使用するアクションだけを抽出します。
   
    d. リストから **[フラット ファイルのデコード]** オプションを選択します。   
   ![Screenshot of Flat File Decoding option](media/logic-apps-enterprise-integration-flatfile/flatfile-2.png)   
3. **[コンテンツ]** コントロールを選択します。 これにより、デコードする内容として使用できる、前の手順で作成した内容の一覧が作成されます。 受信 HTTP 要求からの " *本文* " がデコードする内容として使用できるようになったことがわかります。 デコードする内容を **[コンテンツ]** コントロールに直接入力することもできます。     
4. *Body* タグを選択します。 現在、body タグは **[コンテンツ]** コントロールにあることに注意してください。
5. 内容のデコードに使用するスキーマの名前を選択します。 次のスクリーンショットでは、スキーマ名として *OrderFile* が選択されています。 このスキーマ名は、既に統合アカウントにアップロードされています。
   
   ![Screenshot of Flat File Decoding dialog box](media/logic-apps-enterprise-integration-flatfile/flatfile-decode-1.png)    
6. 作業内容を保存します。  
   ![Screenshot of Save icon](media/logic-apps-enterprise-integration-flatfile/flatfile-6.png)    

この時点で、コネクタをデコードするフラット ファイルの設定が終了します。 実際のアプリケーションでは、デコードされたデータを Salesforce などの業務アプリケーションに保存することができます。 Salesforce にデコードするアクションの出力を送信するアクションを簡単に追加することができます。

ここでコネクタをテストするには、HTTP エンドポイントへの要求を作成し、要求の本文にデコードする XML の内容を含めます。  

## <a name="next-steps"></a>次のステップ
* [Enterprise Integration Pack についての詳細情報](logic-apps-enterprise-integration-overview.md "Enterprise Integration Pack についての詳細情報")  




<!--HONumber=Jan17_HO5-->


