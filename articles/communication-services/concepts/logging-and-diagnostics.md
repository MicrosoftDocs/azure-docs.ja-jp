---
title: Communication Services のログ
titleSuffix: An Azure Communication Services concept document
description: Azure Communication Services のログについて説明します
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 5ed75a7f8c7915645778696282e179af3a4d4091
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103493596"
---
# <a name="communication-services-logs"></a>Communication Services のログ

Azure Communication Services には、Communication Services ソリューションの監視とデバッグに使用できるログ機能が用意されています。 これらの機能は、Azure portal で構成することができます。

## <a name="enable-diagnostic-logs-in-your-resource"></a>リソースの診断ログの有効化

リソースの作成時には、既定でログが無効になっています。 ログを有効にするには、 **[監査]** セクションにあるリソース メニューの **[診断設定]** ブレードに移動します。 続いて、 **[診断設定を追加する]** をクリックします。

次に、目的のアーカイブ先を選択します。 アーカイブ先としては現在、ストレージ アカウントと Log Analytics がサポートされています。 キャプチャしたいログの種類を選択したら、診断設定を保存してください。
 
新しい設定は、約 10 分で有効になります。 自分の Communication Services リソースの [ログ] ペイン内で、構成したアーカイブ先へのログの出力が開始されます。

:::image type="content" source="./media/diagnostic-settings.png" alt-text="ACS の [診断設定] オプション。":::

診断の構成の詳細については、[Azure リソース ログ](../../azure-monitor/essentials/platform-logs-overview.md)の概要に関するページを参照してください。

## <a name="resource-log-categories"></a>リソース ログのカテゴリ

Communication Services には、有効にできるログが 3 種類用意されています。

* **利用状況ログ** - 課金の対象となる各サービス内容に関連付けられた利用状況データを提供します。
* **チャット操作ログ** - チャット サービスに関連した基本的な情報を提供します。
* **SMS 操作ログ** - SMS サービスに関連した基本的な情報を提供します。
* **認証操作ログ** - 認証サービスに関連した基本的な情報を提供します。

### <a name="usage-logs-schema"></a>利用状況ログのスキーマ

| プロパティ | 説明 |
| -------- | ---------------|
| Timestamp | ログが生成された時刻のタイムスタンプ (UTC)。 |
| 操作の名前 | ログ レコードに関連付けられている操作。 |
| 操作バージョン | API を使用して operationName が実行された場合は、操作に関連付けられている `api-version`。 この操作に対応する API がない場合、バージョンは、操作に関連付けられているプロパティが今後、変更された場合、その操作のバージョンを表します。 |
| カテゴリ | イベントのログ カテゴリ。 カテゴリは細分化されており、これを使用して、特定のリソースのログを有効または無効にすることができます。 イベントのプロパティ BLOB 内に表示されるプロパティは、特定のログ カテゴリとリソースの種類内のものと同じです。 |
| 関連付け ID | 相関性があるイベントの ID。 複数のテーブル間で相関性のあるイベントを特定する際に使用できます。 |
| Properties | Communication Services の各種モードに該当するその他のデータ。 |
| Record ID (レコード ID) | 特定の利用状況レコードの一意の ID。 |
| 使用の種類 | 使用モード (チャット、PSTN、NAT など)。 |
| Unit 型 | 特定の使用モードに関する使用量の基準となる単位の種類 (分、メガバイト、メッセージ数など)。 |
| Quantity | そのレコードに関して使用または消費された単位の数。 |

### <a name="chat-operational-logs"></a>チャット操作ログ

| プロパティ | 説明 |
| -------- | ---------------|
| TimeGenerated | ログが生成された時刻のタイムスタンプ (UTC)。 |
| OperationName | ログ レコードに関連付けられている操作。 |
| CorrelationID | 相関性があるイベントの ID。 複数のテーブル間で相関性のあるイベントを特定する際に使用できます。 |
| OperationVersion | operationName が API を使用して実行された場合は、操作に関連付けられている api-version。 この操作に対応する API がない場合、バージョンは、操作に関連付けられているプロパティが今後、変更された場合、その操作のバージョンを表します。 |
| カテゴリ | イベントのログ カテゴリ。 カテゴリは細分化されており、これを使用して、特定のリソースのログを有効または無効にすることができます。 イベントのプロパティ BLOB 内に表示されるプロパティは、特定のログ カテゴリとリソースの種類内のものと同じです。 |
| ResultType | 操作のステータス。 |
| ResultSignature | 操作の副状態。 この操作が REST API 呼び出しに対応している場合、このフィールドは、対応する REST 呼び出しの HTTP 状態コードです。 |
| ResultDescription | この操作を説明する静的テキスト。 |
| DurationMs | 操作時間 (ミリ秒)。 |
| CallerIpAddress | 操作が、一般的に利用できる IP アドレスを持つエンティティからの API 呼び出しに対応している場合は、呼び出し元 IP アドレス。 |
| Level | イベントの重大度レベル。 |
| URI | 要求の URI。 |
| UserId | 要求の送信者のユーザー ID。 |
| ChatThreadId | 要求に関連付けられているチャット スレッド ID。 |
| ChatMessageId | 要求に関連付けられているチャット メッセージ ID。 |
| SdkType | 要求で使用された SDK の種類。 |
| PlatformType | 要求で使用されたプラットフォームの種類。 |

