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
ms.author: LADocs; klam
ms.translationtype: HT
ms.sourcegitcommit: 9633e79929329470c2def2b1d06d95994ab66e38
ms.openlocfilehash: a5bf24de1a7a2b6d4c1ae6416c95d83ef7506da3
ms.contentlocale: ja-jp
ms.lasthandoff: 08/04/2017

---

# <a name="manage-your-logic-apps-with-visual-studio-cloud-explorer"></a>Visual Studio Cloud Explorer でロジック アプリを管理する

[Azure Portal](https://portal.azure.com/) には Azure Logic Apps を設計および管理するための強力な機能が用意されていますが、Visual Studio Cloud Explorer を使用すれば、ロジック アプリをはじめとする多くの Azure 資産を管理できます。 Visual Studio Cloud Explorer を使用すると、発行済みのロジック アプリを参照、管理、編集、およびダウンロードできます。 管理タスクには、実行履歴の有効化、無効化、および実行が含まれます。 

Visual Studio でロジック アプリにアクセスして管理するには、まず Azure Logic Apps Tools for Visual Studio をインストールおよび構成します。 

## <a name="prerequisites"></a>前提条件

* [Visual Studio 2015 または Visual Studio 2017](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* [最新の Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 以上)
* [Visual Studio Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015)
* Web アクセス (組み込みデザイナー使用時)

## <a name="install-visual-studio-tools-for-logic-apps"></a>Logic Apps 用 Visual Studio ツールのインストール

前提条件をインストールしたら、Azure Logic Apps Tools for Visual Studio をダウンロードしてインストールする必要があります。

1. Visual Studio を開きます。 **[ツール]** メニューの **[拡張機能と更新プログラム]** を選びます。
2. Visual Studio ギャラリーでオンラインで検索できるように **[オンライン]** カテゴリを展開します。
3. **Azure Logic Apps Tools for Visual Studio** が見つかるまで、「**Logic Apps**」を参照または検索します。
4. 拡張機能をダウンロードしてインストールするには、**[ダウンロード]** をクリックします。
5. インストール後、Visual Studio を再起動します。

> [!NOTE]
> Azure Logic Apps Tools for Visual Studio を直接ダウンロードするには、[Visual Studio Marketplace](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9) に移動します。

## <a name="browse-for-logic-apps-in-cloud-explorer"></a>Cloud Explorer でロジック アプリを参照する

1.  Cloud Explorer を開くには、**[表示]** メニューの **[Cloud Explorer]** を選択します。
2.  リソース グループまたはリソースの種類のいずれかでロジック アプリを参照します。 

    * リソースの種類で参照する場合は、Azure サブスクリプションを選択し、**[Logic Apps]** セクションを展開して対象のロジック アプリを選択します。 
    * リソース グループで参照する場合は、対象のロジック アプリがあるリソース グループを展開し、対象のロジック アプリを選択します。

    対象のロジック アプリのコマンドを表示するには、対象のロジック アプリを右クリックするか Cloud Explorer の下部にある **[アクション]** メニューから選択します。

    ![ロジック アプリを参照する](./media/logic-apps-manage-from-vs/browse.png)

## <a name="edit-your-logic-app-with-logic-apps-designer"></a>Logic Apps デザイナーでロジック アプリを編集する

Cloud Explorer から、現在デプロイされているロジック アプリを Azure Portal で使用する同じデザイナーで開くことができます。 

* ロジック アプリを編集するには、Cloud Explorer で対象のロジック アプリを右クリックし、**[Logic App Editor で開く]** を選択します。 

* 更新プログラムをクラウドに発行するには、**[発行]** を選択します。 

* 新しい実行を開始するには、**[トリガーの実行]** を選択します。

![Logic Apps デザイナー](./media/logic-apps-manage-from-vs/designer.png)

デザイナーからロジック アプリを**ダウンロード**することもできます。 このアクションはロジック アプリの定義を自動的にパラメーター化し、その定義を Azure Resource Manager 展開テンプレートとして保存します。 この展開テンプレートは Azure Resource Group のプロジェクトに追加できます。

## <a name="browse-your-logic-app-run-history"></a>ロジック アプリの実行履歴を参照する

ロジック アプリの実行履歴を表示するには、ロジック アプリを右クリックし、**[実行履歴を開く]** を選択します。 表示されているプロパティのいずれかを基準にして実行履歴の順序を変更するには、列ヘッダーを選択します。

![実行履歴](media/logic-apps-manage-from-vs/runs.png)

インスタンスの実行履歴を表示し、各ステップの入力と出力を含めた実行結果を確認するには、いずれかの実行インスタンスをダブルクリックします。

![実行履歴の結果 (ステップの入力と出力)](./media/logic-apps-manage-from-vs/history.png)

## <a name="next-steps"></a>次のステップ

* [初めてのロジック アプリの作成](logic-apps-create-a-logic-app.md)
* [Visual Studio で Azure Logic Apps を作成してデプロイする](logic-apps-deploy-from-vs.md)
* [一般的な例とシナリオを確認する](logic-apps-examples-and-scenarios.md)
* [ビデオ: Azure Logic Apps を使用してビジネス プロセスを自動化する](http://channel9.msdn.com/Events/Build/2016/T694)
* [ビデオ: Azure Logic Apps を使用してシステムを統合する](http://channel9.msdn.com/Events/Build/2016/P462)

