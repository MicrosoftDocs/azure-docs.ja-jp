---
title: Azure Database for MySQL サーバーのファイアウォール規則
description: Azure Database for MySQL サーバーのファイアウォール規則について説明します。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 0802185b7fb0d1a6d7d41cd1fa5a30f5ce10424b
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443905"
---
# <a name="azure-database-for-mysql-server-firewall-rules"></a>Azure Database for MySQL サーバーのファイアウォール規則
ファイアウォールは、どのコンピューターに権限を持たせるかを指定するまで、データベース サーバーへのすべてのアクセスを遮断します。 ファイアウォールは、各要求の送信元 IP アドレスに基づいてサーバーへのアクセス権を付与します。

ファイアウォールを構成するには、受け入れ可能な IP アドレスの範囲を指定するファイアウォール規則を作成します。 ファイアウォール規則はサーバー レベルで作成できます。

**ファイアウォール規則**:この規則により、クライアントは、Azure Database for MySQL サーバー全体、つまり、同じ論理サーバー内のすべてのデータベースにアクセスできるようになります。 サーバー レベルのファイアウォール規則を構成するには、Azure Portal または Azure CLI コマンドを使用します。 サーバー レベルのファイアウォール規則を作成するには、サブスクリプション所有者またはサブスクリプション共同作成者である必要があります。

## <a name="firewall-overview"></a>ファイアウォールの概要
既定では、Azure Database for MySQL サーバーへのすべてのデータベース アクセスが、ファイアウォールによってブロックされます。 他のコンピューターからサーバーの使用を開始するには、サーバー レベルのファイアウォール規則を 1 つ以上指定して、サーバーへのアクセスを有効にする必要があります。 ファイアウォール規則を使用して、インターネットからのアクセスを許可する IP アドレス範囲を指定します。 Azure Portal Web サイト自体へのアクセスは、ファイアウォール規則の影響は受けません。

インターネットおよび Azure からの接続の試行は、次の図に示されるように、Azure Database for MySQL データベースに到達する前にファイアウォールを通過する必要があります。

![ファイアウォールのしくみを示すサンプル フロー](./media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>インターネットからの接続
サーバーレベルのファイアウォール規則は、Azure Database for MySQL サーバー上のすべてのデータベースに適用されます。

要求の IP アドレスがサーバーレベルのファイアウォール規則で指定されたいずれかの IP アドレス範囲内にある場合は、接続が許可されます。

要求の IP アドレスがデータベース レベルのファイアウォール規則またはサーバー レベルのファイアウォール規則で指定された IP アドレス範囲外にある場合、接続要求は失敗します。

## <a name="connecting-from-azure"></a>Azure からの接続
Azure のアプリケーションが Azure Database for MySQL サーバーに接続できるようにするには、Azure 接続を有効にする必要があります。 たとえば、Azure Web Apps アプリケーションや Azure VM で実行されるアプリケーションをホストしたり、Azure Data Factory データ管理ゲートウェイから接続したりするためです。 ファイアウォール規則でこれらの接続を有効にするために、リソースが同じ仮想ネットワーク (VNet) またはリソース グループに存在する必要はありません。 Azure からアプリケーションがデータベース サーバーに接続しようとした場合、ファイアウォールは、Azure の接続が許可されていることを確認します。 これらの種類の接続を有効にするには、いくつかの方法があります。 開始アドレスと終了アドレスが 0.0.0.0 であるファイアウォール設定は、これらの接続が許可されていることを示します。 あるいは、ポータルの **[接続のセキュリティ]** ウィンドウから **[Azure サービスへのアクセスを許可]** オプションを **[オン]** に設定し、 **[保存]** をクリックすることもできます。 接続試行が許可されていない場合、この要求は Azure Database for MySQL サーバーに到達しません。

> [!IMPORTANT]
> このオプションは、ファイアウォールを構成して、他のお客様のサブスクリプションからの接続を含むすべての接続を許可します。 このオプションを選択する場合は、ログインおよびユーザーのアクセス許可が、承認されたユーザーのみにアクセスを制限していることを確認してください。
> 

![ポータルで [Azure サービスへのアクセスを許可] を構成する](./media/concepts-firewall-rules/allow-azure-services.png)

## <a name="programmatically-managing-firewall-rules"></a>ファイアウォール規則のプログラムによる管理
ファイアウォール規則は、Azure Portal に加え、Azure CLI を使用してプログラムで管理することができます。 「[Create and manage Azure Database for MySQL firewall rules using Azure CLI (Azure CLI を使用した Azure Database for MySQL ファイアウォール規則の作成と管理)](./howto-manage-firewall-using-cli.md)」も参照してください

## <a name="troubleshooting-the-database-firewall"></a>データベース ファイアウォールのトラブルシューティング
Microsoft Azure Database for MySQL サーバー サービスに期待どおりにアクセスできない場合は、次の点を検討してください。

* **許可一覧に変更が反映されない:** Azure Database for MySQL サーバーのファイアウォール構成に対する変更が反映されるまで最大 5 分間の遅延が発生する場合があります。

* **ログインが許可されない、または正しくないパスワードが使用された:** Azure Database for MySQL サーバーでは、ログインのアクセス許可がないか、使用したパスワードが正しくない場合、Azure Database for MySQL サーバーへの接続は拒否されます。 ファイアウォール設定の作成は、クライアントに対し、サーバーへの接続を試行する機会のみを提供します。それぞれのクライアントは、必要なセキュリティ資格情報を提供する必要があります。

* **動的 IP アドレス:** 動的 IP アドレス指定によるインターネット接続を使用しており、ファイアウォールの通過に問題が発生している場合は、次の解決策のいずれかをお試しください。

* Azure Database for MySQL サーバーにアクセスするクライアント コンピューターに割り当てられている IP アドレス範囲について、インターネット サービス プロバイダー (ISP) に問い合わせ、ファイアウォール規則として、IP アドレス範囲を追加してください。

* 動的 IP アドレスの代わりに、静的 IP アドレスを取得し、ファイアウォール規則として、IP アドレス範囲を追加してください。

## <a name="next-steps"></a>次の手順

* [Azure Portal を使用した Azure Database for MySQL ファイアウォール規則の作成と管理](./howto-manage-firewall-using-portal.md)
* [Azure CLI を使用した Azure Database for MySQL ファイアウォール規則の作成と管理](./howto-manage-firewall-using-cli.md)
