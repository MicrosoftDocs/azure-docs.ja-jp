---
title: Key Vault 証明書の概要
description: 次のシナリオでは、キー コンテナー内に最初の証明書を作成するために必要な追加の手順を含め、Key Vault の証明書管理サービスの主な使用方法をいくつか概説します。
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 06/13/2020
ms.author: mbaldwin
ms.openlocfilehash: 85f17897c0e3089a2d2bc5b172e98fa24e8085ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94920442"
---
# <a name="get-started-with-key-vault-certificates"></a>Key Vault 証明書の概要
次のシナリオでは、キー コンテナー内に最初の証明書を作成するために必要な追加の手順を含め、Key Vault の証明書管理サービスの主な使用方法をいくつか概説します。

以下の概要を示します。
- 最初の Key Vault 証明書の作成
- Key Vault と提携している証明機関での証明書の作成
- Key Vault と提携していない証明機関での証明書の作成
- 証明書のインポート

## <a name="certificates-are-complex-objects"></a>証明書は複合オブジェクトである
証明書は、Key Vault 証明書として互いにリンクされている相互に関連付けられた 3 つのリソース (証明書メタデータ、キー、シークレット) から構成されます。


![証明書は複合的である](../media/azure-key-vault.png)


## <a name="creating-your-first-key-vault-certificate"></a>最初の Key Vault 証明書の作成  
 証明書を Key Vault (KV) に作成するには、その前に前提条件手順 1. と 2. を正常に完了する必要があり、キー コンテナーがこのユーザー/組織に存在する必要があります。  

