---
title: Azure SQL Database に対して Intel SGX を有効にする
description: SGX 対応のハードウェアの世代を選択することによって、Azure SQL Database でのセキュリティで保護されたエンクレーブが設定された Always Encrypted に対して Intel SGX を有効にする方法について説明します。
keywords: データの暗号化, sql 暗号化, データベース暗号化, 機密データ, Always Encrypted, セキュリティで保護されたエンクレーブ, SGX, 構成証明
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: ded1406c47bb3f00c366da7a5b28319f3712f8a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98733758"
---
# <a name="enable-intel-sgx-for-your-azure-sql-database"></a>Azure SQL Database に対して Intel SGX を有効にする 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL Database のセキュリティで保護されたエンクレーブが設定された Always Encrypted は、現在、**パブリック プレビュー** 段階にあります。

Azure SQL Database の[セキュリティで保護されたエンクレーブが設定された Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-enclaves) では、[Intel Software Guard Extensions (Intel SGX)](https://itpeernetwork.intel.com/microsoft-azure-confidential-computing/) のエンクレーブが使用されます。 Intel SGX を使用できるようにするには、データベースで[仮想コア モデル](service-tiers-vcore.md)と [DC シリーズ](service-tiers-vcore.md#dc-series)のハードウェアの世代を使用する必要があります。

Intel SGX エンクレーブを有効にするように DC シリーズのハードウェアの世代を構成することは、Azure SQL Database 管理者の責任です。 「[SGX エンクレーブと構成証明を構成する場合のロールと責任](always-encrypted-enclaves-plan.md#roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation)」を参照してください。

> [!NOTE]
> Intel SGX は、DC シリーズ以外のハードウェアの世代では使用できません。 たとえば、Intel SGX は Gen5 ハードウェアでは使用できず、[DTU モデル](service-tiers-dtu.md)を使用するデータベースでは使用できません。

> [!IMPORTANT]
> データベースに対して DC シリーズのハードウェアの世代を構成する前に、DC シリーズのリージョン別の提供状況を確認し、そのパフォーマンスの制限を理解するようにしてください。 詳細については、[DC シリーズ](service-tiers-vcore.md#dc-series)に関するセクションをご覧ください。

特定のハードウェアの世代を使用するように新規または既存のデータベースを構成する方法の詳細については、「[ハードウェアの世代を選択する](service-tiers-vcore.md#selecting-a-hardware-generation)」を参照してください。
   
## <a name="next-steps"></a>次の手順

- [Azure SQL データベース サーバー用に Azure Attestation を構成する](always-encrypted-enclaves-configure-attestation.md)

## <a name="see-also"></a>関連項目

- [チュートリアル: Azure SQL Database でのセキュリティで保護されたエンクレーブが設定された Always Encrypted の概要](always-encrypted-enclaves-getting-started.md)