---
title: NFS Ubuntu Linux Server ボリュームを作成する
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service (AKS) のポッドで使用するための NFS Ubuntu Linux Server ボリュームを手動で作成する方法について説明します
services: container-service
author: ozboms
ms.topic: article
ms.date: 4/25/2019
ms.author: obboms
ms.openlocfilehash: 4e817d572a98ffb8135adf58d13f50ccacbc8746
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86251996"
---
# <a name="manually-create-and-use-an-nfs-network-file-system-linux-server-volume-with-azure-kubernetes-service-aks"></a>NFS (Network File System) Linux Server ボリュームを手動で作成し、Azure Kubernetes Service (AKS) と共に使用する
コンテナー間のデータ共有はしばしば、コンテナーを基盤とするサービスやアプリケーションで必要となります。 一般的に、外部の永続ボリューム上の同じ情報にさまざまなポットでアクセスする必要があります。    
Azure Files という選択肢がありますが、永続的共有ストレージのもう 1 つの形態が Azure VM 上で NFS Server を作成することです。 

この記事では、Ubuntu 仮想マシンで NFS Server を作成する方法について説明します。 この共有ファイル システムへの AKS コンテナー アクセスを与える方法についても説明します。

## <a name="before-you-begin"></a>開始する前に
この記事は、AKS クラスターがすでに存在していることを前提としています。 AKS クラスターが必要であれば、[Azure CLI を使用した場合][aks-quickstart-cli]または [Azure portal を使用した場合][aks-quickstart-portal]の AKS のクイックスタートを参照してください。

AKS クラスターは、NFS Server と同じか、対等の仮想ネットワークに置く必要があります。 クラスターは既存の VNET に作成する必要があります。この VNET は、VM と同じ VNET でもかまいません。

既存の VNET で構成する手順については、[既存の VNET 内での AKS クラスターの作成][aks-virtual-network]および [VNET ピアリングによる仮想ネットワーク間の接続][peer-virtual-networks]に関するドキュメントで説明しています

また、Ubuntu Linux 仮想マシン (18.04 LTS など) を作成しているものとします。 設定とサイズは自分の好みに合わせることができます。また、Azure 経由でデプロイできます。 Linux のクイック スタートについては、[Linux VM の管理][linux-create]に関する記事を参照してください。

AKS クラスターを最初にデプロイする場合、Azure によって、Ubuntu マシンのデプロイ時、仮想ネットワーク フィールドが自動的に入力され、同じ VNET 内で稼働します。 ただし、代わりに対等なネットワークを使用する場合、上記のドキュメントを参照してください。

## <a name="deploying-the-nfs-server-onto-a-virtual-machine"></a>仮想マシンに NFS Server をデプロイする
次は、Ubuntu 仮想マシン内で NFS Server を設定するためのスクリプトです。
```bash
#!/bin/bash

# This script should be executed on Linux Ubuntu Virtual Machine

EXPORT_DIRECTORY=${1:-/export/data}
DATA_DIRECTORY=${2:-/data}
AKS_SUBNET=${3:-*}

echo "Updating packages"
apt-get -y update

echo "Installing NFS kernel server"

apt-get -y install nfs-kernel-server

echo "Making data directory ${DATA_DIRECTORY}"
mkdir -p ${DATA_DIRECTORY}

echo "Making new directory to be exported and linked to data directory: ${EXPORT_DIRECTORY}"
mkdir -p ${EXPORT_DIRECTORY}

echo "Mount binding ${DATA_DIRECTORY} to ${EXPORT_DIRECTORY}"
mount --bind ${DATA_DIRECTORY} ${EXPORT_DIRECTORY}

echo "Giving 777 permissions to ${EXPORT_DIRECTORY} directory"
chmod 777 ${EXPORT_DIRECTORY}

parentdir="$(dirname "$EXPORT_DIRECTORY")"
echo "Giving 777 permissions to parent: ${parentdir} directory"
chmod 777 $parentdir

echo "Appending bound directories into fstab"
echo "${DATA_DIRECTORY}    ${EXPORT_DIRECTORY}   none    bind  0  0" >> /etc/fstab

echo "Appending localhost and Kubernetes subnet address ${AKS_SUBNET} to exports configuration file"
echo "/export        ${AKS_SUBNET}(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports
echo "/export        localhost(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports

nohup service nfs-kernel-server restart
```
サーバーが (スクリプトにより) 再起動します。NFS Server を AKS にマウントできます。

