---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/26/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: aa9a715fdafc143a116458691965087b016dec1f
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83343356"
---
関数アプリで現在使用されているランタイム バージョンを表示して更新するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) で、関数アプリに移動します。

1. **[設定]** で **[構成]** を選択します。 **[関数のランタイム設定]** タブで、 **[ランタイム バージョン]** を探します。 特定のランタイム バージョンに注目してください。 以下の例では、バージョンが `~3` に設定されています。

    :::image type="content" source="./media/functions-view-update-version-portal/functions-view-runtime-version.png" alt-text="ランタイム バージョンを表示します。" border="true":::

1. バージョン 1.x ランタイムに関数アプリを固定するには、 **[ランタイム バージョン]** で **[~1]** を選択します。 アプリに関数がある場合、このスイッチは無効です。

1. ランタイム バージョンを変更したときは、 **[概要]** タブに戻り、 **[再起動]** を選択してアプリを再起動します。  関数アプリがバージョン 1.x ランタイムでの実行を再開します。関数を作成するときは、バージョン 1.x のテンプレートを使用します。

    :::image type="content" source="./media/functions-view-update-version-portal/functions-restart-function-app.png" alt-text="関数アプリを再起動します。" border="true":::
