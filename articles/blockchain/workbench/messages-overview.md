---
title: メッセージを使用して Azure Blockchain Workbench と統合する
description: メッセージを使用して Azure Blockchain Workbench Preview を他のシステムと統合する方法の概要。
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 14bd0f84bc9490d95d3dbe0b9f122882f0d2059d
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2019
ms.locfileid: "74324504"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Azure Blockchain Workbench のメッセージング統合

Azure Blockchain Workbench は、REST API を提供するだけでなく、メッセージング ベースの統合も提供します。 Workbench は Azure Event Grid を介して台帳中心のイベントを発行し、ダウンストリームの消費者がこれらのイベントに基づいてデータを取り込んだり、行動を起こしたりすることができるようにします。 信頼性の高いメッセージングを必要とするクライアントの場合、Azure Blockchain Workbench は Azure Service Bus エンドポイントにもメッセージを送信します。

## <a name="input-apis"></a>入力 API

ユーザーの作成、コントラクトの作成、およびコントラクトの更新のために外部システムからトランザクションを開始する場合は、メッセージング入力 API を使用して、台帳に対するトランザクションを実行できます。 入力 API の使用方法を示すサンプルについては、[メッセージング統合のサンプル](https://aka.ms/blockchain-workbench-integration-sample)をご覧ください。

次に示すのは、現在使用可能な入力 API です。

### <a name="create-user"></a>ユーザーの作成

新しいユーザーを作成します。

要求には次のフィールドが必要です。

| **Name**             | **説明**                                      |
|----------------------|------------------------------------------------------|
| requestId            | クライアントによって指定された GUID                                |
| firstName            | ユーザーの名                              |
| lastName             | ユーザーの姓                               |
| emailAddress         | ユーザーの電子メール アドレス                           |
| externalId           | ユーザーの Azure AD オブジェクト ID                      |
| connectionId         | ブロックチェーン接続の一意識別子 |
| messageSchemaVersion | メッセージング スキーマ バージョン                            |
| messageName          | **CreateUserRequest**                               |

例:

``` json
{
    "requestId": "e2264523-6147-41fc-bbbb-edba8e44562d",
    "firstName": "Ali",
    "lastName": "Alio",
    "emailAddress": "aa@contoso.com",
    "externalId": "6a9b7f65-ffff-442f-b3b8-58a35abd1bcd",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateUserRequest"
}
```

Blockchain Workbench は、次のフィールドを含む応答を返します。

| **Name**              | **説明**                                                                                                             |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------|
| requestId             | クライアントによって指定された GUID |
| userId                | 作成されたユーザーの ID |
| userChainIdentifier   | ブロックチェーン ネットワーク上に作成されたユーザーのアドレス。 Ethereum では、このアドレスはユーザーの**オン チェーン** アドレスです。 |
| connectionId          | ブロックチェーン接続の一意識別子|
| messageSchemaVersion  | メッセージング スキーマ バージョン |
| messageName           | **CreateUserUpdate** |
| status                | ユーザー作成要求の状態。  成功した場合、値は **Success** です。 失敗した場合、値は **Failure** です。     |
| additionalInformation | 状態に基づいて提供される追加情報 |

**ユーザー作成**が成功した場合の Blockchain Workbench からの応答例:

``` json
{ 
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Success", 
    "additionalInformation": { } 
} 
```

要求が成功しなかった場合は、追加情報にエラーの詳細が示されます。

``` json
{
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": null, 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Failure", 
    "additionalInformation": { 
        "errorCode": 4000, 
        "errorMessage": "User cannot be provisioned on connection." 
    }
}
```

### <a name="create-contract"></a>コントラクトの作成

新しいコントラクトを作成します。

要求には次のフィールドが必要です。

| **Name**             | **説明**                                                                                                           |
|----------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId            | クライアントによって指定された GUID |
| userChainIdentifier  | ブロックチェーン ネットワーク上に作成されたユーザーのアドレス。 Ethereum では、このアドレスはユーザーの**オン チェーン** アドレスです。 |
| applicationName      | アプリケーションの名前 |
| version              | アプリケーションのバージョン。 複数のバージョンのアプリケーションを有効にしている場合は必須です。 それ以外の場合、バージョンは省略可能です。 アプリケーションのバージョン管理の詳細については、「[Azure Blockchain Workbench アプリケーションのバージョン管理](version-app.md)」を参照してください。 |
| workflowName         | ワークフローの名前 |
| parameters           | コントラクト作成のために入力されたパラメーター |
| connectionId         | ブロックチェーン接続の一意識別子 |
| messageSchemaVersion | メッセージング スキーマ バージョン |
| messageName          | **CreateContractRequest** |

例:

``` json
{ 
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211", 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "applicationName": "AssetTransfer",
    "version": "1.0",
    "workflowName": "AssetTransfer", 
    "parameters": [ 
        { 
            "name": "description", 
            "value": "a 1969 dodge charger" 
        }, 
        { 
            "name": "price", 
            "value": "12345" 
        } 
    ], 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateContractRequest" 
}
```

Blockchain Workbench は、次のフィールドを含む応答を返します。

| **Name**                 | **説明**                                                                   |
|--------------------------|-----------------------------------------------------------------------------------|
| requestId                | クライアントによって指定された GUID                                                             |
| contractId               | Azure Blockchain Workbench 内のコントラクトの一意の識別子 |
| contractLedgerIdentifier | 台帳のコントラクトのアドレス                                            |
| connectionId             | ブロックチェーン接続の一意識別子                               |
| messageSchemaVersion     | メッセージング スキーマ バージョン                                                         |
| messageName              | **CreateContractUpdate**                                                      |
| status                   | コントラクト作成要求の状態。  指定できる値**Submitted**、**Committed**、**Failure**。  |
| additionalInformation    | 状態に基づいて提供される追加情報                              |

**コントラクト作成**が送信された後の Blockchain Workbench からの応答例。

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

**コントラクト作成**がコミットされた後の Blockchain Workbench からの応答例。

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

要求が成功しなかった場合は、追加情報にエラーの詳細が示されます。

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": null,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Failure",
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract cannot be provisioned on connection."
    }
}
```

### <a name="create-contract-action"></a>コントラクト アクションの作成

新しいコントラクト アクションを作成します。

要求には次のフィールドが必要です。

| **Name**                 | **説明**                                                                                                           |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId                | クライアントによって指定された GUID |
| userChainIdentifier      | ブロックチェーン ネットワーク上に作成されたユーザーのアドレス。 Ethereum では、このアドレスはユーザーの**オン チェーン** アドレスです。 |
| contractLedgerIdentifier | 台帳のコントラクトのアドレス |
| version                  | アプリケーションのバージョン。 複数のバージョンのアプリケーションを有効にしている場合は必須です。 それ以外の場合、バージョンは省略可能です。 アプリケーションのバージョン管理の詳細については、「[Azure Blockchain Workbench アプリケーションのバージョン管理](version-app.md)」を参照してください。 |
| workflowFunctionName     | ワークフロー関数の名前 |
| parameters               | コントラクト作成のために入力されたパラメーター |
| connectionId             | ブロックチェーン接続の一意識別子 |
| messageSchemaVersion     | メッセージング スキーマ バージョン |
| messageName              | **CreateContractActionRequest** |

例:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398",
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "version": "1.0",
    "workflowFunctionName": "modify",
    "parameters": [
        {
            "name": "description",
            "value": "a 1969 dodge charger"
        },
        {
            "name": "price",
            "value": "12345"
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionRequest"
}
```

