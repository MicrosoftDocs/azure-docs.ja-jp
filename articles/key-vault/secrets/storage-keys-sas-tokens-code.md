---
title: コードでの Shared Access Signature トークンの取得 | Azure Key Vault
description: マネージド ストレージ アカウント機能によって、Azure Key Vault と Azure ストレージ アカウント間がシームレスに統合されます。 このサンプルでは、Azure SDK for .NET を使用して SAS トークンを管理します。
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 2a0202c5259ccebedf03ade217f57b6305b9fa1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94444930"
---
# <a name="create-sas-definition-and-fetch-shared-access-signature-tokens-in-code"></a>コードでの SAS 定義の作成と Shared Access Signature トークンの取得

キー コンテナー内に格納されている Shared Access Signature (SAS) トークンを使用して、ご利用のストレージ アカウントを管理できます。 詳細については、[SAS を使用した Azure Storage リソースへの制限付きアクセスの許可](../../storage/common/storage-sas-overview.md)に関するページを参照してください。

> [!NOTE]
> 共有キーによる承認のセキュリティと使いやすさを強化できるように、[Azure ロールベースのアクセス制御 (Azure RBAC)](../../storage/common/storage-auth-aad.md) を使用してストレージ アカウントをセキュリティで保護することをお勧めします。

この記事では、SAS 定義を作成し、SAS トークンをフェッチする .NET コードのサンプルを示します。 Key Vault マネージド ストレージ アカウント用に生成されたクライアントなど、詳細については、[ShareLink](/samples/azure/azure-sdk-for-net/share-link/) のサンプルを参照してください。 SAS トークンを作成して保存する方法の詳細については、「[Key Vault と Azure CLI を使用してストレージ アカウント キーを管理する](overview-storage-keys.md)」または「[Key Vault と Azure PowerShell を使用してストレージ アカウント キーを管理する](overview-storage-keys-powershell.md)」を参照してください。

## <a name="code-samples"></a>コード サンプル

次の例では、SAS テンプレートを作成します。

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="91-97":::

このテンプレートを使用して、SAS 定義を作成できます。 

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="137-156":::

SAS 定義を作成したら、`SecretClient` を使用してシークレットなどの SAS トークンを取得できます。 次のように、ストレージ アカウント名、ダッシュ、シークレット名の順に記述する必要があります。

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="52-58":::

Shared Access Signature トークンの有効期限が間もなく切れる場合は、同じシークレットを再度フェッチして、新しいものを生成することができます。

取得した Key Vault SAS トークンを使用して Azure Storage サービスにアクセスする方法のガイドについては、[アカウント SAS を使用した Blob service へのアクセス](../../storage/common/storage-account-sas-create-dotnet.md#use-an-account-sas-from-a-client)に関する記事をご覧ください

> [!NOTE]
> アプリでは、ストレージから 403 を取得した場合、SAS の更新に向けた準備が行われる必要があります。これにより、キーが侵害され、通常のローテーション期間よりも速くローテンションを行う必要があるケースを処理することができます。 

## <a name="next-steps"></a>次のステップ
- [SAS を使用して Azure Storage リソースへの制限付きアクセスを許可する](../../storage/common/storage-sas-overview.md)方法について学習します。
- [Azure PowerShell](overview-storage-keys-powershell.md) または [Azure CLI と Key Vault を使用してストレージ アカウント キーを管理する](overview-storage-keys.md)方法を確認する。
- [マネージド ストレージ アカウント キーのサンプル](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)を参照する