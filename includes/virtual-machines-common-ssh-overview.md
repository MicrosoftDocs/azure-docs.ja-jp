---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 12/21/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 3f9b86dd3716a25ab95265cf46b616144f57163b
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71168656"
---
## <a name="overview-of-ssh-and-keys"></a>SSH とキーの概要

[SSH](https://www.ssh.com/ssh/) は、セキュリティで保護されていない接続においてセキュリティで保護されたサインインを可能にする、暗号化された接続プロトコルです。 SSH は、Azure でホストされる Linux VM の既定の接続プロトコルです。 SSH 自体は暗号化された接続を提供しますが、SSH 接続でパスワードを使用すると、VM はブルートフォース攻撃やパスワードの推測に対して脆弱になります。 SSH を使用して VM に接続するためのより安全で推奨される方法は、公開キーと秘密キーのペア (*SSH キー*とも呼ばれます) を使用する方法です。 

* *公開キー*は、Linux VM か、公開キー暗号化で使用する他のサービスに配置します。

* *秘密キー*は、ローカル システム上に残ります。 このキーは安全に保管してください。 このキーは共有しないようにしてください。

SSH クライアントを使用してお使いの Linux VM (公開キーがある) に接続するときに、リモート VM ではクライアントをテストして、秘密キーが処理されていることを確かめます。 クライアントに秘密キーがある場合、VM へのアクセス権が付与されます。 

組織のセキュリティ ポリシーに応じて、単一の公開キーと秘密キーのペアを再利用して複数の Azure VM とサービスにアクセスできます。 アクセスする VM またはサービスごとに異なるキーのペアを用意する必要はありません。 

公開キーはだれとでも共有できますが、秘密キーを所有するのは自分 (またはローカル セキュリティ インフラストラクチャ) にする必要があります。