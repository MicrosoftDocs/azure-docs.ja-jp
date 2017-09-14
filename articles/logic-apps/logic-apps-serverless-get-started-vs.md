---
title: "Visual Studio でのサーバーレス アプリの構築 | Microsoft Docs"
description: "Visual Studio でのアプリの作成、デプロイ、および管理に関するこのガイドの説明を使用して、最初のサーバーレス アプリの操作を開始してください。"
keywords: 
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: LADocs; jehollan
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: 3672beda8a502e5fe2c8182076a8edef7ee9ebf6
ms.contentlocale: ja-jp
ms.lasthandoff: 06/02/2017

---
# <a name="build-a-serverless-app-in-visual-studio-with-logic-apps-and-functions"></a>ロジック アプリと関数を使用して Visual Studio でサーバーレス アプリを構築する

Azure のサーバーレス ツールおよび機能により、クラウド アプリケーションの迅速な開発とデプロイが可能になります。  このドキュメントは、Visual Studio でサーバーレス アプリケーションの構築を開始する方法に重点を置いています。  Azure でのサーバーレスの概要については、[この記事を参照してください](logic-apps-serverless-overview.md)。

## <a name="getting-everything-ready"></a>すべての準備

Visual Studio からサーバーレス アプリケーションを構築するために必要な前提条件を次に示します。

