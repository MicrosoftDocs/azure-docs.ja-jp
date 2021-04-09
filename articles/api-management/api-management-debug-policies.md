---
title: Visual Studio Code で Azure API Management ポリシーをデバッグする | Microsoft Docs
description: Visual Studio Code の Azure API Management 拡張機能を使用して Azure API Management ポリシーをデバッグする方法について説明します
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/22/2020
ms.author: apimpm
ms.openlocfilehash: 2e45d1274cf7332dbca70eaa8fc51f0ac98e5359
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648018"
---
# <a name="debug-azure-api-management-policies-in-visual-studio-code"></a>Visual Studio Code で Azure API Management ポリシーをデバッグする

Azure API Management の[ポリシー](api-management-policies.md)では、API パブリッシャーが、認証、承認、スロットリング、キャッシュ、変換などの横断的問題に対応する助けになる強力な機能が提供されます。 API の要求または応答に対して順に実行される一連のステートメントが集まってポリシーが形成されます。 

この記事では、[Visual Studio Code 用の Azure API Management 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement)を使用して、API Management ポリシーをデバッグする方法について説明します。 

## <a name="prerequisites"></a>前提条件

* 最初にこの[クイックスタート](get-started-create-service-instance.md)に従って、API Management Developer レベルのインスタンスを作成します。

* [Visual Studio Code](https://code.visualstudio.com/) と、Visual Studio Code 用の最新バージョンの [Azure API Management 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement)をインストールします。 

* API を API Management インスタンスにインポートします。 手順の例については、「[チュートリアル: Visual Studio Code 用の API Management 拡張機能を使用して API をインポートおよび管理する](visual-studio-code-tutorial.md)」を参照してください。

## <a name="restrictions-and-limitations"></a>制限事項と制約事項

この機能は、API Management の Developer レベルでのみ使用できます。 各 API Management インスタンスでサポートされる同時デバッグ セッションは 1 つだけです。

## <a name="initiate-a-debugging-session"></a>デバッグ セッションを開始する

1. Visual Studio Code を起動します
2. Azure 拡張機能で API Management 拡張機能に移動します
3. デバッグする API Management インスタンスを見つけます
4. デバッグする API と操作を見つけます
5. 操作を右クリックし、 **[ポリシーのデバッグの開始]** を選択します

この時点で、拡張機能により、API Management ゲートウェイとの間でデバッグ セッションの開始と確立が試みられます。

![デバッグを開始する](media/api-management-debug-policies/initiate-debugging-session.png)

## <a name="send-a-test-request"></a>テスト要求を送信する
デバッグ セッションが確立されると、拡張機能によって新しいエディターが開かれます。そこで、[REST クライアントの拡張機能](https://marketplace.visualstudio.com/items?itemName=humao.rest-client)を利用してテスト用の HTTP 要求を作成し、この操作に送信できます。

要求には既に **Ocp-Apim-Debug** ヘッダーが追加されていることがわかります。 このヘッダーは必須であり、API Management ゲートウェイでデバッグ機能をトリガーするには、値をサービス レベルのすべてのアクセス許可を持つサブスクリプション キーに設定する必要があります。

実際のテスト シナリオに従って、エディターで HTTP 要求を変更します。 次に、 **[要求の送信]** をクリックして、テスト要求を API Management ゲートウェイに送信します。

![テスト要求を送信する](media/api-management-debug-policies/rest-client.png)

## <a name="debug-policies"></a>デバッグ ポリシー
テスト HTTP 要求が送信されると、拡張機能によって、この操作の有効なポリシーを示すデバッグ ウィンドウが開かれて、最初の有効なポリシーで停止します。 

![ポリシーをデバッグする](media/api-management-debug-policies/main-window.png)

ポリシー パイプラインに従うには、個々のポリシーをシングル ステップ実行するか、ポリシー内にブレークポイントを設定して、そのポリシーまで直接ステップ実行します。 

**[変数]** パネルで、システムに作成された変数とユーザーが作成した変数の値を調べることができます。 **[ブレークポイント]** パネルでは、設定されているすべてのブレークポイントの一覧を表示できます。 **[コールスタック]** パネルでは、現在有効なポリシーのスコープを表示できます。 

ポリシー実行中にエラーが発生した場合は、それが発生したポリシーの位置にエラーの詳細が表示されます。 

![exceptions](media/api-management-debug-policies/exception.png)

> [!TIP]
> 完了したら忘れずに、 **[停止]** ボタンをクリックしてデバッグ セッションを終了してください。


## <a name="next-steps"></a>次の手順

+ [Visual Studio Code 用の API Management 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement)の詳細を確認します。 
+ [GitHub リポジトリ](https://github.com/Microsoft/vscode-apimanagement)で問題を報告します

