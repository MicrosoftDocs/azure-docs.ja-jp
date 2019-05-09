---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: ef8dae8219eaf1a85a5c112705517b992e25a50f
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2019
ms.locfileid: "56741169"
---
予測エンドポイントへのアクセスには、エンドポイント キーが必要です。 このクイック スタートでは、LUIS アカウントに関連付けられている無料のスタート キーを使用します。 
 
1. LUIS アカウントを使用してサインインします。 

    [![Language Understanding (LUIS) アプリ リストのスクリーン ショット](media/cognitive-services-luis/app-list.png "Language Understanding (LUIS) アプリ リストのスクリーンショット")](media/cognitive-services-luis/app-list.png)

2. 右上のメニューで名前を選択し、**[Settings]\(設定)** を選択します。

    ![LUIS ユーザー設定メニューへのアクセス](media/cognitive-services-luis/get-user-settings-in-luis.png)

3. **[Authoring key]\(オーサリング キー)** の値をコピーします。 後ほど、このクイック スタートで使用します。 

    [![Language Understanding (LUIS) ユーザー設定のスクリーン ショット](media/cognitive-services-luis/get-user-authoring-key.png "Language Understanding (LUIS) ユーザー設定のスクリーン ショット")](media/cognitive-services-luis/get-user-authoring-key.png)

    オーサリング キーを使うと、すべての LUIS アプリで、オーサリング API への要求は無料で無制限に、予測エンドポイント API へのクエリは 1 か月あたり 1000 回まで行うことができます。 <!--Once the prediction endpoint quota from the authoring key is used for the month, you need to create a **Language Understanding** key from the Azure portal. The key created in the portal is known as the endpoint key. The endpoint key is used _only_ for prediction endpoint queries.-->
