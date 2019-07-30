---
title: B2B エンタープライズ統合用の RosettaNet メッセージ - Azure Logic Apps
description: Enterprise Integration Pack を備えた Azure Logic Apps で RosettaNet メッセージを交換する
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 88e02f3fbbca8007fdf479bb973f50c42a878d6e
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332423"
---
# <a name="exchange-rosettanet-messages-for-b2b-enterprise-integration-in-azure-logic-apps"></a>Azure Logic Apps で B2B エンタープライズ統合用の RosettaNet メッセージを交換する 

[RosettaNet](https://resources.gs1us.org) は、ビジネス情報を共有するための標準プロセスを確立した非営利コンソーシアムです。 これらの標準は、サプライチェーン プロセスで一般的に使用されており、半導体、エレクトロニクス、および物流業界に広まっています。 RosettaNet コンソーシアムは、一般的なビジネス プロセス定義をすべての RosettaNet メッセージ交換に提供する Partner Interface Processes (PIP) の作成と管理を担っています。 RosettaNet は、XML に基づいており、メッセージ ガイドライン、ビジネス プロセスのインターフェイス、企業間通信の実装フレームワークを定義します。

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) の RosettaNet コネクタは、RosettaNet 標準をサポートする統合ソリューションを作成するために役立ちます。 このコネクタは RosettaNet Implementation Framework (RNIF) バージョン 2.0.01 に基づいています。 RNIF は、ビジネス パートナーが共同作業によって RosettaNet PIP を実行できるようにするオープン ネットワーク アプリケーション フレームワークです。 このフレームワークは、メッセージ構造、受信確認の必要性、Multipurpose Internet Mail Extensions (MIME) エンコード、およびデジタル署名を定義します。

具体的には、コネクタは次の機能を提供します。

* RosettaNet メッセージをエンコードまたは受信します。
* RosettaNet メッセージをデコードまたは送信します。
* 応答、および障害通知の生成を待ちます。

これらの機能のために、コネクタは RNIF 2.0.01 で定義されているすべての PIP をサポートします。 パートナーとの通信は、同期または非同期にできます。

## <a name="rosettanet-concepts"></a>RosettaNet の概念

ここでは、RosettaNet の仕様に固有であり、RosettaNet ベースの統合を構築する際に重要である、いくつかの概念と用語について説明します。

