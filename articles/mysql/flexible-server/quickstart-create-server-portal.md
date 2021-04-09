---
title: 'クイックスタート: Azure Database for MySQL フレキシブル サーバーを作成する - Azure portal'
description: この記事では、Azure portal を使用して、Azure Database for MySQL フレキシブル サーバーを数分で作成する手順について説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/22/2020
ms.openlocfilehash: 074b799a4f0e83c47aac0b2b3fca5386bd45429f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100521970"
---
# <a name="quickstart-use-the-azure-portal-to-create-an-azure-database-for-mysql-flexible-server"></a>クイックスタート: Azure portal を使用して Azure Database for MySQL フレキシブル サーバーを作成する

Azure Database for MySQL フレキシブル サーバーは、高可用性 MySQL サーバーをクラウドで実行、管理、スケーリングするために使用できる管理サービスです。 このクイックスタートでは、Azure portal を使用してフレキシブル サーバーを作成する方法について説明します。

> [!IMPORTANT] 
> Azure Database for MySQL フレキシブル サーバーは現在、パブリック プレビュー段階にあります。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする
[Azure ポータル](https://portal.azure.com/)にアクセスします。 資格情報を入力してポータルにサインインします。 既定のビューはサービス ダッシュボードです。

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>Azure Database for MySQL フレキシブル サーバーを作成する

フレキシブル サーバーは、定義済みの一連の[コンピューティング リソースとストレージ リソース](./concepts-compute-storage.md)を使って作成します。 サーバーは、[Azure リソース グループ](../../azure-resource-manager/management/overview.md)内に作成します。

フレキシブル サーバーを作成するには、これらの手順を実行します。

1. ポータルで **[Azure Database for MySQL サーバー]** を検索して選択します。
    
    > :::image type="content" source="./media/quickstart-create-server-portal/find-mysql-portal.png" alt-text="[Azure Database for MySQL サーバー] の検索を示すスクリーンショット。":::

2. **[追加]** を選択します。 

3. **[Select Azure Database for MySQL deployment option]\(Azure Database for MySQL のデプロイ オプションの選択\)** ページで、デプロイ オプションとして **[フレキシブル サーバー]** を選択します。
     
    > :::image type="content" source="./media/quickstart-create-server-portal/deployment-option.png" alt-text="[フレキシブル サーバー] オプションを示すスクリーンショット。":::    

4. **[基本]** タブで、次の情報を入力します。 

    > :::image type="content" source="./media/quickstart-create-server-portal/create-form.png" alt-text="フレキシブル サーバー ページの [基本] タブを示すスクリーンショット。"::: 
                                    
    |**設定**|**推奨値**|**説明**|
    |---|---|---|
    サブスクリプション|お使いのサブスクリプション名|サーバーに使用する Azure サブスクリプション。 複数のサブスクリプションをお持ちの場合は、リソースの課金対象にするサブスクリプションを選択します。|
    Resource group|**myresourcegroup**| 新しいリソース グループ名、またはサブスクリプションの既存のリソース グループ名。|
    サーバー名 |**mydemoserver**|フレキシブル サーバーを識別する一意の名前。 指定したサーバー名にドメイン名 `mysql.database.azure.com` が追加されます。 サーバー名に含めることができるのは、英小文字、数字、およびハイフン (-) のみであり、 3 から 63 文字にする必要があります。|
    管理者ユーザー名 |**mydemouser**| サーバーに接続するときに使用する自分のサインイン アカウント。 管理ユーザー名を **azure_superuser**、**admin**、**administrator**、**root**、**guest**、または **public** にすることはできません。|
    Password |お使いのパスワード| サーバー管理者アカウントの新しいパスワード。 8 ～ 128 文字にする必要があります。 また、パスワードには、英大文字、英小文字、数字 (0 から 9)、英数字以外の文字 (!、$、#、% など) のうち、3 つのカテゴリの文字が含まれている必要があります。|
    Region|ユーザーに最も近いリージョン| ユーザーに最も近い場所。|
    Version|**5.7**| MySQL のメジャー バージョン。|
    コンピューティングとストレージ | **Burstable**、**Standard_B1ms**、**10 GiB**、**7 日** | 新しいサーバーのコンピューティング、ストレージ、およびバックアップ構成。 **[サーバーの構成]** を選択します。 **Burstable**、**Standard_B1ms**、**10 GiB**、**7 日** は、それぞれ **[コンピューティング レベル]** 、 **[コンピューティング サイズ]** 、 **[ストレージ サイズ]** 、 **[バックアップの保有期間]** の既定値です。 これらの値はそのままにすることも、調整することもできます。 コンピューティングとストレージの選択を保存するには、 **[保存]** を選択して構成を続行します。 次のスクリーンショットは、コンピューティングとストレージのオプションを示しています。|
    
    > :::image type="content" source="./media/quickstart-create-server-portal/compute-storage.png" alt-text="コンピューティングとストレージのオプションを示すスクリーンショット。":::

5. ネットワーク オプションを構成します。

    **[ネットワーク]** タブでは、サーバーに到達するための方法を選択できます。 Azure Database for MySQL フレキシブル サーバーでは、2 つの方法でサーバーに接続できます。 
   - パブリック アクセス (許可された IP アドレス)
   - プライベート アクセス (VNet 統合) 
   
   パブリック アクセスを使用する場合、サーバーへのアクセスは、ファイアウォール規則に追加されている許可された IP アドレスに限定されます。 この方法では、特定の IP アドレスまたは範囲に対してファイアウォールを開く規則を作成しない限り、外部のアプリケーションやツールはサーバーとサーバー上のデータベースに接続できなくなります。 プライベート アクセス (VNet 統合) を使用する場合、サーバーへのアクセスは仮想ネットワークに限定されます。 このクイックスタートでは、パブリック アクセスを有効にしてサーバーに接続する方法について説明します。 [接続方法の詳細については、概念に関する記事をご覧ください。](./concepts-networking.md)

    > [!NOTE]
    > サーバーの作成後に接続方法を変更することはできません。 たとえば、サーバーの作成時に **[パブリック アクセス (使用できる IP アドレス)]** を選択した場合、サーバーの作成後に **[プライベート アクセス (VNet 統合)]** に変更することはできません。 VNet 統合によってサーバーへのアクセスをセキュリティで保護できるように、プライベート アクセスを指定してサーバーを作成することを強くお勧めします。 [プライベート アクセスの詳細については、概念に関する記事をご覧ください。](./concepts-networking.md)

    > :::image type="content" source="./media/quickstart-create-server-portal/networking.png" alt-text="[ネットワーク] タブを示すスクリーンショット。":::  

6. **[確認と作成]** を選択して、フレキシブル サーバーの構成を確認します。

7. **[作成]** を選択して、サーバーをプロビジョニングします。 プロビジョニングには数分かかる場合があります。

8. ツール バーの **[通知]** (ベル ボタン) を選択して、デプロイ プロセスを監視します。 デプロイが完了したら、 **[ダッシュボードにピン留めする]** を選択できます。これにより、Azure portal ダッシュボードにこのフレキシブル サーバーのタイルが作成されます。 このタイルは、サーバーの **[概要]** ページへのショートカットです。 **[リソースに移動]** を選択すると、サーバーの **[概要]** ページが開きます。

既定では、information_schema、mysql、performance_schema、sys の各データベースが、ご利用のサーバーに作成されます。

> [!NOTE]
> 接続の問題が発生しないように、Azure Database for MySQL フレキシブル サーバーで使用されるポート 3306 経由の送信トラフィックがネットワークで許可されているかどうかを確認してください。  

## <a name="connect-to-the-server-by-using-mysqlexe"></a>mysql.exe を使用してサーバーに接続する

プライベート アクセス (VNet 統合) を使用してフレキシブル サーバーを作成した場合は、サーバーと同じ仮想ネットワーク内のリソースからサーバーに接続する必要があります。 仮想マシンを作成し、フレキシブル サーバーと共に作成された仮想ネットワークに追加できます。 詳細については、[プライベート アクセス](how-to-manage-virtual-network-portal.md)の構成に関するドキュメントを参照してください。

パブリック アクセス (使用できる IP アドレス) を使用してフレキシブル サーバーを作成した場合は、サーバー上のファイアウォール規則のリストにローカル IP アドレスを追加できます。 詳細な手順については、[ファイアウォール規則の作成または管理](how-to-manage-firewall-portal.md)に関するドキュメントを参照してください。

ローカル環境からサーバーに接続するには、[mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) または [MySQL Workbench](./connect-workbench.md) のどちらかを使用できます。 

```bash
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl=true --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

**パブリック アクセス** を使用してフレキシブル サーバーをプロビジョニングした場合、以下に示したように、[Azure Cloud Shell](https://shell.azure.com/bash) から、プレインストールされた mysql クライアントを使用してフレキシブル サーバーに接続することもできます。

Azure Cloud Shell を使用してフレキシブル サーバーに接続するためには、Azure Cloud Shell からフレキシブル サーバーへのネットワーク アクセスを許可する必要があります。 そのためには、Azure portal で MySQL フレキシブル サーバーの **[ネットワーク]** ブレードに移動し、 **[ファイアウォール]** セクションの [Allow public access from any Azure service within Azure to this server]\(Azure 内の Azure サービスからこのサーバーへのパブリック アクセスを許可する\) というチェック ボックスをオンにし、[保存] をクリックして設定を保存します。

> [!NOTE]
> **[Allow public access from any Azure service within Azure to this server]\(Azure 内の Azure サービスからこのサーバーへのパブリック アクセスを許可する\)** チェック ボックスは、開発やテストの用途でのみオンにしてください。 すべての Azure サービス (または資産) に割り当てられた IP アドレスからの接続を許可するようにファイアウォールが構成されます。たとえば、他のユーザーのサブスクリプションからの接続も許可されます。

**[Try it]\(試してみる\)** をクリックして Azure Cloud Shell を起動し、次のコマンドを使用してフレキシブル サーバーに接続します。 実際のサーバー名、ユーザー名、パスワードをコマンドで使用します。 

```azurecli-interactive
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl=true --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

上のコマンドの実行後、フレキシブル サーバーに接続しているときに次のエラー メッセージが表示された場合、ファイアウォール規則の設定に不備があります。前述の [Allow public access from any Azure service within Azure to this server]\(Azure 内の Azure サービスからこのサーバーへのパブリック アクセスを許可する\) の設定が済んでいないか、オプションが保存されていません。 ファイアウォールの設定を再試行して、もう一度やり直してください。

ERROR 2002 (HY000):Can't connect to MySQL server on <servername> (115) (エラー 2002 (HY000): &lt;servername&gt; 上の MySQL サーバーに接続できません (115))

## <a name="clean-up-resources"></a>リソースをクリーンアップする
これで、リソース グループに Azure Database for MySQL フレキシブル サーバーが作成されました。 これらのリソースが今後不要である思われる場合は、リソース グループを削除してリソースを削除することも、単にこの MySQL サーバーを削除することもできます。 リソース グループを削除するには、次の手順のようにします。

1. Azure portal で、「**リソース グループ**」を検索して選択します。
1. リソース グループの一覧で、リソース グループの名前を選択します。
1. ご自分のリソース グループの **[概要]** ページで、 **[リソース グループの削除]** を選択します。
1. 確認のダイアログ ボックスでリソース グループの名前を入力し、 **[削除]** を選択します。

サーバーを削除するには、次に示すように、サーバーの **[概要]** ページで **[削除]** を選択します。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-server-portal/delete-server.png" alt-text="サーバーを削除する方法を示すスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [MySQL を使用した PHP (Laravel) Web アプリを作成する](tutorial-php-database-app.md)
