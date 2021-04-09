---
title: インクルード ファイル
description: インクルード ファイル
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 04/16/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 8833bb75f5f50372deda3e71c1df90bed9a20054
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996350"
---
## <a name="enable-event-grid-resource-provider"></a>Event Grid リソース プロバイダーを有効にする

Azure サブスクリプションで Event Grid を使用したことがない場合は、Event Grid リソース プロバイダーを登録する必要がある可能性があります。

Azure Portal で次の操作を行います。

1. 左メニューの **[サブスクリプション]** を選択します。
1. Event Grid に使用するサブスクリプションを選択します。
1. 左側のメニューの **[設定]** で、 **[リソース プロバイダー]** を選択します。
1. **Microsoft.EventGrid** を探します。
1. 登録されていない場合は、 **[登録]** を選択します。 

登録完了まで少し時間がかかることがあります。 **[最新の情報に更新]** を選択して、状態を更新します。 **[状態]** が **[登録済み]** に になったら、次に進めることができます。