### <a name="sms-operational-logs"></a>SMS 操作ログ

| プロパティ | 説明 |
| -------- | ---------------|
| TimeGenerated | ログが生成された時刻のタイムスタンプ (UTC)。 |
| OperationName | ログ レコードに関連付けられている操作。 |
| CorrelationID | 相関性があるイベントの ID。 複数のテーブル間で相関性のあるイベントを特定する際に使用できます。 |
| OperationVersion | operationName が API を使用して実行された場合は、操作に関連付けられている api-version。 この操作に対応する API がない場合、バージョンは、操作に関連付けられているプロパティが今後、変更された場合、その操作のバージョンを表します。 |
| カテゴリ | イベントのログ カテゴリ。 カテゴリは細分化されており、これを使用して、特定のリソースのログを有効または無効にすることができます。 イベントのプロパティ BLOB 内に表示されるプロパティは、特定のログ カテゴリとリソースの種類内のものと同じです。 |
| ResultType | 操作のステータス。 |
| ResultSignature | 操作の副状態。 この操作が REST API 呼び出しに対応している場合、このフィールドは、対応する REST 呼び出しの HTTP 状態コードです。 |
| ResultDescription | この操作を説明する静的テキスト。 |
| DurationMs | 操作時間 (ミリ秒)。 |
| CallerIpAddress | 操作が、一般的に利用できる IP アドレスを持つエンティティからの API 呼び出しに対応している場合は、呼び出し元 IP アドレス。 |
| Level | イベントの重大度レベル。 |
| URI | 要求の URI。 |
| OutgoingMessageLength | 送信メッセージに含まれる文字数。 |
| IncomingMessageLength | 受信メッセージに含まれる文字数。 |
| DeliveryAttempts | このメッセージの配信試行回数。 |
| PhoneNumber | SMS メッセージの送信先である電話番号。 |
| SdkType | 要求で使用された SDK の種類。 |
| PlatformType | 要求で使用されたプラットフォームの種類。 |
| メソッド | 要求で使用されたメソッド。 |

### <a name="authentication-operational-logs"></a>認証操作ログ

| プロパティ | 説明 |
| -------- | ---------------|
| TimeGenerated | ログが生成された時刻のタイムスタンプ (UTC)。 |
| OperationName | ログ レコードに関連付けられている操作。 |
| CorrelationID | 相関性があるイベントの ID。 複数のテーブル間で相関性のあるイベントを特定する際に使用できます。 |
| OperationVersion | API を使用して `operationName` が実行された場合は、その操作に関連付けられている `api-version`。 この操作に対応する API がない場合、バージョンは、操作に関連付けられているプロパティが今後、変更された場合、その操作のバージョンを表します。 |
| カテゴリ | イベントのログ カテゴリ。 カテゴリは細分化されており、これを使用して、特定のリソースのログを有効または無効にすることができます。 イベントのプロパティ BLOB 内に表示されるプロパティは、特定のログ カテゴリとリソースの種類内のものと同じです。 |
| ResultType | 操作のステータス。 |
| ResultSignature | 操作のサブステータス。 この操作が REST API 呼び出しに対応している場合、このフィールドは、対応する REST 呼び出しの HTTP 状態コードです。 |
| DurationMs | 操作時間 (ミリ秒)。 |
| CallerIpAddress | 操作が、一般的に利用できる IP アドレスを持つエンティティからの API 呼び出しに対応している場合は、呼び出し元 IP アドレス。 |
| Level | イベントの重大度レベル。 |
| URI | 要求の URI。 |
| SdkType | 要求で使用された SDK の種類。 |
| PlatformType | 要求で使用されたプラットフォームの種類。 |
| ID | 操作に関連する Communication Services ID。 |
| スコープ | アクセス トークンに含まれる Communication Services のスコープ。 |
