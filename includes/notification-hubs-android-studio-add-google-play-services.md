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
ms.openlocfilehash: cd202c98ed605209f5600965ecdb6c0b4c03c17e
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509127"
---
1. Android Studio で、メニューから **[ツール]** を選択し、 **[SDK Manager]** を選択します。 
2. 自分のプロジェクトで使用される Android SDK のターゲット バージョンを選択します。 次に、 **[パッケージの詳細ページの表示]** を選択します。 

    ![Android SDK Manager - ターゲット バージョンを選択する](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. まだインストールされていない場合は、 **[Google API]** を選択します。

    ![Android SDK Manager - Google API の選択](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. **[SDK Tools]\(SDK ツール\)** タブに切り替えます。Google Play 開発者サービスをまだインストールしていない場合は、次の図に示すように **[Google Play 開発者サービス]** を選択します。 次に、 **[適用]** をクリックしてインストールします。 SDK のパスは後の手順で使用するので、メモしておいてください。

    ![Android SDK Manager - Google Play 開発者サービスの選択](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. **[Confirm Change]\(変更の確認\)** ダイアログ ボックスが表示される場合は、 **[OK]** を選択します。 コンポーネント インストーラーによって、要求したコンポーネントがインストールされます。 コンポーネントがインストールされた後、 **[Finish]\(完了\)** を選択します。
4. **[OK]** を選択して、 **[Settings for New Projects]\(新しいプロジェクトの設定\)** ダイアログ ボックスを閉じます。  
5. **app** ディレクトリの build.gradle ファイルを開き、`dependencies` の下に次の行を追加します。 

    ```gradle
    implementation 'com.google.android.gms:play-services-gcm:16.0.0'
    ```
5. ツール バーの **[今すぐ同期]** アイコンを選択します。

    ![Gradle と同期する](./media/notification-hubs-android-studio-add-google-play-services/gradle-sync.png)
1. AndroidManifest.xml ファイルを開き、次のタグを *application* タグに追加します。

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```
