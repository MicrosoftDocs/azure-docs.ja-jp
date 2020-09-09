---
title: ビジュアルの作成
description: Azure Data Factory でビジュアルの作成機能を使用する方法について説明します
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
ms.reviewer: ''
manager: anandsub
ms.date: 05/15/2020
ms.openlocfilehash: ac51fd63adcc0328ff67f5fbe13dbfdb768f4bfe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84343117"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Azure Data Factory でのビジュアルの作成

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory ユーザー インターフェイス エクスペリエンス (UX) を使用すると、コードを記述することなくデータ ファクトリ用リソースを視覚的に作成してデプロイできます。 パイプライン キャンバスにアクティビティをドラッグし、テストの実行を行い、反復的にデバッグを行い、パイプラインの実行を展開して監視することができます。

現時点では、Azure Data Factory UX は Microsoft Edge と Google Chrome でのみサポートされています。

## <a name="authoring-canvas"></a>作成キャンバス

**作成キャンバス**を開くには、鉛筆アイコンをクリックします。 

![作成キャンバス](media/author-visually/authoring-canvas.png)

ここでは、ファクトリを構成するパイプライン、アクティビティ、データセット、リンクされたサービス、データ フロー、トリガー、および統合ランタイムを作成します。 作成キャンバスを使用してパイプラインの構築を開始するには、[コピー アクティビティを使用したデータのコピー](tutorial-copy-data-portal.md)に関する記事をご覧ください。 

既定のビジュアル作成エクスペリエンスは、Data Factory サービスと直接連携します。 データ ファクトリ パイプラインでの作業時にソース管理とコラボレーションを可能にするために、Azure Repos Git または GitHub 統合もサポートされます。 これらの作成エクスペリエンスの違いについて詳しくは、「[Azure Data Factory でのソース管理](source-control.md)」をご覧ください。

### <a name="properties-pane"></a>プロパティ ペイン

パイプライン、データセット、データ フローなどの最上位レベルのリソースの場合、上位のプロパティは、キャンバスの右側のプロパティ ペインで編集できます。 プロパティ ペインには、名前、説明、注釈、その他の上位プロパティなどのプロパティが含まれています。 パイプライン アクティビティやデータ フロー変換などのサブリソースは、キャンバスの下部にあるパネルを使用して編集されます。 

![作成キャンバス](media/author-visually/properties-pane.png)

プロパティ ペインは、既定ではリソースの作成時にのみ表示されます。 編集するには、キャンバスの右上隅にあるプロパティ ペイン アイコンをクリックします。

## <a name="management-hub"></a>管理ハブ

Azure Data Factory UX の *[管理]* タブによってアクセスされる管理ハブは、データ ファクトリのグローバル管理アクションをホストするポータルです。 ここでは、データ ストアと外部コンピューティングへの接続、ソース管理の構成、およびトリガーの設定を管理できます。 詳細については、[管理ハブ](author-management-hub.md)の機能に関するページを参照してください。

![リンクされたサービスの管理](media/author-management-hub/management-hub-linked-services.png)

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

## <a name="next-steps"></a>次のステップ

パイプラインの監視と管理について詳しくは、[プログラムでのパイプラインの監視と管理](monitor-programmatically.md)に関する記事をご覧ください。
