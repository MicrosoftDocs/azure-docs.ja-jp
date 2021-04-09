---
title: Azure HDInsight でドメイン アカウントの SSH アクセスを管理する
description: HDInsight で Azure AD アカウントの SSH アクセスを管理する手順。
ms.service: hdinsight
ms.topic: how-to
ms.date: 02/14/2020
ms.openlocfilehash: 3fab06f5e269f311f798c096a6465c8c6ce75fc2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946744"
---
# <a name="manage-ssh-access-for-domain-accounts-in-azure-hdinsight"></a>Azure HDInsight でドメイン アカウントの SSH アクセスを管理する

セキュリティで保護されたクラスターでは、既定で、[Azure AD DS](../../active-directory-domain-services/overview.md) のすべてのドメイン ユーザーが [SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) を使用してヘッド ノードとエッジ ノードにアクセスできます。 これらのユーザーは sudoers グループに属しておらず、root アクセスを取得しません。 クラスターの作成中に作成された SSH ユーザーは、root アクセスを持ちます。

## <a name="manage-access"></a>アクセスを管理する

特定のユーザーまたはグループへの SSH アクセスを変更するには、各ノードで `/etc/ssh/sshd_config` を更新します。

1. [ssh コマンド](../hdinsight-hadoop-linux-use-ssh-unix.md)を使用してクラスターに接続します。 次のコマンドを編集して CLUSTERNAME をクラスターの名前に置き換えてから、そのコマンドを入力します。

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. `ssh_confi`g ファイルを開きます。

    ```bash
    sudo nano /etc/ssh/sshd_config
    ```

1. 必要に応じて、`sshd_config` ファイルを変更します。 ユーザーを特定のグループに制限すると、ローカル アカウントはそのノードに SSH で接続できません。 構文の例を次に示します。

    ```bash
    AllowUsers useralias1 useralias2

    AllowGroups groupname1 groupname2
    ```

    次に、変更を保存します (**Ctrl + X**、**Y**、**Enter** キー)。

1. sshd を起動します。

    ```bash
    sudo systemctl restart sshd
    ```

1. ノードごとに上記の手順を繰り返します。

## <a name="ssh-authentication-log"></a>SSH 認証ログ

SSH 認証ログは `/var/log/auth.log` に書き込まれます。 ローカル アカウントまたはドメイン アカウントの SSH によるログイン エラーが発生した場合は、ログを調べてエラーをデバッグする必要があります。 多くの場合、問題は特定のユーザー アカウントに関連している可能性があります。通常は、既定の SSH ユーザー (ローカル アカウント) を使用して他のユーザー アカウントまたは SSH を試し、kinit を試すことをお勧めします。

## <a name="ssh-debug-log"></a>SSH デバッグ ログ

詳細ログ記録を有効にするには、`-d` オプションを使用して `sshd` を再起動する必要があります。 `/usr/sbin/sshd -d` と同様に、`sshd` をカスタム ポート (2222 など) で実行して、メイン SSH デーモンを停止する必要がないようにすることもできます。 SSH クライアントで `-v` オプションを使用して、さらに多くのログを取得することもできます (エラーのクライアント側のビュー)。

## <a name="next-steps"></a>次のステップ

* [Enterprise セキュリティ パッケージを使用して HDInsight クラスターを管理する](./apache-domain-joined-manage.md)
* [SSH を使用して HDInsight (Apache Hadoop) に接続する](../hdinsight-hadoop-linux-use-ssh-unix.md)。
