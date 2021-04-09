---
title: Azure Blockchain Service のセキュリティ
description: Azure Blockchain Service のデータ アクセスとセキュリティの概念です
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 989d9f2afad30517a85185878d694c0b6640e987
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "80879600"
---
# <a name="azure-blockchain-service-security"></a>Azure Blockchain Service のセキュリティ

Azure Blockchain Service では、Azure の複数の機能を使用して、データのセキュリティと可用性が維持されます。 データは、分離、暗号化、および認証を使用してセキュリティ保護されます。

## <a name="isolation"></a>分離:

Azure Blockchain Service のリソースは、プライベート仮想ネットワーク内に分離されます。 各トランザクション ノードと検証ノードは、仮想マシン (VM) です。 ある仮想ネットワーク内の VM は、別の仮想ネットワーク内の VM とは直接通信できません。 分離により、通信は仮想ネットワーク内でプライベートの状態を維持されます。 Azure 仮想ネットワークの分離について詳しくは、「[Azure Public Cloud での分離](../../security/fundamentals/isolation-choices.md#networking-isolation)」をご覧ください。

![VNET の図](./media/data-security/vnet.png)

## <a name="encryption"></a>暗号化

ユーザー データは Azure Storage に格納されます。 ユーザー データは、セキュリティと機密保持のため、移動時および保存時に暗号化されます。 詳細については、次を参照してください。[Azure Storage のセキュリティ ガイド](../../storage/blobs/security-recommendations.md)。

## <a name="authentication"></a>認証

トランザクションは、RPC エンドポイント経由でブロックチェーン ノードに送信できます。 クライアントは、ユーザーの認証を処理し、TLS 経由でデータを暗号化するリバース プロキシ サーバーを使用して、トランザクション ノードを通信します。

![認証のダイアグラム](./media/data-security/authentication.png)

RPC アクセスの認証には 3 つのモードがあります。

### <a name="basic-authentication"></a>[基本認証]

基本認証では、ユーザー名とパスワードが含まれる HTTP 認証ヘッダーが使われます。 ユーザー名は、ブロックチェーン ノードの名前です。 パスワードは、メンバーまたはノードのプロビジョニング中に設定されます。 パスワードは、Azure portal または CLI を使用して変更できます。

### <a name="access-keys"></a>アクセス キー

アクセス キーでは、エンドポイントの URL に含まれる、ランダムに生成された文字列が使用されます。 2 つのアクセス キーにより、キーをローテーションできます。 Azure portal および CLI で、キーを再生成できます。

### <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory (Azure AD) では要求に基づく認証メカニズムが使用され、ユーザーは Azure AD ユーザー資格情報を使用して Azure AD によって認証されます。 Azure AD ではクラウドベースの ID 管理が提供され、お客様は企業全体で 1 つの ID を使用して、クラウド上のアプリケーションにアクセスできます。 Azure Blockchain Service は Azure AD と統合されており、ID フェデレーション、シングル サインオン、多要素認証を利用できます。 ブロックチェーンのメンバーとノードのアクセス用に、組織のユーザー、グループ、およびアプリケーションのロールを割り当てることができます。

Azure AD のクライアント プロキシは、[GitHub](https://github.com/Microsoft/azure-blockchain-connector/releases) で入手できます。 クライアント プロキシでは、ユーザーは Azure AD サインイン ページに誘導され、認証が成功するとベアラー トークンが取得されます。 その後、ユーザーは、Geth や Truffle などの Ethereum クライアント アプリケーションを、クライアント プロキシのエンドポイントに 接続します。 最後に、トランザクションが送信されると、クライアント プロキシは http ヘッダーにベアラー トークンを挿入し、リバース プロキシは OAuth プロトコルを使用してトークンを検証します。

## <a name="keys-and-ethereum-accounts"></a>キーと Ethereum アカウント

Azure Blockchain Service のメンバーをプロビジョニングするときに、Ethereum アカウントおよび公開キーと秘密キーのペアが生成されます。 秘密キーは、ブロックチェーンにトランザクションを送信するために使用されます。 Ethereum アカウントは、公開キーのハッシュの最後の 20 バイトです。 Ethereum アカウントは、ウォレットとも呼ばれます。

秘密キーと公開キーのペアは、JSON 形式のキー ファイルとして格納されます。 秘密キーは、ブロックチェーン台帳サービスの作成時に入力されたパスワードを使用して暗号化されます。

秘密キーは、トランザクションにデジタル署名するために使用されます。 プライベート ブロックチェーンでは、秘密キーによって署名されたスマート コントラクトが、署名者の ID を表します。 受信側で署名の有効性を確認するには、署名者の公開キーと、署名から計算されたアドレスを比較できます。

Quorum ノードを一意に識別するには、Constellation キーが使用されます。 Constellation キーは、ノードのプロビジョニング時に生成され、Quorum のプライベート トランザクションの privateFor パラメーターで指定されます。

## <a name="next-steps"></a>次のステップ

[Azure Blockchain Service 用に Azure Active Directory アクセスを構成する方法](configure-aad.md)に関するページを参照してください。
