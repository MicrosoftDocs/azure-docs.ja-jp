---
title: 仮想マシンおよび仮想マシン スケール セット向けの Azure Disk Encryption
description: 仮想マシン (VM) と VM スケール セットに対する Azure Disk Encryption について説明します。 Azure Disk Encryption は、Linux と Windows の両方の VM で動作します。
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/15/2019
ms.custom: seodec18
ms.openlocfilehash: 21194bf2fe76a7eb0ee034d4a502c20ee3032dd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87543675"
---
# <a name="azure-disk-encryption-for-virtual-machines-and-virtual-machine-scale-sets"></a>仮想マシンおよび仮想マシン スケール セット向けの Azure Disk Encryption

Azure Disk Encryption は、Linux と Windows 両方の仮想マシンに加えて、仮想マシン スケール セットに適用できます。 

## <a name="linux-virtual-machines"></a>Linux Virtual Machines

以下の記事では、Linux 仮想マシンの暗号化に関するガイダンスを提供します。

### <a name="current-version-of-azure-disk-encryption"></a>現在のバージョンの Azure Disk Encryption

- [Linux 仮想マシン向け Azure Disk Encryption の概要](../../virtual-machines/linux/disk-encryption-overview.md)
- [Linux VM での Azure Disk Encryption シナリオ](../../virtual-machines/linux/disk-encryption-linux.md)
- [Azure CLI を使用して Linux VM を作成、暗号化する](../../virtual-machines/linux/disk-encryption-cli-quickstart.md)
- [Azure PowerShell を使用して Linux VM を作成、暗号化する](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)
- [Azure portal を使用した Linux VM の作成と暗号化](../../virtual-machines/linux/disk-encryption-portal-quickstart.md)
- [Linux の Azure Disk Encryption 拡張機能スキーマ](../../virtual-machines/extensions/azure-disk-enc-linux.md)
- [Azure Disk Encryption 用のキー コンテナーの作成と構成](../../virtual-machines/linux/disk-encryption-key-vault.md)
- [Azure Disk Encryption のサンプル スクリプト](../../virtual-machines/linux/disk-encryption-sample-scripts.md)
- [Azure Disk Encryption のトラブルシューティング](../../virtual-machines/linux/disk-encryption-troubleshooting.md)
- [Azure Disk Encryption に関してよく寄せられる質問](../../virtual-machines/linux/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure AD を使用した Azure ディスク暗号化 (以前のバージョン)

- [Linux 仮想マシン向けの Azure AD を使用した Azure Disk Encryption の概要](../../virtual-machines/linux/disk-encryption-overview-aad.md)
- [Linux VM での Azure AD を使用した Azure Disk Encryption のシナリオ](../../virtual-machines/linux/disk-encryption-linux.md)
- [Azure AD を使用した Azure Disk Encryption 用のキー コンテナーの作成と構成 (以前のリリース)](../../virtual-machines/linux/disk-encryption-key-vault-aad.md)

## <a name="windows-virtual-machines"></a>Windows Virtual Machines

以下の記事では、Windows 仮想マシンの暗号化に関するガイダンスを提供します。

### <a name="current-version-of-azure-disk-encryption"></a>現在のバージョンの Azure Disk Encryption

- [Windows 仮想マシン向け Azure Disk Encryption の概要](../../virtual-machines/windows/disk-encryption-overview.md)
- [Windows VM での Azure Disk Encryption シナリオ](../../virtual-machines/windows/disk-encryption-windows.md)
- [Azure CLI を使用して Windows VM を作成、暗号化する](../../virtual-machines/windows/disk-encryption-cli-quickstart.md)
- [Azure PowerShell を使用して Windows VM を作成、暗号化する](../../virtual-machines/windows/disk-encryption-powershell-quickstart.md)
- [Azure portal を使用した Windows 仮想マシンの作成と暗号化](../../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- [Windows の Azure Disk Encryption 拡張機能スキーマ](../../virtual-machines/extensions/azure-disk-enc-windows.md)
- [Azure Disk Encryption 用のキー コンテナーの作成と構成](../../virtual-machines/windows/disk-encryption-key-vault.md)
- [Azure Disk Encryption のサンプル スクリプト](../../virtual-machines/windows/disk-encryption-sample-scripts.md)
- [Azure Disk Encryption のトラブルシューティング](../../virtual-machines/windows/disk-encryption-troubleshooting.md)
- [Azure Disk Encryption に関してよく寄せられる質問](../../virtual-machines/windows/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure AD を使用した Azure ディスク暗号化 (以前のバージョン)

- [Windows 仮想マシン向けの Azure AD を使用した Azure Disk Encryption の概要](../../virtual-machines/windows/disk-encryption-overview-aad.md)
- [Windows VM での Azure AD を使用した Azure Disk Encryption のシナリオ](../../virtual-machines/windows/disk-encryption-windows.md)
- [Azure AD を使用した Azure Disk Encryption 用のキー コンテナーの作成と構成 (以前のリリース)](../../virtual-machines/windows/disk-encryption-key-vault-aad.md)

## <a name="virtual-machine-scale-sets"></a>仮想マシン スケール セット

以下の記事では、仮想マシン スケール セットの暗号化に関するガイダンスを提供します。

- [仮想マシン スケール セット向け Azure Disk Encryption の概要](../../virtual-machine-scale-sets/disk-encryption-overview.md) 
- [Azure CLI を使用して仮想マシン スケール セットを暗号化する](../../virtual-machine-scale-sets/disk-encryption-cli.md) 
- [Azure PowerShell を使用して仮想マシン スケール セットを暗号化する](../../virtual-machine-scale-sets/disk-encryption-powershell.md)
- [Azure Resource Manager を使用して仮想マシン スケール セットを暗号化する](../../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)
- [Azure Disk Encryption のキー コンテナーを作成して構成する](../../virtual-machine-scale-sets/disk-encryption-key-vault.md)
- [Azure Disk Encryption を仮想マシン スケール セットの拡張機能のシーケンス処理と共に使用する](../../virtual-machine-scale-sets/disk-encryption-extension-sequencing.md)

## <a name="next-steps"></a>次のステップ

- [Azure 暗号化の概要](encryption-overview.md)
- [保存データの暗号化](encryption-atrest.md)
- [データ セキュリティと暗号化のベスト プラクティス](data-encryption-best-practices.md)
