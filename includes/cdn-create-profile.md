---
title: インクルード ファイル
description: インクルード ファイル
services: cdn
author: SyntaxC4
ms.service: azure-cdn
ms.topic: include
ms.date: 04/30/2020
ms.author: cfowler
ms.custom: include file
ms.openlocfilehash: d73a8d743a948cbd94a3af81fe2e77c45c0eeb67
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996279"
---
## <a name="create-a-new-cdn-profile"></a>新しい CDN プロファイルを作成する

CDN プロファイルは、CDN エンドポイントのコンテナーであり、価格レベルを指定します。

1. Azure portal の左上にある **[リソースの作成]** を選択します。 **[新規]** ウィンドウが表示されます。
   
1. **[CDN]** を見つけて選択し、 **[作成]** を選択します。
   
    ![CDN リソースの選択](./media/cdn-create-profile/cdn-new-resource.png)

    **[CDN のプロファイル]** ウィンドウが表示されます。

1. 次の値を入力します。
   
    | 設定  | 値 |
    | -------- | ----- |
    | **名前** | プロファイル名として「*cdn-profile-123*」と入力します。 この名前はグローバルに一意でなければなりません。この名前が既に使用されている場合は、別の名前を入力してください。 |
    | **サブスクリプション** | ドロップダウン リストから Azure サブスクリプションを選択します。 |
    | **リソース グループ** | **[新規作成]** を選択してリソース グループ名に「*CDNQuickstart-rg*」と入力します。または、そのグループが既にある場合は、 **[Use existing]\(既存のものを使用\)** を選択して *[CDNQuickstart-rg]* を選択します。 | 
    | **リソース グループの場所** | ドロップダウン リストから最寄りの場所を選択します。 |
    | **価格レベル** | ドロップダウン リストから **[Standard Akamai]** オプションを選択します。 (Akamai レベルのデプロイ時間は約 1 分です。 Microsoft レベルは約 10 分、Verizon レベルは約 90 分かかります。) |
    | **今すぐ新しい CDN エンドポイントを作成する** | オフのままにします。 |  
   
    ![新しい CDN プロファイル](./media/cdn-create-profile/cdn-new-profile.png)

1. **[作成]** を選択してプロファイルを作成します。

