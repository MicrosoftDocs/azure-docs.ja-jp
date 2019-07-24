---
title: Hadoop での SSH の使用 - Azure HDInsight
description: Secure Shell (SSH) を使用して HDInsight にアクセスできます。 このドキュメントでは、Windows、Linux、Unix、または macOS の各クライアントから ssh および scp コマンドを使用して HDInsight に接続する方法について説明します。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
keywords: Linux での Hadoop コマンド, Hadoop Linux コマンド, Hadoop macOS, ssh Hadoop, ssh Hadoop クラスター
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017
ms.openlocfilehash: ffae3e8c23a30e683db85ad6745ab30cfee93f2e
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2019
ms.locfileid: "59283993"
---
# <a name="connect-to-hdinsight-apache-hadoop-using-ssh"></a>SSH を使用して HDInsight (Apache Hadoop) に接続する

[Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) を使って Azure HDInsight の Apache Hadoop に安全に接続する方法について説明します。 仮想ネットワークを介した接続については、「[Azure HDInsight 仮想ネットワーク アーキテクチャ](./hdinsight-virtual-network-architecture.md)」および「[Azure Virtual Network を使用した Azure HDInsight の拡張](./hdinsight-extend-hadoop-virtual-network.md)」を参照してください。

次の表に、SSH クライアントを使用して HDInsight に接続する際に必要なアドレスとポートの情報を示します。

| Address | ポート | 接続先 |
| ----- | ----- | ----- |
| `<clustername>-ssh.azurehdinsight.net` | 22 | プライマリ ヘッド ノード |
| `<clustername>-ssh.azurehdinsight.net` | 23 | セカンダリ ヘッド ノード |
| `<clustername>-ed-ssh.azurehdinsight.net` | 22 | エッジ ノード (HDInsight の ML サービス) |
| `<edgenodename>.<clustername>-ssh.azurehdinsight.net` | 22 | エッジ ノード (エッジ ノードが存在する場合はその他のクラスターの種類) |

`<clustername>` をクラスターの名前に置き換えます。 `<edgenodename>` をエッジ ノードの名前に置き換えます。 

