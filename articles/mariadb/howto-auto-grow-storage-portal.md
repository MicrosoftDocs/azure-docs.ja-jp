---
title: ストレージの自動拡張 - Azure portal - Azure Database for MariaDB
description: この記事では、Azure portal を使用して Azure Database for MariaDB のストレージの自動拡張を有効にする方法について説明します
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 56bc6c0e7a294f80e946a2eb0c84e5d0bd49d0f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98663470"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-using-the-azure-portal"></a>Azure portal を使用した Azure Database for MariaDB のストレージの自動拡張
この記事では、ワークロードに影響を与えることなく拡張されるように Azure Database for MariaDB サーバーのストレージを構成する方法について説明します。

サーバーが、割り当てられたストレージの上限に達すると、そのサーバーは読み取り専用としてマークされます。 ただし、ストレージの自動拡張を有効にした場合、サーバーのストレージは、増大するデータに合わせて拡張されます。 プロビジョニングされたストレージが 100 GB 未満のサーバーでは、空きストレージがプロビジョニングされたストレージの 1 GB または 10% のどちらか大きい方を下回ると、プロビジョニングされたストレージ サイズが 5 GB 単位ですぐに拡張されます。 プロビジョニングされたストレージが 100 GB を超えるサーバーの場合、空きストレージ領域がプロビジョニングされたストレージ サイズの 5% を下回ると、プロビジョニングされたストレージ サイズが 5% 単位で増加します。 [こちら](concepts-pricing-tiers.md#storage)で指定されているストレージの上限が適用されます。

## <a name="prerequisites"></a>前提条件
このハウツー ガイドを完了するには、次が必要です。
- [Azure Database for MariaDB サーバー](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>ストレージの自動拡張を有効にする 

MariaDB サーバー ストレージの自動拡張を設定するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) で、既存の Azure Database for MariaDB サーバーを選択します。

2. MariaDB サーバーのページの **[設定]** の見出しの下にある **[価格レベル]** をクリックして、価格レベルのページを開きます。

3. [自動拡張] セクションで **[はい]** を選択して、ストレージの自動拡張を有効にします。

    ![Azure Database for MariaDB - 価格レベルの設定 - 自動拡張](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. **[OK]** をクリックして変更を保存します。

5. 自動拡張が正常に有効化されたことを確認する通知が表示されます。

    ![Azure Database for MariaDB - 自動拡張の成功](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>次のステップ

[メトリックに関するアラートを作成する方法](howto-alert-metric.md)について学習します。
