---
title: インクルード ファイル
description: インクルード ファイル
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/06/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: a7d0a899b4a2ef86153e82a4e3f04e7ec430a1bc
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488044"
---
1. [Google Cloud Console](https://console.developers.google.com/cloud-resource-manager)に移動し、Google アカウントの資格情報でサインインします。 
2. ツールバーの **[プロジェクトの作成]** を選択します。 
   
    ![新しいプロジェクトの作成](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)   
3. **[プロジェクト名]** にプロジェクトの名前を入力し、**[作成]** をクリックします。
4. ツールバーの **[通知]** ボタンを選択し、一覧から自分のプロジェクトを選択します。 プロジェクトのダッシュ ボードが表示されます。 URL "`https://console.developers.google.com/home/dashboard?project=<YOUR PROJECT NAME>`" を使用してダッシュボードに直接アクセスすることもできます。

    ![[通知] でプロジェクトを選択する](./media/mobile-services-enable-google-cloud-messaging/alert-new-project.png)
5. ダッシュボードの **[プロジェクト情報]** タイルにある**プロジェクト番号**をメモしておきます。 

    ![プロジェクト ID](./media/mobile-services-enable-google-cloud-messaging/project-number.png)
6. ダッシュボードの **[API]** タイルで、**[API の概要に移動]** を選択します。 

    ![API 概要へのリンク](./media/mobile-services-enable-google-cloud-messaging/go-to-api-overview.png)
7. **[API]** ページで **[API とサービスの有効化]** を選択します。 

    ![[API とサービスの有効化] ボタン](./media/mobile-services-enable-google-cloud-messaging/enable-api-services-button.png)
8. **[Google Cloud Messaging]** を探して選択します。 

    ![Google Cloud Messaging を探して選択](./media/mobile-services-enable-google-cloud-messaging/search-select-gcm.png)
9. プロジェクトで [Google Cloud Messaging] を有効にするには、**[有効にする]** を選択します。

    ![Google Cloud Messaging を有効にする](./media/mobile-services-enable-google-cloud-messaging/enable-gcm-button.png)
10. ツールバーで **[認証情報を作成]** を選択します。 

    ![[認証情報を作成] ボタン](./media/mobile-services-enable-google-cloud-messaging/create-credentials-button.png)
11. **[プロジェクトへの認証情報の追加]** ページで、**[API キー]** リンクを選択します。 

    ![[認証情報を作成] ボタン](./media/mobile-services-enable-google-cloud-messaging/api-key-button.png)    
12. **[API キー]** ページで、**[Create/Save]\(作成/保存\)** を選択します。 次の例では、**[IP アドレス]** のオプションが選択され、許可されている IP アドレスとして **0.0.0.0/0** が入力されています。 API キーには適切な制限を設定する必要があります。 

    ![[API キー] ページの [Create]\(作成\) ボタン](./media/mobile-services-enable-google-cloud-messaging/api-key-create-button.png)
13. **API キー**をクリップボードにコピーして、任意の場所に保存します。 

    ![API キーをコピーする](./media/mobile-services-enable-google-cloud-messaging/copy-api-key.png)
   
    この API キー値を使用して、Azure が GCM で認証し、アプリの代わりにプッシュ通知を送信できるようにします。 プロジェクト ダッシュボードに戻るには、URL "`https://console.developers.google.com/home/dashboard?project=<YOUR PROJECT NAME>`" を使用します。

