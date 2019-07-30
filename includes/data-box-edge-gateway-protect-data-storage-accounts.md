---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 653c175a559f5c0b7dc551b396e91276332df20a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181819"
---
デバイスは、Azure 内のデータの宛先として使用されるストレージ アカウントに関連付けられています。 ストレージ アカウントへのアクセスは、各ストレージ アカウントに関連付けられたサブスクリプションと 2 つの 512 ビット ストレージ アクセス キーによって制御されます。

Data Box Edge デバイスがストレージ アカウントにアクセスするときに、いずれかのキーが認証に使用されます。 もう一方のキーは予備で保持されているため、それらのキーを定期的にローテーションできます。

セキュリティ上の理由から、多くのデータ センターでキーのローテーションが義務化されています。 キーのローテーションに関しては、以下のベスト プラクティスに従うようお勧めします。

- ストレージ アカウント キーは、ストレージ アカウントの root パスワードに似ています。 アカウント キーは慎重に保護してください。 このパスワードを他のユーザーに配布したり、ハード コードしたり、他のユーザーからアクセスできるプレーンテキストで保存したりしないでください。
- 侵害される可能性があると考えられる場合は、Azure Portal 経由で[アカウント キーを再生成](../articles/storage/common/storage-account-manage.md#regenerate-access-keys)します。
- Azure 管理者は、Azure Portal の [ストレージ] セクションを使用してストレージ アカウントに直接アクセスすることにより、プライマリまたはセカンダリ キーを定期的に変更または再生成する必要があります。