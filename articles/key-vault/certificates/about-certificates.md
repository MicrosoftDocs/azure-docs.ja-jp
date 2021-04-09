---
title: Azure Key Vault の証明書について - Azure Key Vault
description: Azure Key Vault の REST インターフェイスと証明書の概要です。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: b410dc89b286ef830f0d5b6a9c33fe77d380f5d1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102507213"
---
# <a name="about-azure-key-vault-certificates"></a>Azure Key Vault の証明書について

Key Vault 証明書のサポートにより、x509 証明書が管理されるようになります。動作は次のとおりです。  

-   証明書の所有者が、Key Vault 作成プロセスを介して、または既存の証明書をインポートして、証明書を作成できます。 自己署名証明書と証明機関によって生成された証明書の両方が含まれます。
-   Key Vault 証明書の所有者は、秘密キー マテリアルを操作せずに、X509 証明書のセキュリティ保護されたストレージと管理を実装できます。  
-   証明書所有者が、証明書のライフサイクルを管理するように Key Vault が指示するポリシーを作成できます。  
-   証明書所有者が、証明書の失効や更新のライフサイクル イベントを通知するために証明書の連絡先情報を指定できます。  
-   選択した発行者 (Key Vault パートナー X509 証明書プロバイダーまたは証明機関) による自動更新をサポートします。

>[!Note]
>パートナーではないプロバイダー/証明機関も許可されますが、自動更新機能はサポートされません。

## <a name="composition-of-a-certificate"></a>証明書の構成

Key Vault 証明書が作成されると、アドレス指定可能なキーとシークレットも同じ名前で作成されます。 Key Vault のキーを使うとキー操作を行うことができ、Key Vault のシークレットを使うとシークレットとして証明書の値を取得できます。 Key Vault 証明書には、公開 x509 証明書メタデータも含まれます。  

証明書の ID とバージョンは、キーとシークレットに似ています。 Key Vault 証明書のバージョンで作成されるアドレス指定可能なキーとシークレットの特定のバージョンは、Key Vault 証明書の応答で使用できます。
 
![証明書は複合オブジェクトである](../media/azure-key-vault.png)

## <a name="exportable-or-non-exportable-key"></a>エクスポート可能なキーまたはエクスポート不可能なキー

Key Vault 証明書が作成されるとき、秘密キーと共にアドレス指定可能なシークレットから PFX または PEM 形式で取得できます。 証明書の作成に使用されるポリシーでは、キーがエクスポート可能であることが示されている必要があります。 ポリシーでエクスポートできないことが示されている場合、シークレットとして取得されるとき、秘密キーは値に含まれません。  

アドレス指定可能なキーは、エクスポート不可能な KV 証明書との関連性が高くなります。 アドレス指定可能な KV キーの操作は、KV 証明書の作成に使用された KV 証明書ポリシーの *keyusage* フィールドからマップされます。  

