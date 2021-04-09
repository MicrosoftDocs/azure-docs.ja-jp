---
title: Azure Application Insights からのアラートの送信 | Microsoft Docs
description: Azure Application Insights を使用して、アプリケーションのエラーに応じてアラートを送信するためのチュートリアルです。
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 04/10/2019
ms.custom: mvc
ms.openlocfilehash: 6b8cc951301ec315f758478eda1c0736882e66f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100597726"
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>Azure Application Insights でアプリケーションの正常性を監視してアラートを送信する

Azure Application Insights を使用すると、ご利用のアプリケーションを監視して、アプリケーションを使用できない、障害が発生している、またはパフォーマンスに問題がある場合にアラートを送信できます。  このチュートリアルでは、アプリケーションの可用性を継続的にチェックするテストを作成する方法を説明します。

学習内容は次のとおりです。

> [!div class="checklist"]
> * アプリケーションの応答を継続的にチェックする可用性テストを作成する
> * 問題が発生したときに管理者にメールを送信する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

[Application Insights のリソース](../app/create-new-resource.md)を作成します。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal [https://portal.azure.com](https://portal.azure.com) にサインインします。

## <a name="create-availability-test"></a>可用性テストを作成する

Application Insights の可用性テストを使用して、世界中のさまざまな場所から、ご利用のアプリケーションを自動的にテストすることができます。   このチュートリアルでは、ご使用の Web アプリケーションが使用できるかどうかを確認するために URL テストを実行します。  アプリケーションの操作を詳細にテストするための完全なチュートリアルを作成することもできます。 

1. **[Application Insights]** を選択してから、ご利用のサブスクリプションを選択します。  

2. **[調査]** メニューの **[可用性]** を選択して、**[テストの作成]** をクリックします。

    ![可用性テストを追加する](media/tutorial-alert/add-test-001.png)

3. テストの名前を入力し、その他は既定値のままにします。  この選択により、5 つの異なる地域から 5 分ごとにアプリケーション URL の要求がトリガーされます。

4. **[アラート]** を選択して **[アラート]** ドロップダウンを開きます。ここでは、テストが失敗した場合の応答方法の詳細を定義できます。 **[Near-realtime]\(準リアルタイム\)** を選択し、状態を **[有効]** に設定します。

    アラートの条件を満たした場合のメールの送信先となるメール アドレスを入力します。  Webhook のアドレスを入力して、アラートの条件を満たした場合の連絡先を入力することもできます (任意)。

    ![テストを作成する](media/tutorial-alert/create-test-001.png)

5. テスト パネルに戻り、省略記号を選択してアラートを編集し、準リアルタイム アラートの構成を入力します。

    ![アラートの編集](media/tutorial-alert/edit-alert-001.png)

6. 失敗した場所を 3 以上に設定します。 アラートしきい値に違反したときに通知を受けるユーザーを構成するために[アクション グループ](../alerts/action-groups.md)を作成します。

    ![アラートの UI の保存](media/tutorial-alert/save-alert-001.png)

7. アラートを構成したら、テストの名前をクリックして、各場所の詳細を表示します。 テストは折れ線グラフと散布図の両方の形式で表示でき、特定の時間範囲における成功/失敗が視覚化されます。

    ![テストの詳細](media/tutorial-alert/test-details-001.png)

8. 散布図の各点をクリックすると、任意のテストの詳細にドリルダウンできます。 これにより、エンドツーエンドのトランザクションの詳細ビューが起動します。 次の例では、失敗した要求の詳細が表示されています。

    ![テスト結果](media/tutorial-alert/test-result-001.png)
  
## <a name="next-steps"></a>次のステップ

これで問題が生じた場合のアラートの送信方法について学習できました。次のチュートリアルでは、ユーザーがどのようにアプリケーションを使用しているかを分析する方法を説明します。

> [!div class="nextstepaction"]
> [ユーザーについての理解](./tutorial-users.md)

