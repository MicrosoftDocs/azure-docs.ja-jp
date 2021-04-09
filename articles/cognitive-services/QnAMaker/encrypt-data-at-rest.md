---
title: QnA Maker での保存データの暗号化
titleSuffix: Azure Cognitive Services
description: Microsoft からは Microsoft が管理する暗号化キーが提供されます。また、カスタマー マネージド キー (CMK) と呼ばれている独自のキーで自分の Cognitive Services サブスクリプションを管理することをお客様に許可します。 この記事では、QnA Maker での保存データの暗号化と、CMK を有効化および管理する方法について説明します。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.author: egeaney
ms.openlocfilehash: 19dc0f3a676d5373b28e4b7055050477c426f847
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100524077"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>QnA Maker での保存データの暗号化

QnA Maker を使うと、クラウドに永続化されるデータが自動的に暗号化されるので、組織のセキュリティとコンプライアンスの目標を達成するのに役立ちます。

## <a name="about-encryption-key-management"></a>暗号化キーの管理について

サブスクリプションでは、Microsoft が管理する暗号化キーが既定で使用されます。 カスタマー マネージド キー (CMK) と呼ばれているユーザー独自のキーを使用してサブスクリプションを管理するオプションもあります。 CMK を使用すると、アクセス制御の作成、ローテーション、無効化、取り消しを、いっそう柔軟に行うことができます。 また、データを保護するために使われる暗号化キーを監査することもできます。 CMK がサブスクリプション用に構成されている場合は、Azure Key Vault で暗号化キーを制御しながら、2 つ目の保護レイヤーを提供する二重暗号化を利用できます。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/v1)

QnA Maker では、Azure Search から CMK のサポートが使用されます。 [Azure Key Vault を使用して Azure Search 内で CMK](../../search/search-security-manage-encryption-keys.md) を構成します。 この Azure インスタンスを QnA Maker サービスに関連付けて、CMK を有効にする必要があります。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/v2)

QnA Maker によって [Azure Search からの CMK](../../search/search-security-manage-encryption-keys.md) が使用され、Azure 検索インデックスに格納されているデータを暗号化するため、与えられた CMK を自動的に関連付けます。

---

> [!IMPORTANT]
> Azure Search サービス リソースは 2019 年 1 月以降に作成されている必要があり、無料 (共有) レベルでは使用できません。 現在 Azure portal では、カスタマー マネージド キーの構成はサポートされていません。

## <a name="enable-customer-managed-keys"></a>カスタマー マネージド キーを有効にする

QnA Maker サービスでは、Azure Search サービスから CMK が使用されます。 CMK を有効にするには、次の手順に従います。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/v1)

1. 新しい Azure Search インスタンスを作成し、[Azure Cognitive Search でのカスタマー マネージド キーの前提条件](../../search/search-security-manage-encryption-keys.md#prerequisites)に関する記事に記載されている前提条件に従います。

   ![暗号化の設定を表示する 1](../media/cognitive-services-encryption/qna-encryption-1.png)

2. QnA Maker リソースを作成すると、Azure Search インスタンスに自動的に関連付けられます。 このインスタンスでは CMK を使用できません。 CMK を使用するには、手順 1 で新しく作成した Azure Search インスタンスを関連付ける必要があります。 具体的には、QnA Maker リソースの `AzureSearchAdminKey` と `AzureSearchName` を更新する必要があります。

   ![暗号化の設定を表示する 2](../media/cognitive-services-encryption/qna-encryption-2.png)

3. 次に、新しいアプリケーション設定を作成します。
   * **名前**:`CustomerManagedEncryptionKeyUrl`
   * **値**: Azure Search インスタンスを作成する手順 1 で取得した値を使用します。

   ![暗号化の設定を表示する 3](../media/cognitive-services-encryption/qna-encryption-3.png)

4. 完了したら、ランタイムを再起動します。 これで、QnA Maker サービスで CMK が有効になりました。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/v2)

1.  QnA Maker マネージド (プレビュー) サービスの **[暗号化]** タブに移動します。
2.  **[カスタマー マネージド キー]** オプションを選択します。 [[カスタマー マネージド キー]](../../storage/common/customer-managed-keys-configure-key-vault.md?tabs=portal) の詳細を入力し、 **[保存]** をクリックします。

     :::image type="content" source="../media/cognitive-services-encryption/qnamaker-v2-encryption-cmk.png" alt-text="QnA Maker マネージド (プレビュー) CMK 設定" lightbox="../media/cognitive-services-encryption/qnamaker-v2-encryption-cmk.png":::

3.  正常に保存されると、Azure Search インデックスに格納されているデータの暗号化に CMK が使用されます。

> [!IMPORTANT]
> ナレッジ ベースを作成する前に、新しい Azure Cognitive Search サービスで CMK を設定することをお勧めします。 既存のナレッジ ベースを使用して QnA Maker サービスで CMK を設定した場合、ナレッジ ベースにアクセスできなくなるおそれがあります。 暗号化されたコンテンツを Azure Cognitive Search で使用する方法の詳細については、[こちら](../../search/search-security-manage-encryption-keys.md#work-with-encrypted-content)をご覧ください。

> [!NOTE]
> カスタマー マネージド キーを使用できるようにするには、[Cognitive Services カスタマー マネージド キー要求フォーム](https://aka.ms/cogsvc-cmk)に記入して送信します。

---

## <a name="regional-availability"></a>リージョン別の提供状況

カスタマー マネージド キーは、すべての Azure Search リージョンで使用できます。

## <a name="encryption-of-data-in-transit"></a>転送中のデータの暗号化

QnA Maker ポータルは、ユーザーのブラウザー上で実行されます。 どのアクションでも、各コグニティブ サービス API への直接呼び出しがトリガーされます。 そのため、QnA Maker では転送中のデータに対応しています。
ただし、QnA Maker ポータル サービスは米国西部でホストされており、まだ米国以外のお客様にとっては最適な状況ではありません。 

## <a name="next-steps"></a>次のステップ

* [Azure Key Vault 内の CMK を使用した Azure Search での暗号化](../../search/search-security-manage-encryption-keys.md)
* [保存データの暗号化](../../security/fundamentals/encryption-atrest.md)
* [Azure Key Vault の詳細を確認する](../../key-vault/general/overview.md)