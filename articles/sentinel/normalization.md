---
title: Azure Sentinel のデータの正規化 | Microsoft Docs
description: この記事では、Azure Sentinel でさまざまなソースからのデータを正規化する方法について説明し、正規化スキーマの詳細を説明します。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: yelevin
ms.openlocfilehash: 5d847ac7ed805ad88bc24ed63896edc6f7596f9b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101729778"
---
# <a name="normalization-in-azure-sentinel"></a>Azure Sentinel での正規化

この記事では、Azure Sentinel でのデータ スキーマの正規化、および具体的には、リンクが含まれるネットワーク接続とセッション スキーマについて説明します。

## <a name="what-is-normalization"></a>正規化とは

さまざまなデータ型とテーブルを一緒に操作することには、課題が発生します。 さまざまなデータ型とスキーマの理解を深める必要があり、特徴を共有する (たとえば、ファイアウォール デバイスに関連する) ものであっても、個々に分析ルール、ブック、およびハンティング クエリの独自のセットを記述して使用する必要があります。 調査やハンティングに必要な、さまざまなデータ型の相関関係も難しくなります。 Azure Sentinel では、統一の正規化されたビューでさまざまなソースからのデータを処理するためのシームレスなエクスペリエンスが提供されます。

Azure Sentinel の **Common Information Model** は、次の 3 つの側面で構成されています。

- **正規化されたスキーマ** では、簡単に使用して、統一された機能を構築できる予測可能なイベントの種類 (テーブル) の一般的なセットがカバーされています。 さらに、このスキーマには、正規化された列の規則と、値と形式の標準化 (IP アドレスなどのデータの標準の一貫性のある表現) の定義も含まれます。

- **パーサー** は、さまざまな種類の既存のデータを正規化されたスキーマにマップします。 モデルに従って、クエリ時 (関数を使用) またはインジェスト時に、データを正規化されたスキーマに解析できます。 現時点では、クエリ時の解析のみがサポートされています。

- **各正規化されたスキーマの内容** には、分析ルール、対話型ブック、ハンティング クエリ、および追加の内容が含まれます。

### <a name="current-release"></a>現在のリリース

このリリースでは、 [正規化されたネットワーク接続とセッション スキーマ](./normalization-schema.md) (v 1.0.0) をパブリック プレビューで使用できます。 スキーマは、ファイアウォール、ワイヤー データ、NSG、Netflow、プロキシ システム、Web セキュリティ ゲートウェイによってログに記録されたものなど、ネットワーク接続やセッションについて記述されたものです。  選択したクエリ時パーサーと分析ルールを、スキーマと共に使用できます。

現在、スキーマはクエリ時パーサーを介してのみ使用できます (パーサーのセクションを参照してください)。

