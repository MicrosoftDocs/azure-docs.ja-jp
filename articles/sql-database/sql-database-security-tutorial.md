---
title: "Azure SQL データベースのセキュリティ保護 | Microsoft Docs"
description: "Azure SQL データベースをセキュリティで保護する手法と機能について説明します。"
services: sql-database
documentationcenter: 
author: DRediske
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,security
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 06/28/2017
ms.author: daredis
ms.translationtype: HT
ms.sourcegitcommit: 6e76ac40e9da2754de1d1aa50af3cd4e04c067fe
ms.openlocfilehash: 4bc09ad13ed0c9dc9257e9c75ec6f9ff3d689a0b
ms.contentlocale: ja-jp
ms.lasthandoff: 07/31/2017

---
# <a name="secure-your-azure-sql-database"></a>Azure SQL データベースのセキュリティ保護

SQL Database では、ファイアウォール規則、ユーザーに ID の入力を求める認証メカニズム、データに対する承認 (ロール ベースのメンバーシップとアクセス許可のほか、行レベルのセキュリティと動的データ マスクを使用) を使用して、データベースへのアクセスを制限することで、データを保護します。

いくつかの簡単な手順に従うだけで、悪意のあるユーザーや未承認のアクセスからデータベースを今まで以上に強力に保護できるようになります。 このチュートリアルで学習する内容は次のとおりです。 

> [!div class="checklist"]
> * Azure Portal でサーバーにサーバーレベルのファイアウォール規則を設定する
> * SSMS を利用し、データベースにデータベースレベルのファイアウォール規則を設定する
> * セキュリティで保護された接続文字列を使用してデータベースに接続する
> * ユーザー アクセスを管理する
> * 暗号化でデータを保護する
> * SQL Database の監査を有効にする
> * SQL Database の脅威の検出を有効にする

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下のものが必要です。

- 最新バージョンの [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) がインストールされていること。 
- Microsoft Excel がインストールされていること。
- Azure SQL のサーバーとデータベースが作成されていること - 「[Azure ポータルで Azure SQL データベースを作成する](sql-database-get-started-portal.md)」、「[Azure CLI を使用して単一の Azure SQL データベースを作成する](sql-database-get-started-cli.md)」、「[PowerShell を使用して単一の Azure SQL データベースを作成する](sql-database-get-started-powershell.md)」を参照してください。 

## <a name="log-in-to-the-azure-portal"></a>Azure ポータルにログインする

