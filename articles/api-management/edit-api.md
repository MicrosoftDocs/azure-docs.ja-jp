---
title: Azure Portal を使用した API の編集 | Microsoft Docs
description: このチュートリアルでは、API Management (APIM) を使用して API を編集する方法を示します。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 11/08/2017
ms.author: apimpm
ms.openlocfilehash: 6be36493fabce07838991c789e111e918a9a826d
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072152"
---
# <a name="edit-an-api"></a>API の編集

このチュートリアルの手順では、API Management (APIM) を使用して API を編集する方法を示します。 

+ これは、APIM インスタンスで操作を追加、削除、名前変更することで行うことができます。 
+ API の Swagger を編集することができます。

## <a name="prerequisites"></a>前提条件

+ [Azure API Management インスタンスを作成する](get-started-create-service-instance.md)
+ [最初の API をインポートして発行する](import-and-publish.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="edit-an-api-in-apim"></a>APIM で API を編集する

![API の編集](./media/edit-api/edit-api001.png)

1. **[API]** タブをクリックします。
2. 前にインポートした API の 1 つを選びます。
3. **[デザイン]** タブを選択します。
4. 編集する操作を選びます。
5. 操作の名前を変更するには、 **[フロントエンド]** ウィンドウの**鉛筆**を選択します。

## <a name="update-the-swagger"></a>Swagger を更新する

次の手順に従って、Azure Portal からバックエンド API を更新できます。

1. **[すべての操作]** を選択します
2. **[フロントエンド]** ウィンドウの鉛筆をクリックします。

    ![API の編集](./media/edit-api/edit-api002.png)

    API の Swagger が表示されます。

    ![API の編集](./media/edit-api/edit-api003.png)

3. Swagger を更新します。
4. **[保存]** をクリックします。

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [APIM ポリシーのサンプル](policy-samples.md)
> [発行された API の変換と保護](transform-api.md)