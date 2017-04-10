---
title: "SSH トンネリングを使用して Azure HDInsight サービスにアクセスする | Microsoft Docs"
description: "SSH トンネルを使用して、Linux ベースの HDInsight ノードでホストされている Web リソースを安全に閲覧する方法について説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 879834a4-52d0-499c-a3ae-8d28863abf65
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/06/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 4d9471fcdff26ac76312fcecdb427dca842f7ee3
ms.lasthandoff: 03/25/2017


---
# <a name="use-ssh-tunneling-to-access-ambari-web-ui-jobhistory-namenode-oozie-and-other-web-uis"></a>SSH トンネリングを使用して Ambari Web UI、JobHistory、NameNode、Oozie、およびその他の Web UI にアクセスする

Linux ベースの HDInsight クラスターは、インターネット経由で Ambari Web UI にアクセスできますが、UI の一部の機能にはアクセスできません。 たとえば、Ambari 経由で表示されるその他のサービスの Web UI などです。 Ambari Web UI の全機能を使用するには、クラスター ヘッドに対して SSH トンネルを使用する必要があります。

## <a name="why-use-an-ssh-tunnel"></a>SSH トンネルを使用する理由

Ambari の一部のメニューは、SSH トンネルなしでは完全に表示されません。これは、クラスターで実行されている他の Hadoop サービスが公開している Web サイトとサービスに依存しているためです。 多くの場合、これらの Web サイトはセキュリティで保護されていないので、インターネットで直接公開するのは安全ではありません。 場合によっては、Zookeeper ノードなど、他のクラスター ノードで Web サイトが実行されているサービスもあります。

次のサービスは Ambari Web UI が使用しているサービスですが、SSH トンネルなしではアクセスできません。

* JobHistory
* NameNode
* スレッド スタック
* Oozie Web UI
* HBase Master と Logs の UI

クラスターをカスタマイズするスクリプト アクションを使用する場合、インストールするサービスまたはユーティリティで Web UI を公開するには、SSH トンネルが必要です。 たとえば、スクリプト アクションを使用して Hue をインストールする場合、SSH トンネルを使用して Hue Web UI にアクセスする必要があります。

## <a name="what-is-an-ssh-tunnel"></a>SSH トンネルとは

[Secure Shell (SSH) トンネリング](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling)は、ローカル ワークステーション上のポートに送信されたトラフィックを、HDInsight クラスター ヘッド ノードへの SSH 接続経由でルーティングします。この要求は、ヘッド ノードから発信されたかのように解決されます。 応答は、トンネル経由でワークステーションにルーティングされます。

## <a name="prerequisites"></a>前提条件

Web トラフィックに SSH トンネルを使用するには、以下が必要です。

* SSH クライアント。 Linux および UNIX のディストリビューション、Macintosh OS X、および Bash on Windows 10 の場合、オペレーティング システムに `ssh` コマンドが用意されています。 `ssh` コマンドが用意されていない Windows バージョンの場合は、[PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) をお勧めします。
  
  > [!NOTE]
  > `ssh`または PuTTY 以外の SSH クライアントを使用する場合は、クライアントのマニュアルで SSH トンネルの確立方法を確認してください。

