---
title: Azure Attestation のトラブルシューティング ガイド
description: 一般的に発生する問題のトラブルシューティング ガイド
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 3ae3e12c11f194b3efcc149382dc952bd74d38b5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97704318"
---
# <a name="microsoft-azure-attestation-troubleshooting-guide"></a>Microsoft Azure Attestation のトラブルシューティング ガイド

Azure Attestation でのエラー処理は、「[Microsoft REST API のガイドライン](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#7102-error-condition-responses)」に従って実装されます。 Azure Attestation API によって返されるエラー応答には、HTTP 状態コードと名前と値のペアが含まれ、これらには、"code" と "message" という名前が付いています。 "code" の値は人間が判読できる、エラーの種類を示すインジケーターです。 "message" の値は、ユーザーを支援し、エラーの詳細を提供することを意図しています。

この記事で対処できない問題が発生している場合は、[Azure サポート ページ](https://azure.microsoft.com/support/options/)で Azure サポート リクエストを送信することもできます。

Azure Attestation によって返されるエラーの例を次に示します。

## <a name="1-http401--unauthorized-exception"></a>1.HTTP–401 :未承認の例外

### <a name="http-status-code"></a>HTTP 状態コード
401

**エラー コード** 権限がありません

**シナリオの例**
  - ユーザーに Attestation の Reader ロールが割り当てられていない場合に Attestation でエラーが発生する
  - ユーザーに適切なロールが割り当てられていないため、構成証明ポリシーを管理できない
  - ユーザーに適切なロールが割り当てられていないため、構成証明ポリシーの署名者を管理できない

Reader ロールを持つユーザーが、PowerShell で構成証明ポリシーを編集しようとしている 

  ```powershell
  Set-AzAttestationPolicy : Operation returned HTTP Status Code 401
At line:1 char:1
+ Set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
  ```

**トラブルシューティングの手順**

構成証明ポリシーまたはポリシーの署名者を表示するには、Azure AD ユーザーに "Actions" のアクセス許可が必要です。
- Microsoft.Attestation/attestationProviders/attestation/read

  このアクセス許可は、"Owner" (ワイルドカードのアクセス許可)、"Reader" (ワイルドカードのアクセス許可)、"Attestation Reader" (Azure Attestation 専用のアクセス許可) などのロールを経由して AD ユーザーに割り当てることができます。

ポリシーの署名者を追加または削除する、またはポリシーを構成するには、Azure AD ユーザーに、"Actions" に対して次のアクセス許可が必要です。
- Microsoft.Attestation/attestationProviders/attestation/write
- Microsoft.Attestation/attestationProviders/attestation/delete

  これらのアクセス許可は、"Owner" (ワイルドカードのアクセス許可)、"Contributor" (ワイルドカードのアクセス許可)、"Attestation Contributor" (Azure Attestation 専用のアクセス許可) などのロールを経由して AD ユーザーに割り当てることができます。

構成証明に既定のプロバイダーを使用するか、カスタム ポリシーを使用した独自のプロバイダーを作成するかは、お客様が選択できます。 カスタム構成証明プロバイダーに構成証明要求を送信するには、ユーザーに "Owner" (ワイルドカードアクセス許可) または "Reader" (ワイルドカードアクセス許可) または "Attestation Reader" ロールが必要です。 既定のプロバイダーには、任意の Azure AD ユーザーがアクセスできます。

PowerShell でロールを確認するには、次のように実行します。

a. PowerShell を起動し、"Connect-AzAccount" コマンドレットを使用して Azure にログインします。

b. Azure ロールの割り当て設定を確認します。


  ```powershell
  $c = Get-AzContext
  Get-AzRoleAssignment -ResourceGroupName $attestationResourceGroup -ResourceName $attestationProvider -ResourceType Microsoft.Attestation/attestationProviders -SignInName $c.Account.Id
  ```

  次のような結果が表示されます。

  ```
  RoleAssignmentId   :/subscriptions/subscriptionId/providers/Microsoft.Authorization/roleAssignments/roleAssignmentId
  
  Scope              : /subscriptions/subscriptionId
  
  DisplayName        : displayName
  
  SignInName         : signInName
  
  RoleDefinitionName : Reader
  
  RoleDefinitionId   : roleDefinitionId
  
  ObjectId           : objectid
  
  ObjectType         : User
  
  CanDelegate        : False
 
  ```

c. 一覧に適切なロールの割り当てが見つからない場合は、[ここ](../role-based-access-control/role-assignments-powershell.md)の手順に従ってください。

## <a name="2-http--400-errors"></a>2.HTTP - 400 エラー

### <a name="http-status-code"></a>HTTP 状態コード
400

要求で 400 が返される理由はさまざまです。 Azure Attestation API によって返されるエラーの例を次に示します。

### <a name="21-attestation-failure-due-to-policy-evaluation-errors"></a>2.1. ポリシー評価エラーによる Attestation の失敗

Attestation ポリシーには、承認規則と発行規則が含まれます。 エンクレーブ証拠は、承認規則に基づいて評価されます。 発行規則では、構成証明トークンに含まれる要求が定義されます。 エンクレーブ証拠の要求が承認規則に準拠していない場合、証明書を呼び出すと、ポリシー評価エラーが返されます。 

**エラー コード** PolicyEvaluationError

**シナリオ例** エンクレーブ引用の要求が構成証明ポリシーの承認規則と一致しない場合

```
Native operation failed with 65518: G:\Az\security\Attestation\src\AttestationServices\Instance\NativePolicyWrapper\NativePolicyEngine.cpp(168)\(null)!00007FF801762308: (caller: 00007FF80143DCC8) Exception(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Policy Engine Exception: A Deny claim was issued, authorization failed.]

G:\Az\security\Attestation\src\AttestationServices\Instance\Enclave\api.cpp(840)\(null)!00007FF801739FF3: (caller: 00007FF801232801) LogHr(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Unhandled Enclave Exception: "Policy Evaluation Error has occurred"]

```

**トラブルシューティングの手順** 同様に構成する前に、ユーザーは SGX 構成証明ポリシーに対してエンクレーブ証拠を評価することができます。

"draftPolicyForAttestation" パラメーターにポリシー テキストを指定して、構成証明 API に要求を送信します。 AttestSgxEnclave API は、構成証明の呼び出し中にこのポリシードキュメントを使用し、これを使用して、構成証明ポリシーを使用する前にテストすることができます。 このフィールドが存在するときに生成された構成証明トークンは、セキュリティで保護されません。

[構成証明ポリシーの例](./policy-examples.md)を参照してください。

### <a name="22-attestation-failure-due-to-invalid-input"></a>2.2. 無効な入力による Attestation の失敗

**エラー コード** InvalidParameter

**シナリオ例** 入力が無効であるため、SGX 構成証明が失敗する場合。 次に、エラー メッセージの例をいくつか示します。
- 引用物にエラーがあるため、指定された引用は無効です 
- 引用が生成されたデバイスが Azure のベースライン要件を満たしていないため、指定された引用は無効です
- PCK キャッシュ サービスによって提供された TCBInfo または QEID が無効なため、指定された引用は無効です

**トラブルシューティングの手順**

Microsoft Azure Attestation は、Intel SDK および Open Enclave SDK によって生成された SGX 引用の構成証明をサポートしています。

Open Enclave SDK または Intel SDK を使用して構成証明を実行する方法については、[コード例](/samples/browse/?expanded=azure&terms=attestation)を参照してください。

### <a name="23-invalid-certificate-chain-error-while-uploading-policypolicy-signer"></a>2.3. ポリシーまたはポリシー署名者のアップロード中に発生する無効な証明書チェーン エラー

**エラー コード** InvalidParameter

**シナリオ例** 署名付きポリシーの構成、または無効な証明書チェーン (ルート証明書の基本制限の拡張機能が Subject Type = CA に設定されていない場合など) で署名されたポリシー署名者の追加または削除

```
Native operation failed with 65529: C:\source\src\AttestationServices\Instance\SgxPal\sgxcert.cpp(1074)\(null)!00007FFA285CDAED: (caller: 00007FFA285C36E8) Exception(0) 83FFFFF9 The requested item is not found    Msg:[Unable to find issuer certificate CN=attestationsigningcert]
C:\source\src\AttestationServices\Instance\Enclave\api.cpp(618)\(null)!00007FFA286DCBF8: (caller: 00007FFA285860D3) LogHr(0) 83FFFFF9 The requested item is not found    Msg:[Unhandled Enclave Exception: "The requested item is not found"]
At line:1 char:1
+ Set-AzAttestationPolicy -Name "testpolicy1" -ResourceGroupName "BugBa ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy

```

**トラブルシューティング手順** ルート証明書は、CA によって発行されたものとしてフラグが付けられている (x.509 の基本制限) 必要があり、そうでないと、有効な証明書と見なされません。 

ルート証明書の基本制限の拡張機能が、Subject Type = CA であることを示すように設定されていることを確認します。

そうでないと、証明書チェーンは無効であると見なされます。

[ポリシー署名者](./policy-signer-examples.md)の例と[ポリシー](./policy-examples.md)の例を参照してください。 

### <a name="24-adddelete-policy-signer-failure"></a>2.4. ポリシー署名者の追加または削除エラー

**エラー コード** InvalidOperation

**シナリオの例**

ユーザーが "maa-policyCertificate" 要求なしで JWS をアップロードした場合

```
Add-AzAttestationPolicySigner : Operation returned HTTP Status Code 400
Code: InvalidOperation
Message: Native operation failed with 74: ..\Enclave\enclave.cpp(2213)\(null)!: (caller: ) Exception(0) 83FF004A Bad
message    Msg:[Could not find "maa-policyCertificate" claim in policy token]
..\Enclave\api.cpp(496)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad
message"]
At line:1 char:1
+ Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGro ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Add-AzAttestationPolicySigner], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.AddAzureAttestationPolicySigner

```

ユーザーが JWS 形式で証明書をアップロードしない場合

```
Add-AzAttestationPolicySigner : Operation returned HTTP Status Code 400
Code: InvalidOperation
Message: Native operation failed with 74: ..\JsonWebToken\jsonwebtoken.cpp(375)\(null)!: (caller: ) LogHr(0) 83FF004A
Bad message    Msg:[RETURN_IF_TRUE('(firstPeriod == std::string::npos)') failed with 0x4a: Malformed JWT: Could not
find first period in the token.]
..\Enclave\enclave.cpp(2106)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message
Msg:[THROW_IF_ERROR('DecomposeJsonWebSignature(&policyJws, encodedJoseHeader, encodedJwsBody, jwsSignature)') failed
with 0x4a: 'Bad message']
..\Enclave\enclave.cpp(2106)\(null)!: (caller: ) Exception(0) 83FF004A Bad message
..\Enclave\api.cpp(496)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad
message"]
At line:1 char:1
+ Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGro ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Add-AzAttestationPolicySigner], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.AddAzureAttestationPolicySigner
```

**トラブルシューティングの手順** 新しいポリシー署名者証明書を追加または削除するには、"x-ms-policyCertificate" という名前の要求を持つ RFC7519 JSON Web Token (JWT) を使用します。 要求の値は RFC7517 JSON Web キーです。このキーには、追加する証明書が含まれます。 JWT は、プロバイダーに関連付けられている有効なポリシー署名者証明書の秘密キーで署名されている必要があります。 [ポリシー署名者の例](./policy-signer-examples.md)を参照してください。

### <a name="25-attestation-policy-configuration-failure"></a>2.5. Attestation ポリシーの構成エラー

**エラー コード** PolicyParsingError

**シナリオ例** ポリシーが、正しくない構文 (セミコロン、または有効な JWT ポリシーが不足しているなど) で指定された場合

```
Native operation failed with 65526: ..\NativePolicyWrapper\NativePolicyEngine.cpp(31)\(null)!: (caller: ) Exception(0) 83FFFFF6 Invalid policy was specified    Msg:[Policy Parser Exception Thrown: Offending
symbol: '['
Line: '2', Column: '1'
Failure message: 'mismatched input '[' expecting ';''
Failing rule: 'policy > versionInfo']
..\Enclave\api.cpp(618)\(null)!: (caller: ) LogHr(0) 83FFFFF6 Invalid policy was specified    Msg:[Unhandled Enclave Exception: "Invalid policy was specified"]
At line:1 char:1
+ set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
```

**エラー コード** InvalidOperation

**シナリオ例** 無効なコンテンツが提供された (ポリシーのアップロードまたはポリシーの署名が必要なときに署名なしのポリシーなど)

```
Native operation failed with 74: ..\Shared\base64url.h(226)\(null)!: (caller: ) Exception(0) 83FF004A Bad message    Msg:[Unknown base64 character: 41 (')')]
..\Enclave\api.cpp(618)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad message"]
At line:1 char:1
+ set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
```

**トラブルシューティングの手順** テキスト形式のポリシーが、UTF-8 でエンコードされていることを確認します。

ポリシーの署名が必要な場合は、構成証明ポリシーは RFC7519 JSON Web Token (JWT) 形式でのみ構成する必要があります。 ポリシーの署名が不要な場合は、テキスト形式または JWT 形式でポリシーを構成できます。

JWT 形式でポリシーを構成するには、"AttestationPolicy" という名前の要求と一緒に JWT を使用します。 要求の値は、Base64URL エンコード バージョンのポリシー テキストです。 構成証明プロバイダーがポリシー署名者の証明書で構成されている場合、JWT は、プロバイダーに関連付けられた有効なポリシー署名者証明書の秘密キーで署名する必要があります。 

テキスト形式でポリシーを構成するには、ポリシー テキストを直接指定します。

PowerShell では、JWT 形式でポリシーを構成するための PolicyFormat を JWT として指定します。 既定のポリシー形式はテキストです。

構成証明の[ポリシーの例](./policy-examples.md)と[構成証明ポリシーの作成方法](./author-sign-policy.md)を参照してください。 

## <a name="3-azattestation-installation-issues-in-powershell"></a>3.PowerShell での Az.Attestation のインストールに関する問題

PowerShell で Az または Az.Attestation モジュールをインストールできない

### <a name="error"></a>エラー

警告:パッケージ ソース 'https://www.powershellgallery.com/api/v2 ' PackageManagement\Install-Package を解決できない指定された検索条件とモジュール名に一致するものが見つかりませんでした

### <a name="troubleshooting-steps"></a>トラブルシューティングの手順

PowerShell ギャラリーには、非推奨のトランスポート層セキュリティ (TLS) バージョン 1.0 および 1.1 が存在します。 

TLS 1.2 以降のバージョンが推奨されます。 

PowerShell ギャラリーの操作を続行するには、Install-Module コマンドの前に次のコマンドを実行してください。

**[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12**

## <a name="4-policy-accessconfiguration-issues-in-powershell"></a>4.PowerShell でのポリシーのアクセスと構成に関する問題

ユーザーに適切なロールが割り当てられています。 しかし、PowerShell を使用して構成証明ポリシーを管理するときに、承認の問題に直面します。

### <a name="error"></a>エラー
オブジェクト ID が &lt;object Id&gt; のクライアントに、スコープ ‘subcriptions/&lt;subscriptionId&gt;resourcegroups/secure_enclave_poc/providers/Microsoft.Authorization/roleassignments/&lt;role assignmentId&gt;’ に対して Microsoft.Authorization/roleassignments/write アクションを実行するための許可がないか、スコープが無効です。 アクセス許可が最近付与された場合は、資格情報を更新してください。

### <a name="troubleshooting-steps"></a>トラブルシューティングの手順

構成証明の操作をサポートするうえで必要な Az モジュールの最小バージョンは次のとおりです。 

 **Az 4.5.0** 
 
 **Az.Accounts 1.9.2**
 
 **Az.Attestation 0.1.8** 

すべての Az モジュールについてインストールされているバージョンを確認するには、次のコマンドを実行します。 

```powershell
Get-InstalledModule 
```

バージョンが最小要件を満たしていない場合は、Update-Module コマンドを実行します。

例: - Update-Module -Name Az.Attestation
