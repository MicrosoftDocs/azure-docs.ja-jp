---
title: "Azure Cloud Shell (プレビュー) での Bash の機能 | Microsoft Docs"
description: "Azure Cloud Shell での Bash の機能の概要"
services: Azure
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: juluk
ms.openlocfilehash: 158b0b65ba53630050275c910f2a7acfc39b8759
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="features--tools-for-bash-in-azure-cloud-shell"></a>Azure Cloud Shell での Bash の機能とツール

[!include [features-introblock](../../includes/cloud-shell-features-introblock.md)]

> [!TIP]
> [PowerShell](features-powershell.md) の機能とツールも利用できます。

Cloud Shell での Bash は `Ubuntu 16.04 LTS` で実行します。

## <a name="features"></a>Features (機能)

### <a name="secure-automatic-authentication"></a>自動認証をセキュリティで保護する

Cloud Shell での Bash は、Azure CLI 2.0 のアカウント アクセスを安全かつ自動的に認証します。

### <a name="ssh-into-azure-linux-virtual-machines"></a>Azure Linux 仮想マシンへの SSH 接続

Azure CLI 2.0 からの Linux VM の作成では、既定の SSH キーを作成して、`$Home` ディレクトリに格納できます。 `$Home` に SSH キーを置くと、Cloud Shell から Azure Linux VM に直接 SSH 接続できます。 キーは、ファイル共有の acc_<user>.img に保持され、ファイル共有またはキーへのアクセスを使用または共有するときのベスト プラクティスを使います。

### <a name="home-persistence-across-sessions"></a>セッション間での $Home の永続化

セッション間でファイルを保持する場合、Cloud Shell の初回起動時に、Azure ファイル共有のアタッチについてのチュートリアルがあります。
チュートリアルが完了すると、今後のすべてのセッションで、記憶域 (`$Home\clouddrive` としてマウントされる) が自動的にアタッチされます。
さらに、Cloud Shell の Bash では、`$Home` ディレクトリが .img として Azure ファイル共有に永続化されます。
`$Home` およびマシン状態の外部にあるファイルは、セッション間で保持されません。

[Cloud Shell の Bash でのファイルの永続化については、こちらを参照してください。](persisting-shell-storage.md)

## <a name="tools"></a>ツール

|カテゴリ   |名前   |
|---|---|
|Linux シェル インタープリター|Bash<br> sh               |
|Azure ツール            |[Azure CLI 2.0](https://github.com/Azure/azure-cli) と [1.0](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy)<br> [Batch Shipyard](https://github.com/Azure/batch-shipyard) <br>    |
|テキスト エディター           |vim<br> nano<br> emacs       |
|ソース管理         |git                    |
|ビルド ツール            |make<br> maven<br> npm<br> pip         |
|コンテナー             |[Docker CLI](https://github.com/docker/cli)/[Docker Machine](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Helm](https://github.com/kubernetes/helm)<br> [DC/OS CLI](https://github.com/dcos/dcos-cli)         |
|データベース              |MySQL クライアント<br> PostgreSql クライアント<br> [sqlcmd ユーティリティ](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|その他                  |iPython クライアント<br> [Cloud Foundry CLI](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> |

## <a name="language-support"></a>言語のサポート

|言語   |バージョン   |
|---|---|
|.NET       |1.01       |
|Go         |1.7        |
|Java       |1.8        |
|Node.js    |6.9.4      |
|PowerShell  |[6.0 (ベータ)](https://github.com/PowerShell/powershell/releases)       |
|Python     |2.7 および 3.5 (既定)|

## <a name="next-steps"></a>次のステップ
[Cloud Shell の Bash のクイックスタート](quickstart.md) <br>
[Azure CLI 2.0 について](https://docs.microsoft.com/cli/azure/)
