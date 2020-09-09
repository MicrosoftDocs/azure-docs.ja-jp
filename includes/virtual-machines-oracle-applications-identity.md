---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 2b28cc742bb981e0db535d39cb0c7fbf027470ea
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "83673766"
---
### <a name="identity-tier"></a>ID 層 

Microsoft と Oracle のパートナーシップにより、Azure、OCI、Oracle アプリケーションの垣根を越えて統一された ID の設定が可能となっています。 JD Edwards EnterpriseOne または PeopleSoft アプリケーション スイートで、Azure AD と Oracle IDCS との間にシングル サインオンを設定するには、Oracle HTTP Server (OHS) のインスタンスが必要となります。

OHS は、アプリケーション層へのリバース プロキシとして機能します。つまり、エンド アプリケーションに対するすべての要求が OHS を経由します。 Oracle Access Manager WebGate は、エンド アプリケーションに向かうすべての要求をインターセプトする OHS Web サーバー プラグインです。 アクセスしようとしているリソースが保護されている (認証済みのセッションが必要である) 場合、WebGate がユーザーのブラウザーを介して Identity Cloud Service との OIDC 認証フローを開始します。 OpenID Connect WebGate でサポートされるフローの詳細については、[Oracle Access Manager のドキュメント](https://docs.oracle.com/cd/E52734_01/oam/AIAAG/GUID-1E927D1B-FB83-425B-8768-85DB441821A4.htm#AIAAG7327)を参照してください。

この構成では、既に Azure AD にログインしているユーザーが Oracle Identity Cloud Service を介し、再ログインなしで、JD Edwards EnterpriseOne または PeopleSoft アプリケーションに移動することができます。 このソリューションをデプロイすることによって、お客様はシングル サインオンの利点を得ることができます (資格情報が 1 セットで済む、サインオン エクスペリエンスとセキュリティが向上する、ヘルプデスク コストが軽減されるなど)。

Azure AD で JD Edwards EnterpriseOne または PeopleSoft のシングル サインオンを設定する方法について詳しくは、関連する [Oracle のホワイトペーパー](https://cloud.oracle.com/iaas/whitepapers/deploy_peoplesoft_jdedwards_across_oci_azure.pdf)を参照してください。