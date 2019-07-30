---
title: Azure Blockchain Workbench 構成リファレンス
description: Azure Blockchain Workbench アプリケーションの構成の概要。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 04/15/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 4d29d8e86a30f105c4aa50ec9615f8165fa238d3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60578982"
---
# <a name="azure-blockchain-workbench-configuration-reference"></a>Azure Blockchain Workbench 構成リファレンス

Azure Blockchain Workbench アプリケーションは、構成メタデータとスマート コントラクト コードで定義されたマルチパーティ ワークフローです。 構成メタデータでは、ブロックチェーン アプリケーションの大まかなワークフローと対話モデルを定義します。 スマート コントラクトを使用して、ブロックチェーン アプリケーションのビジネス ロジックを定義します。 Workbench は、構成とスマート コントラクト コードを使用して、ブロックチェーン アプリケーションのユーザー エクスペリエンスを生成します。

構成のメタデータには、各ブロックチェーン アプリケーションの次の情報が指定されます。

* ブロックチェーン アプリケーションの名前と説明
* ブロックチェーン アプリケーション内で操作または参加できるユーザーに固有のロール
* 1 つ以上のワークフロー。 各ワークフローは、ビジネス ロジックのフローを制御する状態マシンとして機能します。 ワークフローは独立している場合と、相互作用がある場合があります。

定義される各ワークフローには、次の情報が指定されます。

* ワークフローの名前と説明。
* ワークフローの状態。  各状態は、ビジネス ロジックの制御フローの 1 段階です。 
* 次の状態に遷移するアクション
* 各アクションの開始が許可されているユーザー ロール
* コード ファイル内のビジネス ロジックを表すスマート コントラクト

## <a name="application"></a>アプリケーション

ブロックチェーン アプリケーションには、構成メタデータ、ワークフロー、アプリケーション内で操作または参加できるユーザー ロールが含まれています。

