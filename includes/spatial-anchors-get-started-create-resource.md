---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: dc8d3c2d400204f53b05bb5536af95679541f3f6
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305198"
---
## <a name="create-a-spatial-anchors-resource"></a>Spatial Anchors リソースを作成する

<a href="https://portal.azure.com" target="_blank">Azure ポータル</a>にアクセスします。

Azure portal の左側のナビゲーション ウィンドウで、**[リソースの作成]** を選択します。

検索ボックスを使用して、「**Spatial Anchors**」を検索します。

   ![Spatial Anchors の検索](./media/spatial-anchors-get-started-create-resource/portal-search.png)

**[Spatial Anchors]** を選択します。 ダイアログ ボックスで **[作成]** を選択します。

**[Spatial Anchors アカウント]** ダイアログ ボックスで以下を行います。

- 一意のリソース名を入力します。
- リソースをアタッチするサブスクリプションを選択します。
- **[新規作成]** を選択して、リソース グループを作成します。 「**myResourceGroup**」と名前を付け、**[OK]** を選択します。
      [!INCLUDE [resource group intro text](resource-group.md)]
- リソースを配置する場所 (リージョン) を選択します。
- **[新規]** を選択して、リソースの作成を開始します。

   ![リソースの作成](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

リソースが作成されたら、リソースのプロパティを確認できます。 リソースの **[アカウント ID]** 値は、後で必要になるため、テキスト エディターにコピーしておきます。

   ![リソースのプロパティ](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

**[設定]** で **[キー]** を選択します。 **[主キー]** の値をテキスト エディターにコピーします。 この値は `Account Key` です この情報は後で必要になります。

   ![アカウント キー](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
