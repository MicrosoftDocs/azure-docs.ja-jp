---
title: PowerShell:Bring Your Own Key (BYOK) TDE を有効にする
titleSuffix: Azure SQL Managed Instance
description: PowerShell を使用して、保存時の暗号化に Bring Your Own Key (BYOK) Transparent Data Encryption (TDE) を使用するように Azure SQL Managed Instance を構成する方法について説明します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, sstein
ms.date: 08/25/2020
ms.openlocfilehash: 34a849fde315b45bdb1df577cf26c91f458abd72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91323234"
---
# <a name="transparent-data-encryption-in-sql-managed-instance-using-your-own-key-from-azure-key-vault"></a>Azure Key Vault の独自のキーを使用した SQL Managed Instance での Transparent Data Encryption

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

この PowerShell スクリプトの例では、Azure Key Vault のキーを使用し、Azure SQL Managed Instance のユーザー管理キーで Transparent Data Encryption (TDE) が構成されます。 よく "TDE の Bring Your Own Key (BYOK)" と呼ばれるシナリオです。 詳細については、「[カスタマー マネージド キーを使用した Azure SQL Transparent Data Encryption](../../database/transparent-data-encryption-byok-overview.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

- 既存のマネージド インスタンス。 「[PowerShell を使用してマネージド インスタンスを作成する](create-configure-managed-instance-powershell.md)」をご覧ください。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

PowerShell をローカルで使用する場合も、Azure Cloud Shell を使用する場合も、Azure PowerShell 2.3.2 以降のバージョンが必要です。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照するか、または次のサンプル スクリプトを使用して、現在のユーザーを対象にモジュールをインストールします。

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="sample-scripts"></a>サンプルのスクリプト 

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](/powershell/azure/)をご覧ください。

SQL Managed Instance 用の PowerShell サンプル スクリプトは、他にも [Azure SQL Managed Instance 用の PowerShell スクリプト](../../database/powershell-script-content-guide.md)のページでご覧いただけます。
