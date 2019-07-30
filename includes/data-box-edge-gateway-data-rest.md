---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 8a6ceee0f7db4b7b368dce1a28c8edc0b2b11180
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181817"
---
保存データの場合:

- ローカル データの保護には BitLocker XTS-AES 256 ビット暗号化が使用されます。
- 共有に格納されたデータへのアクセスは制限されます。

    - 共有データにアクセスする SMB クライアントには、その共有に関連付けられたユーザー資格情報が必要です。 これらの資格情報は、その共有が作成されるときに定義されます。
    - 共有が作成されるときは、その共有にアクセスする NFS クライアントの IP アドレスを追加する必要があります。