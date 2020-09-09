---
title: Language Understanding サービスでの保存データの暗号化
titleSuffix: Azure Cognitive Services
description: Language Understanding サービスでの保存データの暗号化。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 4fc816c3894120a5d1b356d91ebebbc56f21b530
ms.sourcegitcommit: ff19f4ecaff33a414c0fa2d4c92542d6e91332f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2020
ms.locfileid: "85052695"
---
# <a name="language-understanding-service-encryption-of-data-at-rest"></a>Language Understanding サービスでの保存データの暗号化

データは、クラウドに永続化されるときに、Language Understanding サービスによって自動的に暗号化されます。 Language Understanding サービスの暗号化によってデータが保護され、組織のセキュリティおよびコンプライアンス コミットメントを満たすのに役立ちます。

## <a name="about-cognitive-services-encryption"></a>Cognitive Services の暗号化について

データは、[FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) に準拠する [256 ビット AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 暗号化を使用して暗号化および暗号化解除されます。 暗号化と暗号化解除は透過的であり、暗号化とアクセスはユーザーによって管理されます。 データは既定でセキュリティ保護され、暗号化を利用するためにコードまたはアプリケーションを変更する必要はありません。

## <a name="about-encryption-key-management"></a>暗号化キーの管理について

サブスクリプションでは、Microsoft が管理する暗号化キーが既定で使用されます。 ユーザー独自のキーを使用してサブスクリプションを管理するためのオプションもあります。 カスタマー マネージド キー (CMK) を使用すると、アクセス制御の作成、ローテーション、無効化、取り消しを、いっそう柔軟に行うことができます。 また、データを保護するために使われる暗号化キーを監査することもできます。

## <a name="customer-managed-keys-with-azure-key-vault"></a>Azure Key Vault でのカスタマー マネージド キー

ユーザー独自のキーを使用してサブスクリプションを管理するためのオプションもあります。 カスタマー マネージド キー (CMK、Bring Your Own Key (BYOK) とも呼ばれます) を使用すると、アクセス制御の作成、ローテーション、無効化、取り消しを、いっそう柔軟に行うことができます。 また、データを保護するために使われる暗号化キーを監査することもできます。

カスタマー マネージド キーを格納するには、Azure Key Vault を使用する必要があります。 独自のキーを作成してキー コンテナーに格納することも、Azure Key Vault API を使ってキーを生成することもできます。 Cognitive Services リソースとキー コンテナーは、同じリージョンの同じ Azure Active Directory (Azure AD) テナント内に存在する必要がありますが、サブスクリプションは異なっていてもかまいません。 Azure Key Vault の詳細については、「[Azure Key Vault とは](https://docs.microsoft.com/azure/key-vault/key-vault-overview)」をご覧ください。

### <a name="customer-managed-keys-for-language-understanding"></a>Language Understanding 用のカスタマー マネージド キー

カスタマー マネージド キーを使用できるようにするには、 [LUIS サービス カスタマー マネージド キー要求フォーム](https://aka.ms/cogsvc-cmk)に記入して送信します。 要求の状態について連絡を差し上げるまで、約 3 から 5 営業日かかります。 要求によっては、お客様は待ち行列に登録され、スペースが利用できるようになってから承認される場合があります。 LUIS での CMK の使用が承認されたら、Azure portal から新しい Language Understanding リソースを作成し、価格レベルとして E0 を選択する必要があります。 新しい SKU は、CMK を除き、既に使用可能な F0 SKU と同じように機能します。 ユーザーは、F0 を新しい E0 SKU にアップグレードすることはできません。

![LUIS サブスクリプションの図](../media/cognitive-services-encryption/luis-subscription.png)

### <a name="regional-availability"></a>リージョン別の提供状況

カスタマー マネージド キーは、すべての [オーサリング リージョン](luis-reference-regions.md)で利用できます。 

### <a name="limitations"></a>制限事項

既存のアプリケーションまたは以前に作成したアプリケーションで E0 レベルを使用する場合、いくつかの制限があります。

* E0 リソースへの移行はブロックされます。 ユーザーは、アプリを F0 リソースに移行することだけができます。 既存のリソースを F0 に移行した後は、E0 レベルで新しいリソースを作成できます。 詳しくは、[こちらの移行](https://docs.microsoft.com/azure/cognitive-services/luis/luis-migration-authoring)に関する記事をご覧ください。  
* E0 リソースへの、または E0 リソースからのアプリケーションの移動はブロックされます。 この制限を回避するには、既存のアプリケーションをエクスポートし、E0 リソースとしてインポートします。
* Bing Spell Check 機能はサポートされていません。
* アプリケーションが E0 の場合、エンド ユーザーのトラフィックのログ記録は無効になります。
* Azure Bot Service からの音声プライミング機能は、E0 レベルのアプリケーションではサポートされていません。 この機能は Azure Bot Service で利用できますが、Azure Bot Service では CMK はサポートされていません。
* ポータルからの音声プライミング機能には、Azure Blob Storage が必要です。 詳しくは、[独自のストレージの持ち込み](../Speech-Service/speech-encryption-of-data-at-rest.md#bring-your-own-storage-byos-for-customization-and-logging)に関する記事をご覧ください。

### <a name="enable-customer-managed-keys"></a>カスタマー マネージド キーを有効にする

新しい Cognitive Services リソースは、常に Microsoft のマネージド キーを使用して暗号化されます。 リソースを作成するときに、カスタマー マネージド キーを有効にすることはできません。 カスタマー マネージド キーは Azure Key Vault に格納され、キー コンテナーは、Cognitive Services リソースに関連付けられているマネージド ID に対してキーのアクセス許可を付与するアクセス ポリシーを使用して、プロビジョニングする必要があります。 マネージド ID は、CMK の価格レベルを使用してリソースを作成した後でのみ使用できます。

Cognitive Services の暗号化のために Azure Key Vault でカスタマー マネージド キーを使用する方法については、以下を参照してください。

- [Azure portal から Cognitive Services の暗号化用に Key Vault でカスタマー マネージド キーを構成する](../Encryption/cognitive-services-encryption-keys-portal.md)

カスタマー マネージド キーを有効にすると、システムによって割り当てられるマネージド ID (Azure AD の機能) も有効になります。 システム割り当てのマネージド ID が有効になると、このリソースは Azure Active Directory に登録されます。 登録された後、マネージド ID には、カスタマー マネージド キーのセットアップ時に選択されたキー コンテナーへのアクセス権が付与されます。 詳しくは、[マネージド ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) に関する記事をご覧ください。

> [!IMPORTANT]
> システム割り当てのマネージド ID を無効にすると、キー コンテナーへのアクセスは削除され、カスタマー キーで暗号化されたデータにはアクセスできなくなります。 このデータに依存する機能はすべて動作しなくなります。

> [!IMPORTANT]
> マネージド ID は現在、クロスディレクトリ シナリオをサポートしていません。 Azure portal でカスタマー マネージド キーを構成すると、内部でマネージド ID が自動的に割り当てられます。 その後、サブスクリプション、リソース グループ、またはリソースを 1 つの Azure AD ディレクトリから別のディレクトリに移動した場合、そのリソースに関連付けられているマネージド ID は新しいテナントに転送されないため、カスタマー マネージド キーが機能しなくなることがあります。 詳細については、「[Azure リソース用マネージド ID に関する FAQ と既知の問題](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)」の中の「**Azure AD ディレクトリ間のサブスクリプションの転送**」を参照してください。  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Azure Key Vault にカスタマー マネージド キーを格納する

カスタマー マネージド キーを有効にするには、Azure キー コンテナーを使用してキーを格納する必要があります。 Key Vault で **[論理的な削除]** と **[Do Not Purge]\(消去しない\)** の両方のプロパティを有効にする必要があります。

Cognitive Services の暗号化では、サイズが 2048 の RSA キーのみがサポートされています。 キーの詳細については、「[Azure Key Vault のキー、シークレット、証明書について](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)」の「**Key Vault のキー**」を参照してください。

### <a name="rotate-customer-managed-keys"></a>カスタマー マネージド キーをローテーションする

Azure Key Vault のカスタマー マネージド キーは、お使いのコンプライアンス ポリシーに従ってローテーションすることができます。 キーをローテーションするときは、新しいキー URI を使用するように Cognitive Services リソースを更新する必要があります。 Azure portal で新しいバージョンのキーを使用するようにリソースを更新する方法については、[Azure portal を使用した Cognitive Services 用のカスタマー マネージド キーの構成](../Encryption/cognitive-services-encryption-keys-portal.md)に関する記事の「**キーのバージョンを更新する**」セクションをご覧ください。

キーをローテーションしても、リソースのデータの再暗号化はトリガーされません。 ユーザーがこれ以上操作を行う必要はありません。

### <a name="revoke-access-to-customer-managed-keys"></a>カスタマー マネージド キーへのアクセス権を取り消す

カスタマー マネージド キーへのアクセス権を取り消すには、PowerShell または Azure CLI を使用します。 詳細については、[Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) に関する記事、または [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault) に関する記事を参照してください。 アクセス権を取り消すと、Cognitive Services で暗号化キーにアクセスできなくなるため、Cognitive Services リソース内のすべてのデータへのアクセスが事実上ブロックされます。

## <a name="next-steps"></a>次のステップ

* [LUIS サービス カスタマー マネージド キー要求フォーム](https://aka.ms/cogsvc-cmk)
* [Azure Key Vault の詳細を確認する](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
