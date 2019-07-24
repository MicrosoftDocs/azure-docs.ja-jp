---
title: SSH トンネリングを使用して Azure HDInsight にアクセスする
description: SSH トンネルを使用して、Linux ベースの HDInsight ノードでホストされている Web リソースを安全に閲覧する方法について説明します。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/15/2018
ms.author: hrasheed
ms.openlocfilehash: 03c86aa069300f88b61752ebd3223e424f6e9c96
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2019
ms.locfileid: "54382608"
---
# <a name="use-ssh-tunneling-to-access-apache-ambari-web-ui-jobhistory-namenode-apache-oozie-and-other-web-uis"></a>SSH トンネリングを使用して Apache Ambari Web UI、JobHistory、NameNode、Apache Oozie、およびその他の Web UI にアクセスする

HDInsight クラスターは、インターネット経由で Apache Ambari Web UI にアクセスできますが、一部の機能には SSH トンネルが必要です。 たとえば、Apache Oozie サービスの web UI は、SSh トンネルせず、インターネット経由でアクセスできません。

## <a name="why-use-an-ssh-tunnel"></a>SSH トンネルを使用する理由

Ambari のメニューのいくつかは、SSH トンネル経由でのみ機能します。 これらのメニューは、ワーカー ノードなどの他のノード上で実行している Web サイトとサービスに依存します。

次の Web UI には SSH トンネルが必要です。

* JobHistory
* NameNode
* スレッド スタック
* Oozie Web UI
* HBase Master と Logs の UI

クラスターをカスタマイズするスクリプト アクションを使用する場合、インストールするサービスまたはユーティリティで Web UI を公開するには、SSH トンネルが必要です。 たとえば、スクリプト アクションを使用して Hue をインストールする場合、SSH トンネルを使用して Hue Web UI にアクセスする必要があります。

> [!IMPORTANT]  
> 仮想ネットワーク経由で HDInsight に直接アクセスできる場合は、SSH トンネルを使用する必要はありません。 仮想ネットワーク経由で HDInsight に直接アクセスする例については、「[オンプレミス ネットワークへの HDInsight の接続](connect-on-premises-network.md)」をご覧ください。

## <a name="what-is-an-ssh-tunnel"></a>SSH トンネルとは

[Secure Shell (SSH) のトンネリング](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling)HDInsight 上のヘッド ノードにローカル コンピューター上のポートを接続します。 ローカル ポートに送信されるトラフィックは、ヘッド ノードへの SSH 接続を介してルーティングされます。 要求は、ヘッド ノードから送信された場合と同じように解決されます。 応答は、トンネル経由でワークステーションにルーティングされます。

## <a name="prerequisites"></a>前提条件

* SSH クライアント ほとんどのオペレーティング システムには、`ssh` コマンドによる SSH クライアントが用意されています。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

