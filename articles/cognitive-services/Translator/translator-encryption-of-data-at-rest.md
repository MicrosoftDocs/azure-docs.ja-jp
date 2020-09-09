---
title: Translator による保存データの暗号化
titleSuffix: Azure Cognitive Services
description: Translator による保存データの暗号化。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: egeaney
ms.openlocfilehash: bc328efd648eb3dd522f5233e2a5c440911ac58c
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310837"
---
# <a name="translator-encryption-of-data-at-rest"></a>Translator による保存データの暗号化

データは Translator によって自動的に暗号化されます。それをアップロードしてカスタム翻訳モデルを作成すると、クラウドに永続化されるので、組織のセキュリティとコンプライアンスの目標を達成するのに役立ちます。

## <a name="about-cognitive-services-encryption"></a>Cognitive Services の暗号化について

データは、[FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) に準拠する [256 ビット AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 暗号化を使用して暗号化および暗号化解除されます。 暗号化と暗号化解除は透過的であり、暗号化とアクセスはユーザーによって管理されます。 データは既定でセキュリティ保護され、暗号化を利用するためにコードまたはアプリケーションを変更する必要はありません。

## <a name="about-encryption-key-management"></a>暗号化キーの管理について

サブスクリプションでは、Microsoft のマネージド暗号化キーが既定で使用されます。 カスタマー マネージド キーをサポートする価格レベルを使用している場合は、次の図に示すように、[Azure portal](https://portal.azure.com) の **[暗号化]** セクションでリソースの暗号化設定を確認できます。

![暗号化の設定を表示する](../media/cognitive-services-encryption/encryptionblade.png)

Microsoft マネージド暗号化キーのみをサポートするサブスクリプションの場合、 **[暗号化]** セクションはありません。

## <a name="customer-managed-keys-with-azure-key-vault"></a>Azure Key Vault でのカスタマー マネージド キー

ユーザー独自のキーを使用してサブスクリプションを管理するためのオプションもあります。 カスタマー マネージド キー (CMK、Bring Your Own Key (BYOK) とも呼ばれます) を使用すると、アクセス制御の作成、ローテーション、無効化、取り消しを、いっそう柔軟に行うことができます。 また、データを保護するために使われる暗号化キーを監査することもできます。

> [!IMPORTANT]
> カスタマー マネージド キーは、Translator サービスのすべての価格レベルで利用できます。 カスタマー マネージド キーを使用できるように要求するには、[Translator カスタマー マネージド キー要求フォーム](https://aka.ms/cogsvc-cmk)に記入して送信します。要求の状態について連絡を差し上げるまで、約 3 から 5 営業日かかります。 要求によっては、お客様は待ち行列に登録され、スペースが利用できるようになってから承認される場合があります。 Translator サービスでの CMK の使用が承認されたら、新しい Translator リソースを作成する必要があります。 Translator リソースが作成されたら、Azure Key Vault を使用してマネージド ID を設定できます。

Translator 用のカスタマー マネージド キーを有効にするには、次の手順のようにします。

1. Translator または Cognitive Services の新しいリージョン リソースを作成します。 これはグローバル リソースでは機能しません。
2. Azure portal でマネージド ID を有効にし、カスタマー マネージド キーの情報を追加します。
3. カスタム翻訳ツールで新しいワークスペースを作成し、このサブスクリプション情報を関連付けます。

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

### <a name="enable-customer-managed-keys"></a>カスタマー マネージド キーを有効にする

カスタマー マネージド キーを格納するには、Azure Key Vault を使用する必要があります。 独自のキーを作成してキー コンテナーに格納することも、Azure Key Vault API を使ってキーを生成することもできます。 Cognitive Services リソースとキー コンテナーは、同じリージョンの同じ Azure Active Directory (Azure AD) テナント内に存在する必要がありますが、サブスクリプションは異なっていてもかまいません。 Azure Key Vault の詳細については、「[Azure Key Vault とは](https://docs.microsoft.com/azure/key-vault/key-vault-overview)」をご覧ください。

新しい Cognitive Services リソースは、常に Microsoft のマネージド キーを使用して暗号化されます。 リソースを作成するときに、カスタマー マネージド キーを有効にすることはできません。 カスタマー マネージド キーは Azure Key Vault に格納され、キー コンテナーは、Cognitive Services リソースに関連付けられているマネージド ID に対してキーのアクセス許可を付与するアクセス ポリシーを使用して、プロビジョニングする必要があります。 マネージド ID は、リソースが作成されるとすぐに使用できるようになります。

Cognitive Services の暗号化のために Azure Key Vault でカスタマー マネージド キーを使用する方法については、以下を参照してください。

- [Azure portal から Cognitive Services の暗号化用に Key Vault でカスタマー マネージド キーを構成する](../Encryption/cognitive-services-encryption-keys-portal.md)

カスタマー マネージド キーを有効にすると、システムによって割り当てられるマネージド ID (Azure AD の機能) も有効になります。 システム割り当てのマネージド ID が有効になると、このリソースは Azure Active Directory に登録されます。 登録された後、マネージド ID には、カスタマー マネージド キーのセットアップ時に選択されたキー コンテナーへのアクセス権が付与されます。 詳しくは、[マネージド ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) に関する記事をご覧ください。

> [!IMPORTANT]
> システム割り当てのマネージド ID を無効にすると、キー コンテナーへのアクセスは削除され、カスタマー キーで暗号化されたデータにはアクセスできなくなります。 このデータに依存する機能はすべて動作しなくなります。 デプロイしたモデルもすべてデプロイ解除されます。 アップロードしたすべてのデータが、カスタム翻訳ツールから削除されます。 マネージド ID を再び有効にしても、モデルが自動的に再デプロイされることはありません。

> [!IMPORTANT]
> マネージド ID は現在、クロスディレクトリ シナリオをサポートしていません。 Azure portal でカスタマー マネージド キーを構成すると、内部でマネージド ID が自動的に割り当てられます。 その後、サブスクリプション、リソース グループ、またはリソースを 1 つの Azure AD ディレクトリから別のディレクトリに移動した場合、そのリソースに関連付けられているマネージド ID は新しいテナントに転送されないため、カスタマー マネージド キーが機能しなくなることがあります。 詳細については、「[Azure リソース用マネージド ID に関する FAQ と既知の問題](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)」の中の「**Azure AD ディレクトリ間のサブスクリプションの転送**」を参照してください。  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Azure Key Vault にカスタマー マネージド キーを格納する

カスタマー マネージド キーを有効にするには、Azure キー コンテナーを使用してキーを格納する必要があります。 Key Vault で **[論理的な削除]** と **[Do Not Purge]\(消去しない\)** の両方のプロパティを有効にする必要があります。

Cognitive Services の暗号化では、サイズが 2048 の RSA キーのみがサポートされています。 キーの詳細については、「[Azure Key Vault のキー、シークレット、証明書について](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)」の「**Key Vault のキー**」を参照してください。

> [!NOTE]
> キー コンテナー全体を削除すると、データは表示されなくなり、すべてのモデルがデプロイ解除されます。 アップロードしたすべてのデータが、カスタム翻訳ツールから削除されます。 

### <a name="revoke-access-to-customer-managed-keys"></a>カスタマー マネージド キーへのアクセス権を取り消す

カスタマー マネージド キーへのアクセス権を取り消すには、PowerShell または Azure CLI を使用します。 詳細については、[Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) に関する記事、または [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault) に関する記事を参照してください。 アクセス権を取り消すと、Cognitive Services で暗号化キーにアクセスできなくなるため、Cognitive Services リソース内のすべてのデータへのアクセスが事実上ブロックされ、モデルがデプロイ解除されます。 アップロードしたすべてのデータも、カスタム翻訳ツールから削除されます。


## <a name="next-steps"></a>次のステップ

* [Azure Key Vault の詳細を確認する](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
