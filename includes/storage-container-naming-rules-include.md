---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 3e5afeffa6f47ff79e31f189abc6d5e26d8f2586
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050508"
---
Azure Storage のどの BLOB もコンテナーに格納する必要があります。 コンテナーは、BLOB 名の一部を形成しています。 たとえば、次の BLOB の URI のサンプルでは、 `mycontainer` がコンテナーの名前です。

```http
https://storagesample.blob.core.windows.net/mycontainer/blob1.txt
https://storagesample.blob.core.windows.net/mycontainer/photos/myphoto.jpg
```

コンテナー名は有効な DNS 名で、次の名前規則に準拠している必要があります。

1. コンテナー名は英文字または数字で始まり、英文字、数字、ダッシュ (-) 文字のみを含めることができます。
2. すべてのダッシュ (-) 文字は、その直前または直後に文字または数字が使用されている必要があります。連続するダッシュ文字は、コンテナー名では使用できません。
3. コンテナー名の文字はすべて小文字である必要があります。
4. コンテナー名の長さは、3 ～ 63 文字にする必要があります。

> [!IMPORTANT]
> コンテナーの名前は、常に小文字にする必要があります。 コンテナー名に大文字が含まれている場合や、コンテナーの名前付け規則の他の違反がある場合、400 エラー (無効な要求) が発生することがあります。 
> 
> 

