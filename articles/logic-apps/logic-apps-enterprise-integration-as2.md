---
title: B2B の AS2 メッセージを送受信する
description: Azure Logic Apps と Enterprise Integration Pack を使用して B2B エンタープライズ統合シナリオ用の AS2 メッセージを交換します
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: 1845de8332287f85e96b4e29c40caa66cb8704d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91856672"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Azure Logic Apps と Enterprise Integration Pack で B2B エンタープライズ統合用の AS2 メッセージを交換する

> [!IMPORTANT]
> 元の AS2 コネクタは非推奨になっているため、追跡機能が必要な場合を除き、代わりに **AS2 (v2)** コネクタを使用するようにしてください。 v2 コネクタでは追跡機能を除き、元のバージョンと同じ機能を提供します。このコネクタは、Logic Apps ランタイムにとってネイティブであり、メッセージ サイズ、スループット、および待ち時間について大幅なパフォーマンスの向上を実現します。 また、v2 コネクタでは、統合アカウントへの接続を作成する必要はありません。 代わりに、前提条件で説明されているように、コネクタを使用する予定のロジック アプリに統合アカウントがリンクされていることを確認してください。

Azure Logic Apps で AS2 メッセージを操作するには、AS2 コネクタを使用できます。これには、AS2 (バージョン 1.2) 通信をサポートして管理するためのトリガーとアクションが用意されています。 たとえば、メッセージの送信時にセキュリティと信頼性を確立するには、以下のアクションを使用できます。

* [**AS2 エンコード** アクション](#encode): 暗号化、デジタル署名、メッセージ処理通知 (MDN) を介した受信確認を提供します。これらは否認防止をサポートするのに役立ちます。 たとえば、このアクションでは、AS2/HTTP ヘッダーが適用され、構成されている場合は、次のタスクが実行されます。

  * 送信メッセージに署名する。
  * 送信メッセージを暗号化する。
  * メッセージを圧縮する。
  * MIME ヘッダー内のファイル名を送信する。

* [**AS2 デコード** アクション](#decode): 暗号化の解除、デジタル署名、メッセージ処理通知 (MDN) を介した受信確認を提供します。 たとえば、このアクションでは、次のタスクが実行されます。

  * AS2/HTTP ヘッダーを処理する。
  * 受信した MDN と元の送信メッセージを調整する。
  * 否認不可データベース内のレコードを更新し、関連付ける。
  * AS2 状態レポート用のレコードを書き込む。
  * ペイロードの内容を base64 でエンコードして出力する。
  * MDN が必要かどうかを判断する。 AS2 契約に基づいて、MDN を同期または非同期のどちらにするかを決定する。
  * AS2 契約に基づいて、同期または非同期の MDN を生成する。
  * MDN に対して関連付けトークンとプロパティを設定する

  このアクションでは、構成されている場合、次のタスクも実行されます。

  * 署名を確認する。
  * メッセージの暗号化を解除する。
  * メッセージの圧縮を解除する。
  * メッセージ ID の重複を確認し不許可にする。

この記事では、AS2 エンコード アクションとデコード アクションを既存のロジック アプリに追加する方法について説明します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* お客様が AS2 コネクタを使用するロジック アプリと、ご利用のロジック アプリのワークフローを開始するトリガー。 AS2 コネクタでは、アクションのみ提供され、トリガーは提供されません。 ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」と[クイック スタートの初めてのロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。

* ご利用の Azure サブスクリプションに関連付けられ、お客様が AS2 コネクタの使用を計画しているロジック アプリにリンクされている[統合アカウント](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。 使用するロジックアプリと統合アカウントは両方とも同じ場所または同じ Azure リージョンに存在する必要があります。

* AS2 ID 修飾子を使用してご利用の統合アカウント内で既に定義されている少なくとも 2 つの[取引先](../logic-apps/logic-apps-enterprise-integration-partners.md)。

* AS2 コネクタを使用するには、事前に取引先間の AS2 [契約](../logic-apps/logic-apps-enterprise-integration-agreements.md)を作成し、その契約をご利用の統合アカウントに格納しておく必要があります。

* 証明書の管理に [ Azure Key Vault](../key-vault/general/overview.md) を使用する場合は、ご利用のコンテナー キーで **暗号化** 操作および **暗号化の解除** 操作が許可されていることを確認してください。 そのようになっていない場合、エンコードとデコードのアクションは失敗します。

  Azure portal で、ご利用のキー コンテナーのキーに移動し、そのキーの **許可された** 操作を表示し、**暗号化** 操作と **暗号化の解除** 操作が選択されていることを確認します。例：

  ![コンテナー キーの操作を確認する](media/logic-apps-enterprise-integration-as2/key-vault-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>AS2 メッセージをエンコードする

1. [Azure portal](https://portal.azure.com) のロジック アプリ デザイナーでロジック アプリを開きます (まだ開いていない場合)。

1. デザイナーで、ご利用のロジック アプリに新しいアクションを追加します。

1. **[アクションを選択してください]** と検索ボックスの下の **[すべて]** を選択します。 検索ボックスに「as2 エンコード」と入力し、AS2 (v2) アクションの **[AS2 エンコード]** を選択したことを確認します。

   ![[AS2 エンコード] を選択する](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. ここで、以下のプロパティに関する情報を提供します。

   | プロパティ | 説明 |
   |----------|-------------|
   | **エンコードするメッセージ** | メッセージ ペイロード |
   | **AS2 の送信元** | AS2 契約で指定されているメッセージ送信者の識別子 |
   | **AS2 の宛先** | AS2 契約で指定されているメッセージ受信者の識別子 |
   |||

   次に例を示します。

   ![メッセージのエンコード プロパティ](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

> [!TIP]
> 署名または暗号化されたメッセージの送信中に問題が発生した場合は、異なる SHA256 アルゴリズム フォームを試すことを検討してください。 AS2 の仕様では SHA256 フォームに関する情報は提供されないため、各プロバイダーでは独自の実装または形式が使用されます。

<a name="decode"></a>

## <a name="decode-as2-messages"></a>AS2 メッセージをデコードする

1. [Azure portal](https://portal.azure.com) のロジック アプリ デザイナーでロジック アプリを開きます (まだ開いていない場合)。

1. デザイナーで、ご利用のロジック アプリに新しいアクションを追加します。

1. **[アクションを選択してください]** と検索ボックスの下の **[すべて]** を選択します。 検索ボックスに「as2 デコード」と入力し、AS2 (v2) アクションの **[AS2 デコード]** を選択したことを確認します。

   ![[AS2 デコード] を選択する](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. **[エンコードするメッセージ]** プロパティと **[メッセージ ヘッダー]** プロパティで、前のトリガーまたはアクションの出力から値を選択します。

   たとえば、ご利用のロジック アプリで、要求トリガーを介してメッセージが受信されたとします。 そのトリガーから出力を選択できます。

   ![要求の出力から本文とヘッダーを選択します。](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png)

## <a name="sample"></a>サンプル

完全に動作するロジック アプリとサンプルの AS2 シナリオのデプロイを試すには、[AS2 ロジック アプリのテンプレートとシナリオ](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/)を参照してください。

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの Swagger ファイルに記述される、アクションや制限などのこのコネクタの技術的詳細は、[コネクタの参照ページ](/connectors/as2/)を参照してください。 

> [!NOTE]
> [統合サービス環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) のロジック アプリの場合、このコネクタの ISE のラベルが付いたバージョンでは、代わりに [ISE の B2B メッセージ制限](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits)が使用されます。

## <a name="next-steps"></a>次のステップ

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。
