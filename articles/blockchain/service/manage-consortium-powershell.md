---
title: Azure PowerShell を使用して Azure Blockchain Service コンソーシアムのメンバーを管理する
description: Azure PowerShell を使用して Azure Blockchain Service コンソーシアムのメンバーを管理する方法について説明します。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/10/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 9f76597a91c0e22f57d1ba66ff1a16eea9002af0
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68250082"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-by-using-powershell"></a>PowerShell を使用して Azure Blockchain Service のコンソーシアム メンバーを管理する

PowerShell を使用して、Azure Blockchain Service のブロックチェーン コンソーシアム メンバーを管理することができます。 管理者特権を持つメンバーは、ブロックチェーン コンソーシアムのすべての参加者の招待、追加、削除、ロール変更を行うことができます。 ユーザー特権を持つメンバーは、ブロックチェーン コンソーシアムのすべての参加者を表示でき、自分のメンバー表示名を変更できます。

## <a name="prerequisites"></a>前提条件

* [Azure portal](create-member.md) を使用してブロックチェーン メンバーを作成します。
* コンソーシアム、メンバー、ノードについて詳しくは、「[Azure Blockchain Service のコンソーシアム](consortium.md)」をご覧ください。

## <a name="open-azure-cloud-shell"></a>Azure Cloud Shell を開く

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。

