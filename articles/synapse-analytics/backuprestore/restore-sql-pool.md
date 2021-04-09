---
title: 既存の専用 SQL プールを復元する
description: 既存の専用 SQL プールを復元するための攻略ガイド。
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 10/29/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 86150107273b247bef2c74e5b8c5272d7148587e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94331999"
---
# <a name="restore-an-existing-dedicated-sql-pool"></a>既存の専用 SQL プールを復元する

この記事では、Azure portal と Synapse Studio を使用して Azure Synapse Analytics で既存の専用 SQL プールを復元する方法について説明します。 この記事は、復元と geo リストアの両方に適用されます。 

## <a name="restore-an-existing-dedicated-sql-pool-through-the-synapse-studio"></a>Synapse Studio を使用して既存の専用 SQL プールを復元する

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. Synapse ワークスペースに移動します。 
3. [はじめに] -> [Synapse Studio を開く] で、 **[開く]** を選択します。

    ![ Synapse Studio](../media/sql-pools/open-synapse-studio.png)
4. 左側のナビゲーション ウィンドウで、 **[データ]** を選択します。
5. **[プールを管理する]** を選択します。 
6. **[+ 新規]** を選択して、新しい専用 SQL プールを作成します。 
7. [追加設定] タブで、復元する [復元ポイント] を選択します。 

    geo リストアを実行する場合は、回復するワークスペースと専用 SQL プールを選択します。 

8. **[自動復元ポイント]** または **[ユーザー定義の復元ポイント]** を選択します。 

    ![復元ポイント](../media/sql-pools/restore-point.PNG)

    専用 SQL プールに自動復元ポイントがない場合は、数時間待つか、復元する前にユーザー定義の復元ポイントを作成します。 ユーザー定義の復元ポイントを使用する場合は、既存のものを選択するか、新しく作成します。

    geo バックアップを復元する場合は、復元するソース リージョンにあるワークスペースと専用 SQL プールを選択するだけです。 

9. **[確認および作成]** を選択します。

## <a name="restore-an-existing-dedicated-sql-pool-through-the-azure-portal"></a>Azure portal を使用して既存の専用 SQL プールを復元する

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. 復元する元の専用 SQL プールに移動します。
3. [概要] ブレードの上部にある **[復元]** を選択します。

    ![ 復元の概要](../media/sql-pools/restore-sqlpool-01.png)

4. **[自動復元ポイント]** または **[ユーザー定義の復元ポイント]** を選択します。 

    専用 SQL プールに自動復元ポイントがない場合は、数時間待つか、復元する前にユーザー定義の復元ポイントを作成します。 

    geo リストアを実行する場合は、回復するワークスペースと専用 SQL プールを選択します。 

5. **[確認および作成]** を選択します。

## <a name="next-steps"></a>次の手順

- [復元ポイントを作成します](sqlpool-create-restore-point.md)
