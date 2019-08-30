---
title: Azure API Management で Key Vault 証明書を使用してカスタム ドメインを追加できない | Microsoft Docs
description: Key Vault 証明書を使用して Azure API Management にカスタム ドメインを追加できない問題をトラブルシューティングする方法について説明します。
services: api-management
documentationcenter: ''
author: genlin
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/19/2019
ms.author: tehnoonr
ms.openlocfilehash: 30dfd864c6c237024c72325da813691ac5043bd0
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072087"
---
# <a name="failed-to-update-api-management-service-hostnames"></a>API Management サービスのホスト名を更新できなかった

この記事では、Azure API Management サービスに対してカスタム ドメインを追加するときに発生する可能性がある "API Management サービスのホスト名を更新できませんでした" エラーについて説明します。 この記事では、問題の解決に役立つトラブルシューティング手順について説明します。

## <a name="symptoms"></a>現象

Azure Key Vault の証明書を使って API Management サービスに対するカスタム ドメインを追加しようとすると、次のエラー メッセージが表示されます。

- API Management サービスのホスト名を更新できませんでした。 Request to resource 'https://vaultname.vault.azure.net/secrets/secretname/?api-version=7.0 ' failed with StatusCode: Forbidden for RequestId: . Exception message: Operation returned an invalid status code 'Forbidden'. (リソース 'https://vaultname.vault.azure.net/secrets/secretname/?api-version=7.0' に対する要求が、次の要求 ID に対してステータス コード Forbidden で失敗しました。例外メッセージ: 操作によって無効な状態コード 'Forbidden' が返されました。)

## <a name="cause"></a>原因

カスタム ドメインに対して使用しようとしているキー コンテナーにアクセスするためのアクセス許可が、API Management サービスにありません。

## <a name="solution"></a>解決策

この問題を解決するには、次の手順に従ってください。

1. [Azure portal](Https://portal.azure.com) に移動し、API Management インスタンスを選択して、 **[マネージド ID]** を選択します。 **[Azure Active Directory に登録する]** オプションが **[はい]** に設定されていることを確認します。 
    ![Registering with Azure Active Director](./media/api-management-troubleshoot-cannot-add-custom-domain/register-with-aad.png) (Azure Active Directory に登録する)
1. Azure portal で、 **[キー コンテナー]** サービスを開き、カスタム ドメインに使用するキー コンテナーを選択します。
1. **[アクセス ポリシー]** を選択し、API Management サービス インスタンスの名前と一致するサービス プリンシパルがあるかどうかを確認します。 存在する場合は、そのサービス プリンシパルを選択し、 **[シークレットのアクセス許可]** の下に **[取得]** アクセス許可が表示されていることを確認します。  
    ![Adding access policy for service principal](./media/api-management-troubleshoot-cannot-add-custom-domain/access-policy.png) (サービス プリンシパルのアクセス ポリシーの追加)
1. API Management サービスが一覧にない場合は、 **[アクセス ポリシーの追加]** を選択し、次のアクセス ポリシーを作成します。
    - **テンプレートからの構成**: なし
    - **プリンシパルの選択**: API Management サービスの名前を検索し、一覧から選択します
    - **キーのアクセス許可**: なし
    - **シークレットのアクセス許可**: 取得
    - **証明書のアクセス許可**: なし
1. **[OK]** を選択してアクセス ポリシーを作成します。
1. **[保存]** を選択して変更を保存します。

問題が解決しているかどうかを確認します。 これを行うには、Key Vault 証明書を使用して、API Management サービスでカスタム ドメインを作成してみてください。

## <a name="next-steps"></a>次の手順
API Management サービスについてさらに確認します。

- API Management についてのその他の [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=api-management) をご覧ください。
* バックエンド サービスを保護するその他の方法については、「[相互証明書認証](api-management-howto-mutual-certificates.md)」を参照してください。

* [API Management サービス インスタンスの作成](get-started-create-service-instance.md)。
* [Azure API Management での最初の API の管理](import-and-publish.md)。