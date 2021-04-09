---
title: スキャン ルール セットを作成する
description: 組織内のデータ ソースをすばやくスキャンできるように、Azure Purview でスキャン ルール セットを作成します。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/02/2020
ms.openlocfilehash: 9662652a6a40285ad382857975ec0dd04b8ba8be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96550853"
---
# <a name="create-a-scan-rule-set"></a>スキャン ルール セットを作成する

Azure Purview カタログで、スキャン ルール セットを作成すると、組織内のデータ ソースをすばやくスキャンできます。

スキャン ルール セットとは、一連のスキャン ルールをスキャンと簡単に関連付けられるようにグループ化するためのコンテナーです。 たとえば、データ ソースの種類ごとに既定のスキャン ルール セットを作成した後、社内のすべてのスキャンに対して既定でこれらのスキャン ルール セットを使用する場合があります。 また、適切なアクセス許可を持つユーザーが、ビジネス ニーズに基づいてさまざまな構成で他のスキャン ルール セットを作成できるようにしたい場合もあります。

## <a name="steps-to-create-a-scan-rule-set"></a>スキャン ルール セットの作成手順

スキャン ルール セットを作成するには、次の操作を行います。

1. Azure Purview カタログで、 **[管理センター]** を選択します。

1. 左側のウィンドウで **[スキャン ルール セット]** を選択してから、 **[新規]** を選択し ます。

1. **[新しいスキャン ルール セット]** ページで、 **[ソースの種類]** ドロップダウン リストから、カタログ スキャナーでサポートされているデータ ソースを選択します。 スキャン ルール セットは、スキャンするデータ ソースの種類ごとに作成できます。

1. スキャン ルール セットに **名前** を指定します。 最大長は 63 文字で、空白は使用できません。 必要に応じて、**説明** を入力します。 最大長は 256 文字です。

   :::image type="content" source="./media/create-a-scan-rule-set/purview-home-page.png" alt-text="スキャン ルール セットのページを示すスクリーンショット。" border="true":::

1. **[続行]** をクリックします。

   **[ファイルの種類の選択]** ページが表示されます。 このページ上のファイルの種類のオプションは、前のページで選択したデータ ソースの種類によって異なりますので、ご注意ください。 すべてのファイルの種類が既定で有効になっています。

      :::image type="content" source="./media/create-a-scan-rule-set/select-file-types-page.png" alt-text="[ファイルの種類の選択] ページを示すスクリーンショット。":::

   このページ上で **[ドキュメント ファイルの種類]** を選択すると、Office ファイルの種類 (.doc、.docm、.docx、.dot、.odp、.ods、.odt、.pdf、.pot、.pps、.ppsx、.ppt、.pptm、.pptx、.xlc、.xls、.xlsb、.xlsm、.xlsx、.xlt) を追加または除外できます。

1. チェック ボックスをオンまたはオフにして、ファイルの種類のタイルを有効または無効にします。 Data Lake という種類のデータ ソース (Azure Data Lake Storage Gen2 や Azure Blob など) を選択した場合は、スキーマの抽出と分類に使用されるファイルの種類を有効にします。

