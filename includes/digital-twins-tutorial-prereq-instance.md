---
author: baanders
description: Azure Digital Twins チュートリアルのインクルード ファイル - インスタンスを設定するための前提条件
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 5c41f7516cecdb6bbc42a66d118a90986dd7de56
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87827337"
---
### <a name="prepare-an-azure-digital-twins-instance"></a>Azure Digital Twins インスタンスを準備する

このチュートリアルを完了するには、プログラミングの対象とする **Azure Digital Twins インスタンス**が必要です。 以前の作業で Azure Digital Twins インスタンスが既に設定されている場合は、そのインスタンスを使用できます。

* それ以外の場合は、"[*インスタンスと認証を設定する方法*](../articles/digital-twins/how-to-set-up-instance-scripted.md)" に関するページの説明に従って、**インスタンスと認証を設定**します。 説明には、各手順が正常に完了し、新しいインスタンスを使用する準備ができていることを確認するための手順も含まれています。

このチュートリアルでは、インスタンスを設定したときの以下の値が必要になります。 これらの値を再度収集する必要がある場合は、以下のリンクを使用して、[Azure portal](https://portal.azure.com) でそれらを見つけるためのセットアップの記事の対応するセクションを参照してください。
* Azure Digital Twins インスタンスの "**_名前_**" ([ポータルで見つける](../articles/digital-twins/how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))
* Azure Digital Twins インスタンスの "**_ホスト名_**" ([ポータルで見つける](../articles/digital-twins/how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))
* Azure AD アプリ登録の "**_アプリケーション (クライアント) ID_**" ([ポータルで見つける](../articles/digital-twins/how-to-set-up-instance-portal.md#collect-important-values))
* Azure AD アプリ登録の "**_ディレクトリ (テナント) ID_**" ([ポータルで見つける](../articles/digital-twins/how-to-set-up-instance-portal.md#collect-important-values))