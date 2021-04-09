---
title: JavaScript アプリケーションのソース マップのサポート - Azure Monitor Application Insights
description: Application Insights を使用して、独自のストレージ アカウントの BLOB コンテナーにソース マップをアップロードする方法について説明します。
ms.topic: conceptual
author: DavidCBerry13
ms.author: daberry
ms.date: 06/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: 79462dcb503e5835254b89f7ebaa7a9ee34946d3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98933935"
---
# <a name="source-map-support-for-javascript-applications"></a>JavaScript アプリケーションに対するソース マップのサポート

Application Insights では、独自のストレージ アカウントの BLOB コンテナーに対するソース マップのアップロードがサポートされています。
ソース マップを使用して、エンド ツー エンドのトランザクション詳細ページで見つかった呼び出し履歴をアンミニファイすることができます。 [JavaScript SDK][ApplicationInsights-JS] または [Node.js SDK][ApplicationInsights-Node.js] によって送信されたすべての例外は、ソース マップでアンミニファイできます。

![ストレージ アカウントとリンクすることによって呼び出し履歴をアンミニファイする](./media/source-map-support/details-unminify.gif)

## <a name="create-a-new-storage-account-and-blob-container"></a>新しいストレージ アカウントと BLOB コンテナーを作成する

ストレージ アカウントまたは BLOB コンテナーが既にある場合は、このステップを省略できます。

1. [Create a new storage account \(新しいストレージ アカウントの作成\)][create storage account]
2. ストレージ アカウント内に [BLOB コンテナーを作成][create blob container]します。 必ず [パブリック アクセス レベル] を [`Private`] に設定して、ソース マップにパブリックにアクセスできないようにします。

> [!div class="mx-imgBorder"]
>![コンテナーのアクセス レベルをプライベートに設定する必要がある](./media/source-map-support/container-access-level.png)

## <a name="push-your-source-maps-to-your-blob-container"></a>ソース マップを BLOB コンテナーにプッシュする

構成済みの BLOB コンテナーにソース マップを自動的にアップロードするように構成することで、継続的配置パイプラインをストレージ アカウントと統合する必要があります。

Blob Storage コンテナーのフォルダー構造がソース マップのコンパイルおよびデプロイ時のフォルダー構造と同じであれば、ソース マップを Blob Storage コンテナーにアップロードできます。 一般的なユース ケースは、`1.2.3/static/js/main.js` のように、デプロイ フォルダーにそのバージョンでプレフィックスを付けることです。 `sourcemaps` という名前の Azure Blob コンテナーでアンミニファイ処理するとき、`sourcemaps/1.2.3/static/js/main.js.map` に置かれているソース マップの取得が試行されます。

### <a name="upload-source-maps-via-azure-pipelines-recommended"></a>Azure Pipelines 経由でソース マップをアップロードする (推奨)

Azure Pipelines を使用してアプリケーションの継続的なビルドとデプロイを行っている場合は、[Azure ファイル コピー][azure file copy] タスクをパイプラインに追加して、ソース マップを自動的にアップロードします。

> [!div class="mx-imgBorder"]
> ![ソース マップを Azure Blob Storage にアップロードするために、Azure ファイル コピー タスクをパイプラインに追加する](./media/source-map-support/azure-file-copy.png)

## <a name="configure-your-application-insights-resource-with-a-source-map-storage-account"></a>ソース マップのストレージ アカウントで Application Insights リソースを構成する

### <a name="from-the-end-to-end-transaction-details-page"></a>エンドツーエンド トランザクションの詳細ページから

エンドツーエンド トランザクションの詳細タブで *[Unminify]\(アンミニファイ\)* をクリックすると、リソースが未構成の場合、構成するよう求めるプロンプトが表示されます。

1. ポータルで、ミニファイされた例外の詳細を表示します。
2. *[Unminify]\(アンミニファイ\)* をクリックします
3. リソースが構成されていない場合は、構成を求めるメッセージが表示されます。

### <a name="from-the-properties-page"></a>プロパティ ページから

Application Insights リソースにリンクされているストレージ アカウントまたは BLOB コンテナーを構成または変更する場合は、Application Insights リソースの *[プロパティ]* タブを表示します。

1. Application Insights リソースの *[プロパティ]* タブに移動します。
2. *[ソース マップ BLOB コンテナーの変更]* をクリックします。
3. ソース マップ コンテナーとして別の BLOB コンテナーを選択します。
4. [`Apply`] をクリックします。

> [!div class="mx-imgBorder"]
> ![[プロパティ] ブレードに移動して、選択した Azure BLOB コンテナーを再構成する](./media/source-map-support/reconfigure.png)

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="required-azure-role-based-access-control-azure-rbac-settings-on-your-blob-container"></a>BLOB コンテナーで必要な Azure ロールベースのアクセス制御 (Azure RBAC) の設定

ポータルでこの機能を使用するすべてのユーザーには、少なくとも、BLOB コンテナーに対する[ストレージ BLOB データ閲覧者][storage blob data reader]が割り当てられている必要があります。 この機能を通じてソース マップを使用する他のすべてのユーザーに、このロールを割り当てる必要があります。

> [!NOTE]
> コンテナーの作成方法によっては、ユーザーまたはチームにこれが自動的に割り当てられなかった可能性があります。

### <a name="source-map-not-found"></a>ソース マップが見つからない

1. 対応するソース マップが正しい BLOB コンテナーにアップロードされていることを確認します
2. ソース マップ ファイルの名前が、マップ対象の JavaScript ファイルの名前の後に `.map` が付加されたものになっていることを確認します。
    - たとえば、`/static/js/main.4e2ca5fa.chunk.js` では、`main.4e2ca5fa.chunk.js.map` という名前の BLOB が検索されます
3. ブラウザーのコンソールで、エラーがログに記録されているかどうかを確認します。 サポート チケットにこれを含めます。

## <a name="next-steps"></a>次の手順

* [Azure ファイル コピー タスク](/azure/devops/pipelines/tasks/deploy/azure-file-copy)


<!-- Remote URLs -->
[create storage account]: ../../storage/common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal
[create blob container]: ../../storage/blobs/storage-quickstart-blobs-portal.md
[storage blob data reader]: ../../role-based-access-control/built-in-roles.md#storage-blob-data-reader
[ApplicationInsights-JS]: https://github.com/microsoft/applicationinsights-js
[ApplicationInsights-Node.js]: https://github.com/microsoft/applicationinsights-node.js
[azure file copy]: https://aka.ms/azurefilecopyreadme
