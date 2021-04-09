---
title: Stretch Database に対する Transparent Data Encryption の有効化
description: Azure での SQL Server Stretch Database に対する Transparent Data Encryption (TDE) の有効化
services: sql-server-stretch-database
documentationcenter: ''
ms.assetid: a44ed8f5-b416-4c41-9b1e-b7271f10bdc3
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/14/2016
author: blazem-msft
ms.author: blazem
ms.reviewer: jroth
manager: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: b016987162cc8202b7ad28d4dd8e5ab2953469d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95024247"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>Azure での Stretch Database に対する Transparent Data Encryption (TDE) の有効化
> [!div class="op_single_selector"]
> * [Azure Portal](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Transparent Data Encryption (TDE) を使用すると、データベース、関連付けられているバックアップ、保管されているトランザクション ログ ファイルの暗号化と暗号化解除をリアルタイムで実行することにより、悪意のあるアクティビティの脅威からデータを保護できます。アプリケーションを変更する必要はありません。

TDE は、データベース暗号化キーと呼ばれる対称キーを使用してデータベース全体のストレージを暗号化します。 データベース暗号化キーは組み込まれているサーバー証明書によって保護されます。 組み込みのサーバー証明書は、Azure サーバーごとに一意です。 Microsoft は、少なくとも 90 日ごとにこれらの証明書を自動的に回転します。 TDE の一般的な説明については、「[透過的なデータ暗号化 (TDE)]」を参照してください。

## <a name="enabling-encryption"></a>暗号化の有効化
Stretch 対応 SQL Server データベースから移行したデータを格納している Azure データベースの TDE を有効にするには、次の操作を行います。

1. [Azure ポータル](https://portal.azure.com)
2. データベース ブレードで **[設定]** ボタンをクリックします。
3. **[透過的なデータ暗号化]** オプションを選択します。![[設定] ブレードが表示された Azure portal のスクリーンショット。 [General]\(全般\) セクションでは、[透過的なデータ暗号化] が強調表示されています。][1]
4. **[On]\(オン\)** 設定を選択して、 **[保存]** を選択します。
   ![[透過的なデータ暗号化] ブレードが表示された Azure portal のスクリーンショット。 データの暗号化が有効で、[保存] ボタンが強調表示されています。][2]

## <a name="disabling-encryption"></a>暗号化の無効化
Stretch 対応 SQL Server データベースから移行したデータを格納している Azure データベースの TDE を無効にするには、次の操作を行います。

1. [Azure ポータル](https://portal.azure.com)
2. データベース ブレードで **[設定]** ボタンをクリックします。
3. **[透過的なデータ暗号化]** オプションを選択します
4. **[オフ]** 、 **[保存]** の順に選択します

<!--Anchors-->
[透過的なデータ暗号化 (TDE)]: /sql/relational-databases/security/encryption/transparent-data-encryption


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->