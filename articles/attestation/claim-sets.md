---
title: Azure Attestation の要求セット
description: Azure Attestation の要求セット。
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 23bcfcb92a7fa642e111a67bf92c1306a606bb2a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101704805"
---
# <a name="claim-sets"></a>要求セット

Microsoft Azure Attestation を使用したエンクレーブの構成証明の過程で生成される要求は、以下のカテゴリに分類できます。

- **入力要求**: 構成証明の証拠を解析した後に Microsoft Azure Attestation によって生成される要求。ポリシーの作成者がカスタム ポリシーに承認規則を定義する際に使用できます

- **出力要求**: Azure Attestation によって生成される要求。最終的に構成証明トークンに追加される要求がすべて含まれます

- **プロパティ要求**: Azure Attestation によって出力として作成される要求。 これには、レポートのエンコード、レポートの有効期間など、構成証明トークンのプロパティを表すすべての要求が含まれます。

### <a name="common-incoming-claims-across-all-attestation-types"></a>すべての構成証明タイプに共通する入力要求

次の要求は、Azure Attestation によって生成されます。すべての構成証明タイプのカスタム ポリシーに承認規則を定義する際に、ポリシー作成者が使用できます。

- **x-ms-ver**: JWT スキーマ バージョン ("1.0")
- **x-ms-attestation-type**: 構成証明のタイプを表す文字列値 
- **x-ms-policy-hash**: BASE64URL(SHA256(UTF8(BASE64URL(UTF8(policy text))))) として計算される Azure Attestation 評価ポリシーのハッシュ
- **x-ms-policy-signer**: "jwk" メンバーを含む JSON オブジェクト。"jwk" メンバーは、ユーザーが署名済みのポリシーをアップロードするとき、そのポリシーに署名する際に使用したキーを表します。

以下の要求は非推奨と見なされてはいますが、完全にサポートされています。 非推奨となっていない要求名の使用をお勧めします。

非推奨の要求 | 推奨される要求 
--- | --- 
ver | x-ms-ver
tee | x-ms-attestation-type
maa-policyHash | x-ms-policy-hash
policy_hash | x-ms-policy-hash
policy_signer | x-ms-policy-signer

### <a name="common-outgoing-claims-across-all-attestation-types"></a>すべての構成証明タイプに共通する出力要求

次の要求は、サービスによって、すべての構成証明タイプの構成証明トークンに含まれます。