[shell.azure.com/powershell](https://shell.azure.com/powershell) に移動して、別のブラウザー タブで Cloud Shell を開くこともできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、 **[入力]** を選択して実行します。

## <a name="install-the-powershell-module"></a>PowerShell モジュールをインストールする

PowerShell ギャラリーから Microsoft.AzureBlockchainService.ConsortiumManagement.PS パッケージをインストールします。

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>情報の基本設定を設定する

情報の基本設定変数を設定すると、コマンドレットを実行したときにより詳しい情報を取得できます。 既定では、 *$InformationPreference* は *SilentlyContinue* に設定されます。

コマンドレットからのより詳しい情報のために、PowerShell では以下のように基本設定を設定します。

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>Web3 の接続を確立する

コンソーシアムのメンバーを管理するには、Blockchain Service メンバー エンドポイントに対して Web3 の接続を確立します。 次のスクリプトを使用して、コンソーシアム管理コマンドレットを呼び出すためのグローバル変数を設定できます。

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

*\<Member account password\>* は、メンバーを作成するときに使用したメンバー アカウントのパスワードに置き換えます。

他の値は Azure portal で探します。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 既定の Blockchain Service メンバーの **[概要]** ページに移動します。

    ![メンバーの概要](./media/manage-consortium-powershell/member-overview.png)

    *\<Member account\>* と *\<RootContract address\>* は、ポータルの値に置き換えます。

1. エンドポイント アドレスについては、 **[トランザクション ノード]** を選択して、**既定のトランザクション ノード**を選択します。 既定のノードは、ブロックチェーン メンバーと同じ名前になっています。
1. **[接続文字列]** を選択します。

    ![Connection strings](./media/manage-consortium-powershell/connection-strings.png)

    *\<Endpoint address\>* は、 **[HTTPS (Access key 1)]** (HTTPS (アクセス キー 1)) または **[HTTPS (Access key 2)]** (HTTPS (アクセス キー 2)) の値に置き換えます。

## <a name="manage-the-network-and-smart-contracts"></a>ネットワークとスマート コントラクトを管理する

コンソーシアムの管理を担当するブロックチェーン エンドポイント スマート コントラクトへの接続を確立するには、ネットワークとスマート コントラクトのコマンドレットを使います。

### <a name="import-consortiummanagementcontracts"></a>Import-ConsortiumManagementContracts

コンソーシアム管理のスマート コントラクトに接続するには、このコマンドレットを使用します。 これらのコントラクトは、コンソーシアム内のメンバーの管理と適用に使用されます。

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| パラメーター | 説明 | 必須 |
|-----------|-------------|:--------:|
| RootContractAddress | コンソーシアム管理スマート コントラクトのルート コントラクト アドレス | はい |
| Web3Client | New-Web3Connection から取得した Web3Client オブジェクト | はい |

#### <a name="example"></a>例

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Import-Web3Account

リモート ノードの管理アカウントの情報を保持するオブジェクトを作成するには、このコマンドレットを使用します。

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| パラメーター | 説明 | 必須 |
|-----------|-------------|:--------:|
| ManagedAccountAddress | ブロックチェーン メンバーのアカウント アドレス | はい |
| ManagedAccountPassword | アカウント アドレスのパスワード | はい |

#### <a name="example"></a>例

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>New-Web3Connection

トランザクション ノードの RPC エンドポイントへの接続を確立するには、このコマンドレットを使用します。

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| パラメーター | 説明 | 必須 |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | ブロックチェーン メンバーのエンドポイント アドレス | はい |

#### <a name="example"></a>例

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>コンソーシアム メンバーを管理する

コンソーシアム内のメンバーを管理するには、コンソーシアム メンバー管理コマンドレットを使用します。 使用可能なアクションは、コンソーシアムのロールによって異なります。

### <a name="get-blockchainmember"></a>Get-BlockchainMember

メンバーの詳細を取得したり、コンソーシアムのメンバーの一覧を表示したりするには、このコマンドレットを使用します。

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| パラメーター | 説明 | 必須 |
|-----------|-------------|:--------:|
| EnableAdfsAuthentication | 詳細を取得する Blockchain Service メンバーの名前。 名前を入力すると、そのメンバーの詳細が返されます。 名前を省略すると、すべてのコンソーシアム メンバーの一覧が返されます。 | いいえ |
| Members | Import-ConsortiumManagementContracts で取得したメンバー オブジェクト | はい |
| Web3Client | New-Web3Connection から取得した Web3Client オブジェクト | はい |

#### <a name="example"></a>例

```powershell-interactive
$ContractConnection | Get-BlockchainMember -Name <Member Name>
```

#### <a name="example-output"></a>出力例

```
Name           : myblockchainmember
CorrelationId  : 0
DisplayName    : myCompany
SubscriptionId : <Azure subscription ID>
AccountAddress : 0x85b911c9e103d6405573151258d668479e9ebeef
Role           : ADMIN
```

### <a name="remove-blockchainmember"></a>Remove-BlockchainMember

ブロックチェーン メンバーを削除するには、このコマンドレットを使用します。

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| パラメーター | 説明 | 必須 |
|-----------|-------------|:--------:|
| EnableAdfsAuthentication | 削除するメンバーの名前 | はい |
| Members | Import-ConsortiumManagementContracts で取得したメンバー オブジェクト | はい |
| Web3Account | Import-Web3Account で取得した Web3Account オブジェクト | はい |
| Web3Client | New-Web3Connection から取得した Web3Client オブジェクト | はい |

#### <a name="example"></a>例

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

表示名やコンソーシアムのロールなど、ブロックチェーンのメンバーの属性を設定するには、このコマンドレットを使用します。

コンソーシアム管理者は、すべてのメンバーの **DisplayName** と **Role** を設定できます。 ユーザー ロールを持つコンソーシアム メンバーは、自分のメンバーの表示名のみを変更できます。

```
Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| パラメーター | 説明 | 必須 |
|-----------|-------------|:--------:|
| EnableAdfsAuthentication | ブロックチェーン メンバーの名前 | はい |
| DisplayName | 新しい表示名 | いいえ |
| AccountAddress | アカウント アドレス | いいえ |
| Members | Import-ConsortiumManagementContracts で取得したメンバー オブジェクト | はい |
| Web3Account | Import-Web3Account で取得した Web3Account オブジェクト | はい |
| Web3Client |  New-Web3Connection から取得した Web3Client オブジェクト| はい |

#### <a name="example"></a>例

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>コンソーシアム メンバーの招待を管理する

コンソーシアム メンバーの招待を管理するには、コンソーシアム メンバー招待管理コマンドレットを使用します。 使用可能なアクションは、コンソーシアムのロールによって異なります。

### <a name="new-blockchainmemberinvitation"></a>New-BlockchainMemberInvitation

コンソーシアムに新しいメンバーを招待するには、このコマンドレットを使用します。

```
New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| パラメーター | 説明 | 必須 |
|-----------|-------------|:--------:|
| SubscriptionId | 招待するメンバーの Azure サブスクリプション ID | はい |
| Role | コンソーシアムのロール。 使用できる値は ADMIN または USER です。 ADMIN は、コンソーシアムの管理者ロールです。 USER は、コンソーシアムのメンバー ロールです。 | はい |
| Members | Import-ConsortiumManagementContracts で取得したメンバー オブジェクト | はい |
| Web3Account | Import-Web3Account で取得した Web3Account オブジェクト | はい |
| Web3Client | New-Web3Connection から取得した Web3Client オブジェクト | はい |

#### <a name="example"></a>例

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

コンソーシアム メンバーの招待の状態を取得または一覧表示するには、このコマンドレットを使用します。

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| パラメーター | 説明 | 必須 |
|-----------|-------------|:--------:|
| SubscriptionId | 招待するメンバーの Azure サブスクリプション ID。 サブスクリプション ID を指定した場合は、サブスクリプション ID の招待の詳細が返されます。 サブスクリプション ID を省略すると、すべてのメンバーの招待の一覧が返されます。 | いいえ |
| Members | Import-ConsortiumManagementContracts で取得したメンバー オブジェクト | はい |
| Web3Client | New-Web3Connection から取得した Web3Client オブジェクト | はい |

#### <a name="example"></a>例

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation – SubscriptionId <Azure subscription ID>
```

#### <a name="example-output"></a>出力例

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Remove-BlockchainMemberInvitation

コンソーシアム メンバーの招待を取り消すには、このコマンドレットを使用します。

```
Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>
```

| パラメーター | 説明 | 必須 |
|-----------|-------------|:--------:|
| SubscriptionId | 取り消すメンバーの Azure サブスクリプション ID | はい |
| Members | Import-ConsortiumManagementContracts で取得したメンバー オブジェクト | はい |
| Web3Account | Import-Web3Account で取得した Web3Account オブジェクト | はい |
| Web3Client | New-Web3Connection から取得した Web3Client オブジェクト | はい |

#### <a name="example"></a>例

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

既存の招待に対して**ロール**を設定するには、このコマンドレットを使用します。 招待を変更できるのはコンソーシアム管理者のみです。

```
Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| パラメーター | 説明 | 必須 |
|-----------|-------------|:--------:|
| SubscriptionId | 招待するメンバーの Azure サブスクリプション ID | はい |
| Role | 招待の新しいコンソーシアム ロール。 使用できる値は、**USER** または **ADMIN** です。 | はい |
| Members |  Import-ConsortiumManagementContracts で取得したメンバー オブジェクト | はい |
| Web3Account | Import-Web3Account で取得した Web3Account オブジェクト | はい |
| Web3Client | New-Web3Connection から取得した Web3Client オブジェクト | はい |

#### <a name="example"></a>例

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>次の手順

コンソーシアム、メンバー、ノードについて詳しくは、以下をご覧ください。

> [!div class="nextstepaction"]
> [Azure Blockchain Service のコンソーシアム](consortium.md)
