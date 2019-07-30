---
title: インクルード ファイル
description: インクルード ファイル
author: jonels-msft
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: include
ms.date: 05/14/2019
ms.author: jonels
ms.custom: include file
ms.openlocfilehash: c07e352288d7dc1d0bf198fd74c8baaded3a2d23
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181418"
---
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインします

[Azure Portal](https://portal.azure.com) にサインインします。

## <a name="create-an-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL - Hyperscale (Citus) の作成

Azure Database for PostgreSQL サーバーを作成するには、次の手順に従います。
1. Azure Portal の左上隅にある **[リソースの作成]** をクリックします。
2. **[新規]** ページで **[データベース]** を選択し、 **[データベース]** ページで **[Azure Database for PostgreSQL]** を選択します。
3. デプロイ オプションについては、 **[Hyperscale (Citus) サーバー グループ - プレビュー]** の下にある **[作成]** ボタンをクリックします。
4. 新しいサーバーの詳細フォームには次の情報を入力してください。
   - リソース グループ: このフィールドのテキスト ボックスの下の **[新規作成]** リンクをクリックします。 **myresourcegroup** などの名前を入力します。
   - サーバー グループ名: 新しいサーバー グループの一意の名前を入力します。これはサーバー サブドメインにも使用されます。
   - 管理者ユーザー名: 現在、値 **citus** である必要があり、変更することはできません。
   - パスワード: 少なくとも 8 文字で、次のカテゴリのうち 3 つのカテゴリの文字が含まれている必要があります。英字大文字、英字小文字、数字 (0 から 9)、英数字以外の文字 (!、$、#、% など)。
   - 場所: データに最速でアクセスできるよう、お客様のユーザーに最も近い場所を使用します。

   > [!IMPORTANT]
   > ここで指定するサーバー管理者のパスワードは、サーバーとそのデータベースにログインするために必要です。 後で使用するために、この情報を覚えておくか、記録しておきます。

5. **[サーバー グループの構成]** をクリックします。 そのセクションの設定を変更しないで、 **[保存]** をクリックします。
6. **[確認と作成]** 、 **[作成]** の順にクリックして、サーバーをプロビジョニングします。 プロビジョニングには数分かかります。
7. ページは、デプロイを監視するためにリダイレクトされます。 ライブ状態が **[デプロイが進行中です]** から **[デプロイが完了しました]** に変わったら、ページの左側にある **[出力]** メニュー項目をクリックします。
8. 出力ページには、コーディネーターのホスト名が含まれており、値をクリップボードにコピーするためのボタンが横に付いています。 後で使用するために、この情報を記録しておきます。

## <a name="configure-a-server-level-firewall-rule"></a>サーバーレベルのファイアウォール規則の構成

Azure Database for PostgreSQL - Hyperscale (Citus) (プレビュー) サービスは、サーバーレベルでファイアウォールを使用します。 既定では、ファイアウォールは、すべての外部のアプリケーションとツールがコーディネーター ノードやデータベースに接続することを防止します。 特定の IP アドレス範囲のファイアウォールを開放する規則を追加する必要があります。

1. コーディネーター ノードのホスト名を以前にコピーした **[出力]** セクションから、[戻る] をクリックして **[概要]** メニュー項目に戻ります。

2. デプロイのサーバー グループの名前を探してクリックします。 (サーバー グループ名はサフィックスが*ありません*。 名前の末尾が "-c"、"-w0"、"-w1" などのサーバーはサーバー グループではありません)。

3. 左側のメニューの **[セキュリティ]** の下にある **[ファイアウォール]** をクリックします。

4. **[+ 現在のクライアント IP アドレスに対するファイアウォール規則の追加]** リンクをクリックします。

5. 最後に、 **[保存]** ボタンをクリックします。

   > [!NOTE]
   > Azure PostgreSQL サーバーはポート 5432 を介して通信します。 企業ネットワーク内から接続しようとしても、ポート 5432 での送信トラフィックがネットワークのファイアウォールで禁止されている場合があります。 その場合、会社の IT 部門によってポート 5432 が開放されない限り、Azure SQL Database サーバーに接続することはできません。
   >

## <a name="connect-to-the-database-using-psql"></a>psql を使用したデータベースへの接続

Azure Database for PostgreSQL サーバーを作成したときに、**citus** という名前の既定のデータベースも作成されています。 データベース サーバーに接続するには、接続文字列と管理者パスワードが必要です。

1. 接続文字列を取得します。 サーバー グループ ページで、 **[接続文字列]** メニュー項目をクリックします。 ( **[設定]** の下にあります)。**C++ (libpq)** のマークが付けられている文字列を見つけます。 次のような形式になります。

   ```
   host=hostname.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require
   ```

   文字列をコピーします。 "{your\_password}" を以前に選択した管理者パスワードに置き換える必要があります。 システムは、プレーンテキストのパスワードを保存しないため、接続文字列に表示できません。

2. ローカル コンピューターでターミナル ウィンドウを開きます。

3. プロンプトで、[psql](https://www.postgresql.org/docs/current/app-psql.html) ユーティリティを使用して Azure Database for PostgreSQL サーバーに接続します。 パスワードが含まれていることを確認して、接続文字列を引用符で囲んで渡します。
   ```bash
   psql "{connection_string}"
   ```

   たとえば、次のコマンドは、サーバー グループ **mydemoserver** のコーディネーター ノードに接続します。

   ```bash
   psql "host=mydemoserver-c.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```
