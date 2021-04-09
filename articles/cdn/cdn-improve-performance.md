---
title: Azure CDN でのファイル圧縮によるパフォーマンスの向上 | Microsoft Docs
description: Azure CDN でファイルを圧縮して、ファイル転送速度とページ読み込みパフォーマンスを向上させる方法を説明します。
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: af1cddff-78d8-476b-a9d0-8c2164e4de5d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/28/2018
ms.author: allensu
ms.openlocfilehash: 11a2dbfc9c6da60e4dd96f65d2a20165a3663e8c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103601544"
---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>Azure CDN でのファイル圧縮によるパフォーマンスの向上
ファイル圧縮は、サーバーからファイルを送信する前にファイル サイズを減らすことができるため、ファイルの転送速度とページ読み込みのパフォーマンスを向上させることができる簡単で効率的な方法です。 ファイル圧縮により、帯域幅のコストを軽減し、ユーザーへの応答を改善することができます。

ファイル圧縮を有効にするには、次の 2 つの方法があります。

- 配信元サーバーで圧縮を有効にします。 この方法では、Azure CDN は圧縮ファイルを受け渡し、要求したクライアントに配信します。
- CDN POP サーバーで直接、圧縮を有効にします (*即時圧縮*)。 この方法では、配信元サーバーで圧縮されていない場合でも、CDN でファイルが圧縮され、エンド ユーザーに渡されます。

> [!IMPORTANT]
> Azure CDN 構成の変更がネットワーク全体に反映されるまでには時間がかかる可能性があります。 
> - **Azure CDN Standard from Microsoft** プロファイルの場合、通常、反映は 10 分以内で完了します。 
> - **Azure CDN Standard from Akamai** プロファイルの場合、通常、反映は 1 分以内で完了します。 
> - **Azure CDN Standard from Verizon** プロファイルおよび **Azure CDN Premium from Verizon** プロファイルの場合、通常、反映は 10 分で完了します。 
> 
> 今回初めて CDN エンドポイントの圧縮を設定した場合は、トラブルシューティングの前に、圧縮設定が確実に POP に反映されるように 1 ～ 2 時間の待機時間を検討してください。

## <a name="enabling-compression"></a>圧縮の有効化
Standard および Premium CDN レベルでは同じ圧縮機能が提供されますが、ユーザー インターフェイスは異なります。 Standard と Premium CDN レベルの違いの詳細については、[Azure CDN の概要](cdn-overview.md)に関するページを参照してください。

### <a name="standard-cdn-profiles"></a>Standard CDN プロファイル 
> [!NOTE]
> このセクションは、**Azure CDN Standard from Microsoft** プロファイル、**Azure CDN Standard from Verizon** プロファイル、および **Azure CDN Standard from Akamai** プロファイルに適用されます。
> 
> 

1. CDN プロファイルのページで、管理する CDN エンドポイントを選択します。

    ![CDN プロファイル エンドポイント](./media/cdn-file-compression/cdn-endpoints.png)

    CDN エンドポイントのページが開きます。
2. **[圧縮]** を選択します。

    ![ポータル メニューで [圧縮] が選択されているエンドポイントを示すスクリーンショット。](./media/cdn-file-compression/cdn-compress-select-std.png)

    圧縮のページが開きます。
3. 圧縮を有効にするには、 **[オン]** を選択します。

    ![[圧縮] の有効化を示すスクリーンショット。](./media/cdn-file-compression/cdn-compress-standard.png)
4. 既定の MIME の種類を使用するか、MIME の種類を追加または削除して一覧を変更します。

   > [!TIP]
   > 圧縮形式に圧縮を適用することは可能ですが、推奨されません。 たとえば、ZIP、MP3、MP4、JPG などの形式です。
   > 

5. 変更後、 **[保存]** を選択します。

### <a name="premium-cdn-profiles"></a>Premium CDN プロファイル
> [!NOTE]
> このセクションは、**Azure CDN Premium from Verizon** プロファイルだけに適用されます。
> 

1. CDN プロファイルのページで、 **[管理]** を選択します。

    ![CDN の管理の選択](./media/cdn-file-compression/cdn-manage-btn.png)

    CDN 管理ポータルが開きます。
2. **[HTTP ラージ]** タブ、 **[キャッシュの設定]** フライアウトの順にマウスのカーソルを合わせます。 **[圧縮]** を選択します。

    ![CDN 圧縮の選択](./media/cdn-file-compression/cdn-compress-select.png)

    圧縮のオプションが表示されます。

    ![CDN のファイル圧縮のオプション](./media/cdn-file-compression/cdn-compress-files.png)
3. **[Compression Enabled]\(圧縮の有効化\)** を選択して圧縮を有効にします。 **[ファイルの種類]** ボックスで、コンマ区切りのリスト (スペースなし) として、圧縮する MIME の種類を入力します。

   > [!TIP]
   > 圧縮形式に圧縮を適用することは可能ですが、推奨されません。 たとえば、ZIP、MP3、MP4、JPG などの形式です。
   > 

4. 変更後、 **[更新]** を選択します。

## <a name="compression-rules"></a>圧縮のルール

### <a name="azure-cdn-standard-from-microsoft-profiles"></a>Azure CDN Standard from Microsoft プロファイル

