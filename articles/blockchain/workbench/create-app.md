---
title: ブロックチェーン アプリケーションを作成する - Azure Blockchain Workbench
description: Azure Blockchain Workbench プレビュー用ブロックチェーン アプリケーションを作成する方法についてのチュートリアル。
ms.date: 08/24/2020
ms.topic: tutorial
ms.reviewer: ravastra
ms.openlocfilehash: add790a069d2e0ea66d84bbd632825cf9331fd38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88784030"
---
# <a name="tutorial-create-a-blockchain-application-for-azure-blockchain-workbench"></a>チュートリアル:Azure Blockchain Workbench 用ブロックチェーン アプリケーションを作成する

Azure Blockchain Workbench を使用すると、構成とスマート コントラクト コードで定義されたマルチパーティ ワークフローを表すブロックチェーン アプリケーションを作成できます。

学習内容は次のとおりです。

> [!div class="checklist"]
> * ブロックチェーン アプリケーションの構成
> * スマート コントラクト コード ファイルの作成
> * Blockchain Workbench へのブロックチェーン アプリケーションの追加
> * ブロックチェーン アプリケーションへのメンバーの追加

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

* Blockchain Workbench のデプロイ。 デプロイの詳細については、[Azure Blockchain Workbench のデプロイ](deploy.md)に関するページを参照してください。
* Blockchain Workbench に関連付けられているテナントの Azure Active Directory ユーザー。 詳細については、[Azure Blockchain Workbench での Azure AD ユーザーの追加](manage-users.md#add-azure-ad-users)に関するセクションを参照してください。
* Blockchain Workbench 管理者アカウント。 詳細については、[Azure Blockchain Workbench での Blockchain Workbench 管理者の追加](manage-users.md#manage-blockchain-workbench-administrators)に関するセクションを参照してください。

## <a name="hello-blockchain"></a>ブロックチェーンの基本

ここでは、要求元が要求を送信し、レスポンダーが要求に対する応答を送信する基本的なアプリケーションを構築します。
たとえば、「こんにちは、調子はどうですか?」という要求に対し、「最高です!」という応答を返すことができます。 要求と応答の両方が、基になるブロックチェーンに記録されます。

アプリケーション ファイルを作成する手順に従うか、[サンプルを GitHub からダウンロード](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/hello-blockchain)できます。

## <a name="configuration-file"></a>構成ファイル

構成メタデータでは、ブロックチェーン アプリケーションの大まかなワークフローと対話モデルを定義します。 構成メタデータは、ブロックチェーン アプリケーションのワークフローのステージと対話モデルを表します。 構成ファイルの内容の詳細については、「[Azure Blockchain Workbench configuration reference (Azure Blockchain Workbench の構成リファレンス)](configuration.md)」を参照してください。

1. 任意のエディターで、`HelloBlockchain.json` という名前のファイルを作成します。
2. ブロックチェーン アプリケーションの構成を定義する次の JSON を追加します。

    ``` json
    {
      "ApplicationName": "HelloBlockchain",
      "DisplayName": "Hello, Blockchain!",
      "Description": "A simple application to send request and get response",
      "ApplicationRoles": [
        {
          "Name": "Requestor",
          "Description": "A person sending a request."
        },
        {
          "Name": "Responder",
          "Description": "A person responding to a request"
        }
      ],
      "Workflows": [
        {
          "Name": "HelloBlockchain",
          "DisplayName": "Request Response",
          "Description": "A simple workflow to send a request and receive a response.",
          "Initiators": [ "Requestor" ],
          "StartState": "Request",
          "Properties": [
            {
              "Name": "State",
              "DisplayName": "State",
              "Description": "Holds the state of the contract.",
              "Type": {
                "Name": "state"
              }
            },
            {
              "Name": "Requestor",
              "DisplayName": "Requestor",
              "Description": "A person sending a request.",
              "Type": {
                "Name": "Requestor"
              }
            },
            {
              "Name": "Responder",
              "DisplayName": "Responder",
              "Description": "A person sending a response.",
              "Type": {
                "Name": "Responder"
              }
            },
            {
              "Name": "RequestMessage",
              "DisplayName": "Request Message",
              "Description": "A request message.",
              "Type": {
                "Name": "string"
              }
            },
            {
              "Name": "ResponseMessage",
              "DisplayName": "Response Message",
              "Description": "A response message.",
              "Type": {
                "Name": "string"
              }
            }
          ],
          "Constructor": {
            "Parameters": [
              {
                "Name": "message",
                "Description": "...",
                "DisplayName": "Request Message",
                "Type": {
                  "Name": "string"
                }
              }
            ]
          },
          "Functions": [
            {
              "Name": "SendRequest",
              "DisplayName": "Request",
              "Description": "...",
              "Parameters": [
                {
                  "Name": "requestMessage",
                  "Description": "...",
                  "DisplayName": "Request Message",
                  "Type": {
                    "Name": "string"
                  }
                }
              ]
            },
            {
              "Name": "SendResponse",
              "DisplayName": "Response",
              "Description": "...",
              "Parameters": [
                {
                  "Name": "responseMessage",
                  "Description": "...",
                  "DisplayName": "Response Message",
                  "Type": {
                    "Name": "string"
                  }
                }
              ]
            }
          ],
          "States": [
            {
              "Name": "Request",
              "DisplayName": "Request",
              "Description": "...",
              "PercentComplete": 50,
              "Value": 0,
              "Style": "Success",
              "Transitions": [
                {
                  "AllowedRoles": ["Responder"],
                  "AllowedInstanceRoles": [],
                  "Description": "...",
                  "Function": "SendResponse",
                  "NextStates": [ "Respond" ],
                  "DisplayName": "Send Response"
                }
              ]
            },
            {
              "Name": "Respond",
              "DisplayName": "Respond",
              "Description": "...",
              "PercentComplete": 90,
              "Value": 1,
              "Style": "Success",
              "Transitions": [
                {
                  "AllowedRoles": [],
                  "AllowedInstanceRoles": ["Requestor"],
                  "Description": "...",
                  "Function": "SendRequest",
                  "NextStates": [ "Request" ],
                  "DisplayName": "Send Request"
                }
              ]
            }
          ]
        }
      ]
    }
    ```

3. `HelloBlockchain.json` ファイルを保存します。

構成ファイルは、複数のセクションに分かれています。 各セクションの詳細は次のとおりです。

### <a name="application-metadata"></a>アプリケーション メタデータ

構成ファイルの先頭には、アプリケーションの名前や説明など、アプリケーションに関する情報が含まれています。

### <a name="application-roles"></a>アプリケーション ロール

アプリケーション ロール セクションでは、ブロックチェーン アプリケーション内で操作または参加できるユーザー ロールを定義します。 機能に応じて異なるロールのセットを定義します。 要求 - 応答シナリオの場合、要求を生成するエンティティである要求元の機能と、応答を生成するエンティティであるレスポンダーの機能は異なっています。

### <a name="workflows"></a>Workflows

ワークフローでは、コントラクトのステージとアクションを 1 つ以上定義します。 要求 - 応答シナリオの場合、ワークフローの最初のステージ (状態) では、要求元 (ロール) がアクション (遷移) を実行して、要求を送信します (関数)。 次のステージ (状態) では、レスポンダー (ロール) がアクション (遷移) を実行して、応答を送信します (関数)。 アプリケーションのワークフローには、コントラクトのフローを記述するために必要なプロパティ、関数、および状態を含めることができます。

## <a name="smart-contract-code-file"></a>スマート コントラクト コード ファイル

スマート コントラクトは、ブロックチェーン アプリケーションのビジネス ロジックを表します。 現在、Blockchain Workbench では、ブロックチェーン台帳として Ethereum をサポートしています。 Ethereum では、スマート コントラクト用の自力執行権のあるビジネス ロジックを作成するためのプログラミング言語として [Solidity](https://solidity.readthedocs.io) を使用します。

Solidity のスマート コントラクトは、オブジェクト指向言語のクラスに似ています。 各コントラクトには、スマート コントラクトのステージとアクションを実装するための状態と関数を含めます。

任意のエディターで、`HelloBlockchain.sol` という名前のファイルを作成します。

### <a name="version-pragma"></a>バージョン プラグマ

ベスト プラクティスとして、ターゲットとする Solidity のバージョンを指定します。 バージョンを指定しておくと、将来の Solidity バージョンとの非互換性を回避するのに役立ちます。

`HelloBlockchain.sol` スマート コントラクト コード ファイルの先頭に次のバージョン プラグマを追加します。

``` solidity
pragma solidity >=0.4.25 <0.6.0;
```

### <a name="configuration-and-smart-contract-code-relationship"></a>構成とスマート コントラクト コードの関係

Blockchain Workbench では、構成ファイルとスマート コントラクト コード ファイルを使用してブロックチェーン アプリケーションを作成します。 構成の定義内容とスマート コントラクトのコードとの間には関係があります。 アプリケーションを作成するには、コントラクトの詳細、関数、パラメーター、および型が一致する必要があります。 Blockchain Workbench では、アプリケーションを作成する前にファイルを検証します。

### <a name="contract"></a>コントラクト

`HelloBlockchain.sol` スマート コントラクト コード ファイルに **contract** ヘッダーを追加します。

``` solidity
contract HelloBlockchain {
```

### <a name="state-variables"></a>状態変数

状態変数は、各コントラクト インスタンスの状態の値を格納します。 コントラクトの状態変数は、構成ファイルで定義されているワークフローのプロパティと一致する必要があります。

`HelloBlockchain.sol` スマート コントラクト コード ファイルでコントラクトに状態変数を追加します。

``` solidity
    //Set of States
    enum StateType { Request, Respond}

    //List of properties
    StateType public  State;
    address public  Requestor;
    address public  Responder;

    string public RequestMessage;
    string public ResponseMessage;
```

### <a name="constructor"></a>Constructor

コンストラクターでは、ワークフローの新しいスマート コントラクト インスタンスの入力パラメーターを定義します。 コンストラクターの必要なパラメーターは、構成ファイルでコンストラクター パラメーターとして定義します。 パラメーターの数、順序、型は両方のファイルで一致する必要があります。

コンストラクター関数では、コントラクトを作成する前に実行するビジネス ロジックを記述します。 たとえば、状態変数を開始値で初期化します。

`HelloBlockchain.sol` スマート コントラクト コード ファイルでコントラクトにコンストラクター関数を追加します。

``` solidity
    // constructor function
    constructor(string memory message) public
    {
        Requestor = msg.sender;
        RequestMessage = message;
        State = StateType.Request;
    }
```

### <a name="functions"></a>関数

関数とは、コントラクト内のビジネス ロジックの実行可能ユニットです。 関数の必要なパラメーターは、構成ファイルで関数パラメーターとして定義します。 パラメーターの数、順序、型は両方のファイルで一致する必要があります。 関数は、構成ファイルで Blockchain Workbench ワークフローの遷移に関連付けます。 遷移とは、コントラクトで定められるとおりアプリケーションのワークフローの次のステージに進むために実行されるアクションです。

関数で実行するビジネス ロジックを記述します。 たとえば、状態変数の値を変更します。

1. `HelloBlockchain.sol` スマート コントラクト コード ファイルでコントラクトに次の関数を追加します。

    ``` solidity
        // call this function to send a request
        function SendRequest(string memory requestMessage) public
        {
            if (Requestor != msg.sender)
            {
                revert();
            }
    
            RequestMessage = requestMessage;
            State = StateType.Request;
        }
    
        // call this function to send a response
        function SendResponse(string memory responseMessage) public
        {
            Responder = msg.sender;
    
            ResponseMessage = responseMessage;
            State = StateType.Respond;
        }
    }
    ```

2. `HelloBlockchain.sol` スマート コントラクト コード ファイルを保存します。

## <a name="add-blockchain-application-to-blockchain-workbench"></a>Blockchain Workbench へのブロックチェーン アプリケーションの追加

Blockchain Workbench にブロックチェーン アプリケーションを追加するには、アプリケーションを定義する構成ファイルとスマート コントラクト ファイルをアップロードします。

1. Web ブラウザーで、Blockchain Workbench の Web アドレスに移動します。 たとえば、`https://{workbench URL}.azurewebsites.net/` です。Web アプリケーションは、Blockchain Workbench をデプロイすると作成されます。 Blockchain Workbench の Web アドレスを調べる方法については、「[Blockchain Workbench Web URL (Blockchain Workbench の Web URL)](deploy.md#blockchain-workbench-web-url)」を参照してください。
2. [Blockchain Workbench 管理者](manage-users.md#manage-blockchain-workbench-administrators)としてサインインします。
3. **[アプリケーション]**  >  **[新規]** の順に選択します。 **[新しいアプリケーション]** ウィンドウが表示されます。
4. **[Upload the contract configuration]\(コントラクトの構成のアップロード\)**  >  **[参照]** の順に選択して、作成した **HelloBlockchain.json** 構成ファイルを探します。 構成ファイルは自動的に検証されます。 **[表示]** リンクを選択すると、検証エラーが表示されます。 アプリケーションをデプロイする前に検証エラーを修正してください。
5. **[Upload the contract code]\(コントラスト コードのアップロード\)**  >  **[参照]** の順に選択して、**HelloBlockchain.sol** スマート コントラクト コード ファイルを探します。 コード ファイルは自動的に検証されます。 **[表示]** リンクを選択すると、検証エラーが表示されます。 アプリケーションをデプロイする前に検証エラーを修正してください。
6. **[デプロイ]** を選択して、構成とスマート コントラクト ファイルに基づいてブロックチェーン アプリケーションを作成します。

ブロックチェーン アプリケーションのデプロイには数分かかります。 デプロイが完了すると、新しいアプリケーションが **[アプリケーション]** に表示されます。 

> [!NOTE]
> また、[Azure Blockchain Workbench REST API](/rest/api/azure-blockchain-workbench) を使用してブロックチェーン アプリケーションを作成することもできます。

## <a name="add-blockchain-application-members"></a>ブロックチェーン アプリケーション メンバーの追加

コントラクトを開始し、アクションを実行するアプリケーション メンバーをアプリケーションに追加します。 アプリケーション メンバーを追加するには、[Blockchain Workbench 管理者](manage-users.md#manage-blockchain-workbench-administrators)である必要があります。

1. **[アプリケーション]**  >  **[Hello, Blockchain!]** の順に選択します。
2. アプリケーションに関連付けられているメンバーの数がページの右上隅に表示されます。 新しいアプリケーションの場合、メンバーの数はゼロです。
3. ページの右上隅にある **[メンバー]** リンクを選択します。 アプリケーションの現在のメンバーが一覧表示されます。
4. メンバーシップ一覧で、 **[メンバーの追加]** を選択します。
5. 追加するメンバーの名前を選択するか、入力します。 Blockchain Workbench テナントに存在する Azure AD ユーザーのみが一覧に表示されます。 ユーザーが見つからない場合は、[Azure AD ユーザーを追加する](manage-users.md#add-azure-ad-users)必要があります。
6. メンバーの **ロール** を選択します。 最初のメンバーのロールには **[要求元]** を選択します。
7. **[追加]** を選択して、メンバーとそのロールをアプリケーションに追加します。
8. 別のメンバーを **[レスポンダー]** ロールとしてアプリケーションに追加します。

Blockchain Workbench でのユーザー管理の詳細については、[Azure Blockchain Workbench でのユーザーの管理](manage-users.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

このハウツー記事では、基本的な要求と応答のアプリケーションを作成しました。 アプリケーションを使用する方法については、次のハウツー記事に進みます。

> [!div class="nextstepaction"]
> [ブロックチェーン アプリケーションの使用](use.md)
