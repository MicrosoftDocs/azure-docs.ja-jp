---
title: コマーシャル マーケットプレースの分析データにアクセスするためのサンプル アプリケーション
description: サンプル アプリケーションを使用して、コマーシャル マーケットプレースの独自の分析アプリケーションを作成します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 8fe2301c4d4ed2a582774c65d5dbe68bab416aa3
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583606"
---
# <a name="sample-application-for-accessing-commercial-marketplace-analytics-data"></a>コマーシャル マーケットプレースの分析データにアクセスするためのサンプル アプリケーション

サンプル アプリケーションは C# および JAVA の各言語で作成されていて、[GitHub](https://github.com/partneranalytics) で入手できます。

- [C# サンプル アプリケーション](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV)
- [Java サンプル アプリケーション](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV_Java)

サンプル アプリケーションからアイデアを得て、任意の言語で独自のアプリケーションを作成することもできます。

サンプル アプリケーションでは、次の目標を達成できます。

- Azure Active Directory (Azure AD) トークンを生成する。
- 使用可能なデータセットを取得する。
- ユーザー定義クエリを作成する。
- ユーザー定義およびシステムのクエリを取得する。
- レポートをスケジュールする。

このサンプル アプリケーションでは、他の機能の API を呼び出す方法については示されていません。 ただし、他の API は、前述と同じ方法で呼び出すことができます。

## <a name="how-to-run-the-application"></a>アプリケーションを実行する方法

1. 次のコマンドを使用して、リポジトリをローカル システムに複製します。

    `git clone https://github.com/partneranalytics/ProgrammaticExportSampleAppISV.git`

    > [!NOTE]
    > 詳しい手順については、GitHub [リポジトリ](https://github.com/partneranalytics/ProgrammaticExportSampleAppISV.git)内の `ProgrammaticExportSampleAppISV/README.md` ファイルを参照してください。

1. アプリケーションをすばやく実行するには、**appsettings.Development.js** でクライアント ID とクライアント シークレットを更新します。

    :::image type="content" source="./media/analytics-programmatic-access/appsettings-development-json.png" alt-text="appsettings.Development.js ファイルのスニペットを示しています。":::

アプリを実行すると、ローカル Web サーバーが起動し、ページが開きます (`https://localhost:44365/ProgrammaticExportSampleApp/sample`)。

[![[レポートの​​スケジュール] ページを示しています。](./media/analytics-programmatic-access/schedule-report.png)](./media/analytics-programmatic-access/schedule-report.png#lightbox)

このページでは、ローカル コンピューター上で実行されている Web サーバーに対して API 呼び出しを行います。その後、プログラムで実際にアクセス API 呼び出しを行います。

## <a name="code-snippets"></a>コード スニペット

プログラムでアクセス API 呼び出しを実行するための C# コードの基本構造は次のとおりです。

:::image type="content" source="./media/analytics-programmatic-access/code-snippets.png" alt-text="API 呼び出しのスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

- [コマーシャル マーケットプレースの分析データにアクセスするための API](analytics-available-apis.md)
