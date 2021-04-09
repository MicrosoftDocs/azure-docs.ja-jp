---
title: 分析データへのプログラムによるアクセスについてよく寄せられる質問
description: コマーシャル マーケットプレース プランのパートナー センターで、分析データへのプログラムによるアクセスに関してよく寄せられる質問。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 393a718632138f4ffcf26e4875eea9ba3d886897
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583563"
---
# <a name="programmatic-access-of-analytics-data-common-questions"></a>分析データへのプログラムによるアクセスについてよく寄せられる質問

この記事では、コマーシャル マーケットプレース プランのパートナー センターで分析データにプログラムでアクセスする方法についてよく寄せられる質問について説明します。

## <a name="api-responses"></a>API の応答

200 (成功) 以外の API 応答を受け取る可能性のあるさまざまなシナリオは何ですか?

次の表で、API の応答と、それを受け取った場合の対処方法について説明します。

| エラーの説明 | エラー コード | トラブルシューティング |
| ------------ | ------------- | ------------- |
| 権限がありません | 401 | これは認証の例外です。 Azure Active Directory (Azure AD) トークンの正確性を確認してください。 Azure AD トークンは 60 分有効です。その後、Azure AD トークンを再生成する必要があります。 |
| 無効なテーブル名です | 400 | データセットの名前が間違っています。 "すべてのデータセットの取得" API を呼び出して、データセット名を再確認してください。 |
| 列名が正しくありません | 400| クエリ内の列の名前が正しくありません。 "すべてのデータセットの取得" API を呼び出して列名を再確認するか、次の表の列名を参照してください。<br><ul><li>[[注文の詳細] テーブル](orders-dashboard.md#orders-details-table)</li><li>[使用状況の詳細テーブル](usage-dashboard.md#usage-details-table)</li><li>[顧客詳細テーブル](customer-dashboard.md#customer-details-table)</li><li>[Marketplace の分析情報の詳細テーブル](insights-dashboard.md#marketplace-insights-details-table)</li></UL> |
| null または欠損値です | 400 | API の要求ペイロードの一部としての必須パラメーターがない可能性があります。 |
| 無効なレポート パラメーターです | 400 | レポート パラメーターが正しいことを確認してください。 たとえば、`RecurrenceInterval` パラメーターに 4 より小さい値を指定している可能性があります。 |
| 繰り返し間隔 は 4 ～ 90 でなければなりません | 400 | `RecurrenceInterval` 要求パラメーターの値が 4 ～ 90 であることを確認してください。 |
| 無効な QueryId です | 400 | 生成された `QueryId` を再確認してください。 |
| 作成のためのレポート パラメーターが無効です - レポートの開始時刻は現在の UTC 時刻から 4 時間以上である必要があります - | 400 | 要求ペイロードの一部としての開始時刻パラメーターを過去にすることはできません。 レポートの開始時刻は、現在の UTC 時刻から 4 時間以上である必要があります。 |
| 要求された値 'string' が見つかりません | 400 | 要求パラメーター `callbackurl` または `format` を更新したかどうかを確認してください。 |
| 指定されたフィルターで項目が見つかりませんでした。 | 404 | レポート実行の取得 API で使用した `reportID` パラメーターを確認してください。 |
| 指定されたフィルター条件で実行された実行はありません。 reportId または executionId を再確認し、レポートのスケジュールされた実行時間の後に API を再試行してください | 404 | `reportId` が正しいことを確認してください。 要求ペイロードで指定されているように、レポートのスケジュールされた実行時間の後に、API を再試行してください。 |
| レポートの作成中に内部エラーが発生しました。 関連付け ID <> | 500 | フィールド "StartTime"、"QueryStartTime"、および "Querystarttime" の日付の形式が正しいことを確認してください。 |
| Service unavailable (サービス利用不可) | 500 | サービス利用不可 (5xx エラー) が継続的に表示される場合は、[サポート チケット](support.md)を作成してください。 |
||||

## <a name="no-records"></a>レコードがありません

セキュリティで保護された場所からレポートをダウンロードすると、API 応答 200 が表示されます。 なぜ "レコードがありません" が表示されるのでしょうか?

クエリ内の文字列に、列ヘッダーに許可されている値のいずれかが含まれているかどうかを確認してください。 たとえば、次のクエリは結果を返しません。

`"SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paided' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH"`

この例では、SKUBillingType に使用できる値は Paid または Free です。 さまざまな列に使用できるすべての値については、次の表を参照してください。

- [[注文の詳細] テーブル](orders-dashboard.md#orders-details-table)
- [使用状況の詳細テーブル](usage-dashboard.md#usage-details-table)
- [顧客詳細テーブル](customer-dashboard.md#customer-details-table)
- [Marketplace の分析情報の詳細テーブル](insights-dashboard.md#marketplace-insights-details-table)

## <a name="next-steps"></a>次のステップ

- [コマーシャル マーケットプレースの分析データにアクセスするための API](analytics-available-apis.md)
