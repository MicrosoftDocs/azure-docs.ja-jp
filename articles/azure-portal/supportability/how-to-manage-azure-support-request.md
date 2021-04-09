---
title: Azure サポート リクエストを管理する
description: サポート リクエストの表示、メッセージの送信、リクエストの重大度の変更、Azure サポートとの診断情報の共有、クローズされたサポート リクエストの再開、およびファイルのアップロードの方法について説明します。
tags: billing
ms.assetid: 86697fdf-3499-4cab-ab3f-10d40d3c1f70
ms.topic: how-to
ms.date: 12/14/2020
ms.openlocfilehash: 4d0c03e0035f6b71a23891ac1691f5421c1bdb76
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102502520"
---
# <a name="manage-an-azure-support-request"></a>Azure サポート リクエストを管理する

[Azure サポート リクエストを作成](how-to-create-azure-support-request.md)したら、[Azure portal](https://portal.azure.com) でそれを管理できます (この記事で説明)。 [Azure サポート チケット REST API ](/rest/api/support)を使用して、または [Azure CLI](/cli/azure/azure-cli-support-request) を使用して、プログラムによって要求を作成して管理することもできます。

## <a name="view-support-requests"></a>サポート リクエストを表示する

サポート リクエストの詳細と状態を表示するには、 **[ヘルプとサポート]**  >   **[すべてのサポート リクエスト]** の順に移動します。

:::image type="content" source="media/how-to-manage-azure-support-request/all-requests-lower.png" alt-text="すべてのサポート リクエスト":::

このページでは、サポート リクエストの検索、フィルター処理、および並べ替えを行うことができます。 サポート リクエストを選択すると、その重大度やリクエストに関連付けられているメッセージなどの詳細が表示されます。

## <a name="send-a-message"></a>メッセージの送信

1. **[すべてのサポート リクエスト]** ページで、サポート リクエストを選択します。

1. **[サポート リクエスト]** ページで、 **[新しいメッセージ]** を選択します。

1. メッセージを入力し、 **[送信]** を選択します。

## <a name="change-the-severity-level"></a>重大度レベルを選択します。

> [!NOTE]
> 最高重大度は[サポート プラン](https://azure.microsoft.com/support/plans)ごとに異なります。
>

1. **[すべてのサポート リクエスト]** ページで、サポート リクエストを選択します。

1. **[サポート リクエスト]** ページで、 **[変更]** を選択します。

    :::image type="content" source="media/how-to-manage-azure-support-request/change-severity.png" alt-text="サポート リクエストの重大度を変更する":::

1. リクエストが既にサポート エンジニアに割り当てられているかによって、Azure portal に 2 つの画面のいずれかが表示されます。

    - リクエストが割り当てられていない場合は、次のような画面が表示されます。 新しい重大度レベルを選択して、 **[変更]** を選択します。

        :::image type="content" source="media/how-to-manage-azure-support-request/unassigned-can-change-severity.png" alt-text="新しい重大度レベルを選択する":::

    - リクエストが割り当てられている場合は、次のような画面が表示されます。 **[OK]** を選択してから、重大度レベルの変更を要求する [新しいメッセージ](#send-a-message)を作成します。

        :::image type="content" source="media/how-to-manage-azure-support-request/assigned-cant-change-severity.png" alt-text="新しい重大度レベルを選択できない":::

## <a name="share-diagnostic-information-with-azure-support"></a>Azure サポートと診断情報を共有する

サポート リクエストを作成する場合、既定では、 **[診断情報の共有]** オプションが選択されています。 これにより、Azure サポートが Azure リソースから[診断情報](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/)を収集できるようになります。

* リクエストが作成された後で、このオプションをオフにすることはできません。

* リクエストの作成時にこのオプションをオフにした場合は、リクエストが作成された後でそれを選択できます。

    1. **[すべてのサポート リクエスト]** ページで、サポート リクエストを選択します。
    
    1. **[サポート リクエスト]** ページで、 **[アクセス許可の付与]** を選択してから、 **[はい]** と **[OK]** を選択します。
    
        :::image type="content" source="media/how-to-manage-azure-support-request/grant-permission-manage.png" alt-text="診断情報に対してアクセス許可を付与する":::

## <a name="upload-files"></a>ファイルをアップロードする

ファイルのアップロード オプションを使用すると、サポート リクエストに関連すると思われる診断ファイルやその他のファイルをアップロードすることができます。

1. **[すべてのサポート リクエスト]** ページで、サポート リクエストを選択します。

1. **[サポート リクエスト]** ページで、目的のファイルを参照して見つけてから、 **[アップロード]** を選択します。 ファイルが複数存在する場合は、この手順を繰り返します。

    :::image type="content" source="media/how-to-manage-azure-support-request/file-upload.png" alt-text="ファイルをアップロードする":::

### <a name="file-upload-guidelines"></a>ファイルのアップロードのガイドライン

ファイル アップロード オプションを使用する場合は、以下のガイドラインに従ってください。

* プライバシーを保護するため、アップロードには個人情報を含めないでください。
* ファイル名は、110 文字以内にする必要があります。
* 複数のファイルをアップロードすることはできません。
* ファイルは、4 MB 以下である必要があります。
* すべてのファイルには、 *.docx* や *.xlsx* などのファイル名拡張子が必要です。 次の表に、アップロードが許可されるファイル名拡張子を示します。

| 0 ～ 9、A ～ C    | D ～ G   | H - N         | O - Q   | R ～ T      | U ～ W        | X ～ Z     |
|-------------|-------|-------------|-------|----------|------------|---------|
| .7z         | .dat  | .har        | .odx  | .rar     | .uccapilog | .xlam   |
| .a          | .db   | .hwl        | .oft  | .rdl     | .uccplog   | .xlr    |
| .abc        | .DMP  | .ics        | .old  | .rdlc    | .udcx      | .xls    |
| .adm        | .do_  | .ini        | .one  | .re_     | .vb_       | .xlsb   |
| .aspx       | .doc  | .java       | .osd  | .remove  | .vbs_      | .xlsm   |
| .ATF        | .docm | .jpg        | .OUT  | .ren     | .vcf       | .xlsx   |
| .b          | .docx | .LDF        | .p1   | .rename  | .vsd       | .xlt    |
| .ba_        | .dotm | .letterhead | .pcap | .rft     | .wdb       | .xltx   |
| .bak        | .dotx | .lo_        | .pdb  | .rpt     | .wks       | .xml    |
| .blg        | .dtsx | .log        | .pdf  | .rte     | .wma       | .xmla   |
| .CA_        | .eds  | .lpk        | .piz  | .rtf     | .wmv       | .xps    |
| .CAB        | .emf  | .manifest   | .pmls | .run     | .wmz       | .xsd    |
| .cap        | .eml  | .master     | .png  | .saz     | .wps       | .xsn    |
| .catx       | .emz  | .mdmp       | .potx | .sql     | .wpt       | .xxx    |
| .CFG        | .err  | .mof        | .ppt  | .sqlplan | .wsdl      | .z_     |
| .compressed | .etl  | .mp3        | .pptm | .stp     | .wsp       | .z01    |
| .Config     | .evt  | .mpg        | .pptx | .svclog  | .wtl       | .z02    |
| .cpk        | .evtx | .ms_        | .prn  | .tdb     | -          | .zi     |
| .cpp        | .EX   | .msg        | .psf  | .tdf     | -          | .zi_    |
| .cs         | .ex_  | .mso        | .pst  | .text    | -          | .zip    |
| .CSV        | .ex0  | .msu        | .pub  | .thmx    | -          | .zip_   |
| .cvr        | .FRD  | .nfo        | -     | .tif     | -          | .zipp   |
| -           | .gif  | -           | -     | .trc     | -          | .zipped |
| -           | .guid | -           | -     | .TTD     | -          | .zippy  |
| -           | .gz   | -           | -     | .tx_     | -          | .zipx   |
| -           | -     | -           | -     | .txt     | -          | .zit    |
| -           | -     | -           | -     | -        | -          | .zix    |
| -           | -     | -           | -     | -        | -          | .zzz    |

## <a name="close-a-support-request"></a>サポート リクエストをクローズする

サポート リクエストをクローズする必要がある場合は、要求をクローズするように求める[メッセージを送信](#send-a-message)します。

## <a name="reopen-a-closed-request"></a>クローズされたリクエストを再度開く

クローズされたサポート リクエストを再度開く必要がある場合は、[新しいメッセージ](#send-a-message)を作成します。これにより、リクエストが自動的に再度開きます。

## <a name="cancel-a-support-plan"></a>サポート プランを取り消す

サポート プランを取り消す必要がある場合は、[サポート プランの取り消し](../../cost-management-billing/manage/cancel-azure-subscription.md#cancel-a-support-plan)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

[Azure サポート要求を作成する方法](how-to-create-azure-support-request.md)

[Azure サポート チケット REST API](/rest/api/support)
