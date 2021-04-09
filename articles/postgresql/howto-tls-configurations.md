---
title: TLS の構成 - Azure portal - Azure Database for PostgreSQL - Single Server
description: Azure portal を使用して Azure Database for PostgreSQL Single Server に対する TLS の構成を設定する方法について説明します
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: 25be6b3c4e3172fc8ee14b97fd890b5948c284ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93242366"
---
# <a name="configuring-tls-settings-in-azure-database-for-postgresql-single---server-using-azure-portal"></a>Azure portal を使用した Azure Database for PostgreSQL Single Server での TLS 設定の構成

この記事では、接続のために許可された TLS の最小バージョンを適用して、構成されている TLS の最小バージョンより低い TLS バージョンのすべての接続を拒否することによりネットワーク セキュリティを向上させるように Azure Database for PostgreSQL を構成する方法について説明します。

Azure Database for PostgreSQL に接続するための TLS バージョンを適用できます。 ユーザーは、使用しているデータベース サーバーに TLS の最小バージョンを設定できるようになりました。 たとえば、最小 TLS バージョンを TLS 1.0 に設定すると、サーバーでは TLS 1.0、1.1、および 1.2 以上を使用するクライアントからの接続が許可されます。 代わりに、最小 TLS バージョンを 1.2 以上に設定すると、TLS 1.2 を使用するクライアントからの接続だけを許可し、TLS 1.0 と TLS 1.1 を使用する接続はすべて拒否することを意味します。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドを完了するには、次が必要です。

* [Azure Database for PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL Single Server の TLS 構成を設定する

PostgreSQL の TLS の最小バージョンを設定するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) で、既存の Azure Database for PostgreSQL を選択します。

1.  Azure Database for PostgreSQL - Single Server ページの **[設定]** で、 **[接続のセキュリティ]** をクリックして接続のセキュリティの構成ページを開きます。

1. TLS 1.2 より低い TLS バージョンでの PostgreSQL Single Server への接続を拒否するには、 **[TLS の最小バージョン]** で **[1.2]** を選択します。

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value.png" alt-text="Azure Database for PostgreSQL Single Server の TLS 構成":::

1. **[保存]** をクリックして変更を保存します。

1. 接続のセキュリティの設定が正常に有効化されたことを確認する通知が表示されます。

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value-success.png" alt-text="Azure Database for PostgreSQL Single Server の TLS 構成 (成功)":::

## <a name="next-steps"></a>次のステップ

[メトリックに関するアラートを作成する方法](howto-alert-on-metric.md)について学習します