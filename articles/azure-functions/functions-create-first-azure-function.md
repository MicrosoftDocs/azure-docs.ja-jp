---
title: "初めての Azure 関数の作成 | Microsoft Docs"
description: "初めての Azure 関数、つまりサーバー不要のアプリケーションを&2; 分以内に作成します。"
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 4a1669e7-233e-4ea2-9b83-b8624f2dbe59
ms.service: functions
ms.devlang: multiple
ms.topic: hero-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/14/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: a797910e286cd2aacf5a8aa6c509e2b0f5f60276
ms.lasthandoff: 03/17/2017


---
# <a name="create-your-first-azure-function"></a>初めての Azure 関数の作成

このトピックでは、ポータルで Azure Functions のクイック スタートを使用して、HTTP 要求によって呼び出される簡単な "hello world" 関数を作成する方法について説明します。 Azure Functions の詳細については、「 [Azure Functions の概要](functions-overview.md)」を参照してください。

開始する前に、Azure アカウントが必要です。 [無料アカウント](https://azure.microsoft.com/free/)を入手できます。 また、Azure に登録せずに、[Azure Functions を試す](https://azure.microsoft.com/try/app-service/functions/)こともできます。

## <a name="create-a-function-from-the-portal-quickstart"></a>ポータルのクイック スタートから関数を作成する

1. [Azure Functions ポータル](https://functions.azure.com/signin)に移動し、Azure アカウントでサインインします。 
 
2. 新しい関数アプリの一意の**名前**を入力するか、自動生成された名前をそのまま使用し、希望の**リージョン**を選んで、**[作成 + 開始]** をクリックします。 有効な名前には、英文字、数字、ハイフンのみを使用できます。 アンダースコア (**_**) は使用できません。

3. **[クイック スタート]** タブで、**[webhook + API]** をクリックし、関数の言語を選択して、**[この関数を作成する]** をクリックします。 選択した言語で、定義済み関数が新しく作成されます。 
   
    ![](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

4. (オプション) クイック スタートのこの時点で、ポータルの Azure Functions 機能のクイック ツアーを開始するかどうか選択することができます。 ツアーを完了するかスキップしたら、HTTP 要求を送信して、新しい関数をテストできます。

## <a name="test-the-function"></a>関数をテストする
[!INCLUDE [Functions quickstart test](../../includes/functions-quickstart-test.md)]

## <a name="watch-the-video"></a>ビデオを見る
次のビデオでは、このチュートリアルの基本的な手順を実行する方法について説明します。 

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-your-first-Azure-Function-simple/player]
> 


## <a name="next-steps"></a>次のステップ
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


