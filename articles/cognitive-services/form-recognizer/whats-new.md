---
title: Form Recognizer の新機能
titleSuffix: Azure Cognitive Services
description: Form Recognizer API の最新の変更点を把握します。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: pafarley
ms.openlocfilehash: ddd1f61ada539ebb00341dd83919f1c851a0f3e1
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816373"
---
# <a name="whats-new-in-form-recognizer"></a>Form Recognizer の新機能

Form Recognizer サービスは、継続的に更新されます。 この記事で、機能の強化、修正、ドキュメントの更新についての最新情報を入手してください。

## <a name="august-2020"></a>2020 年 8 月

### <a name="new-features"></a>新機能

**Form Recognizer v2.1 パブリック プレビューが利用可能になりました。** V2.1-preview.1 がリリースされました。次の機能を備えています。 


- **REST API リファレンスが利用可能** - [v2.1-preview.1 リファレンス](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync)を参照してください 
- **英語の他に新たに言語をサポート**。次の[言語](language-support.md)がサポートされるようになりました。`Layout` および `Train Custom Model` の場合: 英語 (`en`)、簡体中国語 (`zh-Hans`)、オランダ語 (`nl`)、フランス語 (`fr`)、ドイツ語 (`de`)、イタリア語 (`it`)、ポルトガル語 (`pt`)、およびスペイン語 (`es`)。
- **チェックボックス、選択マークの検出** – Form Recognizer では、チェックボックスやオプション ボタンなどの選択マークの検出および抽出がサポートされています。 選択マークは `Layout` で抽出されます。また、`Train Custom Model`_ - [ラベルを使用したトレーニング]_ でラベル付けおよびトレーニングを行って、選択マーク用のキーと値のペアを抽出することもできるようになりました。 
- **[モデルの作成]** を使用すると、1 つのモデル ID で複数のモデルを構成して呼び出すことができます。 作成済みのモデル ID を使用して分析するドキュメントが送信されると、分類ステップが最初に実行され、適切なカスタム モデルにルーティングされます。 モデルの作成は、`Train Custom Model` -  _[ラベルを使用したトレーニング]_ で使用できます。
- **モデル名**。管理と追跡が容易になるように、カスタム モデルにわかりやすい名前を追加します。
- **[名刺用の新しい事前構築されたモデル](concept-business-cards.md)** 。英語の名刺内の共通のフィールドを抽出するためのものです。
- **[事前に作成された領収書の新しいロケール](concept-receipts.md)** 。EN-US に加えて、EN-AU、EN-CA、EN-GB、EN-IN でもサポートが利用できるようになりました。
- **品質向上**。`Layout`、`Train Custom Model` -  _[ラベルを使用しないトレーニング]_ 、および _[ラベルを使用したトレーニング]_ が対象となります。


**v2.0** には、次の更新が含まれています。
-   NET、Python、Java、および JavaScript 用の[クライアント ライブラリ](quickstarts/client-library.md)が一般提供になりました。 


