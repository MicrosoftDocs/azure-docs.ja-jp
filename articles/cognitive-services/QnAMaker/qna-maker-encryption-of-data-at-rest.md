---
title: QnA Maker での保存データの暗号化
titleSuffix: Azure Cognitive Services
description: Microsoft からは Microsoft が管理する暗号化キーが提供されます。また、カスタマー マネージド キー (CMK) と呼ばれている独自のキーで自分の Cognitive Services サブスクリプションを管理することをお客様に許可します。 この記事では、QnA Maker での保存データの暗号化と、CMK を有効化および管理する方法について説明します。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 07/10/2020
ms.author: egeaney
ms.openlocfilehash: 1ca0dda046329e95c649540fd42f96ca43838c85
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87086707"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>QnA Maker での保存データの暗号化

QnA Maker を使うと、クラウドに永続化されるデータが自動的に暗号化されるので、組織のセキュリティとコンプライアンスの目標を達成するのに役立ちます。

## <a name="about-encryption-key-management"></a>暗号化キーの管理について

サブスクリプションでは、Microsoft が管理する暗号化キーが既定で使用されます。 ユーザー独自のキーを使用してサブスクリプションを管理するオプションもあります。 カスタマー マネージド キー (CMK) を使用すると、アクセス制御の作成、ローテーション、無効化、取り消しを、いっそう柔軟に行うことができます。 また、データを保護するために使われる暗号化キーを監査することもできます。

QnA Maker では、Azure Search から CMK のサポートが使用されます。 [Azure Key Vault を使用して Azure Search 内に CMK](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys) を作成する必要があります。 この Azure インスタンスを QnA Maker サービスに関連付けて、CMK を有効にする必要があります。

> [!IMPORTANT]
> Azure Search サービス リソースは 2019 年 1 月以降に作成されている必要があり、無料 (共有) レベルでは使用できません。 現在 Azure portal では、カスタマー マネージド キーの構成はサポートされていません。

## <a name="enable-customer-managed-keys"></a>カスタマー マネージド キーを有効にする

QnA Maker サービスでは、Azure Search サービスから CMK が使用されます。 CMK を有効にするには、次の手順に従います。

1. 新しい Azure Search インスタンスを作成し、[Azure Cognitive Search でのカスタマー マネージド キーの前提条件](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys#prerequisites)に関する記事に記載されている前提条件に従います。

   ![暗号化の設定を表示する](../media/cognitive-services-encryption/qna-encryption-1.png)

2. QnA Maker リソースを作成すると、Azure Search インスタンスに自動的に関連付けられます。 これは CMK では使用できません。 CMK を使用するには、手順 1 で新しく作成した Azure Search インスタンスを関連付ける必要があります。 具体的には、QnA Maker リソースの `AzureSearchAdminKey` と `AzureSearchName` を更新する必要があります。

   ![暗号化の設定を表示する](../media/cognitive-services-encryption/qna-encryption-2.png)

3. 次に、新しいアプリケーション設定を作成します。
   * **Name**:これを `CustomerManagedEncryptionKeyUrl`
   * **値**: これは、Azure Search インスタンスを作成する手順 1 で取得した値です。

   ![暗号化の設定を表示する](../media/cognitive-services-encryption/qna-encryption-3.png)

4. 完了したら、ランタイムを再起動します。 これで、QnA Maker サービスで CMK が有効になりました。

## <a name="regional-availability"></a>リージョン別の提供状況

カスタマー マネージド キーは、すべての Azure Search リージョンで使用できます。

## <a name="encryption-of-data-in-transit"></a>転送中のデータの暗号化

QnA Maker ポータルは、ユーザーのブラウザー上で実行されます。 どのアクションでも、各コグニティブ サービス API への直接呼び出しがトリガーされます。 そのため、QnA Maker では転送中のデータに対応しています。
ただし、QnA Maker ポータル サービスは米国西部でホストされており、まだ米国以外のお客様にとっては最適な状況ではありません。 

## <a name="next-steps"></a>次のステップ

* [Azure Key Vault 内の CMK を使用した Azure Search での暗号化](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)
* [保存データの暗号化](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
* [Azure Key Vault の詳細を確認する](https://docs.microsoft.com/azure/key-vault/key-vault-overview)