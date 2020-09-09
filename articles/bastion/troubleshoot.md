---
title: Azure Bastion のトラブルシューティング | Microsoft Docs
description: この記事では、Azure Bastion のトラブルシューティング方法について説明します。
services: bastion
author: charwen
ms.service: bastion
ms.topic: troubleshooting
ms.date: 10/16/2019
ms.author: charwen
ms.openlocfilehash: f3c142491363f30513877ae4368f291430aa3675
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85831932"
---
# <a name="troubleshoot-azure-bastion"></a>Azure Bastion のトラブルシューティング

この記事では、Azure Bastion のトラブルシューティング方法について説明します。

## <a name="unable-to-create-an-nsg-on-azurebastionsubnet"></a><a name="nsg"></a>AzureBastionSubnet 上で NSG を作成できない

**質問:** Azure Bastion サブネット上で NSG を作成しようとすると、次のエラーを受け取ります。 *"Network security group <NSG name> does not have necessary rules for Azure Bastion Subnet AzureBastionSubnet" (ネットワーク セキュリティ グループには Azure Bastion Subnet AzureBastionSubnet に必要な規則がありません)* 。

**A:** NSG を作成して *AzureBastionSubnet* に適用する場合は、NSG に次の規則を追加済みであることを確認してください。 これらの規則を追加しないと、NSG の作成/更新は失敗します。

1. コントロール プレーンの接続 - GatewayManager からの 443 での受信
2. 診断ログなど - 443 での AzureCloud への送信 (このサービス タグ内のリージョン タグはまだサポートされていません)。
3. ターゲット VM - 3389 および 22 での VirtualNetwork への送信

NSG 規則の例については、[クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion-nsg)を参照してください。
詳細については、[Azure Bastion の NSG のガイダンス](bastion-nsg.md)に関する記事を参照してください。

## <a name="unable-to-use-my-ssh-key-with-azure-bastion"></a><a name="sshkey"></a>Azure Bastion に SSH キーを使用できません

**質問:** SSH キー ファイルを参照しようとすると、次のエラーを受け取ります。 *"SSH 秘密キーは、"-----BEGIN RSA PRIVATE KEY-----" で始まり、"-----END RSA PRIVATE KEY-----" で終わる必要があります"* 。

**A:** 現在のところ、Azure Bastion は RSA SSH キーのみをサポートしています。 SSH 用の RSA 秘密キーであるキー ファイルの参照には、必ずターゲット VM 上にプロビジョニングされた公開キーを使用してください。 

たとえば、次のコマンドを使用して新しい RSA SSH キーを作成できます。

**ssh-keygen -t rsa -b 4096 -C "email@domain.com"**

出力:

```
ashishj@dreamcatcher:~$ ssh-keygen -t rsa -b 4096 -C "email@domain.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ashishj/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/ashishj/.ssh/id_rsa.
Your public key has been saved in /home/ashishj/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:c+SBciKXnwceaNQ8Ms8C4h46BsNosYx+9d+AUxdazuE email@domain.com
The key's randomart image is:
+---[RSA 4096]----+
|      .o         |
| .. ..oo+. +     |
|=.o...B==.O o    |
|==o  =.*oO E     |
|++ .. ..S =      |
|oo..   + =       |
|...     o o      |
|         . .     |
|                 |
+----[SHA256]-----+
```

## <a name="unable-to-sign-in-to-my-windows-domain-joined-virtual-machine"></a><a name="domain"></a>Windows ドメインに参加している仮想マシンにサインインできません

**質問:** ドメインに参加している Windows 仮想マシンに接続できません。

**A:** Azure Bastion は、ドメイン参加 VM へのサインインには、ユーザー名とパスワードに基づくドメイン サインインのみをサポートしています。 Azure portal でドメインの資格情報を指定する場合は、サインインに *domain\username* 形式ではなく UPN (username@domain) 形式を使用します。 これは、ドメイン参加またはハイブリッド参加 (ドメイン参加と Azure AD 参加の両方) 仮想マシンでサポートされています。 Azure AD 参加のみの仮想マシンではサポートされていません。

## <a name="file-transfer-issues"></a><a name="filetransfer"></a>ファイル転送に関する問題

**質問:** Azure Bastion ではファイル転送はサポートされていますか?

**A:** 現時点では、ファイル転送はサポートされていません。 サポートの追加に取り組んでいます。

## <a name="black-screen-in-the-azure-portal"></a><a name="blackscreen"></a>Azure portal の黒い画面

**質問:** Azure Bastion を使用して接続しようとすると、Azure portal に黒い画面が表示されます。

**A:** これは、ネットワーク接続の問題が Web ブラウザーと Azure Bastion 間 (クライアントのインターネット ファイアウォールによって WebSockets トラフィックがブロックされる場合など) または Azure Bastion とターゲット VM 間に存在する場合に発生します。 ほとんどの場合、AzureBastionSubnet、または仮想ネットワーク内の RDP/SSH トラフィックをブロックしているターゲット VM サブネットに適用された NSG が関わっています。 クライアントのインターネット ファイアウォールで WebSocket トラフィックを許可し、ターゲット VM サブネットで NSG を確認します。

## <a name="next-steps"></a>次のステップ

詳細については、[Bastion の FAQ](bastion-faq.md) に関する記事を参照してください。
