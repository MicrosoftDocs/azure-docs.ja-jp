---
title: ファイアウォール規則の管理 - Hyperscale (Citus) - Azure Database for PostgreSQL
description: Azure portal を使用して Azure Database for PostgreSQL - Hyperscale (Citus) のファイアウォール規則を作成および管理します
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 9/12/2019
ms.openlocfilehash: c84616e8a9b9ff9722f5a104175c80c37dbcbcc3
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86116915"
---
# <a name="manage-firewall-rules-for-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL - Hyperscale (Citus) のファイアウォール規則を管理する
サーバーレベルのファイアウォール規則を使用して、指定した IP アドレスまたは IP アドレス範囲からの Hyperscale (Citus) コーディネーター ノードへのアクセスを管理できます。

## <a name="prerequisites"></a>前提条件
このハウツー ガイドの手順を実行するには、以下が必要です。
- サーバー グループ ([Azure Database for PostgreSQL - Hyperscale (Citus) サーバー グループの作成](quickstart-create-hyperscale-portal.md)に関する記事を参照)。

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Azure Portal でサーバーレベルのファイアウォール規則を作成する

> [!NOTE]
> これらの設定には、Azure Database for PostgreSQL - Hyperscale (Citus) サーバー グループの作成時にもアクセスできます。 **[ネットワーク]** タブの下で、 **[パブリック エンドポイント]** をクリックします。
> ![Azure portal - [ネットワーク] タブ](./media/howto-hyperscale-manage-firewall-using-portal/0-create-public-access.png)

1. PostgreSQL サーバー グループのページで、[セキュリティ] 見出しの下の **[ネットワーク]** をクリックして、ファイアウォール規則を開きます。

   ![Azure portal - [ネットワーク] のクリック](./media/howto-hyperscale-manage-firewall-using-portal/1-connection-security.png)

2. ツールバー (以下のオプション A)、またはリンク (オプション B) のいずれかの **[クライアント IP の追加]** をクリックします。 いずれかの方法により、Azure システムによって認識されたコンピューターのパブリック IP アドレスでファイアウォール規則が自動的に作成されます。

   ![Azure portal - [クライアント IP の追加] のクリック](./media/howto-hyperscale-manage-firewall-using-portal/2-add-my-ip.png)

あるいは、(オプション B の右側にある) **[+ Add 0.0.0.0 - 255.255.255.255]** をクリックすると、IP だけでなく、インターネット全体でコーディネーター ノードのポート 5432 にアクセスできるようになります。 この場合も、クライアントでは、クラスターを使用するために正しいユーザー名とパスワードでログインする必要があります。 ただし、世界中からのアクセスは短時間のみ、運用環境以外のデータベースだけに許可することをお勧めします。

3. 構成を保存する前に、IP アドレスを確認します。 場合によっては、Azure Portal で見られる IP アドレスは、インターネットおよび Azure サーバーにアクセスするときに使用する IP アドレスと異なることがあります。 そのため、開始 IP と終了 IP を変更してルール関数を予想どおりにする必要があります。
   検索エンジンまたはその他のオンライン ツールを使用して、自分の IP アドレスを確認します (たとえば、"what is my IP" を検索します)。

   ![「What is my IP」の Bing 検索](./media/howto-hyperscale-manage-firewall-using-portal/3-what-is-my-ip.png)

4. アドレス範囲を追加します。 このファイアウォール規則では、単一の IP アドレスまたはアドレスの範囲を指定できます。 規則を単一の IP アドレスに限定する場合は、[開始 IP] と [終了 IP] のフィールドに同じアドレスを入力します。 ファイアウォールを開くと、管理者、ユーザー、およびアプリケーションは、ポート 5432 でコーディネーター ノードにアクセスできるようになります。

5. ツール バーの **[保存]** をクリックしてこのサーバーレベルのファイアウォール規則を保存します。 ファイアウォール規則の更新が成功したという確認を待機します。

## <a name="connecting-from-azure"></a>Azure からの接続

Azure でホストされているアプリケーション (Azure Web Apps アプリケーションや Azure VM で実行されるアプリケーションなど) に、Hyperscale データベースへのアクセス権を簡単に付与する方法があります。 **[ネットワーク]** ウィンドウからポータルの **[Azure サービスおよびリソースにこのサーバー グループへのアクセスを許可する]** オプションを **[はい]** に設定し、 **[保存]** を押すだけです。

> [!IMPORTANT]
> このオプションは、ファイアウォールを構成して、他のお客様のサブスクリプションからの接続を含むすべての接続を許可します。 このオプションを選択する場合は、ログインおよびユーザーのアクセス許可が、承認されたユーザーのみにアクセスを制限していることを確認してください。

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Azure ポータルで既存のサーバー レベルのファイアウォール規則を管理する
ファイアウォール規則を管理する手順を繰り返します。
* 現在のコンピューターを追加するには、 **[クライアント IP の追加]** のボタンをクリックします。 **[保存]** をクリックして変更を保存します。
* さらに IP アドレスを追加するには、規則名、開始および終了 IP アドレスを入力します。 **[保存]** をクリックして変更を保存します。
* 既存の規則を変更するには、規則内の任意のフィールドをクリックし、変更します。 **[保存]** をクリックして変更を保存します。
* 既存の規則を削除するには、省略記号 [...] をクリックし、 **[削除]** をクリックします。 **[保存]** をクリックして変更を保存します。

## <a name="next-steps"></a>次のステップ
- 接続の問題のトラブルシューティング方法を含む、[ファイアウォール規則の概念](concepts-hyperscale-firewall-rules.md)について確認します。
