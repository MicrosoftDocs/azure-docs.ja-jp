---
title: Azure Remote Rendering アカウントを作成する
description: Azure Remote Rendering 用のアカウントを作成する手順について説明します。
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: 83bd4a7ae0082d24f7ac617719e628f4db4baeb9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98197636"
---
# <a name="create-an-azure-remote-rendering-account"></a>Azure Remote Rendering アカウントを作成する

この章では、**Azure Remote Rendering** サービスのアカウントを作成する手順について説明します。 クイックスタートまたはチュートリアルを完了するには、有効なアカウントが必要です。

## <a name="create-an-account"></a>アカウントを作成する

Azure Remote Rendering サービスのアカウントを作成するには、次の手順を実行する必要があります。

1. [Mixed Reality プレビュー ページ](https://aka.ms/MixedRealityPrivatePreview)にアクセスします。
1. [リソースの作成] をクリックします。
1. 検索フィールド ([marketplace を検索]) で「Remote Rendering」と入力し、Enter キーを押します。
1. 結果の一覧で、[Remote Rendering] タイルをクリックします。
1. 次の画面で、[作成] ボタンをクリックします。 フォームが開き、新しい Remote Rendering アカウントが作成されます。
    1. [リソース名] をアカウントの名前に設定します。
    1. 必要に応じて、[サブスクリプション] を更新します。
    1. [リソース グループ] を任意のリソース グループに設定します。
    1. [場所] ドロップダウンから、このリソースを作成するリージョンを選択します。 下の[アカウント リージョン](create-an-account.md#account-regions)にの解説を参照してください。
1. アカウントが作成されたら、そのアカウントに移動し、次の操作を行います。
    1. *[概要]* タブで、[アカウント ID] をメモします。
    1. *[設定] > [アクセス キー]* タブで、[プライマリ キー] をメモします。これはアカウントの秘密アカウント キーです。

### <a name="account-regions"></a>アカウント リージョン
アカウントの作成時に指定された場所によって、アカウント リソースが割り当てられるリージョンが決まります。 作成後にこれを変更することはできません。 ただし、アカウントの場所に関係なく、アカウントを使用して、任意の[サポートされているリージョン](./../reference/regions.md)の Remote Rendering セッションに接続できます。

### <a name="retrieve-the-account-information"></a>アカウント情報を取得する

サンプルとチュートリアルでは、アカウント ID とキーを指定する必要があります。 たとえば、PowerShell サンプル スクリプトに使用される **arrconfig.json** ファイルの場合は、次のようになります。

```json
"accountSettings": {
    "arrAccountId": "<fill in the account ID from the Azure portal>",
    "arrAccountKey": "<fill in the account key from the Azure portal>",
    "region": "<select from available regions>"
},
```

*region* オプションを入力するには、[使用可能なリージョンの一覧](../reference/regions.md)を参照してください。

**`arrAccountId`** と **`arrAccountKey`** の値は、次の手順に従ってポータルで確認できます。

* [Azure portal](https://www.portal.azure.com) に移動します
* **"Remote Rendering アカウント"** を見つけます。これは、 **[最近のリソース]** 一覧に表示されます。 また、上部にある検索バーで検索することもできます。 その場合は、使用するサブスクリプションが [既定のサブスクリプション フィルター] (検索バーの横にあるフィルター アイコン) で選択されていることを確認します。

![サブスクリプション フィルター](./media/azure-subscription-filter.png)

アカウントをクリックするとこの画面が表示され、 **[アカウント ID]** がすぐに表示されます。

![Azure アカウント ID](./media/azure-account-id.png)

キーについては、左側のパネルで **[アクセス キー]** を選択します。 次のページには、プライマリ キーとセカンダリ キーが表示されます。

![Azure アクセス キー](./media/azure-account-primary-key.png)

**`arrAccountKey`** の値には、プライマリ キーとセカンダリ キーのどちらかを指定できます。

## <a name="link-storage-accounts"></a>ストレージ アカウントをリンクする

この段落では、ストレージ アカウントを Remote Rendering アカウントにリンクする方法について説明します。 ストレージ アカウントがリンクされている場合は、アカウント内のデータを操作するたびに SAS URI を生成する必要はありません。たとえば、モデルを読み込む場合などです。 代わりに、[モデルの読み込み](../concepts/models.md#loading-models)に関するセクションの説明に従って、ストレージ アカウント名を直接使用できます。

このアクセス方法を使用する必要があるストレージ アカウントごとに、この段落の手順を実行する必要があります。 ストレージ アカウントをまだ作成していない場合は、[レンダリング用のモデルの変換に関するクイックスタート](../quickstarts/convert-model.md#storage-account-creation)の各手順に従ってください。

ここでは、ストレージ アカウントがあることを前提としています。 ポータルでストレージ アカウントに移動し、そのストレージ アカウントの **[アクセス制御 (IAM)]** タブに移動します。

![ストレージ アカウントの IAM](./media/azure-storage-account.png)

このストレージ アカウントに対する所有者のアクセス許可があることを確認して、ロールの割り当てを確実に追加できるようにします。 アクセス許可がない場合、 **[ロールの割り当てを追加する]** オプションは無効になります。

[ロールの割り当てを追加する] タイルの **[追加]** をクリックして、ロールを追加します。

![ストレージ アカウントの IAM のロール割り当て追加](./media/azure-add-role-assignment.png)

* 上のスクリーンショットに示すように、**ストレージ BLOB データ共同作成者** ロールを割り当てます。
* **[アクセスの割り当て先]** ドロップダウンから、 **[Remote Rendering アカウント]** システムによって割り当てられたマネージド ID を選択します。
* 最後のドロップダウンで、サブスクリプションと Remote Rendering アカウントを選択します。
* [保存] をクリックして変更を保存します。

> [!WARNING]
> Remote Rendering アカウントが表示されない場合は、この[トラブルシューティングに関するセクション](../resources/troubleshoot.md#cant-link-storage-account-to-arr-account)を参照してください。

> [!IMPORTANT]
> Azure ロールの割り当ては Azure Storage によってキャッシュされるため、Remote Rendering アカウントへのアクセスを許可してから、ストレージ アカウントへのアクセスに使用できるようになるまでに、最大 30 分の遅延が発生する場合があります。 詳細については、[Azure ロールベースのアクセス制御 (Azure RBAC) に関するドキュメント](../../role-based-access-control/troubleshooting.md#role-assignment-changes-are-not-being-detected)をご覧ください。

## <a name="next-steps"></a>次のステップ

* [認証](authentication.md)
* [認証に Azure フロントエンド API を使用する](frontend-apis.md)
* [PowerShell スクリプトの例](../samples/powershell-example-scripts.md)