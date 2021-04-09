---
title: Azure SQL Database で Intel SGX エンクレーブと構成証明を計画する
description: Azure SQL Database でのセキュリティで保護されたエンクレーブが設定された Always Encrypted のデプロイを計画します。
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
ms.openlocfilehash: 4448ce051b0c9e73865e8057cc4f224c9cbeb571
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98732746"
---
# <a name="plan-for-intel-sgx-enclaves-and-attestation-in-azure-sql-database"></a>Azure SQL Database で Intel SGX エンクレーブと構成証明を計画する

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL Database のセキュリティで保護されたエンクレーブが設定された Always Encrypted は、現在、**パブリック プレビュー** 段階にあります。

Azure SQL Database の[セキュリティで保護されたエンクレーブが設定された Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-enclaves) では、[Intel Software Guard Extensions (Intel SGX)](https://itpeernetwork.intel.com/microsoft-azure-confidential-computing/) のエンクレーブが使用され、[Microsoft Azure Attestation](/sql/relational-databases/security/encryption/always-encrypted-enclaves#secure-enclave-attestation) が必要です。

## <a name="plan-for-intel-sgx-in-azure-sql-database"></a>Azure SQL Database で Intel SGX を計画する

Intel SGX は、ハードウェアベースの高信頼実行環境テクノロジです。 Intel SGX は、[仮想コア モデル](service-tiers-vcore.md)と [DC シリーズ](service-tiers-vcore.md?#dc-series)のハードウェアの世代を使用するデータベースで使用できます。 そのため、データベース内で、セキュリティで保護されたエンクレーブが設定された Always Encrypted を使用できるようにするには、データベースの作成時に DC シリーズのハードウェアの世代を選択するか、DC シリーズのハードウェアの世代を使用するように既存のデータベースを更新する必要があります。

> [!NOTE]
> Intel SGX は、DC シリーズ以外のハードウェアの世代では使用できません。 たとえば、Intel SGX は Gen5 ハードウェアでは使用できず、[DTU モデル](service-tiers-dtu.md)を使用するデータベースでは使用できません。

> [!IMPORTANT]
> データベースに対して DC シリーズのハードウェアの世代を構成する前に、DC シリーズのリージョン別の提供状況を確認し、そのパフォーマンスの制限を理解するようにしてください。 詳細については、[DC シリーズ](service-tiers-vcore.md#dc-series)に関する記事を参照してください。

## <a name="plan-for-attestation-in-azure-sql-database"></a>Azure SQL データベースでの構成証明を計画する

[Microsoft Azure Attestation](../../attestation/overview.md) (プレビュー) は、DC シリーズのハードウェアの世代を使用した Azure SQL データベース内の Intel SGX エンクレーブを含む、高信頼実行環境 (TEE) を証明するためのソリューションです。

Azure SQL Database 内で Intel SGX エンクレーブを証明するために Azure Attestation を使用するには、次のことを行う必要があります。

1. [構成証明プロバイダー](../../attestation/basic-concepts.md#attestation-provider)を作成し、構成証明ポリシーを使用して構成します。 

2. Azure SQL 論理サーバーに、作成した構成証明プロバイダーへのアクセスを許可します。

## <a name="roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation"></a>SGX エンクレーブと構成証明を構成する場合のロールと責任

Azure SQL Database での Always Encrypted に対する Intel SGX エンクレーブと構成証明をサポートするように環境を構成するには、さまざまな種類のコンポーネントを設定する必要があります。これには、Microsoft Azure Attestation、Azure SQL Database、エンクレーブ構成証明をトリガーするアプリケーションがあります。 それぞれの種類のコンポーネントの構成は、以下の異なるロールのいずれかを引き受けるユーザーによって実行されます。

- 構成証明管理者 - Microsoft Azure Attestation 内で構成証明プロバイダーを作成し、構成証明ポリシーを作成し、Azure SQL 論理サーバーに構成証明プロバイダーへのアクセスを付与し、ポリシーを指す構成証明 URL をアプリケーション管理者に共有します。
- Azure SQL Database 管理者 - DC シリーズのハードウェアの世代を選択することでデータベースでの SGX エンクレーブを有効にし、構成証明プロバイダーにアクセスする必要がある Azure SQL 論理サーバーの ID を構成証明管理者に提供します。
- アプリケーション管理者 - 構成証明管理者から取得した構成証明 URL でアプリケーションを構成します。

(実際の機密データを扱う) 運用環境では、構成証明を構成するときに組織がロールの分離に従うことが重要です。その場合、さまざまなユーザーがそれぞれ異なるロールを引き受けます。 特に、Always Encrypted を組織にデプロイする目的が、Azure SQL Database 管理者が機密データにアクセスできないようにすることにより攻撃対象領域を減らすことである場合、Azure SQL Database 管理者は構成証明ポリシーを制御できません。

## <a name="next-steps"></a>次の手順

- [Azure SQL データベースに対して Intel SGX を有効にする](always-encrypted-enclaves-enable-sgx.md)

## <a name="see-also"></a>関連項目

- [チュートリアル: Azure SQL Database でのセキュリティで保護されたエンクレーブが設定された Always Encrypted の概要](always-encrypted-enclaves-getting-started.md)