証明書でサポートされるキー ペアの種類

 - サポートされているキーの種類: RSA、RSA-HSM、EC、EC-HSM、oct (一覧は[こちら](/rest/api/keyvault/createcertificate/createcertificate#jsonwebkeytype))。エクスポート可能にできるのは、RSA、EC のみです。 HSM キーはエクスポートできません。

|キーの種類|詳細|セキュリティ|
|--|--|--|
|**RSA**| "ソフトウェアで保護される" RSA キー|FIPS 140-2 レベル 1|
|**RSA-HSM**| "HSM で保護された" RSA キー (Premium SKU のみ)|FIPS 140-2 レベル 2 HSM|
|**EC**| "ソフトウェアで保護される" 楕円曲線キー|FIPS 140-2 レベル 1|
|**EC-HSM**| "HSM で保護された" 楕円曲線キー (Premium SKU のみ)|FIPS 140-2 レベル 2 HSM|
|||

## <a name="certificate-attributes-and-tags"></a>証明書の属性とタグ

証明書のメタデータ、アドレス指定可能なキー、およびアドレス指定可能なシークレットだけでなく、Key Vault 証明書にも属性とタグが含まれます。  

### <a name="attributes"></a>属性

証明書の属性は、KV 証明書の作成時に作成されるアドレス指定可能なキーとシークレットの属性に反映されます。  

Key Vault 証明書には次の属性があります。  

-   *enabled*: boolean、省略可能、既定値は **true**。 この属性を指定して、証明書のデータをシークレットとして取得できるか、またはキーとして操作可能かを示すことができます。 また、*nbf* および *exp* と組み合わせて使います。*nbf* と *exp* の間で操作が発生する場合、enabled が true に設定されている場合にのみ許可されます。 *nbf* から *exp* までのウィンドウの外側の操作は、自動的に禁止されます。  

応答に含まれる追加の読み取り専用属性があります。

-   *created*:IntDate: このバージョンの証明書が作成された日時を示します。  
-   *updated*:IntDate: このバージョンの証明書が更新された日時を示します。  
-   *exp*:IntDate: x509 証明書の有効期限日の値を含みます。  
-   *nbf*:IntDate: x509 証明書の有効期間開始日の値を含みます。  

> [!Note] 
> Key Vault 証明書の期限が切れると、アドレス指定可能なキーとシークレットは機能しなくなります。  

### <a name="tags"></a>Tags

 キーとシークレットのタグに似た、クライアントによって指定されたキーと値のペアのディクショナリです。  

 > [!Note]
> 呼び出し元は、そのオブジェクトの種類 (キー、シークレット、証明書) に対して *list* または *get* アクセス許可を持っている場合、タグを読み取ることができます。

## <a name="certificate-policy"></a>証明書ポリシー

証明書ポリシーには、Key Vault 証明書のライフサイクルを作成して管理する方法に関する情報が含まれています。 秘密キーを含む証明書がキー コンテナーにインポートされると、X509 証明書を読み取ることによって既定のポリシーが作成されます。  

Key Vault 証明書を最初から作成するときは、ポリシーを提供する必要があります。 ポリシーでは、この Key Vault 証明書バージョンまたは次の Key Vault 証明書バージョンを作成する方法を指定します。 ポリシーが確立された後は、将来のバージョンに対する後続の作成操作では必要はありません。 Key Vault 証明書のすべてのバージョンに対して、ポリシーのインスタンスは 1 つだけ存在します。  

大まかに言えば、証明書ポリシーには次の情報が含まれています (それらの定義は[こちら](/powershell/module/az.keyvault/set-azkeyvaultcertificatepolicy)から見つけることができます)。  

-   X509 証明書のプロパティ:サブジェクト名、サブジェクト代替名、および x509 証明書要求の作成に使用される他のプロパティが含まれます。  
-   キーのプロパティ: キーの種類、キーの長さ、エクスポート可能、ReuseKeyOnRenewal フィールドが含まれています。 これらのフィールドは、キー コンテナーにキーの生成方法を示します。 
     - サポートされているキーの種類: RSA、RSA-HSM、EC、EC-HSM、oct (一覧は[こちら](/rest/api/keyvault/createcertificate/createcertificate#jsonwebkeytype)にあります) 
-   シークレットのプロパティ: 証明書をシークレットとして取得するための、シークレット値を生成するアドレス指定可能なシークレットのコンテンツの種類などの、シークレットのプロパティが含まれます。  
-   有効期間アクション: KV 証明書の有効期間アクションが含まれます。 各有効期間アクションには次のものが含まれています。  

     - トリガー: 有効期限までの日数または有効期間のパーセンテージで指定されます  

     - アクション: アクションの種類 (*emailContacts* または *autoRenew*) を指定します  

-   発行者:x509 証明書の発行に使用する証明書発行者に関するパラメーターです。  
-   ポリシー属性: ポリシーに関連付けられた属性が含まれます  

### <a name="x509-to-key-vault-usage-mapping"></a>X509 と Key Vault の使用方法の対応

次の表では、x509 キー使用ポリシーと、Key Vault 証明書作成の一部として作成されるキーの有効なキー操作との対応を示します。

|**X509 キー使用フラグ**|**Key Vault キー操作**|**既定の動作**|
|----------|--------|--------|
|DataEncipherment|encrypt、decrypt| 該当なし |
|DecipherOnly|復号化| 該当なし  |
|DigitalSignature|sign、verify| 証明書作成時に使用方法が指定されてない場合の Key Vault の既定値 | 
|EncipherOnly|encrypt| 該当なし |
|KeyCertSign|sign、verify|該当なし|
|KeyEncipherment|wrapKey、unwrapKey| 証明書作成時に使用方法が指定されてない場合の Key Vault の既定値 | 
|NonRepudiation|sign、verify| 該当なし |
|crlsign|sign、verify| 該当なし |

## <a name="certificate-issuer"></a>証明書の発行者

Key Vault 証明書オブジェクトは、x509 証明書を要求するための選択された証明書発行者プロバイダーとの通信に使用される構成を保持しています。  

-   Key Vault は、TLS/SSL 証明書の次の証明書発行者プロバイダーと提携しています

|**プロバイダー名**|**場所**|
|----------|--------|
|DigiCert|パブリック クラウドおよび Azure Government 内のすべての Key Vault サービスの場所でサポートされます|
|GlobalSign|パブリック クラウドおよび Azure Government 内のすべての Key Vault サービスの場所でサポートされます|

Key Vault で証明書発行者を作成するには、その前に、次の前提条件手順 1 と 2 を正常に完了する必要があります。  

1. 証明機関 (CA) プロバイダーにオンボードします  

    -   組織の管理者は、会社 (例: Contoso) を少なくとも 1 つの CA プロバイダーにオンボードする必要があります。  

2. 管理者は、TLS/SSL 証明書を登録 (および更新) するための Key Vault に対する要求者資格情報を作成します  

    -   キー コンテナー内のプロバイダーの発行者オブジェクトの作成に使用される構成を提供します  

証明書ポータルから発行者オブジェクトを作成する方法について詳しくは、[Key Vault 証明書のブログ](/archive/blogs/kv/manage-certificates-via-azure-key-vault)をご覧ください  

Key Vault では、異なる発行者プロバイダー構成で複数の発行者オブジェクトを作成できます。 発行者オブジェクトが作成されたら、その名前を 1 つまたは複数の証明書ポリシーで参照できます。 発行者オブジェクトを参照することで、証明書の作成と更新中に CA プロバイダーに X509 証明書を要求するときに、発行者オブジェクトで指定されている構成を使用するよう Key Vault に指示します。  

発行者オブジェクトはコンテナー内に作成され、同じコンテナー内の KV 証明書でのみ使用できます。  

## <a name="certificate-contacts"></a>証明書の連絡先

証明書の連絡先には、証明書有効期間イベントによってトリガーされる通知を送信する連絡先情報が含まれています。 連絡先情報は、キー コンテナー内のすべての証明書によって共有されます。 通知は、キー コンテナー内の任意の証明書のイベントに指定されているすべての連絡先に送信されます。 証明書の連絡先を設定する方法については、[こちら](overview-renew-certificate.md#steps-to-set-certificate-notifications)を参照してください。  

## <a name="certificate-access-control"></a>証明書のアクセス制御

 証明書のアクセス制御は Key Vault によって管理され、それらの証明書を格納している Key Vault によって提供されます。 証明書のアクセス制御ポリシーは、同じキー コンテナー内のキーとシークレットに対するアクセス制御ポリシーとは別です。 ユーザーは、1 つまたは複数のコンテナーを作成して証明書を保持し、証明書のセグメント化と管理に適切なシナリオを維持することができます。  証明書のアクセスの制御について詳しくは、[こちら](certificate-access-control.md)を参照してください。

## <a name="next-steps"></a>次のステップ

- [Key Vault について](../general/overview.md)
- [キー、シークレット、証明書について](../general/about-keys-secrets-certificates.md)
- [キーについて](../keys/about-keys.md)
- [シークレットについて](../secrets/about-secrets.md)
- [認証、要求、応答](../general/authentication-requests-and-responses.md)
- [Key Vault 開発者ガイド](../general/developers-guide.md)
