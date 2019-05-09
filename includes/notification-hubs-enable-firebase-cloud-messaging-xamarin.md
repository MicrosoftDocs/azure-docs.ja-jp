---
title: インクルード ファイル
description: インクルード ファイル
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/11/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 90ffebf5f3375e94545f82a95f5c240ad845bd94
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/14/2019
ms.locfileid: "57908288"
---
1. [Firebase コンソール](https://firebase.google.com/console/)にサインインします。 Firebase プロジェクトがまだない場合は、新しく作成します。
2. プロジェクトを作成した後、**[Add Firebase to your Android app]\(Android アプリに Firebase を追加する\)** を選択します。 

    ![Android アプリへの Firebase の追加](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. **[Android アプリへの Firebase の追加]** ページで、次の手順を実行します。 
    1. **[Android package name]\(Android パッケージ名\)** に、パッケージの名前を入力します。 (例: `tutorials.tutoria1.xamarinfcmapp`)。 

        ![パッケージ名を指定する](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. **[アプリの登録]** を選択します。 
4. **[google-services.json をダウンロード]** を選択し、プロジェクトの **app** フォルダーにファイルを保存して、**[次へ]** をクリックします。 

    ![google-services.json をダウンロードする](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
6. ページで **[次へ]** を選択します。 
7. ページで **[この手順をスキップする]**  を選択します。 

    ![最後の手順をスキップする](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. Firebase コンソールで、プロジェクトの歯車アイコンを選択します。 次に、**[Project Settings]\(プロジェクト設定\)** を選択します。

    ![[Project Settings]\(プロジェクト設定\) の選択](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. **google-services.json** ファイルをダウンロードしていない場合は、このページでダウンロードできます。 
5. 上部にある **[クラウド メッセージング]** タブに切り替えます。 
6. 後で使用するために、**サーバー キー**をコピーし、保存します。 この値を使用して通知ハブを構成します。