1. 特定のデータ ソースの種類に対して、[カスタムのファイルの種類を作成する](#create-a-custom-file-type)こともできます。

1. **[続行]** をクリックします。

   **[分類ルールの選択]** ページが表示されます。 このページには、選択された **システム ルール** と **カスタム ルール**、および選択された分類ルールの合計数が表示されます。 既定では、 **[システム ルール]** のすべてのチェック ボックスがオンになっています

1. ルールを追加または除外する場合は、 **[システム ルール]** の分類ルール チェック ボックスをカテゴリごとにグローバルにオンまたはオフにすることができます。

   :::image type="content" source="./media/create-a-scan-rule-set/select-classification-rules.png" alt-text="[分類ルールの選択] ページを示すスクリーンショット。":::

1. カテゴリ ノードを展開して、個々のチェック ボックスをオンまたはオフにすることができます。 たとえば、**Argentina.DNI 番号** のルールに誤検知が多い場合は、その特定のチェック ボックスをオフにすることができます。

   :::image type="content" source="./media/create-a-scan-rule-set/select-system-rules.png" alt-text="システム ルールの選択方法を示すスクリーンショット。":::

1. **[作成]** を選択して、スキャン ルール セットの作成を完了します。

### <a name="create-a-custom-file-type"></a>カスタムのファイルの種類を作成する

Azure Purview では、スキャン ルール セットでのカスタムの拡張子の追加とカスタムの列区切り記号の定義がサポートされています。

カスタムのファイルの種類を作成するには、次の操作を行います。

1. 「[スキャン ルール セットの作成手順](#steps-to-create-a-scan-rule-set)」の手順 1 から 5 に従うか、既存のスキャン ルール セットを編集します。

1. **[ファイルの種類の選択]** ページで、 **[新しいファイルの種類]** を選択して、新しいカスタムのファイルの種類を作成します。

   :::image type="content" source="./media/create-a-scan-rule-set/select-new-file-type.png" alt-text="[ファイルの種類の選択] ページで [新しいファイルの種類] を選択する方法を示すスクリーンショット。":::

1. **ファイル拡張子** と省略可能な **説明** を入力します。

   :::image type="content" source="./media/create-a-scan-rule-set/new-custom-file-type-page.png" alt-text="[新しいカスタムのファイルの種類] ページを示すスクリーンショット。" border="true":::

1. **[ファイルの内容]** に対して次のいずれかの選択を行って、ファイル内のファイルの内容の種類を指定します。

   - **[カスタムの区切り記号]** を選択して、独自の **カスタムの区切り記号** (1 文字のみ) を入力します。

   - **[システム ファイルの種類]** を選択して、 **[システム ファイルの種類]** ドロップダウン リストからシステム ファイルの種類 (XML など) を選択します。

1. **[作成]** を選択して、カスタム ファイルを保存します。

   **[ファイルの種類の選択]** ページに戻り、新しいカスタムのファイルの種類が新しいタイルとして挿入されています。

   :::image type="content" source="./media/create-a-scan-rule-set/new-custom-file-type-tile.png" alt-text="[ファイルの種類の選択] ページ上の新しいカスタムのファイルの種類のタイルを示すスクリーンショット。":::

1. 変更または削除する場合は、新しいファイルの種類のタイルで **[編集]** を選択します。

1. **[続行]** を選択して、スキャン ルール セットの構成を完了します。

## <a name="system-scan-rule-sets"></a>システム スキャン ルール セット

システム スキャン ルール セットとは、Azure Purview カタログごとに自動的に作成される Microsoft 定義のスキャン ルール セットです。 各システム スキャン ルール セットは、特定のデータ ソースの種類に関連付けられています。 スキャンを作成するときに、それをシステム スキャン ルール セットに関連付けることができます。 Microsoft によってこれらのシステム ルール セットが更新されるたびに、お使いのカタログでそれらを更新し、関連するすべてのスキャンに更新プログラムを適用できます。

1. システム スキャン ルール セットの一覧を表示するために、 **[管理センター]** で **[スキャン ルール セット]** を選択し、 **[システム]** タブを選択します。

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-sets.jpg" alt-text="システム スキャン ルール セットの一覧を示すスクリーンショット。" lightbox="./media/create-a-scan-rule-set/system-scan-rule-sets.jpg":::

1. 各システム スキャン ルール セットには、 **[名前]** 、 **[ソースの種類]** 、 **[バージョン]** があります。 **[バージョン]** 列でスキャン ルール セットのバージョン番号を選択した場合は、現在のバージョンと以前のバージョン (存在する場合) に関連付けられているルールが表示されます。

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-set-page.jpg" alt-text="システム スキャン ルール セットのページを示すスクリーンショット。" lightbox="./media/create-a-scan-rule-set/system-scan-rule-set-page.jpg":::

1. システム スキャン ルール セットの更新プログラムが利用可能な場合は、 **[バージョン]** 列で **[更新]** を選択できます。 システム スキャン ルール セットのページで、 **[更新する新しいバージョンを選択する]** ドロップダウン リストからバージョンを選択します。 このページに、新しいバージョンと現在のバージョンに関連付けられているシステムの分類ルールの一覧が表示されます。

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-set-version.jpg" alt-text="システム スキャン ルール セットのバージョンの変更方法を示すスクリーンショット。" lightbox="./media/create-a-scan-rule-set/system-scan-rule-set-version.jpg":::

### <a name="associate-a-scan-with-a-system-scan-rule-set"></a>スキャンをシステム スキャン ルール セットに関連付ける

[スキャンを作成](tutorial-scan-data.md#scan-data-into-the-catalog)するときに、次のように、それをシステム スキャン ルール セットに関連付けることができます。

1. **[スキャン ルール セットの選択]** ページで、システム スキャン ルール セットを選択します。

   :::image type="content" source="./media/create-a-scan-rule-set/set-system-scan-rule-set.jpg" alt-text="スキャン用のシステム スキャン ルール セットの選択方法を示すスクリーンショット。" lightbox="./media/create-a-scan-rule-set/set-system-scan-rule-set.jpg":::

1. **[続行]** を選択してから、 **[保存と実行]** を選択します。
