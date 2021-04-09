---
title: SCP を使用して Azure Linux VM とファイルをやり取りする
description: SCP と SSH キー ペアを使用して Azure の Linux VM とファイルを安全にやり取りする方法について説明します。
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure
ms.topic: how-to
ms.date: 07/12/2017
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 83b57055ee7a3fedab014abeab96520c3877b843
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558442"
---
# <a name="move-files-to-and-from-a-linux-vm-using-scp"></a>SCP を使用して Linux VM とファイルをやり取りする

この記事では、Secure Copy (SCP) を使用して、ワークステーションと Azure Linux VM 間でファイルをやり取りする方法を説明します。 Azure インフラストラクチャの管理においては、ワークステーションと Linux VM の間でファイルを安全かつ迅速にやり取りできることが重要です。 

この記事では、[SSH の公開キー ファイルおよび秘密キー ファイル](mac-create-ssh-keys.md)を使用して Azure にデプロイされた Linux VM が必要です。 また、ローカル コンピューター用の SCP クライアントも必要です。 SSH 上に構築されており、ほとんどの Linux および Mac のコンピューターの既定の Bash シェルと、一部の Windows シェルに含まれています。

## <a name="quick-commands"></a>クイック コマンド

Linux VM にファイルをコピーする

```bash
scp file azureuser@azurehost:directory/targetfile
```

Linux VM からファイルをコピーする

```bash
scp azureuser@azurehost:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>詳細なチュートリアル

例として、Linux VM に Azure 構成ファイルを移動し、ログ ファイルのディレクトリを取得します。両方とも SCP キーと SSH キーを使用します。   

## <a name="ssh-key-pair-authentication"></a>SSH キー ペア認証

SCP は、トランスポート層に SSH を使用します。 SSH は転送先のホストで認証を処理し、SSH に既定で備わっている暗号化されたトンネルでファイルを移動します。 SSH 認証に、ユーザー名とパスワードを使用できます。 ただし、セキュリティのベスト プラクティスとして、SSH 公開キー認証および秘密キー認証をお勧めします。 SSH によって接続が認証されると、SCP がファイルのコピーを開始します。 適切に構成された `~/.ssh/config` と SSH 公開キーおよび秘密キーを使用することによって、(IP アドレスの) サーバー名のみを使用して SCP 接続を確立できます。 SSH キーが 1 つしかない場合は、SCP が `~/.ssh/` ディレクトリでキーを探し、既定で VM へのログインに使用します。

`~/.ssh/config` と SSH 公開キーと秘密キーの構成方法について詳しくは、[SSH キーの作成](mac-create-ssh-keys.md)に関する記事をご覧ください。

## <a name="scp-a-file-to-a-linux-vm"></a>Linux VM へのファイルの SCP

最初の例では、Azure 構成ファイルを、自動化のデプロイに使用される Linux VM にコピーします。 このファイルには秘密情報が含まれる Azure API の資格情報が入っているため、セキュリティが重要です。 SSH が提供する暗号化されたトンネルがファイルのコンテンツを保護します。

次のコマンドは *myserver.eastus.cloudapp.azure.com* を使用してローカルの *.azure/config* ファイルを Azure VM にコピーします。 Azure VM の管理者ユーザー名は *azureuser* です。 ファイルのターゲットは */home/azureuser/* ディレクトリです。 このコマンドの値を独自の値に置き換えます。

```bash
scp ~/.azure/config azureuser@myserver.eastus.cloudapp.com:/home/azureuser/config
```

## <a name="scp-a-directory-from-a-linux-vm"></a>Linux VM からのディレクトリの SCP

この例では、ログ ファイルが入ったディレクトリを Linux VM からワークステーションにコピーします。 ログ ファイルには機微なデータや秘密のデータが含まれている場合があります。 ただし、SCP を使用することでログ ファイルのコンテンツが暗号化されます。 SCP による転送を使用すれば、セキュリティで保護しながら、ログ ディレクトリやファイルをワークステーションに簡単に取得できます。

次のコマンドは、Azure VM の */home/azureuser/logs/* ディレクトリにあるファイルをローカルの /tmp ディレクトリにコピーします。

```bash
scp -r azureuser@myserver.eastus.cloudapp.com:/home/azureuser/logs/. /tmp/
```

`-r` フラグを使用すると、コマンド内に列挙されたディレクトリのポイントから、ファイルやディレクトリを再帰的にコピーするように SCP に指示できます。  また、コマンドラインの構文は `cp` コピー コマンドの構文に似ています。

## <a name="next-steps"></a>次のステップ

* [VMAccess 拡張機能を使用して、Azure Linux VM 上のユーザー、SSH を管理し、ディスクをチェックまたは修復する](../extensions/vmaccess.md?toc=/azure/virtual-machines/linux/toc.json)