[Azure ポータル](https://portal.azure.com/)にログインします。

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Azure Portal でサーバーレベルのファイアウォール規則を作成する

SQL データベースは、ファイアウォールによって保護されます。 既定では、サーバーとサーバー内部のデータベースに対する接続は、他の Azure サービスからの接続を除き、すべて拒否されます。 詳細については、「[Azure SQL Database のサーバーレベルとデータベースレベルのファイアウォール規則](sql-database-firewall-configure.md)」を参照してください。

最も安全な構成は、[Azure サービスへのアクセスを許可] をオフにしておくことです。 Azure VM またはクラウド サービスからデータベースに接続する必要がある場合は、[予約済み IP](../virtual-network/virtual-networks-reserved-public-ip.md) を作成し、予約済み IP アドレスだけにファイアウォール経由のアクセスを許可する必要があります。 

サーバーが特定の IP アドレスからの接続を許可するように、次の手順に従って、[SQL Database のサーバーレベルのファイアウォール規則](sql-database-firewall-configure.md)を作成します。 

> [!NOTE]
> 前のチュートリアルまたはクイックスタートのいずれかを利用して Azure でサンプル データベースを作成しているとき、それらのチュートリアルで利用した IP アドレスと同じアドレスを持つコンピューターでこのチュートリアルを実行している場合、サーバーレベルのファイアウォール規則が既に作成されているため、この手順を省略できます。
>

1. 左側のメニューで **[SQL データベース]** をクリックし、**[SQL データベース]** ページで、ファイアウォール規則を構成するデータベースをクリックします。 このデータベースの概要ページが開くと、完全修飾サーバー名 (**mynewserver-20170313.database.windows.net** など) や追加の構成オプションが表示されます。

      ![サーバーのファイアウォール規則](./media/sql-database-security-tutorial/server-firewall-rule.png) 

2. 前の画像に示されているように、ツール バーの **[サーバー ファイアウォールの設定]** をクリックします。 SQL Database サーバーの **[ファイアウォール設定]** ページが開きます。 

3. ツール バーの **[クライアント IP の追加]** をクリックして、ポータルへの接続に使用されているコンピューターのパブリック IP アドレスを追加するか、ファイアウォール規則を手動で入力し、**[保存]** をクリックします。

      ![サーバーのファイアウォール規則の設定](./media/sql-database-security-tutorial/server-firewall-rule-set.png) 

4. **[OK]** をクリックしてから、**[X]** をクリックして **[ファイアウォール設定]** ページを閉じます。

これで、指定した IP アドレスまたは IP アドレスの範囲を使用して、サーバー内の任意のデータベースに接続できるようになりました。

> [!NOTE]
> SQL Database の通信は、ポート 1433 上で行われます。 企業ネットワーク内から接続しようとしても、ポート 1433 での送信トラフィックがネットワークのファイアウォールで禁止されている場合があります。 その場合、会社の IT 部門によってポート 1433 が開放されない限り、Azure SQL Database サーバーに接続することはできません。
>

## <a name="create-a-database-level-firewall-rule-using-ssms"></a>SSMS を使用してデータベースレベルのファイアウォール規則を作成する

データベースレベルのファイアウォール規則では、同じ論理サーバー内の異なるデータベースに異なるファイアウォール設定を作成したり、移動可能なファイアウォール規則を作成したりできます。移動可能なファイアウォール規則は、[フェールオーバー](sql-database-geo-replication-overview.md)の際、SQL サーバーに残らず、データベースの後を付いて移動します。 データベースレベルのファイアウォール規則の構成は、Transact-SQL ステートメントを使用して、最初のサーバーレベルのファイアウォール規則を構成した後にのみ行うことができます。 詳細については、「[Azure SQL Database のサーバーレベルとデータベースレベルのファイアウォール規則](sql-database-firewall-configure.md)」を参照してください。

データベース固有のファイアウォール規則を作成するには、次の手順に従います。

1. [SQL Server Management Studio](./sql-database-connect-query-ssms.md) などを使用して、データベースに接続します。

2. オブジェクト エクスプローラーで、ファイアウォール規則を追加するデータベースを右クリックし、**[新しいクエリ]** をクリックします。 データベースに接続された空のクエリ ウィンドウが開きます。

3. クエリ ウィンドウで、IP アドレスをパブリック IP アドレスに変更し、次のクエリを実行します。

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

4. ツール バーの **[実行]** をクリックして、ファイアウォール規則を作成します。

## <a name="view-how-to-connect-an-application-to-your-database-using-a-secure-connection-string"></a>安全な接続文字列を利用し、データベースにアプリケーションを接続する方法を表示する

クライアント アプリケーションと SQL Database の間に暗号化された安全な接続を確立するには、接続文字列を次のように構成する必要があります。

- 暗号化接続を要求する
- サーバー証明書を信頼しない 

そうすることで、トランスポート層セキュリティ (TLS) を使用して接続が確立され、中間者攻撃のリスクが軽減されます。 次のスクリーンショットで ADO.net の場合が示されているように、サポートされているクライアント ドライバー用の SQL Database の正しく構成されている接続文字列を Azure Portal から取得できます。

1. 左側のメニューの **[SQL データベース]** を選択し、**[SQL データベース]** ページで目的のデータベースをクリックします。

2. データベースの **[概要]** ページで、**[データベース接続文字列の表示]** をクリックします。

3. 完全な **ADO.NET** 接続文字列を確認します。

    ![ADO.NET の接続文字列](./media/sql-database-security-tutorial/adonet-connection-string.png)

## <a name="creating-database-users"></a>データベース ユーザーの作成

ユーザーを作成する前に、まず、Azure SQL Database でサポートされている 2 種類の認証のうちいずれかを選択する必要があります。 

**SQL 認証**。論理サーバー内の特定のデータベースのコンテキストでのみ有効であるログインとユーザーのユーザー名とパスワードを使用します。 

**Azure Active Directory 認証**。Azure Active Directory によって管理されているユーザー ID を使用します。 

SQL Database に対する認証で [Azure Active Directory](./sql-database-aad-authentication.md) を使用する場合は、作業を続行する前に、設定済みの Azure Active Directory が存在する必要があります。

SQL 認証を使用するユーザーを作成するには、次の手順に従います。

1. [SQL Server Management Studio](./sql-database-connect-query-ssms.md) などを使用して、サーバー管理者の資格情報でデータベースに接続します。

2. オブジェクト エクスプローラーで、新しいユーザーを追加するデータベースを右クリックし、**[新しいクエリ]** をクリックします。 選択したデータベースに接続されている空のクエリ ウィンドウが開きます。

3. クエリ ウィンドウに次のクエリを入力します。

    ```sql
    CREATE USER ApplicationUser WITH PASSWORD = 'YourStrongPassword1';
    ```

4. ツール バーの **[実行]** をクリックして、ユーザーを作成します。

5. 既定では、ユーザーはデータベースに接続できますが、データを読み取ったり書き込んだりするアクセス許可は付与されていません。 新しく作成したユーザーにこれらのアクセス許可を付与するには、新しいクエリ ウィンドウで次の 2 つのコマンドを実行します。

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUser;
    ```

新しいユーザーの作成のような管理者タスクを実行する必要がない場合は、データベースに接続するために、これらの非管理者アカウントをデータベース レベルで作成することをお勧めします。 Azure Active Directory を使用して認証を行う方法については、[Azure Active Directory のチュートリアル](./sql-database-aad-authentication-configure.md)を参照してください。


## <a name="protect-your-data-with-encryption"></a>暗号化でデータを保護する

Azure SQL Database の Transparent Data Encryption (TDE) では、保存データを自動的に暗号化します。暗号化されたデータベースにアクセスするアプリケーションを変更する必要はありません。 新しく作成されたデータベースの場合、TDE は既定でオンです。 データベースの TDE を有効にするには、または TDE がオンになっていることを確認するには、次の手順を行います。

1. 左側のメニューの **[SQL データベース]** を選択し、**[SQL データベース]** ページで目的のデータベースをクリックします。 

2. **[Transparent Data Encryption]** をクリックして、TDE の構成ページを開きます。

    ![Transparent Data Encryption](./media/sql-database-security-tutorial/transparent-data-encryption-enabled.png)

3. 必要に応じて、**[データの暗号化]** をオンにし、**[保存]** をクリックします。

暗号化プロセスは、バックグラウンドで開始されます。 [SQL Server Management Studio](./sql-database-connect-query-ssms.md) を使用して SQL Database に接続し、`sys.dm_database_encryption_keys` ビューの encryption_state 列に対してクエリを実行すると、進行状況を監視できます。

## <a name="enable-sql-database-auditing-if-necessary"></a>必要に応じて、SQL Database の監査を有効にする

Azure SQL Database の監査では、データベース イベントを追跡し、Azure Storage アカウントの監査ログにイベントを書き込みます。 監査により、規制に対するコンプライアンスの維持、データベース活動の理解、セキュリティ違反の疑いを示す差異や異常に対する洞察が容易になります。 SQL データベースの既定の監査ポリシーを作成するには、次の手順に従います。

1. 左側のメニューの **[SQL データベース]** を選択し、**[SQL データベース]** ページで目的のデータベースをクリックします。 

2. [設定] ブレードで、**[監査と脅威の検出]** を選択します。 サーバーレベルの監査が無効になっており、このコンテキストでサーバーの監査設定を表示または変更する **[サーバー設定を表示]** リンクがあることをご確認ください。

    ![監査ブレード](./media/sql-database-security-tutorial/auditing-get-started-settings.png)

3. サーバー レベルで指定されたものと異なる種類 (または場所) の監査を有効にする必要がある場合には、[監査] を**オン**にして、**BLOB** 監査タイプを選択します。 サーバーの BLOB 監査が有効になっている場合、データベース構成監査とサーバー BLOB 監査が横並びで存在します。

    ![監査を有効化](./media/sql-database-security-tutorial/auditing-get-started-turn-on.png)

4. **[容量の詳細]** を選択して [監査ログの容量] ブレードを開きます。 ログを保存する Azure Storage アカウントと古いログを削除するタイミングを指定するリテンション期間を選択し、一番下にある **[OK]** をクリックします。 

   > [!TIP]
   > 監査レポートのテンプレートを最大限活用するには、すべての監査済みデータベースに同じストレージ アカウントを使用してください。
   > 

5. [ **Save**] をクリックします。

> [!IMPORTANT]
> 監査対象イベントをカスタマイズする場合、PowerShell または REST API から実行できます。詳細については、「[Automation (PowerShell / REST API)](sql-database-auditing.md#subheading-7)」セクションを参照してください。
>

## <a name="enable-sql-database-threat-detection"></a>SQL Database の脅威の検出を有効にする

脅威の検出で提供される新しいセキュリティ階層は、異常なアクティビティに対するセキュリティ アラートを提供することによって、発生する可能性のある脅威をユーザーが検出して対応できるようにします。 ユーザーは、SQL Database の監査を使って疑わしいイベントを調査し、データベース内のデータに対するアクセス、侵害、または悪用の試行による結果かどうかを判断できます。 脅威の検出を使用するとデータベースに対する潜在的な脅威に簡単に対処でき、セキュリティの専門家である必要や、高度なセキュリティ監視システムを管理する必要はありません。
たとえば、脅威の検出は、SQL インジェクションの可能性を示す特定の異常なデータベース アクティビティを検出します。 SQL インジェクションはインターネットでの一般的な Web アプリケーションのセキュリティの問題の 1 つであり、データ駆動型アプリケーションの攻撃に使用されます。 攻撃者は、アプリケーションの脆弱性を利用してアプリケーションの入力フィールドに悪意のある SQL ステートメントを挿入し、データベースのデータを侵害または変更します。

1. 監視する SQL データベースの構成ブレードに移動します。 [設定] ブレードで、**[監査と脅威の検出]** を選択します。

    ![ナビゲーション ウィンドウ](./media/sql-database-security-tutorial/auditing-get-started-settings.png)
2. **[監査と脅威の検出]** 構成ブレードで、監査を **[ON]** にすると、脅威の検出の設定が表示されます。

3. 脅威の検出を **[ON]** にします。

4. 異常なデータベース アクティビティが検出されたときにセキュリティ アラートを受け取る電子メールのリストを構成します。

5. **[監査と脅威の検出]** ブレードの **[保存]** をクリックして、新規または更新済みの監査と脅威の検出のポリシーを保存します。

    ![ナビゲーション ウィンドウ](./media/sql-database-security-tutorial/td-turn-on-threat-detection.png)

    異常なデータベース アクティビティが検出されると、電子メールで通知を受け取ります。 電子メールでは、異常なアクティビティの特徴、データベース名、サーバー名、イベントの時刻など、疑わしいセキュリティ イベントについての情報が提供されます。 さらに、データベースへの潜在的な脅威の考えられる原因および調査や緩和のための推奨されるアクションについての情報も提供されます。 以下の手順では、このような電子メールを受信した場合の対処について説明します。

    ![脅威の検出の電子メール](./media/sql-database-threat-detection-get-started/4_td_email.png)

6. 電子メールの **[Azure SQL 監査ログ]** リンクをクリックすると、Azure Portal が起動され、疑わしいイベントの時刻前後の関連する監査レコードが表示されます。

    ![監査レコード](./media/sql-database-threat-detection-get-started/5_td_audit_records.png)

7. 監査レコードをクリックすると、SQL ステートメント、エラーの理由、クライアントの IP など、疑わしいデータベース アクティビティについての詳細が表示されます。

    ![レコードの詳細](./media/sql-database-security-tutorial/6_td_audit_record_details.png)

8. [監査レコード] ブレードの **[Excel で開く]** をクリックすると、あらかじめ構成されている Excel テンプレートが開き、疑わしいイベントの時刻前後の監査ログがインポートされて詳細な分析が実行されます。

    > [!NOTE]
    > Excel 2010 以降では、Power Query と**高速結合**の設定が必要です。

    ![Excel でレコードを開く](./media/sql-database-threat-detection-get-started/7_td_audit_records_open_excel.png)

9. **[高速結合]** の設定を構成するには、**[POWER QUERY]** リボン タブの **[オプション]** を選択して [オプション] ダイアログ ボックスを表示します。 [プライバシー] セクションを選択し、2 番目のオプション [プライバシー レベルを無視し、可能であればパフォーマンスを向上させる] をオンにします。

    ![Excel の高速結合](./media/sql-database-threat-detection-get-started/8_td_excel_fast_combine.png)

10. SQL 監査ログをロードするには、[設定] タブのパラメーターが正しく設定されていることを確認してから、データのリボンを選択し、[すべて更新] ボタンをクリックします。

    ![Excel のパラメーター](./media/sql-database-threat-detection-get-started/9_td_excel_parameters.png)

11. **[SQL 監査ログ]** シートに結果が表示されます。このシートでは、検出された異常なアクティビティを詳細に分析し、アプリケーションでのセキュリティ イベントの影響を軽減できます。


## <a name="next-steps"></a>次のステップ
いくつかの簡単な手順に従うだけで、悪意のあるユーザーや未承認のアクセスからデータベースを今まで以上に強力に保護できるようになります。 このチュートリアルで学習する内容は次のとおりです。 

> [!div class="checklist"]
> * サーバーまたはデータベースのファイアウォール規則をセットアップする
> * セキュリティで保護された接続文字列を使用してデータベースに接続する
> * ユーザー アクセスを管理する
> * 暗号化でデータを保護する
> * SQL Database の監査を有効にする
> * SQL Database の脅威の検出を有効にする

> [!div class="nextstepaction"]
>[SQL Database のパフォーマンスを向上させる](sql-database-performance-tutorial.md)


