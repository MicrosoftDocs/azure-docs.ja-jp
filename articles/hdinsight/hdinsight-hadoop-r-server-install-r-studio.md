---
title: "HDInsight の R Server に RStudio をインストールする - Azure | Microsoft Docs"
description: "HDInsight の R Server に RStudio をインストールする方法。"
services: hdinsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 918abb0d-8248-4bc5-98dc-089c0e007d49
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/19/2017
ms.author: bradsev
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 416420d855505508735ebd8526e93efdb230ad53
ms.contentlocale: ja-jp
ms.lasthandoff: 06/20/2017


---
# <a name="installing-rstudio-with-r-server-on-hdinsight"></a>HDInsight の R Server に RStudio をインストールする

この記事では、カスタム スクリプトを使用して、クラスターのエッジ ノードにコミュニティ (無料) バージョンの [RStudio Server](https://www.rstudio.com/products/rstudio-server/) をインストールする方法について説明します。 RStudio Server は、リモート クライアント向けにブラウザー ベースの IDE を提供するものであり、Linux で広く使用されています。 現在 R に関して、複数の統合開発環境 (IDE) が利用できます。その例を次に示します。

- Microsoft の [R Tools for Visual Studio](https://www.visualstudio.com/en-us/features/rtvs-vs.aspx) (RTVS) 
- [RStudio Server](https://www.rstudio.com/products/rstudio-server/) 
- Walware の Eclipse ベースの [StatET](http://www.walware.de/goto/statet)

HDInsight クラスターのエッジ ノードに RStudio Server をインストールする利点は、クラスターの R Server を使用した R スクリプトの開発と実行に IDE の機能をフルに活用できることです。 R コンソールを既定の構成で使用した場合と比べ、生産性を大幅に向上させることができます。

> [!NOTE]
> この記事で説明する手順は、クラスターをプロビジョニングするときに RStudio Server コミュニティ エディションのインストールを選択しなかった場合にのみ該当します。 プロビジョニング中に追加した場合は、クラスターの Azure Portal エントリの **[R Server ダッシュボード]** タイル、**[R Studio Server]** タイルの順にクリックしてアクセスします。 

商用ライセンスが提供される Pro バージョンの RStudio Server の方がよい場合は、[RStudio Server](https://www.rstudio.com/products/rstudio/download-server/) のインストール手順に従って操作してください。

> [!NOTE]
> このドキュメントの手順は、HDInsight クラスター上に R Server が必要です。ご利用の HDInsight クラスターに対し、[R インストール スクリプト アクション](hdinsight-hadoop-r-scripts-linux.md)を使用して R をインストールした場合、この手順は正しく機能しません。
>
> 

## <a name="prerequisites"></a>前提条件

* R Server がインストールされた Azure HDInsight クラスター。 手順については、 [HDInsight の R Server クラスターの概要](hdinsight-hadoop-r-server-get-started.md)に関するページを参照してください。
* SSH クライアント。 Linux および UNIX のディストリビューション、または Macintosh OS X の場合、オペレーティング システムに `ssh` コマンドが用意されています。 Windows には、[Cygwin](http://www.redhat.com/services/custom/cygwin/) と [OpenSSH オプション](https://www.youtube.com/watch?v=CwYSvvGaiWU)、または [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) が推奨されます。  

## <a name="install-rstudio-on-the-cluster-using-a-custom-script"></a>カスタム スクリプトを使用してクラスターに RStudio をインストールする

手順は次のようになります。

1. クラスターのエッジ ノードを特定します。 R Server がインストールされた HDInsight クラスターの場合、ヘッド ノードとエッジ ノードには次の命名規則があります。
   * ヘッド ノード `CLUSTERNAME-ssh.azurehdinsight.net`
   * エッジ ノード `CLUSTERNAME-ed-ssh.azurehdinsight.net` 

2. 手順 1. の命名パターンを使用して、クラスターのエッジ ノードに SSH でログインします。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

3. 接続したら、クラスターのルート ユーザーになります。 SSH セッションでは、次のコマンドを使用します。

        sudo su -

4. カスタム スクリプトをダウンロードして RStudio をインストールします。 次のコマンドを使用します。

        wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/InstallRStudio.sh

5. カスタム スクリプト ファイルのアクセス許可を変更し、スクリプトを実行します。 次のコマンドを使用します。

        chmod 755 InstallRStudio.sh
        ./InstallRStudio.sh

6. R Server がインストールされた HDInsight クラスターを作成するときに SSH パスワードを使用した場合は、この手順をスキップし、次に進むことができます。 クラスターを作成する代わりに SSH キーを使用した場合、SSH ユーザーのパスワードを設定する必要があります。 RStudio に接続するときに、このパスワードが必要になります。 次のコマンドを実行します。

        passwd USERNAME
        Current Kerberos password:
        New password:
        Retype new password:
        Current Kerberos password:


7. **現在の Kerberos パスワード**の入力を求められたら、**Enter** キーを押します。  `USERNAME` を HDInsight クラスターの SSH ユーザーに置き換える必要があります。 パスワードを正しく設定すると、次のようなメッセージが表示されます。

        passwd: password updated successfully

    SSH セッションを終了します。

8. クラスターへの SSH トンネルを作成するには、HDInsight クラスターの `ssh -L localhost:8787:localhost:8787 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net` からクライアント コンピューターにマッピングします。 SSH トンネルを作成してから、新しいブラウザー セッションを開く必要があります。

   * Linux クライアント、または [Cygwin](http://www.redhat.com/services/custom/cygwin/) を使用する Windows クライアントで、ターミナル セッションを開き、次のコマンドを実行します。

             ssh -L localhost:8787:localhost:8787 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

       **USERNAME** は、実際の HDInsight クラスターの SSH ユーザーに置き換えます。また、**CLUSTERNAME** は、HDInsight クラスターの名前に置き換えます。
       `-i id_rsa_key` を追加して、パスワードの代わりに SSH キーを使用することもできます。        
   * PuTTY を使用する Windows クライアントの場合

     1. PuTTY を開き、接続情報を入力します。
     2. ダイアログの左にある **[カテゴリ]** セクションで、**[接続]**、**[SSH]** の順に展開し、**[トンネル]** を選択します。
     3. **[SSH ポートの転送を管理するオプション]** フォームに次の情報を入力します。

        * **[ソース ポート]** - 転送するクライアント上のポート たとえば、「**8787**」と入力します。
        * **[Destination (宛先)]** - ローカル クライアント コンピューターにマッピングする宛先。 たとえば、「**localhost:8787**」と入力します。

            ![SSH トンネルを作成する](./media/hdinsight-hadoop-r-server-install-r-studio/createsshtunnel.png "SSH トンネルを作成する")

     4. **[追加]** をクリックして設定を追加し、**[開く]** をクリックして SSH 接続を開きます。
     5. メッセージが表示されたら、サーバーにログインして SSH セッションを確立し、トンネルを有効にします。

9. Web ブラウザーを開き、トンネルに入力したポートに基づいて次の URL を入力します。

        http://localhost:8787/ 

10. SSH のユーザー名とパスワードを入力してクラスターに接続するように求められます。 クラスターの作成時に SSH キーを使用した場合、手順 5. で作成したパスワードを入力する必要があります。

    ![R Studio に接続する](./media/hdinsight-hadoop-r-server-install-r-studio/connecttostudio.png "SSH トンネルを作成する")

11. RStudio のインストールが成功したかどうかをテストするには、R ベースの MapReduce および Spark ジョブをクラスターで実行するテスト スクリプトを実行します。 テスト スクリプトをダウンロードし、RStudio で実行するには、SSH コンソールに戻り、次のコマンドを入力します。

    *    R で Hadoop クラスターを作成した場合は、次のコマンドを実行します。

            wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi.r
    *    R で Spark クラスターを作成した場合は、次のコマンドを実行します。

            wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi_spark.r

12. RStudio に、ダウンロードしたテスト スクリプトが表示されます。 ファイルをダブルクリックして開き、ファイルの内容を選択して **[Run]** をクリックします。 **[Console]** ウィンドウに出力が表示されます。

   ![インストールをテストする](./media/hdinsight-hadoop-r-server-install-r-studio/test-r-script.png "インストールをテストする")

ほかには、`source(testhdi.r)` または `source(testhdi_spark.r)` と入力してスクリプトを実行する方法があります。

## <a name="see-also"></a>関連項目

* [Compute context options for R Server on HDInsight clusters (HDInsight クラスターでの R Server の計算コンテキストのオプション)](hdinsight-hadoop-r-server-compute-contexts.md)
* [HDInsight の R Server 向けの Azure Storage オプション](hdinsight-hadoop-r-server-storage.md)


