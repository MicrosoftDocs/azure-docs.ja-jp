---
title: "SCP を使用した Linux VM とのファイルのやり取り | Microsoft Docs"
description: "SCP と SSH キー ペアを使用した Linux VM との安全なファイルのやり取りについて説明します。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/14/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 5ca865475b82b73e5c7e1ab21f098a87548605d9
ms.openlocfilehash: fe07ea13f202f19e399a8ede163fcba932490418


---

# <a name="moving-files-to-and-from-a-linux-vm-using-scp"></a>SCP を使用した Linux VM とのファイルのやり取り

この記事では、Secure Copy (SCP) を使用して、ワークステーションと Azure Linux VM 間でファイルをやり取りする方法を説明します。  たとえば、Linux VM への Azure 構成ファイルの移動や、ログ ファイル ディレクトリの取得には、ともに SCP と SSH キーを使用します。   

この記事の要件は次のとおりです。

- [Azure アカウント](https://azure.microsoft.com/pricing/free-trial/)

- [SSH パブリック キー ファイルおよびプライベート キー ファイル](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>クイック コマンド

Linux VM にファイルをコピーする

```bash
scp file user@host:directory/targetfile
```

Linux VM からファイルをコピーする

```bash
scp user@host:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>詳細なチュートリアル

Azure インフラストラクチャの管理においては、ワークステーションと Linux VM 間での迅速かつ安全なファイルのやり取りが重要になります。  この記事の説明では SCP を使用します。SCP は、SSH 上に構築されたツールであり、Linux、Mac、Windows の Bash シェルに既定で含まれています。

## <a name="ssh-key-pair-authentication"></a>SSH キー ペア認証

SCP は、トランスポート層に SSH を使用します。  転送に SSH を使用することによって、SSH に規定で備わっている暗号化されたトンネルでファイルを移動しながら、転送先ホストでの認証を処理します。  SSH 認証ではユーザー名とパスワードを使用できますが、最適なセキュリティを実現するためには SSH パブリック キーとプライベート キーによる認証を強くお勧めします。 SSH によって接続が認証されると、SCP によるファイルのコピー プロセスが開始されます。  適切に構成された `~/.ssh/config` と SSH パブリック キーとプライベート キーを使用することによって、ユーザー名ではなくサーバー名のみを使って SCP 接続を確立できます。  SSH キーが&1; つしかない場合は、SCP が `~/.ssh/` ディレクトリでキーを探し、既定で VM へのログインに使用します。

`~/.ssh/config` と SSH パブリック キーとプライベート キーの構成方法についての詳細は、[SSH キーの作成](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事に従ってください。

## <a name="scp-a-file-to-a-linux-vm"></a>Linux VM へのファイルの SCP

最初の例では、Azure 資格情報ファイルを、オートメーションのデプロイに使用される Linux VM にコピーします。  このファイルには重要な機密情報を含む Azure API 資格情報が含まれるためセキュリティが重要となり、SSH によって提供される暗号化されたトンネルによって、ファイルの内容が保護されます。

```bash
scp ~/.azure/credentials myserver:/home/ahmet/.azure/credentials
```

## <a name="scp-a-directory-from-a-linux-vm"></a>Linux VM からのディレクトリの SCP

この例では、ログ ファイルが入ったディレクトリを Linux VM からワークステーションにコピーします。  ログ ファイルに機密情報や秘密データが含まれているかどうかにかかわらず、SCP によってログ ファイルの内容が暗号化されます。  SCP による安全な転送を使用すれば、セキュリティで保護しながら、ログ ディレクトリやファイルをワークステーションに簡単に取得できます。

```bash
scp -r myserver:/home/ahmet/logs/ /tmp/.
```

`-r` cli フラグを使用すると、コマンド内にリスト化されたディレクトリのポイントからファイルやディレクトリを再帰的にコピーするように SCP に指示できます。  また、コマンドラインの構文は `cp` コピー コマンドの構文に似ています。

## <a name="next-steps"></a>次のステップ

* [VMAccess 拡張機能を使用して、Azure Linux VM 上のユーザー、SSH を管理し、ディスクをチェックまたは修復する](virtual-machines-linux-using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SSHD の構成により Linux VM で SSH パスワードを無効化する](virtual-machines-linux-mac-disable-ssh-password-usage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



<!--HONumber=Dec16_HO3-->


