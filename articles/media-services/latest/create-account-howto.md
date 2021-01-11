---
title: Azure Media Services アカウントの作成
description: このチュートリアルでは、Azure Media Services アカウントを作成する手順について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 93f5e4d659b94bd79345a5e687de14ab6a5e8ba6
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267974"
---
# <a name="create-a-media-services-account"></a>Media Services アカウントを作成する

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure で暗号化、エンコード、分析、管理、およびメディア コンテンツのストリーミングを開始するには、Media Services アカウントを作成する必要があります。 Media Services アカウントは、1 つまたは複数のストレージ アカウントに関連付ける必要があります。 この記事では、新しい Azure Media Services アカウントを作成する手順について説明します。

> [!NOTE]
> Media Services アカウントおよび関連するすべてのストレージ アカウントは、同じ Azure サブスクリプションに存在する必要があります。 待ち時間やデータ エグレス コストが増加することを回避するために、ストレージ アカウントを Media Services アカウントと同じ場所で使用することを強くお勧めします。

 Azure portal または CLI のいずれかを使用して、Media Services アカウントを作成できます。 使用するメソッドのタブを選択します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="portal"></a>[ポータル](#tab/portal/)

## <a name="use-the-azure-portal"></a>Azure ポータルの使用

<!-- Move this section. This section should be moved to conceptual.  It doesn't belong in task based -->
Azure Portal には、Azure Media Services アカウントをすばやく作成する方法が用意されています。 アカウントを使用して Media Services にアクセスすると、Azure でメディア コンテンツを保存、暗号化、エンコード、管理、およびストリーミングすることができます。

現時点では、[Azure portal](https://portal.azure.com/) を使って次のことを実行できます。

* Media Services v3 の[ライブ イベント](live-events-outputs-concept.md)を管理する 
* v3 の[アセット](assets-concept.md)を表示する (管理ではない) 
* [API のアクセスに関する情報を取得する](./access-api-howto.md)。 

他のすべての管理タスク ([変換とジョブ](transforms-jobs-concept.md)や[コンテンツ保護](content-protection-overview.md)など) については、[REST API](https://aka.ms/ams-v3-rest-ref)、[CLI](https://aka.ms/ams-v3-cli-ref)、またはサポートされているいずれかの [SDK](media-services-apis-overview.md#sdks) を使用します。
<!-- Move this section. This section should be moved to conceptual.  It doesn't belong in task based -->

### <a name="use-the-azure-portal-to-create-a-media-services-account"></a>Azure portal を使用して Media Services アカウントを作成する

1. [Azure Portal](https://portal.azure.com/) にサインインします。
1. **[+リソースの作成]**  >  **[メディア]**  >  **[Media Services]** の順にクリックします。
1. **[Media Services アカウントを作成する]** セクションに必要な値を入力します。

    | 名前 | 説明 |
    | ---|---|
    |**アカウント名**|新しい Media Services アカウントの名前を入力します。 Media Services アカウント名に使用できる文字は、小文字または数字のみで、空白を含めることはできません。長さは 3 文字から 24 文字です。|
    |**サブスクリプション**|複数のサブスクリプションがある場合は、アクセス権のある Azure サブスクリプションの一覧から 1 つを選択します。|
    |**リソース グループ**|新規または既存のリソースを選択します。 リソース グループとは、ライフサイクル、アクセス許可、ポリシーを共有するリソースの集まりです。 [こちら](../../azure-resource-manager/management/overview.md#resource-groups)をご覧ください。|
    |**場所**|Media Services アカウントのメディアとメタデータのレコードを保存する地理的リージョンを選択します。 このリージョンでメディアの処理とストリーミングが行われます。 ドロップダウン リストのボックスには、利用可能な Media Services リージョンのみが表示されます。 |
    |**ストレージ アカウント**|Media Services アカウントのメディア コンテンツの BLOB ストレージとなるストレージ アカウントを選択します。 Media Services アカウントと同じリージョンにある既存のストレージ アカウントを選択することも、新しいストレージ アカウントを作成することもできます。 新しいストレージ アカウントは同じリージョンに作成されます。 ストレージ アカウントの命名規則は、Media Services アカウントと同じです。<br/><br/>1 つの**プライマリ** ストレージ アカウントを持つ必要があります。Media Services アカウントに関連付けられた任意の数の **セカンダリ** ストレージ アカウントを持つことができます。 Azure portal を使用して、セカンダリ ストレージ アカウントを追加できます。 詳細については、[Azure Storage アカウントの Azure Media Services アカウント](storage-account-concept.md)に関するページを参照してください。<br/><br/>Media Services アカウントおよび関連するすべてのストレージ アカウントは、同じ Azure サブスクリプションに存在する必要があります。 待ち時間やデータ エグレス コストが増加することを回避するために、ストレージ アカウントを Media Services アカウントと同じ場所で使用することを強くお勧めします。|

1. **[ダッシュボードにピン留めする]** チェック ボックスをオンにして、アカウントのデプロイの進行状況を確認します。
1. フォームの下部にある **[作成]** をクリックします。

    Media Services アカウントの作成時に、**既定**のストリーミング エンドポイントが**停止**状態でアカウントに追加されます。 コンテンツのストリーミングを開始し、[ダイナミック パッケージ](dynamic-packaging-overview.md)と[動的暗号化](content-protection-overview.md)を活用するには、コンテンツのストリーミング元のストリーミング エンドポイントが**実行中**状態である必要があります。 

## <a name="cli"></a>[CLI](#tab/cli/)

## <a name="use-the-azure-cli"></a>Azure CLI の使用

<!-- NOTE: The following are in the includes file and are reused in other How To articles. All task based content should be in the includes folder with the task- prefix prepended to the file name. -->

### <a name="set-the-azure-subscription"></a>Azure サブスクリプションを設定する

[!INCLUDE [Set the Azure subscription with CLI](./includes/task-set-azure-subscription-cli.md)]

### <a name="create-a-resource-group"></a>リソース グループを作成する

[!INCLUDE [Create a resource group with CLI](./includes/task-create-resource-group-cli.md)]

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成

[!INCLUDE [Create a storage account with CLI](./includes/task-create-storage-account-cli.md)]

### <a name="create-a-media-services-account"></a>Media Services アカウントを作成する

[!INCLUDE [Create a Media Services account with CLI](./includes/task-create-media-services-account-cli.md)]

### <a name="see-also"></a>関連項目

* [Azure CLI](/cli/azure/ams?view=azure-cli-latest)
* [Media Services アカウントにセカンダリ ストレージを接続する](/cli/azure/ams/account/storage?view=azure-cli-latest#az-ams-account-storage-add)

---

## <a name="next-steps"></a>次のステップ

[ファイルのストリーミング](stream-files-dotnet-quickstart.md)
