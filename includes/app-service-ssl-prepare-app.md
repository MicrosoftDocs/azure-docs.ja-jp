---
title: インクルード ファイル
description: インクルード ファイル
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 10/15/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: ecdd419331c88e712644851f9213861f882cf0f6
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/13/2018
ms.locfileid: "53381563"
---
## <a name="prepare-your-web-app"></a>Web アプリの準備

カスタム SSL 証明書 (サードパーティ証明書または App Service 証明書) を Web アプリにバインドするには、[App Service プラン](https://azure.microsoft.com/pricing/details/app-service/)が **Basic**、**Standard**、**Premium**、**Isolated** のいずれかのレベルである必要があります。 この手順では、Web アプリが、サポートされている価格レベルであることを確認します。

### <a name="log-in-to-azure"></a>Azure にログインする

[Azure Portal](https://portal.azure.com)を開きます。

### <a name="navigate-to-your-web-app"></a>Web アプリに移動する

左側のメニューで **[App Services]** をクリックした後、Web アプリの名前をクリックします。

![Web アプリの選択](./media/app-service-ssl-prepare-app/select-app.png)

Web アプリの管理ページが表示されます。  

### <a name="check-the-pricing-tier"></a>価格レベルの確認

Web アプリ ページの左側のナビゲーションで **[設定]** セクションまでスクロールし、**[スケール アップ (App Service のプラン)]** を選択します。

![スケール アップ メニュー](./media/app-service-ssl-prepare-app/scale-up-menu.png)

Web アプリが **F1** レベルまたは **D1** レベルに含まれていないことを確認します。 Web アプリの現在のレベルは、ダーク ブルーのボックスで強調表示されます。

![価格レベルの確認](./media/app-service-ssl-prepare-app/check-pricing-tier.png)

カスタム SSL は、**F1** レベルまたは **D1** レベルではサポートされていません。 スケール アップする必要がある場合は、次のセクションの手順に従います。 それ以外の場合は、**[スケール アップ]** ページを閉じて、「[App Service プランのスケール アップ](#scale-up-your-app-service-plan)」セクションはスキップしてください。

### <a name="scale-up-your-app-service-plan"></a>App Service プランのスケール アップ

非 Free レベルのいずれかを選びます (**B1**、**B2**、**B3**、または**運用**カテゴリのいずれかのレベル)。 その他のオプションについては、**[See additional options]\(その他のオプションを参照する\)** をクリックします。

**[Apply]** をクリックします。

![価格レベルの選択](./media/app-service-ssl-prepare-app/choose-pricing-tier.png)

次の通知が表示されたら、スケール操作は完了です。

![スケール アップの通知](./media/app-service-ssl-prepare-app/scale-notification.png)

