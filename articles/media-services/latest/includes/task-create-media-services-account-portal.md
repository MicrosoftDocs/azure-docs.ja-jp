---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 11/04/2020
ms.author: inhenkel
ms.custom: portal
ms.openlocfilehash: 72857564a6b195353e7bf2a27c8369004d52d3f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95971338"
---
<!-- Use the portal to create a media services account. -->

## <a name="create-a-media-services-account"></a>Media Services アカウントを作成する

1. [Azure Portal](https://portal.azure.com/) にサインインします。
1. **[+リソースの作成]**  >  **[メディア]**  >  **[Media Services]** の順にクリックします。
1. **[Media Services アカウントを作成する]** セクションに必要な値を入力します。

    | 名前 | 説明 |
    | ---|---|
    |**アカウント名**|新しい Media Services アカウントの名前を入力します。 Media Services アカウント名に使用できる文字は、小文字または数字のみで、空白を含めることはできません。長さは 3 文字から 24 文字です。|
    |**サブスクリプション**|複数のサブスクリプションがある場合は、アクセス権のある Azure サブスクリプションの一覧から 1 つを選択します。|
    |**リソース グループ**|新規または既存のリソースを選択します。 リソース グループとは、ライフサイクル、アクセス許可、ポリシーを共有するリソースの集まりです。 [こちら](../../../azure-resource-manager/management/overview.md#resource-groups)をご覧ください。|
    |**場所**|Media Services アカウントのメディアとメタデータのレコードを保存する地理的リージョンを選択します。 このリージョンでメディアの処理とストリーミングが行われます。 ドロップダウン リストのボックスには、利用可能な Media Services リージョンのみが表示されます。 |
    |**ストレージ アカウント**|Media Services アカウントのメディア コンテンツの BLOB ストレージとなるストレージ アカウントを選択します。 Media Services アカウントと同じリージョンにある既存のストレージ アカウントを選択することも、新しいストレージ アカウントを作成することもできます。 新しいストレージ アカウントは同じリージョンに作成されます。 ストレージ アカウントの命名規則は、Media Services アカウントと同じです。<br/><br/>1 つの **プライマリ** ストレージ アカウントを持つ必要があります。Media Services アカウントに関連付けられた任意の数の **セカンダリ** ストレージ アカウントを持つことができます。 Azure portal を使用して、セカンダリ ストレージ アカウントを追加できます。 詳細については、[Azure Storage アカウントの Azure Media Services アカウント](../storage-account-concept.md)に関するページを参照してください。<br/><br/>Media Services アカウントおよび関連するすべてのストレージ アカウントは、同じ Azure サブスクリプションに存在する必要があります。 待ち時間やデータ エグレス コストが増加することを回避するために、ストレージ アカウントを Media Services アカウントと同じ場所で使用することを強くお勧めします。|
    |**詳細設定**| **[システム管理]** をクリックすると、システム マネージド ID を使用してアカウントを作成できます。  この選択によって、カスタマー マネージド キー (Bring Your Own Key、BYOK) と Media Services を使用して、信頼されたストレージを有効にすることができます。  カスタマー マネージド キーの詳細については、「[Media Services での Bring Your Own Key (カスタマー マネージド キー)](../concept-use-customer-managed-keys-byok.md)」を参照してください。 また、[マネージド ID](../concept-managed-identities.md) も有効になります。

1. **[ダッシュボードにピン留めする]** チェック ボックスをオンにして、アカウントのデプロイの進行状況を確認します。
1. フォームの下部にある **[作成]** をクリックします。
