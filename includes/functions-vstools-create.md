---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 6c430f22a9d4fa0fad95bcaa41675545fffd91ec
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58227220"
---
Visual Studio の Azure Functions プロジェクト テンプレートでは、Azure の関数アプリに発行できるプロジェクトを作成します。 関数アプリを使用すると、リソースを管理、デプロイ、および共有するための論理ユニットとして関数をグループ化できます。

1. Visual Studio の **[ファイル]** メニューで､**[新規作成]** > **[プロジェクト]** を選択します。

2. **[新しいプロジェクト]** ダイアログ ボックスで、**[インストール済み]** > **[Visual C#]** > **[クラウド]** > **[Azure Functions]** を展開します。 プロジェクトの名前を入力し、**[ON]** を選択します。 関数アプリ名は、C# 名前空間として有効である必要があります。そのため、アンダースコア、ハイフン、その他の英数字以外の文字は使用しないでください。

    ![Visual Studio で関数を作成するための [新しいプロジェクト] ダイアログ ボックス](./media/functions-vstools-create/functions-vs-new-project.png)

3. 図の下の表に示した設定を使用してください。

    ![Visual Studio の [新しい関数] ダイアログ ボックス](./media/functions-vstools-create/functions-vs-new-function.png) 

    | Setting      | 推奨値  | 説明                      |
    | ------------ |  ------- |----------------------------------------- |
    | **バージョン** | Azure Functions 2.x <br />(.NET Core) | この設定で、.NET Core をサポートする Azure Functions のバージョン 2.x ランタイムを使用する関数プロジェクトが作成されます。 Azure Functions 1.x では、.NET Framework がサポートされます。 詳細については、[Azure Functions ランタイム バージョンをターゲットにする方法](../articles/azure-functions/functions-versions.md)に関するページを参照してください。   |
    | **テンプレート** | HTTP トリガー | この設定で、HTTP 要求によってトリガーされる関数が作成されます。 |
    | **ストレージ アカウント**  | ストレージ エミュレーター | HTTP トリガーは Azure Storage アカウント接続を使用しません。 他のすべてのトリガーの種類では、有効なストレージ アカウント接続文字列が必要です。 |
    | **アクセス権** | Anonymous | 作成される関数を、すべてのクライアントがキーを使用せずにトリガーできます。 この承認設定により、新しい関数のテストが容易になります。 キーと承認の詳細については、「[Azure Functions における HTTP と webhook のバインド](../articles/azure-functions/functions-bindings-http-webhook.md)」の「[承認キー](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys)」をご覧ください。 |
    
    > [!NOTE]
    > **アクセス権**を `Anonymous` に設定していることを確認します。 `Function` の既定のレベルを選択した場合、関数エンドポイントにアクセスする要求で、[関数キー](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys)を提示する必要があります。
    
4. **[OK]** を選択して、関数プロジェクトと、HTTP でトリガーされる関数を作成します。
