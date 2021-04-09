---
title: プロビジョニング エージェントを無効化または削除する
description: Linux VM およびイメージのプロビジョニング エージェントを無効化または削除する方法について説明します。
author: danielsollondon
ms.service: virtual-machines
ms.collection: linux
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 7c797957c292b9859ca41951b15f58c3d0be40b2
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102561070"
---
# <a name="disable-or-remove-the-linux-agent-from-vms-and-images"></a>VM とイメージの Linux エージェントを無効化または削除する

Linux エージェントを削除する前に、Linux エージェントの削除後に VM で実行できなくなる処理について理解しておく必要があります。

Azure 仮想マシン (VM) [拡張機能](../extensions/overview.md)は、Azure VM のデプロイ後に構成および自動化タスクを実行できるようにする複数の小さなアプリケーションです。拡張機能は Azure コントロール プレーンによってインストールされ、管理されます。 [Azure Linux エージェント](../extensions/agent-linux.md)の役割は、プラットフォーム拡張機能コマンドを処理し、VM 内で拡張機能の状態を適切に維持することです。

Azure プラットフォームでは、VM の構成、監視、セキュリティ、およびユーティリティというさまざまなアプリケーションの多くの拡張機能をホストします。 ファーストパーティとサードパーティの拡張機能は多数用意されています。拡張機能が使用される主なシナリオの例としては次のようなものがあります。
* ファーストパーティである Azure のサービス (Azure Backup、Monitoring、Disk Encryption、Security、Site Replication など) をサポートします。
* SSH およびパスワードのリセット
* VM の構成 - カスタム スクリプトの実行、Chef エージェントや Puppet エージェントなどのインストールなど。
* AV 製品、VM 脆弱性ツール、VM およびアプリの監視ツールなどのサードパーティ製品。
* 拡張機能は、新しい VM のデプロイにバンドルできます。 たとえば、大規模なデプロイ、VM プロビジョンでのアプリケーションの構成、またはデプロイ後のサポート対象の拡張機能で運用するシステムに対する実行の一部にすることができます。

## <a name="disabling-extension-processing"></a>拡張機能の処理の無効化

