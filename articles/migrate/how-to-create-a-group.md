---
title: Azure Migrate でのアセスメントのためにマシンをグループ化する | Microsoft Docs
description: Azure Migrate サービスでアセスメントを実行する前にマシンをグループ化する方法について説明します。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/28/2018
ms.author: raynew
ms.openlocfilehash: c11d2f22fa08417107b0eecdd902b4521410b358
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2018
ms.locfileid: "53252424"
---
# <a name="group-machines-for-assessment"></a>アセスメントためのマシンのグループ化

この記事では、[Azure Migrate](migrate-overview.md) によるアセスメントのためにマシンのグループを作成する方法について説明します。 Azure Migrate では、グループ内のマシンを評価して Azure への移行に適しているかどうかを確認し、Azure でマシンを実行する際のサイズとコストの見積りを示します。 一緒に移行する必要があるマシンがわかっている場合は、手動で次の方法を使用して Azure Migrate でグループを作成できます。 一緒にグループ化する必要があるマシンがよくわからない場合は、Azure Migrate の依存関係視覚化機能を使用してグループを作成できます。 [詳細情報。](how-to-create-group-machine-dependencies.md)

> [!NOTE]
> 依存関係可視化機能は、Azure Government では使用できません。

## <a name="create-a-group"></a>グループの作成

1. Azure Migrate プロジェクトの**概要**で、[管理] の  **[グループ]** > **[+グループ]** をクリックし、グループ名を指定します。
2. グループに 1 つ以上のマシンを追加し、 **[作成]** をクリックします。
3. 必要に応じて、グループの新しいアセスメントの実行を選択することもできます。

    ![グループの作成](./media/how-to-create-a-group/create-group.png)

グループが作成されたら、**[グループ]** ページでグループを選択し、マシンを追加または削除してグループを変更できます。

## <a name="next-steps"></a>次の手順

- [マシンの依存関係マッピング](how-to-create-group-machine-dependencies.md)を使用して、信頼度の高いグループを作成する方法を確認します。
- 評価の計算方法の[詳細を確認](concepts-assessment-calculation.md)します。
