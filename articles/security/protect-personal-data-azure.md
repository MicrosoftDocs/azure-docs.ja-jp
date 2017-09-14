---
title: "Microsoft Azure での個人データの保護 | Microsoft Docs"
description: "Azure を使用して個人データを保護するための最初の記事"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: barclayn
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 4dbdb2dc11bdc515fb3856dd45203868122c7726
ms.contentlocale: ja-jp
ms.lasthandoff: 08/30/2017

---
# <a name="protect-personal-data-in-microsoft-azure"></a>Microsoft Azure を使用して個人データを保護する

この記事では、Azure のセキュリティ テクノロジおよびサービスを使用して個人データを保護するための記事を紹介します。 これは、多くの企業にとって、業界のコンプライアンスおよびガバナンス イニシアチブのために重要な要件です。 このシナリオには、問題の説明と会社の目標が含まれています。

## <a name="scenario-and-problem-statement"></a>シナリオと問題の説明

米国に本社を置く大手クルーズ会社が、地中海、アドリア海、バルト海だけでなくイギリス諸島での旅程を提供できるように、事業を拡張しています。 この会社は、これらの取り組みを支援するために、イタリア、ドイツ、デンマーク、英国に拠点を置くいくつかの小規模なクルーズ会社を買収しました。

その会社では、クラウドに会社のデータを保存するために Microsoft Azure を使用しています。 このデータには、次のような顧客や従業員の情報が含まれています。

- 住所
- 電話番号
- 納税者番号
- クレジット カード情報

この会社では、顧客や従業員のデータのプライバシーを保護する必要があり、関係部署が必要な時にいつでもデータにアクセスできるようにしておく必要があります。 (給与を管理する部署や、予約を受け付ける部署など)

## <a name="company-goals"></a>会社の目標 

- 個人データを含むデータ ソースは暗号化されてクラウド ストレージに格納されます。

- ある場所から他の場所に個人データを転送するときは、転送中のデータは暗号化されます。 これは、仮想ネットワークまたはインターネットを仲介する、企業のデータ センターと Azure クラウドの間でデータを移動するときに実行されます。

- 個人データの機密性と整合性は、厳格な ID 管理とアクセス制御テクノロジによって不正なアクセスから保護されます。

- 脆弱性と脅威を監視することによって、個人データは、データ漏洩による露出から保護されます。

- 個人データを格納または転送する Azure サービスのセキュリティ状態を査定して、個人データをより強力に保護するための改善箇所が特定されます。

## <a name="data-protection-guidance"></a>データ保護に関するガイダンス

次の記事には、上記の個人のデータ保護の目標の実現に役立つ技術ガイダンスが含まれています。

- [Azure の暗号化テクノロジ](protect-personal-data-at-rest.md)

- [Azure の暗号化テクノロジ](protect-personal-data-in-transit-encryption.md)

- [Azure の ID 管理とアクセス テクノロジ](protect-personal-data-identity-access-controls.md)

- [Azure のネットワーク セキュリティ テクノロジ](protect-personal-data-network-security.md)

- [Azure Security Center](protect-personal-data-azure-security-center.md)



## <a name="next-steps"></a>次のステップ

- [Azure セキュリティに関する情報サイト](https://aka.ms/AzureSecInfo)

- [Microsoft セキュリティ センター](https://www.microsoft.com/TrustCenter/default.aspx)

- [Azure Security Center](https://azure.microsoft.com/services/security-center/)

- [Azure セキュリティ チームのブログ](https://www.azuresecurityorg)

- [Azure.com ブログ - セキュリティ](https://azure.microsoft.com/blog/topics/security/)