必要に応じて拡張機能の処理を無効にする方法がいくつか用意されていますが、続行する前に、VM に展開されているすべての拡張機能を削除する **必要があります**。たとえば、Azure CLI を使用すると、[一覧を表示](/cli/azure/vm/extension#az-vm-extension-list)して [削除](/cli/azure/vm/extension#az-vm-extension-delete)できます。

```azurecli
az vm extension delete -g MyResourceGroup --vm-name MyVm -n extension_name
```
> [!Note]
> 
> 上記の操作を行わないと、プラットフォームは拡張機能の構成を送信しようと試み、40 分後にタイムアウトします。

### <a name="disable-at-the-control-plane"></a>コントロール プレーンを無効にする
今後も拡張機能が必要になるかどうか不明な場合は、Linux エージェントを VM にインストールしたままにしておいて、プラットフォームから拡張機能の処理機能を無効にすることができます。 このオプションは `Microsoft.Compute` API バージョン `2018-06-01` 以降で使用でき、インストールされている Linux エージェントのバージョンに依存しません。

```azurecli
az vm update -g <resourceGroup> -n <vmName> --set osProfile.allowExtensionOperations=false
```
上記のコマンドで "true" を設定すると、プラットフォームからこの拡張機能の処理を簡単に再び有効にすることができます。

## <a name="remove-the-linux-agent-from-a-running-vm"></a>実行中の VM から Linux エージェントを削除する

上記の手順に従って、VM から既存のすべての拡張機能を前もって **削除** していることを確認します。

### <a name="step-1-remove-the-azure-linux-agent"></a>手順 1:Azure Linux エージェントを削除する

関連付けられている構成成果物ではなく、Linux エージェントのみを削除した場合は、後日再インストールできます。 ルートとして次のいずれかを実行し、Azure Linux エージェントを削除します。

#### <a name="for-ubuntu-1804"></a>Ubuntu 18.04 以上の場合
```bash
apt -y remove walinuxagent
```

#### <a name="for-redhat--77"></a>Red Hat 7.7 以上の場合
```bash
yum -y remove WALinuxAgent
```

#### <a name="for-suse"></a>SUSE の場合
```bash
zypper --non-interactive remove python-azure-agent
```

### <a name="step-2-optional-remove-the-azure-linux-agent-artifacts"></a>手順 2:(省略可能) Azure Linux エージェントの成果物を削除する
> [!IMPORTANT] 
>
> Linux エージェントに関連付けられているすべての成果物を削除できますが、その場合は後日再インストールできなくなります。 したがって、上記のコマンドを使用して、まず Linux エージェントを無効にし、その後で Linux エージェントを削除することを強くお勧めします。 

Linux エージェントを再びインストールしないことがわかっている場合は、次のコマンドを実行できます。

#### <a name="for-ubuntu-1804"></a>Ubuntu 18.04 以上の場合
```bash
apt -y purge walinuxagent
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

#### <a name="for-redhat--77"></a>Red Hat 7.7 以上の場合
```bash
yum -y remove WALinuxAgent
rm -f /etc/waagent.conf.rpmsave
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

#### <a name="for-suse"></a>SUSE の場合
```bash
zypper --non-interactive remove python-azure-agent
rm -f /etc/waagent.conf.rpmsave
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

## <a name="preparing-an-image-without-the-linux-agent"></a>Linux エージェントを使用せずにイメージを準備する
cloud-init が既に含まれたイメージがあり、Linux エージェントを削除する必要があるが、これからも cloud-init を使用してプロビジョニングする場合は、手順 2 (および必要に応じて手順 3) の手順をルートとして実行して Azure Linux エージェントを削除してから、次のコマンドを使用して、cloud-init 構成とキャッシュ データを削除し、カスタム イメージが作成されるように VM を準備します。

```bash
cloud-init clean --logs --seed 
```

## <a name="deprovision-and-create-an-image"></a>プロビジョニングを解除してイメージを作成する
Linux エージェントには、"waagent -deprovision+user" のステップを使用して、既存のイメージ メタデータの一部をクリーンアップする機能がありますが、メタデータを削除した後は、以下のような操作を実行して、その他の機密データをイメージから削除する必要があります。

- 既存のすべての SSH ホスト キーを削除します

   ```bash
   rm /etc/ssh/ssh_host_*key*
   ```
- 管理者アカウントを削除します

   ```bash
   touch /var/run/utmp
   userdel -f -r <admin_user_account>
   ```
- ルート パスワードを削除します

   ```bash
   passwd -d root
   ```

上記の手順を完了したら、Azure CLI を使用してカスタム イメージを作成できます。


**通常のマネージド イメージを作成する**
```azurecli
az vm deallocate -g <resource_group> -n <vm_name>
az vm generalize -g <resource_group> -n <vm_name>
az image create -g <resource_group> -n <image_name> --source <vm_name>
```

**Shared Image Gallery にイメージのバージョンを作成する**

```azurecli
az sig image-version create \
    -g $sigResourceGroup 
    --gallery-name $sigName 
    --gallery-image-definition $imageDefName 
    --gallery-image-version 1.0.0 
    --managed-image /subscriptions/00000000-0000-0000-0000-00000000xxxx/resourceGroups/imageGroups/providers/images/MyManagedImage
```
### <a name="creating-a-vm-from-an-image-that-does-not-contain-a-linux-agent"></a>Linux エージェントを含まないイメージからの VM の作成
Linux エージェントが含まれていないイメージから VM を作成する場合、この VM では拡張機能がサポートされことを VM のデプロイ構成で必ず示す必要があります。

> [!NOTE] 
> 
> 上記の操作を行わないと、プラットフォームは拡張機能の構成を送信しようと試み、40 分後にタイムアウトします。

拡張機能が無効になっている VM をデプロイする場合は、[--enable-agent](/cli/azure/vm#az-vm-create) を指定して Azure CLI を実行できます。

```azurecli
az vm create \
    --resource-group $resourceGroup \
    --name $prodVmName \
    --image RedHat:RHEL:8.1-ci:latest \
    --admin-username azadmin \
    --ssh-key-value "$sshPubkeyPath" \
    --enable-agent false
```

また、`"provisionVMAgent": false,` を設定して Azure Resource Manager (ARM) テンプレートを使用することによっても、これを実行できます。

```json
"osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "linuxConfiguration": {
        "disablePasswordAuthentication": "true",
        "provisionVMAgent": false,
        "ssh": {
            "publicKeys": [
                {
                    "path": "[concat('/home/', parameters('adminUsername'), '/.ssh/authorized_keys')]",
                    "keyData": "[parameters('adminPublicKey')]"
```

## <a name="next-steps"></a>次のステップ

詳細については、[Linux のプロビジョニング](provisioning.md)に関するページを参照してください。
