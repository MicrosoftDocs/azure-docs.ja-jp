---
title: Azure Data Lake Analytics の U-SQL ラインタイム エラーをトラブルシューティングする方法
description: U-SQL ランタイム エラーをトラブルシューティングする方法について説明します。
services: data-lake-analytics
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/10/2019
ms.openlocfilehash: 39b4a3bc5e5f70a5699f4fd84ec86dc61cf21483
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475262"
---
# <a name="learn-how-to-troubleshoot-u-sql-runtime-failures-due-to-runtime-changes"></a>実行時の変更による U-SQL ランタイム エラーをトラブルシューティングする方法について説明します。

コンパイラ、オプティマイザー、ジョブ マネージャーを含む Azure Data Lake の U-SQL ランタイムは、U-SQL コードを処理します。

## <a name="choosing-your-u-sql-runtime-version"></a>U-SQL ランタイム バージョンの選択

Visual Studio、ADL SDK、または Azure Data Lake Analytics ポータルから U-SQL ジョブを送信すると、ジョブは現在使用可能な既定のランタイムを使用します。 新しいバージョンの U-SQL ランタイムは定期的にリリースされ、マイナー更新プログラムとセキュリティ修正プログラムの両方が含まれています。

カスタムのランタイム バージョンを選択することもできます。その理由は、新しい更新プログラムを試したい、古いバージョンのランタイムを使い続ける必要がある、報告された問題の修正プログラムが提供されていて、通常の新しい更新を待つことができない、のいずれかです。

> [!CAUTION]
> 既定のものとは異なるランタイムを選択すると、U-SQL ジョブが中断される可能性があります。 これらの他のバージョンは、テスト目的でのみ使用してください。

まれに Microsoft サポートによって、別のバージョンのランタイムがご使用のアカウントの既定として固定されていることがあります。 この固定は、できるだけ早く元に戻してください。 そのバージョンに固定されていると、後日期限切れになります。

### <a name="monitoring-your-jobs-u-sql-runtime-version"></a>ジョブの U-SQL ランタイム バージョンの監視

Visual Studio のジョブ ブラウザーまたは Azure portal のジョブの履歴を使用して、過去のジョブによって使用されていたランタイム バージョンの履歴をアカウントのジョブ履歴で確認できます。

1. Azure Portal で Data Lake Analytics アカウントに移動します。
2. **[すべてのジョブの表示]** を選択します。 アカウントのすべてのアクティブなジョブと最近終了したジョブの一覧が表示されます。
3. 必要に応じて **[フィルター]** をクリックして、 **[時間の範囲]** 、 **[ジョブ名]** 、および **[作成者]** の値でジョブを見つけやすくします。
4. 完了したジョブで使用されたランタイムを確認できます。

![過去のジョブのランタイム バージョンの表示](./media/runtime-troubleshoot/prior-job-usql-runtime-version-.png)

使用可能なランタイム バージョンは、時間の経過と共に変化します。 既定のランタイムは常に "default"(既定) と呼ばれます。Microsoft では、少なくとも以前のランタイムを一定期間使用可能にすると共に、さまざまな理由で特別なランタイムを使用可能にしています。 明示的に指定されたランタイムは、通常、次の形式に従います (斜体は変数部分に使用され、[] は省略可能な部分を示します)。

release_YYYYMMDD_adl_buildno[_modifier]

たとえば、release_20190318_adl_3394512_2 は、2019 年 3 月 18 日のランタイム リリースのビルド 3394512 の 2 番目のバージョンを意味し、release_20190318_adl_3394512_private は同じリリースのプライベート ビルドを意味します。 注:この日付は、そのリリースの最終チェックインの実行日に関連付けられており、必ずしも正式なリリース日ではありません。


## <a name="troubleshooting-u-sql-runtime-version-issues"></a>U-SQL ランタイム バージョンの問題のトラブルシューティング

発生する可能性があるランタイム バージョンの問題として、次の 2 つが考えられます。

1. スクリプトまたは一部のユーザー コードによって、あるリリースから次のリリースにかけて動作が変更される場合。 このような重大な変更は、通常はリリース ノートの発行によって事前に伝達されます。 このような重大な変更が発生した場合は、Microsoft サポートに連絡して、この重大な動作を報告し (それがまだ文書化されていない場合)、以前のランタイム バージョンに対してジョブを送信してください。

2. 既定以外のランタイムが自分のアカウントに固定されているときに、そのランタイムを明示的または暗黙的に使用していて、そのランタイムがしばらくして削除された場合。 ランタイムの欠落が発生した場合は、スクリプトをアップグレードして、現在の既定のランタイムで実行するようにしてください。 さらに時間がかかる場合は、Microsoft サポートにお問い合わせください。

## <a name="see-also"></a>関連項目

- [Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)
- [Azure  Portal を使用して Azure Data Lake Analytics を管理する](data-lake-analytics-manage-use-portal.md)
- [Azure portal を使用して Azure Data Lake Analytics でジョブを監視する](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
