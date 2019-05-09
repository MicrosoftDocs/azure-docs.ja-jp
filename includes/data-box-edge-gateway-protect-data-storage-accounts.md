---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 23587e617c62cfe4aa24256330c5f7673dd1c674
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684449"
---
デバイスは、Azure のデータの宛先として使用するストレージ アカウントと関連付けられます。 ストレージ アカウントへのアクセスは、各ストレージ アカウントに関連付けられたサブスクリプションと 2 つの 512 ビット ストレージ アクセス キーによって制御されます。

Data Box Edge デバイスがストレージ アカウントにアクセスするときに、いずれかのキーが認証に使用されます。 もう 1 つは、キーの定期的なローテーションを見越して、あらかじめ作成されるものです。

セキュリティ上の理由から、多くのデータ センターでキーのローテーションが義務化されています。 キーのローテーションに関しては、以下のベスト プラクティスに従うようお勧めします。

- ストレージ アカウント キーは、ストレージ アカウントの root パスワードに似ています。 アカウント キーは慎重に保護してください。 このパスワードを他のユーザーに配布したり、ハードコーディングしたり、他のユーザーがアクセスできるプレーン テキストで保存したりしないでください。
- アカウント キーが侵害されたと思われる場合は、Azure Portal を使用して[キーを再生成](../articles/storage/common/storage-account-manage.md#regenerate-access-keys)してください。
- Azure 管理者が Azure ポータルの [ストレージ] セクションを使用してストレージ アカウントに直接アクセスして、プライマリ キーまたはセカンダリ キーを定期的に変更または再生成する。