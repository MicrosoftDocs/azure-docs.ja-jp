---
title: Azure Attestation の要求セット
description: Azure Attestation の要求セット。
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 555dccbd3c2dfe61bac5891514deface6f8a877d
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89237068"
---
# <a name="claim-sets"></a>要求セット

Microsoft Azure Attestation を使用したエンクレーブの構成証明の過程で生成される要求は、以下のカテゴリに分類できます。

- **入力要求**: 構成証明の証拠を解析した後に Microsoft Azure Attestation によって生成される要求。

- **出力要求**: Azure Attestation によって出力として作成される要求。 これには、最終的に構成証明トークンに追加される要求がすべて含まれます。

- **プロパティ要求**: Azure Attestation によって出力として作成される要求。 これには、レポートのエンコード、レポートの有効期間など、構成証明トークンのプロパティを表すすべての要求が含まれます。

JWT RFC によって定義され、かつ Azure Attestation によって応答オブジェクトの中で使用される要求は以下のとおりです。

- **"iss" (発行者) 要求**: "iss" (発行者) 要求では、JWT を発行した元本を指定します。 一般に、この要求の処理はアプリケーション固有です。 "iss" 値は、StringOrURI 値を含む大文字と小文字が区別される文字列です。
- **"iat" (発行時刻) 要求**: "iat" (発行時刻) 要求では、JWT が発行された時刻を指定します。 この要求を使用して、JWT の経過時間を特定できます。 その値は、NumericDate 値を含む数値でなければなりません。
- **"exp" (有効期限) 要求**: "exp" (有効期限) 要求は、JWT の処理を受け入れることができなくなる時刻を指定します。 "exp" 要求を処理するためには、現在の日時が、"exp" 要求で示されている有効期限よりも前でなければなりません。

  注:時刻のずれを考慮するために、発行時刻 (iat) に 5 分の猶予が追加されます。
- **"nbf" (期間の開始時刻) 要求**: "nbf" (期間の開始時刻) 要求では、指定した時刻よりも後に JWT の処理を受け入れることができるようになります。 "nbf" 要求を処理するためには、現在の日時が、"nbf" 要求で示されている日時以降でなければなりません。
  注:時刻のずれを考慮するために、発行時刻 (iat) に 5 分の猶予が追加されます。

## <a name="claims-issued-by-azure-attestation-in-sgx-enclaves"></a>SGX エンクレーブで Azure Attestation によって発行される要求

### <a name="incoming-claims"></a>入力要求 

- **$is-debuggable**: エンクレーブでデバッグが有効になっているかどうかを示すブール値
- **$sgx-mrsigner**: クォートの "mrsigner" フィールドを 16 進数でエンコードした値
- **$sgx-mrenclave**: クォートの "mrenclave" フィールドを 16 進数でエンコードした値
- **$product-id**
- **$svn**: クォートでエンコードされたセキュリティ バージョン番号 
- **$tee**: エンクレーブの種類 

### <a name="outgoing-claims"></a>出力要求

- **is-debuggable**: エンクレーブでデバッグが有効になっているかどうかを示すブール値
- **sgx-mrsigner**: クォートの "mrsigner" フィールドを 16 進数でエンコードした値
- **sgx-mrenclave**: クォートの "mrenclave" フィールドを 16 進数でエンコードした値
- **product-id**
- **svn**: クォートでエンコードされたセキュリティ バージョン番号 
- **tee**: エンクレーブの種類 
- **maa-ehd**: 構成証明要求で指定された "エンクレーブ保持データ" を Base64Url でエンコードしたバージョン 
- **aas-ehd**: 構成証明要求で指定された "エンクレーブ保持データ" を Base64Url でエンコードしたバージョン 

## <a name="claims-issued-by-azure-attestation-in-vbs-enclaves"></a>VBS エンクレーブで Azure Attestation によって発行される要求

### <a name="incoming-claims-can-also-be-used-as-outgoing-claims"></a>入力要求 (出力要求として使用することも可能)

