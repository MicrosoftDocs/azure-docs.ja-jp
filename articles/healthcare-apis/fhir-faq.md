---
title: Azure の FHIR サービスについての FAQ - Azure API for FHIR
description: FHIR API におけるデータの保存場所やバージョン サポートなど、Azure API for FHIR についてよく寄せられる質問とその回答を示します。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/03/2020
ms.author: matjazl
ms.openlocfilehash: 252abcac6e9e39930593c1b110bf6d55ffdfc33f
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87843523"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>Azure API for FHIR についてよく寄せられる質問

## <a name="azure-api-for-fhir"></a>FHIR 用の Azure API

### <a name="what-is-fhir"></a>FHIR とは何でしょうか?
高速ヘルスケア相互運用性リソース (Fast Healthcare Interoperability Resources) は、略して FHIR ("ファイア" と発音) と呼ばれ、さまざまな医療システム間で医療データの交換を可能にする相互運用性標準です。 この標準は HL7 組織によって策定され、世界各国の医療機関で採用されています。 FHIR の最新バージョンは R4 (Release 4) です。 Azure API for FHIR では R4 がサポートされるほか、以前のバージョンの STU3 (Standard for Trial Use 3) もサポートされています。 FHIR の詳細については、[HL7.org](http://hl7.org/fhir/summary.html) のページを参照してください。

### <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>FHIR API のデータは Azure に格納されるのですか?

はい。データは Azure のマネージド データベースに格納されます。 Azure API for FHIR は、基になるデータ ストアへの直接アクセスを提供していません。

### <a name="what-identity-provider-do-you-support"></a>どの ID プロバイダーがサポートされますか?

現在当社は、ID プロバイダーとして Microsoft Azure Active Directory をサポートしています。

### <a name="what-fhir-version-do-you-support"></a>どのバージョンの FHIR がサポートされますか?

Azure API for FHIR (PaaS) と FHIR Server for Azure (オープンソース) では、どちらもバージョン 4.0.0 と 3.0.1 がサポートされます。

詳細については、「[サポートされている機能](fhir-features-supported.md)」を参照してください。 バージョン間の変更点については、[HL7 FHIR のバージョン履歴](https://hl7.org/fhir/R4/history.html)を参照してください。

### <a name="whats-the-difference-between-the-open-source-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>オープン ソースの Microsoft FHIR Server for Azure と Azure API for FHIR の違いは何ですか?

Azure API for FHIR は、オープンソースの Microsoft FHIR Server for Azure サーバーのホスト マネージド バージョンです。 マネージド サービスでは、メンテナンスや更新プログラムがすべて Microsoft によって提供されます。 

FHIR Server for Azure を実行している場合、基になるサービスに直接アクセスすることができます。 ただし、サーバーのメンテナンスや更新に加え、PHI データを格納する場合は必要なコンプライアンス作業もすべて自分で行う必要があります。

開発の観点から言うと、すべての機能はまず、オープンソースの Microsoft FHIR Server for Azure にデプロイされます。 オープンソースでの検証後、PaaS の Azure API for FHIR ソリューションにリリースされます。 オープンソース リリースから PaaS リリースまでの時間は、機能の複雑さやその他ロードマップ上の優先度によって異なります。 

### <a name="what-is-smart-on-fhir"></a>SMART on FHIR とは何でしょうか?

SMART (Substitutable Medical Applications and Reusable Technology) on FHIR は、FHIR サーバーやその他の医療 IT システム (電子カルテ、医療情報交換など) にパートナー アプリケーションを統合するための一連のオープン仕様です。 SMART on FHIR アプリケーションを作成することにより、多数の異なるシステムから確実にそのアプリケーションにアクセスして活用することができます。
認証と Azure API for FHIR。 SMART の詳細については、[SMART Health IT](https://smarthealthit.org/) に関するページを参照してください。

## <a name="azure-iot-connector-for-fhir-preview"></a>Azure IoT Connector for FHIR (プレビュー)

### <a name="what-is-iomt"></a>IoMT とは何ですか?
IoMT とは Internet of Medical Things の略で、ネットワーク上の他の医療 IT システムとの間で健康とウェルネスのデータをキャプチャして交換する IoT デバイスのカテゴリです。 IoMT デバイスの例としては、フィットネスおよび医療用ウェアラブル、監視センサー、アクティビティ トラッカー、ポイント オブ ケア キオスク、スマート ピルなどがあります。

### <a name="how-many-azure-iot-connector-for-fhir-preview-do-i-need"></a>Azure IoT Connector for FHIR (プレビュー) はいくつ必要ですか?
1 つの Azure IoT Connector for FHIR* を使用して、多数のさまざまな種類のデバイスからデータを取り込むことができます。 次の理由で異なるコネクタを使用することもできます。
- **スケール**:パブリック プレビューでは、Azure IoT Connector for FHIR のリソース容量が固定されており、1 秒あたり約 200 メッセージというスループットが想定されています。 より高いスループットが必要な場合は、さらに多くの Azure IoT Connector for FHIR を追加できます。
- **デバイスの種類**:デバイス管理上の理由により、所有する IoMT デバイスの種類ごとに個別の Azure IoT Connector for FHIR をセットアップすることができます。

### <a name="is-there-a-limit-on-number-of-azure-iot-connector-for-fhir-preview-during-public-preview"></a>パブリック プレビュー期間中の Azure IoT Connector for FHIR (プレビュー) の数に制限はありますか?
はい、Azure IoT Connector for FHIR 機能がパブリック プレビュー段階にある間は、1 つのサブスクリプションで作成できるのは 2 つだけです。 プレビュー期間中はその機能を無料でご利用いただけるため、予期しない費用が発生しないようにこの制限が設けられています。 Azure IoT Connector for FHIR の制限は、ご希望に応じて 5 つまで増やすことができます。

### <a name="what-azure-regions-azure-iot-connector-for-fhir-preview-feature-is-available-during-public-preview"></a>パブリック プレビュー期間中は、どの Azure リージョンで Azure IoT Connector for FHIR (プレビュー) 機能を利用できますか?
Azure IoT Connector for FHIR は、Azure API for FHIR が使用可能なすべての Azure リージョンで利用できます。

### <a name="can-i-configure-scaling-capacity-for-azure-iot-connector-for-fhir-preview"></a>Azure IoT Connector for FHIR (プレビュー) の容量のスケーリングを構成できますか?
パブリック プレビュー期間中は Azure IoT Connector for FHIR が無料であるため、容量のスケーリングは固定され、制限されます。 パブリック プレビューで使用可能な Azure IoT Connector for FHIR 構成では、1 秒あたり約 200 メッセージというスループットが想定されています。 何らかの形式のリソース容量の構成が一般提供 (GA) で利用可能になります。

### <a name="what-fhir-version-does-azure-iot-connector-for-fhir-preview-support"></a>Azure IoT Connector for FHIR (プレビュー) では、どのバージョンの FHIR がサポートされていますか?
Azure IoT Connector for FHIR では現在、FHIR のバージョン R4 のみがサポートされています。 したがって、この機能は Azure API for FHIR の R4 インスタンスでのみ表示され、現時点では Microsoft でバージョン STU3 をサポートする予定はありません。

### <a name="why-cant-i-install-azure-iot-connector-for-fhir-preview-when-private-link-is-enabled-on-azure-api-for-fhir"></a>Azure API for FHIR で Private Link が有効になっている場合、Azure IoT Connector for FHIR (プレビュー) をインストールできないのはなぜですか?
現時点では、Azure IoT Connector for FHIR で Private Link 機能をサポートしていません。 このため、Azure API for FHIR で Private Link が有効になっている場合は、Azure IoT Connector for FHIR をインストールすることも、その逆を行うこともできません。 この制限事項は、Azure IoT Connector for FHIR が一般提供 (GA) で利用可能になると解消される予定です。

### <a name="whats-the-difference-between-the-open-source-iomt-fhir-connector-for-azure-and-azure-iot-connector-for-fhir-preview-feature-of-azure-api-for-fhir-service"></a>オープンソースの Azure 用 IoMT FHIR コネクタと Azure API for FHIR サービスの Azure IoT Connector for FHIR (プレビュー) 機能の違いは何ですか?
Azure IoT Connector for FHIR は、オープンソースの Azure 用 IoMT FHIR コネクタのホストおよびマネージド バージョンです。 マネージド サービスでは、メンテナンスや更新プログラムがすべて Microsoft によって提供されます。

Azure 用の IoMT FHIR コネクタを実行している場合は、基になるリソースに直接アクセスすることができます。 ただし、サーバーのメンテナンスや更新に加え、PHI データを格納する場合は必要なコンプライアンス作業もすべて自分で行う必要があります。

開発の観点から言うと、すべての機能はまず、オープンソースの Azure 用 IoMT FHIR コネクタにデプロイされます。 オープンソースでの検証後、Azure API for FHIR サービスの PaaS Azure IoT Connector for FHIR 機能にリリースされます。 オープンソース リリースから PaaS リリースまでの時間は、機能の複雑さやその他ロードマップ上の優先度によって異なります。

## <a name="next-steps"></a>次のステップ

この記事では、Azure API for FHIR についてよく寄せられる質問のいくつかを確認しました。 FHIR Server for Azure のサポートされている機能について確認してください。
 
>[!div class="nextstepaction"]
>[サポートされる FHIR 機能](fhir-features-supported.md)

*Azure portal では、Azure IoT Connector for FHIR は IoT Connector (プレビュー) と呼ばれています。

FHIR は HL7 の登録商標であり、HL7 の許可を得て使用しています。