**手順 1** - 証明機関 (CA) プロバイダー  
-   特定の会社 (たとえば、 Contoso など) の IT 管理者、PKI 管理者または CA のアカウントを管理する任意のユーザーとしてのオンボーディングは、Key Vault 証明書を使用するための前提条件です。  
    次の CA は、現在 Key Vault と提携しているプロバイダーです。 詳しくは[こちら](./create-certificate.md#partnered-ca-providers)をご覧ください   
    -   DigiCert - Key Vault は、DigiCert による OV TLS/SSL 証明書を提供します。  
    -   GlobalSign - Key Vault は、GlobalSign による OV TLS/SSL 証明書を提供します。  

**手順 2** - CA プロバイダーのアカウント管理者は、Key Vault が使用する資格証明を作成して、Key Vault を介して TLS/SSL 証明書を登録、更新、使用します。

**手順 3** - Contoso 管理者と証明書を所有する Contoso 従業員 (Key Vault ユーザー) は、CA に応じて、証明書を管理者から取得するか、CA のアカウントから直接取得できます。  

- [証明書の発行者](/rest/api/keyvault/setcertificateissuer/setcertificateissuer)リソースを設定することで、キー コンテナーへの資格情報の追加操作を開始します。 証明書の発行者は、Azure Key Vault (KV) で CertificateIssuer リソースとして表示されるエンティティです。 これは、KV 証明書のソースに関する情報 (発行者名、プロバイダー、資格情報、その他の管理ための詳細情報) の提供に使用されます。
  - 例: MyDigiCertIssuer  
    -   プロバイダー  
    -   資格情報 - CA アカウント資格情報。 各 CA が固有の特定のデータを持ちます。  

    CA プロバイダーでのアカウントの作成について詳しくは、[Key Vault のブログ](/archive/blogs/kv/manage-certificates-via-azure-key-vault)で関連する投稿をご覧ください。  

**手順 3.1** - 通知用の [証明書連絡先](/rest/api/keyvault/setcertificatecontacts/setcertificatecontacts)を設定します。 これは、Key Vault ユーザーの連絡先です。 Key Vault はこの手順を適用しません。  

注 - 手順 3.1 までのこのプロセスは、1 回のみの操作です。  

## <a name="creating-a-certificate-with-a-ca-partnered-with-key-vault"></a>Key Vault と提携している CA での証明書の作成

![Key Vault と提携している証明機関での証明書の作成](../media/certificate-authority-2.png)

**手順 4** - 次の説明は、前の図の緑の番号付きの手順に対応しています。  
  (1) - 上の図で、アプリケーションは証明書を作成しています。これは、キー コンテナーにキーを作成することで内部的に開始します。  
  (2) - Key Vault は、TLS/SSL 証明書要求を CA に送信します。  
  (3) - アプリケーションは、ループおよび待機プロセスで Key Vault に証明書の完了をポーリングします。 Key Vault が CA の応答で x509 証明書を受信すると、証明書の作成が完了します。  
  (4) - CA は、X509 TLS/SSL 証明書で Key Vault の TLS/SSL 証明書要求に応答します。  
  (5) - 新しい証明書の作成は、CA の X509 証明書の合併で完了します。  

  Key Vault ユーザー - ポリシーを指定することで証明書を作成します

  -   必要に応じて繰り返します  
  -   ポリシー制約  
      -   X509 のプロパティ  
      -   キーのプロパティ  
      -   プロバイダー リファレンス - > 例: MyDigiCertIssure  
      -   更新情報 - > 例: 有効期限まで 90 日  

  - 証明書の作成プロセスは、通常は非同期プロセスで、キー コンテナーへの証明書の作成操作の状態のポーリングが含まれます。  
[証明書の取得操作](/rest/api/keyvault/getcertificateoperation/getcertificateoperation)  
      -   状態: 完了、エラー情報ありで失敗、またはキャンセル済み  
      -   作成の遅延のため、キャンセル操作を開始できます。 キャンセルは、有効な場合と有効でない場合とがあります。  

### <a name="network-security-and-access-policies-associated-with-integrated-ca"></a>統合 CA に関連付けられたネットワーク セキュリティとアクセス ポリシー
Key Vault サービスは要求を CA に送信します (送信トラフィック)。 したがって、ファイアウォール対応のキー コンテナーと完全に互換性があります。 Key Vault はアクセス ポリシーを CA と共有しません。 署名要求を個別に受け入れるように CA を構成する必要があります。 [信頼された CA の統合に関するガイド](./how-to-integrate-certificate-authority.md)

## <a name="import-a-certificate"></a>証明書のインポート  
 代わりに、証明書を Key Vault にインポートできます (PFX または PEM)。  

 証明書のインポート - PEM または PFX がディスク上に必要で、秘密キーが必要です。 
-   コンテナー名と証明書名 (ポリシーは省略可能) を指定する必要があります

-   PEM/PFX ファイルには、KV が解析して証明書のポリシーの設定に使用できる属性が含まれています。 証明書のポリシーが既に指定されている場合、KV は PFX/PEM ファイルからデータを照合しようとします。  

-   インポートが完了すると、後続の操作では新しいポリシー (新しいバージョン) が使用されます。  

-   それ以降の操作がない場合、Key Vault はまず有効期限通知を送信します。 

-   また、ユーザーはポリシーを編集できます。これはインポート時に機能しますが、インポート時に情報が指定されなかった場合は既定値が含まれています。 例: 発行者情報なし  

### <a name="formats-of-import-we-support"></a>サポート対象のインポートの形式
Azure Key Vault では、キー コンテナーへの証明書のインポートに対し、.pem および .pfx 証明書ファイルがサポートされています。
PEM ファイル形式の次の種類のインポートがサポートされています。 PEM でエンコードされた単一の証明書と、PKCS #8 でエンコードされた、以下を含む暗号化されていないキー

-----BEGIN CERTIFICATE----- -----END CERTIFICATE-----

-----BEGIN PRIVATE KEY----- -----END PRIVATE KEY-----

証明書をインポートするときは、キーがファイル自体に含まれていることを確認する必要があります。 秘密キーが異なる形式で別にある場合は、キーと証明書を組み合わせる必要があります。 証明機関によっては、証明書が異なる形式で提供されるため、証明書をインポートする前に、.pem 形式または .pfx 形式であることを確認します。 

### <a name="formats-of-merge-csr-we-support"></a>サポートされているマージ CSR の形式
AKV では、2 つの PEM ベースの形式がサポートされています。 PKCS #8 でエンコードされた単一の証明書、または base64 でエンコードされた P7B (CA によって署名された証明書のチェーン) のいずれかをマージできます 

-----BEGIN CERTIFICATE----- -----END CERTIFICATE-----


## <a name="creating-a-certificate-with-a-ca-not-partnered-with-key-vault"></a>Key Vault と提携していない CA での証明書の作成  
 この方法では、Key Vault の提携プロバイダー以外の CA を使用できます。つまり、組織は任意の CA を使用できます。  

![独自の証明機関の証明書の作成](../media/certificate-authority-1.png)  

 次の手順の説明は、前の図の緑の文字の手順に対応しています。  

  (1) - 上の図で、アプリケーションは証明書を作成しています。これは、キー コンテナーにキーを作成することで内部的に開始します。  

  (2) - Key Vault はアプリケーションに証明書署名要求 (CSR) を返します。  

  (3) - アプリケーションは、選択した CA に CSR を渡します。  

  (4) - 選択した CA は、X509 証明書で応答します。  

  (5) - アプリケーションは、CA からの X509 証明書の合併で新しい証明書の作成を完了します。
