---
title: ポータルを使用した Azure Event Grid サブスクリプション
description: ポータルから Event Grid サブスクリプションを作成する方法について説明します。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: spelluru
ms.openlocfilehash: b54bc52a2feaf4646d801265ddb273c2c86158ee
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2019
ms.locfileid: "54477908"
---
# <a name="subscribe-to-events-through-portal"></a>ポータルを使用したイベントのサブスクライブ

この記事では、ポータルを使用して Event Grid サブスクリプションを作成する方法について説明します。

## <a name="create-event-subscriptions"></a>イベント サブスクリプションの作成

サポートされている任意の[イベント ソース](event-sources.md)の Event Grid サブスクリプションを作成するには、次の手順に従います。 この記事では、Azure サブスクリプション用に Event Grid サブスクリプションを作成する方法を説明します。

1. **[すべてのサービス]** を選択します。

   ![[すべてのサービス] を選択する](./media/subscribe-through-portal/select-all-services.png)

1. "**Event Grid サブスクリプション**" を検索して、使用可能なオプションから選択します。

   ![Search](./media/subscribe-through-portal/search.png)

1. **[+ イベント サブスクリプション]** を選択します。

   ![[サブスクリプションの追加]](./media/subscribe-through-portal/add-subscription.png)

1. 作成するサブスクリプションの種類を選択します。 たとえば、ご使用の Azure サブスクリプションのイベントにサブスクライブするには、**[Azure サブスクリプション]** とターゲット サブスクリプションを選択します。

   ![Azure サブスクリプションの選択](./media/subscribe-through-portal/azure-subscription.png)

1. このイベント ソースのすべてのイベントの種類にサブスクライブするには、**[すべてのイベントの種類を購読します]** をオンのままにします。 それ以外の場合は、このサブスクリプションのイベントの種類を選択します。

   ![イベントの種類の選択](./media/subscribe-through-portal/select-event-types.png)

1. イベントを処理するためのエンドポイントやサブスクリプション名など、イベント サブスクリプションに関するその他の詳細情報を指定します。

   ![サブスクリプションの詳細の指定](./media/subscribe-through-portal/provide-subscription-details.png)

1. 配信不能レタリングを有効にして、再試行ポリシーをカスタマイズするには、**[追加の機能]** を選択します。

   ![追加機能を選択する](./media/subscribe-through-portal/select-additional-features.png)

1. 配信されないイベントの格納に使用するコンテナーを選択して、再試行の送信方法を設定します。

   ![配信不能レタリングと再試行を有効にする](./media/subscribe-through-portal/set-deadletter-retry.png)

1. 完了したら、**[作成]** を選択します。

## <a name="create-subscription-on-resource"></a>リソースでのサブスクリプションの作成

一部のイベント ソースでは、そのリソースのポータル インターフェイスを介してイベント サブスクリプションを作成できます。 イベント ソースを選択し、左側のウィンドウで**イベント**を探します。

![サブスクリプションの詳細の指定](./media/subscribe-through-portal/resource-events.png)

ポータルには、そのソースに関連するイベント サブスクリプションを作成するためのオプションが表示されます。

## <a name="next-steps"></a>次の手順

* イベント配信と再試行については、「[Event Grid によるメッセージ配信と再試行](delivery-and-retry.md)」をご覧ください。
* Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Event Grid の使用をすぐに開始するには、[Azure Event Grid でのカスタム イベントの作成とルーティング](custom-event-quickstart.md)に関する記事を参照してください。
