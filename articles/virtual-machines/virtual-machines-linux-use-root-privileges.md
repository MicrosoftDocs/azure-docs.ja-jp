---
title: "Linux 仮想マシンでの root 権限の使用 | Microsoft Docs"
description: "Azure 上の Linux 仮想マシンで root 権限を使用する方法について説明します。"
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: a2c106a2-dceb-43a3-9dd1-50ed77685952
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: szark
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1c8c5f4e622863a0961bd5e1c7a915e9511c871f


---
# <a name="using-root-privileges-on-linux-virtual-machines-in-azure"></a>Azure 上の Linux 仮想マシンでの root 権限の使用
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

既定では、 `root` ユーザーは Azure 上の Linux 仮想マシンで無効になっています。 ユーザーは `sudo` コマンドを使用して、昇格された特権でコマンドを実行できます。 ただし、システムがプロビジョニングされている方法によって操作が異なります。

1. **SSH キーとパスワードまたはパスワードのみ**: 仮想マシンが証明書 (`.CER` ファイル)、SSH キーとパスワード、またはユーザー名とパスワードだけでプロビジョニングされた場合。 この場合は、コマンドを実行する前に `sudo` からユーザーのパスワードの入力が求められます。
2. **SSH キーのみ**: 仮想マシンが証明書 (`.cer`、`.pem`、または `.pub` ファイル) または SSH キーでプロビジョニングされ、パスワードがない場合。  この場合は、コマンドを実行する前に `sudo` **求められません** 。

## <a name="ssh-key-and-password-or-password-only"></a>SSH キーとパスワード、またはパスワードのみ
SSH キーまたはパスワード認証を使用して Linux 仮想マシンにログインし、`sudo` を使用してコマンドを実行します。たとえば、次のようになります。

    # sudo <command>
    [sudo] password for azureuser:

この場合はパスワードの入力が求められます。 パスワードの入力後、`sudo` によってコマンドが `root` 権限で実行されます。

`/etc/sudoers.d/waagent` ファイルを編集して、パスワードなし sudo を有効にすることもできます。

    #/etc/sudoers.d/waagent
    azureuser ALL = (ALL) NOPASSWD: ALL

この変更によって、"azureuser" ユーザーはパスワードなしで sudo を実行できます。

## <a name="ssh-key-only"></a>SSH キーのみ
SSH キー認証を使用して Linux 仮想マシンにログオンし、`sudo` を使用してコマンドを実行します。たとえば、次のようになります。

    # sudo <command>

この場合はパスワードの入力が **求められません** 。 `<enter>` を押した後、`sudo` によってコマンドが `root` 権限で実行されます。




<!--HONumber=Nov16_HO3-->


