---
title: Azure Key Vault と Azure Stack Edge リソースの統合とデバイスのアクティブ化
description: Azure Stack Edge Pro デバイスをアクティブ化するときに、Azure Key Vault がシークレットの管理にどのように関連するかを説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: c18f875dde43ab6df512375152d76a31c30a9e77
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102439869"
---
# <a name="azure-key-vault-integration-with-azure-stack-edge"></a>Azure Key Vault と Azure Stack Edge の統合 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Key Vault は、シークレット管理のために Azure Stack Edge リソースと統合されています。 この記事では、デバイスをアクティブ化するときに Azure Stack Edge リソース用に Azure キー コンテナーが作成されて、その後、シークレットの管理に使用される方法を詳しく説明します。 


## <a name="about-key-vault-and-azure-stack-edge"></a>Key Vault と Azure Stack Edge について

Azure Key Vault クラウド サービスは、トークン、パスワード、証明書、API キー、その他のシークレットを安全に格納し、それらへのアクセスを制御するために使用されます。 また、Key Vault により、データの暗号化に使用される暗号化キーの作成と制御が簡単になります。 許可されるトランザクションと対応する料金の詳細については、[Azure Key Vault の価格](https://azure.microsoft.com/pricing/details/key-vault/)に関するページを参照してください。

Azure Stack Edge サービスの場合、使用されるシークレットの 1 つはチャネル整合性キー (CIK) です。 このキーを使用すると、シークレットを暗号化できます。 Key Vault の統合により、CIK はキー コンテナーに安全に格納されます。 詳細については、「[シークレットとキーを安全に保存](../key-vault/general/overview.md#securely-store-secrets-and-keys)」を参照してください。


## <a name="key-vault-creation"></a>キー コンテナーの作成

アクティブ化キーの生成処理中に、Azure Stack Edge リソース用のキー コンテナーが作成されます。 

- Azure Stack Edge リソースを作成するときには、*Microsoft.KeyVault* リソース プロバイダーを登録する必要があります。 サブスクリプションへの所有者または共同作成者のアクセス権をお持ちの場合、リソース プロバイダーの登録は自動的に行われます。 キー コンテナーは、Azure Stack Edge リソースと同じサブスクリプションとリソース グループに作成されます。 

- Azure Stack Edge リソースを作成すると、マネージド サービス ID (MSI) も作成されます。これはリソースの有効期間にわたって保持され、これとクラウド上のリソース プロバイダーとの間で通信が行われます。 

    MSI が有効になると、Azure によって Azure Stack Edge リソース用の信頼できる ID が作成されます。

- Azure Stack Edge リソースを作成し、Azure portal からアクティブ化キーを生成したら、キー コンテナーが作成されます。 このキー コンテナーは、シークレットの管理に使用され、Azure Stack Edge リソースが存在する限り保持されます。 

    ![アクティブ化キーの生成時に作成されたキー コンテナー](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-3.png)

- 既定のキー名をそのまま使用することも、キー コンテナーのカスタム名を指定することもできます。 キー コンテナー名の長さは 3 - 24 文字にする必要があります。 既に使用されているキー コンテナーを使用することはできません。 <!--The MSI is then used to authenticate to key vault to retrieve secrets.--> 

    ![Azure Stack Edge リソースの作成時に作成された MSI](media/azure-stack-edge-gpu-deploy-prep/create-resource-8.png)

- Azure Key Vault を参照するには、Azure Stack Edge リソースで **[プロパティ]** に移動し、キー コンテナー名を選択します。 

- 誤って削除されないように、キー コンテナーではリソース ロックが有効になっています。 また、キー コンテナーでは論理的な削除が有効になっており、誤って削除された場合は 90 日以内にキー コンテナーを復元することができます。 詳細については、「[Azure Key Vault の論理的な削除の概要](../key-vault/general/soft-delete-overview.md)」を参照してください

    キー コンテナーが誤って削除され、消去保護期間の 90 日が経過していない場合は、次の手順に従って[キー コンテナーを回復](../key-vault/general/key-vault-recovery.md#list-recover-or-purge-soft-deleted-secrets-keys-and-certificates)します。 

- Azure キー コンテナーと Azure Stack Edge リソースが統合される前に、既存の Azure Stack Edge リソースがあった場合は、影響を受けません。 既存の Azure Stack Edge リソースを引き続き使用できます。 

- Azure Stack Edge リソースが削除されると、そのリソースと共に Azure キー コンテナーも削除されます。 確認を求められます。 このキー コンテナーを削除しない場合は、同意しないことを選択できます。 キー コンテナーはそのまま残り、Azure Stack Edge リソースのみが削除されます。 

- 他のキーを格納するためにこのキー コンテナーを使用していた場合も、削除から 90 日以内なら復元できます。 この消去保護期間中は、このキー コンテナー名を使用して新しいキー コンテナーを作成することはできません。

キー コンテナーとデバイスのアクティブ化に関連する問題が発生した場合は、[デバイスのアクティブ化の問題のトラブルシューティング](azure-stack-edge-gpu-troubleshoot-activation.md)に関する記事をご覧ください。

<!--## Key vault secret management

When you generate an activation key, the following events occur:

1. You request an activation key in the Azure portal. The request is then sent to Key Vault resource provider. 
1. A standard tier key vault with access policy is created and is locked by default. This key vault uses the default name or the custom name that you specified.
1. The key vault authenticates with MSI the request to generate activation key. The MSI is also added to the key vault access policy and a channel integrity key is generated and placed in the key vault.
1. The activation key is returned to the Azure portal. You can then copy this key and use it in the local UI to activate your device.-->



## <a name="next-steps"></a>次のステップ

- [アクティブ化キーの生成](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key)方法に関する詳細をご覧ください。