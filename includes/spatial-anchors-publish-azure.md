---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 0dab71b6d169e26a3d7dc208dd09efe1143fbe13
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305214"
---
### <a name="open-the-publish-wizard"></a>発行ウィザードを開く

**ソリューション エクスプローラー**で、**SharingService** プロジェクトを右クリックし、**[発行]** を選択します。

発行ウィザードが起動します。 **[App Service]** > **[発行]** の順に選択し、**[App Service の作成]** ダイアログ ボックスを開きます。

### <a name="sign-in-to-azure"></a>Azure へのサインイン

**[App Service の作成]** ダイアログ ボックスで **[アカウントの追加]** を選択し、Azure サブスクリプションにサインインします。 既にサインインしている場合、ドロップダウン リストからアカウントを選択します。

> [!NOTE]
> 既にサインインしている場合は、まだ **[作成]** を選択しないでください。
>

### <a name="create-a-resource-group"></a>リソース グループの作成

[!INCLUDE [resource group intro text](resource-group.md)]

**[リソース グループ]** の横にある **[新規]** をクリックします。

リソース グループに **myResourceGroup** という名前を付けて、**[OK]** をクリックします。

### <a name="create-an-app-service-plan"></a>App Service プランを作成する

[!INCLUDE [app-service-plan](app-service-plan.md)]

**[ホスティング プラン]** の隣にある **[新規]** を選択します。

**[ホスティング プランの構成]** ダイアログ ボックスで、以下の設定を使用します。

| Setting | 推奨値 | 説明 |
|-|-|-|
|App Service プラン| MySharingServicePlan | App Service プランの名前です。 |
| Location | 米国西部 | Web アプリがホストされているデータ センターです。 |
| Size | 無料 | [価格レベル](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)によって、ホスティング機能が決まります。 |

**[OK]** を選択します。

### <a name="create-and-publish-the-web-app"></a>Web アプリを作成して発行する

**[アプリ名]** に一意のアプリ名 (有効な文字は `a-z`、`0-9`、`-`) を入力するか、自動的に生成された一意の名前をそのまま使用します。 Web アプリの URL は `https://<app_name>.azurewebsites.net` です。`<app_name>` には自分のアプリの名前を指定します。

**[作成]** をクリックして、Azure リソースの作成を開始します。

ウィザードの完了後に、Azure に ASP.NET Core Web アプリを発行してから、既定のブラウザーでアプリを開きます。

![Azure で発行された ASP.NET Web アプリ](./media/spatial-anchors-azure/web-app-running-live.png)

このセクションで使用したアプリ名は、形式 `https://<app_name>.azurewebsites.net` の URL プレフィックスとして使用されます。 後で必要になるため、この URL はメモしておきます。
