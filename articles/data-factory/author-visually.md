---
title: Azure Data Factory でのビジュアルの作成 | Microsoft Docs
description: Azure Data Factory でビジュアルの作成機能を使用する方法について説明します
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: djpmsft
ms.author: daperlov
ms.reviewer: ''
manager: craigg
ms.openlocfilehash: e8b9aa3af6b15272e19589eb422016f4abd9380f
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883665"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Azure Data Factory でのビジュアルの作成

Azure Data Factory ユーザー インターフェイス エクスペリエンス (UX) を使用すると、コードを記述することなくデータ ファクトリ用リソースを視覚的に作成してデプロイできます。 パイプライン キャンバスにアクティビティをドラッグし、テストの実行を行い、反復的にデバッグを行い、パイプラインの実行を展開して監視することができます。

## <a name="authoring-canvas"></a>作成キャンバス

**作成キャンバス**を開くには、鉛筆アイコンをクリックします。 

![作成キャンバス](media/author-visually/authoring-canvas.png)

ここでは、ファクトリを構成するパイプライン、アクティビティ、データセット、リンクされたサービス、データ フロー、トリガー、および統合ランタイムを作成します。 作成キャンバスを使用してパイプラインの構築を開始するには、[コピー アクティビティを使用したデータのコピー](tutorial-copy-data-portal.md)に関する記事をご覧ください。 

既定のビジュアル作成エクスペリエンスは、Data Factory サービスと直接連携します。 データ ファクトリ パイプラインでの作業時にソース管理とコラボレーションを可能にするために、Azure Repos Git または GitHub 統合もサポートされます。 これらの作成エクスペリエンスの違いについて詳しくは、「[Azure Data Factory でのソース管理](source-control.md)」をご覧ください。

## <a name="expressions-and-functions"></a>式と関数

静的な値の代わりに式と関数を使用すると、Azure Data Factory 内の多くのプロパティを指定できます。

プロパティ値の式を指定するには、 **[動的なコンテンツの追加]** を選択するか、フィールドにフォーカスがあるときに **Alt + P** キーを押します。

![動的なコンテンツの追加](media/author-visually/dynamic-content-1.png)

これにより、**Data Factory の式ビルダー**が開き、サポートされているシステム変数、アクティビティの出力、関数、およびユーザー指定の変数またはパラメーターから式を作成できます。 

![式ビルダー](media/author-visually/dynamic-content-2.png)

式言語について詳しくは、「[Azure Data Factory の式と関数](control-flow-expression-language-functions.md)」をご覧ください。

## <a name="provide-feedback"></a>フィードバックの提供

機能に関するコメントを送信するには、またはツールの問題について Microsoft に通知するには、 **[フィードバック]** を選択します。

![フィードバック](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>次の手順

パイプラインの監視と管理について詳しくは、[プログラムでのパイプラインの監視と管理](monitor-programmatically.md)に関する記事をご覧ください。
