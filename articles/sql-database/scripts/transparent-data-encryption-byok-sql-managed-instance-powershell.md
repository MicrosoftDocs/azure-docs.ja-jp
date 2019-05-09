---
title: PowerShell:BYOK TDE の有効化 - Azure SQL Database Managed Instance | Microsoft Docs
description: PowerShell を使用して、保存時の暗号化に BYOK Transparent Data Encryption (TDE) を使用するように Azure SQL Managed Instance を構成する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 04/09/2019
ms.openlocfilehash: c08b5559fd599fb297f294a54aed67c65676aee4
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/11/2019
ms.locfileid: "59496235"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Azure Key Vault の独自のキーを使用して Managed Instance で Transparent Data Encryption を管理する

この PowerShell スクリプトの例では、Azure Key Vault のキーを使用し、Azure SQL Managed Instance の Bring Your Own Key シナリオで Transparent Data Encryption (TDE) が構成されます。 Bring Your Own Key (BYOK) をサポートする TDE の詳細については、[Azure SQL の TDE Bring Your Own Key](../transparent-data-encryption-byok-azure-sql.md) に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件

- 既存のマネージド インスタンス。 「[PowerShell を使用して Azure SQL Database マネージド インスタンスを作成する](sql-database-create-configure-managed-instance-powershell.md)」を参照してください。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

PowerShell をローカルで使用する場合も、Azure Cloud Shell を使用する場合も、AZ PowerShell 1.1.1 プレビューまたはそれ以降のバージョンのプレビューが必要です。 アップグレードする必要がある場合は、「[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)」を参照するか、または次のサンプル スクリプトを使用して、モジュールをインストールします。

`Install-Module -Name Az.Sql -RequiredVersion 1.1.1-preview -AllowPrerelease -Force`

PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="sample-scripts"></a>サンプルのスクリプト

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>次の手順

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の SQL Database 用の PowerShell サンプル スクリプトは、[Azure SQL Database 用の PowerShell スクリプト](../sql-database-powershell-samples.md)のページにあります。