* SOCKS5 プロキシを使用するように構成できる Web ブラウザー。

    > [!WARNING]  
    > Windows インターネット設定に組み込まれている SOCKS プロキシサポートは SOCKS5 をサポートしていないため、このドキュメントの手順では機能しません。 以下のブラウザーは Windows のプロキシ設定に依存するため、現時点ではこのドキュメントの手順には使用できません。
    >
    > * Microsoft Edge
    > * Microsoft Internet Explorer
    >
    > Google Chrome も Windows のプロキシ設定に依存します。 ただし、SOCKS5 をサポートする拡張機能をインストールできます。 [FoxyProxy Standard](https://chrome.google.com/webstore/detail/foxyproxy-standard/gcknhkkoolaabfmlnjonogaaifnjlfnp) をお勧めします。

## <a name="usessh"></a>SSH コマンドを使用してトンネルを作成する

次のように `ssh` コマンドを使用して、SSH トンネルを作成します。 **sshuser** を実際の HDInsight クラスターの SSH ユーザーに置き換えます。また、**sshuser** は、HDInsight クラスターの名前に置き換えます:

```bash
ssh -C2qTnNf -D 9876 sshuser@clustername-ssh.azurehdinsight.net
```

このコマンドは、ローカル ポート 9876 へのトラフィックを SSH 経由でクラスターにルーティングする接続を作成します。 オプションは次のとおりです。

* **D 9876** - トンネル経由でトラフィックをルーティングするローカル ポートです。
* **C** - すべてのデータを圧縮します (Web トラフィックの大部分はテキストであるため)。
* **2** - SSH プロトコル バージョンを強制的に 2 のみに指定します。
* **q** - Quiet モードです。
* **T** - pseudo-tty の割り当てを無効にします (ポートの転送だけを行うため)。
* **n** - STDIN を読み取らないようにします (ポートの転送だけを行うため)。
* **N** - リモート コマンドを実行しません (ポートの転送だけを行うため)。
* **f** - バックグラウンドで実行します。

コマンドが完了すると、ローカル コンピューターのポート 9876 に送信されるトラフィックは、クラスターのヘッド ノードにルーティングされるようになります。

## <a name="useputty"></a>PuTTY を使用してトンネルを作成する

[PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty) は、Windows 用のグラフィカル SSH クライアントです。 PuTTY に慣れていない場合は、[PuTTY のドキュメント](https://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html)を参照してください。 PuTTY を使って SSH トンネルを作成するには、次の手順のようにします。

### <a name="create-or-load-a-session"></a>セッションの作成または読み込み

1. PuTTY を開いて、左側のメニューで **[セッション]** が選択されていることを確認します。 セッションが既に保存されている場合は、**[保存されたセッション]** リストからセッション名を選択し、**[読み込み]** をクリックします。

1. まだ保存されたセッションがない場合は、接続情報を入力します。
    * **[ホスト名] (または IP アドレス)** - HDInsight クラスターの SSH アドレス (**mycluster-ssh.azurehdinsight.net** など)。
    * **[ポート]** - 22
    * **[接続の種類]** - SSH
1. **[保存]**

    ![SSH セッションを作成する](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-create-putty-session.png)

2. ダイアログの左にある **[カテゴリ]** セクションで、**[接続]**、**[SSH]** の順に展開し、**[トンネル]** を選択します。

3. **[SSH ポートの転送を管理するオプション]** フォームに次の情報を入力します。
   
   * **[ソース ポート]** - 転送するクライアント上のポート (**9876** など)。

   * **[宛先]** - HDInsight クラスターの SSH アドレス (**mycluster-ssh.azurehdinsight.net** など)。

   * **[動的]** - 動的な SOCKS プロキシを有効にします。
     
     ![トンネリング オプションの画像](./media/hdinsight-linux-ambari-ssh-tunnel/puttytunnel.png)

4. **[追加]** をクリックして設定を追加し、**[開く]** をクリックして SSH 接続を開きます。

5. プロンプトが表示されたら、サーバーにログインします。

## <a name="use-the-tunnel-from-your-browser"></a>ブラウザーからトンネルを使用する

> [!IMPORTANT]  
> このセクションの手順では、Mozilla FireFox ブラウザーを使用します。Mozilla FireFox はすべてのプラットフォームで同じプロキシ設定を提供するからです。 その他の最新ブラウザー (Google Chrome など) では、トンネルを使用するために FoxyProxy などの拡張機能が必要になる場合があります。

1. ブラウザーを、**localhost** とトンネルの作成時に使用したポートを **SOCKS v5** プロキシとして使用するように構成します。 Firefox の設定は次のようになります。 9876 以外のポートを使用する場合は、そのポート番号に変更します。
   
    ![Firefox の設定の画像](./media/hdinsight-linux-ambari-ssh-tunnel/firefoxproxy.png)
   
   > [!NOTE]  
   > **リモート DNS** を選択すると、ドメイン ネーム システム (DNS) 要求は HDInsight クラスターを使用して解決されます。 この設定は、クラスターのヘッド ノードを使って DNS を解決します。

2. [https://www.whatismyip.com/](https://www.whatismyip.com/) などのサイトにアクセスして、トンネルが動作することを確認します。 返される IP は、Microsoft Azure データ センターで使用されるものです。

## <a name="verify-with-ambari-web-ui"></a>Ambari Web UI を使用して確認する

クラスターが確立したら、次の手順で、Ambari Web からサービス Web UI にアクセスできることを確認します。

1. ブラウザーで、http\://headnodehost:8080 に移動します。 この `headnodehost` アドレスはトンネル経由でクラスターに送信され、Ambari が実行されているヘッド ノードに解決されます。 プロンプトが表示されたら、クラスターの管理者ユーザー名 (admin) とパスワードを入力します。 Ambari Web UI からもう一度入力を求められることがあります。 その場合は、情報を再入力します。

   > [!NOTE]  
   > http\://headnodehost:8080 のアドレスを使用してクラスターに接続する場合は、トンネル経由で接続しています。 通信は、HTTPS ではなく SSH トンネルを使って保護されます。 HTTPS を使用してインターネット経由で接続するには、https\://clustername.azurehdinsight.net を使用します。ここで、**clustername** はクラスターの名前です。

2. Ambari Web UI でページの左側にある一覧から [HDFS] を選択します。

    ![HDFS が選択された画像](./media/hdinsight-linux-ambari-ssh-tunnel/hdfsservice.png)

3. HDFS サービスの情報が表示されたら、 **[Quick Links]** を選択します。 クラスターのヘッド ノードの一覧が表示されます。 ヘッド ノードのいずれかを選択し、 **[NameNode UI]** を選択します。

    ![QuickLinks メニューが展開された画像](./media/hdinsight-linux-ambari-ssh-tunnel/namenodedropdown.png)

   > [!NOTE]  
   > __[Quick Links]\(クイック リンク\)__ を選択すると、待機インジケーターが表示されることがあります。 この状態は、低速のインターネット接続を使っている場合に発生する可能性があります。 サーバーからデータが取得されるまで 1 ～ 2 分待ってから、改めて一覧を表示してみてください。
   >
   > **[Quick Links]\(クイック リンク\)** メニューの一部のエントリが、画面の右側で切れている場合があります。 その場合は、マウスを使ってメニューを拡大し、右矢印キーを使って画面を右にスクロールして、メニューの残りを表示します。

4. 次の図ようなページが表示されます。

    ![NameNode UI の画像](./media/hdinsight-linux-ambari-ssh-tunnel/namenode.png)

   > [!NOTE]  
   > このページの URL に注意してください。**http\://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster** のようになっています。 この URI は、ノードの内部の完全修飾ドメイン名 (FQDN) を使っているので、SSH トンネルを使うことによってのみアクセスできます。

## <a name="next-steps"></a>次の手順

SSH トンネルを作成して使う方法を学習したので、Ambari を使う他の方法を以下のドキュメントで参照してください。

* [Apache Ambari を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md)

SSH の使用方法の詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページをご覧ください。

