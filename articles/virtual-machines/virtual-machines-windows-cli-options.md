---
title: "Windows 上での Azure CLI の使用 | Microsoft Docs"
description: "Windows 上での Azure CLI の使用"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/14/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: eb2fff3b72646e63c53054a246a47c18df414c60
ms.lasthandoff: 03/11/2017

---

# <a name="using-the-azure-cli-on-windows"></a>Windows 上での Azure CLI の使用

Azure コマンド ライン インターフェイス (CLI) は、Azure リソースの作成と管理のためのコマンドラインとスクリプト環境を提供します。 Azure CLI は、Mac OS X、Linux、Windows オペレーティング システムで使用できます。 これらのオペレーティング システムにわたって CLI コマンドは同じですが、オペレーティング システム固有のスクリプト構文は異なることがあります。

このドキュメントでは、Windows 上で Azure CLI をインストールおよび実行する方法の詳細と、それぞれにおける構文に関する考慮事項の詳細を説明します。 詳細な Azure CLI のドキュメントについては、[Azure CLI ドキュメント]( https://docs.microsoft.com/en-us/cli/azure/overview)に関するページをご覧ください。

## <a name="windows-subsystem-for-linux"></a>Windows Subsystem for Linux

Windows Subsystem for Linux (WSL) は、Windows 10 Anniversary エディション上で Ubuntu Linux 環境を提供します。 有効な場合、WSL は Azure CLI スクリプトの作成および実行に使用できるネイティブ Bash エクスペリエンスを提供します。 WSL は、ネイティブ Bash エクスペリエンスを提供するため、Azure CLI スクリプトは Mac OS X、Linux、Windows の間で変更なしに共有できます。

WSL 内で Azure CLI を使用するには、次の手順を実行します。

|タスク | このサンプルについての指示 |
|---|---|
| WSL を有効にする | [WSL ドキュメントをインストールする](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide) |
| Azure CLI のインストール |[WSL の Ubuntu 14.04 上に CLI をインストールする](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2#ubuntu)|

## <a name="powershell"></a>PowerShell

Azure CLI は、Windows でネイティブに実行できます。 この構成では、Windows オペレーティング システム上に Azure CLI パッケージがインストールされ、PowerShell からコマンドを実行できます。 この構成では、任意のサポートされているバージョンの Windows 上で Azure CLI コマンドおよびスクリプトを実行できますが、プラットフォーム固有のスクリプト構文が必要です。 このため、Mac OS X、Linux、Windows 間で変更なしにスクリプトを共有できるとは限りません。

Windows 上で Azure CLI を使用するには、[Windows 上での CLI のインストール](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2#windows)に関する記事の手順を使用してパッケージをインストールします。

## <a name="docker-image"></a>Docker イメージ

Docker for Windows を使用する場合は、Azure CLI を含む Docker イメージで起動できます。 このイメージは、Linux に基づいており、ネイティブ Bash エクスペリエンスが含まれています。  Docker for Windows と Azure CLI イメージを使用する場合は、Mac OS X、Linux、Windows の間でスクリプトを共有します。 

Docker for Windows 上で Azure CLI を使用するには、Docker for Windows が実行されていることを確認し、次のコマンドを実行します。

```bash
docker run -it azuresdk/azure-cli-python:latest bash
```

完了すると、Azure CLI ツールが事前に読み込まれた Bash セッションが開始されます。

## <a name="next-steps"></a>次のステップ

[Azure 仮想マシン用の CLI サンプル](./virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Azure Web Apps 用の CLI サンプル](../app-service-web/app-service-cli-samples.md)

[Azure SQL 用の CLI サンプル](../sql-database/sql-database-cli-samples.md)