データを追加の表現に解析し、[OSSEM エンティティ名前付けモデル](https://ossemproject.com/cdm/entities/intro.html#)を使用して、既存および将来の正規化されたテーブルと整合性のある列を作成することができます。

## <a name="normalized-schema-and-parsing"></a>正規化されたスキーマと解析

### <a name="how-our-normalized-schemas-are-defined"></a>正規化されたスキーマを定義する方法

Azure Sentinel は、[Open Source Security Events Metadata (OSSEM)](https://ossemproject.com/intro.html) Common Information Model と連携して、正規化されたテーブル間での予測可能なエンティティの相関関係を実現します。 OSSEM は、多様なデータ ソースとオペレーティング システムからのセキュリティ イベント ログのドキュメントと標準化に主に重点を置いたコミュニティ主導のプロジェクトです。 さらに、このプロジェクトでは、データ エンジニアがデータの正規化手順時に使用できる Common Information Model (CIM) を提供しているため、セキュリティ アナリストが多様なデータソースで、データをクエリし、分析できます。

[OSSEM CIM](https://ossemproject.com/cdm/intro.html) は、一連のエンティティ (例: ファイル、ホスト、IP、プロセス) を定義し、そのようなエンティティごとに属性のセットを定義します。 また、CIM は、これらのエンティティの関連属性を使用する一連のテーブル ([ネットワーク セッション](https://ossemproject.com/cdm/tables/network_session.html) テーブルなど) を定義し、シームレスで予測可能な相関関係を実現します。 エンティティは入れ子にできます (たとえば、ソース エンティティに、名前属性を持つファイル エンティティを含めることができます)。

OSSEM エンティティ構造の詳細については、[公式 OSSEM リファレンス](https://ossemproject.com/cdm/guidelines/entity_structure.html)を参照してください。

### <a name="how-the-normalized-schemas-are-implemented-in-azure-sentinel"></a>Azure Sentinel で正規化されたスキーマを実装する方法

Azure Sentinel の OSSEM CIM の実装では、OSSEM 表現を Log Analytics 表形式表現に射影しています。この表現が組み込みテーブルであるか、または既存のデータをこの表現にマップするクエリ時パーサーや関数を使用して作成されたかに関係ありません。 表形式表現の場合、OSSEM エンティティ名と属性名を連結し、それらをまとめて 1 つの列名にマップします。 たとえば、md5 属性を含むハッシュ エンティティを含むファイル エンティティを含むソース エンティティは、次の Log Analytics 列として実装されます。SrcFileHashMd5。 (OSSEM では、既定で *snake_case* が使用されますが、Azure Sentinel と Log Analytics では *PascalCase* が使用されます。 OSSEM では、それらの列は src_file_hash_md5 になります)。

Azure Sentinel のお客様に固有の Log Analytics プラットフォーム要件とユース ケースのため、Azure Sentinel 実装に追加のカスタム フィールドが存在する場合があります。

### <a name="schema-reference"></a>スキーマ参照

詳細については、[スキーマ リファレンス ドキュメント](./normalization-schema.md)のほか、公式の [OSSEM プロジェクト ドキュメント](https://ossemproject.com/cdm/intro.html)を参照してください。

スキーマ参照には、値と形式の標準化も含まれています。 元の、または解析済みのソース フィールドは、標準形式でないか、またはスキーマ標準の値のリストを使用していない可能性があるため、完全に正規化するには、スキーマ標準表現に変換する必要がある場合があります。

## <a name="parsers"></a>パーサー

- [解析とは](#what-is-parsing)
- [クエリ時パーサーの使用](#using-query-time-parsers)

### <a name="what-is-parsing"></a>解析とは

定義済みの正規化されたテーブルの基本セットが提供されているので、データをそれらのテーブルに変換 (解析/マップ) する必要があります。 つまり、特定のデータを、その生の形式から、正規化されたスキーマの既知の列に抽出します。 Azure Sentinel の解析は **クエリ時** に行われます。パーサーは、既存のテーブル (CommonSecurityLog、カスタム ログ テーブル、syslog など) のデータを、正規化されたテーブル スキーマに変換する Log Analytics ユーザー関数 (Kusto Query Language (KQL) を使用して) として構築されています。

Azure Sentinel でまだサポートされていない他の種類の解析は、**インジェスト時** に行われます。データをそのデータ ソースから取り込まれると同時に、正規化されたテーブルに直接収集できます。 インジェスト時の解析では、関数を使用する必要なくデータ モデルが直接クエリされるため、パフォーマンスが向上します。

### <a name="using-query-time-parsers"></a>クエリ時パーサーの使用

- [パーサーのインストール](#installing-a-parser)
- [パーサーの使用](#using-the-parsers)
- [パーサーのカスタマイズ](#customizing-parsers)

#### <a name="installing-a-parser"></a>パーサーのインストール

使用可能なクエリ時パーサーは、Azure Sentinel [公式 GitHub リポジトリ](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/Normalized%20Schema%20-%20Networking%20(v1.0.0))で入手できます。 各パーサーはバージョン管理されているため、ユーザーが将来の更新を簡単に使用し、監視できます。 パーサーをインストールするには:

1. 上記の GitHub リンクにある関連する各 KQL ファイルから、関連するパーサー コンテンツをクリップボードにコピーします

1. Azure Sentinel ポータルで [ログ] ページを開き、KQL ファイルの内容をログ画面に貼り付けて、 **[保存]** をクリックします。

    :::image type="content" source="./media/normalization/install-new-parser.png" alt-text="新しいパーサーをインストールする":::

1. [保存] ダイアログで、次のようにフィールドに入力します。
    1. **Name**: **[関数のエイリアス]** フィールドで使用されている同じ値を使用することが推奨されます (上記の例では、*CheckPoint_Network_NormalizedParser*)
    
    1. **名前を付けて保存**: **[関数]** を選択します

    1. **関数のエイリアス**: *PRODUCT_Network_NormalizedParser* の名前付け規則で名前を付ける必要があります (上記の例では、*CheckPoint_Network_NormalizedParser*)。

    1. **カテゴリ**: 既存のカテゴリを選択するか、新しいカテゴリ (*Normalizednetworksessionsparc* など) を作成できます
    
        :::image type="content" source="./media/normalization/save-new-parser.png" alt-text="パーサーを保存する":::

パーサーを適切に使用するには、Empty ネットワーク スキーマ パーサー (これにより、すべてのネットワーク セッション スキーマのフィールドの空の表形式ビューが作成されます) と Network メタパーサー (これにより、すべての有効なパーサーを結合して、ネットワーク スキーマのさまざまなソースからのデータの単一ビューが作成されます) もインストールする必要があります。 これらの 2 つのパーサーのインストールは、前述の手順と同様の方法で行います。

クエリ関数の保存時に、新しい関数を反映させるために、クエリ エクスプローラーを閉じてから再度開く必要がある場合があります。

#### <a name="using-the-parsers"></a>パーサーの使用

有効にすると、メタパーサーを使用して、現在有効なすべてのパーサーの統一されたビューに対してクエリを実行できます。 これを行うには、Azure Sentinel ログ ページにアクセスし、メタパーサーに対してクエリを実行します。

:::image type="content" source="./media/normalization/query-parser.png" alt-text="パーサーのクエリ":::
 
[クエリ エクスプローラー] をクリックして、ログ ページのクエリ エクスプローラーを使用して、メタパーサーや個々のパーサーにアクセスすることもできます。

:::image type="content" source="./media/normalization/query-explorer.png" alt-text="クエリ エクスプローラー":::

右側のウィンドウで、[保存されたクエリ] セクションを展開し、'NormalizedNetworkParsers' フォルダー (または、パーサーの作成時に選択したカテゴリ名) を見つけます。

:::image type="content" source="./media/normalization/find-parser.png" alt-text="パーサーの検索":::

個々のパーサーをクリックして、そこで使用されている基になる関数を確認し、実行できます (前述のように、そのエイリアスによって直接アクセスすることもできます)。 パーサーによっては、利便性のため、元のフィールドと正規化されたフィールドを横に並べて保持できます。 これは、パーサーのクエリで簡単に編集できます。

> [!TIP]
> ハンティングや検出クエリなど、どのクエリでも、Azure Sentinel テーブルの代わりに保存した関数を使用できます。 詳細については、次を参照してください。
>
> - [Azure Sentinel のデータの正規化](normalization.md#parsers)
> - [Azure Monitor ログのテキストの解析](../azure-monitor/logs/parse-text.md)
>
#### <a name="customizing-parsers"></a>パーサーのカスタマイズ

上記の手順 (クエリ エクスプローラーでのパーサーの検索) を繰り返すことができます。関連パーサーをクリックし、その関数の実装を確認します。
たとえば、メタパーサーを編集して、個々のパーサーを追加または削除できます。

:::image type="content" source="./media/normalization/customize-parser.png" alt-text="パーサーのカスタマイズ":::
 
関数を変更したら、[保存] を再度クリックし、同じ名前、エイリアス、およびカテゴリを使用します。 上書きダイアログが開きます。[OK] をクリックします。

:::image type="content" source="./media/normalization/are-you-sure.png" alt-text="確認":::

#### <a name="additional-information"></a>関連情報

JSON、XML、CSV は、クエリ時の解析に特に便利です。 Azure Sentinel には、JSON、XML、CSV 用の解析関数のほか、JSON 解析ツールが組み込まれています。  詳細については、[Azure Sentinel での JSON フィールドの使用](https://techcommunity.microsoft.com/t5/azure-sentinel/tip-easily-use-json-fields-in-sentinel/ba-p/768747)に関するページ (ブログ) を参照してください。 

Log Analytics の[保存されたクエリ](../azure-monitor/logs/example-queries.md) (クエリ時パーサーの実装) の詳細を確認してください。


## <a name="next-steps"></a>次のステップ

このドキュメントでは、Azure Sentinel の正規化スキーマについて説明しました。 参照スキーマ自体については、[Azure Sentinel のデータ正規化スキーマのリファレンス](./normalization-schema.md)を参照してください。

* [Azure Sentinel ブログ](https://aka.ms/azuresentinelblog)。 Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。