>[!IMPORTANT]  
>**AKS_SUBNET** を自分のクラスターからの情報に変更します。"*" を指定した場合、NFS Server がすべてのポートと接続に対してオープンになります。

VM を作成したら、上記のスクリプトをファイルにコピーします。 その後、次を使用し、ファイルをローカル コンピューターから (あるいはスクリプトが置かれている他の場所から) VM に次の方法で移動できます。 
```console
scp /path/to/script_file username@vm-ip-address:/home/{username}
```
自分の VM にスクリプトが置かれたら、VM に ssh で通信し、コマンドからスクリプトを実行できます。
```console
sudo ./nfs-server-setup.sh
```
アクセス却下エラーで実行できなかった場合、コマンドから実行権限を設定できます。
```console
chmod +x ~/nfs-server-setup.sh
```

## <a name="connecting-aks-cluster-to-nfs-server"></a>AKS クラスターを NFS サーバーに接続する
永続ボリュームとボリュームへのアクセス方法を指定する永続ボリューム要求をプロビジョニングすることで、NFS Server をクラスターに接続できます。

同じまたは対等の仮想ネットワークにある 2 つのサービスを接続する必要があります。 同じ VNET でクラスターを設定する方法は、[既存の VNET 内での AKS クラスターの作成][aks-virtual-network]に関する記事を参照してください

同じ仮想ネットワーク (または、対等な仮想ネットワーク) に置かれている場合、AKS クラスターで永続ボリュームと永続ボリューム要求をプロジェクトする必要があります。 その後、コンテナーでは、NFS ドライブをそのローカル ディレクトリにマウントできます。

次は、永続ボリュームの Kubernetes 定義の例です (この定義では、クラスターと VM が同じ VNET にあるものとしています)。

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: <NFS_NAME>
  labels:
    type: nfs
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: <NFS_INTERNAL_IP>
    path: <NFS_EXPORT_FILE_PATH>
```
**NFS_INTERNAL_IP**, **NFS_NAME** と **NFS_EXPORT_FILE_PATH** を NFS Server の情報に変更します。

永続ボリューム要求のファイルも必要になります。 たとえば、次のものを含めます。

>[!IMPORTANT]  
>**"storageClassName"** は空の文字列にする必要があります。空ではないと、要求は機能しません。

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: <NFS_NAME>
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
  selector: 
    matchLabels:
      type: nfs
```

## <a name="troubleshooting"></a>トラブルシューティング
クラスターからサーバーに接続できない場合、エクスポート ディレクトリかその親にサーバーにアクセスするための権限が与えられていない可能性があります。

エクスポート ディレクトリとその親ディレクトリの両方にアクセス許可 777 が与えられていることを確認してください。

下のコマンドを実行することでアクセス許可を確認できます。ディレクトリに *'drwxrwxrwx'* というアクセス許可が与えられている必要があります。
```console
ls -l
```

## <a name="more-information"></a>詳細情報
完全なチュートリアルが必要であれば、あるいは NFS Server セットアップのデバッグでサポートが必要な場合、次の詳しいチュートリアルをご覧ください。
  - [NFS チュートリアル][nfs-tutorial]

## <a name="next-steps"></a>次のステップ

関連するベスト プラクティスについては、[AKS のストレージとバックアップに関するベスト プラクティス][operator-best-practices-storage]に関する記事を参照してください。

<!-- LINKS - external -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[linux-create]: ../virtual-machines/linux/tutorial-manage-vm.md
[nfs-tutorial]: https://help.ubuntu.com/community/SettingUpNFSHowTo#Pre-Installation_Setup
[aks-virtual-network]: ./configure-kubenet.md#create-an-aks-cluster-in-the-virtual-network
[peer-virtual-networks]: ../virtual-network/tutorial-connect-virtual-networks-portal.md

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[operator-best-practices-storage]: operator-best-practices-storage.md
