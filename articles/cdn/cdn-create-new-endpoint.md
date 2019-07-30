---
title: クイック スタート - Azure CDN プロファイルとエンドポイントの作成 | Microsoft Docs
description: このクイック スタートでは、CDN プロファイルと CDN エンドポイントを新しく作成することによって Azure CDN を有効にする方法を紹介しています。
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 4ca51224-5423-419b-98cf-89860ef516d2
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 05/24/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 2a3325217c1ec854e4f6cef3facce5580fb06a57
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593985"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint"></a>クイック スタート:Azure CDN プロファイルとエンドポイントの作成
このクイック スタートでは、CDN プロファイルと CDN エンドポイントを新しく作成することによって Azure Content Delivery Network (CDN) を有効にします。 プロファイルとエンドポイントを作成すると、顧客へのコンテンツの配信を開始することができます。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件
このクイック スタートを行うには、配信元ホスト名として使用する、*mystorageacct123* という名前のストレージ アカウントを作成しておく必要があります。 詳しくは、「[Azure ストレージ アカウントと Azure CDN との統合](cdn-create-a-storage-account-with-cdn.md)」をご覧ください。

## <a name="log-in-to-the-azure-portal"></a>Azure Portal にログインする
Azure アカウントを使用して [Azure Portal](https://portal.azure.com) にログインします。

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>新しい CDN エンドポイントの作成

CDN プロファイルを作成したら、これを使用してエンドポイントを作成できます。

1. Azure Portal のダッシュボードで、作成した CDN プロファイルを選択します。 目的の CDN プロファイルが見つからない場合は、 **[すべてのサービス]** 、 **[CDN のプロファイル]** の順に選択します。 **[CDN のプロファイル]** ページで、使用するプロファイルを選択します。 
   
    [CDN のプロファイル] ページが表示されます。

2. **[エンドポイント]** を選択します。
   
    ![CDN プロファイル](./media/cdn-create-new-endpoint/cdn-select-endpoint.png)
   
    **[エンドポイントの追加]** ウィンドウが表示されます。

3. エンドポイント設定については、次の表で指定されている値を使用します。

    | Setting | 値 |
    | ------- | ----- |
    | **Name** | エンドポイントのホスト名として「*my-endpoint-123*」と入力します。 この名前はグローバルに一意でなければなりません。この名前が既に使用されている場合は、別の名前を入力してください。 この名前は、ドメイン _&lt;エンドポイント名&gt;_ .azureedge.net でキャッシュされたリソースにアクセスする際に使用します。|
    | **配信元の種類** | **[ストレージ]** を選択します。 | 
    | **配信元のホスト名** | ホスト名として「*mystorageacct123.blob.core.windows.net*」と入力します。 この名前はグローバルに一意でなければなりません。この名前が既に使用されている場合は、別の名前を入力してください。 |
    | **配信元のパス** | 空白のままにします。 |
    | **配信元のホスト ヘッダー** | 生成された既定値をそのまま使用します。 |  
    | **プロトコル** | **[HTTP]** と **[HTTPS]** のオプションを既定値の選択状態のままにします。 |
    | **配信元のポート** | 既定のポート値のままにします。 | 
    | **最適化の対象** | 既定で選択される **[一般的な Web 配信]** のままにします。 |

    ![エンドポイントの追加ウィンドウ](./media/cdn-create-new-endpoint/cdn-add-endpoint.png)

3. **[追加]** を選択して、新しいエンドポイントを作成します。
   
   エンドポイントが作成されると、プロファイルのエンドポイントの一覧に表示されます。
    
   ![CDN エンドポイント](./media/cdn-create-new-endpoint/cdn-endpoint-success.png)
    
   登録内容が反映されるまでに時間がかかるため、エンドポイントはすぐには使用できません。 
   - **Azure CDN Standard from Microsoft** プロファイルの場合、通常、反映は 10 分以内で完了します。 
   - **Azure CDN Standard from Akamai** プロファイルの場合、通常、反映は 1 分以内で完了します。 
   - **Azure CDN Standard from Verizon** プロファイルおよび **Azure CDN Premium from Verizon** プロファイルの場合、通常、反映は 90 分以内で完了します。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ
前の手順では、リソース グループ内に CDN プロファイルとエンドポイントを作成しました。 [次の手順](#next-steps)に進んでエンドポイントにカスタム ドメインを追加する方法について学習するには、これらのリソースを保存してください。 ただし、将来これらのリソースを使用する予定がない場合は、次の手順に従ってリソース グループを削除してリソースを削除することで、追加の料金が発生するのを避けることができます。

1. Azure Portal の左側のメニューで、 **[リソース グループ]** 、 **[my-resource-group-123]** の順に選択します。

2. **[リソース グループ]** ページで、 **[リソース グループの削除]** を選択し、テキスト ボックスに「*my-resource-group-123*」と入力し、 **[削除]** を選択します。

    このアクションにより、このクイック スタートで作成したリソース グループ、プロファイル、およびエンドポイントが削除されます。

## <a name="next-steps"></a>次の手順
カスタム ドメインを CDN エンドポイントに追加する方法については、次のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [チュートリアル:カスタム ドメインを Azure CDN エンドポイントに追加する](cdn-map-content-to-custom-domain.md)


