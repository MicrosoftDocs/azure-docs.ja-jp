---
title: Azure Key Vault における証明書のアクセスの制御について
description: Azure Key Vault における証明書のアクセスの制御の概要
services: key-vault
author: sebansal
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 10/12/2020
ms.author: sebansal
ms.openlocfilehash: 1308debb34d724f93526b776f19e0cbf1914d945
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92128576"
---
# <a name="certificate-access-control"></a>証明書のアクセス制御

 証明書のアクセス制御は Key Vault によって管理され、それらの証明書を格納している Key Vault によって提供されます。 証明書のアクセス制御ポリシーは、同じキー コンテナー内のキーとシークレットに対するアクセス制御ポリシーとは別です。 ユーザーは、1 つまたは複数のコンテナーを作成して証明書を保持し、証明書のセグメント化と管理に適切なシナリオを維持することができます。  

 次のアクセス許可は、キー コンテナーのシークレット アクセス制御エントリでプリンシパルごとに使用でき、シークレット オブジェクトに対して許可される操作を厳密に反映します。  

- 証明書管理操作に対するアクセス許可
  - **get**:証明書の現在のバージョンまたは証明書の任意のバージョンを取得します
  - **list**:現在の証明書または証明書のバージョンの一覧表示します  
  - **update**:証明書を更新します
  - **create**:Key Vault 証明書を作成します
  - **import**:Key Vault 証明書に証明書マテリアルをインポートします
  - **delete**:証明書、そのポリシー、およびそのすべてのバージョンを削除します  
  - **recover**:削除された証明書を復旧します
  - **backup**:キー コンテナー内の証明書をバックアップします
  - **restore**:バックアップした証明書をキー コンテナーに復元します
  - **managecontacts**:Key Vault 証明書の連絡先を管理します  
  - **manageissuers**:Key Vault 証明書の機関/発行者を管理します
  - **getissuers**:証明書の機関/発行者を取得します
  - **listissuers**:証明書の機関/発行者を一覧表示します  
  - **setissuers**:Key Vault 証明書の機関/発行者を作成または更新します  
  - **deleteissuers**:Key Vault 証明書の機関/発行者を削除します  
 
- 特権操作に対するアクセス許可
  - **purge**:削除された証明書をパージ (完全に削除) します

詳しくは、[Key Vault REST API リファレンス内の証明書の操作](/rest/api/keyvault)の説明をご覧ください。 アクセス許可の設定については、「[コンテナー - アクセス ポリシーの更新](/rest/api/keyvault/vaults/updateaccesspolicy)」を参照してください。

## <a name="troubleshoot"></a>トラブルシューティング
アクセス ポリシーがないためにエラーが発生することがあります。 たとえば、"```Error type : Access denied or user is unauthorized to create certificate```" というものがあります。このエラーを解決するには、証明書を追加するか、アクセス許可を作成する必要があります。

## <a name="next-steps"></a>次のステップ

- [Key Vault について](../general/overview.md)
- [キー、シークレット、証明書について](../general/about-keys-secrets-certificates.md)
- [認証、要求、応答](../general/authentication-requests-and-responses.md)
- [Key Vault 開発者ガイド](../general/developers-guide.md)
