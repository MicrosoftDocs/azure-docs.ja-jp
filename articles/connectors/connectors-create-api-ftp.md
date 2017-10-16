---
title: "ロジック アプリで FTP コネクタを使用する方法 | Microsoft Docs"
description: "Azure App Service を使用してロジック アプリを作成します。 FTP サーバーに接続して、ファイルを管理します。 FTP サーバーのファイルのアップロード、更新、取得、削除など、多様なアクションを実行できます。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: d83c55fe-eb59-4b7b-a5ec-afac5c772616
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/22/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 61bfbedfd4f1e84b6976099323a32f3a720634c0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-ftp-connector"></a>FTP コネクタの使用
FTP コネクタを使用して、FTP サーバー上のファイルを監視、管理、作成します。 

[任意のコネクタ](apis-list.md)を使用するには、まずロジック アプリを作成する必要があります。 [ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)から始めることができます。

## <a name="connect-to-ftp"></a>FTP に接続する
ロジック アプリから任意のサービスにアクセスできるようにするには、まず、そのサービスへの*接続*を作成する必要があります。 [接続](connectors-overview.md)により、ロジック アプリと別のサービスとの接続が実現します。  

### <a name="create-a-connection-to-ftp"></a>FTP への接続を作成する
> [!INCLUDE [Steps to create a connection to FTP](../../includes/connectors-create-api-ftp.md)]
> 
> 

## <a name="use-a-ftp-trigger"></a>FTP トリガーを使用する
トリガーとは、ロジック アプリで定義されたワークフローの開始に使用できるイベントです。 トリガーの詳細については[こちら](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)を参照してください。  

> [!IMPORTANT]
> FTP コネクタを使用するには、インターネットからアクセスできる、パッシブ モードで動作するように構成されている FTP サーバーが必要です。 また、FTP コネクタは、**暗黙的 FTPS (FTP over SSL)** に対応していません。 FTP コネクタは、明示的 FTPS (FTP over SSL) のみをサポートしています。  
> 
> 

この例では、FTP サーバー上でファイルが追加または変更されたときに、**[FTP - When a file is added or modified (FTP - ファイルが追加または変更されたとき)]** トリガーによりロジック アプリ ワークフローを開始する方法について説明します。 エンタープライズの例では、このトリガーを使用して、FTP フォルダーに顧客からの注文を表す新しいファイルがあるかどうかを監視できます。  次に、**[Get file content (ファイルの内容を取得する)]** などの FTP コネクタ アクションを使用して注文の内容を取得し、後続の処理や注文データベースへの格納を行うことができます。

1. ロジック アプリ デザイナーの検索ボックスに「*ftp*」と入力し、**[FTP - When a file is added or modified (FTP - ファイルが追加または変更されたとき)]** トリガーを選択します。   
   ![FTP トリガーの画像 1](./media/connectors-create-api-ftp/ftp-trigger-1.png)  
   **[When a file is added or modified (ファイルが追加または変更されたとき)]** コントロールを開きます。  
   ![FTP トリガーの画像 2](./media/connectors-create-api-ftp/ftp-trigger-2.png)  
2. コントロールの右側にある **[...]** を選択します。 これによりフォルダー ピッカー コントロールが開きます。  
   ![FTP トリガーの画像 3](./media/connectors-create-api-ftp/ftp-trigger-3.png)  
3. **[>]** (右矢印) を選択し、新しいファイルや変更されたファイルを監視するフォルダーを参照します。 そのフォルダーを選択します。フォルダーが **[フォルダー]** コントロールに表示されます。  
   ![FTP トリガーの画像 4](./media/connectors-create-api-ftp/ftp-trigger-4.png)   

これで、特定の FTP フォルダーでファイルが変更または作成されたときにワークフローの他のトリガーおよびアクションの実行を開始するトリガーがロジック アプリに構成されました。 

> [!NOTE]
> ロジック アプリを機能させるためには、少なくとも 1 つのトリガーとアクションを含まなければなりません。 アクションを追加するには、次のセクションの手順に従います。  
> 
> 

## <a name="use-a-ftp-action"></a>FTP アクションを使用する
アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。 アクションの詳細については[こちら](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)を参照してください。  

トリガーを追加した後は、次の手順に従って、トリガーによって検出された新しいファイルまたは変更されたファイルの内容を取得するアクションを追加します。    

1. **[+ 新しいステップ]** を選択して、FTP サーバー上のファイルの内容を取得するアクションを追加します。  
2. **[アクションの追加]** のリンクを選択します。  
   ![FTP アクションの画像 1](./media/connectors-create-api-ftp/ftp-action-1.png)  
3. 「*FTP*」と入力して、FTP に関連するすべてのアクションを検索します。
4. FTP フォルダーで新しいファイルまたは変更されたファイルが検出されたときに実行するアクションとして **[FTP - Get file content (FTP - ファイルの内容を取得する)]** を選択します。      
   ![FTP アクションの画像 2](./media/connectors-create-api-ftp/ftp-action-2.png)  
   **[Get file content (ファイルの内容を取得する)]** コントロールが開きます。 **注**: ロジック アプリによる FTP サーバー アカウントへのアクセスをまだ承認していない場合は、承認を求められます。  
   ![FTP アクションの画像 3](./media/connectors-create-api-ftp/ftp-action-3.png)   
5. **[ファイル]** コントロール (**[ファイル]*** の下にある空白) を選択します。 ここでは、FTP サーバーで検出された新しいファイルまたは変更されたファイルの各種プロパティを使用することができます。  
6. **[File content (ファイルの内容)]** オプションを選択します。  
   ![FTP アクションの画像 4](./media/connectors-create-api-ftp/ftp-action-4.png)   
7. コントロールが更新され、FTP サーバー上の新しいファイルまたは変更されたファイルについて、**[FTP - Get file content (FTP - ファイルの内容を取得する)]** アクションによって "*ファイルの内容*" が取得されることが示されます。      
   ![FTP アクションの画像 5](./media/connectors-create-api-ftp/ftp-action-5.png)     
8. 作業内容を保存し、ファイルを FTP フォルダーに追加して、ワークフローをテストします。    

これで、FTP サーバー上のフォルダーを監視し、FTP フォルダーで新しいファイルまたは変更されたファイルを検出したときにワークフローを開始するトリガーがロジック アプリに構成されました。 

このロジック アプリには、新しいファイルまたは変更されたファイルの内容を取得するアクションも構成されています。

この段階で、別のアクション (たとえば、新しいファイルまたは変更されたファイルの内容を SQL データベース テーブルに挿入する [[SQL Server - insert row (SQL Server - 行を挿入する)]](connectors-create-api-sqlazure.md) アクション) を追加することができます。  

## <a name="connector-specific-details"></a>コネクタ固有の詳細

[コネクタの詳細](/connectors/ftpconnector/)に関するページに、Swagger で定義されているトリガーとアクション、さらに制限が記載されています。 

## <a name="next-steps"></a>次のステップ
[ロジック アプリを作成します](../logic-apps/logic-apps-create-a-logic-app.md)

