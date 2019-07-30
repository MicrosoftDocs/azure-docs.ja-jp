---
title: Azure Security Center による脅威検出 | Microsoft Docs
description: " Microsoft Cloud App Security を Azure Security Center と統合することにより、脅威と悪意のあるものを検出します。 "
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: rkarlin
ms.openlocfilehash: af7896ec4afaeefda7261542bf593a89a7bb9ae8
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551801"
---
# <a name="ueba-for-azure-resources-and-users"></a>Azure リソースおよびユーザーに対する UEBA 

Azure Security Center では、Microsoft Cloud App Security と提携して、ユーザーとエンティティの行動分析 (UEBA) に基づいて Azure のリソースとユーザー (Azure アクティビティ) に関するアラートを生成します。 これらのアラートでは、ユーザーの行動の異常が検出されます。ユーザーとエンティティの行動分析および機械学習 (ML) に基づいているため、サブスクリプションのアクティビティ全体で高度な脅威検出をすぐに実行できます。 アラートは自動的に有効になるため、新しい異常検出では、サブスクリプションに関連付けられているユーザーとリソース全体のさまざまな行動の異常を対象に即時検出が実行され、即座に結果が返されます。 また、これらのアラートでは Microsoft Cloud App Security の検出エンジンに既に存在している追加データが活用されます。これは、調査プロセスのスピードアップと脅威の継続的な防止に役立ちます。 

> [!NOTE]
> Azure Security Center では、仮想マシンや Azure Active Directory テナントなどのお客様のリソースから収集した、またはお客様のリソースに関連付けられている、セキュリティ関連のお客様データのコピーを (a) そのリソースと同じ geo 内に保存する場合があります。ただし、Microsoft がまだ Azure Security Center をデプロイしていない geo は除きます。それらの geo では、そうしたデータのコピーは米国に保存されます。(b) Azure Security Center により、そうしたデータの処理に別の Microsoft オンライン サービスが使用される場合、その別のオンライン サービスの地域に関するルールに従ってそうしたデータを保存する場合があります。
>

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>前提条件

- アクティブ化済みの有効な [Microsoft Cloud App Security ライセンス](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)
- [Security Center Standard レベル](https://azure.microsoft.com/pricing/details/security-center/)
 
## <a name="threat-detection-alerts"></a>脅威検出アラート

Security Center では、次のような Cloud App Security による異常検出アラートがサポートされています。

**あり得ない移動**
-  この検出は、(1 つまたは複数のセッションにおける) 2 つのユーザー アクティビティが地理的に離れている場所で、最初の場所から 2 回目の場所にユーザーが移動するのに要する時間より短い時間内に発生したことを示します。これは、別のユーザーが同じ資格情報を使用していることを示唆します。 この検出では、組織内の他のユーザーが普通に使用する VPN や場所など、あり得ない移動状況の原因になる明らかな "誤検知" を無視する機械学習アルゴリズムが活用されます。 この検出には 7 日間の初期学習期間があり、その間に新しいユーザーのアクティビティ パターンの学習が行われます。

**頻度の低い国からのアクティビティ**
- この検出では、新しい場所や頻度の低い場所を判断する際に、過去にアクティビティが発生した場所が考慮されます。 異常検出エンジンにより、組織内のユーザーが以前に使用したことのある場所に関する情報が格納されます。 組織内のユーザーが最近訪れていない場所や一度も訪れたことのない場所でアクティビティが発生すると、アラートがトリガーされます。 

**匿名の IP アドレスからのアクティビティ**
- この検出は、匿名プロキシ IP アドレスとして識別されている IP アドレスからユーザーがアクティブだったことを示します。 このようなプロキシは、自分のデバイスの IP アドレスを隠したいユーザーによって使用され、悪意のある目的で使用される場合があります。 この検出では、組織内の他のユーザーがよく使用する IP アドレスのタグ付けが間違っているなどの "誤検知" を削減する機械学習アルゴリズムが活用されます。
 
  ![脅威検出アラート](./media/security-center-ueba-mcas/security-center-mcas-alert.png)

## <a name="disabling-threat-detection-alerts"></a>脅威検出アラートの無効化

これらのアラートは既定で有効になっていますが、無効にすることもできます。

1. [Security Center] ブレードで、 **[Pricing & settings]\(価格と設定\)** を選択し、適切なサブスクリプションを選択します。
2. **[脅威検出]** をクリックします。
3. **[Enable integrations]\(統合の有効化\)** で、 **[Allow Microsoft Cloud App Security]\(Microsoft Cloud App Security が自分のデータにアクセスすることを許可する\)** チェック ボックスをオフにし、 **[保存]** をクリックします。

   ![脅威検出アラート](./media/security-center-ueba-mcas/security-center-mcas-optout.png)

> [!NOTE]
> 7 日間の初期学習期間がありますが、その間にすべての異常検出アラートが発生するわけではありません。 その後、各セッションは、過去 1 か月間に検出されたアクティビティ、ユーザーがアクティブだったとき、IP アドレス、デバイスなどに加え、これらのアクティビティのリスク スコアと比較されます。 これらの検出は、環境のプロファイルを作成し、組織のアクティビティを基に学習したベースラインに従ってアラートをトリガーする機械学習異常検出エンジンの一部です。 また、これらの検出では、誤検知を削減するためにユーザーとログオン パターンのプロファイルを作成するように設計された機械学習アルゴリズムが活用されます。
>
  
## <a name="next-steps"></a>次の手順
この記事では、Azure Security Center による脅威検出について説明しました。 セキュリティ センターの詳細については、次を参照してください。

* 「[Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)」-- このサービスの使用に関してよく寄せられる質問が記載されています。
* 「[Azure セキュリティ センターでのセキュリティ ヘルスの監視](security-center-monitoring.md)」-- Azure リソースの正常性を監視する方法について説明しています。



<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png