* SOCKS5 プロキシを使用するように構成できる Web ブラウザー。

    > [!WARNING]
    > Windows に組み込まれている SOCKS プロキシのサポートは SOCKS5 をサポートしていないため、このドキュメントの手順では機能しません。 以下のブラウザーは Windows のプロキシ設定に依存するため、現時点ではこのドキュメントの手順には使用できません。
    > 
    > * Microsoft Edge
    > * Microsoft Internet Explorer
    >
    > Google Chrome も Windows のプロキシ設定に依存します。 ただし、SOCKS5 をサポートする拡張機能をインストールできます。 [FoxyProxy Standard](https://chrome.google.com/webstore/detail/foxyproxy-standard/gcknhkkoolaabfmlnjonogaaifnjlfnp) をお勧めします。

## <a name="usessh"></a>SSH コマンドを使用してトンネルを作成する

次のように `ssh` コマンドを使用して、SSH トンネルを作成します。 **USERNAME** は、実際の HDInsight クラスターの SSH ユーザーに置き換えます。また、**CLUSTERNAME** は、HDInsight クラスターの名前に置き換えます。

```
ssh -C2qTnNf -D 9876 USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
```

これで、ローカル ポート 9876 へのトラフィックを SSH 経由でクラスターにルーティングする接続が作成されます。 オプションは次のとおりです。

* **D 9876** - トンネル経由でトラフィックをルーティングするローカル ポートです。
* **C** - すべてのデータを圧縮します (Web トラフィックの大部分はテキストであるため)。
* **2** - SSH プロトコル バージョンを強制的に 2 のみに指定します。
* **q** - Quiet モードです。
* **T** - pseudo-tty の割り当てを無効にします (ポートの転送だけを行うため)。
* **n** - STDIN を読み取らないようにします (ポートの転送だけを行うため)。
* **N** - リモート コマンドを実行しません (ポートの転送だけを行うため)。
* **f** - バックグラウンドで実行します。

SSH キーを使用してクラスターを構成した場合は、SSH 秘密キーのパスを `-i` パラメーターで指定する必要があることがあります。

コマンドが完了すると、ローカル コンピューター上でポート 9876 に送信されるトラフィックは Secure Sockets Layer (SSL) 経由でクラスターのヘッド ノードにルーティングされるようになり、そのヘッド ノードで生成されたように表示されます。

## <a name="useputty"></a>PuTTY を使用してトンネルを作成する

次の手順に従ってPuTTY を使用して、SSH トンネルを作成します。

1. PuTTY を開き、接続情報を入力します。 PuTTY に慣れていない場合は、[PuTTY のドキュメント (http://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html)](http://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html) を参照してください。

2. ダイアログの左にある **[カテゴリ]** セクションで、**[接続]**、**[SSH]** の順に展開し、**[トンネル]** を選択します。

3. **[SSH ポートの転送を管理するオプション]** フォームに次の情報を入力します。
   
   * **[ソース ポート]** - 転送するクライアント上のポート (**9876** など)。

   * **[宛先]** - Linux ベースの HDInsight クラスターの SSH アドレス (**mycluster-ssh.azurehdinsight.net** など)。

   * **[動的]** - 動的な SOCKS プロキシを有効にします。
     
     ![トンネリング オプションの画像](./media/hdinsight-linux-ambari-ssh-tunnel/puttytunnel.png)

4. **[追加]** をクリックして設定を追加し、**[開く]** をクリックして SSH 接続を開きます。

5. プロンプトが表示されたら、サーバーにログインします。 これにより、SSH セッションが確立され、トンネルが有効になります。

## <a name="use-the-tunnel-from-your-browser"></a>ブラウザーからトンネルを使用する

> [!IMPORTANT]
> このセクションの手順では、Mozilla FireFox ブラウザーを使用します。Mozilla FireFox はすべてのプラットフォームで同じプロキシ設定を提供するからです。 その他の最新ブラウザー (Google Chrome など) では、トンネルを使用するために FoxyProxy などの拡張機能が必要になる場合があります。

1. ブラウザーを、**localhost** とトンネルの作成時に使用したポートを **SOCKS v5** プロキシとして使用するように構成します。 Firefox の設定は次のようになります。 9876 以外のポートを使用する場合は、そのポート番号に変更します。
   
    ![Firefox の設定の画像](./media/hdinsight-linux-ambari-ssh-tunnel/firefoxproxy.png)
   
   > [!NOTE]
   > **リモート DNS** を選択すると、ドメイン ネーム システム (DNS) 要求は HDInsight クラスターを使用して解決されます。 選択しない場合、DNS はローカルに解決されます。

2. Firefox でプロキシ設定を有効または無効にして、 [http://www.whatismyip.com/](http://www.whatismyip.com/) などのサイトにアクセスして、トラフィックがトンネルを経由しているかどうかを確認します。 設定が有効の場合、返される IP アドレスは Microsoft Azure データセンター内のコンピューターの IP アドレスになります。

## <a name="verify-with-ambari-web-ui"></a>Ambari Web UI を使用して確認する

クラスターが確立したら、次の手順で、Ambari Web からサービス Web UI にアクセスできることを確認します。

1. ブラウザーで http://headnodehost:8080 を開きます。 この `headnodehost` アドレスはトンネル経由でクラスターに送信され、Ambari が実行されているヘッド ノードに解決されます。 プロンプトが表示されたら、クラスターの管理者ユーザー名 (admin) とパスワードを入力します。 Ambari Web UI からもう一度入力を求められることがあります。 その場合は、情報を再入力します。
   
   > [!NOTE]
   > http://headnodehost:8080 アドレスを使用してクラスターに接続すると、HTTP を使用して、Ambari が実行されているヘッド ノードにトンネル経由で直接接続され、通信は SSH トンネルを使用して保護されます。 トンネルを使用せずにインターネット経由で接続すると、通信は HTTPS を使用して保護されます。 HTTPS を使用してインターネット経由で接続するには、https://CLUSTERNAME.azurehdinsight.net を使用します。**CLUSTERNAME** はクラスターの名前です。

2. Ambari Web UI でページの左側にある一覧から [HDFS] を選択します。
   
    ![HDFS が選択された画像](./media/hdinsight-linux-ambari-ssh-tunnel/hdfsservice.png)
3. HDFS サービスの情報が表示されたら、 **[Quick Links]**を選択します。 クラスターのヘッド ノードの一覧が表示されます。 ヘッド ノードのいずれかを選択し、 **[NameNode UI]**を選択します。
   
    ![QuickLinks メニューが展開された画像](./media/hdinsight-linux-ambari-ssh-tunnel/namenodedropdown.png)
   
   > [!NOTE]
   > インターネット接続が低速な場合、またはヘッド ノードの負荷が高い場合は、 **[Quick Links]**を選択したときにメニューではなく待機インジケーターが表示されることがあります。 その場合は、サーバーからデータが取得されるまで 1 ～ 2 分待ってから、改めて一覧を表示してみてください。
   > 
   > モニターの解像度が低い場合、またはブラウザー ウィンドウが最大化されていない場合は、 **[Quick Links]** メニューの一部のエントリが画面の右側で切れて表示されることがあります。 その場合は、マウスを使用してメニューを拡大するか、右矢印キーを使用して画面を右にスクロールして、メニューの残りを表示します。
   > 
   > 
4. 次のようなページが表示されます。
   
    ![NameNode UI の画像](./media/hdinsight-linux-ambari-ssh-tunnel/namenode.png)
   
   > [!NOTE]
   > このページの URL に注意してください。**http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster** この URL は、ノードの内部の完全修飾ドメイン名 (FQDN) を使用しているので、アクセスするには SSH トンネルを使用する必要があります。
   > 
   > 

## <a name="next-steps"></a>次のステップ

ここでは、SSH トンネルを作成し、使用する方法について説明しました。Ambari を使用してクラスターを監視および管理する方法については、次を参照してください。

* [Ambari を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md)

SSH の使用方法の詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページをご覧ください。


