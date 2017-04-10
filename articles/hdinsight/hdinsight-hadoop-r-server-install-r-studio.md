---
title: "HDInsight の R Server に RStudio をインストールする | Microsoft Docs"
description: "HDInsight の R Server に RStudio をインストールする方法。"
services: hdinsight
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 918abb0d-8248-4bc5-98dc-089c0e007d49
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 970419650e1d3e22637733fdb4fba72ff27f5339
ms.lasthandoff: 03/25/2017


---
# <a name="installing-rstudio-with-r-server-on-hdinsight"></a>HDInsight の R Server に RStudio をインストールする
現在、R に使用できる統合開発環境 (IDE) は複数あります。たとえば、Microsoft から最近発表された [R Tools for Visual Studio](https://www.visualstudio.com/en-us/features/rtvs-vs.aspx) (RTVS) は、[RStudio](https://www.rstudio.com/products/rstudio-server/) のデスクトップおよびサーバー ツールのファミリです。また、Walware の Eclipse ベースの [StatET](http://www.walware.de/goto/statet) などがあります。 Linux で最もよく使用されているのは、[RStudio Server](https://www.rstudio.com/products/rstudio-server/) です。RStudio Server には、リモート クライアントに使用するブラウザーベースの IDE があります。  HDInsight クラスターのエッジ ノードに RStudio Server をインストールすると、クラスターの R Server を使用した R スクリプトの開発と実行に IDE の機能をフルに活用できるようになり、既定の R コンソールを使用した場合よりも生産性が大幅に向上します。

> [!NOTE]
> この記事で説明する手順は、クラスターをプロビジョニングするときに RStudio Server コミュニティ エディションのインストールを選択しなかった場合にのみ該当します。  プロビジョニング中に追加した場合は、クラスターの Azure Portal エントリの **[R Server ダッシュ ボード]** タイル、**[R Studio Server]** タイルの順にクリックしてアクセスします。 

この記事では、カスタム スクリプトを使用して、クラスターのエッジ ノードにコミュニティ (無料) バージョンの RStudio Server をインストールする方法について説明します。 商用ライセンスが提供される Pro バージョンの RStudio Server の方がよい場合は、 [RStudio Server](https://www.rstudio.com/products/rstudio/download-server/)のインストール手順に従って操作してください。

> [!NOTE]
> このドキュメントの手順には、HDInsight の R Server クラスターが必要です。[R インストール スクリプト アクション](hdinsight-hadoop-r-scripts-linux.md)を使用して R をインストールした HDInsight クラスターを使用している場合は、この手順は正しく機能しません。
>
> 

## <a name="prerequisites"></a>前提条件
* R Server がインストールされた Azure HDInsight クラスター。 手順については、 [HDInsight の R Server クラスターの概要](hdinsight-hadoop-r-server-get-started.md)に関するページを参照してください。
* SSH クライアント。 Linux および UNIX のディストリビューション、または Macintosh OS X の場合、オペレーティング システムに `ssh` コマンドが用意されています。 Windows には、[Cygwin](http://www.redhat.com/services/custom/cygwin/) と [OpenSSH オプション](https://www.youtube.com/watch?v=CwYSvvGaiWU)、または [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) が推奨されます。  

## <a name="install-rstudio-on-the-cluster-using-a-custom-script"></a>カスタム スクリプトを使用してクラスターに RStudio をインストールする
1. クラスターのエッジ ノードを特定します。 R Server がインストールされた HDInsight クラスターの場合、ヘッド ノードとエッジ ノードには次の命名規則があります。

   * ヘッド ノード `CLUSTERNAME-ssh.azurehdinsight.net`
   * エッジ ノード `CLUSTERNAME-ed-ssh.azurehdinsight.net` 
2. 上記の命名パターンを使用して、クラスターのエッジ ノードに SSH でログインします。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

3. 接続したら、クラスターのルート ユーザーになります。 SSH セッションでは、次のコマンドを使用します。

        sudo su -
4. カスタム スクリプトをダウンロードして RStudio をインストールします。 次のコマンドを使用します。

        wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/InstallRStudio.sh
5. カスタム スクリプト ファイルのアクセス許可を変更し、スクリプトを実行します。 次のコマンドを使用します。

        chmod 755 InstallRStudio.sh
        ./InstallRStudio.sh
6. R Server がインストールされた HDInsight クラスターを作成するときに SSH パスワードを使用した場合は、この手順をスキップし、次に進むことができます。 クラスターを作成する代わりに SSH キーを使用した場合、SSH ユーザーのパスワードを設定する必要があります。 RStudio に接続するときに、このパスワードが必要になります。 次のコマンドを実行します。 **現在の Kerberos パスワード**の入力を求められたら、そのまま **Enter** キーを押します。  `USERNAME` を HDInsight クラスターの SSH ユーザーに置き換える必要があります。

        passwd USERNAME
        Current Kerberos password:
        New password:
        Retype new password:
        Current Kerberos password:

    パスワードを正しく設定すると、次のようなメッセージが表示されます。

        passwd: password updated successfully

    SSH セッションを終了します。

7. クラスターへの SSH トンネルを作成するには、HDInsight クラスターの `ssh -L localhost:8787:localhost:8787 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net` からクライアント コンピューターにマッピングします。 SSH トンネルを作成してから、新しいブラウザー セッションを開く必要があります。

   * Linux クライアント、または [Cygwin](http://www.redhat.com/services/custom/cygwin/) を使用する Windows クライアントで、ターミナル セッションを開き、次のコマンドを実行します。

           ssh -L localhost:8787:localhost:8787 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

       **USERNAME** は、実際の HDInsight クラスターの SSH ユーザーに置き換えます。また、**CLUSTERNAME** は、HDInsight クラスターの名前に置き換えます。`-i id_rsa_key` を追加して、パスワードではなく、SSH キーも使用することもできます        
   * PuTTY を使用する Windows クライアントの場合

     1. PuTTY を開き、接続情報を入力します。
     2. ダイアログの左にある **[カテゴリ]** セクションで、**[接続]**、**[SSH]** の順に展開し、**[トンネル]** を選択します。
     3. **[SSH ポートの転送を管理するオプション]** フォームに次の情報を入力します。

        * **[ソース ポート]** - 転送するクライアント上のポート たとえば、「**8787**」と入力します。
        * **[Destination (宛先)]** - ローカル クライアント コンピューターにマッピングする宛先。 たとえば、「**localhost:8787**」と入力します。

        ![SSH トンネルを作成する](./media/hdinsight-hadoop-r-server-install-r-studio/createsshtunnel.png "SSH トンネルを作成する")
     4. **[追加]** をクリックして設定を追加し、**[開く]** をクリックして SSH 接続を開きます。
     5. プロンプトが表示されたら、サーバーにログインします。 これにより、SSH セッションが確立され、トンネルが有効になります。
8. Web ブラウザーを開き、トンネルに入力したポートに基づいて次の URL を入力します。

        http://localhost:8787/ 
9. SSH のユーザー名とパスワードを入力してクラスターに接続するように求められます。 クラスターの作成時に SSH キーを使用した場合、前述の手順 5 で作成したパスワードを入力する必要があります。

    ![R Studio に接続する](./media/hdinsight-hadoop-r-server-install-r-studio/connecttostudio.png "SSH トンネルを作成する")
10. RStudio のインストールが成功したかどうかをテストするには、R ベースの MapReduce および Spark ジョブをクラスターで実行するテスト スクリプトを実行します。 SSH コンソールに戻り、次のコマンドを入力して、テスト スクリプトをダウンロードし、RStudio で実行します。

*    R で Hadoop クラスターを作成した場合は、次のコマンドを実行します。

           wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi.r
*    R で Spark クラスターを作成した場合は、次のコマンドを実行します。

                    wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi_spark.r
11. RStudio に、ダウンロードしたテスト スクリプトが表示されます。 ファイルをダブルクリックして開き、ファイルの内容を選択して **[Run]**をクリックします。 **[Console]** ウィンドウに出力が表示されます。

   ![インストールをテストする](./media/hdinsight-hadoop-r-server-install-r-studio/test-r-script.png "インストールをテストする")

ほかには、`source(testhdi.r)` または `source(testhdi_spark.r)` と入力してスクリプトを実行する方法があります。

## <a name="see-also"></a>関連項目
* [Compute context options for R Server on HDInsight clusters (HDInsight クラスターでの R Server の計算コンテキストのオプション)](hdinsight-hadoop-r-server-compute-contexts.md)
* [HDInsight の R Server 向けの Azure Storage オプション](hdinsight-hadoop-r-server-storage.md)