クラスターにエッジ ノードが含まれている場合は、SSH を使用して __常にエッジ ノードに接続する__ ことをお勧めします。 ヘッド ノードは、Hadoop の正常な稼働に不可欠なサービスをホストします。 エッジ ノードは、配置されたもののみを実行します。 エッジ ノードの使用の詳細については、[HDInsight でのエッジ ノードの使用](hdinsight-apps-use-edge-node.md#access-an-edge-node)に関する記事を参照してください。

> [!TIP]  
> HDInsight に初めて接続すると、ホストの信頼性を確立できないという警告が SSH クライアントに表示されることがあります。 メッセージが表示されたら、"はい" を選択して、SSH クライアントの信頼済みサーバーの一覧にこのホストを追加してください。
>
> 以前に同じ名前でサーバーに接続したことがある場合は、保存されているホスト キーとサーバーのホスト キーが一致しないという警告が表示されることがあります。 サーバー名の既存のエントリを削除する方法については、SSH クライアントのドキュメントを参照してください。

## <a name="ssh-clients"></a>SSH クライアント

Linux、Unix、および macOS システムでは、`ssh` コマンドと `scp` コマンドが用意されています。 `ssh` クライアントは、通常、Linux または Unix ベースのシステムとの間にリモートのコマンドライン セッションを作成するために使用します。 `scp` クライアントは、クライアントとリモート システムの間でファイルを安全にコピーするために使用します。

Microsoft Windows には、既定では SSH クライアントがインストールされていません。 Windows 用の `ssh` クライアントと `scp` クライアントは、次の各パッケージで入手できます。

* [OpenSSH クライアント](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse)。 Windows 10 Fall Creators Update で導入されるオプション機能です。

* [Bash on Ubuntu on Windows 10](https://docs.microsoft.com/windows/wsl/about)。

* [Azure Cloud Shell](../cloud-shell/quickstart.md)。 Cloud Shell は、ブラウザー内で Bash 環境を提供します。

* [Git](https://git-scm.com/)。

[PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/) や [MobaXterm](https://mobaxterm.mobatek.net/) などのグラフィカルな SSH クライアントもいくつか存在します。 これらのクライアントは HDInsight への接続に使用できますが、接続するプロセスは、`ssh` ユーティリティを使用する場合とは異なります。 詳細については、お使いのグラフィカル クライアントに関するドキュメントを参照してください。

## <a id="sshkey"></a>認証:SSH キー

SSH キーでは、[公開キー暗号化](https://en.wikipedia.org/wiki/Public-key_cryptography)を使用して SSH セッションを認証します。 SSH キーは、パスワードよりも安全性が高く、Hadoop クラスターへのアクセスをセキュリティで保護する簡単な方法を提供します。

SSH アカウントがキーを使用してセキュリティ保護されている場合、クライアントは対応する秘密キーを接続時に提供する必要があります。

* ほとんどのクライアントは、__既定のキー__ を使用するように構成できます。 たとえば、Linux 環境または Unix 環境では、`ssh` クライアントは `~/.ssh/id_rsa` で秘密キーを探します。

* __秘密キーへのパス__ を指定できます。 `ssh` クライアントでは、秘密キーへのパスを指定するために `-i` パラメーターが使用されます。 たとえば、「 `ssh -i ~/.ssh/id_rsa sshuser@myedge.mycluster-ssh.azurehdinsight.net` 」のように入力します。

* 異なる複数のサーバーで使用するための __複数の秘密キー__ がある場合は、[ssh-agent (https://en.wikipedia.org/wiki/Ssh-agent)](https://en.wikipedia.org/wiki/Ssh-agent) などのユーティリティを使用することを検討してください。 `ssh-agent` ユーティリティを使用すると、SSH セッションを確立するときに使用するキーを自動的に選択できます。

> [!IMPORTANT]  
> パスフレーズを使用して秘密キーをセキュリティ保護した場合は、そのキーを使用するときにパスフレーズを入力する必要があります。 `ssh-agent` などのユーティリティは、利便性のためにパスワードをキャッシュすることができます。

### <a name="create-an-ssh-key-pair"></a>SSH キー ペアの作成

`ssh-keygen` コマンドを使用して、公開キーと秘密キーのファイルを作成します。 次のコマンドを実行すると、HDInsight で使用できる 2,048 ビットの RSA キー ペアが生成されます。

    ssh-keygen -t rsa -b 2048

キーの作成プロセス中には、情報の入力が求められます。 たとえば、キーが格納されている場所や、パスフレーズを使用するかどうかなどです。 プロセスが完了すると、公開キーと秘密キーの 2 つのファイルが作成されます。

* __公開キー__ は、HDInsight クラスターの作成に使用します。 公開キーの拡張子は `.pub` です。

* __秘密キー__ は、HDInsight クラスターでクライアントを認証するために使用します。

> [!IMPORTANT]  
> キーはパスフレーズを使用してセキュリティ保護することができます。 パスフレーズは実質的には秘密キーのパスワードです。 だれかが秘密キーを入手したとしても、パスフレーズがないと使用できません。

### <a name="create-hdinsight-using-the-public-key"></a>秘密キーを使用して HDInsight を作成する

| 作成方法 | 公開キーの使用方法 |
| ------- | ------- |
| Azure ポータル | __[クラスター ログインと同じパスワードを使用します]__ チェック ボックスをオフにし、[SSH 認証の種類] で __[公開キー]__ を選択します。 最後に、公開キー ファイルを選択するか、ファイルのテキストの内容を __[SSH 公開キー]__ フィールドに貼り付けます。</br>![HDInsight クラスター作成時の [SSH 公開キー] ダイアログ ボックス](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-public-key.png) |
| Azure PowerShell | [New-AzHdinsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) コマンドレットの `-SshPublicKey` パラメーターを使用し、公開キーの内容を文字列として渡します。|
| Azure CLI | [az hdinsight create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) コマンドの `--sshPublicKey` パラメーターを使用し、公開キーの内容を文字列として渡します。 |
| Resource Manager テンプレート | テンプレートでの SSH キーの使用例については、[SSH キーを使用した Linux での HDInsight のデプロイ](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-publickey/)に関する記事を参照してください。 [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-publickey/azuredeploy.json) ファイルの `publicKeys` 要素は、クラスターの作成時にキーを Azure に渡すために使用されます。 |

## <a id="sshpassword"></a>認証:パスワード

SSH アカウントは、パスワードを使用してセキュリティ保護することができます。 SSH を使用して HDInsight に接続すると、パスワードの入力を求められます。

> [!WARNING]  
> SSH でパスワード認証を使用することはお勧めしません。 パスワードは推測可能であり、ブルート フォース攻撃に対して脆弱です。 代わりに、[SSH キーによる認証](#sshkey)を行うことをお勧めします。

> [!IMPORTANT]  
> SSH アカウントのパスワードは、HDInsight クラスターの作成後 70 日で期限切れになります。 パスワードの期限が切れた場合は、[HDInsight の管理](hdinsight-administer-use-portal-linux.md#change-passwords)に関するドキュメントの情報を使用して変更できます。

### <a name="create-hdinsight-using-a-password"></a>パスワードを使用して HDInsight を作成する

| 作成方法 | パスワードの指定方法 |
| --------------- | ---------------- |
| Azure ポータル | 既定では、SSH ユーザー アカウントには、クラスター ログイン アカウントと同じパスワードがあります。 別のパスワードを使用するには、__[クラスター ログインと同じパスワードを使用します]__ チェック ボックスをオフにし、__[SSH パスワード]__ フィールドにパスワードを入力します。</br>![HDInsight クラスター作成時の [SSH パスワード] ダイアログ ボックス](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-password.png)|
| Azure PowerShell | [New-AzHdinsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) コマンドレットの `--SshCredential` パラメーターを使用して、SSH ユーザー アカウントの名前とパスワードを含む `PSCredential` オブジェクトを渡します。 |
| Azure CLI | [az hdinsight create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) コマンドの `--sshPassword` パラメーターを使用して、パスワードの値を提供します。 |
| Resource Manager テンプレート | テンプレートを使用したパスワードの使用例については、[SSH パスワードを使用した Linux での HDInsight のデプロイ](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/)に関する記事を参照してください。 [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-password/azuredeploy.json) ファイルの `linuxOperatingSystemProfile` 要素は、クラスターの作成時に SSH アカウントの名前とパスワードを Azure に渡すために使用されます。|

### <a name="change-the-ssh-password"></a>SSH パスワードを変更する

SSH ユーザー アカウントのパスワード変更に関する詳細については、[HDInsight の管理](hdinsight-administer-use-portal-linux.md#change-passwords)に関するドキュメントの「__パスワードの変更__」セクションを参照してください。

## <a id="domainjoined"></a>認証:ドメイン参加済み HDInsight

__ドメイン参加済み HDInsight クラスター__ を使用している場合は、SSH ローカル ユーザーと接続した後で `kinit` コマンドを使用する必要があります。 このコマンドを実行すると、ドメイン ユーザーとパスワードの入力が求められ、クラスターに関連付けられた Azure Active Directory ドメインによってセッションが認証されます。

また、ドメイン アカウントを使用して SSH 接続するために、各ドメイン参加ノード (たとえば、ヘッド ノード、エッジ ノード) で Kerberos 認証を有効にすることができます。 これを行うには、sshd 構成ファイルを編集します。
```bash
sudo vi /etc/ssh/sshd_config
```
コメントを解除し、`KerberosAuthentication` を次に変更します:  `yes`

```bash
sudo service sshd restart
```

Kerberos 認証が成功したかどうかを確認するには、常に `klist` コマンドを使用します。

詳細については、[ドメイン参加済み HDInsight の構成](./domain-joined/apache-domain-joined-configure.md)に関する記事を参照してください。

## <a name="connect-to-nodes"></a>ノードへの接続

ヘッド ノードとエッジ ノード (存在する場合) には、インターネット経由で、ポート 22 および 23 でアクセスできます。

* __ヘッド ノード__ に接続する場合、プライマリ ヘッド ノードに接続するときはポート __22__ を使用し、セカンダリ ヘッド ノードに接続するときはポート __23__ を使用します。 使用する完全修飾ドメイン名は、`clustername-ssh.azurehdinsight.net` です。ここで、`clustername` は実際のクラスターの名前です。

    ```bash
    # Connect to primary head node
    # port not specified since 22 is the default
    ssh sshuser@clustername-ssh.azurehdinsight.net

    # Connect to secondary head node
    ssh -p 23 sshuser@clustername-ssh.azurehdinsight.net
    ```
    
* __エッジ ノード__ に接続するときは、ポート 22 を使用します。 完全修飾ドメイン名は、`edgenodename.clustername-ssh.azurehdinsight.net` です。ここで、`edgenodename` は、エッジ ノードを作成したときに指定した名前です。 `clustername` は、クラスターの名前です。

    ```bash
    # Connect to edge node
    ssh sshuser@edgnodename.clustername-ssh.azurehdinsight.net
    ```

> [!IMPORTANT]  
> 前の例では、パスワード認証が使用されていること、または証明書認証が自動的に行われていることを前提としています。 認証に SSH キー ペアを使用していて、証明書が自動的に使用されない場合は、`-i` パラメーターを使用して秘密キーを指定します。 たとえば、「 `ssh -i ~/.ssh/mykey sshuser@clustername-ssh.azurehdinsight.net` 」のように入力します。

接続後はプロンプトが変更され、SSH ユーザー名と接続先ノードが示されます。 たとえば、プライマリ ヘッド ノードに `sshuser` として接続されている場合、プロンプトは `sshuser@hn0-clustername:~$` になります。

### <a name="connect-to-worker-and-apache-zookeeper-nodes"></a>ワーカー ノードと Apache Zookeeper ノードに接続する

worker ノードと Zookeeper ノードには、インターネットから直接アクセスすることはできません。 これらのノードには、クラスターのヘッド ノードまたはエッジ ノードからアクセスできます。 他のノードに接続するための一般的な手順は次のとおりです。

1. SSH を使用してヘッド ノードまたはエッジ ノードに接続します。

        ssh sshuser@myedge.mycluster-ssh.azurehdinsight.net

2. ヘッド ノードまたはエッジ ノードへの SSH 接続から、`ssh` コマンドを使用してクラスター内のワーカー ノードに接続します。

        ssh sshuser@wn0-myhdi

    ノード名の一覧を取得するには、[Apache Ambari REST API を使用した HDInsight の管理](hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes)に関するページを参照してください。

SSH アカウントが __パスワード__ を使用してセキュリティで保護されている場合は、接続時にパスワードを入力します。

SSH アカウントが __SSH キー__ を使用してセキュリティで保護されている場合は、クライアント側で SSH の転送が有効になっていることを確認します。

> [!NOTE]  
> 別の方法でクラスター内のすべてのノードに直接アクセスするには、Azure 仮想ネットワークに HDInsight をインストールします。 次に、リモート マシンを同じ仮想ネットワーク内に参加させることで、クラスター内のすべてのノードに直接アクセスできます。
>
> 詳細については、[HDInsight での仮想ネットワークの使用](hdinsight-extend-hadoop-virtual-network.md)に関する記事を参照してください。

### <a name="configure-ssh-agent-forwarding"></a>SSH エージェント転送を構成する

> [!IMPORTANT]  
> 次の手順では、Linux または UNIX ベースのシステムを想定して Bash on Windows 10 を使用します。 次の手順をお使いのシステムで利用できない場合は、必要に応じて SSH クライアントのドキュメントを参照してください。

1. テキスト エディターを使用して `~/.ssh/config`を開きます。 このファイルが存在しない場合は、コマンド ラインで「`touch ~/.ssh/config`」と入力して作成できます。

2. `config` ファイルに次のテキストを追加します。

        Host <edgenodename>.<clustername>-ssh.azurehdinsight.net
          ForwardAgent yes

    __Host__ 情報を、SSH での接続先となるノードのアドレスで置き換えます。 前の例ではエッジ ノードを使用しています。 このエントリにより、指定したノード用に SSH エージェント転送が構成されます。

3. 端末から次のコマンドを使用して、SSH エージェント転送をテストします。

        echo "$SSH_AUTH_SOCK"

    このコマンドでは、次のテキストのような情報が返されます。

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    何も返されない場合は、`ssh-agent` が実行されていません。 詳細については、[「Using ssh-agent with ssh (ssh での ssh-agent の使用)」(http://mah.everybody.org/docs/ssh)](http://mah.everybody.org/docs/ssh) でエージェントのスタートアップ スクリプト情報を参照するか、お使いの SSH クライアントのドキュメントを確認してください。

4. **ssh-agent** が実行していることを確認したら、次のコマンドを使用して SSH 秘密キーをエージェントに追加します。

        ssh-add ~/.ssh/id_rsa

    秘密キーを別のファイルに格納している場合は、 `~/.ssh/id_rsa` をそのファイルのパスに置き換えます。

5. SSH を使用して、クラスターのエッジ ノードまたはヘッド ノードに接続します。 次に、SSH コマンドを使用して、ワーカー ノードまたは Zookeeper ノードに接続します。 転送されたキーを使用して、接続が確立します。

## <a name="copy-files"></a>ファイルのコピー

`scp` ユーティリティを使用すると、クラスター内の個々のノード間でファイルをコピーできます。 たとえば、次のコマンドは、ローカル システムの `test.txt` ディレクトリをプライマリ ヘッド ノードにコピーします。

```bash
scp test.txt sshuser@clustername-ssh.azurehdinsight.net:
```

`:` の後にパスが指定されていないため、ファイルは `sshuser` ホーム ディレクトリに配置されます。

次の例は、`test.txt` ファイルをプライマリ ヘッド ノードの `sshuser` ホーム ディレクトリからローカル システムにコピーします。

```bash
scp sshuser@clustername-ssh.azurehdinsight.net:test.txt .
```

> [!IMPORTANT]  
> `scp` は、クラスター内の個々のノードのファイル システムだけにアクセスできます。 クラスターの HDFS と互換性のあるストレージ内のデータにアクセスするために使用することはできません。
>
> SSH セッションから使用するためのリソースをアップロードする必要がある場合は、`scp` を使用します。 たとえば、Python スクリプトをアップロードし、SSH セッションからスクリプトを実行します。
>
> HDFS と互換性のあるストレージに直接データを読み込む方法の詳細については、以下のドキュメントを参照してください。
>
> * [Azure Storage を使用する HDInsight](hdinsight-hadoop-use-blob-storage.md)
>
> * [Azure Data Lake Storage を使用する HDInsight](hdinsight-hadoop-use-data-lake-store.md)

## <a name="next-steps"></a>次の手順

* [HDInsight で SSH トンネリングを使用する](hdinsight-linux-ambari-ssh-tunnel.md)
* [HDInsight で仮想ネットワークを使用する](hdinsight-extend-hadoop-virtual-network.md)
* [HDInsight でエッジ ノードを使用する](hdinsight-apps-use-edge-node.md#access-an-edge-node)
