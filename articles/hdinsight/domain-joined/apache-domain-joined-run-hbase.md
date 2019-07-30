---
title: チュートリアル - Enterprise セキュリティ パッケージを使用して HDInsight に Apache HBase ポリシーを構成する - Azure
description: チュートリアル - Enterprise セキュリティ パッケージを使用して Azure HDInsight に HBase 用の Apache Ranger ポリシーを構成する方法について説明します。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: tutorial
ms.date: 06/18/2019
ms.openlocfilehash: 04592ba307cd696c20778d4a79f03be2eb0ac987
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274405"
---
# <a name="tutorial-configure-apache-hbase-policies-in-hdinsight-with-enterprise-security-package"></a>チュートリアル:Enterprise セキュリティ パッケージを使用して HDInsight に Apache HBase ポリシーを構成する

Enterprise セキュリティ パッケージ (ESP) の Apache HBase クラスター用の Apache Ranger ポリシーを構成する方法について説明します。 ESP クラスターは、ユーザーがドメイン資格情報で認証できるドメインに接続されます。 このチュートリアルでは、HBase テーブル内の異なる列ファミリへのアクセスを制限する 2 つの Ranger ポリシーを作成します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * ドメイン ユーザーの作成
> * Ranger ポリシーの作成
> * HBase クラスター内でのテーブルの作成
> * Ranger ポリシーのテスト

## <a name="before-you-begin"></a>開始する前に

* Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/)を作成してください。

