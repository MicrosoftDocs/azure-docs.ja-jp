---
title: "Azure Linux VM へのアクセスのリセット | Microsoft Docs"
description: "VMAccess 拡張機能と Azure CLI 2.0 を使用して Linux VM 上の管理ユーザーを管理し、アクセスをリセットする方法"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 08/04/2017
ms.author: danlep
ms.openlocfilehash: 3596b50b68cabf212218825566c0f8313f054f65
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="manage-administrative-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli-20"></a>VMAccess 拡張機能と Azure CLI 2.0 を使用して、Linux VM 上の管理ユーザー、SSH を管理し、ディスクをチェックまたは修復する
Linux VM 上のディスクがエラーを示しています。 何らかの理由で Linux VM の root パスワードをリセットしたか、誤って SSH 秘密キーを削除してしまいました。 これがかつてのデータ センターの時代で起きていたら、車で駆けつけ、KVM を開けて、サーバー コンソールにたどり着くことになっていたでしょう。 Azure VMAccess 拡張機能は、コンソールにアクセスして、Linux へのアクセスをリセットしたり、ディスク レベルの保守を実行したりできるその KVM スイッチとして考えてください。

この記事では、Azure VMAccess 拡張機能を使用して、Linux 上のディスクのチェックや修復、ユーザー アクセスのリセット、管理ユーザー アカウントの管理、または SSH 構成のリセットを行う方法を説明します。 これらの手順は、[Azure CLI 1.0](using-vmaccess-extension-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) を使用して実行することもできます。


## <a name="ways-to-use-the-vmaccess-extension"></a>VMAccess 拡張機能の使用方法
Linux VM で VMAccess 拡張機能を使用する方法は 2 つあります。

* Azure CLI 2.0 と必要なパラメーターを使用する。
* [VMAccess 拡張機能が処理して、動作の基となる未加工の JSON ファイル](#use-json-files-and-the-vmaccess-extension)を使用する。

次の例では、[az vm user](/cli/azure/vm/user) コマンドを使用します。 これらの手順を実行するには、[Azure CLI 2.0](/cli/azure/install-az-cli2) の最新版をインストールし、[az login](/cli/azure/#login) を使用して Azure アカウントにログインする必要があります。

## <a name="reset-ssh-key"></a>SSH キーのリセット
次の例では、`myVM` という名前の VM 上のユーザー `azureuser` の SSH キーをリセットします。

```azurecli
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="reset-password"></a>パスワードのリセット
次の例では、`myVM` という名前の VM 上のユーザー `azureuser` のパスワードをリセットします。

```azurecli
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="restart-ssh"></a>SSH を再起動する
次の例は、SSH デーモンを再起動し、`myVM`という名前の VM 上で SSH の構成を既定値にリセットします。

```azurecli
az vm user reset-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-an-administrativesudo-user"></a>管理/sudo ユーザーの作成
次の例では、**sudo** のアクセス許可を持つ `myNewUser` という名前のユーザーを作成します。 アカウントでは、`myVM` という名前の VM 上で認証用 SSH キーを使用します。 この方法は、現在の資格情報を紛失したり、忘れたりした場合に、VM へのアクセスを回復できるように設計されています。 ベスト プラクティスとしては、**sudo** のアクセス許可を持つアカウントを制限する必要があります。

```azurecli
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```



## <a name="delete-a-user"></a>ユーザーの削除
次の例では、`myVM` という名前の VM 上のユーザー `myNewUser` を削除します。

```azurecli
az vm user delete \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```


## <a name="use-json-files-and-the-vmaccess-extension"></a>JSON ファイルと VMAccess 拡張機能の使用
次の例では、未加工の JSON ファイルを使用します。 [az vm extension set](/cli/azure/vm/extension#set) を使用して JSON ファイルを呼び出します。 これらの JSON ファイルは、Azure テンプレートから呼び出すこともできます。 

### <a name="reset-user-access"></a>ユーザー アクセスのリセット
Linux VM で root へのアクセスを失った場合、VMAccess スクリプトを起動して、ユーザーの SSH キーまたはパスワードをリセットできます。

ユーザーの SSH 公開キーをリセットするには、`reset_ssh_key.json` という名前のファイルを作成し、次の形式の設定を追加します。 `username`と `ssh_key` パラメーターは、独自の値に置き換えてください。

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNGxxxxxx2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7xxxxxxwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5wxxtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
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

ユーザー パスワードをリセットするには、`reset_user_password.json` という名前のファイルを作成し、次の形式の設定を追加します。 `username`と `password` パラメーターは、独自の値に置き換えてください。

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

### <a name="restart-ssh"></a>SSH を再起動する
SSH デーモンを再起動し、SSH 構成を既定値にリセットするには、`reset_sshd.json` という名前のファイルを作成します。 次の内容を追加します。

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

### <a name="manage-administrative-users"></a>管理ユーザーの管理

認証に SSH キーを使用する、**sudo** のアクセス許可を持つユーザーを作成するには、`create_new_user.json` という名前のファイルを作成し、次の形式の設定を追加します。 `username` と `ssh_key` パラメーターは、独自の値に置き換えてください。 この方法は、現在の資格情報を紛失したり、忘れたりした場合に、VM へのアクセスを回復できるように設計されています。 ベスト プラクティスとしては、**sudo** のアクセス許可を持つアカウントを制限する必要があります。

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

ユーザーを削除するには、`delete_user.json` という名前のファイルを作成し、次の内容を追加します。 `remove_user` パラメーターは、独自の値に置き換えてください。

```json
{
  "remove_user":"myNewUser"
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
VMAccess を使用して、Linux VM に追加したディスクをチェックして修復することも可能です。

ディスクをチェックして修復するには、`disk_check_repair.json` という名前のファイルを作成し、次の形式の設定を追加します。 `repair_disk` の名前は、独自の値に置き換えてください。

```json
{
  "check_disk": "true",
  "repair_disk": "true, mydiskname"
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
Azure VMAccess 拡張機能を使用して Linux を更新することは、実行中の Linux VM で変更を加える 1 つの方法です。 また、cloud-init と Azure Resource Manager テンプレートのようなツールを使用して、起動時に Linux VM を変更することもできます。

[Linux 用の仮想マシンの拡張機能とその機能](extensions-features.md)

[Linux VM 拡張機能を使用した Azure Resource Manager テンプレートの作成](../windows/template-description.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[cloud-init を利用し、作成時に Linux VM をカスタマイズする](using-cloud-init.md)