Blockchain Workbench は、次のフィールドを含む応答を返します。

| **Name**              | **説明**                                                                   |
|-----------------------|-----------------------------------------------------------------------------------|
| requestId             | クライアントによって指定された GUID|
| contractId            | Azure Blockchain Workbench 内のコントラクトの一意の識別子 |
| connectionId          | ブロックチェーン接続の一意識別子 |
| messageSchemaVersion  | メッセージング スキーマ バージョン |
| messageName           | **CreateContractActionUpdate** |
| status                | コントラクト アクション要求の状態。 指定できる値**Submitted**、**Committed**、**Failure**。                         |
| additionalInformation | 状態に基づいて提供される追加情報 |

**コントラクト アクション作成**が送信された後の Blockchain Workbench からの応答例。

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

**コントラクト アクション作成**がコミットされた後の Blockchain Workbench からの応答例。

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

要求が成功しなかった場合は、追加情報にエラーの詳細が示されます。

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Failure",
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract action cannot be provisioned on connection."
    }
}
```

### <a name="input-api-error-codes-and-messages"></a>入力 API のエラー コードとメッセージ

**エラー コード 4000:無効な要求のエラー**
- Invalid connectionId \(無効な connectionId です\)
- CreateUserRequest deserialization failed \(CreateUserRequest のシリアル化を解除できませんでした\)
- CreateContractRequest deserialization failed \(CreateContractRequest のシリアル化を解除できませんでした\)
- CreateContractActionRequest deserialization failed \(CreateContractActionRequest のシリアル化を解除できませんでした\)
- Application {identified by application name} does not exist \({アプリケーション名によって識別された} アプリケーションが存在しません\)
- Application {identified by application name} does not have workflow \({アプリケーション名によって識別された} アプリケーションにワークフローがありません\)
- UserChainIdentifier does not exist \(UserChainIdentifier が存在しません\)
- Contract {identified by ledger identifier} does not exist \({台帳識別子によって識別された} コントラクトが存在しません\)
- Contract {identified by ledger identifier} does not have function {workflow function name} \({台帳識別子によって識別された} コントラクトには、{ワークフロー関数名} 関数がありません\)
- UserChainIdentifier does not exist \(UserChainIdentifier が存在しません\)

**エラー コード 4090:競合のエラー**
- ユーザーは既に存在します
- Contract already exists \(コントラクトは既に存在します\)
- Contract action already exists \(コントラクト アクションは既に存在します\)

**エラー コード 5000:内部サーバー エラー**
- 例外メッセージ

## <a name="event-notifications"></a>イベント通知

イベント通知を使用すると、Blockchain Workbench およびそれが接続されているブロックチェーン ネットワークで発生するイベントをユーザーとダウンストリーム システムに通知することができます。 イベント通知は、コードで直接使用するか、Logic Apps や Flow などのツールでダウンストリーム システムへのデータのフローをトリガーするために使用されます。

受け取ることができるさまざまなメッセージの詳細については、「[通知メッセージのリファレンス](#notification-message-reference)」を参照してください。

### <a name="consuming-event-grid-events-with-azure-functions"></a>Azure Functions での Event Grid イベントの使用

Blockchain Workbench で発生したイベントについて、ユーザーが Event Grid による通知を望む場合は、Azure Functions を使用して、Event Grid からのイベントを利用することができます。

1. Azure Portal で **Azure 関数アプリ**を作成します。
2. 新しい関数を作成します。
3. Event Grid のテンプレートを探します。 メッセージを読み取るための基本的なテンプレート コードが表示されます。 必要に応じてコードを変更します。
4. 関数を保存します。 
5. Blockchain Workbench のリソース グループから Event Grid を選択します。

### <a name="consuming-event-grid-events-with-logic-apps"></a>Logic Apps での Event Grid イベントの使用

1. Azure Portal で新しい **Azure Logic App** を作成します。
2. ポータルで Azure Logic App を開くときに、トリガーの選択を求めるメッセージが表示されます。 **[Azure Event Grid -- When a resource event occurs]\(Azure Event Grid -- リソース イベントが発生するとき\)** を選択します。
3. ワークフロー デザイナーが表示されるときに、サインインを求められます。
4. サブスクリプションを選択します。 リソースとして **Microsoft.EventGrid.Topics** を選択します。 Azure Blockchain Workbench リソース グループのリソースの名前の中から、**リソース名**を選択します。
5. Blockchain Workbench のリソース グループから Event Grid を選択します。

## <a name="using-service-bus-topics-for-notifications"></a>通知の Service Bus トピックの使用

Service Bus トピックは、Blockchain Workbench で発生するイベントについてユーザーに通知するために使用できます。 

1. Workbench のリソース グループ内の Service Bus に移動します。
2. **[トピック]** を選択します。
3. **[エグレストピック]** を選択します。
4. このトピックに新しいサブスクリプションを作成します。 それのキーを取得します。
5. このサブスクリプションからのイベントにサブスクライブするプログラムを作成します。

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Logic Apps による Service Bus メッセージの使用

1. Azure Portal で新しい **Azure Logic App** を作成します。
2. ポータルで Azure Logic App を開くときに、トリガーの選択を求めるメッセージが表示されます。 検索ボックスに「**Service Bus**」と入力し、Service Bus との対話の種類に適したトリガーを選択します。 たとえば、 **[Service Bus -- メッセージがトピック サブスクリプションに着信したとき (オート コンプリート)]** です。
3. ワークフロー デザイナーが表示されたら、Service Bus の接続情報を指定します。
4. サブスクリプションを選択し、**workbench-external** のトピックを指定します。
5. このトリガーからのメッセージを利用するアプリケーションのロジックを開発します。

## <a name="notification-message-reference"></a>通知メッセージのリファレンス

**messageName** に応じて、通知メッセージは次のいずれかの種類になります。

### <a name="block-message"></a>ブロック メッセージ

個々のブロックに関する情報が含まれます。 *BlockMessage* には、ブロック レベル情報のセクションとトランザクション情報のセクションが含まれます。

| 名前 | 説明 |
|------|-------------|
| block | [ブロック情報](#block-information)を含みます |
| transactions | ブロックのコレクション [トランザクション情報](#transaction-information)を含みます |
| connectionId | 接続の一意の識別子 |
| messageSchemaVersion | メッセージング スキーマ バージョン |
| messageName | **BlockMessage** |
| additionalInformation | 提供された追加情報 |

#### <a name="block-information"></a>ブロック情報

| 名前              | 説明 |
|-------------------|-------------|
| blockId           | Azure Blockchain Workbench 内のブロックの一意の識別子 |
| blockNumber       | 台帳のブロックの一意の識別子 |
| blockHash         | ブロックのハッシュ |
| previousBlockHash | 前のブロックのハッシュ |
| blockTimestamp    | ブロックのタイムスタンプ |

#### <a name="transaction-information"></a>トランザクション情報

| 名前               | 説明 |
|--------------------|-------------|
| transactionId      | Azure Blockchain Workbench 内のトランザクションの一意の識別子 |
| transactionHash    | 台帳のトランザクションのハッシュ |
| from               | 送信元トランザクションの台帳の一意の識別子 |
| to                 | 送信先トランザクションの台帳の一意の識別子 |
| provisioningStatus | トランザクションのプロビジョニング プロセスの現在の状態を示します。 次のいずれかの値になります。 </br>0 – データベースで API によってトランザクションが作成されている</br>1 – トランザクションが台帳に送信されている</br>2 – トランザクションが台帳に正常にコミットされている</br>3 または 4 - トランザクションの台帳へのコミットが失敗した</br>5 - トランザクションが台帳に正常にコミットされた |

Azure Blockchain Workbench からの *BlockMessage* の例:

``` json
{
    "block": {
        "blockId": 123,
        "blockNumber": 1738312,
        "blockHash": "0x03a39411e25e25b47d0ec6433b73b488554a4a5f6b1a253e0ac8a200d13fffff",
        "previousBlockHash": null,
        "blockTimestamp": "2018-10-09T23:35:58Z",
    },
    "transactions": [
        {
            "transactionId": 234,
            "transactionHash": "0xa4d9c95b581f299e41b8cc193dd742ef5a1d3a4ddf97bd11b80d123fec27ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": null,
            "provisioningStatus": 1
        },
        {
            "transactionId": 235,
            "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
            "from": "0xadd97e1e595916e29ea94fda894941574000ffff",
            "to": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff",
            "provisioningStatus": 2
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "BlockMessage",
    "additionalInformation": {}
}
```

### <a name="contract-message"></a>コントラクト メッセージ

コントラクトに関する情報が含まれます。 このメッセージには、コントラクト プロパティのセクションとトランザクション情報のセクションが含まれます。 特定のブロックのコントラクトを変更したすべてのトランザクションがトランザクション セクションに含まれます。

| 名前 | 説明 |
|------|-------------|
| blockId | Azure Blockchain Workbench 内のブロックの一意の識別子 |
| blockHash | ブロックのハッシュ |
| modifyingTransactions | コントラクトを[変更したトランザクションが](#modifying-transaction-information) |
| contractId | Azure Blockchain Workbench 内のコントラクトの一意の識別子 |
| contractLedgerIdentifier | 台帳でのコントラクトの一意の識別子 |
| contractProperties | [コントラクトのプロパティ](#contract-properties) |
| isNewContract | このコントラクトが新しく作成されたかどうかを示します。 指定できる値は次のとおりです。true: このコントラクトは作成された新しいコントラクトです。 false: このコントラクトはコントラクトの更新です。 |
| connectionId | 接続の一意の識別子 |
| messageSchemaVersion | メッセージング スキーマ バージョン |
| messageName | **ContractMessage** |
| additionalInformation | 提供された追加情報 |

#### <a name="modifying-transaction-information"></a>トランザクション情報の変更

| 名前               | 説明 |
|--------------------|-------------|
| transactionId | Azure Blockchain Workbench 内のトランザクションの一意の識別子 |
| transactionHash | 台帳のトランザクションのハッシュ |
| from | 送信元トランザクションの台帳の一意の識別子 |
| to | 送信先トランザクションの台帳の一意の識別子 |

#### <a name="contract-properties"></a>コントラクトのプロパティ

| 名前               | 説明 |
|--------------------|-------------|
| workflowPropertyId | Azure Blockchain Workbench 内のワークフロー プロパティの一意の識別子 |
| name | ワークフロー プロパティの名前 |
| value | ワークフロー プロパティの値 |

Azure Blockchain Workbench からの *ContractMessage* の例:

``` json
{
    "blockId": 123,
    "blockhash": "0x03a39411e25e25b47d0ec6433b73b488554a4a5f6b1a253e0ac8a200d13fffff",
    "modifyingTransactions": [
        {
            "transactionId": 234,
            "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": "0xf8559473b3c7197d59212b401f5a9f07ffff"
        },
        {
            "transactionId": 235,
            "transactionHash": "0xa4d9c95b581f299e41b8cc193dd742ef5a1d3a4ddf97bd11b80d123fec27ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": "0xf8559473b3c7197d59212b401f5a9f07b429ffff"
        }
    ],
    "contractId": 111,
    "contractLedgerIdentifier": "0xf8559473b3c7197d59212b401f5a9f07b429ffff",
    "contractProperties": [
        {
            "workflowPropertyId": 1,
            "name": "State",
            "value": "0"
        },
        {
            "workflowPropertyId": 2,
            "name": "Description",
            "value": "1969 Dodge Charger"
        },
        {
            "workflowPropertyId": 3,
            "name": "AskingPrice",
            "value": "30000"
        },
        {
            "workflowPropertyId": 4,
            "name": "OfferPrice",
            "value": "0"
        },
        {
            "workflowPropertyId": 5,
            "name": "InstanceAppraiser",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 6,
            "name": "InstanceBuyer",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 7,
            "name": "InstanceInspector",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 8,
            "name": "InstanceOwner",
            "value": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff"
        },
        {
            "workflowPropertyId": 9,
            "name": "ClosingDayOptions",
            "value": "[21,48,69]"
        }
    ],
    "isNewContract": false,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "ContractMessage",
    "additionalInformation": {}
}
```

### <a name="event-message-contract-function-invocation"></a>イベント メッセージ:コントラクト関数呼び出し

関数名、パラメーターの入力、関数の呼び出し元など、コントラクト関数が呼び出されたときの情報を含みます。

| 名前 | 説明 |
|------|-------------|
| eventName                   | **ContractFunctionInvocation** |
| caller                      | [呼び出し元情報](#caller-information) |
| contractId                  | Azure Blockchain Workbench 内のコントラクトの一意の識別子 |
| contractLedgerIdentifier    | 台帳でのコントラクトの一意の識別子 |
| functionName                | 関数の名前 |
| parameters                  | [パラメータ情報](#parameter-information) |
| transaction                 | トランザクション情報 |
| inTransactionSequenceNumber | ブロック内のトランザクションのシーケンス番号 |
| connectionId                | 接続の一意の識別子 |
| messageSchemaVersion        | メッセージング スキーマ バージョン |
| messageName                 | **EventMessage** |
| additionalInformation       | 提供された追加情報 |

#### <a name="caller-information"></a>呼び出し元情報

| 名前 | 説明 |
|------|-------------|
| type | ユーザーやコントラクトなど、呼び出し元の種類 |
| id | Azure Blockchain Workbench 内の呼び出し元の一意の識別子 |
| ledgerIdentifier | 台帳での呼び出し元の一意の識別子 |

#### <a name="parameter-information"></a>パラメータ情報

| 名前 | 説明 |
|------|-------------|
| name | パラメーター名 |
| value | パラメーター値 |

#### <a name="event-message-transaction-information"></a>イベント メッセージ トランザクション情報

| 名前               | 説明 |
|--------------------|-------------|
| transactionId      | Azure Blockchain Workbench 内のトランザクションの一意の識別子 |
| transactionHash    | 台帳のトランザクションのハッシュ |
| from               | 送信元トランザクションの台帳の一意の識別子 |
| to                 | 送信先トランザクションの台帳の一意の識別子 |

Blockchain Workbench からの *EventMessage ContractFunctionInvocation* の例:

``` json
{
    "eventName": "ContractFunctionInvocation",
    "caller": {
        "type": "User",
        "id": 21,
        "ledgerIdentifier": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60ffff"
    },
    "contractId": 34,
    "contractLedgerIdentifier": "0xf8559473b3c7197d59212b401f5a9f07b429ffff",
    "functionName": "Modify",
    "parameters": [
        {
            "name": "description",
            "value": "a new description"
        },
        {
            "name": "price",
            "value": "4567"
        }
    ],
    "transaction": {
        "transactionId": 234,
        "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
        "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
        "to": "0xf8559473b3c7197d59212b401f5a9f07b429ffff"
    },
    "inTransactionSequenceNumber": 1,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation": { }
}
```

### <a name="event-message-application-ingestion"></a>イベント メッセージ:アプリケーション インジェスト

アップロードされるアプリケーションの名前やバージョンなど、アプリケーションが Workbench にアップロードされるときの情報を含みます。

| 名前 | 説明 |
|------|-------------|
| eventName | **ApplicationIngestion** |
| applicationId | Azure Blockchain Workbench 内のアプリケーションの一意の識別子 |
| applicationName | アプリケーション名 |
| applicationDisplayName | アプリケーション表示名 |
| applicationVersion | アプリケーションのバージョン |
| applicationDefinitionLocation | アプリケーション構成ファイルを配置する URL |
| contractCodes | アプリケーションの[コントラクト コード](#contract-code-information)のコレクション |
| applicationRoles | アプリケーションの[アプリケーション ロール](#application-role-information)のコレクション |
| applicationWorkflows | アプリケーションの[アプリケーション ワークフロー](#application-workflow-information)のコレクション |
| connectionId | 接続の一意の識別子 |
| messageSchemaVersion | メッセージング スキーマ バージョン |
| messageName | **EventMessage** |
| additionalInformation | ここで提供される追加情報には、アプリケーション ワークフローの状態と遷移情報が含まれています。 |

#### <a name="contract-code-information"></a>コントラクト コードの情報

| 名前 | 説明 |
|------|-------------|
| id | Azure Blockchain Workbench 内のコントラクト コード ファイルの一意の識別子 |
| ledgerId | Azure Blockchain Workbench 内の台帳の一意の識別子 |
| location | コントラクト コード ファイルが配置される URL |

#### <a name="application-role-information"></a>アプリケーション ロール情報

| 名前 | 説明 |
|------|-------------|
| id | Azure Blockchain Workbench 内のアプリケーション ロールの一意の識別子 |
| name | アプリケーション ロールの名前 |

#### <a name="application-workflow-information"></a>アプリケーション ワークフロー情報

| 名前 | 説明 |
|------|-------------|
| id | Azure Blockchain Workbench 内のアプリケーション ワークフローの一意の識別子 |
| name | アプリケーション ワークフロー名 |
| displayName | アプリケーション ワークフロー表示名 |
| functions | [アプリケーション ワークフローの関数](#workflow-function-information)のコレクション|
| states | [アプリケーション ワークフローの状態](#workflow-state-information)のコレクション |
| properties | アプリケーション [ワークフロー プロパティ情報](#workflow-property-information) |

##### <a name="workflow-function-information"></a>ワークフロー関数の情報

| 名前 | 説明 |
|------|-------------|
| id | Azure Blockchain Workbench 内のアプリケーション ワークフロー関数の一意の識別子 |
| name | 関数名 |
| parameters | 関数のパラメーター |

##### <a name="workflow-state-information"></a>ワークフローの状態の情報

| 名前 | 説明 |
|------|-------------|
| name | 状態名 |
| displayName | 状態の表示名 |
| style | 状態スタイル (成功または失敗) |

##### <a name="workflow-property-information"></a>ワークフロー プロパティの情報

| 名前 | 説明 |
|------|-------------|
| id | Azure Blockchain Workbench 内のアプリケーション ワークフロー プロパティの一意の識別子 |
| name | プロパティ名 |
| type | プロパティの種類 |

Blockchain Workbench からの *EventMessage ApplicationIngestion* の例:

``` json
{
    "eventName": "ApplicationIngestion",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": "1.0",
    "applicationDefinitionLocation": "http://url",
    "contractCodes": [
        {
            "id": 23,
            "ledgerId": 1,
            "location": "http://url"
        }
    ],
    "applicationRoles": [
            {
                "id": 134,
                "name": "Buyer"
            },
            {
                "id": 135,
                "name": "Seller"
            }
       ],
    "applicationWorkflows": [
        {
            "id": 89,
            "name": "AssetTransfer",
            "displayName": "Asset Transfer",
            "functions": [
                {
                    "id": 912,
                    "name": "",
                    "parameters": [
                        {
                            "name": "description",
                            "type": {
                                "name": "string"
                             }
                        },
                        {
                            "name": "price",
                            "type": {
                                "name": "int"
                            }
                        }
                    ]
                },
                {
                    "id": 913,
                    "name": "modify",
                    "parameters": [
                        {
                            "name": "description",
                            "type": {
                                "name": "string"
                             }
                        },
                        {
                            "name": "price",
                            "type": {
                                "name": "int"
                            }
                        }
                    ]
                }
            ],
            "states": [ 
                 {
                      "name": "Created",
                      "displayName": "Created",
                      "style" : "Success"
                 },
                 {
                      "name": "Terminated",
                      "displayName": "Terminated",
                      "style" : "Failure"
                 }
            ],
            "properties": [
                {
                    "id": 879,
                    "name": "Description",
                    "type": {
                        "name": "string"
                     }
                },
                {
                    "id": 880,
                    "name": "Price",
                    "type": {
                        "name": "int"
                     }
                }
            ]
        }
    ],
    "connectionId": [ ],
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation":
        {
            "states" :
            [
                {
                    "Name": "BuyerAccepted",
                    "Transitions": [
                        {
                            "DisplayName": "Accept",
                            "AllowedRoles": [ ],
                            "AllowedInstanceRoles": [ "InstanceOwner" ],
                            "Function": "Accept",
                            "NextStates": [ "SellerAccepted" ]
                        }
                    ]
                }
            ]
        }
}
```

### <a name="event-message-role-assignment"></a>イベント メッセージ:ロール割り当て

ロールの割り当てを実行したユーザーや、ロールおよび対応するアプリケーションの名前など、Workbench でユーザーがロールを割り当てられたときの情報を含みます。

| 名前 | 説明 |
|------|-------------|
| eventName | **RoleAssignment** |
| applicationId | Azure Blockchain Workbench 内のアプリケーションの一意の識別子 |
| applicationName | アプリケーション名 |
| applicationDisplayName | アプリケーション表示名 |
| applicationVersion | アプリケーションのバージョン |
| applicationRole        | [アプリケーション ロール](#roleassignment-application-role)に関する情報 |
| assigner               | [アサイナー](#roleassignment-assigner)に関する情報 |
| assignee               | [アサイニー](#roleassignment-assignee)に関する情報 |
| connectionId           | 接続の一意の識別子 |
| messageSchemaVersion   | メッセージング スキーマ バージョン |
| messageName            | **EventMessage** |
| additionalInformation  | 提供された追加情報 |

#### <a name="roleassignment-application-role"></a>RoleAssignment アプリケーション ロール

| 名前 | 説明 |
|------|-------------|
| id | Azure Blockchain Workbench 内のアプリケーション ロールの一意の識別子 |
| name | アプリケーション ロールの名前 |

#### <a name="roleassignment-assigner"></a>RoleAssignment アサイナー

| 名前 | 説明 |
|------|-------------|
| id | Azure Blockchain Workbench 内のユーザーの一意の識別子 |
| type | アサイナーの種類 |
| chainIdentifier | 台帳でのユーザーの一意の識別子 |

#### <a name="roleassignment-assignee"></a>RoleAssignment アサイニー

| 名前 | 説明 |
|------|-------------|
| id | Azure Blockchain Workbench 内のユーザーの一意の識別子 |
| type | アサイニーの種類 |
| chainIdentifier | 台帳でのユーザーの一意の識別子 |

Blockchain Workbench からの *EventMessage RoleAssignment* の例:

``` json
{
    "eventName": "RoleAssignment",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": "1.0",
    "applicationRole": {
        "id": 134,
        "name": "Buyer"
    },
    "assigner": {
        "id": 1,
        "type": null,
        "chainIdentifier": "0xeFFC7766d38aC862d79706c3C5CEEf089564ffff"
    },
    "assignee": {
        "id": 3,
        "type": null,
        "chainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff"
    },
    "connectionId": [ ],
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation": { }
}
```

## <a name="next-steps"></a>次の手順

- [スマート コントラクトの統合パターン](integration-patterns.md)