ソース: [IETF JWT](https://tools.ietf.org/html/rfc7519) で定義されているとおり

- **"jti" (JWT ID) 要求**
- **"iss" (発行者) 要求**
- **"iat" (発行時刻) 要求**
- **"exp" (有効期限) 要求**
- **"nbf" (期間の開始時刻) 要求**

ソース: [IETF EAT](https://tools.ietf.org/html/draft-ietf-rats-eat-03#page-9) で定義されているとおり

- **"Nonce 要求" (nonce)**

次の要求は、入力要求に基づいて、既定で構成証明トークンに含まれます。

- **x-ms-ver**: JWT スキーマ バージョン ("1.0")
- **x-ms-attestation-type**: 構成証明のタイプを表す文字列値 
- **x-ms-policy-hash**: BASE64URL によって計算されたポリシー テキストの SHA256 ハッシュを含む文字列値 BASE64URL(SHA256(UTF8(BASE64URL(UTF8(ポリシー テキスト)))))
- **x-ms-policy-signer**: 署名済みのポリシー ヘッダーに存在する証明書チェーンまたは公開キーを含む JWK が含まれます。 x-ms-policy-signer は、ポリシーが署名されている場合にのみ追加されます

## <a name="claims-specific-to-sgx-enclaves"></a>SGX エンクレーブに固有の要求

### <a name="incoming-claims-specific-to-sgx-attestation"></a>SGX 構成証明に固有の入力要求

次の要求は、Azure Attestation によって生成されます。SGX 構成証明のカスタム ポリシーに承認規則を定義する際に、ポリシー作成者が使用できます。

- **x-ms-sgx-is-debuggable**: エンクレーブでデバッグが有効になっているかどうかを示すブール値
- **x-ms-sgx-product-id**
- **x-ms-sgx-mrsigner**: クォートの "mrsigner" フィールドを 16 進数でエンコードした値
- **x-ms-sgx-mrenclave**: クォートの "mrenclave" フィールドを 16 進数でエンコードした値
- **x-ms-sgx-svn**: クォートでエンコードされたセキュリティ バージョン番号 

### <a name="outgoing-claims-specific-to-sgx-attestation"></a>SGX 構成証明に固有の出力要求

次の要求は、SGX 構成証明のサービスによって生成され、構成証明トークンに含まれます。

- **x-ms-sgx-is-debuggable**: エンクレーブでデバッグが有効になっているかどうかを示すブール値
- **x-ms-sgx-product-id**
- **x-ms-sgx-mrsigner**: クォートの "mrsigner" フィールドを 16 進数でエンコードした値
- **x-ms-sgx-mrenclave**: クォートの "mrenclave" フィールドを 16 進数でエンコードした値
- **x-ms-sgx-svn**: クォートでエンコードされたセキュリティ バージョン番号 
- **x-ms-sgx-ehd**: エンクレーブ保持データ。BASE64URL(エンクレーブ保持データ) として書式設定されます。
- **x-ms-sgx-collateral**: 構成証明の実行に使用される付帯情報を表す JSON オブジェクト。 x-ms-sgx-collateral 要求の値は、次のキーと値のペアを含んだ入れ子になった JSON オブジェクトです。
    - **qeidcertshash**: QE Identity の発行側の証明書の SHA256 値
    - **qeidcrlhash**: QE Identity の発行側の証明書の CRL 一覧の SHA256 値
    - **qeidhash**: QE Identity の付帯情報の SHA256 値
    - **quotehash**: 評価されたクォートの SHA256 値
    - **tcbinfocertshash**: TCB Info の発行側の証明書の SHA256 値
    - **tcbinfocrlhash**: TCB Info の発行側の証明書の CRL 一覧の SHA256 値
    - **tcbinfohash**: 構成証明の実行に使用される付帯情報を表す JSON オブジェクト

以下の要求は非推奨と見なされてはいますが、完全にサポートされており、今後も引き続き追加されます。 非推奨となっていない要求名の使用をお勧めします。

非推奨の要求 | 推奨される要求
--- | --- 
$is-debuggable | x-ms-sgx-is-debuggable
$sgx-mrsigner | x-ms-sgx-mrsigner
$sgx-mrenclave | x-ms-sgx-mrenclave
$product-id | x-ms-sgx-product-id
$svn | x-ms-sgx-svn
$tee | x-ms-attestation-type
maa-ehd | x-ms-sgx-ehd
aas-ehd | x-ms-sgx-ehd
maa-attestationcollateral | x-ms-sgx-collateral

## <a name="claims-specific-to-trusted-platform-module-tpm-vbs-attestation"></a>トラステッド プラットフォーム モジュール (TPM) または VBS の構成証明でのみ発行される要求

### <a name="incoming-claims-for-tpm-attestation"></a>TPM の構成証明に使用される入力要求

TPM の構成証明用に Azure Attestation によって発行される要求。 要求の状態は、構成証明用に提供される証拠によって異なります。

- **aikValidated**: 構成証明識別キー (AIK) の証明書が検証済みであるかどうかの情報を含むブール値
- **aikPubHash**: base64(SHA256(DER 形式の AIK 公開キー)) を含む文字列
- **tpmVersion**:  トラステッド プラットフォーム モジュール (TPM) のメジャー バージョンを含む整数値
- **secureBootEnabled**: セキュア ブートが有効であるかどうかを示すブール値
- **iommuEnabled**: IOMMU (Input-Output Memory Management Unit) が有効であるかどうかを示すブール値
- **bootDebuggingDisabled**: ブート デバッグが無効であるかどうかを示すブール値
- **notSafeMode**: Windows がセーフ モードで実行されていないかどうかを示すブール値
- **notWinPE**: Windows が WinPE モードで実行されていないかどうかを示すブール値
- **vbsEnabled**: VBS が有効であるかどうかを示すブール値
- **vbsReportPresent**: VBS エンクレーブ レポートが利用できるかどうかを示すブール値

### <a name="incoming-claims-for-vbs-attestation"></a>VBS の構成証明に使用される入力要求

VBS の構成証明用に Azure Attestation によって発行される要求は、TPM の構成証明用の要求に追加されるものです。 要求の状態は、構成証明用に提供される証拠によって異なります。

- **enclaveAuthorId**: エンクレーブの作成者 ID (エンクレーブのプライマリ モジュールの作成者識別子) を Base64Url でエンコードした値を含む文字列値
- **enclaveImageId**: エンクレーブのイメージ ID (エンクレーブのプライマリ モジュールのイメージ識別子) を Base64Url でエンコードした値を含む文字列値
- **enclaveOwnerId**: エンクレーブの所有者 ID (エンクレーブの所有者識別子) を Base64Url でエンコードした値を含む文字列値
- **enclaveFamilyId**: エンクレーブのファミリ ID を Base64Url でエンコードした値を含む文字列値。 エンクレーブのプライマリ モジュールのファミリ ID です
- **enclaveSvn**: エンクレーブのプライマリ モジュールのセキュリティ バージョン番号を含む整数値
- **enclavePlatformSvn**: エンクレーブをホストするプラットフォームのセキュリティ バージョン番号を含む整数値
- **enclaveFlags**: enclaveFlags 要求は、エンクレーブのランタイム ポリシーを表すフラグを含んだ整数値

### <a name="outgoing-claims-specific-to-tpm-and-vbs-attestation"></a>TPM と VBS の構成証明に固有の出力要求

- **cnf (確認)** : "cnf" 要求は、所有証明キーを識別するために使用されます。 RFC 7800 で定義された確認要求には、JSON Web Key (JWK) オブジェクト (RFC 7517) として表された構成証明済みエンクレーブ キーのパブリック部分が含まれます
- **rp_data (証明書利用者データ)** : 要求で指定された証明書利用者データ (存在する場合)。証明書利用者がレポートの鮮度を保証するために nonce として使用されます。 rp_data は、rp_data が存在する場合にのみ追加されます

### <a name="property-claims"></a>プロパティ要求

- **report_validity_in_minutes**: トークンの有効期間を示す整数要求。
  - **既定値 (時間)** : 1 日 (分単位)。
  - **最大値 (時間)** : 1 年 (分単位)。
- **omit_x5c**: サービスの信頼性の証しとなる証明書を Azure Attestation が省略すべきかどうかを示すブール値要求。 true の場合、構成証明トークンに x5t が追加されます。 false (既定値) の場合、構成証明トークンに x5c が追加されます。

## <a name="next-steps"></a>次のステップ
- [構成証明ポリシーを作成して署名する方法](author-sign-policy.md)
- [PowerShell を使用して Azure Attestation を設定する](quickstart-powershell.md)
