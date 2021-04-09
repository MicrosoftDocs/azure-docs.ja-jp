---
title: Speech Services のクォータと制限
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Speech Services のクォータと制限に関するクイック リファレンス、詳細な説明、およびベストプラクティス
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: alexeyo
ms.openlocfilehash: 78e40250710c133cbed53c05137971247663b0df
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103564424"
---
# <a name="speech-services-quotas-and-limits"></a>Speech Services のクォータと制限

この記事には、Azure Cognitive Speech Services のクォータとすべての [価格レベル](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)の制限に関するクイック リファレンスおよび **詳細な説明** が記載されています。 また、要求のスロットリングを回避するためのベスト プラクティスについても説明します。 

## <a name="quotas-and-limits-quick-reference"></a>クォータと制限のクイック リファレンス
[テキスト読み上げのクォータと制限](#text-to-speech-quotas-and-limits-per-speech-resource)へ移動する
### <a name="speech-to-text-quotas-and-limits-per-speech-resource"></a>Speech リソースごとの音声テキスト変換のクォータと制限
次の表の "調整可能" 行のないパラメーターは、すべての価格レベルで調整可能 **ではありません**。

#### <a name="online-transcription"></a>オンライン文字起こし
[Speech SDK](speech-sdk.md) と [Speech-to-text REST API for short audio](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) の両方または一方で使用する場合。

| Quota | Free (F0)<sup>1</sup> | Standard (S0) |
|--|--|--|
| **同時要求の制限 - 基本モデル** | 1 | 100 (既定値) |
| 調整可能 | いいえ<sup>2</sup> | はい<sup>2</sup> |
| **同時要求の制限 - カスタム モデル** | 1 | 20 (既定値) |
| 調整可能 | いいえ<sup>2</sup> | はい<sup>2</sup> |

#### <a name="batch-transcription"></a>バッチ文字起こし
| Quota | Free (F0)<sup>1</sup> | Standard (S0) |
|--|--|--|
| Rest API の制限 | バッチ文字起こしは、F0 では使用できません | 1 分あたり 300 要求 |
| オーディオ入力ファイルの最大サイズ | 該当なし | 1 GB |
| 入力 BLOB の最大サイズ (zip アーカイブなどに複数のファイルを含めることができます。上記のファイル サイズの制限に注意してください) | 該当なし | 2.5 GB |
| BLOB コンテナーの最大サイズ | 該当なし | 5 GB |
| コンテナーごとの BLOB の最大数 | 該当なし | 10000 |
| 文字起こし要求あたりの最大ファイル数 (入力として複数のコンテンツ URL を使用する場合) | 該当なし | 1000  |
| 同時実行されるジョブの最大数 | 該当なし | 2000  |

#### <a name="model-customization"></a>モデルのカスタマイズ
| Quota | Free (F0)<sup>1</sup> | Standard (S0) |
|--|--|--|
| Rest API の制限 | 1 分あたり 300 要求 | 1 分あたり 300 要求 |
| 音声データセットの最大数 | 2 | 500 |
| データ インポートの最大音響データセット ファイル サイズ | 2 GB | 2 GB |
| データ インポートの最大言語データセット ファイル サイズ | 200 MB | 1.5 GB |
| データ インポートの最大発音データセット ファイル サイズ | 1 KB | 1 MB |
| [モデルの作成](https://westcentralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CreateModel/) API 要求で `text` パラメーターを使用する場合のテキストの最大サイズ | 200 KB | 500 KB |

<sup>1</sup> **Free (F0)** 価格レベルについては、[価格ページ](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)で月額料金に関するページを参照してください。<br/>
<sup>2</sup> [その他の説明](#detailed-description-quota-adjustment-and-best-practices)、[ベスト プラクティス](#general-best-practices-to-mitigate-throttling-during-autoscaling)、および[調整手順](#speech-to-text-increasing-online-transcription-concurrent-request-limit)に関するセクションを参照してください。<br/> 

### <a name="text-to-speech-quotas-and-limits-per-speech-resource"></a>Speech リソースごとのテキスト読み上げのクォータと制限
次の表の "調整可能" 行のないパラメーターは、すべての価格レベルで調整可能 **ではありません**。

| Quota | Free (F0)<sup>3</sup> | Standard (S0) |
|--|--|--|
| **標準およびニューラル音声の 1 秒あたりのトランザクションの最大数 (TPS)** | 200<sup>4</sup> | 200<sup>4</sup> |  |
| **Custom Voice の同時要求の上限** |  |  |
| 既定値 | 10 | 10 |
| 調整可能 | いいえ<sup>5</sup> | ○<sup>5</sup> |
| **HTTP 固有のクォータ** |  |
| 要求ごとに生成されるオーディオの最大長 | 10 分 | 10 分 |
| SSML 内の個別の `<voice>` タグの最大数 | 50 | 50 |
| **Websocket 固有のクォータ** |  |  |
|1 ターンあたりに生成されるオーディオの最大長 | 10 分 | 10 分 |
|1 ターンあたりの最大 SSML メッセージ サイズ |64 KB |64 KB |
| **Rest API の制限** | 1 分あたり 20 要求 | 1 分あたり 300 要求 |


<sup>3</sup> **Free (F0)** 価格レベルについては、[価格ページ](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)で月額料金に関するページを参照してください。<br/>
<sup>4</sup> [その他の説明](#detailed-description-quota-adjustment-and-best-practices)および[ベスト プラクティス](#general-best-practices-to-mitigate-throttling-during-autoscaling)に関するセクションを参照してください。<br/>
<sup>5</sup> [その他の説明](#detailed-description-quota-adjustment-and-best-practices)、[ベスト プラクティス](#general-best-practices-to-mitigate-throttling-during-autoscaling)、および[調整手順](#text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice)に関するセクションを参照してください。<br/> 

## <a name="detailed-description-quota-adjustment-and-best-practices"></a>詳細な説明、クォータの調整、およびベスト プラクティス
クォータの引き上げを要求する前に (該当する場合)、それが必要であることを確認します。 Speech サービスでは、自動スケーリング テクノロジを用いて必要なコンピューティング リソースを "オンデマンド" モードにすると同時に、過剰なハードウェア容量を維持しないことで顧客のコストを低く抑えることができます。 ワークロードが定義された制限内であるにもかかわらず、アプリケーションで応答コード 429 ("要求が多すぎます") を毎回受け取る場合 (「[クォータと制限のクイック リファレンス](#quotas-and-limits-quick-reference)」を参照)、ユーザーの要求に合わせてサービスのスケール アップが行われている最中で、まだ必要なスケールに達しておらず、今すぐリクエストに対応するのに十分なリソースがない可能性があります。 この状態は通常一時的なものであり、長くは続かないはずです。

### <a name="general-best-practices-to-mitigate-throttling-during-autoscaling"></a>自動スケーリング時のスロットリングを緩和するための一般的なベスト プラクティス
スロットリングに関連する問題 (応答コード 429) を最小限に抑えるには、次の手法を使用することをお勧めします。
- アプリケーションで再試行ロジックを実装します。
- ワークロードが急激に変化しないようにします。 ワークロードは徐々に増やします <br/>
*例:* アプリケーションでは、テキスト読み上げが使用されており、現在のワークロードは 5 TPS (1 秒あたりのトランザクション数) です。 次の 1 秒間で、負荷を 20 TPS (4 倍以上) に増やしたとします。 この新しい負荷に対応するため、サービスでは直ちにスケールアップが開始されますが、おそらく 1 秒以内に処理することはできないため、一部の要求では応答コード 429 が返されます。   
- さまざまな負荷増加パターンをテストします
  - [音声テキスト変換の例](#speech-to-text-example-of-a-workload-pattern-best-practice)を参照してください
- 同じまたは異なるリージョンに追加の Speech リソースを作成し、"ラウンド ロビン" 手法を用いてそれらの間でワークロードを分散させます。 これは、**テキスト読み上げの TPS (1 秒あたりのトランザクション数)** のパラメーターで特に重要で、これは Speech リソースあたり 200 に設定されており、調整することはできません  

次のセクションでは、クォータを調整する特定のケースについて説明します。<br/>
「[テキスト読み上げ:Custom Voice における文字起こしの同時要求の上限を上げる](#text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice)」に移動する

### <a name="speech-to-text-increasing-online-transcription-concurrent-request-limit"></a>音声テキスト変換: オンライン文字起こしの同時要求の上限を上げる
既定では、同時要求の数は Speech リソース (ベース モデル) あたり、またはカスタム エンドポイント (カスタム モデル) あたり 20 に制限されています。 Standard 価格レベルでは、この数を増やすことができます。 要求を送信する前に、[こちらのセクション](#detailed-description-quota-adjustment-and-best-practices)の資料について理解していること、およびこれらの[ベスト プラクティス](#general-best-practices-to-mitigate-throttling-during-autoscaling)を把握していることを確認してください。

同時要求の上限を上げても、コストに直接影響することは **ありません**。 Speech Services では、"使用した分だけ支払う" モデルを使用しています。 この制限によって、要求のスロットリングが開始される前に、サービスがどの程度スケーリングされるかが定義されます。

**ベース** と **カスタム** モデルの同時要求の上限は、**個別に** 調整する必要があります。

同時要求の上限パラメーターの既存の値は、Azure portal、コマンドライン ツール、または API 要求では表示 **されません**。 既存の値を確認するには、Azure サポート リクエストを作成します。

>[!NOTE]
>[Speech コンテナー](speech-container-howto.md)は、ホストされているハードウェアの CPU によってのみ制限されるため、コンテナーで同時要求の上限を上げる必要はありません。 ただし、Speech コンテナーには、考慮すべき独自の容量制限があります。 *「オンプレミスの音声テキスト変換コンテナーの容量計画とコストの見積もりはどうすればいいでしょうか。」* という質問を [Speech コンテナー FAQ](./speech-container-howto.md) でご覧ください。

#### <a name="have-the-required-information-ready"></a>以下の必要な情報を準備します。
- **ベース モデル** の場合:
  - Speech リソース ID
  - リージョン
- **カスタム モデル** の場合: 
  - リージョン
  - カスタム エンドポイント ID

- **情報を取得する方法 (ベース モデル)** :  
  - [Azure portal](https://portal.azure.com/) に移動します
  - 同時実行要求の制限を増やす Speech リソースを選択します
  - *プロパティ* (*リソース管理* グループ) を選択します 
  - 次のフィールドの値をコピーして保存しておきます。
    - **リソース ID**
    - **場所** (エンドポイントのリージョン)

- **情報を取得する方法 (カスタム モデル)** :
  - [Speech Studio](https://speech.microsoft.com/) ポータルにアクセスします
  - 必要に応じてサインインします
  - Custom Speech にアクセスします
  - プロジェクトを選択します
  - *[デプロイ]* に移動します
  - 必要なエンドポイントを選択します
  - 次のフィールドの値をコピーして保存しておきます。
    - **サービス リージョン** (ユーザーのエンドポイント リージョン)
    - **エンドポイント ID**
  
#### <a name="create-and-submit-support-request"></a>サポート リクエストの作成と送信
リソースに対する同時要求の上限の引き上げを開始するか、必要に応じて、サポート リクエストを送信して現在の上限を確認してください。

- [必要な情報](#have-the-required-information-ready)があることを確認します
- [Azure portal](https://portal.azure.com/) に移動します
- 同時実行要求の上限を上げる (または確認する) Speech リソースを選択します
- *[新しいサポート リクエスト]* ( *[サポート + トラブルシューティング]* グループ) を選択します 
- Azure サブスクリプションと Azure リソースに関する情報が自動的に入力された新しいウィンドウが表示されます
- *[概要]* を入力します ("STT 同時実行要求の上限を上げる" など)
- *[問題の種類]* で、"クォータまたはサブスクリプションの問題" を選択します
- 表示された *[問題のサブタイプ]* で以下を選択します。
  - 増加を要求する場合は "Quota or concurrent requests increase" (クォータまたは同時要求の増加)
  - 既存の制限を確認する場合は "クォータまたは使用状況の検証"
- *[次へ: ソリューション]* をクリックします
- 要求の作成を進めます
- *[詳細]* タブの *[説明]* フィールドに以下を入力します:
  - この要求が **音声テキスト変換** のクォータに関するものであることを示すメモ
  - **ベース** または **カスタム** モデル
  - [前に収集](#have-the-required-information-ready)した Azure リソース情報 
  - 必要な情報を入力して、 *[確認と作成]* タブの *[作成]* ボタンをクリックします
  - Azure portal 通知のサポート リクエスト番号をメモしておきます。 後続の処理のための連絡が間もなくして届きます

### <a name="speech-to-text-example-of-a-workload-pattern-best-practice"></a>音声テキスト変換: ワークロード パターンの例のベスト プラクティス
この例では、[自動スケーリングが進行中である](#detailed-description-quota-adjustment-and-best-practices)ことによって発生する可能性がある要求のスロットリングを軽減するために、次のようなアプローチを推奨しています。 これは "正確なレシピ" ではなく、必要に応じて従い、調整するテンプレートにすぎません。

Speech リソースの同時要求の上限が 300 に設定されているとします。 ワークロードを 20 の同時接続から開始し、1.5 から 2 分ごとにコンカレント接続の負荷を 20 ずつ増加させます。 応答コード 429 が多すぎる場合は、サービス応答を制御し、フォールバックするロジックを実装します (負荷を軽減)。 その後、1-2-4-4 分のパターンで再試行します。 (つまり、1 分間で負荷増加を再試行し、これが正常に行われない場合は 2 分間で再試行する、のように行います)

一般に、運用環境に移行する前にワークロードとワークロード パターンをテストしておくことを強くお勧めします。

### <a name="text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice"></a>テキスト読み上げ: Custom Voice における文字起こしの同時要求の上限を上げる
既定では、Custom Voice エンドポイントの同時要求の数は 10 に制限されています。 Standard 価格レベルでは、この数を増やすことができます。 要求を送信する前に、[こちらのセクション](#detailed-description-quota-adjustment-and-best-practices)の資料について理解していること、およびこれらの[ベスト プラクティス](#general-best-practices-to-mitigate-throttling-during-autoscaling)を把握していることを確認してください。

同時要求の上限を上げても、コストに直接影響することは **ありません**。 Speech Services では、"使用した分だけ支払う" モデルを使用しています。 この制限によって、要求のスロットリングが開始される前に、サービスがどの程度スケーリングされるかが定義されます。

同時要求の上限パラメーターの既存の値は、Azure portal、コマンドライン ツール、または API 要求では表示 **されません**。 既存の値を確認するには、Azure サポート リクエストを作成します。

>[!NOTE]
>[Speech コンテナー](speech-container-howto.md)は、ホストされているハードウェアの CPU によってのみ制限されるため、コンテナーで同時要求の上限を上げる必要はありません。

#### <a name="prepare-the-required-information"></a>次の必要な情報を準備する:
増加の要求を作成するには、デプロイ リージョンとカスタム エンドポイント ID を指定する必要があります。 これを取得するには、次の操作を実行してください。 

- [Speech Studio](https://speech.microsoft.com/) ポータルにアクセスします
- 必要に応じてサインインします
- *Custom Voice* にアクセスします
- プロジェクトを選択します
- *[デプロイ]* に移動します
- 必要なエンドポイントを選択します
- 次のフィールドの値をコピーして保存しておきます。
    - **サービス リージョン** (ユーザーのエンドポイント リージョン)
    - **エンドポイント ID**
  
#### <a name="create-and-submit-support-request"></a>サポート リクエストの作成と送信
リソースに対する同時要求の上限の引き上げを開始するか、必要に応じて、サポート リクエストを送信して現在の上限を確認してください。

- [必要な情報](#prepare-the-required-information)があることを確認します
- [Azure portal](https://portal.azure.com/) に移動します
- 同時実行要求の上限を上げる (または確認する) Speech リソースを選択します
- *[新しいサポート リクエスト]* ( *[サポート + トラブルシューティング]* グループ) を選択します 
- Azure サブスクリプションと Azure リソースに関する情報が自動的に入力された新しいウィンドウが表示されます
- *[概要]* を入力します ("TTS カスタム エンドポイントの同時実行要求の上限を上げる" など)
- *[問題の種類]* で、"クォータまたはサブスクリプションの問題" を選択します
- 表示された *[問題のサブタイプ]* で以下を選択します。
  - 増加を要求する場合は "Quota or concurrent requests increase" (クォータまたは同時要求の増加)
  - 既存の制限を確認する場合は "クォータまたは使用状況の検証"
- *[次へ: ソリューション]* をクリックします
- 要求の作成を進めます
- *[詳細]* タブの *[説明]* フィールドに以下を入力します:
  - この要求が **テキスト読み上げ** のクォータに関するものであることを示すメモ
  - [前に収集](#prepare-the-required-information)した Azure リソース情報 
  - 必要な情報を入力して、 *[確認と作成]* タブの *[作成]* ボタンをクリックします
  - Azure portal 通知のサポート リクエスト番号をメモしておきます。 後続の処理のための連絡が間もなくして届きます