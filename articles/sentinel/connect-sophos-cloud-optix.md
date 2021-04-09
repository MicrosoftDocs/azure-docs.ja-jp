---
title: Azure Sentinel に Sophos Cloud Optix データを接続する | Microsoft Docs
description: Sophos Cloud Optix コネクタを使用して、<PRODUCT NAME> ログを Azure Sentinel にプルする方法について説明します。 <PRODUCT NAME> のデータをブックに表示し、アラートを作成し、調査を改善します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/26/2021
ms.author: yelevin
ms.openlocfilehash: c66205ffd9bd5a742d645cbf2f9251a44329a16e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101743557"
---
# <a name="connect-your-sophos-cloud-optix-to-azure-sentinel"></a>Azure Sentinel に Sophos Cloud Optix を接続する

> [!IMPORTANT]
> Sophos Cloud Optix コネクタは、現在 **プレビュー** 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

Sophos Cloud Optix コネクタを使用すると、Azure Sentinel にすべての Sophos Cloud Optix セキュリティ ソリューションのログを簡単に接続でき、ダッシュボードの表示、カスタム アラートの作成、調査の改善を行うことができます。  この機能により、組織のクラウド セキュリティとコンプライアンス体制に関するより詳細な分析情報が得られ、クラウド セキュリティの運用機能が改善されます。 Sophos Cloud Optix と Azure Sentinel との統合には、REST API を使用します。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="configure-and-connect-sophos-cloud-optix"></a>Sophos Cloud Optix を構成して接続する

Sophos Cloud Optix では、ログを統合し、Azure Sentinel に直接エクスポートできます。

1. Azure Sentinel ポータルで、 **[データ コネクタ]** をクリックし、 **[Sophos Cloud Optix (Preview)]\(Sophos Cloud Optix (プレビュー)\)** を選択します。

1. **[Open connector page]\(コネクタ ページを開く\)** を選択します。

1. コネクタ ページで、 **[ワークスペース ID]** と **[主キー]** をコピーして保存します。

1. Sophos の指示に従って [Microsoft Azure Sentinel と統合](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/tasks/IntegrateAzureSentinel.html)します (3 番目の手順から開始します)。

## <a name="find-your-data"></a>データの検索

接続が正常に確立されると、*SophosCloudOptix_CL* テーブル内の **[カスタム ログ]** セクションの **[ログ]** にデータが表示されます。

Sophos Cloud Optix データを照会するには、クエリ ウィンドウに「`SophosCloudOptix_CL`」と入力します。 いくつかの便利なクエリ サンプルについては、コネクタ ページの **[Next steps]\(次の手順\)** タブおよび [Sophos のドキュメント](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/concepts/ExampleAzureSentinelQueries.html)を参照してください。

## <a name="validate-connectivity"></a>接続の検証

ログが Log Analytics に表示され始めるまで、最大 20 分かかることがあります。 

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Sophos Cloud Optix を Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。
