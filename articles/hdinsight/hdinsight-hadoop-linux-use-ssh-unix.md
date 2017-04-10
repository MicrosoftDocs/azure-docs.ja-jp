---
title: "Windows、Linux、Unix、または OS X から HDInsight (Hadoop) で SSH を使用する | Microsoft Docs"
description: " Secure Shell (SSH) を使用して HDInsight にアクセスできます。 このドキュメントでは、Windows、Linux、Unix、または OS X の各クライアントから SSH を使用して HDInsight に接続する方法について説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: a6a16405-a4a7-4151-9bbf-ab26972216c5
ms.service: hdinsight
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/16/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 89d44476e9de8ac32195efaf66535cdd9fb4260e
ms.lasthandoff: 03/25/2017

---
# <a name="connect-to-hdinsight-hadoop-using-ssh"></a>SSH を使用して HDInsight (Hadoop) に接続する

[Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) を使って HDInsight に安全に接続する方法について説明します。 HDInsight では Linux (Ubuntu) をクラスター内のノードのオペレーティング システムとして使用できます。 SSH は、Linux ベースのクラスターのヘッド ノードとエッジ ノードに接続し、これらのノードで直接コマンドを実行するために使用できます。

次の表に、SSH を使用して HDInsight に接続する際に必要なアドレスとポートの情報を示します。

| Address | ポート | 接続先 |
| ----- | ----- | ----- |
| `<edgenodename>.<clustername>-ssh.azurehdinsight.net` | 22 | エッジ ノード (存在する場合) |
| `<clustername>-ssh.azurehdinsight.net` | 22 | プライマリ ヘッド ノード |
| `<clustername>-ssh.azurehdinsight.net` | 23 | セカンダリ ヘッド ノード |

