---
title: Azure Automation でのセキュリティで保護された資産の暗号化
description: この記事では、暗号化を使用するように Automation アカウントを構成することの概念について説明します。
services: automation
ms.service: automation
ms.subservice: process-automation
author: snehithm
ms.author: snmuvva
ms.date: 01/11/2020
ms.topic: conceptual
manager: kmadnani
ms.openlocfilehash: b46cf3a742158a3347b43a1e9bc6d62d0b2160d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104773693"
---
# <a name="encryption-of-secure-assets-in-azure-automation"></a>Azure Automation でのセキュリティで保護された資産の暗号化

Azure Automation でセキュリティ保護される資産としては、資格情報、証明書、接続、暗号化された変数などがあります。 これらの資産は、Azure Automation で複数のレベルの暗号化を使用して保護されます。 暗号化に使用される最上位のキーに基づいて、暗号化には次の 2 つのモデルがあります。

- Microsoft のマネージド キーの使用
- 管理するキーの使用

## <a name="microsoft-managed-keys"></a>Microsoft のマネージド キー

既定では、Azure Automation アカウントは Microsoft のマネージド キーを使用します。

セキュリティで保護された各資産が暗号化され、Automation アカウントごとに生成される一意キー (データ暗号化キー) を使用して Azure Automation に格納されます。 これらのキー自体も暗号化され、アカウント暗号化キー (AEK) と呼ばれる、アカウントごとに生成されるまた別の一意キーを使用して Azure Automation に格納されます。 これらのアカウント暗号化キーは暗号化され、Microsoft のマネージド キーを使用して Azure Automation に格納されます。 

## <a name="keys-that-you-manage-with-key-vault-preview"></a>Key Vault (プレビュー) を使用して管理するキー

独自のキーを使用して、Automation アカウントのセキュリティで保護された資産の暗号化を管理できます。 Automation アカウントのレベルでカスタマー マネージド キーを指定すると、そのキーは Automation アカウントのアカウント暗号化キーへのアクセスを保護および制御するために使用されます。 この機能は、セキュリティで保護されたすべての資産を暗号化および暗号化を解除するために使用されます。 カスタマー マネージド キーを使用すると、アクセス制御の作成、ローテーション、無効化、取り消しを、いっそう柔軟に行うことができます。 また、セキュリティで保護された資産を保護するために使用される暗号化キーを監査することもできます。

カスタマー マネージド キーを格納するには、Azure Key Vault を使用します。 独自のキーを作成してキー コンテナーに格納することも、Azure Key Vault API を使ってキーを生成することもできます。  Azure Key Vault の詳細については、「 [What is Azure Key Vault? (Azure Key Vault とは)](../key-vault/general/overview.md)

## <a name="use-of-customer-managed-keys-for-an-automation-account"></a>Automation アカウントのカスタマー マネージド キーの使用

Automation アカウントのカスタマー マネージド キーによる暗号化を使用すると、Azure Automation により、関連付けられているキー コンテナー内のカスタマー マネージド キーを使用してアカウント暗号化キーがラップされます。 カスタマー マネージド キーを有効にしてもパフォーマンスに影響はなく、アカウントは新しいキーですぐに暗号化され、遅延は発生しません。

新しい Automation アカウントは、常に Microsoft のマネージド キーを使用して暗号化されます。 アカウントの作成時にカスタマー マネージド キーを有効にすることはできません。 カスタマー マネージド キーは、Azure Key Vault に格納されます。このキー コンテナーは、Automation アカウントに関連付けられているマネージド ID にキーのアクセス許可を付与するアクセス ポリシーを使用してプロビジョニングする必要があります。 マネージド ID は、ストレージ アカウントが作成された後でのみ使用できます。

Azure Automation でのセキュリティで保護された資産の暗号化に使用されているキーを、カスタマー マネージド キーを有効または無効にするか、キーのバージョンを更新するか、または別のキーを指定することによって変更した場合、アカウント暗号化キーの暗号化は変更されますが、Azure Automation アカウントのセキュリティで保護された資産を再暗号化する必要はありません。

> [!NOTE] 
> カスタマー マネージド キーを有効にするには、API バージョン 2020-01-13-preview を使用して Azure Automation REST API 呼び出しを行う必要があります

### <a name="prerequisites-for-using-customer-managed-keys-in-azure-automation"></a>Azure Automation 内でカスタマー マネージド キーを使用するための前提条件

Automation アカウントのカスタマー マネージド キーを有効にする前に、次の前提条件が満たされていることを確認する必要があります。

 - カスタマー マネージド キーが Azure Key Vault に格納されていること。 
 - Key Vault で **[論理的な削除]** と **[Do Not Purge]\(消去しない\)** の両方のプロパティを有効にします。 これらの機能は、誤削除が発生した場合にキーの復旧を許可するために必要です。
 - Azure Automation の暗号化では RSA キーのみがサポートされています。 キーの詳細については、[Azure Key Vault のキー、シークレット、および証明書の概要](../key-vault/general/about-keys-secrets-certificates.md)に関するページを参照してください。
