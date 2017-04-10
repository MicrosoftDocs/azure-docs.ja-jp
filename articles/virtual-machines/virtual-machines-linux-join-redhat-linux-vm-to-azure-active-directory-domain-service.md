---
title: "Red Hat Linux VM を Azure Active Directory DS に参加させる | Microsoft Docs"
description: "既存の Red Hat Enterprise Linux 7 VM を Azure Active Directory ドメイン サービスに参加させる方法。"
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 847a5391cadb00c68e5ee5e12066fe910fd8a259
ms.openlocfilehash: 017c17dc15596ce05400500d709287b7ce52611e


---

# <a name="join-a-redhat-linux-vm-to-an-azure-active-directory-domain-service"></a>Red Hat Linux VM を Azure Active Directory ドメインサービスに参加させる

この記事では、Red Hat Enterprise Linux (RHEL) 7 仮想マシンを Active Directory Domain Services (AADDS) の管理対象ドメインに参加させる方法について説明します。  要件は次のとおりです。

- [Azure アカウント](https://azure.microsoft.com/pricing/free-trial/)

- [SSH パブリック キー ファイルおよびプライベート キー ファイル](virtual-machines-linux-mac-create-ssh-keys.md)

- [Active Directory Domain Services DC](../active-directory-domain-services/active-directory-ds-getting-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>クイック コマンド

_各例を独自の設定に置き換えてください。_

### <a name="switch-the-azure-cli-to-classic-deployment-mode"></a>Azure CLI をクラシック デプロイ モードに切り替える

```azurecli
azure config mode asm
```

### <a name="search-for-a-rhel-version-and-image"></a>RHEL のバージョンとイメージを検索する

```azurecli
azure vm image list | grep "Red Hat"
```

### <a name="create-a-redhat-linux-vm"></a>Red Hat Linux VM を作成する

```azurecli
azure vm create myVM \
-o a879bbefc56a43abb0ce65052aac09f3__RHEL_7_2_Standard_Azure_RHUI-20161026220742 \
-g ahmet \
-p myPassword \
-e 22 \
-t "~/.ssh/id_rsa.pub" \
-z "Small" \
-l "West US"
```

### <a name="ssh-to-the-vm"></a>VM への SSH 接続

```bash
ssh -i ~/.ssh/id_rsa ahmet@myVM
```

### <a name="update-yum-packages"></a>YUM パッケージを更新する

```bash
sudo yum update
```

### <a name="install-packages-needed"></a>必要なパッケージをインストールする

```bash
sudo yum -y install realmd sssd krb5-workstation krb5-libs
```

Linux 仮想マシンに必要なパッケージがインストールされたら、続いて仮想マシンを管理対象ドメインに参加させます。

### <a name="discover-the-aad-domain-services-managed-domain"></a>AAD ドメイン サービスの管理対象ドメインを検出する

```bash
sudo realm discover mydomain.com
```

### <a name="initialize-kerberos"></a>kerberos を初期化する

”AAD DC 管理者” グループに所属するユーザーを指定します。 指定されたユーザーのみが、管理対象ドメインにコンピューターを参加させることができます。

```bash
kinit ahmet@mydomain.com
```

### <a name="join-the-machine-to-the-domain"></a>コンピューターをドメインに参加させる

```bash
sudo realm join --verbose mydomain.com -U 'ahmet@mydomain.com'
```

### <a name="verify-the-machine-is-joined-to-the-domain"></a>コンピューターがドメインに参加していることを確認する

```bash
ssh -l ahmet@mydomain.com mydomain.cloudapp.net
```

## <a name="next-steps"></a>次のステップ

* [Azure のオンデマンド Red Hat Enterprise Linux VM 用 Red Hat Update Infrastructure (RHUI)](virtual-machines-linux-update-infrastructure-redhat.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure Resource Manager の仮想マシンの Key Vault を設定する](virtual-machines-linux-key-vault-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure Resource Manager テンプレートと Azure CLI を使用した仮想マシンのデプロイと管理](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



<!--HONumber=Dec16_HO2-->