**新しいサンプル**は GitHub で入手できます。 
- [知識抽出レシピ - Forms Playbook](https://github.com/microsoft/knowledge-extraction-recipes-forms) では、Form Recognizer の実際のカスタマー エンゲージメントからベスト プラクティスが収集され、これらのプロジェクトの開発時に使用する便利なコード サンプル、チェックリスト、およびサンプル パイプラインが提供されます。 
- [サンプルラベル付けツール](https://github.com/microsoft/OCR-Form-Tools) が、v2.1 の新機能をサポートするように更新されました。 ツールの概要については、こちらの[クイックスタート](quickstarts/label-tool.md)を参照してください。 
- [インテリジェント キオスク](https://github.com/microsoft/Cognitive-Samples-IntelligentKiosk/blob/master/Documentation/FormRecognizer.md) Form Recognizer サンプルでは、`Analyze Receipt` と `Train Custom Model` -  _[ラベルを使用しないトレーニング]_ を統合する方法について示します。



## <a name="july-2020"></a>2020 年 7 月

### <a name="new-features"></a>新機能

* **v2.0 リファレンスが使用可能** [v2.0 API リファレンス](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)と、[.NET](https://docs.microsoft.com/dotnet/api/overview/azure/ai.formrecognizer-readme-pre?view=azure-dotnet)、[Python](https://docs.microsoft.com/python/api/overview/azure/?view=azure-python)、[Java](https://docs.microsoft.com/java/api/overview/azure/ai-formrecognizer-readme-pre?view=azure-java-preview)、および [JavaScript](https://docs.microsoft.com/javascript/api/overview/azure/?view=azure-node-latest) 用のアップデートされた SDK を参照できます。
* **テーブルの機能強化と抽出の機能強化**には、精度の向上とテーブル抽出の機能強化が含まれています。特に、"_ラベルのないカスタム トレーニング_" でテーブルのヘッダーと構造を学習する機能が追加されています。 

* **通貨のサポート** グローバル通貨記号の検出と抽出。
* **Azure Gov** Form Recognizer が、Azure Gov でも使用できるようになりました。
* **強化されたセキュリティ機能**: 
   * **Bring Your Own Key** Form Recognizer は、クラウドに永続化されたときにデータを自動的に暗号化して保護し、セキュリティとコンプライアンスに関する組織のコミットメントを満たすのに役立つようにします。 サブスクリプションでは、Microsoft が管理する暗号化キーが既定で使用されます。 ユーザー独自の暗号化キーを使用してサブスクリプションを管理することもできるようになりました。 [カスタマー マネージド キー (CMK、Bring Your Own Key (BYOK) とも呼ばれます)](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/form-recognizer-encryption-of-data-at-rest) を使用すると、アクセス制御の作成、ローテーション、無効化、取り消しを、いっそう柔軟に行うことができます。 また、データを保護するために使われる暗号化キーを監査することもできます。  
   * **プライベート エンドポイント** – 仮想ネットワーク (VNet) を使用して、[Private Link を介してデータに安全にアクセスできるようになります。](https://docs.microsoft.com/azure/private-link/private-link-overview)


## <a name="june-2020"></a>2020 年 6 月

### <a name="new-features"></a>新機能
* **クライアント SDK への CopyModel API の追加** クライアント SDK を使用して、サブスクリプション間でモデルをコピーできるようになりました。 この機能の全般的な情報については、[モデルのバックアップと復旧](./disaster-recovery.md)に関する記事を参照してください。
* **Azure Active Directory の統合**。Azure AD の資格情報を使用して、SDK で Form Recognizer クライアント オブジェクトを認証できるようになりました。
* **SDK 固有の変更** これには、小さな機能追加と破壊的な変更の両方が含まれます。 詳細については、SDK の変更ログを参照してください。
  * [C# SDK プレビュー 3 変更ログ](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/formrecognizer/Azure.AI.FormRecognizer/CHANGELOG.md)
  * [Python SDK プレビュー 3 変更ログ](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Java SDK プレビュー 3 変更ログ](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [JavaScript SDK プレビュー 3 変更ログ](https://github.com/Azure/azure-sdk-for-js/blob/%40azure/ai-form-recognizer_1.0.0-preview.3/sdk/formrecognizer/ai-form-recognizer/CHANGELOG.md)

## <a name="april-2020"></a>2020 年 4 月

### <a name="new-features"></a>新機能
* **Form Recognizer API v2.0 パブリック プレビューの SDK サポート** 今月、サービス サポートが拡張され、Form Recognizer v2.0 (プレビュー) リリースのプレビュー SDK が含まれるようになりました。 以下のリンクを使用して、任意の言語で作業を開始してください。 
   * [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/ai.formrecognizer-readme-pre?view=azure-dotnet)
   * [Java SDK](https://docs.microsoft.com/java/api/overview/azure/ai-formrecognizer-readme-pre?view=azure-java-preview)
   * [Python SDK](https://docs.microsoft.com/python/api/overview/azure/ai-formrecognizer-readme-pre?view=azure-python-preview)
   * [JavaScript SDK](https://docs.microsoft.com/javascript/api/overview/azure/ai-form-recognizer-readme-pre?view=azure-node-preview)

  新しい SDK では、Form Recognizer 用の v2.0 REST API のすべての機能がサポートされています。 たとえば、モデルをラベルの有無にかかわらずトレーニングしたり、フォームからテキスト、キーと値のペア、およびテーブルを抽出したり、事前構築済みの領収書サービスを使用してレシートからデータを抽出したり、レイアウト サービスを使用してドキュメントからテキストやテーブルを抽出したりすることができます。 SDK に関するフィードバックは、[SDK フィードバック フォーム](https://aka.ms/FR_SDK_v1_feedback)を通じて共有できます。
 
* **カスタム モデルのコピー** 新しいカスタム モデルのコピー機能を使用して、リージョン間やサブスクリプション間でモデルをコピーできるようになりました。 Copy Custom Model API を呼び出す前に、ターゲット リソース エンドポイントに対してコピー承認操作を呼び出して、ターゲット リソースにコピーするための承認を取得する必要があります。
   * [コピー承認を生成する](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModelAuthorization) REST API
   * [カスタム モデルをコピーする](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModel) REST API 

### <a name="security-improvements"></a>セキュリティの強化

* FormRecognizer でカスタマー マネージド キーが利用できるようになりました。 詳細については、[Form Recognizer による保存データの暗号化](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/form-recognizer-encryption-of-data-at-rest)に関するページを参照してください。
* Azure Active Directory を使用した Azure リソースへのアクセスにマネージド ID を使用します。 詳細については、[マネージド ID に対するアクセスの承認](https://docs.microsoft.com/azure/cognitive-services/authentication#authorize-access-to-managed-identities)に関するページを参照してください。

## <a name="march-2020"></a>2020 年 3 月 

### <a name="new-features"></a>新機能

* **ラベル付けの値の種類** Form Recognizer サンプル ラベル付けツールを使用して、ラベル付けする値の種類を指定できるようになりました。 現在、次の値の型とバリエーションがサポートされています。
  * `string`
    * 既定値、`no-whitespaces`、`alphanumeric`
  * `number`
    * 既定値、`currency`
  * `date` 
    * 既定値、`dmy`、`mdy`、`ymd`
  * `time`
  * `integer`

  この機能の使用方法については、[サンプル ラベル付けツール](./quickstarts/label-tool.md#specify-tag-value-types) ガイドを参照してください。


* **テーブルの視覚化** サンプル ラベル付けツールで、ドキュメントで認識されたテーブルが表示されるようになりました。 これにより、ラベル付けおよび分析を行う前に、ドキュメントから認識されて抽出されたテーブルを表示できます。 この機能は、レイヤー オプションを使用してオンとオフを切り替えることができます。

  テーブルが認識されて抽出される方法の例を次に示します。

  > [!div class="mx-imgBorder"]
  > ![サンプル ラベル付けツールを使用したテーブルの視覚化](./media/whats-new/formre-table-viz.png)

    抽出されたテーブルは、`"pageResults"` の JSON 出力で使用できます。

  > [!IMPORTANT]
  > テーブルのラベル付けはサポートされていません。 テーブルの認識と抽出が自動的に行われない場合は、キーと値のペアとしてのみラベルを付けることができます。 キーと値のペアとしてテーブルのラベル付けを行うときは、各セルに固有の値としてラベルを付けます。

### <a name="extraction-enhancements"></a>抽出の機能強化

このリリースでは、抽出の機能強化と精度の向上が行われています。具体的には、テキストの同じ行に複数のキーと値のペアが含まれる場合の、ラベル付けと抽出の機能です。 
 
### <a name="sample-labeling-tool-is-now-open-source"></a>サンプル ラベル付けツールがオープンソースになりました

Form Recognizer のサンプル ラベル付けツールが、オープンソース プロジェクトとして使用できるようになりました。 ソリューションにそれを統合し、ニーズに合わせて顧客固有の変更を行うことができます。

Form Recognizer のサンプル ラベル付けツールの詳細については、[GitHub](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md) で入手できるドキュメントを参照してください。

### <a name="tls-12-enforcement"></a>TLS 1.2 の適用

TLS 1.2 は現在、このサービスへのすべての HTTP 要求に適用されるようになりました。 詳細については、[Azure Cognitive Services のセキュリティ](../cognitive-services-security.md)に関するページを参照してください。

## <a name="january-2020"></a>2020 年 1 月

このリリースでは、Form Recognizer 2.0 (プレビュー) が導入されています。 以下のセクションでは、新機能、機能強化、および変更の詳細について説明します。 

### <a name="new-features"></a>新機能

* **カスタム モデル**
  * **ラベルを使用したトレーニング** 手動でラベル付けされたデータを使用して、カスタム モデルをトレーニングできるようになりました。 これにより、性能の高いモデルが得られ、複雑なフォームやキーのない値を含んだフォームでも機能するモデルを作成することが可能です。
  * **非同期 API** 非同期 API 呼び出しを使用して、大規模なデータ セットおよびファイルをトレーニングして分析できます。
  * **TIFF ファイルのサポート** TIFF ドキュメントからのデータをトレーニングして、抽出できるようになりました。
  * **抽出精度の向上**

* **構築済みのレシート モデル**
  * **チップの金額** チップの金額とその他の手書きの値を抽出できるようになりました。
  * **品目の抽出** レシートから品目の値を抽出できます。
  * **信頼値** 抽出された各値のモデルの信頼度を表示できます。
  * **抽出精度の向上**

* **レイアウトの抽出** レイアウト API を使用して、フォームからテキスト データとテーブル データを抽出できるようになりました。

### <a name="custom-model-api-changes"></a>カスタム モデル API の変更点

カスタム モデルのトレーニングと使用のための API 名がすべて変更され、一部の同期メソッドは非同期になりました。 主な変更点は次のとおりです。

* モデルのトレーニング プロセスが非同期になりました。 **/custom/models** API 呼び出し経由で、トレーニングを開始します。 この呼び出しでは、操作 ID が返されます。この ID を **custom/models/{modelID}** に渡すと、トレーニング結果を返すことができます。
* キー/値の抽出は、 **/custom/models/{modelID}/analyze** API 呼び出しによって開始されるようになりました。 この呼び出しでは操作 ID が返され、それを **custom/models/{modelID}/analyzeResults/{resultID}** に渡すと、抽出結果を返すことができます。
* トレーニング操作の操作 ID は、**Operation-Location** ヘッダーではなく、HTTP 応答の **Location** ヘッダー内に配置されるようになりました。

### <a name="receipt-api-changes"></a>レシート API の変更点

販売レシートを読み取る API の名前が変更されました。

* レシート データの抽出は、 **/prebuilt/receipt/analyze** API 呼び出しによって開始されるようになりました。 この呼び出しでは操作 ID が返され、それを **/prebuilt/receipt/analyzeResults/{resultID}** に渡すと、抽出結果を返すことができます。

### <a name="output-format-changes"></a>出力形式の変更点

すべての API 呼び出しへの JSON 応答には、新しい形式が用意されています。 一部のキーと値は、追加、削除、または名前の変更が行われています。 現在の JSON 形式の例については、クイックスタートを参照してください。

## <a name="next-steps"></a>次のステップ

[クイック スタート](quickstarts/curl-train-extract.md)をすべて終え、[Form Recognizer API シリーズ](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)の使用を開始します。
