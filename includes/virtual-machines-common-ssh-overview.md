---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/09/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 612e7dc2f3d1ef6aa5d3598999aa214a2f377e1f
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513190"
---
## <a name="overview-of-ssh-and-keys"></a>SSH とキーの概要

[SSH](https://www.ssh.com/ssh/) は、セキュリティで保護されていない接続において安全なサインインを可能にする、暗号化された接続プロトコルです。 SSH は、Azure でホストされる Linux VM の既定の接続プロトコルです。 SSH は暗号化された接続を提供しますが、SSH 接続でパスワードを使用すると、VM はブルートフォース攻撃に対して脆弱になります。 SSH を介して VM に接続する場合は、公開キーと秘密キーのペアを使用することをお勧めします。これは "*SSH キー*" とも呼ばれています。 

- *公開キー*は、Linux VM 上に配置されます。

- *秘密キー*は、ローカル システム上に残ります。 このキーは安全に保管してください。 このキーは共有しないようにしてください。

SSH クライアントを使用してお使いの Linux VM (公開キーがある) に接続するときに、リモート VM によってクライアントがテストされ、正しい秘密キーの存在が確認されます。 クライアントに秘密キーがある場合、VM へのアクセス権が付与されます。 

組織のセキュリティ ポリシーに応じて、単一の公開キーと秘密キーのペアを再利用して複数の Azure VM とサービスにアクセスできます。 アクセスする VM またはサービスごとに異なるキーのペアを用意する必要はありません。 

ご自分の公開キーはだれとでも共有できますが、ご自分の秘密キーへのアクセス権を所有するのは自分 (またはローカル セキュリティ インフラストラクチャ) のみとする必要があります。