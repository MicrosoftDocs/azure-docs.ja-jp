---
title: Azure での OpenShift Container Platform 3.11 デプロイのトラブルシューティング
description: Azure での OpenShift Container Platform 3.11 デプロイのトラブルシューティング。
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.custom: devx-track-ansible
ms.openlocfilehash: b6dfb4cb697e7d66c7db323df9786ff7ad45e1d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101672328"
---
# <a name="troubleshoot-openshift-container-platform-311-deployment-in-azure"></a>Azure での OpenShift Container Platform 3.11 デプロイのトラブルシューティング

OpenShift クラスターが正常にデプロイされない場合は、Azure portal にエラーが出力されます。 この出力が読みにくく、問題を簡単に識別できない場合があります。 この出力にざっと目を通して、終了コード 3、4、または 5 を探してください。 これら 3 つの終了コードの情報を次に示します。

- 終了コード 3: Red Hat サブスクリプション ユーザー名/パスワードまたは組織 ID/ライセンス認証キーが正しくありません
- 終了コード 4: Red Hat のプール ID が正しくないか、エンタイトルメントがありません
- 終了コード 5: Docker シン プールのボリュームをプロビジョニングできません

他のすべての終了コードについては、ssh 経由でホストに接続して、ログ ファイルを表示してください。

**OpenShift Container Platform 3.11**

Ansible プレイブック ホストに SSH 接続します。 テンプレートまたは Marketplace オファーの場合は、踏み台ホストを使用します。 踏み台ホストから、クラスター内の他のすべてのノード (マスター、インフラストラクチャ、CNS、コンピューティング) に SSH 接続できます。 ログ ファイルを表示するには、root である必要があります。 SSH アクセスでは root は既定で無効になるため、root を使用して他のノードに接続しないでください。

**OKD**

Ansible プレイブック ホストに SSH 接続します。 OKD テンプレート (バージョン 3.9 以前) では、master-0 ホストを使用します。 OKD テンプレート (バージョン3.10 以降) では、踏み台ホストを使用します。 Ansible プレイブック ホストから、クラスター内の他のすべてのノード (マスター、インフラストラクチャ、CN、コンピューティング) に SSH 接続できます。 ログ ファイルを表示するには、root (sudo su -) である必要があります。 SSH アクセスでは root は既定で無効になるため、root を使用して他のノードに接続しないでください。

## <a name="log-files"></a>ログ ファイル

ホスト準備スクリプト用のログ ファイル (標準エラーと標準出力) は、すべてのホストで `/var/lib/waagent/custom-script/download/0` に配置されます。 ホストの準備中にエラーが発生した場合は、これらのログ ファイルを調べてエラーを特定します。

準備スクリプトが正常に実行された場合は、Ansible プレイブック ホストの `/var/lib/waagent/custom-script/download/1` ディレクトリ内のログ ファイルを調べる必要があります。 OpenShift の実際のインストール中にエラーが発生した場合は、標準出力ファイルにエラーが表示されます。 この情報を使用して、サポートに連絡してさらに支援を求めてください。

出力例