- **aikValidated**: 構成証明識別キー (AIK) の証明書が検証済みであるかどうかの情報を含むブール値。
- **aikPubHash**: base64(SHA256(DER 形式の AIK 公開キー)) を含む文字列。
- **tpmVersion**:  トラステッド プラットフォーム モジュール (TPM) のメジャー バージョンを含む整数値。
- **secureBootEnabled**: セキュア ブートが有効であるかどうかを示すブール値。
- **iommuEnabled**: IOMMU (Input-Output Memory Management Unit) が有効であるかどうかを示すブール値。
- **bootDebuggingDisabled**: ブート デバッグが無効であるかどうかを示すブール値。
- **notSafeMode**: Windows がセーフ モードで実行されていないかどうかを示すブール値。
- **notWinPE**: Windows が WinPE モードで実行されていないかどうかを示すブール値。
- **vbsEnabled**: VBS が有効であるかどうかを示すブール値。
- **vbsReportPresent**: VBS エンクレーブ レポートが利用できるかどうかを示すブール値。
- **enclaveAuthorId**: エンクレーブの作成者 ID (エンクレーブのプライマリ モジュールの作成者識別子) を Base64Url でエンコードした値を含む文字列値。
- **enclaveImageId**: エンクレーブのイメージ ID (エンクレーブのプライマリ モジュールのイメージ識別子) を Base64Url でエンコードした値を含む文字列値。
- **enclaveOwnerId**: エンクレーブの所有者 ID (エンクレーブの所有者識別子) を Base64Url でエンコードした値を含む文字列値。
- **enclaveFamilyId**: エンクレーブのファミリ ID (エンクレーブのプライマリ モジュールのファミリ識別子) を Base64Url でエンコードした値を含む文字列値。
- **enclaveSvn**: エンクレーブのプライマリ モジュールのセキュリティ バージョン番号を含む整数値。
- **enclavePlatformSvn**: エンクレーブをホストするプラットフォームのセキュリティ バージョン番号を含む整数値。
- **enclaveFlags**: enclaveFlags 要求は、エンクレーブのランタイム ポリシーを表すフラグを含んだ整数値。
  
### <a name="outgoing-claims"></a>出力要求

- **policy_hash**: BASE64URL によって計算されたポリシー テキストの SHA256 ハッシュを含む文字列値 (SHA256(BASE64URL(UTF8(ポリシー テキスト))))。
- **policy_signer**: 署名済みのポリシー ヘッダーに存在する証明書チェーンまたは公開キーを含む JWK を含んだ文字列値。
- **ver (バージョン)** : レポートのバージョンを含んだ文字列値。 現在は 1.0 です。
- **cnf (確認) 要求**: "cnf" 要求は、所有証明キーを識別するために使用されます。 RFC 7800 で定義された確認要求には、JSON Web Key (JWK) オブジェクト (RFC 7517) として表された構成証明済みエンクレーブ キーのパブリック部分が含まれます。
- **rp_data (証明書利用者データ)** : 要求で指定された証明書利用者データ (存在する場合)。証明書利用者がレポートの鮮度を保証するために nonce として使用されます。
- **"jti" (JWT ID) 要求**: "jti" (JWT ID) 要求は、JWT の一意の識別子を提供します。 この識別子の値は、同じ値が誤って異なるデータ オブジェクトに割り当てられる可能性が無視できるほど小さい方法で割り当てられます。

### <a name="property-claims"></a>プロパティ要求

- **report_validity_in_minutes**: トークンの有効期間を示す整数要求。
  - **既定値 (時間)** : 1 日 (分単位)。
  - **最大値 (時間)** : 1 年 (分単位)。
- **omit_x5c**: サービスの信頼性の証しとなる証明書を Azure Attestation が省略すべきかどうかを示すブール値要求。 true の場合、構成証明トークンに x5t が追加されます。 false (既定値) の場合、構成証明トークンに x5c が追加されます。

## <a name="next-steps"></a>次のステップ
- [構成証明ポリシーを作成して署名する方法](author-sign-policy.md)
- [PowerShell を使用して Azure Attestation を設定する](quickstart-powershell.md)
