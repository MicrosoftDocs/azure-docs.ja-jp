---
title: Azure ストレージ アカウントの SAS キーを使用してコスト データをエクスポートする
description: この記事は、パートナーが SAS キーを作成し、Cost Management によるエクスポートを構成するうえで役立ちます。
author: bandersmsft
ms.author: banders
ms.date: 03/08/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 16302a6bcc3bf41432500d2fdaa4ec27c28dd5b3
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2021
ms.locfileid: "102509621"
---
# <a name="export-cost-data-with-an-azure-storage-account-sas-key"></a>Azure ストレージ アカウントの SAS キーを使用してコスト データをエクスポートする

次の情報は、Microsoft パートナーに限り適用されます。

パートナーは、自社の Microsoft Partner Agreement に関連付けられているテナントに自社の Azure サブスクリプションを所有していないことが少なくありません。 Microsoft Partner Agreement プランを契約しているパートナーが課金アカウントのグローバル管理者であれば、共有アクセス サービス (SAS) キーを使用することにより、コスト データをエクスポートし、別のテナントのストレージ アカウントにコピーすることができます。 言い方を変えると、SAS キーが設定されているストレージ アカウントがあると、パートナーが Partner Agreement の範囲外のストレージ アカウントを使用して、エクスポートされた情報を受け取ることができます。 この記事は、パートナーが SAS キーを作成し、Cost Management によるエクスポートを構成するうえで役立ちます。

## <a name="requirements"></a>必要条件

- Microsoft Partner Agreement を締結したパートナーであり、かつ、Azure プランの顧客がいる必要があります。
- パートナー組織の課金アカウントのグローバル管理者である必要があります。
- パートナー組織の別のテナントにあるストレージ アカウントを構成するためのアクセス権が付与されている必要があります。 自分のストレージ アカウントにデータをエクスポートする場合のアクセス許可とデータ アクセスは、自分で責任を持って管理してください。

## <a name="configure-azure-storage-with-a-sas-key"></a>Azure Storage に SAS キーを構成する

ストレージ アカウントの SAS トークンを取得するか、Azure portal を使用して作成します。 Azure portal で作成する場合には、次の手順を使用します。 SAS キーに関する詳細については、[Shared Access Signature (SAS) を使用してデータに対する制限付きアクセス権を付与する](../../storage/common/storage-sas-overview.md)方法に関するページを参照してください。

1. Azure portal 内で、ストレージ アカウントに移動します。
    - アカウントに複数のテナントに対するアクセス権が付与されている場合には、目的のストレージ アカウントにアクセスできるディレクトリに切り替えます。 Azure portal の右上隅で自分のアカウントを選択して、 **[ディレクトリの切り替え]** を選択します。
    - ストレージ アカウントにアクセスするにあたり、対応するテナント アカウントを使って Azure portal にサインインする操作が必要になることがあります。
1. 左側のメニューで、 **[Shared Access Signature]** を選択します。  
    :::image type="content" source="./media/export-cost-data-storage-account-sas-key/storage-shared-access-signature.png" alt-text="Azure ストレージに構成した Shared Access Signature のスクリーンショット。" lightbox="./media/export-cost-data-storage-account-sas-key/storage-shared-access-signature.png" :::
1. 前の画像に示したとおりの設定でトークンを構成します。
    1. _[使用できるサービス]_ には、 **[BLOB]** を選択します。
    1. _[使用できるリソースの種類]_ には、 **[サービス]** 、 **[コンテナー]** 、 **[オブジェクト]** を選択します。
    1. _[与えられているアクセス許可]_ には、 **[読み取り]** 、 **[書き込み]** 、 **[削除]** 、 **[リスト]** 、 **[追加]** 、 **[作成]** を選択します。
    1. 有効期限と日付を選択します。 エクスポートの SAS トークンは、期限切れになる前に更新するようにしてください。 期限切れまでの期間を長くするほど、新しい SAS トークンが必要になるまでにエクスポートを実行できる期間が長くなります。