```json
TASK [openshift_storage_glusterfs : Load heketi topology] **********************
fatal: [mycluster-master-0]: FAILED! => {"changed": true, "cmd": ["oc", "--config=/tmp/openshift-glusterfs-ansible-IbhnUM/admin.kubeconfig", "rsh", "--namespace=glusterfs", "deploy-heketi-storage-1-d9xl5", "heketi-cli", "-s", "http://localhost:8080", "--user", "admin", "--secret", "VuoJURT0/96E42Vv8+XHfsFpSS8R20rH1OiMs3OqARQ=", "topology", "load", "--json=/tmp/openshift-glusterfs-ansible-IbhnUM/topology.json", "2>&1"], "delta": "0:00:21.477831", "end": "2018-05-20 02:49:11.912899", "failed": true, "failed_when_result": true, "rc": 0, "start": "2018-05-20 02:48:50.435068", "stderr": "", "stderr_lines": [], "stdout": "Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2\n\tAllowing file volumes on cluster.\n\tAllowing block volumes on cluster.\n\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4\n\t\tAdding device /dev/sdd ... OK\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131\n\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdd ... OK\n\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\t\tAdding device /dev/sdd ... OK", "stdout_lines": ["Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2", "\tAllowing file volumes on cluster.", "\tAllowing block volumes on cluster.", "\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4", "\t\tAdding device /dev/sdd ... OK", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131", "\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdd ... OK", "\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\t\tAdding device /dev/sdd ... OK"]}

PLAY RECAP *********************************************************************
mycluster-cns-0       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-1       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-2       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-infra-0     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-1     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-2     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-master-0    : ok=502  changed=198  unreachable=0    failed=1   
mycluster-master-1    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-master-2    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-node-0      : ok=143  changed=55   unreachable=0    failed=0   
mycluster-node-1      : ok=143  changed=55   unreachable=0    failed=0   
localhost                  : ok=13   changed=0    unreachable=0    failed=0   

INSTALLER STATUS ***************************************************************
Initialization             : Complete (0:00:39)
Health Check               : Complete (0:00:24)
etcd Install               : Complete (0:01:24)
Master Install             : Complete (0:14:59)
Master Additional Install  : Complete (0:01:10)
Node Install               : Complete (0:10:58)
GlusterFS Install          : In Progress (0:03:33)
    This phase can be restarted by running: playbooks/openshift-glusterfs/config.yml

Failure summary:

  1. Hosts:    mycluster-master-0
     Play:     Configure GlusterFS
     Task:     Load heketi topology
     Message:  Failed without returning a message.
```

インストール時に発生する最も一般的なエラーは次のとおりです。

1. 秘密キーにパスフレーズが含まれている
2. 秘密キーを含むキー コンテナー シークレットが正しく作成されなかった
3. サービス プリンシパルの資格情報が正しく入力されなかった
4. サービス プリンシパルにリソース グループへの共同作成者アクセス権がない

### <a name="private-key-has-a-passphrase"></a>秘密キーにパスフレーズが含まれている

ssh 接続でアクセス許可が拒否されたというエラーが表示されます。 Ansible プレイブック ホストに ssh 接続して、秘密キーに関するパスフレーズをチェックします。

### <a name="key-vault-secret-with-private-key-wasnt-created-correctly"></a>秘密キーを含むキー コンテナー シークレットが正しく作成されなかった

Ansible プレイブック ホストに秘密キーがコピーされています (~/.ssh/id_rsa)。 このファイルが正しいことを確認します。 Ansible プレイブック ホストからいずれかのクラスター ノードに対して SSH セッションを開くことでテストします。

### <a name="service-principal-credentials-were-entered-incorrectly"></a>サービス プリンシパルの資格情報が正しく入力されなかった

テンプレートまたは Marketplace オファーへの入力時に、不適切な情報が指定されました。 サービス プリンシパルの適切な appId (clientId) とパスワード (clientSecret) を使用していることを確認します。 次の azure cli コマンドを実行して確認します。

```azurecli
az login --service-principal -u <client id> -p <client secret> -t <tenant id>
```

### <a name="service-principal-doesnt-have-contributor-access-to-the-resource-group"></a>サービス プリンシパルにリソース グループへの共同作成者アクセス権がない

Azure クラウド プロバイダーが有効になっている場合、使用するサービス プリンシパルは、リソース グループへの共同作成者アクセス権が必要です。 次の azure cli コマンドを実行して確認します。

```azurecli
az group update -g <openshift resource group> --set tags.sptest=test
```

## <a name="additional-tools"></a>その他のツール

一部のエラーでは、次のコマンドを使用して詳細を取得することもできます。

1. systemctl status \<service>
2. journalctl -xe
