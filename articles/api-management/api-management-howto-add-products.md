---
title: Azure API Management で製品を作成して発行する方法
description: Azure API Management で製品を作成して発行する方法について説明します。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 08/10/2018
ms.author: apimpm
ms.openlocfilehash: 134483764ffcb012ece0769a514683fc9487887f
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073628"
---
# <a name="create-and-publish-a-product"></a>製品を作成して発行する  

Azure API Management の製品には、少なくとも 1 つの API に加え、使用量クォータや使用条件が含まれます。 製品が発行されると、開発者は成果物をサブスクライブして、成果物の API の利用を開始できます。  

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 製品を作成して発行する
> * 製品に API を追加する

![製品チュートリアルを追加する](media/api-management-howto-add-products/added-product.png)

## <a name="prerequisites"></a>前提条件

+ [Azure API Management の用語](api-management-terminology.md)について学習します。
+ 次のクイック スタートを完了すること:[Azure API Management インスタンスを作成する](get-started-create-service-instance.md)。
+ また、次のチュートリアルを完了すること: [最初の API のインポートと発行](import-and-publish.md)。

## <a name="create-and-publish-a-product"></a>製品を作成して発行する

![製品の追加](media/api-management-howto-add-products/02-create-publish-product-01.png)

1. 左側のメニューの **[製品]** をクリックして **[製品]** ページを表示します。
2. **[+ 追加]** をクリックします。

    製品を追加するときに、次の情報を指定する必要があります。 

    | 名前                     | 説明                                                                                                                                                                                                                                                                                                             |
    |--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Display name             | **[開発者ポータル]** で表示する名前です。                                                                                                                                                                                                                                                        |
    | 名前                     | 製品のわかりやすい名前。                                                                                                                                                                                                                                                                                      |
    | 説明              | **[説明]** フィールドには、その目的、アクセスできる API、その他の有益な情報など、製品に関する詳しい情報を入力できます。                                                                                                                                               |
    | State                    | 製品を発行する場合は **[発行済み]** を押します。 製品に含まれる API を呼び出すには、あらかじめ製品を発行しておく必要があります。 新しい製品は既定で発行されず、**Administrators** グループのみに表示されます。                                                                                      |
    | サブスクリプションを要求する    | ユーザーが製品を使用するにはサブスクリプションが必要である場合は、 **[サブスクリプションを要求する]** をオンにします。                                                                                                                                                                                                                                   |
    | 承認が必要        | この製品に対するサブスクリプションの申し込みを管理者の審査の下で承認または拒否する場合は、 **[承認を要求する]** をオンにします。 チェック ボックスがオフの場合、サブスクリプションの申し込みは自動承認されます。                                                                                                                         |
    | サブスクリプション数の制限 | 複数同時に利用できるサブスクリプションの数を制限するには、サブスクリプションの上限を入力します。                                                                                                                                                                                                                                |
    | 法的条項              | サブスクライバーが製品を使用するにあたって同意する必要がある製品の使用条件を入力できます。                                                                                                                                                                                                             |
    | API                     | 製品には、1 つまたは複数の API が関連付けられています。 複数の API を組み込み、開発者ポータルを通じてそれらを開発者に提供できます。 <br/> 製品の作成時に既存の API を追加できます。 製品には後から API を追加できます。 **[設定]** ページを使用して追加することも、API の作成時に追加することもできます。 |

3. **[作成]** をクリックして新しい製品を作成します。

### <a name="add-more-configurations"></a>構成をさらに追加する

**[設定]** タブを選択すると、保存した後で続けて製品を構成できます。 

**[サブスクリプション]** タブでサブスクライバーを表示するか、または製品にサブスクライバーを追加します。

**[アクセス制御]** タブで、開発者やゲスト向けに製品の可視性を設定します。

## <a name="add-apis"> </a>製品への API の追加

製品には、1 つまたは複数の API が関連付けられています。 複数の API を組み込み、開発者ポータルを通じてそれらを開発者に提供できます。 製品の作成時に既存の API を追加できます。 製品には後から API を追加できます。 **[設定]** ページを使用して追加することも、API の作成時に追加することもできます。

開発者は、まず製品をサブスクライブして API へのアクセス権を取得する必要があります。 サブスクライブすると、その製品の API に適したサブスクリプション キーを受け取ります。 APIM インスタンスを作成した場合は、既に管理者になっているため、既定ですべての製品をサブスクライブしています。

### <a name="add-an-api-to-an-existing-product"></a>既存の製品に API を追加する

![製品 API を追加する](media/api-management-howto-add-products/02-create-publish-product-02.png)

1. **[製品]** タブで、製品を選択します。
2. **[API]** タブに移動します。
3. **[+ 追加]** をクリックします。
4. API を選択して **[選択]** をクリックします。

> [!TIP]
> [REST API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/subscription/createorupdate) または PowerShell コマンドを通じてカスタム サブスクリプション キーを使用して、"*製品*" へのユーザーのサブスクリプションを作成または更新できます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * 製品を作成して発行する
> * 製品に API を追加する

次のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [空の API とAPI のモック応答を作成する](mock-api-responses.md)
