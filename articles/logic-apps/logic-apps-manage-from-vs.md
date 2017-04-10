---
title: "Visual Studio でロジック アプリを管理する - Azure Logic Apps | Microsoft Docs"
description: "Visual Studio Cloud Explorer でロジック アプリとその他の Azure 資産を管理する"
author: klam
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 12/19/2016
ms.author: klam
translationtype: Human Translation
ms.sourcegitcommit: 2fafb3c127183c4903ed7ee0da5811988db9116c
ms.openlocfilehash: 9a542f83eb35b159de04f0395cd850376da498de
ms.lasthandoff: 03/02/2017

---

# <a name="manage-your-logic-apps-with-visual-studio-cloud-explorer"></a>Visual Studio Cloud Explorer でロジック アプリを管理する

[Azure Portal](https://portal.azure.com/) には Azure Logic Apps を設計・管理するための強力な機能が用意されていますが、Visual Studio Cloud Explorer を使用すれば、ロジック アプリをはじめとする多くの Azure 資産を Visual Studio で管理できます。 発行済みのロジック アプリを参照して、有効/無効の切り替えや、実行履歴の編集、表示など、さまざまなタスクを実行できます。 

## <a name="installation-steps"></a>インストール手順

Azure Logic Apps 用の Visual Studio ツールをインストールして構成するには、次の手順のようにします。

### <a name="prerequisites"></a>前提条件

* [Visual Studio 2015 または Visual Studio 2017](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* [最新の Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 以上)
* [Visual Studio Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015)
* Web アクセス (組み込みデザイナー使用時)

### <a name="install-visual-studio-tools-for-logic-apps"></a>Logic Apps 用 Visual Studio ツールのインストール

前提条件をインストールした後、次のようにします。

1. Visual Studio を開きます。 **[ツール]** メニューの **[拡張機能と更新プログラム]** を選びます。
2. オンラインで検索できるように **[オンライン]** カテゴリを展開します。
3. **Azure Logic Apps Tools for Visual Studio** が見つかるまで、「**Logic Apps**」を参照または検索します。
4. 拡張機能をダウンロードしてインストールするには、**[ダウンロード]** をクリックします。
5. インストール後、Visual Studio を再起動します。

> [!NOTE]
> [Visual Studio Marketplace](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9) から、Azure Logic Apps Tools for Visual Studio を直接ダウンロードすることもできます。

## <a name="browse-for-logic-apps-in-cloud-explorer"></a>Cloud Explorer でロジック アプリを参照する

1.    Cloud Explorer を開くには、**[表示]** メニューの **[Cloud Explorer]** を選択します。
2.    リソース グループまたはリソースの種類のいずれかでロジック アプリを参照します。 

    リソース タイプで参照する場合は、Azure サブスクリプションを選択し、[Logic Apps] セクションを展開してロジック アプリを&1; つ選択します。 
    ロジック アプリを右クリックするか、Cloud Explorer の下部にある **[アクション]** メニューから選択できます。

    ![ロジック アプリを参照する](./media/logic-apps-manage-from-vs/browse.png)

## <a name="edit-your-logic-app-with-logic-app-designer"></a>ロジック アプリ デザイナーでロジック アプリを編集する

現在デプロイされているロジック アプリを、Azure Portal で使用するのと同じデザイナーで開くには、ロジック アプリを右クリックし、**[Logic App Editor で開く]** を選択します。 

デザイナーでロジック アプリを編集し、更新内容をクラウドに保存したら、**[トリガーの実行]** を選択して新しい実行を開始できます。

![ロジック アプリ デザイナー](./media/logic-apps-manage-from-vs/designer.png)

## <a name="browse-your-logic-app-run-history"></a>ロジック アプリの実行履歴を参照する

ロジック アプリの実行履歴を表示するには、ロジック アプリを右クリックし、**[実行履歴を開く]** を選択します。 表示されているプロパティのいずれかを基準にして実行履歴の順序を変更するには、列ヘッダーを選択します。

![実行履歴](media/logic-apps-manage-from-vs/runs.png)

インスタンスの実行履歴を表示し、各ステップの入力と出力を含めた実行結果を確認するには、いずれかの実行インスタンスをダブルクリックします。

![実行履歴の結果 (ステップの入力と出力)](./media/logic-apps-manage-from-vs/history.png)

## <a name="next-steps"></a>次のステップ

*    Azure Logic Apps の概要については、[Azure Portal で最初にロジック アプリを作成する方法](logic-apps-create-a-logic-app.md)に関するトピックをご覧ください
* [Visual Studio で Azure Logic Apps を作成してデプロイする](logic-apps-deploy-from-vs.md)
* [一般的な例とシナリオを確認する](logic-apps-examples-and-scenarios.md)
* [Azure Logic Apps を使ってビジネス プロセスを自動化する方法を学習する](http://channel9.msdn.com/Events/Build/2016/T694)
* [Azure Logic Apps を使ってシステムを統合する方法を学習する](http://channel9.msdn.com/Events/Build/2016/P462)

