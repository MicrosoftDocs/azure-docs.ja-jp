---
title: ITSMC ダッシュボード内の [コネクタの状態] のエラー
description: IT Service Management Connector ダッシュボードで見られる一般的なエラーについて説明します。
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/18/2021
ms.openlocfilehash: 727e744c59d0a8d90cf320e1ee2e2a17e10ff847
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103471531"
---
# <a name="connector-status-errors-in-the-itsmc-dashboard"></a>ITSMC ダッシュボード内の [コネクタの状態] のエラー

IT Service Management Connector (ITSMC) ダッシュボードに表示されるエラーは、コネクタでの問題を解決するのに役立ちます。

次のセクションでは、ダッシュボードの [コネクタの状態] セクションに表示される一般的なエラーと、それらを解決する方法について説明します。

## <a name="unexpected-response"></a>予期しない応答

**Error**: "Unexpected response from ServiceNow along with success status code. (成功状態コードと共に ServiceNow から予期しない応答が返されました。) Response: { "import_set": "{import_set_id}", "staging_table": "x_mioms_microsoft_oms_incident", "result": [ { "transform_map":"OMS Incident", "table": "incident", "status": "error", "error_message": "{Target record not found|Invalid table|Invalid staging table" }" (応答: { "import_set": "{import_set_id}", "staging_table": "x_mioms_microsoft_oms_incident", "結果": [ { "transform_map":"OMS インシデント", "テーブル": "インシデント", "状態": "エラー", "error_message": "{ターゲット レコードが見つかりません|テーブルが無効です|ステージング テーブルが無効です" }")

**原因**:このエラーは、次の場合に ServiceNow から返されます。

* ServiceNow インスタンスにデプロイされたカスタム スクリプトが原因で、インシデントが無視される。
* ServiceNow 側で "OMS インテグレーター アプリ" のコードが変更された (たとえば、`onBefore` スクリプトを介して)。

**解決方法**:すべてのカスタム スクリプトまたはコードの変更を無効にします。

## <a name="exception-update-failure"></a>例外の更新エラー

**Error**: "{"error":{"message":"Operation Failed","detail":"ACL Exception Update Failed due to security constraints"}" ("{"エラー":{"メッセージ":"操作が失敗しました","詳細":"セキュリティ制約が原因で ACL 例外の更新が失敗しました"}")

**原因**:ServiceNow のアクセス許可が正しく構成されていない。

**解決方法**: すべてのロールが [指定](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role)どおりに適切に割り当てられていることを確認します。

## <a name="problem-sending-a-request"></a>要求の送信に関する問題

**Error**: "An error occurred while sending the request. (この要求の送信中にエラーが発生しました。)"

**原因**: ServiceNow インスタンスが使用できない。

**解決方法**: ServiceNow でご利用のインスタンスを確認します。 削除されているか、使用できない可能性があります。

## <a name="servicenow-rate-problem"></a>ServiceNow のレートに関する問題

**Error**: "ServiceDeskHttpBadRequestException:StatusCode=429"

**原因**:ServiceNow のレート制限が高すぎるか、低すぎる。

**解決方法**:[ServiceNow のドキュメント](https://docs.servicenow.com/bundle/london-application-development/page/integrate/inbound-rest/task/investigate-rate-limit-violations.html)で説明されているように、ServiceNow インスタンスでレート制限を引き上げるか、取り消します。

## <a name="invalid-refresh-token"></a>更新トークンが無効である

**エラー**: 
  * "AccessToken and RefreshToken invalid. (AccessToken および RefreshToken が無効です。) User needs to authenticate again. (ユーザーはもう一度認証を受ける必要があります。)"
  * "Could not sync templates configuration for Event,Alert,Incident. (イベント、アラート、インシデントのテンプレート構成を同期できませんでした。) See Exception Message for more details. (詳細については、例外メッセージを参照してください。)"

**原因**:更新トークンの有効期限が切れている。

**解決方法**: 「[同期に関する問題を手動で修正する方法](./itsmc-resync-servicenow.md)」 で説明されているように、ITSMC を同期して新しい更新トークンを作成します。

## <a name="missing-connector"></a>コネクタが見つからない

**Error**: "Could not create/update work item for alert {alertName}. (アラート {alertName} の作業項目を作成または更新できませんでした。) ITSM Connector {connectionIdentifier} does not exist or was deleted. (ITSM Connector {connectionIdentifier} が存在しないか、削除されました。)"

**原因**:ITSMC が削除された。

**解決方法**: ITSMC は削除されましたが、IT Service Management (ITSM) の定義されたアクション グループはまだ関連付けられています。 この問題を解決するには、次の 3 つの方法があります。

* 該当するアクション グループを見つけて、無効にするか削除する。
* 既存の ITSMC インスタンスを使用するように[アクション グループを再構成する](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)。
* [新しい ITSMC インスタンスを作成](./itsmc-definition.md#create-an-itsm-connection)し、[それを使用するようにアクション グループを再構成する](itsmc-definition.md#create-itsm-work-items-from-azure-alerts)。

## <a name="lack-of-connection-details"></a>接続の詳細が不足している

**エラー**:"Something went wrong. (問題が発生しました。) Could not get connection details. (接続の詳細を取得できませんでした。)" このエラーは、ITSM アクション グループを定義するときに表示されます。

**原因**:このようなエラーが、次のいずれかの状況で表示される。

* 新しく作成された ITSM Connector インスタンスで、初期同期がまだ完了していない。
* コネクタが正しく定義されていない。

**解決方法**: 

* 新しい ITSMC インスタンスを作成すると、作業項目テンプレートや作業項目など、ITSM システムからの情報の同期が開始されます。 [ITSMC を同期して新しい更新トークンを作成します](./itsmc-resync-servicenow.md)。
* [ITSMC で接続の詳細を確認](./itsmc-connections-servicenow.md#create-a-connection)し、ITSMC が正常に[同期](./itsmc-resync-servicenow.md)されることを確認します。


## <a name="ip-restrictions"></a>IP 制限
**エラー**: "無効な要求により、"XXX" という名前の ITSM 接続を追加できませんでした。 エラー: 正しくない要求。 接続に無効なパラメーターが指定されました。 HTTP 例外: 状態コードは禁止されています。"

**原因**: ITSM アプリケーションの IP アドレスでは、パートナーの ITSM ツールからの ITSM 接続を許可していません。

**解決策**: パートナーの ITSM ツールからの ITSM 接続を許可するために ITSM IP アドレスを一覧表示するには、LogAnalytics ワークスペースが属している Azure リージョンのパブリック IP 範囲全体を一覧表示することをお勧めします。 [詳細はこちら](https://www.microsoft.com/download/details.aspx?id=56519) 米国東部、西ヨーロッパ、米国東部 2、米国西部 2、米国中南部のリージョンの場合、顧客は ActionGroup ネットワーク タグのみを一覧表示できます。
