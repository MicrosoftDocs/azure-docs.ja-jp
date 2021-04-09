---
title: Azure Blockchain Service のトランザクション ノードを構成する
description: Azure Blockchain Service のトランザクション ノードを構成する方法です
ms.date: 11/20/2019
ms.topic: how-to
ms.reviewer: janders
ms.openlocfilehash: 0b659fa9f15cd41e9e55de526747d470046cafa4
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "101091260"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>Azure Blockchain Service のトランザクション ノードを構成する

トランザクション ノードは、パブリック エンドポイントを介して Azure Blockchain Service にブロックチェーン トランザクションを送信するために使用されます。 既定のトランザクション ノードは、ブロックチェーンに登録された Ethereum アカウントの秘密キーを格納しているので、削除することはできません。

既定のトランザクション ノードの詳細を表示するには:

1. [Azure portal](https://portal.azure.com) にサインインします。
1. Azure Blockchain Service メンバーに移動します。 **[Transaction nodes]\(トランザクション ノード\)** を選択します。

    ![既定のトランザクション ノードを選択する](./media/configure-transaction-nodes/nodes.png)

    概要の詳細には、パブリック エンドポイント アドレスと公開キーが含まれます。

## <a name="create-transaction-node"></a>トランザクション ノードを作成する

最大 9 個のトランザクション ノードをブロックチェーン メンバーに追加して、全部で 10 個のトランザクション ノードを使用できます。 トランザクション ノードを追加することにより、スケーラビリティと負荷の分散を高めることができます。 たとえば、異なるクライアント アプリケーションごとにトランザクション ノード エンドポイントを作成できます。

トランザクション ノードを追加するには:

1. Azure portal で自分の Azure Blockchain Service メンバーに移動して、 **[Transaction nodes]\(トランザクション ノード\) > [追加]** を選択します。
1. 新しいトランザクション ノードの設定を完了します。

    ![トランザクション ノードを追加する](./media/configure-transaction-nodes/add-node.png)

    | 設定 | 説明 |
    |---------|-------------|
    | 名前 | トランザクション ノードの名前。 名前は、トランザクション ノードのエンドポイントの DNS アドレスを作成するために使われます。 たとえば、「 `newnode-myblockchainmember.blockchain.azure.com` 」のように入力します。 作成後にノード名を変更することはできません。 |
    | Password | 強力なパスワードを設定します。 そのパスワードを使って、基本認証でトランザクション ノードのエンドポイントにアクセスします。

1. **［作成］** を選択します

    新しいトランザクション ノードのプロビジョニングには、約 10 分かかります。 追加のトランザクション ノードにはコストがかかります。 コストについて詳しくは、[Azure の価格](https://aka.ms/ABSPricing)に関するページをご覧ください。

## <a name="endpoints"></a>エンドポイント

トランザクション ノードは、一意の DNS 名とパブリック エンドポイントがあります。

トランザクション ノードのエンドポイントの詳細を表示するには:

1. Azure portal で Azure Blockchain Service メンバーのトランザクション ノードの 1 つに移動して、 **[概要]** を選択します。

    ![ブロックチェーン メンバーに対するトランザクション ノードの概要のスクリーンショット。](./media/configure-transaction-nodes/endpoints.png)

トランザクション ノード エンドポイントはセキュリティで保護されており、認証が必要です。 トランザクション エンドポイントに接続するには、Azure AD Authentication、HTTPS 基本認証、および HTTPS または TLS 経由の WebSocket を介したアクセス キーを使用できます。

### <a name="azure-active-directory-access-control"></a>Azure Active Directory のアクセス制御

Azure Blockchain Service のトランザクション ノード エンドポイントでは、Azure Active Directory (Azure AD) 認証がサポートされています。 Azure AD のユーザー、グループ、およびサービス プリンシパルに、エンドポイントへのアクセスを許可できます。

エンドポイントへの Azure AD アクセス制御を許可するには:

1. Azure portal で自分の Azure Blockchain Service メンバーに移動して、 **[Transaction nodes]\(トランザクション ノード\) > [アクセス制御 (IAM)] > [追加] > [ロールの割り当ての追加]** の順に選択します。
1. ユーザー、グループ、またはサービス プリンシパル (アプリケーション ロール) に対して、新しいロールの割り当てを作成します。

    ![IAM ロールを追加する](./media/configure-transaction-nodes/add-role.png)

    | 設定 | アクション |
    |---------|-------------|
    | Role | **[所有者]** 、 **[共同作成者]** 、または **[閲覧者]** を選択します。
    | アクセスの割り当て先 | **[Azure AD のユーザー、グループ、サービス プリンシパル]** を選択します。
    | Select | 追加するユーザー、グループ、またはサービス プリンシパルを検索します。

1. **[保存]** を選択して、ロールの割り当てを追加します。

Azure AD アクセス制御の詳細については、「[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)」を参照してください

Azure AD 認証を使って接続する方法について詳しくは、[AAD 認証を使用したノードへの接続](configure-aad.md)に関する記事をご覧ください。

### <a name="basic-authentication"></a>[基本認証]

HTTPS 基本認証では、ユーザー名とパスワードの資格情報を、エンドポイントへの要求の HTTPS ヘッダーで渡します。

トランザクション ノードの基本認証エンドポイントの詳細は、Azure portal で表示できます。 Azure Blockchain Service メンバーのトランザクション ノードの 1 つに移動し、設定で **[基本認証]** を選択します。

![[基本認証]](./media/configure-transaction-nodes/basic.png)

ユーザー名はノードの名前で、変更できません。

URL を使用するには、\<password\> をノードのプロビジョニング時に設定したパスワードに置き換えます。 パスワードは、 **[パスワードのリセット]** を選択して更新できます。

### <a name="access-keys"></a>アクセス キー

アクセス キー認証では、アクセス キーはエンドポイントの URL に含まれます。 トランザクション ノードをプロビジョニングすると、2 つのアクセス キーが生成されます。 どちらのアクセス キーも認証に使用できます。 キーが 2 つあるのは、キーを変更およびローテーションできるようにするためです。

トランザクション ノードのアクセス キーの詳細を表示し、アクセス キーが含まれるエンドポイント アドレスをコピーできます。 Azure Blockchain Service メンバーのトランザクション ノードの 1 つに移動し、設定で **[アクセス キー]** を選択します。

### <a name="firewall-rules"></a>ファイアウォール規則

ファイアウォール規則を使用すると、トランザクション ノードに対して認証を試みることができる IP アドレスを制限できます。  トランザクション ノードにファイアウォール規則を構成しないと、すべてのパーティーがアクセスできません。  

トランザクション ノードのファイアウォール規則を表示するには、Azure Blockchain Service メンバーのトランザクション ノードの 1 つに移動し、設定の **[ファイアウォール規則]** を選択します。

ルールの名前、開始 IP アドレス、終了 IP アドレスを **[ファイアウォール規則]** グリッドに入力することで、ファイアウォール規則を追加できます。

![ファイアウォール規則](./media/configure-transaction-nodes/firewall-rules.png)

有効にするには:

* **単一の IP アドレス:** 開始 IP アドレスと終了 IP アドレスに同じ IP アドレスを構成します。
* **IP アドレスの範囲:** 範囲の開始 IP アドレスと終了 IP アドレスを構成します。 たとえば、10.221.34.0 で開始して 10.221.34.255 で終了する範囲を指定すると、10.221.34.xxx サブネット全体が有効になります。
* **すべての IP アドレスを許可する:** 開始 IP アドレスを 0.0.0.0、終了 IP アドレスを 255.255.255.255 と構成します。

## <a name="connection-strings"></a>Connection strings

基本認証またはアクセス キーの使用に対して、トランザクション ノード用の接続文字列の構文が提供されています。 HTTPS および WebSocket 経由のアクセス キーを含む接続文字列が提供されます。

トランザクション ノードの接続文字列を表示して、エンドポイント アドレスをコピーできます。 Azure Blockchain Service メンバーのトランザクション ノードの 1 つに移動し、設定で **[接続文字列]** を選択します。

![Connection strings](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>サンプル コード

Web3、Nethereum、Web3js、および Truffle を使用して、トランザクション ノードにすばやく接続できるように、サンプル コードが提供されています。

トランザクション ノードのサンプル接続コードを表示し、それをコピーして、人気のある開発者ツールで使用できます。 Azure Blockchain Service メンバーのトランザクション ノードの 1 つに移動し、設定で **[サンプル コード]** を選択します。

[Web3]、[Nethereum]、[Truffle]、または [Web3j] タブを選択し、使用するコード サンプルを表示します。

![サンプル コード](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure CLI を使用してトランザクション ノードを構成する](manage-cli.md)
