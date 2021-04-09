---
author: craigshoemaker
ms.service: static-web-apps
ms.topic: include
ms.date: 05/13/2020
ms.author: cshoe
ms.openlocfilehash: cb8f8388dd164f4fc17b81b9b39c6b5a058ca3ff
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "88752489"
---
## <a name="view-the-website"></a>Web サイトを表示する

静的アプリのデプロイには 2 つの側面があります。 まず、アプリを構成する基になる Azure リソースをプロビジョニングします。 2 つ目は、アプリケーションをビルドして発行する GitHub Actions ワークフローです。

新しい静的サイトに移動する前にまず、デプロイ ビルドの実行が完了している必要があります。

Static Web Apps の概要ウィンドウには、Web アプリとの対話に役立つ一連のリンクが表示されます。

:::image type="content" source="../articles/static-web-apps/media/getting-started/overview-window.png" alt-text="概要ウィンドウ":::

1. "_Click here to check the status of your GitHub Actions runs (こちらをクリックして、GitHub Actions の実行の状態を確認してください)_ " というバナーをクリックすると、リポジトリに対して実行されている GitHub アクションが表示されます。 デプロイ ジョブが完了したことを確認したら、生成された URL を使用して Web サイトに移動できます。

2. GitHub Actions ワークフローが完了したら、 _[URL]_ リンクをクリックして、新しいタブで Web サイトを開くことができます。
