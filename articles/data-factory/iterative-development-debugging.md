---
title: Azure Data Factory での反復開発とデバッグ
description: ADF UX で Data Factory パイプラインの開発とデバッグを反復して実行する方法について説明します
ms.date: 02/23/2021
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
author: kromerm
ms.author: makromer
ms.openlocfilehash: ef47d311f5f096db962ea27792e7871dbf0ef81a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101712965"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Azure Data Factory での反復開発とデバッグ
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory を使用すると、データ統合ソリューションを開発するときに、Data Factory パイプラインを反復的に開発およびデバッグできます。 これらの機能を使用すると、行った変更をテストした後で、pull request を作成したり、変更をデータ ファクトリ サービスに公開したりできます。 

この機能の概要とデモンストレーションについては、以下の 8 分間の動画を視聴してください。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="debugging-a-pipeline"></a>パイプラインのデバッグ

パイプライン キャンバスを使用して作成するときは、**デバッグ** 機能を使用してアクティビティをテストできます。 テストの実行を行うときは、**[デバッグ]** を選択する前に、Data Factory に変更を発行する必要はありません。 この機能は、Data Factory ワークフローを更新する前に、変更が期待どおりに動作することを確認する場合に便利です。

![パイプライン キャンバスのデバッグ機能](media/iterative-development-debugging/iterative-development-1.png)

パイプラインが実行中の間は、パイプライン キャンバスの **[出力]** タブで各アクティビティの結果を確認できます。

テストの実行結果は、パイプライン キャンバスの **出力** ウィンドウに表示されます。

![パイプライン キャンバスの出力ウィンドウ](media/iterative-development-debugging/iterative-development-2.png)

テストの実行が成功したら、パイプラインにさらにアクティビティを追加し、反復的な方法でデバッグを続行します。 テストの実行中に、実行を **[キャンセル]** することもできます。

> [!IMPORTANT]
> **[デバッグ]** を選択すると、パイプラインが実際に実行されます。 たとえばパイプラインにコピー アクティビティが含まれていれば、テストの実行では、データがコピー元からコピー先にコピーされます。 その結果、デバッグ時のコピー アクティビティとその他のアクティビティでは、テスト フォルダーを使用することをお勧めします。 パイプラインのデバッグが終わったら、通常の操作で使用する実際のフォルダーに切り替えます。

### <a name="setting-breakpoints"></a>ブレークポイントの設定

Azure Data Factory を使用すると、パイプライン キャンバスの特定のアクティビティに到達するまで、パイプラインをデバッグできます。 アクティビティにテストの終了点となるブレークポイントを設定し、 **[デバッグ]** を選択します。 Data Factory は、パイプライン キャンバスでブレークポイント アクティビティに達するまで、テストが実行されることを保証します。 この *特定の場所までデバッグする* 機能は、パイプライン全体ではなく、パイプライン内のアクティビティのサブセットのみをテストする場合に便利です。

![パイプライン キャンバス上のブレークポイント](media/iterative-development-debugging/iterative-development-3.png)

ブレークポイントを設定するには、パイプライン キャンバス上で要素を選択します。 *[Debug Until]\(特定の場所までデバッグする\)* オプションは、要素の右上隅に空の赤い円として表示されます。

![選択した要素にブレークポイントを設定する前](media/iterative-development-debugging/iterative-development-4.png)

*[Debug Until]\(特定の場所までデバッグする\)* オプションを選択した後は、赤い円が塗りつぶされ、ブレークポイントが有効になったことが示されます。

![選択した要素にブレークポイントを設定した後](media/iterative-development-debugging/iterative-development-5.png)

## <a name="monitoring-debug-runs"></a>デバッグ実行の監視

パイプラインのデバッグ実行を実行すると、パイプライン キャンバスの **[出力]** ウィンドウに結果が表示されます。 出力タブには、現在のブラウザー セッション中に行われた最新の実行のみが含まれます。 

![パイプライン キャンバスの出力ウィンドウ](media/iterative-development-debugging/iterative-development-2.png)

デバッグ実行の履歴ビューを表示したり、すべてのアクティブなデバッグ実行の一覧を表示したりするには、 **[監視]** エクスペリエンスにアクセスします。 

![[View active debug runs] アイコンを選択](media/iterative-development-debugging/view-debug-runs.png)

> [!NOTE]
> Azure Data Factory サービスでは、デバッグの実行履歴が 15 日間のみ保持されます。 

## <a name="debugging-mapping-data-flows"></a>マッピング データ フローのデバッグ

マッピング データ フローを使用すると、大規模に実行されるコーディング不要のデータ変換ロジックを作成できます。 ロジックを構築する際に、デバッグ セッションをオンにして、ライブ Spark クラスターを使用してインタラクティブにデータを操作することができます。 詳細については、「[マッピング データ フローのデバッグ モード](concepts-data-flow-debug-mode.md)」を参照してください。

**[監視]** エクスペリエンスで、ファクトリ全体のアクティブなデータ フロー デバッグ セッションを監視できます。

![データ フロー デバッグ セッションの表示](media/iterative-development-debugging/view-dataflow-debug-sessions.png)

データ フロー デザイナーのデータ プレビューとデータ フローのパイプライン デバッグは、小規模なデータ サンプルを使った場合に最適に機能するよう設計されています。 ただし、パイプラインまたはデータ フローのロジックを大量のデータに対してテストする必要がある場合は、デバッグ セッションで使用される Azure Integration Runtime のサイズを増やしてください (コア数を増やし、汎用的な計算を最小限に減らしてください)。
 
### <a name="debugging-a-pipeline-with-a-data-flow-activity"></a>データ フロー アクティビティが含まれるパイプラインのデバッグ

データ フローが含まれるパイプライン実行のデバッグを行うときに使用するコンピューティングには、2 つのオプションがあります。 既存のデバッグ クラスターを使用することも、データ フローに対して新しい Just-In-Time クラスターを作成することもできます。

既存のデバッグ セッションを使用すると、クラスターが既に稼働中であるためデータ フローの起動時間が大幅に短縮されますが、複数のジョブを一度に実行するときに失敗する場合があるため、複雑なワークロードや並列ワークロードには推奨されません。

アクティビティ ランタイムを使用すると、各データ フロー アクティビティの統合ランタイムで指定された設定を使用して新しいクラスターが作成されます。 これにより、各ジョブを分離することができるため、複雑なワークロードやパフォーマンス テストにはこれを使用する必要があります。 デバッグに使用されるクラスター リソースが、その期間内に追加のジョブ要求の処理のためにも使用できるように、Azure IR で TTL を制御することもできます。

> [!NOTE]
> データ フローが並列で実行されるパイプラインがある場合や、大きなデータセットを使ってテストしなければならないデータ フローがある場合は、データ フロー アクティビティで選択した Integration Runtime を Data Factory で使用できるように、[アクティビティランタイムを使用する] を選択します。 これで、データ フローを複数のクラスターで実行できるようになり、データ フローの並列実行に対応できます。

![データフローを使用したパイプラインの実行](media/iterative-development-debugging/iterative-development-dataflow.png)

## <a name="next-steps"></a>次のステップ

変更をテストした後、「[Azure Data Factory における継続的インテグレーションとデプロイ](continuous-integration-deployment.md)」を使用して、より上位の環境に昇格させます。
