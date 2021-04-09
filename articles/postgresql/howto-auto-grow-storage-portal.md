---
title: ストレージを自動拡張する - Azure portal - Azure Database for PostgreSQL - 単一サーバー
description: この記事では、Azure Database for PostgreSQL - 単一サーバーで Azure portal を使用して、ストレージを自動拡張するように構成する方法について説明します
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 5/29/2019
ms.openlocfilehash: d49047e9b3a65da90f4a5ac9e4b3cb1e32bd59a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92489984"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>Azure portal を使用して Azure Database for PostgreSQL でストレージを自動拡張する - 単一サーバー
この記事では、ワークロードに影響を与えることなく拡張されるように Azure Database for PostgreSQL サーバーのストレージを構成する方法について説明します。

サーバーが、割り当てられたストレージの上限に達すると、そのサーバーは読み取り専用としてマークされます。 ただし、ストレージの自動拡張を有効にした場合、サーバーのストレージは、増大するデータに合わせて拡張されます。 プロビジョニングされたストレージが 100 GB 未満のサーバーでは、空きストレージがプロビジョニングされたストレージの 1 GB または 10% のどちらか大きい方を下回ると、プロビジョニングされたストレージ サイズが 5 GB 単位ですぐに拡張されます。 プロビジョニングされたストレージが 100 GB を超えるサーバーの場合、空きストレージ領域がプロビジョニングされたストレージ サイズの 5% を下回ると、プロビジョニングされたストレージ サイズが 5% 単位で増加します。 [こちら](./concepts-pricing-tiers.md#storage)で指定されているストレージの上限が適用されます。

## <a name="prerequisites"></a>前提条件
このハウツー ガイドを完了するには、次が必要です。
- [Azure Database for PostgreSQL サーバー](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>ストレージの自動拡張を有効にする 

PostgreSQL サーバー ストレージの自動拡張を設定するには、これらの手順に従います。

1. [Azure portal](https://portal.azure.com/) で、既存の Azure Database for PostgreSQL サーバーを選択します。

2. PostgreSQL サーバーのページで **[設定]** にある **[価格レベル]** をクリックして、価格レベルのページを開きます。

3. **[自動拡張]** セクションで、 **[はい]** を選択してストレージの自動拡張を有効にします。

    :::image type="content" source="./media/howto-auto-grow-storage-portal/3-auto-grow.png" alt-text="Azure Database for PostgreSQL - 価格レベルの設定 - 自動拡張":::

4. **[OK]** をクリックして変更を保存します。

5. 自動拡張が正常に有効化されたことを確認する通知が表示されます。

    :::image type="content" source="./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png" alt-text="Azure Database for PostgreSQL - 自動拡張の成功":::

## <a name="next-steps"></a>次のステップ

[メトリックに関するアラートを作成する方法](howto-alert-on-metric.md)について学習します。