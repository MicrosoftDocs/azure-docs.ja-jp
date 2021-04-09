---
title: ファイアウォール規則を管理する - Azure portal - Azure Database for PostgreSQL - フレキシブル サーバー
description: Azure portal を使用して Azure Database for PostgreSQL - フレキシブル サーバー用のファイアウォール規則を作成して管理する
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 8c107b88f964d8e657d6833fc3a2e8425d9053ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90930813"
---
# <a name="create-and-manage-firewall-rules-for-azure-database-for-postgresql---flexible-server-using-the-azure-portal"></a>Azure portal を使用して Azure Database for PostgreSQL - フレキシブル サーバー用のファイアウォール規則を作成して管理する

> [!IMPORTANT]
> Azure Database for PostgreSQL - フレキシブル サーバーは現在パブリック プレビュー段階です。

Azure Database for PostgreSQL - フレキシブル サーバーでは、フレキシブル サーバーに接続するために、同時に使用できないネットワーク接続方法が 2 種類サポートされています。 次の 2 つのオプションがあります。

* パブリック アクセス (許可された IP アドレス)
* プライベート アクセス (VNet 統合)

この記事では、Azure portal を使用して **パブリック アクセス (許可された IP アドレス)** を使用して PostgreSQL サーバーを作成する方法に焦点を当て、フレキシブル サーバーの作成後にファイアウォール規則を管理する方法の概要について説明します。 "*パブリック アクセス (許可された IP アドレス)* " では、PostgreSQL サーバーへの接続は、許可された IP アドレスのみに制限されます。 クライアント IP アドレスは、ファイアウォール規則で許可されている必要があります。 詳細については、「[パブリック アクセス (許可された IP アドレス)](./concepts-networking.md#public-access-allowed-ip-addresses)」をご覧ください。 ファイアウォール規則は、サーバーの作成時に定義できます (推奨) が、後で追加することもできます。 この記事では、パブリック アクセス (許可された IP アドレス) を使用してファイアウォール規則を作成および管理する方法の概要について説明します。

## <a name="create-a-firewall-rule-when-creating-a-server"></a>サーバーの作成時にファイアウォール規則を作成する

1. ポータルの左上隅にある **[リソースの作成]** (+) を選択します。
2. **[データベース]**  >  **[Azure Database for PostgreSQL]** の順に選択します。 検索ボックスに「**PostgreSQL**」と入力してサービスを検索することもできます。
3. デプロイ オプションとして **[Flexible server]\(フレキシブル サーバー\)** を選択します。
4. **[基本]** フォームに入力します。
5. **[ネットワーク]** タブに移動して、サーバーへの接続方法を構成します。
6. **[接続方法]** で、 *[Public access (allowed IP addresses)]\(パブリック アクセス (許可される IP アドレス)\)* を選択します。 **ファイアウォール規則** を作成するには、ファイアウォール規則の名前と単一の IP アドレスまたはアドレスの範囲を指定します。 規則を単一の IP アドレスに限定する場合は、[開始 IP アドレス] と [終了 IP アドレス] のフィールドに同じアドレスを入力します。 ファイアウォールを開くと、管理者、ユーザー、アプリケーションは、有効な資格情報を持っている PostgreSQL サーバー上の任意のデータベースにアクセスできます。
   > [!Note]
   > Azure Database for PostgreSQL - フレキシブル サーバーでは、サーバー レベルでファイアウォールが作成されます。 これにより、外部のアプリケーションやツールからの、サーバーとサーバー上のすべてのデータベースへの接続が禁止されます。接続を許可するには、特定の IP アドレスに対してファイアウォールを開放する規則を作成します。
7. **[確認および作成]** を選択して、フレキシブル サーバーの構成を確認します。
8.  **[作成]** を選択して、サーバーをプロビジョニングします。 プロビジョニングには数分かかる場合があります。

## <a name="create-a-firewall-rule-after-server-is-created"></a>サーバーを作成した後にファイアウォール規則を作成する

1. [Azure portal](https://portal.azure.com/) で、ファイアウォール規則を追加する Azure Database for PostgreSQL - フレキシブル サーバーを選択します。
2. フレキシブル サーバーのページの **[設定]** の見出しで、 **[ネットワーク]** をクリックしてフレキシブル サーバーの [ネットワーク] ページを開きます。

   <!--![Azure portal - click Connection Security](./media/howto-manage-firewall-portal/1-connection-security.png)-->

3. ファイアウォール規則で **[現在のクライアント IP アドレスを追加する]** をクリックします。 これにより、Azure システムによって認識されたコンピューターのパブリック IP アドレスでファイアウォール規則が自動的に作成されます。

   <!--![Azure portal - click Add My IP](./media/howto-manage-firewall-portal/2-add-my-ip.png)-->

4. 構成を保存する前に、IP アドレスを確認します。 場合によっては、Azure Portal で見られる IP アドレスは、インターネットおよび Azure サーバーにアクセスするときに使用する IP アドレスと異なることがあります。 そのため、開始 IP アドレスと終了 IP アドレスを変更してルール関数を予想どおりになるようにする必要があります。

   検索エンジンまたはその他のオンライン ツールを使用して、自分の IP アドレスを確認できます。 (たとえば、"what is my IP" を検索します)。

   <!--![Bing search for What is my IP](./media/howto-manage-firewall-portal/3-what-is-my-ip.png)-->

5. アドレス範囲を追加します。 Azure Database for PostgreSQL - フレキシブル サーバーのファイアウォール規則では、単一の IP アドレスまたはアドレスの範囲を指定できます。 規則を単一の IP アドレスに限定する場合は、[開始 IP アドレス] と [終了 IP アドレス] のフィールドに同じアドレスを入力します。 ファイアウォールを開くと、管理者、ユーザー、アプリケーションは、有効な資格情報を持っている PostgreSQL サーバー上の任意のデータベースにアクセスできます。

   <!--![Azure portal - firewall rules](./media/howto-manage-firewall-portal/4-specify-addresses.png)-->

6. ツール バーの **[保存]** をクリックしてこのファイアウォール規則を保存します。 ファイアウォール規則の更新が成功したという確認を待機します。

   <!--![Azure portal - click Save](./media/howto-manage-firewall-portal/5-save-firewall-rule.png)-->

## <a name="connecting-from-azure"></a>Azure からの接続

Azure にデプロイされたリソースまたはアプリケーションを、フレキシブル サーバーに接続できるようにすることができます。 これには、Azure App Service でホストされる Web アプリケーション、Azure VM で実行される Web アプリケーション、Azure Data Factory データ管理ゲートウェイなどが含まれます。 

Azure 内のアプリケーションがサーバーに接続しようとした場合、ファイアウォールは、Azure の接続が許可されていることを確認します。 この設定を有効にするには、ポータルの **[ネットワーク]** タブから **[Allow public access from Azure services and resources within Azure to this server]\(Azure サービスおよび Azure 内のリソースからこのサーバーへのパブリックアクセスを許可する\)** オプションを選択し、 **[保存]** をクリックします。

ファイアウォール規則でこれらの接続を有効にするために、リソースが同じ仮想ネットワーク (VNet) またはリソース グループに存在する必要はありません。 接続試行が許可されていない場合、この要求は Azure Database for PostgreSQL - フレキシブル サーバーに到達しません。

> [!IMPORTANT]
> このオプションでは、他のお客様のサブスクリプションからの接続を含む、Azure からのすべての接続を許可するようにファイアウォールを構成します。 このオプションを選択する場合は、ログインおよびユーザーのアクセス許可が、承認されたユーザーのみにアクセスを制限していることを確認してください。
>
> フレキシブル サーバーに安全にアクセスするには、 **[Private access (VNet Integration)]\(プライベート アクセス (VNet 統合)\)** を選択することをお勧めします。
>
## <a name="manage-existing-firewall-rules-through-the-azure-portal"></a>Azure portal で既存のファイアウォール規則を管理する

次の手順を繰り返して、ファイアウォール規則を管理します。

- 現在のコンピューターを追加するには、ファイアウォール規則で **[現在のクライアント IP アドレスを追加する]** をクリックします。 **[保存]** をクリックして変更を保存します。
- さらに IP アドレスを追加するには、規則名、開始および終了 IP アドレスを入力します。 **[保存]** をクリックして変更を保存します。
- 既存の規則を変更するには、規則内の任意のフィールドをクリックし、変更します。 **[保存]** をクリックして変更を保存します。
- 既存の規則を削除するには、省略記号 [...] をクリックし、 **[削除]** をクリックします。 **[保存]** をクリックして変更を保存します。

## <a name="next-steps"></a>次のステップ
- [Azure Database for PostgreSQL - フレキシブル サーバーのネットワーク](./concepts-networking.md)の詳細を確認する
- [Azure Database for PostgreSQL - フレキシブル サーバーのファイアウォール規則](./concepts-networking.md#public-access-allowed-ip-addresses)について理解を深める
- [Azure CLI を使用した Azure Database for PostgreSQL ファイアウォール規則の作成と管理](./how-to-manage-firewall-cli.md)。