1. **[許可されるプロトコル]** には、 _[HTTPS のみ]_ を選択します。
1. _[Preferred routing tier]\(優先ルーティング層\)_ には **[Basic]** を選択します。
1. _[署名キー]_ には **[key1]** を選択します。 SAS トークンの署名に使用するキーをローテーションまたは更新した場合には、エクスポート用の SAS トークンを新たに生成しなおす必要があります。
1. **[SAS と接続文字列を生成する]** を選択します。
    表示される **SAS トークン** の値が、エクスポートを構成するときに必要になるトークンです。

## <a name="create-a-new-export-with-a-sas-token"></a>SAS トークンを使用して新しいエクスポートを作成する

課金アカウントのスコープの **[エクスポート]** に移動し、次の手順に従って新しいエクスポートを作成します。

1. **［作成］** を選択します
1. 通常のエクスポートの場合と同じように、エクスポートの詳細を構成します。 既存のディレクトリやコンテナーを使用するようにエクスポートを構成することも、新しいディレクトリやコンテナーを指定し、それがエクスポート時に作成されるようにすることもできます。
1. ストレージの構成では、 **[Use a SAS token]\(SAS トークンを使用する\)** を選択します。  
    :::image type="content" source="./media/export-cost-data-storage-account-sas-key/new-export.png" alt-text="SAS トークンを選択する [新しいエクスポート] を示したスクリーンショット。" lightbox="./media/export-cost-data-storage-account-sas-key/new-export.png" :::
1. ストレージ アカウントの名前を入力し、SAS トークンを貼り付けます。
1. コンテナーまたはディレクトリには、既存のものか、新たに作成するものを指定します。
1. **［作成］** を選択します

SAS トークンベースのエクスポートは、トークンが有効な間に限り機能します。 現在のトークンが期限切れになる前にトークンを設定しなおすようにしてください。そうしないと、エクスポートが動作を停止します。 トークンはストレージ アカウントに対するアクセスを可能にするものであるため、他の機密情報と同等の注意をもって保護する必要があります。 自分のストレージ アカウントにデータをエクスポートする場合のアクセス許可とデータ アクセスは、自分で責任を持って管理してください。

## <a name="troubleshoot-exports-using-sas-tokens"></a>SAS トークンを使用したエクスポートのトラブルシューティング

SAS トークンベースのエクスポートを構成または使用する際によく見られる問題は次のとおりです。

- Azure portal に SAS キー オプションが表示されない。
  - Microsoft Partner Agreement を締結しており、かつ、課金アカウントのグローバル管理者としてのアクセス許可が付与されていることを確認してください。 そのようなユーザーだけが、SAS キーを使用したエクスポートを実行できます。

- エクスポートを構成しようとすると、次のエラーメッセージが表示される:

    **SAS トークンが BLOB サービスに対して有効になっていること、SAS トークンがコンテナーとオブジェクトのリソースの種類に対して有効になっていること、SAS トークンに作成、読み取り、書き込み、削除のアクセス許可が付与されていることを確認してください。(Storage サービスのエラー コード: AuthorizationResourceTypeMismatch)**

    - Azure Storage で SAS キーを正しく構成および生成していることを確認してください。

- エクスポートを作成した後に、SAS キーの全体を表示できない。
  - キーが表示されないのは、正常な動作です。 SAS エクスポートを構成した後は、セキュリティ上の理由からキーが非表示になります。

- エクスポートが構成されているテナントからストレージ アカウントにアクセスできない。
  - 正常な動作です。 ストレージ アカウントが別のテナントにある場合には、そのストレージ アカウントを探す前に、Azure portal 内でそのテナントに移動する必要があります。

- SAS トークン関連のエラーが発生し、エクスポートが失敗する。
  - エクスポートは、SAS トークンが有効な間に限り機能します。 新しいキーを作成してから、エクスポートを実行してください。

## <a name="next-steps"></a>次のステップ

- Cost Management のデータのエクスポートに関する詳細については、[データの作成とエクスポート](tutorial-export-acm-data.md)に関するページを参照してください。
- 大量の使用状況データのエクスポートに関する詳細については、[エクスポートを使用してサイズの大きなデータセットを取得する](ingest-azure-usage-at-scale.md)方法に関するページを参照してください。
