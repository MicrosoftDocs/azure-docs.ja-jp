---
title: Azure Stream Analytics ジョブでログイン資格情報を交換する
description: この記事では、Azure Stream Analytics ジョブの入力および出力シンクの資格情報を更新する方法について説明します。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 14e24c1e9a61eb7ea73a949e17ffbf8c5b768f05
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53099071"
---
# <a name="rotate-login-credentials-for-inputs-and-outputs-of-a-stream-analytics-job"></a>Stream Analytics ジョブの入力と出力のログイン資格情報の交換

Stream Analytics ジョブの入力または出力のための資格情報を再生成したときは常に、新しい資格情報でジョブを更新する必要があります。 資格情報を更新する前に、ジョブを停止する必要があります。ジョブの実行中に資格情報を置き換えることはできません。 ジョブを停止してから再び開始するまでの時間を短縮するため、Stream Analytics は最後の出力からのジョブの再開をサポートしています。 このトピックでは、ログイン資格情報を交換し、新しい資格情報でジョブを再開するプロセスについて説明します。

## <a name="regenerate-new-credentials-and-update-your-job-with-the-new-credentials"></a>新しい資格情報を再生成し、新しい資格情報でジョブを更新する 

このセクションでは、Blob Storage、Event Hubs、SQL Database、Table Storage の資格情報を再生成する手順を説明します。 

### <a name="blob-storagetable-storage"></a>BLOB ストレージとテーブル ストレージ
1. Azure Portal にサインインし、Stream Analytics ジョブの入力/出力として使ったストレージ アカウントを参照します。    
2. 設定セクションで **[アクセス キー]** を開きます。 2 つの既定のキー (key1、key2) のうち、ジョブによって使われていない方を選んで再生成します。  
   ![ストレージ アカウントのキーを再生成する](media/stream-analytics-login-credentials-inputs-outputs/regenerate-storage-keys.png)
3. 新しく生成されたキーをコピーします。    
4. Azure Portal で Stream Analytics ジョブを参照し、**[停止]** を選んで、ジョブが停止するまで待ちます。    
5. 資格情報を更新する Blob/Table Storage の入力/出力を見つけます。    
6. **[ストレージ アカウント キー]** フィールドに新しく生成されたキーを貼り付けて、**[保存]** をクリックします。    
7. 変更内容を保存すると、接続テストが自動的に開始され、[通知] タブでそれを確認できます。2 つの通知が表示されます。1 つは更新の保存に対応し、もう 1 つは接続のテストに対応します。  
   ![キーを編集した後の通知](media/stream-analytics-login-credentials-inputs-outputs/edited-key-notifications.png)
8. 「[最終停止時刻からジョブを開始する](#start-your-job-from-the-last-stopped-time)」セクションに進みます。

### <a name="event-hubs"></a>Event Hubs

1. Azure Portal にサインインし、Stream Analytics ジョブの入力/出力として使ったイベント ハブを参照します。    
2. [設定] セクションで **[共有アクセス ポリシー]** を開き、必要なアクセス ポリシーを選びます。 **[プライマリ キー]** と **[セカンダリ キー]** のうち、ジョブによって使われていない方を選んで再生成します。  
   ![イベント ハブのキーを再生成する](media/stream-analytics-login-credentials-inputs-outputs/regenerate-event-hub-keys.png)
3. 新しく生成されたキーをコピーします。    
4. Azure Portal で Stream Analytics ジョブを参照し、**[停止]** を選んで、ジョブが停止するまで待ちます。    
5. 資格情報を更新するイベント ハブの入力/出力を見つけます。    
6. **[イベント ハブ ポリシー キー]** フィールドに新しく生成されたキーを貼り付けて、**[保存]** をクリックします。    
7. 変更内容を保存すると、接続テストが自動的に開始され、テストに成功したことを確認します。    
8. 「[最終停止時刻からジョブを開始する](#start-your-job-from-the-last-stopped-time)」セクションに進みます。

### <a name="sql-database"></a>SQL Database

既存ユーザーのログイン資格情報を更新するには、SQL データベースに接続する必要があります。 Azure Portal または SQL Server Management Studio などのクライアント側ツールを使って、資格情報を更新することができます。 このセクションでは、Azure Portal を使って資格情報を更新する手順を示します。

1. Azure Portal にサインインし、Stream Analytics ジョブの出力として使った SQL データベースを参照します。    
2. **データ エクスプローラー**でデータベースにログイン/接続し、[承認の種類] として **[SQL Server 認証]** を選び、**[ログイン]** と **[パスワード]** の詳細を入力して、**[OK]** を選びます。  
   ![SQL データベースの資格情報を再生成する](media/stream-analytics-login-credentials-inputs-outputs/regenerate-sql-credentials.png)

3. クエリ タブで、次のクエリを実行して、いずれかのユーザーのパスワードを変更します (`<user_name>` はお使いのユーザー名に、`<new_password>` は新しいパスワードに置き換えてください)。  

   ```SQL
   Alter user `<user_name>` WITH PASSWORD = '<new_password>'
   Alter role db_owner Add member `<user_name>`
   ```

4. 新しいパスワードを書き留めておきます。    
5. Azure Portal で Stream Analytics ジョブを参照し、**[停止]** を選んで、ジョブが停止するまで待ちます。    
6. 資格情報を交換する SQL データベースの出力を探します。 パスワードを更新して変更を保存します。    
7. 変更内容を保存すると、接続テストが自動的に開始され、テストに成功したことを確認します。    
8. 「[最終停止時刻からジョブを開始する](#start-your-job-from-the-last-stopped-time)」セクションに進みます。

### <a name="power-bi"></a>Power BI
1. Azure Portal にサインインして Stream Analytics ジョブを参照し、**[停止]** を選んで、ジョブが停止するまで待ちます。    
2. 資格情報を更新する Power BI 出力を選び、**[承認の更新]** をクリックし (成功メッセージを確認します)、**[保存]** をクリックして変更を保存します。    
3. 変更内容を保存すると、接続テストが自動的に開始され、テストに成功したことを確認します。    
4. 「[最終停止時刻からジョブを開始する](#start-your-job-from-the-last-stopped-time)」セクションに進みます。

## <a name="start-your-job-from-the-last-stopped-time"></a>最後に停止した時刻からジョブを開始する

1. ジョブの **[概要]** ウィンドウに移動し、**[開始]** を選んでジョブを開始します。    
2. **[最終停止時刻]** を選び、**[開始]** をクリックします。 [最終停止時刻] オプションは、以前にジョブを実行して何らかの出力が生成された場合にのみ表示されることに注意してください。 最後の出力値の時刻に基づいてジョブが再び開始されます。
   ![Stream Analytics ジョブの開始](media/stream-analytics-login-credentials-inputs-outputs/start-stream-analytics-job.png)

## <a name="next-steps"></a>次の手順
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