| フィールド | Description | 必須 |
|-------|-------------|:--------:|
| ApplicationName | 一意のアプリケーション名。 対応するスマート コントラクトでは、該当するコントラクト クラスに対して同じ **ApplicationName** を使用する必要があります。  | はい |
| DisplayName | アプリケーションのわかりやすい表示名。 | はい |
| Description | アプリケーションの説明。 | いいえ |
| ApplicationRoles | [ApplicationRoles](#application-roles) のコレクション。 アプリケーション内で操作または参加できるユーザー ロール。  | はい |
| Workflows | [Workflows](#workflows) のコレクション。 各ワークフローは、ビジネス ロジックのフローを制御する状態マシンとして機能します。 | はい |

例については、「[構成ファイルの例](#configuration-file-example)」を参照してください。

## <a name="workflows"></a>Workflows

アプリケーションのビジネス ロジックは、アクションを実行するとビジネス ロジックのフローが別の状態に移行するという、状態マシンとしてモデル化することができます。 ワークフローは、そのような状態とアクションのコレクションです。 各ワークフローは 1 つ以上のスマート コントラクトで構成されています。スマート コントラクトは、コード ファイル内のビジネス ロジックを表します。 実行可能なコントラクトは、ワークフローのインスタンスです。

| フィールド | Description | 必須 | 最大長 |
|-------|-------------|:--------:|-----------:|
| Name | 一意のワークフロー名。 対応するスマート コントラクトでは、該当するコントラクト クラスに対して同じ **Name** を使用する必要があります。 | はい | 50 |
| DisplayName | ワークフローのわかりやすい表示名。 | はい | 255 |
| Description | ワークフローの説明。 | いいえ | 255 |
| Initiators | [ApplicationRoles](#application-roles) のコレクション。 ワークフローでコントラクトを作成する権限を持つユーザーに割り当てられるロール。 | はい | |
| StartState | ワークフローの初期状態の名前。 | はい | |
| Properties | [識別子](#identifiers)のコレクション。 ユーザー エクスペリエンス ツールでオフチェーンの読み取りまたは視覚化を実行できるデータを表します。 | はい | |
| Constructor | ワークフローのインスタンスを作成するための入力パラメーターを定義します。 | はい | |
| Functions | ワークフローで実行できる[関数](#functions)のコレクション。 | はい | |
| States | ワークフローの[状態](#states)のコレクション。 | はい | |

例については、「[構成ファイルの例](#configuration-file-example)」を参照してください。

## <a name="type"></a>type

サポートされているデータ型。

| type | Description |
|-------|-------------|
| address  | *contracts*、*users* などのブロックチェーン アドレスの種類。 |
| array    | integer、bool、money、または time 型の単一レベル配列。 配列は、静的でも動的でもかまいません。 配列内の要素のデータ型を指定するには、**ElementType** を使用します。 [構成例](#example-configuration-of-type-array)をご覧ください。 |
| bool     | ブール データ型。 |
| contract | 型のコントラクトのアドレス。 |
| enum     | 名前が付いた各種の値を列挙したものです。 列挙型を使用するときは、一連の EnumValues も指定する必要があります。 それぞれの値は 255 文字に制限されています。 有効な値として使用できる文字は、アルファベットの大文字と小文字 (A-Z、a-z) および数字 (0-9) です。 [Solidity での構成と使用の例](#example-configuration-of-type-enum)をご覧ください。 |
| int      | 整数データ型。 |
| money    | 通貨データ型。 |
| state    | ワークフローの状態。 |
| string  | 文字列データ型。 最大 4,000 文字。 [構成例](#example-configuration-of-type-string)をご覧ください。 |
| user     | 型ユーザーのアドレス。 |
| time     | 時間データ型。 |
|`[ Application Role Name ]`| アプリケーション ロールに指定する任意の名前。 ユーザーをそのロールの種類に制限します。 |

### <a name="example-configuration-of-type-array"></a>配列型の構成例

```json
{
  "Name": "Quotes",
  "Description": "Market quotes",
  "DisplayName": "Quotes",
  "Type": {
    "Name": "array",
    "ElementType": {
        "Name": "int"
    }
  }
}
```

#### <a name="using-a-property-of-type-array"></a>配列型のプロパティの使用

構成で配列型としてプロパティを定義する場合は、Solidity で配列型のパブリック プロパティを返すための明示的な get 関数を含める必要があります。 例:

```
function GetQuotes() public constant returns (int[]) {
     return Quotes;
}
```

### <a name="example-configuration-of-type-string"></a>文字列型の構成例

``` json
{
  "Name": "description",
  "Description": "Descriptive text",
  "DisplayName": "Description",
  "Type": {
    "Name": "string"
  }
}
```

### <a name="example-configuration-of-type-enum"></a>列挙型の構成例

``` json
{
  "Name": "PropertyType",
  "DisplayName": "Property Type",
  "Description": "The type of the property",
  "Type": {
    "Name": "enum",
    "EnumValues": ["House", "Townhouse", "Condo", "Land"]
  }
}
```

#### <a name="using-enumeration-type-in-solidity"></a>Solidity での列挙型の使用

列挙型を構成で定義したら、Solidity で列挙型を使用することができます。 たとえば、PropertyTypeEnum という列挙型を定義することができます。

```
enum PropertyTypeEnum {House, Townhouse, Condo, Land} PropertyTypeEnum public PropertyType; 
```

Blockchain Workbench の中で有効かつ一貫した宣言となるためには、この一連の文字列が構成とスマート コントラクトとの間で一致している必要があります。

代入の例:

```
PropertyType = PropertyTypeEnum.Townhouse;
```

関数パラメーターの例: 

``` 
function AssetTransfer(string description, uint256 price, PropertyTypeEnum propertyType) public
{
    InstanceOwner = msg.sender;
    AskingPrice = price;
    Description = description;
    PropertyType = propertyType;
    State = StateType.Active;
    ContractCreated();
}

```

## <a name="constructor"></a>Constructor

ワークフローのインスタンスの入力パラメーターを定義します。

| フィールド | Description | 必須 |
|-------|-------------|:--------:|
| Parameters | スマート コントラクトを開始するために必要な[識別子](#identifiers)のコレクション。 | はい |

### <a name="constructor-example"></a>コンストラクターの例

``` json
{
  "Parameters": [
    {
      "Name": "description",
      "Description": "The description of this asset",
      "DisplayName": "Description",
      "Type": {
        "Name": "string"
      }
    },
    {
      "Name": "price",
      "Description": "The price of this asset",
      "DisplayName": "Price",
      "Type": {
        "Name": "money"
      }
    }
  ]
}
```

## <a name="functions"></a>Functions

ワークフローに対して実行できる関数を定義します。

| フィールド | Description | 必須 | 最大長 |
|-------|-------------|:--------:|-----------:|
| Name | 関数の一意の名前。 対応するスマート コントラクトでは、該当する関数に対して同じ **Name** を使用する必要があります。 | はい | 50 |
| DisplayName | 関数のわかりやすい表示名。 | はい | 255 |
| Description | 関数の説明。 | いいえ | 255 |
| Parameters | 関数のパラメーターに対応する[識別子](#identifiers)のコレクション。 | はい | |

### <a name="functions-example"></a>関数の例

``` json
"Functions": [
  {
    "Name": "Modify",
    "DisplayName": "Modify",
    "Description": "Modify the description/price attributes of this asset transfer instance",
    "Parameters": [
      {
        "Name": "description",
        "Description": "The new description of the asset",
        "DisplayName": "Description",
        "Type": {
          "Name": "string"
        }
      },
      {
        "Name": "price",
        "Description": "The new price of the asset",
        "DisplayName": "Price",
        "Type": {
          "Name": "money"
        }
      }
    ]
  },
  {
    "Name": "Terminate",
    "DisplayName": "Terminate",
    "Description": "Used to cancel this particular instance of asset transfer",
    "Parameters": []
  }
]

```

## <a name="states"></a>States

ワークフロー内の一意の状態のコレクション。 各状態で、ビジネス ロジックの制御フローの手順がキャプチャされます。 

| フィールド | Description | 必須 | 最大長 |
|-------|-------------|:--------:|-----------:|
| Name | 状態の一意の名前。 対応するスマート コントラクトでは、該当する状態に対して同じ **Name** を使用する必要があります。 | はい | 50 |
| DisplayName | 状態のわかりやすい表示名。 | はい | 255 |
| Description | 状態の説明。 | いいえ | 255 |
| PercentComplete | Blockchain Workbench のユーザー インターフェイスに表示される整数値。ビジネス ロジックの制御フロー内の進行状況を示します。 | はい | |
| Style | 状態が成功か失敗かを表すビジュアル ヒント。 有効な値は `Success` または `Failure` です。 | はい | |
| Transitions | 現在の状態から次の一連の状態への使用できる[遷移](#transitions)のコレクション。 | いいえ | |

### <a name="states-example"></a>状態の例

``` json
"States": [
    {
      "Name": "Active",
      "DisplayName": "Active",
      "Description": "The initial state of the asset transfer workflow",
      "PercentComplete": 20,
      "Style": "Success",
      "Transitions": [
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Cancels this instance of asset transfer",
          "Function": "Terminate",
          "NextStates": [ "Terminated" ],
          "DisplayName": "Terminate Offer"
        },
        {
          "AllowedRoles": [ "Buyer" ],
          "AllowedInstanceRoles": [],
          "Description": "Make an offer for this asset",
          "Function": "MakeOffer",
          "NextStates": [ "OfferPlaced" ],
          "DisplayName": "Make Offer"
        },
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Modify attributes of this asset transfer instance",
          "Function": "Modify",
          "NextStates": [ "Active" ],
          "DisplayName": "Modify"
        }
      ]
    },
    {
      "Name": "Accepted",
      "DisplayName": "Accepted",
      "Description": "Asset transfer process is complete",
      "PercentComplete": 100,
      "Style": "Success",
      "Transitions": []
    },
    {
      "Name": "Terminated",
      "DisplayName": "Terminated",
      "Description": "Asset transfer has been canceled",
      "PercentComplete": 100,
      "Style": "Failure",
      "Transitions": []
    }
  ]
```

## <a name="transitions"></a>Transitions

次の状態に使用できるアクションです。 1 つ以上のユーザー ロールが各状態でアクションを実行できます。このアクションで、ワークフロー内の状態が別の状態に遷移することがあります。 

| フィールド | Description | 必須 |
|-------|-------------|:--------:|
| AllowedRoles | 移行を開始できるアプリケーション ロールの一覧。 指定されたロールのすべてのユーザーがアクションを実行できる可能性があります。 | いいえ |
| AllowedInstanceRoles | 遷移の開始が許可されているスマート コントラクトに参加している、または指定されているユーザー ロールのリスト。 インスタンス ロールは、ワークフロー内の **Properties** に定義されています。 AllowedInstanceRoles は、スマート コントラクトのインスタンスに参加しているユーザーを表します。 AllowedInstanceRoles を使用すると、コントラクト インスタンス内のユーザー ロールに対するアクションの実行を制限できます。  たとえば、AllowedRoles でロールを指定した場合、ロールの種類 (所有者) のすべてのユーザーでなく、コントラクトを作成したユーザー (InstanceOwner) のみに終了の実行を許可することができます。 | いいえ |
| DisplayName | 遷移のわかりやすい表示名。 | はい |
| Description | 遷移の説明。 | いいえ |
| Function | 遷移を開始する関数の名前。 | はい |
| NextStates | 遷移が成功した後の次の状態候補のコレクション。 | はい |

### <a name="transitions-example"></a>遷移の例

``` json
"Transitions": [
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Cancels this instance of asset transfer",
    "Function": "Terminate",
    "NextStates": [ "Terminated" ],
    "DisplayName": "Terminate Offer"
  },
  {
    "AllowedRoles": [ "Buyer" ],
    "AllowedInstanceRoles": [],
    "Description": "Make an offer for this asset",
    "Function": "MakeOffer",
    "NextStates": [ "OfferPlaced" ],
    "DisplayName": "Make Offer"
  },
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Modify attributes of this asset transfer instance",
    "Function": "Modify",
    "NextStates": [ "Active" ],
    "DisplayName": "Modify"
  }
]

```

## <a name="application-roles"></a>アプリケーション ロール

アプリケーション ロールには、アプリケーション内で操作または参加するユーザーに割り当てることができる一連のロールを定義します。 アプリケーション ロールを使用して、ブロックチェーン アプリケーションとそれに対応するワークフロー内の操作と参加を制限することができます。 

| フィールド | Description | 必須 | 最大長 |
|-------|-------------|:--------:|-----------:|
| Name | アプリケーション ロールの一意の名前。 対応するスマート コントラクトでは、該当するロールに対して同じ **Name** を使用する必要があります。 基本データ型は予約されています。 アプリケーション ロールに [Type](#type) と同じ名前を付けることはできません| はい | 50 |
| Description | アプリケーション ロールの説明。 | いいえ | 255 |

### <a name="application-roles-example"></a>アプリケーション ロールの例

``` json
"ApplicationRoles": [
  {
    "Name": "Appraiser",
    "Description": "User that signs off on the asset price"
  },
  {
    "Name": "Buyer",
    "Description": "User that places an offer on an asset"
  }
]
```
## <a name="identifiers"></a>識別子

識別子は、ワークフローのプロパティ、コンストラクター、および関数のパラメーターを記述するために使用される情報のコレクションを表しています。 

| フィールド | Description | 必須 | 最大長 |
|-------|-------------|:--------:|-----------:|
| Name | プロパティまたはパラメーターの一意の名前。 対応するスマート コントラクトでは、該当するプロパティまたはパラメーターに対して同じ **Name** を使用する必要があります。 | はい | 50 |
| DisplayName | プロパティまたはパラメーターのわかりやすい表示名。 | はい | 255 |
| Description | プロパティまたはパラメーターの説明。 | いいえ | 255 |

### <a name="identifiers-example"></a>識別子の例

``` json
"Properties": [
  {
    "Name": "State",
    "DisplayName": "State",
    "Description": "Holds the state of the contract",
    "Type": {
      "Name": "state"
    }
  },
  {
    "Name": "Description",
    "DisplayName": "Description",
    "Description": "Describes the asset being sold",
    "Type": {
      "Name": "string"
    }
  }
]
```

## <a name="configuration-file-example"></a>構成ファイルの例

資産移転は、高価な資産を売買するためのスマート コントラクトのシナリオであり、検査担当者と評定者が必要です。 売り手は、資産移転スマート コントラクトをインスタンス化することによって、資産を一覧表示することができます。 買い手は、スマート コントラクトに対してアクションを実行してオファーを行うことができます。他の関係者は、資産を検査または評定するアクションを実行できます。 資産の検査と評定の両方が完了とマークされると、コントラクトが完了する前に買い手と売り手が販売内容を再確認します。 プロセスの各時点で、コントラクトが更新されたときにすべての参加者はコントラクトの状態を確認できます。 

コード ファイルなどの詳細については、[Azure Blockchain Workbench の資産移転サンプル](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer)を参照してください。

資産移転サンプルの構成ファイルは次のとおりです。

``` json
{
  "ApplicationName": "AssetTransfer",
  "DisplayName": "Asset Transfer",
  "Description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
  "ApplicationRoles": [
    {
      "Name": "Appraiser",
      "Description": "User that signs off on the asset price"
    },
    {
      "Name": "Buyer",
      "Description": "User that places an offer on an asset"
    },
    {
      "Name": "Inspector",
      "Description": "User that inspects the asset and signs off on inspection"
    },
    {
      "Name": "Owner",
      "Description": "User that signs off on the asset price"
    }
  ],
  "Workflows": [
    {
      "Name": "AssetTransfer",
      "DisplayName": "Asset Transfer",
      "Description": "Handles the business logic for the asset transfer scenario",
      "Initiators": [ "Owner" ],
      "StartState":  "Active",
      "Properties": [
        {
          "Name": "State",
          "DisplayName": "State",
          "Description": "Holds the state of the contract",
          "Type": {
            "Name": "state"
          }
        },
        {
          "Name": "Description",
          "DisplayName": "Description",
          "Description": "Describes the asset being sold",
          "Type": {
            "Name": "string"
          }
        },
        {
          "Name": "AskingPrice",
          "DisplayName": "Asking Price",
          "Description": "The asking price for the asset",
          "Type": {
            "Name": "money"
          }
        },
        {
          "Name": "OfferPrice",
          "DisplayName": "Offer Price",
          "Description": "The price being offered for the asset",
          "Type": {
            "Name": "money"
          }
        },
        {
          "Name": "InstanceAppraiser",
          "DisplayName": "Instance Appraiser",
          "Description": "The user that appraises the asset",
          "Type": {
            "Name": "Appraiser"
          }
        },
        {
          "Name": "InstanceBuyer",
          "DisplayName": "Instance Buyer",
          "Description": "The user that places an offer for this asset",
          "Type": {
            "Name": "Buyer"
          }
        },
        {
          "Name": "InstanceInspector",
          "DisplayName": "Instance Inspector",
          "Description": "The user that inspects this asset",
          "Type": {
            "Name": "Inspector"
          }
        },
        {
          "Name": "InstanceOwner",
          "DisplayName": "Instance Owner",
          "Description": "The seller of this particular asset",
          "Type": {
            "Name": "Owner"
          }
        }
      ],
      "Constructor": {
        "Parameters": [
          {
            "Name": "description",
            "Description": "The description of this asset",
            "DisplayName": "Description",
            "Type": {
              "Name": "string"
            }
          },
          {
            "Name": "price",
            "Description": "The price of this asset",
            "DisplayName": "Price",
            "Type": {
              "Name": "money"
            }
          }
        ]
      },
      "Functions": [
        {
          "Name": "Modify",
          "DisplayName": "Modify",
          "Description": "Modify the description/price attributes of this asset transfer instance",
          "Parameters": [
            {
              "Name": "description",
              "Description": "The new description of the asset",
              "DisplayName": "Description",
              "Type": {
                "Name": "string"
              }
            },
            {
              "Name": "price",
              "Description": "The new price of the asset",
              "DisplayName": "Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Terminate",
          "DisplayName": "Terminate",
          "Description": "Used to cancel this particular instance of asset transfer",
          "Parameters": []
        },
        {
          "Name": "MakeOffer",
          "DisplayName": "Make Offer",
          "Description": "Place an offer for this asset",
          "Parameters": [
            {
              "Name": "inspector",
              "Description": "Specify a user to inspect this asset",
              "DisplayName": "Inspector",
              "Type": {
                "Name": "Inspector"
              }
            },
            {
              "Name": "appraiser",
              "Description": "Specify a user to appraise this asset",
              "DisplayName": "Appraiser",
              "Type": {
                "Name": "Appraiser"
              }
            },
            {
              "Name": "offerPrice",
              "Description": "Specify your offer price for this asset",
              "DisplayName": "Offer Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Reject",
          "DisplayName": "Reject",
          "Description": "Reject the user's offer",
          "Parameters": []
        },
        {
          "Name": "AcceptOffer",
          "DisplayName": "Accept Offer",
          "Description": "Accept the user's offer",
          "Parameters": []
        },
        {
          "Name": "RescindOffer",
          "DisplayName": "Rescind Offer",
          "Description": "Rescind your placed offer",
          "Parameters": []
        },
        {
          "Name": "ModifyOffer",
          "DisplayName": "Modify Offer",
          "Description": "Modify the price of your placed offer",
          "Parameters": [
            {
              "Name": "offerPrice",
              "DisplayName": "Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Accept",
          "DisplayName": "Accept",
          "Description": "Accept the inspection/appraisal results",
          "Parameters": []
        },
        {
          "Name": "MarkInspected",
          "DisplayName": "Mark Inspected",
          "Description": "Mark the asset as inspected",
          "Parameters": []
        },
        {
          "Name": "MarkAppraised",
          "DisplayName": "Mark Appraised",
          "Description": "Mark the asset as appraised",
          "Parameters": []
        }
      ],
      "States": [
        {
          "Name": "Active",
          "DisplayName": "Active",
          "Description": "The initial state of the asset transfer workflow",
          "PercentComplete": 20,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancels this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate Offer"
            },
            {
              "AllowedRoles": [ "Buyer" ],
              "AllowedInstanceRoles": [],
              "Description": "Make an offer for this asset",
              "Function": "MakeOffer",
              "NextStates": [ "OfferPlaced" ],
              "DisplayName": "Make Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Modify attributes of this asset transfer instance",
              "Function": "Modify",
              "NextStates": [ "Active" ],
              "DisplayName": "Modify"
            }
          ]
        },
        {
          "Name": "OfferPlaced",
          "DisplayName": "Offer Placed",
          "Description": "Offer has been placed for the asset",
          "PercentComplete": 30,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Accept the proposed offer for the asset",
              "Function": "AcceptOffer",
              "NextStates": [ "PendingInspection" ],
              "DisplayName": "Accept Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you previously placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Modify the price that you specified for your offer",
              "Function": "ModifyOffer",
              "NextStates": [ "OfferPlaced" ],
              "DisplayName": "Modify Offer"
            }
          ]
        },
        {
          "Name": "PendingInspection",
          "DisplayName": "Pending Inspection",
          "Description": "Asset is pending inspection",
          "PercentComplete": 40,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceInspector" ],
              "Description": "Mark this asset as inspected",
              "Function": "MarkInspected",
              "NextStates": [ "Inspected" ],
              "DisplayName": "Mark Inspected"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceAppraiser" ],
              "Description": "Mark this asset as appraised",
              "Function": "MarkAppraised",
              "NextStates": [ "Appraised" ],
              "DisplayName": "Mark Appraised"
            }
          ]
        },
        {
          "Name": "Inspected",
          "DisplayName": "Inspected",
          "PercentComplete": 45,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceAppraiser" ],
              "Description": "Mark this asset as appraised",
              "Function": "MarkAppraised",
              "NextStates": [ "NotionalAcceptance" ],
              "DisplayName": "Mark Appraised"
            }
          ]
        },
        {
          "Name": "Appraised",
          "DisplayName": "Appraised",
          "Description": "Asset has been appraised, now awaiting inspection",
          "PercentComplete": 45,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceInspector" ],
              "Description": "Mark the asset as inspected",
              "Function": "MarkInspected",
              "NextStates": [ "NotionalAcceptance" ],
              "DisplayName": "Mark Inspected"
            }
          ]
        },
        {
          "Name": "NotionalAcceptance",
          "DisplayName": "Notional Acceptance",
          "Description": "Asset has been inspected and appraised, awaiting final sign-off from buyer and seller",
          "PercentComplete": 50,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "SellerAccepted" ],
              "DisplayName": "SellerAccept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "BuyerAccepted" ],
              "DisplayName": "BuyerAccept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            }
          ]
        },
        {
          "Name": "BuyerAccepted",
          "DisplayName": "Buyer Accepted",
          "Description": "Buyer has signed-off on inspection and appraisal",
          "PercentComplete": 75,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "SellerAccepted" ],
              "DisplayName": "Accept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            }
          ]
        },
        {
          "Name": "SellerAccepted",
          "DisplayName": "Seller Accepted",
          "Description": "Seller has signed-off on inspection and appraisal",
          "PercentComplete": 75,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "Accepted" ],
              "DisplayName": "Accept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            }
          ]
        },
        {
          "Name": "Accepted",
          "DisplayName": "Accepted",
          "Description": "Asset transfer process is complete",
          "PercentComplete": 100,
          "Style": "Success",
          "Transitions": []
        },
        {
          "Name": "Terminated",
          "DisplayName": "Terminated",
          "Description": "Asset transfer has been canceled",
          "PercentComplete": 100,
          "Style": "Failure",
          "Transitions": []
        }
      ]
    }
  ]
}
```
## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench REST API リファレンス](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)

