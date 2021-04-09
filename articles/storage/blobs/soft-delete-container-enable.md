---
title: コンテナーの論理的な削除を有効にして管理する (プレビュー)
titleSuffix: Azure Storage
description: データが誤って変更または削除されたときに簡単に復旧できるように、コンテナーの論理的な削除 (プレビュー) を有効にします。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/05/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 2097c1743e07b5563bc75d3d1cce48aa11b98e5f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102216345"
---
# <a name="enable-and-manage-soft-delete-for-containers-preview"></a>コンテナーの論理的な削除を有効にして管理する (プレビュー)

コンテナーの論理的な削除 (プレビュー) では、お客様のデータが誤って変更または削除されないように保護します。 ストレージ アカウントに対してコンテナーの論理的な削除が有効になっている場合、コンテナーとその内容は、削除された後も、指定した保持期間中は復旧することができます。

アプリケーションまたは別のストレージ アカウントのユーザーによってデータが誤って変更または削除される可能性がある場合は、コンテナーの論理的な削除を有効にすることをお勧めします。 この記事では、コンテナーの論理的な削除を有効にする方法について説明します。 プレビューへの登録方法など、コンテナーの論理的な削除について詳しくは、「[コンテナーの論理的な削除 (プレビュー)](soft-delete-container-overview.md)」をご覧ください。

エンドツーエンドのデータ保護では、BLOB の論理的な削除と BLOB のバージョン管理も有効にすることをお勧めします。 BLOB の論理的な削除も有効にする方法については、[BLOB の論理的な削除の有効化と管理](soft-delete-blob-enable.md)に関する記事をご覧ください。 BLOB のバージョン管理を有効にする方法については、[BLOB のバージョン管理](versioning-overview.md)に関するページをご覧ください。

> [!IMPORTANT]
>
> コンテナーの論理的な削除は、現在 "**プレビュー**" 段階にあります。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用される法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="enable-container-soft-delete"></a>コンテナーの論理的な削除を有効にする

ストレージ アカウントでのコンテナーの論理的な削除は、Azure portal または Azure Resource Manager テンプレートを使用して、いつでも有効または無効にすることができます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal を使用してストレージ アカウントでのコンテナーの論理的な削除を有効にするには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) のストレージ アカウントに移動します。
1. **[Blob service]** の下の **[データ保護]** 設定を見つけます。
1. **[コンテナーの論理的な削除]** プロパティを *[有効]* に設定します。
1. **[保持ポリシー]** で、論理的に削除されたコンテナーが Azure Storage によって保持される期間を指定します。
1. 変更を保存します。

:::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-configure.png" alt-text="Azure portal でコンテナーの論理的な削除を有効にする方法を示すスクリーンショット":::

# <a name="template"></a>[テンプレート](#tab/template)

Azure Resource Manager テンプレートを使用してコンテナーの論理的な削除を有効にするには、**containerDeleteRetentionPolicy** プロパティを設定するテンプレートを作成します。 次の手順は、Azure portal でテンプレートを作成する方法について説明しています。

1. Azure portal で、 **[リソースの作成]** を選択します。
1. **[Marketplace を検索]** で「**template deployment**」と入力し、**Enter** キーを押します。
1. **[テンプレートのデプロイ]** 、 **[作成]** 、 **[エディターで独自のテンプレートを作成する]** の順に選択します。
1. テンプレート エディターで、次の JSON を貼り付けます。 `<account-name>` プレースホルダーは、実際のストレージ アカウントの名前に置き換えます。

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "variables": {},
      "resources": [
          {
              "type": "Microsoft.Storage/storageAccounts/blobServices",
              "apiVersion": "2019-06-01",
              "name": "<account-name>/default",
              "properties": {
                  "containerDeleteRetentionPolicy": {
                      "enabled": true,
                      "days": 7
                  }
              }
          }
      ]
    }
    ```

---

1. 保持期間を指定します。 既定値は 7 です。
1. テンプレートを保存します。
1. アカウントのリソース グループを指定してから **[確認と作成]** ボタンを選択してテンプレートをデプロイし、コンテナーの論理的な削除を有効にします。

## <a name="view-soft-deleted-containers"></a>論理的に削除されたコンテナーを表示する

論理的な削除が有効になっている場合は、論理的に削除されたコンテナーを Azure portal で表示できます。 論理的に削除されたコンテナーは、指定した保持期間中は表示されます。 保持期間が経過すると、論理的に削除されたコンテナーは完全に削除され、表示されなくなります。

論理的に削除されたコンテナーを Azure portal で表示するには、次の手順を実行します。

1. Azure portal でストレージ アカウントに移動し、コンテナーの一覧を表示します。
1. 削除されたコンテナーが一覧に含まれるように [削除されたコンテナーを表示] を切り替えます。

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-list.png" alt-text="論理的に削除されたコンテナーを Azure portal で表示する方法を示すスクリーンショット":::

## <a name="restore-a-soft-deleted-container"></a>論理的に削除されたコンテナーを復元する

保持期間中は、論理的に削除されたコンテナーとその内容を復元できます。 論理的に削除されたコンテナーを Azure portal で復元するには、次の手順を実行します。

1. Azure portal でストレージ アカウントに移動し、コンテナーの一覧を表示します。
1. 復元するコンテナーのコンテキスト メニューを表示し、メニューから **[削除の取り消し]** を選択します。

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-restore.png" alt-text="論理的に削除されたコンテナーを Azure portal で復元する方法を示すスクリーンショット":::

## <a name="next-steps"></a>次の手順

- [コンテナーの論理的な削除 (プレビュー)](soft-delete-container-overview.md)
- [BLOB の論理的な削除](soft-delete-blob-overview.md)
- [BLOB バージョン管理](versioning-overview.md)