**Azure CDN Standard from Microsoft** プロファイルの場合、対象ファイルのみが圧縮されます。 圧縮を実行可能にするためのファイルの条件は、次のとおりです。
- ただし、ファイルは[圧縮用に構成されている](#enabling-compression) MIME の種類である必要があります。
- 1 KB より大きい
- 8 MB 未満

これらのプロファイルでは、次の圧縮エンコードがサポートされています。
- gzip (GNU zip)
- brotli 

要求で複数の圧縮の種類がサポートされている場合、brotli 圧縮が優先されます。

アセットの要求で gzip 圧縮が指定され、要求がキャッシュ ミスになった場合、Azure CDN は POP サーバー上で直接アセットの gzip 圧縮を行います。 その後、圧縮ファイルがキャッシュから提供されます。

配信元がチャンク転送エンコード (CTE) を使用して圧縮データを CDN POP に送信する場合、8 MB より大きい応答サイズはサポートされません。 

### <a name="azure-cdn-from-verizon-profiles"></a>Azure CDN from Verizon プロファイル

**Azure CDN Standard from Verizon** プロファイルと **Azure CDN Premium from Verizon** プロファイルについては、条件を満たすファイルのみが圧縮されます。 圧縮を実行可能にするためのファイルの条件は、次のとおりです。
- 128 バイトより大きい
- 3 MB 未満

これらのプロファイルでは、次の圧縮エンコードがサポートされています。
- gzip (GNU zip)
- DEFLATE
- bzip2
- brotli 

要求で複数の圧縮の種類がサポートされている場合、それらの圧縮の種類は brotli 圧縮よりも優先されます。

アセットの要求で brotli 圧縮が指定され (HTTP ヘッダーは `Accept-Encoding: br`)、要求がキャッシュ ミスになった場合、Azure CDN は POP サーバー上で直接アセットの brotli 圧縮を行います。 その後、圧縮ファイルがキャッシュから提供されます。

### <a name="azure-cdn-standard-from-akamai-profiles"></a>Azure CDN Standard from Akamai プロファイル

**Azure CDN Standard from Akamai** プロファイルの場合、すべてのファイルが圧縮対象です。 ただし、ファイルは[圧縮用に構成されている](#enabling-compression) MIME の種類である必要があります。

これらのプロファイルでは、gzip 圧縮エンコーディングのみがサポートされています。 プロファイル エンドポイントが gzip エンコーディングのファイルを要求する場合は、クライアントの要求に関係なく、常に配信元からの要求です。 

## <a name="compression-behavior-tables"></a>圧縮動作の表
次の表に、すべてのシナリオでの Azure CDN 圧縮動作を示します。

### <a name="compression-is-disabled-or-file-is-ineligible-for-compression"></a>圧縮が無効であるか、ファイルの圧縮が実行不可能
| クライアントから要求された形式 (Accept-Encoding ヘッダー経由) | キャッシュ ファイルの形式 | クライアントへの CDN の応答 | 注&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
| --- | --- | --- | --- |
| Compressed |Compressed |Compressed | |
| Compressed |非圧縮 |非圧縮 | |
| Compressed |キャッシュなし |圧縮または非圧縮 |配信元の応答に応じて、CDN が圧縮を実行するかどうかが決まります。 |
| 非圧縮 |Compressed |非圧縮 | |
| 非圧縮 |非圧縮 |非圧縮 | |
| 非圧縮 |キャッシュなし |非圧縮 | |

### <a name="compression-is-enabled-and-file-is-eligible-for-compression"></a>圧縮が有効でファイルの圧縮が実行可能
| クライアントから要求された形式 (Accept-Encoding ヘッダー経由) | キャッシュ ファイルの形式 | クライアントへの CDN の応答 | Notes |
| --- | --- | --- | --- |
| Compressed |Compressed |Compressed |CDN が、サポートされている形式間のトランスコードを実行します。 <br/>**Microsoft の Azure CDN** では、形式間のコード変換がサポートされていません。元のデータをフェッチした後、その形式に対して個別に圧縮とキャッシュが実行されます。 |
| Compressed |非圧縮 |Compressed |CDN が圧縮を実行します。 |
| Compressed |キャッシュなし |Compressed |配信元から非圧縮ファイルが返された場合、CDN が圧縮を実行します。 <br/>**Azure CDN from Verizon** は、最初の要求で圧縮されていないファイルを渡し、後続の要求のためにファイルを圧縮してキャッシュします。 <br/>`Cache-Control: no-cache` ヘッダーがあるファイルは圧縮されません。 |
| 非圧縮 |Compressed |非圧縮 |CDN が展開を実行します。 <br/>**Microsoft の Azure CDN** では、圧縮解除がサポートされていません。元のデータをフェッチした後、非圧縮のクライアント用に個別にキャッシュが実行されます。 |
| 非圧縮 |非圧縮 |非圧縮 | |
| 非圧縮 |キャッシュなし |非圧縮 | |

## <a name="media-services-cdn-compression"></a>Media Services CDN の圧縮
Media Services CDN ストリーミングが有効なエンドポイントの場合、以下の MIME の種類では、既定で圧縮が有効です。 
- application/vnd.ms-sstr+xml 
- application/dash+xml
- application/vnd.apple.mpegurl
- application/f4m+xml 

## <a name="see-also"></a>関連項目
* [CDN ファイルの圧縮のトラブルシューティング](cdn-troubleshoot-compression.md)    