* [Azure Portal](https://portal.azure.com/) にサインインします。

* [Enterprise セキュリティ パッケージで HDInsight HBase クラスター](apache-domain-joined-configure-using-azure-adds.md)を作成します。

## <a name="connect-to-apache-ranger-admin-ui"></a>Apache Ranger 管理 UI への接続

1. ブラウザーから、URL `https://<ClusterName>.azurehdinsight.net/Ranger/` を使用して Ranger 管理ユーザー インターフェイスに接続します。 必ず、`<ClusterName>` をお使いの HBase クラスターの名前に変更してください。

    > [!NOTE]  
    > Ranger の資格情報は、Hadoop クラスターの資格情報と同じではありません。 ブラウザーで Hadoop のキャッシュされた資格情報が使用されないように、新しい InPrivate ブラウザー ウィンドウを使用して Ranger 管理 UI に接続してください。

2. Azure Active Directory (AD) 管理者の資格情報を使用してサインインします。 Azure AD 管理者の資格情報は、HDInsight クラスターの資格情報や Linux HDInsight ノード SSH の資格情報と同じではありません。

## <a name="create-domain-users"></a>ドメイン ユーザーの作成

**sales_user1** および **marketing_user1** ドメイン ユーザーを作成する方法の詳細については、[Enterprise セキュリティ パッケージでの HDInsight クラスターの作成](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds)に関するページを参照してください。 運用シナリオでは、ドメイン ユーザーは Active Directory テナントに含まれます。

## <a name="create-hbase-tables-and-import-sample-data"></a>HBase テーブルの作成とサンプル データのインポート

SSH を使用して HBase クラスターに接続し、[Apache HBase シェル](https://hbase.apache.org/0.94/book/shell.html)を使用して HBase テーブルの作成、データの挿入、データのクエリを行うことができます。 詳細については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

### <a name="to-use-the-hbase-shell"></a>HBase シェルを使用するには

1. SSH から次の HBase コマンドを実行します。
   
    ```bash
    hbase shell
    ```

2. `Name` と `Contact` の 2 つの列ファミリを持つ HBase テーブル `Customers` を作成します。

    ```hbaseshell   
    create 'Customers', 'Name', 'Contact'
    list
    ```
3. いくつかのデータを挿入します。
    
    ```hbaseshell   
    put 'Customers','1001','Name:First','Alice'
    put 'Customers','1001','Name:Last','Johnson'
    put 'Customers','1001','Contact:Phone','333-333-3333'
    put 'Customers','1001','Contact:Address','313 133rd Place'
    put 'Customers','1001','Contact:City','Redmond'
    put 'Customers','1001','Contact:State','WA'
    put 'Customers','1001','Contact:ZipCode','98052'
    put 'Customers','1002','Name:First','Robert'
    put 'Customers','1002','Name:Last','Stevens'
    put 'Customers','1002','Contact:Phone','777-777-7777'
    put 'Customers','1002','Contact:Address','717 177th Ave'
    put 'Customers','1002','Contact:City','Bellevue'
    put 'Customers','1002','Contact:State','WA'
    put 'Customers','1002','Contact:ZipCode','98008'
    ```
4. テーブルの内容を表示します。
    
    ```hbaseshell
    scan 'Contacts'
    ```
    ![HDInsight Hadoop HBase シェル](./media/apache-domain-joined-run-hbase/hbase-shell-scan-table.png)

## <a name="create-ranger-policies"></a>Ranger ポリシーの作成

**sales_user1** と **marketing_user1** 用の Ranger ポリシーを作成します。

1. **Ranger 管理 UI** を開きます。 **[HBase]** の下の **[\<ClusterName>_hbase]** をクリックします。

   ![Apache Ranger 管理 UI](./media/apache-domain-joined-run-hbase/apache-ranger-admin-login.png)

2. **[List of Policies]\(ポリシーの一覧\)** 画面に、このクラスター用に作成されたすべての Ranger ポリシーが表示されます。 構成済みポリシーが 1 つリストされる場合があります。 **[Add New Policy]\(新しいポリシーの追加\)** をクリックします。

    ![Apache Ranger 管理 UI の作成ポリシー](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policies-list.png)

3. **[Create Policy]\(ポリシーの作成\)** 画面で、次の値を入力します。

   |**設定**  |**推奨値**  |
   |---------|---------|
   |ポリシー名  |  sales_customers_name_contact   |
   |[HBase Table]\(HBase テーブル\)   |  顧客 |
   |[HBase Column-family]\(HBase の列ファミリ\)   |  Name、Contact |
   |[HBase Column]\(HBase の列\)   |  * |
   |[Select Group]\(グループの選択\)  | |
   |ユーザーの選択  | sales_user1 |
   |アクセス許可  | 読み取り |

   トピック名には、次のワイルドカードを含めることができます。

   * `*` は、文字が 0 回以上出現することを示します。
   * `?` は、1 文字を示します。

   ![Apache Ranger 管理 UI の作成ポリシー](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-sales.png)

   >[!NOTE]
   >**[ユーザーの選択]** にドメイン ユーザーが自動的に設定されない場合は、Ranger が Azure AD と同期されるまでしばらく待ってください。

4. **[Add]** をクリックしてポリシーを保存します。

5. **[新しいポリシーの追加]** をクリックし、次の値を入力します。

   |**設定**  |**推奨値**  |
   |---------|---------|
   |ポリシー名  |  marketing_customers_contact   |
   |[HBase Table]\(HBase テーブル\)   |  顧客 |
   |[HBase Column-family]\(HBase の列ファミリ\)   |  Contact |
   |[HBase Column]\(HBase の列\)   |  * |
   |[Select Group]\(グループの選択\)  | |
   |ユーザーの選択  | marketing_user1 |
   |アクセス許可  | 読み取り |

   ![Apache Ranger 管理 UI の作成ポリシー](./media/apache-domain-joined-run-hbase/apache-ranger-hbase-policy-create-marketing.png)  

6. **[Add]** をクリックしてポリシーを保存します。

## <a name="test-the-ranger-policies"></a>Ranger ポリシーのテスト

構成された Ranger ポリシーに基づいて、**sales_user1** は、`Name` と `Contact` の両方の列ファミリの列のすべてのデータを表示できます。 **marketing_user1** は、`Contact` 列ファミリのデータのみを表示できます。

### <a name="access-data-as-salesuser1"></a>sales_user1 としてデータにアクセスする

1. クラスターへの新しい SSH 接続を開きます。 次のコマンドを使用して、クラスターにサインインします。

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. kinit コマンドを使用して、目的のユーザーのコンテキストに変更します。

   ```bash
   kinit sales_user1
   ```

2. HBase シェルを開き、`Customers` テーブルをスキャンします。

   ```hbaseshell
   hbase shell
   scan `Customers`
   ```

3. sales ユーザーは、`Name` 列ファミリの 2 列と `Contact` 列ファミリの 5 列を含む、`Customers` テーブルのすべての列を表示できることに注意してください。

    ```hbaseshell
    ROW                                COLUMN+CELL
     1001                              column=Contact:Address, timestamp=1548894873820, value=313 133rd Place
     1001                              column=Contact:City, timestamp=1548895061523, value=Redmond
     1001                              column=Contact:Phone, timestamp=1548894871759, value=333-333-3333
     1001                              column=Contact:State, timestamp=1548895061613, value=WA
     1001                              column=Contact:ZipCode, timestamp=1548895063111, value=98052
     1001                              column=Name:First, timestamp=1548894871561, value=Alice
     1001                              column=Name:Last, timestamp=1548894871707, value=Johnson
     1002                              column=Contact:Address, timestamp=1548894899174, value=717 177th Ave
     1002                              column=Contact:City, timestamp=1548895103129, value=Bellevue
     1002                              column=Contact:Phone, timestamp=1548894897524, value=777-777-7777
     1002                              column=Contact:State, timestamp=1548895103231, value=WA
     1002                              column=Contact:ZipCode, timestamp=1548895104804, value=98008
     1002                              column=Name:First, timestamp=1548894897419, value=Robert
     1002                              column=Name:Last, timestamp=1548894897487, value=Stevens
    2 row(s) in 0.1000 seconds
    ```

### <a name="access-data-as-marketinguser1"></a>marketing_user1 としてデータにアクセスする

1. クラスターへの新しい SSH 接続を開きます。 次のコマンドを使用して、**marketing_user1** としてサインインします。

   ```bash
   ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
   ```

1. kinit コマンドを使用して、目的のユーザーのコンテキストに変更します

   ```bash
   kinit marketing_user1
   ```

2. HBase シェルを開き、`Customers` テーブルをスキャンします。

    ```hbaseshell
    hbase shell
    scan `Customers`
    ```

3. marketing ユーザーは `Contact` 列ファミリの 5 列のみを表示できることに注意してください。

    ```hbaseshell
    ROW                                COLUMN+CELL
     1001                              column=Contact:Address, timestamp=1548894873820, value=313 133rd Place
     1001                              column=Contact:City, timestamp=1548895061523, value=Redmond
     1001                              column=Contact:Phone, timestamp=1548894871759, value=333-333-3333
     1001                              column=Contact:State, timestamp=1548895061613, value=WA
     1001                              column=Contact:ZipCode, timestamp=1548895063111, value=98052
     1002                              column=Contact:Address, timestamp=1548894899174, value=717 177th Ave
     1002                              column=Contact:City, timestamp=1548895103129, value=Bellevue
     1002                              column=Contact:Phone, timestamp=1548894897524, value=777-777-7777
     1002                              column=Contact:State, timestamp=1548895103231, value=WA
     1002                              column=Contact:ZipCode, timestamp=1548895104804, value=98008
    2 row(s) in 0.0730 seconds
    ```

9. Ranger UI から監査アクセス イベントを表示します。

   ![Ranger UI ポリシーの監査](./media/apache-domain-joined-run-hbase/apache-ranger-admin-audit.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このアプリケーションを引き続き使用しない場合は、次の手順で作成した HBase クラスターを削除します。

1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. 上部の**検索**ボックスに「**HDInsight**」と入力します。 
1. **[サービス]** の下の **[HDInsight クラスター]** を選択します。
1. 表示される HDInsight クラスターの一覧で、このチュートリアル用に作成したクラスターの横にある **[...]** をクリックします。 
1. **[削除]** をクリックします。 **[はい]** をクリックします。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Apache HBase の使用](../hbase/apache-hbase-tutorial-get-started-linux.md)
