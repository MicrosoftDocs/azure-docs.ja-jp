---
title: Data Lake Analytics の最近の変更点
description: Data Lake Analytics は継続的に変更されていますが、この記事ではその最新一覧を提供します。
author: xujiang1
ms.service: data-lake-analytics
ms.topic: overview
ms.author: xujiang1
ms.date: 07/31/2020
ms.openlocfilehash: 6f4885594e4f67d91c595d8afdddc15e8ed4ae75
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "88263182"
---
# <a name="whats-new-in-data-lake-analytics"></a>Data Lake Analytics の新機能

Azure Data Lake Analytics は、特定のコンポーネントについて随時更新されます。 常に最新の更新プログラムを把握できるよう、この記事では以下に関する情報を提供します。

- 主要なコンポーネントのベータ プレビューの通知
- 重要なコンポーネントのバージョン情報。たとえば、コンポーネントの使用可能なバージョンの一覧、現在の既定のバージョンなど。


## <a name="notification-of-key-component-beta-preview"></a>主要なコンポーネントのベータ プレビューの通知

プレビューできる主要コンポーネントのベータ版はありません。 

## <a name="u-sql-runtime"></a>U-SQL ランタイム

コンパイラ、オプティマイザー、ジョブ マネージャーを含む Azure Data Lake の U-SQL ランタイムは、U-SQL コードを処理します。

任意のツールから Azure Data Lake Analytics ジョブを送信すると、ジョブでは運用環境で現在使用可能な既定のランタイムが使用されます。 

ランタイムのバージョンは不定期で更新されます。 以前のランタイムはしばらくの間利用可能な状態に維持されます。 新しいベータ版をプレビューする準備ができると、そこでも利用できるようになります。

> [!CAUTION]
> - 既定のものとは異なるランタイムを選択すると、U-SQL ジョブが中断される可能性があります。 これらの既定以外のバージョンは、運用環境には使用せず、テストにのみ使用することを強くお勧めします。
> - 既定以外のランタイム バージョンには、固定されたライフサイクルがあります。 これは自動的に有効期限が切れます。

次のバージョンは、現在の既定のランタイム バージョンです。

- release-20200124live_adl_16283022_2

U-SQL ランタイムのエラーのトラブルシューティング方法については、「[U-SQL ランタイム エラーのトラブルシューティング](runtime-troubleshoot.md)」を参照してください。

## <a name="net-framework"></a>.NET Framework

現在、Azure Data Lake Analytics では、 **.NET Framework v4.7.2** が使用されています。 

お使いの Azure Data Lake Analytics U-SQL スクリプトのコードでカスタム アセンブリが使用されていて、それらのカスタム アセンブリで .NET ライブラリが使用されている場合は、コードを検証し、破壊的変更が発生しているかどうかを確認します。

.Net のアップグレードのトラブルシューティング方法については、「[.NET アップグレードのトラブルシューティング](runtime-troubleshoot.md)」を参照してください。

## <a name="release-note"></a>リリース ノート

最近の更新プログラムの詳細については、[Azure Data Lake Analytics のリリース ノート](https://github.com/Azure/AzureDataLake/tree/master/docs/Release_Notes)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

* [Azure Portal](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [CLI](data-lake-analytics-get-started-cli.md) で Data Lake Analytics の使用を開始する

