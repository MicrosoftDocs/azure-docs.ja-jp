---
title: 統合ランタイムの作成および管理
description: この記事では、Azure Purview で統合ランタイムを作成して管理する手順について説明します。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 02/03/2021
ms.openlocfilehash: 9276f845c95b5e736180159b282ddedc33523c17
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99980747"
---
# <a name="create-and-manage-a-self-hosted-integration-runtime"></a>セルフホステッド統合ランタイムの作成および管理

この記事では、Azure Purview でデータ ソースのスキャンを行うことができるセルフホステッド統合ランタイム (SHIR) を作成および管理する方法について説明します。

> [!NOTE]
> Purview Integration Runtime は、同じマシン上の Azure Synapse Analytics または Azure Data Factory Integration Runtime と共有することはできません。 別のマシンにインストールする必要があります。

## <a name="create-a-self-hosted-integration-runtime"></a>自己ホスト型統合ランタイムを作成する

1. Purview Studio のホーム ページの左側のナビゲーション ウィンドウで、 **[管理センター]** を選択します。

2. 左側のウィンドウの **[Sources and scanning]\(ソースとスキャン\)** で **[統合ランタイム]** を選択し、 **[+ 新規]** を選択します。

   :::image type="content" source="media/manage-integration-runtimes/select-integration-runtimes.png" alt-text="IR をクリックします。":::

3. **[統合ランタイムのセットアップ]** ページで **[セルフホステッド]** を選択してセルフホステッド IR を作成し、 **[続行]** を選択します。

   :::image type="content" source="media/manage-integration-runtimes/select-self-hosted-ir.png" alt-text="新しい SHIR を作成します。":::

4. IR の名前を入力し、 [作成] を選択します。

5. **[統合ランタイムのセットアップ]** ページで、 **[手動セットアップ]** セクションに記載されている手順に従います。 統合ランタイムは、ダウンロード サイトから、それを実行する VM またはマシンにダウンロードする必要があります。

   :::image type="content" source="media/manage-integration-runtimes/integration-runtime-settings.png" alt-text="キーの取得":::

   - 認証キーをコピーして貼り付けます。

   - セルフホステッド統合ランタイムを、「[Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717)」からローカルの Windows マシンにダウンロードします。 インストーラーを実行します。

   - **[Integration Runtime (セルフホステッド) の登録]** ページで、前に保存した 2 つのキーのいずれかを貼り付け、 **[登録]** を選択します。

     :::image type="content" source="media/manage-integration-runtimes/register-integration-runtime.png" alt-text="キーの入力":::

   - **[新しい統合ランタイム (セルフホステッド) ノード]** ページで **[完了]** を選択します。

6. セルフホステッド統合ランタイムが正常に登録されると、次のウィンドウが表示されます。

   :::image type="content" source="media/manage-integration-runtimes/successfully-registered.png" alt-text="正常に登録":::

## <a name="manage-a-self-hosted-integration-runtime"></a>セルフホステッド統合ランタイムの管理

セルフホステッド統合ランタイムを編集するには、**管理センター** の **[統合ランタイム]** に移動し、IR を選択して [編集] をクリックします。 これで、説明の更新、キーのコピー、新しいキーの再生成ができるようになります。

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime.png" alt-text="IR の編集":::

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime-settings.png" alt-text="IR の詳細の編集":::

セルフホステッド統合ランタイムを削除するには、**管理センター** の **[統合ランタイム]** に移動し、IR を選択して [削除] をクリックします。 IR が削除されると、それに依存している実行中のスキャンは失敗します。

## <a name="next-steps"></a>次のステップ

[削除されたアセットをスキャンが検出する方法](concept-detect-deleted-assets.md)
