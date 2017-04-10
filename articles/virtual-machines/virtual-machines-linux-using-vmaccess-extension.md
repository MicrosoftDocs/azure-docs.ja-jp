---
title: "VMAccess 拡張機能と Azure CLI 2.0 を使用したアクセスのリセット | Microsoft Docs"
description: "VMAccess 拡張機能と Azure CLI 2.0 を使用して Linux VM 上のユーザーを管理し、アクセスをリセットする方法"
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: debdb8a16c8cfd6a137bd2a7c3b82cfdbedb0d8c
ms.openlocfilehash: 4fac98d37dde195af69d8bd03fd796c6eeae3734
ms.lasthandoff: 02/27/2017


---
# <a name="manage-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli-20"></a>VMAccess 拡張機能と Azure CLI 2.0 を使用して、Linux VM 上のユーザー、SSH を管理し、ディスクをチェックまたは修復する
Linux VM 上のディスクがエラーを示しています。 何らかの理由で Linux VM の root パスワードをリセットしたか、誤って SSH 秘密キーを削除してしまいました。 これがかつてのデータ センターの時代で起きていたら、車で駆けつけ、KVM を開けて、サーバー コンソールにたどり着くことになっていたでしょう。 Azure VMAccess 拡張機能は、コンソールにアクセスして、Linux へのアクセスをリセットしたり、ディスク レベルの保守を実行したりできるその KVM スイッチとして考えてください。

この記事では、Azure VMAcesss 拡張機能を使用して、Linux 上のディスクのチェックや修復、ユーザー アクセスのリセット、ユーザー アカウントの管理、またはSSHD 構成のリセットを行う方法を説明します。 これらの手順は、[Azure CLI 1.0](virtual-machines-linux-using-vmaccess-extension-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) を使用して実行することもできます。


## <a name="ways-to-use-the-vmaccess-extension"></a>VMAccess 拡張機能の使用方法
Linux VM で VMAccess 拡張機能を使用する方法は&2; つあります。

* Azure CLI 2.0 と必要なパラメーターを使用する。
* [VMAccess 拡張機能が処理して、動作の基となる未加工の JSON ファイル](#use-json-files-and-the-vmaccess-extension)を使用する。

以下の例では、[az vm access](/cli/azure/vm/access) と適切なパラメーターを使用します。 これらの手順を実行するには、[Azure CLI 2.0](/cli/azure/install-az-cli2) の最新版をインストールし、[az login](/cli/azure/#login) を使用して Azure アカウントにログインする必要があります。

## <a name="reset-ssh-key"></a>SSH キーのリセット
次の例では、`myVM` という名前の VM 上のユーザー `azureuser` の SSH キーをリセットします。

```azurecli
az vm access set-linux-user \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="reset-password"></a>パスワードのリセット
次の例では、`myVM` という名前の VM 上のユーザー `azureuser` のパスワードをリセットします。

```azurecli
az vm access set-linux-user \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="reset-sshd"></a>SSHD のリセット
次の例では、`myVM` という名前の VM 上の SSHD 構成をリセットします。

```azurecli
az vm access reset-linux-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-a-user"></a>ユーザーの作成
次の例では、`myVM` という名前の VM 上に、認証用の SSH キーを使用して `myNewUser` という名前のユーザーを作成します。

```azurecli
az vm access set-linux-user \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="deletes-a-user"></a>ユーザーの削除
次の例では、`myVM` という名前の VM 上のユーザー `myNewUser` を削除します。

```azurecli
az vm access delete-linux-user \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```


## <a name="use-json-files-and-the-vmaccess-extension"></a>JSON ファイルと VMAccess 拡張機能の使用
次の例では、未加工の JSON ファイルを使用します。 [az vm extension set](/cli/azure/vm/extension#set) を使用して JSON ファイルを呼び出します。 これらの JSON ファイルは、Azure テンプレートから呼び出すこともできます。 

### <a name="reset-user-access"></a>ユーザー アクセスのリセット
Linux VM で root へのアクセスを失った場合、VMAccess スクリプトを起動して、ユーザー パスワードをリセットできます。

ユーザーの SSH キーをリセットするには、`reset_ssh_key.json` という名前のファイルを作成し、次の内容を追加します。

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
}
```

次を使用して VMAccess スクリプトを実行します。

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_ssh_key.json
```

ユーザー パスワードをリセットするには、`reset_user_password.json` という名前のファイルを作成し、次の内容を追加します。

```json
{
  "username":"azureuser",
  "password":"myNewPassword" 
}
```

次を使用して VMAccess スクリプトを実行します。

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_user_password.json
```

### <a name="reset-ssh"></a>SSH のリセット
Linux VM SSHD 構成を変更して、変更を確認する前に SSH 接続を閉じた場合、SSH 接続を再開できないことがあります。  VMAccess を使用して、SSH 経由でログインせずに、SSHD 構成を前回正常起動時の構成にリセットすることができます。

SSHD 構成をリセットするには、`reset_sshd.json` という名前のファイルを作成し、次の内容を追加します。

```json
{
  "reset_ssh": true
}
```

次を使用して VMAccess スクリプトを実行します。

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_sshd.json
```

### <a name="manage-users"></a>ユーザーの管理
VMAccess は、ログインしたり、sudo や root アカウントを使用したりしなくても、Linux VM 上のユーザーを管理するために使用できる Python スクリプトです。

ユーザーを作成するには、`create_new_user.json` という名前のファイルを作成し、次の内容を追加します。

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

次を使用して VMAccess スクリプトを実行します。

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings create_new_user.json
```

ユーザーを削除するには、`delete_user.json` という名前のファイルを作成し、次の内容を追加します。

```json
{
  "remove_user":"myDeleteUser"
}
```

次を使用して VMAccess スクリプトを実行します。

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings delete_user.json
```

### <a name="check-or-repair-the-disk"></a>ディスクのチェックと修復
VMAccess を使用して、Linux VM 下のディスクに対して、fsck を実行できます。 また、ディスク チェックを実行し、VMAccess を使用して、ディスクの修復を実行できます。

ディスクをチェックして修復するには、この VMAccess スクリプトを使用して、`disk_check_repair.json` という名前のファイルを作成して次の内容を追加します。

```json
{
  "check_disk": "true",
  "repair_disk": "true, user-disk-name"
}
```

次を使用して VMAccess スクリプトを実行します。

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings disk_check_repair.json
```

## <a name="next-steps"></a>次のステップ
Azure VMAccess 拡張機能を使用して Linux を更新することは、実行中の Linux VM で変更を加える&1; つの方法です。 また、cloud-init と Azure Resource Manager テンプレートのようなツールを使用して、起動時に Linux VM を変更することもできます。

[仮想マシンの拡張機能とその機能について](virtual-machines-linux-extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Linux VM 拡張機能を使用した Azure Resource Manager テンプレートの作成](virtual-machines-linux-extensions-authoring-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[cloud-init を利用し、作成時に Linux VM をカスタマイズする](virtual-machines-linux-using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


