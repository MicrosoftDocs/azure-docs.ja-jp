---
title: インクルード ファイル
description: インクルード ファイル
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 02/05/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: fb27386881e89cd9056d0efccb7d3c301867bd83
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/08/2019
ms.locfileid: "55965989"
---
1. **Android Studio** で、メニューの **[Tools]\(ツール\)** を選択し、**[SDK Manager]\(SDK Manager\)** を選択します。 
2. プロジェクトで使用する Android SDK のターゲット バージョンを選択し、**[Show Package Details]\(パッケージの詳細の表示\)** を選択します。 

    ![Android SDK Manager - ターゲット バージョンを選択する](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. まだインストールされていない場合は、**[Google APIs]\(Google API\)** を選択します。

    ![Android SDK Manager - Google API の選択](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. **[SDK Tools]\(SDK ツール\)** タブに切り替えます。Google Play 開発者サービスをまだインストールしていない場合は、次の図に示すように **[Google Play Services]\(Google Play Services\)** をクリックします。 次に、 **[適用]** をクリックしてインストールします。 SDK のパスは後の手順で使用するので、メモしておいてください。

    ![Android SDK Manager - Google Play 開発者サービスの選択](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. **[Confirm Change]\(変更の確認\)** ダイアログ ボックスが表示される場合は、**[OK]** を選択します。 コンポーネント インストーラーによって、要求したコンポーネントがインストールされます。 コンポーネントがインストールされた後、**[Finish]\(完了\)** を選択します。
4. **[OK]** を選択して、**[Settings for New Projects]\(新しいプロジェクトの設定\)** ダイアログ ボックスを閉じます。  
5. **app** ディレクトリの `build.gradle` ファイルを開き、`dependencies` の下に次の行を追加します。 

    ```gradle
    implementation 'com.google.android.gms:play-services-gcm:16.0.0'
    ```
5. ツール バーの **[Sync Now]\(今すぐ同期\)** アイコンを選択します。

    ![Gradle と同期する](./media/notification-hubs-android-studio-add-google-play-services/gradle-sync.png)
1. **AndroidManifest.xml** を開き、このタグを *application* タグに追加します。

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```
