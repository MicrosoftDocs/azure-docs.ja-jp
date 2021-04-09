---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 11/22/2020
ms.topic: include
ms.openlocfilehash: 2112cde42ee00b78a82962ee46f53110068977c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98187230"
---
## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般提供 (GA)|
|価格:|**コンテナー レジストリ用の Azure Defender** の課金については、[価格に関するページ](../articles/security-center/security-center-pricing.md)をご覧ください。|
|サポートされているレジストリとイメージ:|シェル アクセスによってパブリック インターネットからアクセス可能な ACR レジストリ内の Linux イメージ|
|サポートされていないレジストリとイメージ:|Windows イメージ<br>"プライベート" レジストリ<br>Azure Private Link などのファイアウォール、サービス エンドポイント、またはプライベート エンドポイントによってアクセスが制限されたレジストリ<br>[Docker スクラッチ](https://hub.docker.com/_/scratch/)のようなスーパー ミニマリスト イメージ、またはアプリケーションとそのランタイム依存関係のみが含まれ、パッケージ マネージャー、シェル、または OS は含まれない "ディストリビューションレス" イメージ。|
|必要なロールとアクセス許可:|**セキュリティ閲覧者** および [Azure Container Registry のロールとアクセス許可](../articles/container-registry/container-registry-roles.md)|
|クラウド:|:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: 商用クラウド<br>:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: US Gov および China Gov - 現在、プッシュ時のスキャン機能のみがサポートされています。 詳細については、「[イメージはどのような場合にスキャンされますか](../articles/security-center/defender-for-container-registries-introduction.md#when-are-images-scanned)」を参照|
|||