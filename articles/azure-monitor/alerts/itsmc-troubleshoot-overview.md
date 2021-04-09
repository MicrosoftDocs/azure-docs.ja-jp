---
title: ITSMC での問題のトラブルシューティングを行う
description: IT Service Management Connector での一般的な問題の解決方法について説明します。
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: 7094bf07453c06831fecfa2056480bf498b26a72
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041622"
---
# <a name="troubleshoot-problems-in-it-service-management-connector"></a>IT Service Management Connector での問題のトラブルシューティング

この記事では、IT Service Management Connector (ITSMC) の一般的な問題とそのトラブルシューティングの方法について説明します。

Azure Monitor を使用すると、監視データで重要な状態を検出すると、アラートで事前に通知されます。 これらのアラートは、システムのユーザーが問題に気付く前に、問題を特定して対処するのに役立ちます。

アラートを受け取る方法を選択できます。 メール、SMS、または Webhook を選択することも、解決策を自動化することもできます。 

別の方法として、ITSMC 経由で通知することもできます。 ITSMC には、ServiceNow などの外部のチケット発行システムにアラートを送信するオプションが用意されています。

## <a name="use-the-dashboard-to-analyze-incident-and-change-request-data"></a>ダッシュボードを使用してインシデントと変更要求データを分析する

ITSMC では、接続を設定したときの構成に応じて、最大 120 日分のインシデントと変更要求データを同期できます。 このデータのログ レコード スキーマを取得するには、「[お使いの ITSM 製品から同期されるデータ](./itsmc-synced-data.md)」の記事を参照してください。

インシデントと変更要求データは、ITSMC ダッシュボードを使用して視覚化できます。

![ITSMC ダッシュボードを示すスクリーンショット。](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

ダッシュボードには、コネクタの状態に関する情報も表示されます。 この情報は、接続に関する問題を分析するための出発点として使用できます。 詳細については、[ダッシュボードを使用したエラー調査](./itsmc-dashboard.md)に関するページを参照してください。

## <a name="use-service-map-to-visualize-incidents"></a>Service Map を使用してインシデントを視覚化する

Service Map で、影響を受けたコンピューターに対して同期されたインシデントを視覚化することもできます。

サービス マップは、Windows と Linux システムのアプリケーション コンポーネントを自動的に検出し、サービス間の通信をマップします。 これを使用すると、重要なサービスを提供する相互接続されたシステムとしてサーバーを表示できます。 

Service Map には、TCP 接続の任意のアーキテクチャ全体にわたるサーバー、プロセス、ポートの間の接続が表示されます。 エージェントのインストール以外に構成は必要ありません。 詳細については、[Service Map の使用](../vm/service-map.md)に関するページを参照してください。

Service Map を使用している場合は、次の例に示すように、IT Service Management (ITSM) ソリューションで作成されたサービス デスク項目を表示できます。

![Log Analytics の画面を示すスクリーンショット。](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

## <a name="resolve-problems"></a>問題を解決する

以降のセクションでは、一般的な現象、考えられる原因、および解決策について説明します。 

### <a name="a-connection-to-the-itsm-system-fails-and-you-get-an-error-in-saving-connection-message"></a>ITSM システムへの接続に失敗し、"接続の保存中にエラーが発生しました" というメッセージが表示される

**原因**:原因として考えられるのは、次のいずれかです。

* 資格情報が間違っている。
* 特権が不十分。
* Service Manager 接続の場合: Web アプリが正しくデプロイされていません。

**解決方法**:

* ServiceNow、Cherwell、Provance の接続の場合:
  * 各接続のユーザー名、パスワード、クライアント ID、クライアント シークレットが正しく入力されていることを確認します。  
  * ServiceNow については、対応する ITSM 製品で[十分な特権](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role)があることを確認します。

* Service Manager 接続の場合:  
  * Web アプリが正常にデプロイされていること、およびハイブリッド接続が作成されていることを確認します。 オンプレミスの Service Manager コンピューターとの接続が正常に確立されていることを確認するには、[ハイブリッド接続に関するドキュメント](./itsmc-connections-scsm.md#configure-the-hybrid-connection)で説明されているとおりに、Web アプリの URL に移動します。  

### <a name="duplicate-work-items-are-created"></a>重複する作業項目が作成される

**原因**:原因として考えられるのは、次の 2 つのいずれかです。

* アラートに対して複数の ITSM アクションが定義されている。
* アラートが解決されている。

**解決方法**:2 つの解決策があります。

* アラートごとに 1 つの ITSM アクション グループがあることを確認します。
* アラートが解決した際、一致する作業項目の状態の更新は ITSMC でサポートされていません。 新しい解決済みの作業項目を作成します。

### <a name="work-items-are-not-created"></a>作業項目が作成されない

**原因**:この現象には、いくつかの原因が考えられます。

* ServiceNow 側でコードが変更された。
* アクセス許可が正しく構成されていない。
* ServiceNow のレート制限が高すぎるか、低すぎる。
* 更新トークンの有効期限が切れている。
* ITSMC が削除された。

**解決方法**:[ダッシュボード](itsmc-dashboard.md)を確認し、[コネクタの状態] セクションでエラーを確認します。 次に、[一般的なエラーとその解決策](itsmc-dashboard-errors.md)を確認します。

### <a name="you-cant-create-an-itsm-action-for-an-action-group"></a>アクション グループに対して ITSM アクションを作成できない

**原因**:新しく作成された ITSMC インスタンスで、初期同期がまだ完了していない。

**解決方法**:[一般的なエラーとその解決策](itsmc-dashboard-errors.md)を確認します。

### <a name="sync-connection"></a>接続の同期 

**原因**:この現象には、いくつかの原因が考えられます。

* テンプレートが、アクション定義の一部として表示されていません。
* インシデントおよびイベントが、ServiceNow で作成されていません。

**解決方法**: [コネクタを同期します](itsmc-resync-servicenow.md)。