- Automation アカウントとキー コンテナーは異なるサブスクリプションにあってもかまいませんが、同じ Azure Active Directory テナントに存在する必要があります。

### <a name="assignment-of-an-identity-to-the-automation-account"></a>Automation アカウントへの ID の割り当て

Automation アカウントでカスタマー マネージド キーを使用するには、その Automation アカウントが、カスタマー マネージド キーを格納しているキー コンテナーに対して認証する必要があります。 Azure Automation では、 Azure Key Vault でアカウントを認証するためにシステム割り当てマネージド ID が使用されます。 マネージド ID の詳細については、「[Azure リソースのマネージド ID とは](../active-directory/managed-identities-azure-resources/overview.md)」を参照してください。

Automation アカウントのシステム割り当てマネージド ID は、次の REST API 呼び出しを使用して構成します。

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

要求本文:

```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```

Automation アカウントのシステム割り当て ID が次のような応答で返されます。

```json
{
 "name": "automation-account-name",
 "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
 ..
 "identity": {
    "type": "SystemAssigned",
    "principalId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
    "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
 },
..
}
```

### <a name="configuration-of-the-key-vault-access-policy"></a>キー コンテナーのアクセス ポリシーの構成

Automation アカウントにマネージド ID が割り当てられたら、カスタマー マネージド キーを格納しているキー コンテナーへのアクセスを構成します。 Azure Automation では、カスタマー マネージド キーに **get**、**recover**、**wrapKey**、**UnwrapKey** が必要です。

このようなアクセス ポリシーは、次の REST API 呼び出しを使用して設定できます。

```http
PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sample-group/providers/Microsoft.KeyVault/vaults/sample-vault/accessPolicies/add?api-version=2018-02-14
```

要求本文:

```json
{
  "properties": {
    "accessPolicies": [
      {
        "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
        "objectId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
        "permissions": {
          "keys": [
            "get",
            "recover",
            "wrapKey",
            "unwrapKey"
          ],
          "secrets": [],
          "certificates": []
        }
      }
    ]
  }
}
```

> [!NOTE]
> **tenantId** および **objectId** フィールドには、Automation アカウントのマネージド ID の応答から、それぞれ **identity.tenantId** と **identity.principalId** の値を指定する必要があります。

### <a name="change-the-configuration-of-automation-account-to-use-customer-managed-key"></a>カスタマー マネージド キーを使用するように Automation アカウントの構成を変更する

最後に、次の REST API 呼び出しを使用して、Automation アカウントを Microsoft のマネージド キーからカスタマー マネージド キーに切り替えることができます。

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

要求本文:

```json
{
    "identity": {
    "type": "SystemAssigned"
    },
    "properties": {
        "encryption": {
            "keySource": "Microsoft.Keyvault",
            "keyvaultProperties": {
                "keyName": "sample-vault-key",
                "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
                "keyVersion": "7c73556c521340209371eaf623cc099d"
            }
        }
    }
}
```

応答のサンプル

```json
{
  "name": "automation-account-name",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
  ..
  "properties": {
    ..
    "encryption": {
      "keyvaultProperties": {
         "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
      },
      "keySource": "Microsoft.Keyvault"
    },
    ..
  }
}
```

## <a name="rotation-of-a-customer-managed-key"></a>カスタマー マネージド キーのローテーション

Azure Key Vault のカスタマー マネージド キーは、お使いのコンプライアンス ポリシーに従ってローテーションすることができます。 キーがローテーションされたら、新しいキー URI を使用するように Automation アカウントを更新する必要があります。

キーをローテーションしても、Automation アカウントのセキュリティで保護された資産の再暗号化はトリガーされません。 これ以上必要な操作はありません。

## <a name="revocation-of-access-to-a-customer-managed-key"></a>カスタマー マネージド キーの失効

カスタマー マネージド キーへのアクセス権を取り消すには、PowerShell または Azure CLI を使用します。 詳細については、[Azure Key Vault PowerShell](/powershell/module/az.keyvault/) に関する記事、または [Azure Key Vault CLI](/cli/azure/keyvault) に関する記事を参照してください。 アクセス権を取り消すと、Azure Automation が暗号化キーにアクセスできなくなるため、Automation アカウントのセキュリティで保護されたすべての資産へのアクセスが実質的にブロックされます。

## <a name="next-steps"></a>次のステップ

- Azure Key Vault を理解するには、「[Azure Key Vault とは](../key-vault/general/overview.md)」を参照してください。
- 証明書を操作するには、「[Azure Automation で証明書を管理する](shared-resources/certificates.md)」を参照してください。
- 資格情報を処理するには、「[Azure Automation で資格情報を管理する](shared-resources/credentials.md)」を参照してください。
- Automation 変数を使用するには、「[Azure Automation で変数を管理する](shared-resources/variables.md)」を参照してください。
- 接続を使用する場合のヘルプについては、「[Azure Automation の接続を管理する](automation-connections.md)」を参照してください。
