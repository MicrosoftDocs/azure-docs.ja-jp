---
title: インクルード ファイル
description: インクルード ファイル
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 10/16/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b84b0a8e09bf739ce62dee167ff751b491765c66
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684669"
---
### <a name="create-a-storage-account-for-event-processor-host"></a>イベント プロセッサ ホストのストレージ アカウントの作成
イベント プロセッサ ホストは、永続的なチェックポイントの管理によって Event Hubs のイベントの受信を簡素化し、並列して受信を行うインテリジェントなエージェントです。 チェックポイント処理を実行するために、イベント プロセッサ ホストではストレージ アカウントが必要です。 次の例は、ストレージ アカウントを作成する方法とそのアクセス キーを取得する方法を示します。

1. Azure portal で、画面の左上にある **[リソースの作成]** を選択します。

2. **[ストレージ]**、**[ストレージ アカウント - Blob、File、Table、Queue]** の順に選択します。
   
    ![ストレージ アカウントを選択する](./media/event-hubs-create-storage/create-storage1.png)

3. **[ストレージ アカウントの作成]** ページで、次の手順を実行します。 

   1. ストレージ アカウントの名前を入力します。 
   2. イベント ハブが含まれている Azure サブスクリプションを選択します。
   3. イベント ハブがあるリソース グループを選択します。
   4. リソースを作成する場所を選択します。 
   5. **[確認および作成]** をクリックします。
   
      ![ストレージ アカウントの作成 - ページ](./media/event-hubs-create-storage/create-storage2.png)

4. **[確認および作成]** ページで値を確認し、**[作成]** を選択します。 

    ![ストレージ アカウントの設定を確認し、作成する](./media/event-hubs-create-storage/review-create-storage-account.png)
5. "**デプロイメントが成功しました**" メッセージが表示されたら、ページの上部にある **[リソースに移動]** を選択します。 リソース リストからストレージ アカウントを選択して、ストレージ アカウント ページを起動することもできます。  

    ![デプロイのストレージ アカウントを選択する](./media/event-hubs-create-storage/select-storage-deployment.png) 
7. **[要点]** ウィンドウで **[BLOB]** を選択します。 

    ![BLOB サービスを選択する](./media/event-hubs-create-storage/select-blobs-service.png)
1. 上部にある **[+ コンテナー]** を選択して、コンテナーの**名前**を入力して、**[OK]** を選択します。 

    ![BLOB コンテナーを作成する](./media/event-hubs-create-storage/create-blob-container.png)
1. 左側のメニューの **[アクセス キー]** を選択して、**key1** の値をコピーします。 

    次の値をメモ帳などに一時的に保存します。
    - ストレージ アカウントの名前
    - ストレージ アカウントのアクセス キー
    - コンテナーの名前
