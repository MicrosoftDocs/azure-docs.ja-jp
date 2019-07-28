---
title: インクルード ファイル
description: インクルード ファイル
services: cdn
author: syntaxc4
ms.service: azure-cdn
ms.topic: include
ms.date: 05/24/2018
ms.author: cfowler
ms.custom: include file
ms.openlocfilehash: 8aa6cb3f10b86a6821cd93190ecc2135508739cb
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594004"
---
## <a name="create-a-new-cdn-profile"></a>新しい CDN プロファイルを作成する

CDN プロファイルは、CDN エンドポイントのコンテナーであり、価格レベルを指定します。

1. Azure Portal の左上隅にある **[リソースの作成]** を選択します。 
    
    **[新規]** ウィンドウが表示されます。
   
2. **[Web + モバイル]** 、 **[CDN]** の順に選択します。
   
    ![CDN リソースの選択](./media/cdn-create-profile/cdn-new-resource.png)

    **[CDN のプロファイル]** ウィンドウが表示されます。

3. CDN プロファイルの設定については、次の表で指定されている値を使います。
   
    | Setting  | 値 |
    | -------- | ----- |
    | **Name** | プロファイル名として「*my-cdn-profile-123*」と入力します。 この名前はグローバルに一意でなければなりません。この名前が既に使用されている場合は、別の名前を入力してください。 |
    | **サブスクリプション** | ドロップダウン リストから Azure サブスクリプションを選択します。 |
    | **リソース グループ** | **[新規作成]** を選択し、リソース グループ名として「*my-resource-group-123*」と入力します。 既に使用している場合は、別の名前を入力するか、 **[既存のものを使用]** を選択し、ドロップダウン リストから **[my-resource-group-123]** を選択できます。 | 
    | **リソース グループの場所** | ドロップダウンから **[米国中部]** を選択します。 |
    | **[価格レベル]** | ドロップダウンから **[Standard Verizon]** を選択します。 |
    | **今すぐ新しい CDN エンドポイントを作成する** | オフのままにします。 |  
   
    ![新しい CDN プロファイル](./media/cdn-create-profile/cdn-new-profile.png)

4. プロファイルを作成後にダッシュボードに保存するには、 **[ダッシュボードにピン留め]** をオンにします。
    
5. **[作成]** を選択してプロファイルを作成します。 

    **Azure CDN Standard from Microsoft** プロファイルのみの場合、プロファイル完了は通常 2 時間以内で完了します。 

