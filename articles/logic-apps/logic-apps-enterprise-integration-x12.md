---
title: B2B の X12 メッセージを送受信する
description: Azure Logic Apps と Enterprise Integration Pack を使用して B2B エンタープライズ統合シナリオ用の X12 メッセージを交換します
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/29/2020
ms.openlocfilehash: 61f3f2af61bc24f76d061de672a3eaacd54f7f0e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96015198"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Azure Logic Apps と Enterprise Integration Pack で B2B エンタープライズ統合用の X12 メッセージを交換する

Azure Logic Apps で X12 メッセージを操作するには、X12 コネクタを使用できます。これには、X12 通信を管理するためのトリガーとアクションが用意されています。 EDIFACT メッセージの詳細については、代わりに、[EDIFACT メッセージの交換](logic-apps-enterprise-integration-edifact.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* X12 コネクタを使用するロジック アプリと、ロジック アプリのワークフローを開始するトリガー。 X12 コネクタでは、アクションのみが提供され、トリガーは提供されません。 ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」と[クイック スタートの初めてのロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。

* ご利用の Azure サブスクリプションに関連付けられており、X12 コネクタの使用を計画しているロジック アプリにリンクされている[統合アカウント](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。 使用するロジックアプリと統合アカウントは両方とも同じ場所または同じ Azure リージョンに存在する必要があります。

* X12 ID 修飾子を使用して統合アカウントに既に定義済みの、少なくとも 2 つの[取引先](../logic-apps/logic-apps-enterprise-integration-partners.md)。

* 統合アカウントに既に追加している、XML 検証に使用する[スキーマ](../logic-apps/logic-apps-enterprise-integration-schemas.md)。 医療保険の携行性と責任に関する法律 (HIPAA) スキーマを操作する場合は、[HIPAA スキーマ](#hipaa-schemas)に関する記事を参照してください。

* X12 コネクタを使用するには、事前に取引先との間で X12 [契約](../logic-apps/logic-apps-enterprise-integration-agreements.md)を作成し、その契約を統合アカウントに格納しておく必要があります。 医療保険の携行性と責任に関する法律 (HIPAA) スキーマを操作する場合は、契約に `schemaReferences` セクションを追加する必要があります。 詳細については、[HIPAA スキーマ](#hipaa-schemas)に関する記事を参照してください。

<a name="receive-settings"></a>

## <a name="receive-settings"></a>受信設定

契約のプロパティを設定した後、この契約でパートナーから受信する受信メッセージを識別して処理する方法を構成できます。

1. **[追加]** で、 **[受信設定]** をクリックします。

1. メッセージを交換するパートナーとの契約に基づいて、これらのプロパティを構成します。 **[受信設定]** は次のセクションに分かれています。

   * [識別子](#inbound-identifiers)
   * [受信確認](#inbound-acknowledgement)
   * [スキーマ](#inbound-schemas)
   * [エンベロープ](#inbound-envelopes)
   * [制御番号](#inbound-control-numbers)
   * [Validations](#inbound-validations)
   * [内部設定](#inbound-internal-settings)

   プロパティの説明については、このセクションの表を参照してください。

1. 完了したら、 **[OK]** を選択して、必ず設定を保存します。

<a name="inbound-identifiers"></a>

### <a name="receive-settings---identifiers"></a>受信設定 - 識別子

![受信メッセージの識別子のプロパティ](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-identifiers.png)

| プロパティ | 説明 |
|----------|-------------|
| **ISA1 (Authorization Qualifier) (ISA1 (認証修飾子))** | 使用する認証修飾子の値。 既定値は、 **[00 - No Authorization Information Present]\(00 - 認証情報が存在しません\)** です。 <p>**注**:他の値を選択する場合は、**ISA2** プロパティの値を指定します。 |
| **ISA2** | **ISA1** プロパティが **[00 - No Authorization Information Present]\(00 - 認証情報が存在しません\)** でない場合に使用する認証情報の値。 このプロパティ値には、1 文字以上で 10 文字以下の英数字を入力する必要があります。 |
| **ISA3 (Security Qualifier) (ISA3 (セキュリティ修飾子))** | 使用するセキュリティ修飾子の値。 既定値は、 **[00 - No Security Information Present]\(00 - セキュリティ情報が存在しません\)** です。 <p>**注**:他の値を選択する場合は、**ISA4** プロパティの値を指定します。 |
| **ISA4** | **ISA3** プロパティが **[00 - No Security Information Present]\(00 - セキュリティ情報が存在しません\)** でない場合に使用するセキュリティ情報の値。 このプロパティ値には、1 文字以上で 10 文字以下の英数字を入力する必要があります。 |
|||

<a name="inbound-acknowledgement"></a>

### <a name="receive-settings---acknowledgement"></a>受信設定 - 受信確認

![受信メッセージの受信確認](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-acknowledgement.png)

| プロパティ | 説明 |
|----------|-------------|
| **TA1 Expected (TA1 が必要)** | インターチェンジの送信者に技術確認 (TA1) を返します。 |
| **FA Expected (FA が必要)** | インターチェンジの送信者に機能確認 (FA) を返します。 <p>**FA Version (FA バージョン)** プロパティについては、スキーマのバージョンに基づいて、997 または 999 の受信確認を選択します。 <p>受理されたトランザクション セットの機能確認で AK2 ループの生成を有効にするには、 **[Include AK2 / IK2 Loop]\(AK2 / IK2 ループを含める\)** を選択します。 |
||||

<a name="inbound-schemas"></a>

### <a name="receive-settings---schemas"></a>受信設定 - スキーマ

![受信メッセージのスキーマ](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-schemas.png)

このセクションについては、各トランザクションの種類 (ST01) と送信側アプリケーション (GS02) に対して[統合アカウント](./logic-apps-enterprise-integration-create-integration-account.md)から[スキーマ](../logic-apps/logic-apps-enterprise-integration-schemas.md)を選択します。 EDI 受信パイプラインは、このセクションで設定された値とスキーマを、受信メッセージの ST01 と GS02 の値および受信メッセージのスキーマと照合することにより、受信メッセージを逆アセンブルします。 行を 1 つ完了するたびに、新しい空の行が自動的に表示されます。

| プロパティ | 説明 |
|----------|-------------|
| **Version** | スキーマの X12 バージョン |
| **Transaction Type (ST01) (トランザクションの種類 (ST01))** | トランザクションの種類 |
| **Sender Application (GS02) (送信側アプリケーション (GS02))** | 送信側アプリケーション |
| **[スキーマ]** | 使用するスキーマ ファイル |
|||

<a name="inbound-envelopes"></a>

### <a name="receive-settings---envelopes"></a>受信設定 - エンベロープ

![受信メッセージのトランザクション セットで使用する区切り記号](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-envelopes.png)

| プロパティ | 説明 |
|----------|-------------|
| **ISA11 Usage (ISA11 の使用)** | トランザクション セットで使用する区切り記号: <p>- **標準識別子**: EDI 受信パイプラインの受信ドキュメントの 10 進数表記ではなく、ピリオド (.) を 10 進数表記に使用します。 <p>- **繰り返し区切り記号**: 繰り返し出現する単純なデータ要素または繰り返されるデータ構造体の区切り記号を指定します。 たとえば、繰り返し区切り記号としては通常、キャレット (^) が使用されます。 HIPAA スキーマに対しては、キャレットのみ使用できます。 |
|||

<a name="inbound-control-numbers"></a>

### <a name="receive-settings---control-numbers"></a>受信設定 - 制御番号

![受信メッセージでの制御番号の重複の処理](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-control-numbers.png) 

| プロパティ | 説明 |
|----------|-------------|
| **Disallow Interchange control number duplicates (インターチェンジ制御番号の重複を許可しない)** | 重複したインターチェンジを防止します。 受信したインターチェンジ制御番号に対して、インターチェンジ制御番号 (ISA13) をチェックします。 一致が検出されると、EDI 受信パイプラインはインターチェンジを処理しません。 <p><p>チェックを実行する日数を指定するには、**Check for duplicate ISA13 every (days)\(ISA13 の重複の確認間隔 (日単位)\)** プロパティに値を指定します。 |
| **Disallow Group control number duplicates (グループ制御番号の重複を許可しない)** | 重複するグループ制御番号を持つインターチェンジをブロックします。 |
| **Disallow Transaction set control number duplicates (トランザクション セット制御番号の重複を許可しない)** | 重複するトランザクション セット制御番号を持つインターチェンジをブロックします。 |
|||

<a name="inbound-validations"></a>

### <a name="receive-settings---validations"></a>受信設定 - 検証

![受信メッセージの検証](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-validations.png)

**[Default]\(既定\)** 行には、EDI メッセージ型に使用される検証ルールが表示されます。 別のルールを定義する場合は、ルールを **true** に設定する各ボックスを選択します。 行を 1 つ完了するたびに、新しい空の行が自動的に表示されます。

| プロパティ | 説明 |
|----------|-------------|
| **Message Type (メッセージ型)** | EDI メッセージ型 |
| **EDI Validation (EDI 検証)** | スキーマの EDI プロパティ、長さ制限、空のデータ要素、および末尾の区切り記号で定義されたデータ型で EDI 検証を実行します。 |
| **Extended Validation (拡張検証)** | データ型が EDI でない場合は、検証はデータ要素の要件と許可される繰り返し、列挙、およびデータ要素の長さの検証 (最小または最大) に対して実行されます。 |
| **Allow Leading/Trailing Zeroes (先頭および末尾のゼロを許可)** | 追加の先頭または末尾のゼロと空白文字をすべて保持します。 これらの文字を削除しません。 |
| **Trim Leading/Trailing Zeroes (先頭および末尾のゼロをトリミングする)** | 先頭または末尾のゼロと空白文字をすべて削除します。 |
| **末尾の区切り記号のポリシー** | 末尾の区切り記号を生成します。 <p>- **Not Allowed (禁止)** : 受信インターチェンジの末尾の区切り記号や区切り文字を禁止します。 インターチェンジに末尾の区切り記号や区切り文字が含まれている場合、インターチェンジは無効と宣言されます。 <p>- **Optional (省略可能)** : 末尾の区切り記号や区切り文字の有無に関係なくインターチェンジを受け入れます。 <p>- **Mandatory (必須)** : 受信インターチェンジは、末尾の区切り記号や区切り文字を持っている必要があります。 |
|||

<a name="inbound-internal-settings"></a>

### <a name="receive-settings---internal-settings"></a>受信設定 - 内部設定

![受信メッセージの内部設定](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-internal-settings.png)

| プロパティ | 説明 |
|----------|-------------|
| **Convert implied decimal format Nn to a base 10 numeric value (暗黙的に指定された 10 進形式 Nn を 底 10 の数値に変換する)** | 形式 "Nn" で指定されている EDI 番号を底 10 の数値に変換します。 |
| **Create empty XML tags if trailing separators are allowed (末尾の区切り記号が許可されている場合は空の XML タグを作成する)** | インターチェンジの送信者に、末尾の区切り記号用の空の XML タグを含めさせます。 |
| **Split Interchange as transaction sets - suspend transaction sets on error (インターチェンジをトランザクション セットとして分割 - エラー発生時にトランザクション セットを中断)** | トランザクション セットに適切なエンベロープを適用することで、インターチェンジ内の各トランザクション セットを個別の XML ドキュメントで解析します。 検証に失敗したトランザクションのみを中断します。 |
| **Split Interchange as transaction sets - suspend interchange on error (インターチェンジをトランザクション セットとして分割 - エラー発生時にインターチェンジを中断)** | 適切なエンベロープを適用することで、インターチェンジ内の各トランザクション セットを個別の XML ドキュメントで解析します。 インターチェンジの 1 つ以上のトランザクション セットが検証に失敗した場合、インターチェンジ全体を中断します。 |
| **Preserve Interchange - suspend transaction sets on error (インターチェンジの保存 - エラー発生時にトランザクション セットを中断)** | インターチェンジはそのままにして、バッチ インターチェンジ全体に対する XML ドキュメントを作成します。 検証に失敗したトランザクション セットだけを中断し、他のすべてのトランザクション セットの処理を継続します。 |
| **Preserve Interchange - suspend interchange on error (インターチェンジの保存 - エラー発生時にインターチェンジを中断)** |インターチェンジはそのままにして、バッチ インターチェンジ全体に対する XML ドキュメントを作成します。 インターチェンジの 1 つ以上のトランザクション セットが検証に失敗した場合、インターチェンジ全体を中断します。 |
|||

<a name="send-settings"></a>

## <a name="send-settings"></a>Send Settings (送信の設定)

契約のプロパティを設定した後、この契約でパートナーに送信する送信メッセージを識別して処理する方法を構成できます。

1. **[追加]** で、 **[送信設定]** をクリックします。

1. メッセージを交換するパートナーとの契約に基づいて、これらのプロパティを構成します。 プロパティの説明については、このセクションの表を参照してください。

   **[送信設定]** は次のセクションに分かれています。

   * [識別子](#outbound-identifiers)
   * [受信確認](#outbound-acknowledgement)
   * [スキーマ](#outbound-schemas)
   * [エンベロープ](#outbound-envelopes)
   * [制御バージョン番号](#outbound-control-version-number)
   * [制御番号](#outbound-control-numbers)
   * [文字セットと区切り記号](#outbound-character-sets-separators)
   * [検証](#outbound-validation)

1. 完了したら、 **[OK]** を選択して、必ず設定を保存します。

<a name="outbound-identifiers"></a>

### <a name="send-settings---identifiers"></a>送信設定 - 識別子

![送信メッセージの識別子のプロパティ](./media/logic-apps-enterprise-integration-x12/x12-send-settings-identifiers.png)

| プロパティ | 説明 |
|----------|-------------|
| **ISA1 (Authorization Qualifier) (ISA1 (認証修飾子))** | 使用する認証修飾子の値。 既定値は、 **[00 - No Authorization Information Present]\(00 - 認証情報が存在しません\)** です。 <p>**注**:他の値を選択する場合は、**ISA2** プロパティの値を指定します。 |
| **ISA2** | **ISA1** プロパティが **[00 - No Authorization Information Present]\(00 - 認証情報が存在しません\)** でない場合に使用する認証情報の値。 このプロパティ値には、1 文字以上で 10 文字以下の英数字を入力する必要があります。 |
| **ISA3 (Security Qualifier) (ISA3 (セキュリティ修飾子))** | 使用するセキュリティ修飾子の値。 既定値は、 **[00 - No Security Information Present]\(00 - セキュリティ情報が存在しません\)** です。 <p>**注**:他の値を選択する場合は、**ISA4** プロパティの値を指定します。 |
| **ISA4** | **ISA3** プロパティが **[00 - No Security Information Present]\(00 - セキュリティ情報が存在しません\)** でない場合に使用するセキュリティ情報の値。 このプロパティ値には、1 文字以上で 10 文字以下の英数字を入力する必要があります。 |
|||

<a name="outbound-acknowledgement"></a>

### <a name="send-settings---acknowledgement"></a>送信設定 - 受信確認

![送信メッセージの受信確認プロパティ](./media/logic-apps-enterprise-integration-x12/x12-send-settings-acknowledgement.png)

| プロパティ | 説明 |
|----------|-------------|
| **TA1 Expected (TA1 が必要)** | インターチェンジの送信者に技術確認 (TA1) を返します。 <p>この設定は、メッセージを送信しているホスト パートナーが、契約でゲスト パートナーからの受信確認を要求することを指定します。 ホスト パートナーは、契約の [受信設定] に基づいてこれらの受信確認を想定しています。 |
| **FA Expected (FA が必要)** | インターチェンジの送信者に機能確認 (FA) を返します。 **FA Version (FA バージョン)** プロパティについては、スキーマのバージョンに基づいて、997 または 999 の受信確認を選択します。 <p>この設定は、メッセージを送信しているホスト パートナーが契約でゲスト パートナーからの受信確認を要求することを指定します。 ホスト パートナーは、契約の [受信設定] に基づいてこれらの受信確認を想定しています。 |
|||

<a name="outbound-schemas"></a>

### <a name="send-settings---schemas"></a>送信設定 - スキーマ

![送信メッセージのスキーマ](./media/logic-apps-enterprise-integration-x12/x12-send-settings-schemas.png)

このセクションについては、各トランザクションの種類 (ST01) に対して[統合アカウント](./logic-apps-enterprise-integration-create-integration-account.md)から[スキーマ](../logic-apps/logic-apps-enterprise-integration-schemas.md)を選択します。 行を 1 つ完了するたびに、新しい空の行が自動的に表示されます。

| プロパティ | 説明 |
|----------|-------------|
| **Version** | スキーマの X12 バージョン |
| **Transaction Type (ST01) (トランザクションの種類 (ST01))** | スキーマのトランザクションの種類 |
| **[スキーマ]** | 使用するスキーマ ファイル。 最初にスキーマを選択すると、バージョンとトランザクションの種類が自動的に設定されます。 |
|||

<a name="outbound-envelopes"></a>

### <a name="send-settings---envelopes"></a>送信設定 - エンベロープ

![送信メッセージに使用するトランザクション セット内の区切り記号](./media/logic-apps-enterprise-integration-x12/x12-send-settings-envelopes.png)

| プロパティ | 説明 |
|----------|-------------|
| **ISA11 Usage (ISA11 の使用)** | トランザクション セットで使用する区切り記号: <p>- **Standard Identifier (標準識別子)** : EDI 送信パイプラインの送信ドキュメントの 10 進数表記ではなく、ピリオド (.) を 10 進数表記に使用します。 <p>- **Repetition Separator (繰り返し区切り記号)** : 繰り返し出現する単純なデータ要素または繰り返されるデータ構造体の区切り記号を指定します。 たとえば、繰り返し区切り記号としては通常、キャレット (^) が使用されます。 HIPAA スキーマに対しては、キャレットのみ使用できます。 |
|||

<a name="outbound-control-version-number"></a>

### <a name="send-settings---control-version-number"></a>送信設定 - 制御バージョン番号

![送信メッセージの制御バージョン番号](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-version-number.png)

このセクションについては、各インターチェンジに対して[統合アカウント](./logic-apps-enterprise-integration-create-integration-account.md)から[スキーマ](../logic-apps/logic-apps-enterprise-integration-schemas.md)を選択します。 行を 1 つ完了するたびに、新しい空の行が自動的に表示されます。

| プロパティ | 説明 |
|----------|-------------|
| **Control Version Number (ISA12) (制御バージョン番号 (ISA12))** | X12 標準のバージョン |
| **Usage Indicator (ISA15) (使用状況インジケーター (ISA15))** | インターチェンジのコンテキスト。 **[Test]\(テスト\)** データ、 **[Information]\(情報\)** データ、または **[Production]\(運用\)** データのいずれか |
| **[スキーマ]** | EDI 送信パイプラインに送信される X12 エンコード インターチェンジの GS および ST セグメントの生成に使用するスキーマ。 |
| **GS1** | 省略可能。機能コードを選択します。 |
| **GS2** | 省略可能。アプリケーション送信者を指定します。 |
| **GS3** | 省略可能。アプリケーション受信者を指定します。 |
| **GS4** | 省略可能。 **[CCYYMMDD]** または **[YYMMDD]** を選択します。 |
| **GS5** | 省略可能。 **[HHMM]** 、 **[HHMMSS]** 、または **[HHMMSSdd]** を選択します。 |
| **GS7** | 省略可能。担当機関の値を選択します。 |
| **GS8** | 省略可能。スキーマ ドキュメントのバージョンを指定します。 |
|||

<a name="outbound-control-numbers"></a>

### <a name="send-settings---control-numbers"></a>送信設定 - 制御番号

![送信メッセージの制御番号](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-numbers.png)

| プロパティ | 説明 |
|----------|-------------|
| **Interchange Control Number (ISA13) (インターチェンジ制御番号 (ISA13))** | インターチェンジ制御番号の値の範囲。最小値は 1、最大値は 999999999 です |
| **Group Control Number (GS06) (グループ制御番号 (GS06))** | グループ制御番号の値の範囲。最小値は 1、最大値は 999999999 です |
| **Transaction Set Control Number (ST02) (トランザクション セット制御番号 (ST02))** | トランザクション セット制御番号の値の範囲。最小値は 1、最大値は 999999999 です <p>- **Prefix (プレフィックス)** : 省略可能。英数字の値 <br>- **Suffix (サフィックス)** : 省略可能。英数字の値 |
|||

<a name="outbound-character-sets-separators"></a>

### <a name="send-settings---character-sets-and-separators"></a>送信設定 - 文字セットと区切り文字

![送信メッセージのメッセージ型の区切り記号](./media/logic-apps-enterprise-integration-x12/x12-send-settings-character-sets-separators.png)

**[Default]\(既定\)** 行には、メッセージ スキーマの区切り記号として使用される文字セットが表示されます。 **既定** の文字セットを使用しない場合は、メッセージ型ごとに異なる区切り記号セットを入力できます。 行を 1 つ完了するたびに、新しい空の行が自動的に表示されます。

> [!TIP]
> 特殊文字の値を指定するには、契約を JSON として編集し、特殊文字の ASCII 値を指定します。

| プロパティ | 説明 |
|----------|-------------|
| **Character Set to be used (使用する文字セット)** | X12 文字セット。 **[Basic]\(基本)\** 、 **[Extended]\(拡張\)** 、または **[UTF8]** のいずれか。 |
| **[スキーマ]** | 使用するスキーマ。 スキーマを選択した後、次の区切り記号の説明に基づいて、使用する文字セットを選択します。 |
| **[Input Type]\(入力の種類\)** | 文字セットの入力の種類 |
| **Component Separator (コンポーネントの区切り記号)** | 複合データ要素を区切る 1 文字 |
| **Data Element Separator (データ要素区切り記号)** | 複合データ内の単純データ要素を区切る 1 文字 |
| **replacement Character Separator (置換文字の区切り記号)** | X12 送信メッセージを生成するときにペイロード データ内のすべての区切り文字を置換する置換文字 |
| **Segment Terminator (セグメント終端記号)** | EDI セグメントの終端を示す 1 文字 |
| **サフィックス** | セグメント識別子と共に使用する文字。 サフィックスを指定する場合、セグメント終端記号のデータ要素は空にできます。 セグメント終端記号を空のままにする場合は、サフィックスを指定する必要があります。 |
|||

<a name="outbound-validation"></a>

### <a name="send-settings---validation"></a>送信設定 - 検証

![送信メッセージの検証プロパティ](./media/logic-apps-enterprise-integration-x12/x12-send-settings-validation.png) 

**[Default]\(既定\)** 行には、EDI メッセージ型に使用される検証ルールが表示されます。 別のルールを定義する場合は、ルールを **true** に設定する各ボックスを選択します。 行を 1 つ完了するたびに、新しい空の行が自動的に表示されます。

| プロパティ | 説明 |
|----------|-------------|
| **Message Type (メッセージ型)** | EDI メッセージ型 |
| **EDI Validation (EDI 検証)** | スキーマの EDI プロパティ、長さ制限、空のデータ要素、および末尾の区切り記号で定義されたデータ型で EDI 検証を実行します。 |
| **Extended Validation (拡張検証)** | データ型が EDI でない場合は、検証はデータ要素の要件と許可される繰り返し、列挙、およびデータ要素の長さの検証 (最小または最大) に対して実行されます。 |
| **Allow Leading/Trailing Zeroes (先頭および末尾のゼロを許可)** | 追加の先頭または末尾のゼロと空白文字をすべて保持します。 これらの文字を削除しません。 |
| **Trim Leading/Trailing Zeroes (先頭および末尾のゼロをトリミングする)** | 先頭または末尾のゼロと空白文字をすべて削除します。 |
| **末尾の区切り記号のポリシー** | 末尾の区切り記号を生成します。 <p>- **Not Allowed (禁止)** : 送信インターチェンジの末尾の区切り記号や区切り文字を禁止します。 インターチェンジに末尾の区切り記号や区切り文字が含まれている場合、インターチェンジは無効と宣言されます。 <p>- **Optional (省略可能)** : 末尾の区切り記号や区切り文字の有無に関係なくインターチェンジを送信します。 <p>- **Mandatory (必須)** : 送信インターチェンジは、末尾の区切り記号や区切り文字を持っている必要があります。 |
|||

<a name="hipaa-schemas"></a>

## <a name="hipaa-schemas-and-message-types"></a>HIPAA スキーマとメッセージ型

HIPAA スキーマと 277 または 837 のメッセージ型を操作する場合は、いくつかの追加手順を実行する必要があります。 これらのメッセージ型の[ドキュメント バージョン番号 (GS8)](#outbound-control-version-number) には、9 文字を超える文字 (たとえば、"005010X222A1") が含まれます。 また、一部のドキュメント バージョン番号は、バリアント メッセージ型にマップされます。 スキーマおよび契約で正しいメッセージ型を参照していない場合は、次のエラー メッセージが表示されます。

`"The message has an unknown document type and did not resolve to any of the existing schemas configured in the agreement."`

次の表に、影響を受けるメッセージ型、バリアント、およびそれらのメッセージ型にマップされるドキュメント バージョン番号を示します。

| メッセージ型またはバリアント |  説明 | ドキュメント バージョン番号 (GS8) |
|-------------------------|--------------|-------------------------------|
| 277 | 医療保険情報の状態通知 | 005010X212 |
| 837_I | 健康保険請求: 機関 | 004010X096A1 <br>005010X223A1 <br>005010X223A2 |
| 837_D | 医療保険請求: 歯科 | 004010X097A1 <br>005010X224A1 <br>005010X224A2 |
| 837_P | 健康保険請求: 専門家 | 004010X098A1 <br>005010X222 <br>005010X222A1 |
|||

また、これらのドキュメント バージョン番号を使用するときは、文字の長さが無効であるというエラーが発生するため、EDI の検証を無効にする必要があります。

これらのドキュメント バージョン番号とメッセージ型を指定するには、次の手順を実行します。

1. HIPAA スキーマで、使用するドキュメント バージョン番号に対して、現在のメッセージ型をバリアント メッセージ型に置き換えます。

   たとえば、ドキュメント バージョン番号 `005010X222A1` を `837` メッセージ型で使用するとします。 スキーマでは、それぞれの `"X12_00501_837"` 値を、代わりに `"X12_00501_837_P"` 値に置き換えます。

   スキーマを更新するには、次の手順を実行します。

   1. Azure portal で統合アカウントに移動します。 スキーマを検索してダウンロードします。 メッセージ型を置き換えて、スキーマ ファイルの名前を変更し、変更したスキーマを統合アカウントにアップロードします。 詳細については、「[スキーマの編集](../logic-apps/logic-apps-enterprise-integration-schemas.md#edit-schemas)」を参照してください｡

   1. 契約のメッセージ設定で、変更後のスキーマを選択します。

1. 契約の `schemaReferences` オブジェクトで、ドキュメント バージョン番号と一致するバリアント メッセージ型を指定する別のエントリを追加します。

   たとえば、ドキュメント バージョン番号 `005010X222A1` を `837` メッセージ型で使用するとします。 契約には、次のプロパティと値を含む `schemaReferences` セクションがあります。

   ```json
   "schemaReferences": [
      {
         "messageId": "837",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837"
      }
   ]
   ```

   この `schemaReferences` セクションに、次の値を持つ別のエントリを追加します。

   * `"messageId": "837_P"`
   * `"schemaVersion": "00501"`
   * `"schemaName": "X12_00501_837_P"`

   完了すると、`schemaReferences` セクションは次のようになります。

   ```json
   "schemaReferences": [
      {
         "messageId": "837",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837"
      },
      {
         "messageId": "837_P",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837_P"
      }
   ]
   ```

1. 契約のメッセージ設定で、それぞれのメッセージ型、または **既定** の値を使用している場合はすべてのメッセージ型に対して **[EDI 検証]** チェックボックスをオフにして、EDI 検証を無効にします。

   ![すべてのメッセージ型、または各メッセージ型に対して検証を無効にする](./media/logic-apps-enterprise-integration-x12/x12-disable-validation.png) 

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの Swagger ファイルに記載されているアクションや制限など、このコネクタに関する追加の技術的詳細については、[コネクタの参照ページ](/connectors/x12/)をご覧ください。

> [!NOTE]
> [統合サービス環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) のロジック アプリの場合、ISE のラベルが付いたこのバージョンのコネクタでは、代わりに [ISE の B2B メッセージ制限](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits)が使用されます。

## <a name="next-steps"></a>次のステップ

* [Logic Apps の他のコネクタ](../connectors/apis-list.md)の詳細情報
