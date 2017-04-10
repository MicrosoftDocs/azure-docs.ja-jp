---
title: "Visual Studio でロジック アプリを作成してデプロイする - Azure Logic Apps | Microsoft Docs"
description: "Visual Studio プロジェクトを作成して、Azure Logic Apps を設計、作成、デプロイします。"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: e484e5ce-77e9-4fa9-bcbe-f851b4eb42a6
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 2/14/2017
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 5dfff8620d66eca0d54763efbc5702e4b4d122d1
ms.lasthandoff: 03/14/2017

---

# <a name="design-build-and-deploy-azure-logic-apps-in-visual-studio"></a>Visual Studio で Azure Logic Apps を作成してデプロイする

[Azure Portal](https://portal.azure.com/) には Azure Logic Apps を作成および管理するための優れた機能がありますが、Logic Apps の設計、作成、およびデプロイには、Visual Studio を使う方がよい場合があります。 Visual Studio にはロジック アプリ デザイナーなどの高度なツールが用意されており、ユーザーはそれらを使ってロジック アプリを作成し、デプロイ テンプレートと自動化テンプレートを構成して、任意の環境にデプロイすることができます。 

Azure Logic Apps の概要については、[Azure Portal で最初にロジック アプリを作成する方法](logic-apps-create-a-logic-app.md)に関するトピックをご覧ください。

## <a name="installation-steps"></a>インストール手順

Azure Logic Apps 用の Visual Studio ツールをインストールして構成するには、次の手順のようにします。

### <a name="prerequisites"></a>前提条件

* [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* [最新の Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 以上)
* [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)
* Web アクセス (組み込みデザイナー使用時)

### <a name="install-visual-studio-tools-for-azure-logic-apps"></a>Azure Logic Apps 用 Visual Studio ツールをインストールする

前提条件をインストールした後、次のようにします。

1. Visual Studio を開きます。 **[ツール]** メニューの **[拡張機能と更新プログラム]** を選びます。
2. オンラインで検索できるように **[オンライン]** カテゴリを展開します。
3. **Azure Logic Apps Tools for Visual Studio** が見つかるまで、「**Logic Apps**」を参照または検索します。
4. 拡張機能をダウンロードしてインストールするには、**[ダウンロード]** をクリックします。
5. インストール後、Visual Studio を再起動します。

> [!NOTE]
> [Visual Studio Marketplace](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9) から、Azure Logic Apps Tools for Visual Studio を直接ダウンロードすることもできます。

インストールが終了すると、ロジック アプリ デザイナーで Azure リソース グループ プロジェクトを使用できる状態になります。

## <a name="create-your-project"></a>プロジェクトを作成する

1. **[ファイル]** メニューの **[新規作成]** で **[プロジェクト]** を選びます。 または、プロジェクトを既存のソリューションに追加するには、**[追加]** で **[新しいプロジェクト]** を選びます。

    ![[ファイル] メニュー](./media/logic-apps-deploy-from-vs/filemenu.png)

2. **[新しいプロジェクト]** ダイアログ ボックスで、**[クラウド]** を見つけて、**[Azure リソース グループ]** を選びます。 プロジェクトに名前を付けて、**[OK]** をクリックします。

    ![新しいプロジェクトの追加](./media/logic-apps-deploy-from-vs/addnewproject.png)

3. 空のロジック アプリ デプロイ テンプレートを作成する **[ロジック アプリ]** テンプレートを選びます。 テンプレートを選んだ後、**[OK]** をクリックします。

    ![ロジック アプリ テンプレートを選ぶ](./media/logic-apps-deploy-from-vs/selectazuretemplate1.png)

    これで、ロジック アプリ プロジェクトがソリューションに追加されました。 
    ソリューション エクスプローラーにデプロイ ファイルが表示されます。

    ![デプロイ ファイル](./media/logic-apps-deploy-from-vs/deployment.png)

## <a name="create-your-logic-app-with-logic-app-designer"></a>ロジック アプリ デザイナーでロジック アプリを作成する

ロジック アプリを含む Azure リソース グループ プロジェクトが用意できたら、Visual Studio でロジック アプリ デザイナーを開いてワークフローを作成できます。 

> [!NOTE]
> デザイナーでは、使用可能なプロパティとデータをコネクタで照会するために、インターネット接続が必要です。 たとえば、Dynamics CRM Online コネクタを使っている場合、デザイナは CRM インスタンスを照会して、使用可能なカスタム プロパティと既定のプロパティを表示します。

1. `<template>.json` ファイルを右クリックし、**[ロジック アプリ デザイナーで開く]** を選びます。 (`Ctrl+L`)

2. Azure サブスクリプション、リソース グループ、デプロイ テンプレートの場所を選びます。

    > [!NOTE]
    > ロジック アプリを設計すると、設計時にプロパティを照会する API 接続リソースが作成されます。 Visual Studio はここで選んだリソース グループを使って、設計時にそれらの接続を作成します。 API 接続を表示または変更するには、Azure Portal に移動し、**[API 接続]** を参照します。

    ![Subscription Picker](./media/logic-apps-deploy-from-vs/designer_picker.png)

    デザイナーは、レンダリングのために `<template>.json` ファイル内の定義を使います。

4. ロジック アプリを作成して設計します。 デプロイ テンプレートが変更内容で更新されます。

    ![Visual Studio のロジック アプリ デザイナー](./media/logic-apps-deploy-from-vs/designer_in_vs.png)

Visual Studio によって、ロジック アプリが機能するために必要な接続のリソース ファイルに、`Microsoft.Web/connections` リソースが追加されます。 これらの接続プロパティはデプロイ時に設定できるほか、デプロイ後は、Azure Portal の **[API 接続]** で管理することができます。

### <a name="switch-to-json-code-view"></a>JSON コード ビューに切り替える

ロジック アプリの JSON 表現を表示するには、デザイナーの一番下にある **[コード ビュー]** タブを選びます。

再度フル リソースの JSON に切り替えるには、`<template>.json` ファイルを右クリックして **[開く]** を選びます。

### <a name="add-references-for-dependent-resources-to-visual-studio-deployment-templates"></a>依存リソースの参照を Visual Studio のデプロイ テンプレートに追加する

ロジック アプリで依存リソースを参照する場合は、[Azure Resource Manager テンプレート関数](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions)を、ロジック アプリのデプロイ テンプレートでパラメーターのように使うことができます。 たとえば、ロジック アプリと共にデプロイする Azure 関数または統合アカウントをロジック アプリで参照するような場合です。 ロジック アプリ デザイナーが正しく記述するように、デプロイ テンプレートでのパラメーターの使い方についての次のガイドラインに従ってください。 

ロジック アプリ パラメーターは、次の種類のトリガーとアクションで使うことができます。

*   子ワークフロー
*   関数アプリ
*   APIM 呼び出し
*   API 接続ランタイム URL

また、以下に示す parameters、variables、resourceId、concat などのテンプレート関数を使うことができます。 たとえば、Azure 関数のリソース ID を置換する方法を次に示します。

```
"parameters":{
    "functionName": {
    "type":"string",
    "minLength":1,
    "defaultValue":"<FunctionName>"
    }
},
```

パラメーターは次のように使います。

```
"MyFunction": {
        "type": "Function",
        "inputs": {
        "body":{},
        "function":{
        "id":"[resourceid('Microsoft.Web/sites/functions','functionApp',parameters('functionName'))]"
        }
    },
    "runAfter":{}
}
```

> [!NOTE] 
> ロジック アプリ デザイナーでパラメーターを使うには、既定値を指定する必要があります。次はその例です。
> 
> ```
> "parameters": {
>     "IntegrationAccount": {
>     "type":"string",
>     "minLength":1,
>     "defaultValue":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Logic/integrationAccounts/<integrationAccountName>"
>     }
> },
> ```

### <a name="save-your-logic-app"></a>ロジック アプリを保存する

ロジック アプリを保存するには、**[ファイル]** > **[保存]** の順に選びます。 (`Ctrl+S`) 

アプリを保存するときにロジック アプリにエラーがある場合は、Visual Studio の **[出力]** ウィンドウに表示されます。

## <a name="deploy-your-logic-app-from-visual-studio"></a>Visual Studio からロジック アプリをデプロイする

アプリを構成した後は、いくつかの手順で Visual Studio から直接デプロイすることができます。 

1. ソリューション エクスプローラーでプロジェクトを右クリックし、**[配置]** > **[新しい配置...]** の順に選びます。

    ![新しいデプロイ](./media/logic-apps-deploy-from-vs/newdeployment.png)

2. 要求されたら、Azure サブスクリプションにサインインします。 

3. ロジック アプリをデプロイするリソース グループの詳細を選ぶ必要があります。 終了したら、**[配置]** を選びます。

    > [!NOTE]
    > リソース グループの適切なテンプレートとパラメーター ファイルを選んだことを確認します。 たとえば、運用環境にデプロイする場合は、運用パラメーター ファイルを選びます。

    ![リソース グループへのデプロイ](./media/logic-apps-deploy-from-vs/deploytoresourcegroup.png)

    デプロイの状態が、**[出力]** ウィンドウに表示されます。 
    **[出力元の表示]** の一覧で **[Azure のプロビジョニング]** を選ぶことが必要な場合があります。

    ![デプロイの状態の出力](./media/logic-apps-deploy-from-vs/output.png)

今後は、ソース管理でロジック アプリを編集し、Visual Studio を使って新しいバージョンをデプロイできます。

> [!NOTE]
> Azure Portal で定義を直接変更した場合は、次に Visual Studio からデプロイしたときに変更内容が上書きされます。 

## <a name="add-your-logic-app-to-an-existing-resource-group-project"></a>ロジック アプリを既存のリソース グループ プロジェクトに追加する

既存のリソース グループ プロジェクトがある場合は、[JSON アウトライン] ウィンドウでそのプロジェクトにロジック アプリを追加できます。 また、以前に作成したアプリと共に別のロジック アプリを追加することもできます。

1. `<template>.json` ファイルを開きます。

2. [JSON アウトライン] ウィンドウを開くには、**[表示]** > **[その他のウィンドウ]** > **[JSON アウトライン]** の順に選びます。

3. テンプレート ファイルにリソースを追加するには、[JSON アウトライン] ウィンドウの上部にある **[リソースの追加]** をクリックします。 または、[JSON アウトライン] ウィンドウで、**[リソース]** を右クリックして、**[新しいリソースの追加]** を選びます。

    ![[JSON アウトライン] ウィンドウ](./media/logic-apps-deploy-from-vs/jsonoutline.png)
    
4. **[リソースの追加]** ダイアログ ボックスで、**[ロジック アプリ]** を探して選びます。 ロジック アプリの名前を指定し、**[追加]** を選びます。

    ![リソースの追加](./media/logic-apps-deploy-from-vs/addresource.png)

## <a name="next-steps"></a>次のステップ

* [Visual Studio Cloud Explorer を使用して Azure Logic Apps を管理する](logic-apps-manage-from-vs.md)
* [一般的な例とシナリオを確認する](logic-apps-examples-and-scenarios.md)
* [Azure Logic Apps を使ってビジネス プロセスを自動化する方法を学習する](http://channel9.msdn.com/Events/Build/2016/T694)
* [Azure Logic Apps を使ってシステムを統合する方法を学習する](http://channel9.msdn.com/Events/Build/2016/P462)