* **PIP**

  RosettaNet 組織は、一般的なビジネス プロセス定義をすべての RosettaNet メッセージ交換に提供する Partner Interface Processes (PIP) の作成と管理を担っています。 各 PIP 仕様では、ドキュメント型定義 (DTD) ファイルとメッセージ ガイドライン ドキュメントを提供します。 DTD ファイルは service-content メッセージ構造を定義します。 人間が判読できる HTML ファイルであるメッセージ ガイドライン ドキュメントでは、要素レベルの制約の仕様を定めています。 これらのファイルを合わせて、ビジネス プロセスの完全な定義が提供されます。

   PIP は、高レベルのビジネス機能 (クラスター) とサブ機能 (セグメント) によって分類されます。 たとえば、"3A4" は注文書の PIP であり、"3" は注文管理機能、"3A" は見積もりと受注のサブ機能です。 詳細については、[RosettaNet サイト](https://resources.gs1us.org)を参照してください。

* **アクション**

  PIP の一部であるアクション メッセージは、パートナー間で交換されるビジネス メッセージです。

* **シグナル**

   PIP の一部であるシグナル メッセージは、アクション メッセージへの応答で送信される受信確認です。

* **シングル アクションとダブル アクション**

  シングル アクション PIP の場合、唯一の応答は受信確認シグナル メッセージです。 ダブル アクション PIP の場合、イニシエーターは応答メッセージを受信し、シングル アクション メッセージ フローのほか、受信確認を返信します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* ご利用の契約およびその他の B2B 成果物を格納する[統合アカウント](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。 この統合アカウントは、ご利用の Azure サブスクリプションに関連付けられている必要があります。

* 統合アカウントで定義され、**ビジネス ID** の "DUNS" 修飾子を使用して構成されている 2 つ以上の[パートナー](../logic-apps/logic-apps-enterprise-integration-partners.md)

* RosettaNet メッセージの送受信に必要な、統合アカウントでの PIP プロセス構成。 プロセス構成には、すべての PIP 構成特性が格納されます。 後でパートナーとの契約を作成するときにこの構成を参照できます。 統合アカウントに PIP プロセス構成を作成するには、[PIP プロセス構成の追加](#add-pip)に関する項目を参照してください。

* 統合アカウントにアップロードするメッセージの暗号化、復号化、または署名のための[証明書](../logic-apps/logic-apps-enterprise-integration-certificates.md) (省略可能)。 証明書は、署名または暗号化を使用している場合にのみ必要です。

<a name="add-pip"></a>

## <a name="add-pip-process-configuration"></a>PIP プロセス構成を追加する

PIP プロセス構成を統合アカウントに追加するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) で、統合アカウントを検索して開きます。

1. **[概要]** ウィンドウで、 **[RosettaNet PIP]** タイルを選択します。

   ![RosettaNet タイルを選択する](media/logic-apps-enterprise-integration-rosettanet/select-rosettanet-tile.png)

1. **[RosettaNet PIP]** の下にある **[追加]** を選択します。 PIP の詳細を指定します。

   ![RosettaNet PIP の詳細を追加する](media/logic-apps-enterprise-integration-rosettanet/add-rosettanet-pip.png)

   | プロパティ | 必須 | 説明 |
   |----------|----------|-------------|
   | **Name** | はい | PIP 名 |
   | **PIP コード** | はい | 3 桁の PIP コード。 詳細については、[RosettaNet PIP](https://docs.microsoft.com/biztalk/adapters-and-accelerators/accelerator-rosettanet/rosettanet-pips) を参照してください。 |
   | **PIP バージョン** | はい | 選択した PIP コードに基づいて利用可能な PIP バージョン番号 |
   ||||

   これらの PIP プロパティの詳細については、[RosettaNet Web サイト](https://resources.gs1us.org/RosettaNet-Standards/Standards-Library/PIP-Directory#1043208-pipsreg)を参照してください。

1. 完了したら、 **[OK]** をクリックします。PIP 構成が作成されます。

1. プロセス構成を表示または編集するには、PIP を選択して **[Edit as JSON]\(JSON として編集\)** を選択します。

   すべてのプロセス構成設定は PIP の仕様に由来します。 Logic Apps は、ほとんどの設定に、これらのプロパティに対して最も一般的に使用される値である既定値を事前設定します。

   ![RosettaNet PIP 構成を編集する](media/logic-apps-enterprise-integration-rosettanet/edit-rosettanet-pip.png)

1. 設定が適切な PIP 仕様の値に対応しており、ビジネス ニーズを満たしていることを確認します。 必要に応じて、JSON 内の値を更新し、それらの変更を保存します。

## <a name="create-rosettanet-agreement"></a>RosettaNet 契約を作成する

1. [Azure portal](https://portal.azure.com) で、統合アカウントをまだ開いていない場合は検索して開きます。

1. **[概要]** ウィンドウで、 **[契約]** タイルを選択します。

   ![[契約] タイルを選択する](media/logic-apps-enterprise-integration-rosettanet/select-agreement-tile.png)

1. **[契約]** で **[追加]** を選びます。 契約の詳細を指定します。

   ![契約の詳細を追加する](media/logic-apps-enterprise-integration-rosettanet/add-agreement-details.png)

   | プロパティ | 必須 | 説明 |
   |----------|----------|-------------|
   | **Name** | はい | 契約の名前 |
   | **契約の種類** | はい | **RosettaNet** を選択します。 |
   | **ホスト パートナー** | はい | 契約には、ホストとゲストの両方のパートナーが必要です。 ホスト パートナーは、契約を構成している組織を表します。 |
   | **ホスト ID** | はい | ホスト パートナーの ID。 |
   | **ゲスト パートナー** | はい | 契約には、ホストとゲストの両方のパートナーが必要です。 ゲスト パートナーは、ホスト パートナーと取引している組織を表します。 |
   | **ゲスト ID** | はい | ゲスト パートナーの ID。 |
   | **受信設定** | 多様 | これらのプロパティは、ホスト パートナーが受信するすべてのメッセージに適用されます |
   | **送信の設定** | 多様 | これらのプロパティは、ホスト パートナーが送信するすべてのメッセージに適用されます |  
   | **RosettaNet PIP 参照** | はい | 契約の PIP 参照。 すべての RosettaNet メッセージには PIP 構成が必要です。 |
   ||||

1. ゲスト パートナーからメッセージを受信するために契約をセットアップするには、 **[Receive Settings]\(受信設定\)** を選択します。

   ![受信設定](media/logic-apps-enterprise-integration-rosettanet/add-agreement-receive-details.png)

   1. 受信メッセージの署名または暗号化を有効にするには、 **[メッセージ]** で、 **[メッセージに署名を付ける]** または **[メッセージを暗号化する]** をそれぞれ選択します。

      | プロパティ | 必須 | 説明 |
      |----------|----------|-------------|
      | **メッセージに署名してください** | いいえ | 選択した証明書で受信メッセージに署名します。 |
      | **証明書** | はい (署名が有効な場合) | 署名に使用する証明書 |
      | **メッセージの暗号化を有効にしてください** | いいえ | 選択した証明書で受信メッセージを暗号化します。 |
      | **証明書** | はい (暗号化が有効な場合) | 暗号化に使用する証明書 |
      ||||

   1. 各選択の下で、以前に統合アカウントに追加した、署名または暗号化のそれぞれに使用する[証明書](./logic-apps-enterprise-integration-certificates.md)を選択します。

1. ゲスト パートナーにメッセージを送信するための契約を設定するには、 **[Send Settings]\(送信設定\)** を選択します。

   ![送信設定](media/logic-apps-enterprise-integration-rosettanet/add-agreement-send-details.png)

   1. 送信メッセージの署名または暗号化を有効にするには、 **[メッセージ]** で、 **[Enable message signing]\(メッセージの署名を有効にする\)** または **[Enable message encryption]\(メッセージの暗号化を有効にする\)** をそれぞれ選択します。 各選択の下で、以前に統合アカウントに追加した、署名または暗号化のそれぞれに使用するアルゴリズムと[証明書](./logic-apps-enterprise-integration-certificates.md)を選択します。

      | プロパティ | 必須 | 説明 |
      |----------|----------|-------------|
      | **メッセージの署名を有効にしてください** | いいえ | 選択した署名アルゴリズムと証明書を使用して送信メッセージに署名します。 |
      | **署名アルゴリズム** | はい (署名が有効な場合) | 使用する署名アルゴリズム (選択した証明書に基づきます) |
      | **証明書** | はい (署名が有効な場合) | 署名に使用する証明書 |
      | **メッセージの暗号化を有効にしてください** | いいえ | 選択した暗号化アルゴリズムと証明書を使用して送信を暗号化します。 |
      | **暗号化アルゴリズム** | はい (暗号化が有効な場合) | 使用する暗号化アルゴリズム (選択した証明書に基づきます) |
      | **証明書** | はい (暗号化が有効な場合) | 暗号化に使用する証明書 |
      ||||

   1. **[エンドポイント]** で、アクション メッセージと受信確認の送信に使用する必須の URL を指定します。

      | プロパティ | 必須 | 説明 |
      |----------|----------|-------------|
      | **アクション URL** |  はい | アクション メッセージの送信に使用する URL。 この URL は、同期メッセージと非同期メッセージの両方で必須のフィールドです。 |
      | **Acknowledgment URL** (受信確認 URL) | はい | 受信確認メッセージの送信に使用する URL。 この URL は非同期メッセージで必須のフィールドです。 |
      ||||

1. パートナー用の RosettaNet PIP 参照を契約に設定する場合、 **[RosettaNet PIP references]\(RosettaNet PIP 参照\)** を選択します。 **[PIP Name]\(PIP 名\)** で、以前に作成した PIP の名前を選択します。

   ![PIP 参照](media/logic-apps-enterprise-integration-rosettanet/add-agreement-pip-details.png)

   選択すると、統合アカウントに設定した PIP に基づいて、残りのプロパティが事前設定されます。 必要な場合、 **[PIP Role]\(PIP ロール\)** を変更できます。

   ![選択された PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-selected-pip.png)

これらの手順を完了したら、RosettaNet メッセージを送受信する準備ができています。

## <a name="rosettanet-templates"></a>RosettaNet テンプレート

開発を加速し、統合パターンを推奨するために、RosettaNet メッセージをデコードおよびエンコードするためのロジック アプリ テンプレートを使用できます。 ロジック アプリを作成するときは、Logic App Designer のテンプレート ギャラリーから選択できます。 これらのテンプレートは、[Azure Logic Apps の GitHub リポジトリ](https://github.com/Azure/logicapps)から検索することもできます。

![RosettaNet テンプレート](media/logic-apps-enterprise-integration-rosettanet/decode-encode-rosettanet-templates.png)

## <a name="receive-or-decode-rosettanet-messages"></a>RosettaNet メッセージを受信またはデコードする

1. [空のロジック アプリを作成します](quickstart-create-first-logic-app-workflow.md)。

1. ロジック アプリに[統合アカウントをリンク](logic-apps-enterprise-integration-create-integration-account.md#link-account)します。

1. RosettaNet メッセージをデコードするアクションを追加する前に、Request トリガーなど、ロジック アプリを起動するためのトリガーを追加する必要があります。

1. トリガーを追加した後、 **[New step]\(新しいステップ\)** を選択します。

   ![要求トリガーの追加](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. 検索ボックスに「rosettanet」と入力し、次のアクションを選択します: **[RosettaNet Decode]\(RosettaNet デコード\)**

   !["RosettaNet Decode" (RosettaNet デコード) アクションを検索して選択する](media/logic-apps-enterprise-integration-rosettanet/select-decode-rosettanet-action.png)

1. アクションのプロパティの情報を指定します。

   ![アクションの詳細を指定する](media/logic-apps-enterprise-integration-rosettanet/decode-action-details.png)

   | プロパティ | 必須 | 説明 |
   |----------|----------|-------------|
   | **メッセージ** | はい | デコードする RosettaNet メッセージ  |
   | **ヘッダー** | はい | バージョン (これは RNIF のバージョンです) と応答タイプ (パートナー間の通信の種類を示し、同期または非同期に設定できます) の値を提供する HTTP ヘッダー |
   | **ロール** | はい | PIP でのホスト パートナーのロール |
   ||||

   RosettaNet の [デコード] アクションからの出力には、他のプロパティに加えて**アウトバウンド シグナル**が含まれており、これを選択して、エンコードしてパートナーに送り返す、またはその出力に対して他のアクションを実行することができます。

## <a name="send-or-encode-rosettanet-messages"></a>RosettaNet メッセージを送信またはエンコードする

1. [空のロジック アプリを作成します](quickstart-create-first-logic-app-workflow.md)。

1. ロジック アプリに[統合アカウントをリンク](logic-apps-enterprise-integration-create-integration-account.md#link-account)します。

1. RosettaNet メッセージをエンコードするアクションを追加する前に、Request トリガーなど、ロジック アプリを起動するためのトリガーを追加する必要があります。

1. トリガーを追加した後、 **[New step]\(新しいステップ\)** を選択します。

   ![要求トリガーの追加](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. 検索ボックスに「rosettanet」と入力し、次のアクションを選択します: **[RosettaNet Encode]\(RosettaNet エンコード\)**

   !["RosettaNet Encode" (RosettaNet エンコード) アクションを検索して選択する](media/logic-apps-enterprise-integration-rosettanet/select-encode-rosettanet-action.png)

1. アクションのプロパティの情報を指定します。

   ![アクションの詳細を指定する](media/logic-apps-enterprise-integration-rosettanet/encode-action-details.png)

   | プロパティ | 必須 | 説明 |
   |----------|----------|-------------|
   | **メッセージ** | はい | エンコードする RosettaNet メッセージ  |
   | **Host Partner** (ホスト パートナー) | はい | ホスト パートナーの名前 |
   | **Guest Partner** (ゲスト パートナー) | はい | ゲスト パートナーの名前 |
   | **PIP code** (PIP コード) | はい | PIP コード |
   | **PIP version** (PIP バージョン) | はい | PIP バージョン |  
   | **PIP instance identity** (PIP インスタンス ID) | はい | この PIP メッセージの一意識別子 |  
   | **Message type** (メッセージの種類) | はい | エンコードするメッセージの種類 |  
   | **ロール** | はい | ホスト パートナーのロール |
   ||||

   エンコードされたメッセージをパートナーに送信する準備ができました。

1. エンコードされたメッセージを送信するために、この例では、"HTTP - Send encoded message to partner" (HTTP - エンコードしたメッセージをパートナーに送信) という名前に変更された **[HTTP]** アクションを使用します。

   ![RosettaNet メッセージを送信する [HTTP] アクション](media/logic-apps-enterprise-integration-rosettanet/send-rosettanet-message-to-partner.png)

   RosettaNet の標準に従って、ビジネス トランザクションは、PIP で定義されているすべてのステップが完了した時点ではじめて、完了したと見なされます。

1. エンコードしたメッセージをホストがパートナーに送信した後、ホストはシグナルと受信確認を待ちます。 このタスクを完了するために、 **[RosettaNet wait for response]\(RosettaNet 応答を待つ\)** アクションを追加します。

   !["RosettaNet wait for response" (RosettaNet 応答を待つ) アクションを追加する](media/logic-apps-enterprise-integration-rosettanet/rosettanet-wait-for-response-action.png)

   待機に使用する期間と再試行回数は、統合アカウントでの PIP 構成に基づきます。 応答が受信されない場合、このアクションは失敗通知を生成します。 再試行を処理するために、常に **[エンコード]** および **[Wait for response]\(応答を待つ\)** アクションを **[Until]\(期限\)** ループに配置します。

   ![RosettaNet アクションを含む [Until] (期限) ループ](media/logic-apps-enterprise-integration-rosettanet/rosettanet-loop.png)

## <a name="next-steps"></a>次の手順

* [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) を使用して、検証や変換など、各種のメッセージ操作を実行する方法を確認します。
* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。
