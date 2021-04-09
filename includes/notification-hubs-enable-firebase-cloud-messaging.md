---
title: インクルード ファイル
description: インクルード ファイル
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 09/11/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ccb5e40738680181e7339b8652d029597c7d0bd4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "70935150"
---
1. [Firebase コンソール](https://firebase.google.com/console/)にサインインします。 Firebase プロジェクトがまだない場合は、新しく作成します。
2. プロジェクトを作成した後、 **[Add Firebase to your Android app]\(Android アプリに Firebase を追加する\)** を選択します。 

    ![Android アプリへの Firebase の追加](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. **[Android アプリへの Firebase の追加]** ページで、次の手順を実行します。 
    1. **[Android package name]\(Android パッケージ名\)** については、お使いのアプリケーションの build.gradle ファイル内にある **applicationId** の値をコピーします。 この例では `com.fabrikam.fcmtutorial1app` です。 

        ![パッケージ名を指定する](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. **[アプリの登録]** を選択します。 
4. **[google-services.json をダウンロード]** を選択し、プロジェクトの **app** フォルダーにファイルを保存して、**[次へ]** をクリックします。 

    ![google-services.json をダウンロードする](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
5. Android Studio で、プロジェクトに次の **構成変更** を加えます。 
    1.  プロジェクト レベルの build.gradle ファイル (&lt;project&gt;/build.gradle) で、**dependencies** セクションに次のステートメントを追加します。 

        ```
        classpath 'com.google.gms:google-services:4.0.1'
        ```
    2. アプリレベルの build.gradle ファイル (&lt;project&gt;/&lt;app-module&gt;/build.gradle) で、**dependencies** セクションに次のステートメントを追加します。 

        ```
        implementation 'com.google.firebase:firebase-core:16.0.8'
        implementation 'com.google.firebase:firebase-messaging:17.3.4'
        ```

    3. アプリ レベルの build.gradle ファイルの最後の dependencies セクションの末尾に次の行を追加します。 

        ```
        apply plugin: 'com.google.gms.google-services'
        ```        
    4. ツールバーの **[今すぐ同期]** を選択します。 
 
        ![build.gradle の構成変更](./media/notification-hubs-enable-firebase-cloud-messaging/build-gradle-configurations.png)
6. **[次へ]** を選択します。 
7. **[この手順をスキップする]** を選択します。 

    ![最後の手順をスキップする](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. Firebase コンソールで、プロジェクトの歯車アイコンを選択します。 次に、 **[Project Settings]\(プロジェクト設定\)** を選択します。

    ![[Project Settings]\(プロジェクト設定\) の選択](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. google-services.json ファイルを Android Studio プロジェクトの **app** フォルダーにダウンロードしていない場合は、このページでダウンロードできます。 
5. 上部にある **[クラウド メッセージング]** タブに切り替えます。 
6. 後で使用するために、**サーバー キー** をコピーし、保存します。 この値を使用して、ハブを構成します。