> [!NOTE]
> `<edgenodename>` をエッジ ノードの名前に置き換えます。 エッジ ノードの使用の詳細については、[HDInsight でのエッジ ノードの使用](hdinsight-apps-use-edge-node.md#access-an-edge-node)に関する記事を参照してください。
>
> `<clustername>` を、使用する HDInsight クラスターの名前に置き換えます。
>
> エッジ ノードが存在する場合は、__常にエッジ ノードに接続しておく__ことをお勧めします。 ヘッド ノードは、クラスターの正常な稼働に不可欠なサービスをホストします。 エッジ ノードは、配置されたもののみを実行します。

## <a name="ssh-clients"></a>SSH クライアント

ほとんどのオペレーティング システムに `ssh` クライアントが用意されています。 Microsoft Windows には、既定では SSH クライアントが用意されていません。 Windows 用の SSH クライアントは、次の各パッケージで入手できます。

* [Bash on Ubuntu on Windows 10](https://msdn.microsoft.com/commandline/wsl/about): `ssh` コマンドは Bash on Windows のコマンド ラインを介して提供されます。

* [Git (https://git-scm.com/)](https://git-scm.com/): `ssh` コマンドは GitBash のコマンド ラインを介して提供されます。

* [GitHub Desktop (https://desktop.github.com/)](https://desktop.github.com/): `ssh` コマンドは Git Shell のコマンド ラインを介して提供されます。 GitHub Desktop は、Git Shell のコマンド ラインとして Bash、Windows コマンド プロンプト、PowerShell を使用するように構成できます。

* [OpenSSH (https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH): PowerShell チームが OpenSSH を Windows に移植し、テスト リリースを提供しています。

    > [!WARNING]
    > OpenSSH パッケージには、SSH サーバー コンポーネントの `sshd` が含まれています。 このコンポーネントは、システム上で SSH サーバーを起動し、他のユーザーによる接続を可能にします。 システム上で SSH サーバーをホストする場合を除き、このコンポーネントの構成またはポート 22 の解放は行わないでください。 これは HDInsight との通信には必要はありません。

さらに、[PuTTY (http://www.chiark.greenend.org.uk/~sgtatham/putty/)](http://www.chiark.greenend.org.uk/~sgtatham/putty/) や [MobaXterm (http://mobaxterm.mobatek.net/)](http://mobaxterm.mobatek.net/) といった、いくつかのグラフィカル SSH クライアントもあります。 これらのクライアントは HDInsight への接続に使用できますが、サーバーに接続するプロセスは、`ssh` ユーティリティを使用するときとは異なります。 詳細については、お使いのグラフィカル クライアントに関するドキュメントを参照してください。

## <a id="sshkey"></a>認証: SSH キー

SSH キーでは、クラスターのセキュリティ保護のために[公開キー暗号化](https://en.wikipedia.org/wiki/Public-key_cryptography)を使用します。 SSH キーは、パスワードよりも安全性が高く、HDInsight クラスターを簡単に保護できます。

SSH アカウントがキーを使用してセキュリティ保護されている場合、クライアントは対応する秘密キーを接続時に提供する必要があります。

* ほとんどのクライアントは、__既定のキー__を使用するように構成できます。 たとえば、Linux 環境または Unix 環境では、`ssh` クライアントは `~/.ssh/id_rsa` で秘密キーを探します。

* __秘密キーへのパス__を指定できます。 `ssh` クライアントでは、秘密キーへのパスを指定するために `-i` パラメーターが使用されます。 たとえば、「 `ssh -i ~/.ssh/hdinsight sshuser@myedge.mycluster-ssh.azurehdinsight.net`」のように入力します。

* 異なる複数のサーバーで使用するための__複数の秘密キー__がある場合は、[ssh-agent (https://en.wikipedia.org/wiki/Ssh-agent)](https://en.wikipedia.org/wiki/Ssh-agent) などのユーティリティを使用して、使用するキーを自動的に選択できます。

> [!IMPORTANT]
>
> パスフレーズを使用して秘密キーをセキュリティ保護した場合は、そのキーを使用するときにパスフレーズを入力する必要があります。 `ssh-agent` などのユーティリティは、利便性のためにパスワードをキャッシュすることができます。

### <a name="create-an-ssh-key-pair"></a>SSH キー ペアの作成

`ssh-keygen` コマンドを使用して、公開キーと秘密キーのファイルを作成します。 次のコマンドを実行すると、HDInsight で使用できる 2,048 ビットの RSA キー ペアが生成されます。

    ssh-keygen -t rsa -b 2048

キーの作成プロセス中には、情報の入力が求められます。 たとえば、キーが格納されている場所や、パスフレーズを使用するかどうかなどです。 プロセスが完了すると、公開キーと秘密キーの 2 つのファイルが作成されます。

* __公開キー__は、HDInsight クラスターの作成に使用します。 公開キーの拡張子は `.pub` です。

* __秘密キー__は、HDInsight クラスターでクライアントを認証するために使用します。

> [!IMPORTANT]
> キーはパスフレーズを使用してセキュリティ保護することができます。 これは実質的には秘密キーのパスワードです。 だれかが秘密キーを入手したとしても、パスフレーズがないと使用できません。

### <a name="create-hdinsight-using-the-public-key"></a>秘密キーを使用して HDInsight を作成する

| 作成方法 | 公開キーの使用方法 |
| ------- | ------- |
| **Azure ポータル** | __[クラスター ログインと同じパスワードを使用します]__ チェック ボックスをオフにし、[SSH 認証の種類] で __[公開キー]__ を選択します。 最後に、公開キー ファイルを選択するか、ファイルのテキストの内容を __[SSH 公開キー]__ フィールドに貼り付けます。</br>![HDInsight クラスター作成時の SSH 公開キー ダイアログ ボックス](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-public-key.png) |
| **Azure PowerShell** | `New-AzureRmHdinsightCluster` コマンドレットの `-SshPublicKey` パラメーターを使用し、公開キーの内容を文字列として渡します。|
| **Azure CLI 1.0** | `azure hdinsight cluster create` コマンドの `--sshPublicKey` パラメーターを使用し、公開キーの内容を文字列として渡します。 |
| **Resource Manager テンプレート** | テンプレートでの SSH キーの使用例については、[SSH キーを使用した Linux での HDInsight のデプロイ](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-publickey/)に関する記事を参照してください。 [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-publickey/azuredeploy.json) ファイルの `publicKeys` 要素は、クラスターの作成時にキーを Azure に渡すために使用されます。 |

## <a id="sshpassword"></a>認証: パスワード

SSH アカウントは、パスワードを使用してセキュリティ保護することができます。 SSH を使用して HDInsight に接続すると、パスワードの入力を求められます。

> [!WARNING]
> SSH でパスワード認証を使用することはお勧めしません。 パスワードは推測可能であり、ブルート フォース攻撃に対して脆弱です。 代わりに、[SSH キーによる認証](#sshkey)を行うことをお勧めします。

### <a name="create-hdinsight-using-a-password"></a>パスワードを使用して HDInsight を作成する

| 作成方法 | パスワードの指定方法 |
| --------------- | ---------------- |
| **Azure ポータル** | 既定では、SSH ユーザー アカウントには、クラスター ログイン アカウントと同じパスワードがあります。 別のパスワードを使用するには、__[クラスター ログインと同じパスワードを使用します]__ チェック ボックスをオフにし、__[SSH パスワード]__ フィールドにパスワードを入力します。</br>![HDInsight クラスター作成時の SSH パスワード ダイアログ ボックス](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-password.png)|
| **Azure PowerShell** | `New-AzureRmHdinsightCluster` コマンドレットの `--SshCredential` パラメーターを使用して、SSH ユーザー アカウントの名前とパスワードを含む `PSCredential` オブジェクトを渡します。 |
| **Azure CLI 1.0** | `azure hdinsight cluster create` コマンドの `--sshPassword` パラメーターを使用して、パスワードの値を提供します。 |
| **Resource Manager テンプレート** | テンプレートを使用したパスワードの使用例については、[SSH パスワードを使用した Linux での HDInsight のデプロイ](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/)に関する記事を参照してください。 [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-password/azuredeploy.json) ファイルの `linuxOperatingSystemProfile` 要素は、クラスターの作成時に SSH アカウントの名前とパスワードを Azure に渡すために使用されます。|

### <a name="change-the-ssh-password"></a>SSH パスワードを変更する

SSH ユーザー アカウントのパスワード変更に関する詳細については、[HDInsight の管理](hdinsight-administer-use-portal-linux.md#change-passwords)に関するドキュメントの「__パスワードの変更__」セクションを参照してください。

## <a id="domainjoined"></a>認証: ドメイン参加済み HDInsight

__ドメイン参加済み HDInsight クラスター__を使用している場合、SSH 接続の後で `kinit` コマンドを使用する必要があります。 このコマンドを実行すると、ドメイン ユーザーとパスワードの入力が求められ、クラスターに関連付けられた Azure Active Directory ドメインによってセッションが認証されます。

詳細については、[ドメイン参加済み HDInsight の構成](hdinsight-domain-joined-configure.md)に関する記事を参照してください。

## <a name="connect-to-worker-and-zookeeper-nodes"></a>ワーカー ノードと Zookeeper ノードに接続する

ワーカー ノードと Zookeeper ノードには、インターネットからは直接アクセスできませんが、クラスターのヘッド ノードまたはエッジ ノードからはアクセスできます。 他のノードに接続するための一般的な手順は次のとおりです。

1. SSH を使用してヘッド ノードまたはエッジ ノードに接続します。

        ssh sshuser@myedge.mycluster-ssh.azurehdinsight.net

2. ヘッド ノードまたはエッジ ノードへの SSH 接続から、`ssh` コマンドを使用してクラスター内のワーカー ノードに接続します。

        ssh sshuser@wn0-myhdi

    クラスター内にあるノードのドメイン名の一覧を取得する場合は、ドキュメント「[Ambari REST API を使用した HDInsight の管理](hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes)」で例を参照してください。

SSH アカウントが__パスワード__を使用してセキュリティ保護されている場合は、パスワードの入力を求められ、その後接続が確立します。

SSH アカウントが __SSH キー__を使用してセキュリティ保護されている場合は、ローカル環境が SSH エージェント転送向けに構成されていることを確認する必要があります。

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

    何も返されない場合は、`ssh-agent` が実行されていません。 「[Using ssh-agent with ssh (ssh での ssh-agent の使用)](http://mah.everybody.org/docs/ssh)」(http://mah.everybody.org/docs/ssh) でエージェントのスタートアップ スクリプト情報を参照するか、お使いの SSH クライアントのドキュメントで `ssh-agent` をインストールして構成する手順を確認してください。

4. **ssh-agent** が実行していることを確認したら、次のコマンドを使用して SSH 秘密キーをエージェントに追加します。

        ssh-add ~/.ssh/id_rsa

    秘密キーを別のファイルに格納している場合は、 `~/.ssh/id_rsa` をそのファイルのパスに置き換えます。

5. SSH を使用して、クラスターのエッジ ノードまたはヘッド ノードに接続します。 次に、SSH コマンドを使用して、ワーカー ノードまたは Zookeeper ノードに接続します。 転送されたキーを使用して、接続が確立します。

## <a name="next-steps"></a>次のステップ

* [HDInsight で SSH トンネリングを使用する](hdinsight-linux-ambari-ssh-tunnel.md)
* [HDInsight で仮想ネットワークを使用する](hdinsight-extend-hadoop-virtual-network.md)
* [HDInsight でエッジ ノードを使用する](hdinsight-apps-use-edge-node.md#access-an-edge-node)
