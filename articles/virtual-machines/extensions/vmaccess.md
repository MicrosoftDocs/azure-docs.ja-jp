---
title: Azure Linux VM へのアクセスのリセット
description: VMAccess 拡張機能と Azure CLI を使用して Linux VM 上の管理ユーザーを管理し、アクセスをリセットする方法
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 05/10/2018
ms.openlocfilehash: e0e63762fc151d93cb34885c04dd4d03285c29f1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102557048"
---
# <a name="manage-administrative-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli"></a>VMAccess 拡張機能と Azure CLI を使用して、Linux VM 上の管理ユーザー、SSH を管理し、ディスクをチェックまたは修復する
## <a name="overview"></a>概要
Linux VM 上のディスクがエラーを示しています。 何らかの理由で Linux VM の root パスワードをリセットしたか、誤って SSH 秘密キーを削除してしまいました。 これがかつてのデータ センターの時代で起きていたら、車で駆けつけ、KVM を開けて、サーバー コンソールにたどり着くことになっていたでしょう。 Azure VMAccess 拡張機能は、コンソールにアクセスして、Linux へのアクセスをリセットしたり、ディスク レベルの保守を実行したりできるその KVM スイッチとして考えてください。

この記事では、Azure Resource Manager 仮想マシンで Azure VMAccess 拡張機能を使用して、Linux 上でのディスクのチェックや修復、ユーザー アクセスのリセット、管理ユーザー アカウントの管理、または SSH 構成の更新を行う方法について説明します。 クラシック仮想マシンを管理する必要がある場合は、[クラシック VM のドキュメント](/previous-versions/azure/virtual-machines/linux/classic/reset-access-classic)をご覧ください。 
 
> [!NOTE]
> AAD ログイン拡張機能のインストール後に、VMAccess 拡張機能を使用して VM のパスワードをリセットする場合は、AAD ログイン拡張機能を再度実行して、コンピューターの AAD ログインを再度有効にする必要があります。

## <a name="prerequisites"></a>前提条件
### <a name="operating-system"></a>オペレーティング システム

VMAccess 拡張機能は、次の Linux ディストリビューションに対して実行することができます。

| Distribution | Version |
|---|---|
| Ubuntu | 16.04 LTS、14.04 LTS、12.04 LTS |
| Debian | Debian 7.9+、8.2+ |
| Red Hat | RHEL 6.7+、7.1+ |
| Oracle Linux | 6.4+、7.0+ |
| Suse | 11、12 |
| openSUSE | openSUSE Leap 42.2 + |
| CentOS | CentOS 6.3+、7.0+ |
| CoreOS | 494.4.0+ |

## <a name="ways-to-use-the-vmaccess-extension"></a>VMAccess 拡張機能の使用方法
Linux VM で VMAccess 拡張機能を使用する方法は 2 つあります。

* Azure CLI と必要なパラメーターを使用する。
* [VMAccess 拡張機能が処理して、動作の基となる未加工の JSON ファイル](#use-json-files-and-the-vmaccess-extension)を使用する。

次の例では、[az vm user](/cli/azure/vm/user) コマンドを使用します。 これらの手順を実行するには、[Azure CLI](/cli/azure/install-az-cli2) の最新版をインストールし、[az login](/cli/azure/reference-index) を使用して Azure アカウントにログインする必要があります。

## <a name="update-ssh-key"></a>SSH キーを更新する
次の例では、`myVM` という名前の VM 上のユーザー `azureuser` の SSH キーを更新します。

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

> **注:** `az vm user update` コマンドは、VM 上の管理者ユーザーの `~/.ssh/authorized_keys` ファイルに新しい公開キー テキストを追加します。 これにより、既存の SSH キーが置き換えられたり、削除されたりすることはありません。 これにより、デプロイ時や VMAccess 拡張機能での以降の更新時に設定された以前のキーが削除されることはありません。

## <a name="reset-password"></a>[パスワードのリセット]
次の例では、`myVM` という名前の VM 上のユーザー `azureuser` のパスワードをリセットします。

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="restart-ssh"></a>SSH を再起動する
次の例は、SSH デーモンを再起動し、`myVM`という名前の VM 上で SSH の構成を既定値にリセットします。

```azurecli-interactive
az vm user reset-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-an-administrativesudo-user"></a>管理/sudo ユーザーの作成
次の例では、**sudo** のアクセス許可を持つ `myNewUser` という名前のユーザーを作成します。 アカウントでは、`myVM` という名前の VM 上で認証用 SSH キーを使用します。 この方法は、現在の資格情報を紛失したり、忘れたりした場合に、VM へのアクセスを回復できるように設計されています。 ベスト プラクティスとしては、**sudo** のアクセス許可を持つアカウントを制限する必要があります。

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="delete-a-user"></a>ユーザーの削除
次の例では、`myVM` という名前の VM 上のユーザー `myNewUser` を削除します。

```azurecli-interactive
az vm user delete \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```

## <a name="use-json-files-and-the-vmaccess-extension"></a>JSON ファイルと VMAccess 拡張機能の使用
次の例では、未加工の JSON ファイルを使用します。 [az vm extension set](/cli/azure/vm/extension) を使用して JSON ファイルを呼び出します。 これらの JSON ファイルは、Azure テンプレートから呼び出すこともできます。 

### <a name="reset-user-access"></a>ユーザー アクセスのリセット
Linux VM 上でルートへのアクセスが失われた場合は、VMAccess スクリプトを起動して、ユーザーの SSH キーまたはパスワードを更新できます。

ユーザーの SSH 公開キーを更新するには、`update_ssh_key.json` という名前のファイルを作成し、次の形式の設定を追加します。 `username`と `ssh_key` パラメーターは、独自の値に置き換えてください。

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNGxxxxxx2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7xxxxxxwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5wxxtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
}
```

次を使用して VMAccess スクリプトを実行します。

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings update_ssh_key.json
```

ユーザー パスワードをリセットするには、`reset_user_password.json` という名前のファイルを作成し、次の形式の設定を追加します。 `username`と `password` パラメーターは、独自の値に置き換えてください。

```json
{
  "username":"azureuser",
  "password":"myNewPassword" 
}
```

次を使用して VMAccess スクリプトを実行します。

```azurecli-interactive
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

```azurecli-interactive
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

```azurecli-interactive
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

```azurecli-interactive
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

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings disk_check_repair.json
```
## <a name="troubleshoot-and-support"></a>トラブルシューティングとサポート

### <a name="troubleshoot"></a>トラブルシューティング

拡張機能のデプロイ状態に関するデータを取得するには、Azure Portal か Azure CLI を使用します。 特定の VM の拡張機能のデプロイ状態を確認するには、Azure CLI を使用して次のコマンドを実行します。

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

### <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、[サポートの要求] をクリックします。 Azure サポートの使用方法の詳細については、「 [Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。
