---
title: Azure Database for PostgreSQL サーバーのファイアウォール規則
description: この記事では、Azure Database for PostgreSQL サーバーのファイアウォール規則について説明します。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 793a68ee829d87a8433d5bdd77fe7b43e2f3af19
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548841"
---
# <a name="azure-database-for-postgresql-server-firewall-rules"></a>Azure Database for PostgreSQL サーバーのファイアウォール規則
Azure Database for PostgreSQL Server ファイアウォールは、どのコンピューターに権限を持たせるかを指定するまで、データベース サーバーへのすべてのアクセスを遮断します。 ファイアウォールは、各要求の送信元 IP アドレスに基づいてサーバーへのアクセス権を付与します。
ファイアウォールを構成するには、受け入れ可能な IP アドレスの範囲を指定するファイアウォール規則を作成します。 ファイアウォール規則はサーバー レベルで作成できます。

**ファイアウォール規則**:この規則により、クライアントは、Azure Database for PostgreSQL サーバー全体、つまり、同じ論理サーバー内のすべてのデータベースにアクセスできるようになります。 サーバー レベルのファイアウォール規則を構成するには、Azure Portal または Azure CLI コマンドを使用します。 サーバー レベルのファイアウォール規則を作成するには、サブスクリプション所有者またはサブスクリプション共同作成者である必要があります。

## <a name="firewall-overview"></a>ファイアウォールの概要
既定では、Azure Database for PostgreSQL サーバーへのすべてのデータベース アクセスが、ファイアウォールによってブロックされます。 他のコンピューターからサーバーの使用を開始するには、サーバー レベルのファイアウォール規則を 1 つ以上指定して、サーバーへのアクセスを有効にする必要があります。 ファイアウォール規則を使用して、インターネットからのアクセスを許可する IP アドレス範囲を指定します。 Azure Portal Web サイト自体へのアクセスは、ファイアウォール規則の影響は受けません。
インターネットおよび Azure からの接続の試行は、次の図に示されるように、PostgreSQL データベースに到達する前にファイアウォールを通過する必要があります。

![ファイアウォールのしくみを示すサンプル フロー](media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>インターネットからの接続
サーバー レベルのファイアウォール規則は、同じ Azure Database for PostgreSQL サーバー上のすべてのデータベースに適用されます。 要求の IP アドレスがサーバーレベルのファイアウォール規則で指定されたいずれかの IP アドレス範囲内にある場合は、接続が許可されます。
要求の IP アドレスが、どのサーバー レベルのファイアウォール規則で指定された IP アドレス範囲内にもない場合は、接続要求は失敗します。
たとえば、アプリケーションが PostgreSQL の JDBC ドライバーで接続している場合、ファイアウォールによって接続がブロックされているときに接続しようとすると、次のエラーが発生する可能性があります。
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException:FATAL: no pg\_hba.conf entry for host "123.45.67.890", user "adminuser", database "postgresql", SSL

## <a name="connecting-from-azure"></a>Azure からの接続
Azure のアプリケーションが Azure Database for PostgreSQL サーバーに接続できるようにするには、Azure 接続を有効にする必要があります。 たとえば、Azure Web Apps アプリケーションや Azure VM で実行されるアプリケーションをホストしたり、Azure Data Factory データ管理ゲートウェイから接続したりするためです。 ファイアウォール規則でこれらの接続を有効にするために、リソースが同じ仮想ネットワーク (VNet) またはリソース グループに存在する必要はありません。 Azure からアプリケーションがデータベース サーバーに接続しようとした場合、ファイアウォールは、Azure の接続が許可されていることを確認します。 これらの種類の接続を有効にするには、いくつかの方法があります。 開始アドレスと終了アドレスが 0.0.0.0 であるファイアウォール設定は、これらの接続が許可されていることを示します。 あるいは、ポータルの **[接続のセキュリティ]** ウィンドウから **[Azure サービスへのアクセスを許可]** オプションを **[オン]** に設定し、**[保存]** をクリックすることもできます。 接続試行が許可されていない場合、この要求は Azure Database for PostgreSQL サーバーに到達しません。

> [!IMPORTANT]
> このオプションは、ファイアウォールを構成して、他のお客様のサブスクリプションからの接続を含むすべての接続を許可します。 このオプションを選択する場合は、ログインおよびユーザーのアクセス許可が、承認されたユーザーのみにアクセスを制限していることを確認してください。
> 

![ポータルで [Azure サービスへのアクセスを許可] を構成する](media/concepts-firewall-rules/allow-azure-services.png)

## <a name="programmatically-managing-firewall-rules"></a>ファイアウォール規則のプログラムによる管理
ファイアウォール規則は、Azure Portal に加え、Azure CLI を使用してプログラムで管理することができます。
「[Azure CLI を使用した Azure Database for PostgreSQL ファイアウォール規則の作成と管理](howto-manage-firewall-using-cli.md)」も参照してください

## <a name="troubleshooting-the-database-server-firewall"></a>データベース サーバー ファイアウォールのトラブルシューティング
Microsoft Azure Database for PostgreSQL サーバー サービスに期待どおりにアクセスできない場合は、次の点を検討してください。

* **許可一覧に変更が反映されない:** Azure Database for PostgreSQL サーバーのファイアウォール構成に対する変更が反映されるまで最大 5 分間の遅延が発生する場合があります。

* **ログインが許可されない、または正しくないパスワードが使用された:** Azure Database for PostgreSQL サーバーでは、ログインのアクセス許可がないか、使用したパスワードが正しくない場合、Azure Database for PostgreSQL サーバーへの接続は拒否されます。 ファイアウォール設定の作成は、サーバーへの接続を試行する機会をクライアントに提供するだけです。そのため、各クライアントは、必要なセキュリティ資格情報を提供する必要があります。

たとえば、JDBC クライアントを使用すると、次のエラーが表示されることがあります。
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException:FATAL: password authentication failed for user "yourusername"

* **動的 IP アドレス:** 動的 IP アドレス指定によるインターネット接続を使用しており、ファイアウォールの通過に問題が発生している場合は、次の解決策のいずれかをお試しください。

* Azure Database for PostgreSQL サーバーにアクセスするクライアント コンピューターに割り当てられている IP アドレス範囲について、インターネット サービス プロバイダー (ISP) に問い合わせ、ファイアウォール規則として、その IP アドレス範囲を追加してください。

* 代わりに、クライアント コンピューター用に静的 IP アドレスを取得し、ファイアウォール規則としてその静的 IP アドレス範囲を追加してください。

## <a name="next-steps"></a>次の手順
サーバー レベルおよびデータベース レベルのファイアウォール規則の作成については、次の記事をご覧ください。
* [Azure Portal を使用した Azure Database for PostgreSQL ファイアウォール規則の作成と管理](howto-manage-firewall-using-portal.md)
* [Azure CLI を使用した Azure Database for PostgreSQL ファイアウォール規則の作成と管理](howto-manage-firewall-using-cli.md)
