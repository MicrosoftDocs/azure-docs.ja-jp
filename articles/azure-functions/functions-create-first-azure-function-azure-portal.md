---
title: "Azure Portal で初めての関数を作成する | Microsoft Docs"
description: "Azure へようこそ。 Azure Portal で初めての Azure 関数を作成しましょう。"
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/15/2017
ms.author: glenga
ms.custom: welcome-email
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: 6bb15dfd0e2c40b99d06ef2d695e3ba7ca78e91b
ms.lasthandoff: 03/22/2017


---
# <a name="create-your-first-function-in-the-azure-portal"></a>Azure Portal で初めての関数を作成する

このトピックでは、HTTP 要求によって呼び出される簡単な "hello world" Azure 関数を作成する方法について説明します。 Azure Portal で関数を作成するには、関数の実行をホストするための Function App を Azure App Service で作成しておく必要があります。

このクイック スタートを完了するには、Azure アカウントが必要です。 [無料アカウント](https://azure.microsoft.com/free/)を入手できます。 また、Azure に登録せずに、[Azure Functions を試す](https://azure.microsoft.com/try/app-service/functions/)こともできます。

## <a name="create-a-function-app"></a>Function App を作成する

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

詳しくは、「[Azure Portal から関数アプリを作成する](functions-create-function-app-portal.md)」をご覧ください。

## <a name="create-a-function"></a>関数を作成する
次の手順では、Azure Functions クイック スタートを使用して、新しい Function App で関数を作成します。

1. **[クイック スタート]** タブで、**[webhook + API]** をクリックし、関数の言語を選択して、**[この関数を作成する]** をクリックします。 選択した言語で、定義済み関数が新しく作成されます。  
   
    ![](./media/functions-create-first-azure-function-azure-portal/function-app-quickstart-node-webhook.png)

4. (オプション) クイック スタートのこの時点で、ポータルの Azure Functions 機能のクイック ツアーを開始するかどうか選択することができます。 ツアーを完了するかスキップしたら、HTTP 要求を送信して、新しい関数をテストできます。


## <a name="test-the-function"></a>関数をテストする
[!INCLUDE [Functions quickstart test](../../includes/functions-quickstart-test.md)]

## <a name="next-steps"></a>次のステップ
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