* [Visual Studio 2017](https://www.visualstudio.com/vs/) または Visual Studio 2015 - Community、Professional、または Enterprise
* [Visual Studio 向けロジック アプリ ツール](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551)
* [最新の Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 以上)
* [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)
* ローカルで関数をデバッグするための [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools)
* 組み込みのロジック アプリ デザイナー使用時の Web へのアクセス

## <a name="getting-started-with-a-deployment-template"></a>デプロイ テンプレートの概要

Azure でのリソースの管理は、リソース グループ内で実行されます。  リソース グループは、リソースの論理的なグループです。  リソース グループにより、リソースのコレクションのデプロイおよび管理が可能になります。  Azure のサーバーレス アプリケーションの場合、リソース グループには Azure Logic Apps と Azure Functions の両方が含まれています。  Visual Studio 内でリソース グループ プロジェクトを使用することによって、アプリケーション全体を 1 つの資産として開発、管理、およびデプロイできます。

### <a name="create-a-resource-group-project-in-visual-studio"></a>Visual Studio でのリソース グループ プロジェクトの作成

1. Visual Studio で、**[新しいプロジェクト]** をクリックして追加します
1. **[クラウド]** カテゴリで、Azure の **[リソース グループ]** プロジェクトを選択して作成します  
 * カテゴリまたはプロジェクトが一覧表示されない場合は、Azure SDK が Visual Studio 向けにインストールされていることを確認してください
1. プロジェクトに名前と場所を与え、**[OK]** を選択して、テンプレートを選択するための Visual Studio プロンプトを作成します。  空白から始めたり、ロジック アプリまたは他のリソースから始めたりすることも選択できます。  ただし、この場合は Azure クイック スタート テンプレートを使用して、サーバーレス アプリの操作を開始します。
1. **Azure クイック スタート**
    ![Azure クイック スタート テンプレートの選択][1]からテンプレートを表示することを選択する
1. サーバーレス クイック スタート テンプレート **101-logic-app-and-function-app** を選択し、**[OK]** をクリックします

このクイック スタート テンプレートにより、リソース グループ プロジェクト内にデプロイ テンプレートが作成されます。  このテンプレートには、Azure Functions を呼び出して結果を返す単純なロジック アプリが含まれています。  ソリューション エクスプローラーで `azuredeploy.json` ファイルを開いた場合は、サーバーレス アプリのリソースを確認できます。

## <a name="deploying-the-serverless-application"></a>サーバーレス アプリケーションのデプロイ

Visual Studio でロジック アプリ ビジュアル デザイナーを開くには、事前にデプロイされた Azure リソース グループが存在する必要があります。  これにより、デザイナーは、ロジック アプリ内のリソースやサービスへの接続を作成して使用できます。  開始するために必要なことは、作成されたソリューションのデプロイだけです。

1. Visual Studio でプロジェクトを右クリックし、**[デプロイ]** を選択して **[新規]** デプロイを作成します  ![新しいリソースのデプロイの選択][2]
1. 有効な Azure サブスクリプションとリソース グループを選択する
1. ソリューションを **[デプロイ]** することを選択する
1. ロジック アプリと Azure 関数アプリの名前を入力します。  Azure 関数名は、グローバルに一意である必要があります

サーバーレス ソリューションが指定されたリソース グループにデプロイされます。  Visual Studio で **[出力]** を表示した場合は、デプロイの状態を確認できます。

## <a name="editing-the-logic-app-in-visual-studio"></a>Visual Studio でのロジック アプリの編集

ソリューションがいずれかのリソース グループにデプロイされたら、ビジュアル デザイナーを使用してロジック アプリを編集したり変更したりできます。

1. ソリューション エクスプローラーで `azuredeploy.json` ファイルを右クリックし、**[Open With Logic Apps Designer] \(Logic Apps デザイナーで開く)** を選択します
1. ソリューションがデプロイされた **[リソース グループ]** と **[場所]** を選択し、**[OK]** を選択します

ロジック アプリ ビジュアル デザイナーが Visual Studio に表示されるようになりました。  引き続き手順を追加したり、ワークフローを変更したり、変更を保存したりできます。  また、Visual Studio からロジック アプリを作成することもできます。  テンプレート ナビゲーターで **[リソース]** を右クリックすると、プロジェクトに **[ロジック アプリ]** を追加することを選択できます。  空のロジック アプリは、リソース グループに事前にデプロイされることなくビジュアル デザイナーに読み込まれます。

### <a name="managing-and-viewing-run-history-for-a-deployed-logic-app"></a>デプロイされたロジック アプリの実行履歴の管理および表示

Azure でデプロイされたロジック アプリの実行履歴を管理および表示することもできます。  Visual Studio で **Cloud Explorer** ツールを開いた場合は、任意のロジック アプリを右クリックして、編集、無効化、プロパティの表示、または実行履歴の表示を選択できます。  [編集] をクリックすると、発行されたロジック アプリを Visual Studio リソース グループ プロジェクトにダウンロードすることもできます。  つまり、Azure Portal でロジック アプリの構築を開始した場合でも、引き続きそれをインポートして Visual Studio から管理できます。

## <a name="developing-an-azure-function-in-visual-studio"></a>Visual Studio での Azure 関数の開発

デプロイ テンプレートは、`azuredeploy.json` 変数で指定された Git リポジトリのソリューションに含まれているすべての Azure Functions をデプロイします。  ソリューション内で関数プロジェクトを作成した場合は、それをソース管理 (GitHub、Visual Studio Team Services など) にチェックインし、`repo` 変数を更新すると、テンプレートが Azure 関数をデプロイします。

### <a name="creating-an-azure-function-project"></a>Azure 関数プロジェクトの作成

JavaScript、Python、F#、Bash、Batch、または PowerShell を使用している場合は、[Functions CLI の手順](../azure-functions/functions-run-local.md)に従ってプロジェクトを作成します。  C# で関数を開発している場合は、Azure Functions の現在のソリューションで [C# クラス ライブラリ](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/)を使用できます。

## <a name="next-steps"></a>次のステップ

* [サーバーレス ソーシャル ダッシュボードを構築する方法を学習する](logic-apps-scenario-social-serverless.md)
* [Visual Studio Cloud Explorer からロジック アプリを管理する](logic-apps-manage-from-vs.md)
* [ロジック アプリのワークフロー定義言語](logic-apps-workflow-definition-language.md)

<!-- Image references -->
[1]: ./media/logic-apps-serverless-get-started-vs/select-template.png
[2]: ./media/logic-apps-serverless-get-started-vs/deploy.png

