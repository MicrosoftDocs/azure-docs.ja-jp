---
title: Azure API for FHIR とは - Azure API for FHIR
description: Azure API for FHIR を使用すると、FHIR API シリーズを介して迅速にデータを交換できます。 マネージド クラウド サービスを使用して、保護医療情報 (PHI) を取り込み、管理、保持します。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 11/13/2020
ms.author: matjazl
ms.openlocfilehash: 479e8f870692cc16345d0ca19b8eb065caa28fa8
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018553"
---
# <a name="what-is-azure-api-for-fhirreg"></a>Azure API for FHIR とは&reg;

Azure API for FHIR を使用すると、FHIR® (高速ヘルスケア相互運用性リソース) API シリーズを介して迅速にデータを交換できます。クラウド上でマネージドのサービスとしてのプラットフォーム (PaaS) オファリングによってサポートされます。 これにより、医療データを取り扱うすべての人がクラウド上で保護医療情報 ([PHI](https://www.hhs.gov/answers/hipaa/what-is-phi/index.html)) を簡単に取り込み、管理、保持できるようになります。 

- マネージド FHIR サービス (クラウド上で数分でプロビジョニング) 
- データ アクセスおよび FHIR® 形式での保管を行うための Azure 内のエンタープライズレベルで FHIR® ベースのエンドポイント
- ハイ パフォーマンス、低遅延
- 準拠しているクラウド環境で保護された正常性情報 (PHI) の管理を保護する
- モバイルおよび Web 実装用の SMART on FHIR
- ロールベースのアクセス制御 (RBAC) によるお手元のデータの大規模な制御
- 各データ ストア内でのアクセス、作成、変更、および読み取りを追跡する監査ログ

Azure API for FHIR を使用すると、FHIR サービスを数分で作成してデプロイし、クラウドのエラスティック スケールを活用できます。  必要なスループットとストレージに対してのみ課金されます。 Azure API for FHIR を動作させる Azure サービスは、管理しているデータセットのサイズに関係なく、迅速なパフォーマンスを実現するように設計されています。

FHIR API と準拠しているデータ ストアを使用すると、FHIR API シリーズを利用する任意のシステムに安全に接続して操作することができます。  この PaaS オファリングの運用、保守、更新、コンプライアンス要件については Microsoft が引き受けるため、お客様の運用および開発用のリソースを解放できます。 

次のビデオでは、Azure API for FHIR の概要を紹介しています。

>[!VIDEO https://www.youtube.com/embed/5vS7Iq9vpXE]

## <a name="leveraging-the-power-of-your-data-with-fhir"></a>FHIR を使用してお手元のデータの力を活用する

医療業界では、新しい標準である [FHIR&reg;](https://hl7.org/fhir) (高速ヘルスケア相互運用性リソース) への医療データの変換を急速に進めています。 FHIR を使用すると、セマンティクスとデータ交換が標準化された堅牢で拡張可能なデータ モデルを実現して、FHIR を使用しているすべてのシステムを連携させることができます。  お手元のデータを FHIR に変換することで、電子医療記録システムや研究データベースなどの既存のデータ ソースをすばやく接続できます。 FHIR を使用すると、モバイルや Web 開発の最新の実装で迅速なデータ交換を実現することもできます。 最も重要なこととして、FHIR により、データ インジェストを簡素化し、分析と機械学習ツールを使用した開発を加速させることができます。  

### <a name="securely-manage-health-data-in-the-cloud"></a>クラウド上で医療データを安全に管理する

Azure API for FHIR を使用すると、HL7 FHIR 仕様に基づいた一貫性のある RESTful FHIR API シリーズを介してデータを交換できます。 また、これは、Azure のマネージド PaaS オファリングによってサポートされており、ネイティブ FHIR 形式で保護医療情報 (PHI) データを管理および格納するためのスケーラブルで安全な環境も提供します。  

### <a name="free-up-your-resources-to-innovate"></a>イノベーションのためにお客様のリソースを解放する

リソースを投資して独自の FHIR サービスを構築して実行することもできますが、Azure API for FHIR を使用すると、運用、保守、更新、コンプライアンス要件のワークロードについては Microsoft が引き受けるため、お客様の運用および開発用のリソースを解放できます。

### <a name="enable-interoperability-with-fhir"></a>FHIR との相互運用性を実現する

Azure API for FHIR を使用すると、読み取り、書き込み、検索などの機能に FHIR API シリーズを活用する任意のシステムに接続できます。  これは、電子医療記録、臨床医および患者のダッシュボード、リモート監視プログラムからの臨床データや、FHIR API シリーズを利用する実際のシステムの外部にあるデータベースを統合して、標準化し、機械学習を適用するための強力なツールとして使用することができます。

### <a name="control-data-access-at-scale"></a>データ アクセスを大規模に制御する

お客様のデータを制御するのはお客様です。 ロールベースのアクセス制御 (RBAC) により、実際のデータの保管およびアクセス方法を管理できます。  セキュリティが強化され、管理ワークロードが軽減されるため、実際の環境に合わせて作成したロールの定義に基づいて、作成したデータセットにアクセスできるユーザーを決定します。  

### <a name="audit-logs-and-tracking"></a>監査ログと追跡 

組み込みの監査ログを使用して、実際のデータのフローをすばやく追跡できます。 各データ ストア内でのアクセス、作成、変更、および読み取りが追跡されます。

### <a name="secure-your-data"></a>データのセキュリティ保護

お客様の PHI は、比類のないセキュリティ インテリジェンスによって保護されます。  実際のデータは API インスタンスごとに一意のデータベースに分離され、複数リージョンのフェールオーバーによって保護されます。 Azure API for FHIR には、実際のデータに対する多層防御と高度な脅威保護が実装されています。  

## <a name="applications-for-a-fhir-service"></a>FHIR サービス用のアプリケーション

FHIR サーバーは、医療データの相互運用性を実現するための重要なツールです。  Azure API for FHIR は、作成、デプロイ、使用開始を迅速に行うことができる API とサービスとして設計されています。  医療業界で FHIR 標準が広まるにつれて、ユース ケースは増え続けますが、Azure API for FHIR が役に立つ初期のお客様のアプリケーションには以下のものがあります。 

- **スタートアップ企業および IoT とアプリ開発:** 患者またはプロバイダー中心のアプリ (モバイルまたは Web) を開発しているお客様は、フル マネージド バックエンド サービスとして Azure API for FHIR を利用できます。 Azure API for FHIR は、医療データ用に設計され、セキュリティで保護されたクラウド環境内でのデータの管理およびデータの交換、SMART on FHIR 実装ガイドラインの活用、すべてのプロバイダー システムによる自社テクノロジの利用の実現 (たとえば、ほとんどの EHR では FHIR 読み取り API を実現しています) を行うことができる貴重なリソースを提供します。   
- **医療業界のエコシステム:** さまざまな臨床環境において EHR は主要な "信頼できる情報源" として存在していますが、プロバイダーが複数のデータベースを保持していて、それらが相互に接続されていないことや、データが異なる形式で格納されていることは珍しくありません。  Azure API for FHIR をそうしたシステムの上位に位置するサービスとして利用すると、データを FHIR 形式で標準化できます。  そうすると、一貫性のあるデータ形式によって複数のシステム間でデータを交換することができます。 

- **研究:** FHIR 標準によって、共通の FHIR データ モデルを中心にデータが正規化され、機械学習とデータ共有のワークロードが削減されるため、医療業界の研究者は FHIR 標準全般と Azure API for FHIR が便利なことに気が付くでしょう。
Azure API for FHIR を介してデータを交換すると、監査ログとアクセス制御が提供され、データ フローおよびどのデータ型にだれがアクセスできるかを制御することができます。 

## <a name="fhir-from-microsoft"></a>Microsoft の FHIR

Microsoft の FHIR 機能は、次の 2 つの構成で利用できます。

* Azure API for FHIR – Azure の PaaS オファリング。Azure portal 内で簡単にプロビジョニングでき、Microsoft によって管理されます。
* FHIR Server for Azure – ご使用の Azure サブスクリプションにデプロイできるオープンソース プロジェクト。GitHub (https://github.com/Microsoft/fhir-server ) から入手できます。

FHIR API シリーズを使わない、FHIR サーバーの拡張やカスタマイズまたは基になるサービス (データベースなど) へのアクセスを必要とするユース ケースの場合は、開発者はオープンソースの FHIR Server for Azure を選択する必要があります。   永続化されたデータに FHIR API を介してのみアクセスする必要がある実稼働対応のターンキー FHIR API とバックエンド サービスの実装の場合は、開発者は Azure API for FHIR を選択する必要があります

## <a name="azure-iot-connector-for-fhir-preview"></a>Azure IoT Connector for FHIR (プレビュー)

Azure IoT Connector for FHIR&#174; (高速ヘルスケア相互運用性リソース)* は、Azure API for FHIR のオプション機能であり、Internet of Medical Things (IoMT) デバイスからデータを取り込むことができます。 Internet of Medical Things とは、ネットワーク上の他の医療 IT システムとの間で健康とウェルネスのデータをキャプチャして交換する IoT デバイスのカテゴリです。 IoMT デバイスの例としては、フィットネスおよび医療用ウェアラブル、監視センサー、アクティビティ トラッカー、ポイント オブ ケア キオスク、スマート ピルなどがあります。 Azure IoT Connector for FHIR の機能を使用すると、IoMT データをスケーラブルかつ安全で、規制に準拠した方法で Azure API for FHIR に取り込むためのサービスをすばやく設定できます。

Azure IoT Connector for FHIR を使用すると、IoMT デバイスによって送信される JSON ベースのあらゆるメッセージを受け入れることができます。 このデータは、まず適切な FHIR ベースの [Observation](https://www.hl7.org/fhir/observation.html) リソースに変換され、その後 Azure API for FHIR に保存されます。 データ変換ロジックは、メッセージ スキーマと FHIR の要件に基づいて構成するマッピング テンプレートのペアによって定義されます。 デバイス データは、Azure IoT Connector for FHIR に直接プッシュすることも、他の Azure IoT ソリューション ([Azure IoT Hub](../../iot-hub/index.yml) および [Azure IoT Central](../../iot-central/index.yml)) と連携してシームレスに使用することもできます。 Azure IoT Connector for FHIR によって、セキュリティで保護されたデータ パイプラインが提供される一方で、Azure IoT ソリューションが物理デバイスのプロビジョニングとメンテナンスに対応できるようになります。

### <a name="applications-of-azure-iot-connector-for-fhir-preview"></a>Azure IoT Connector for FHIR (プレビュー) のアプリケーション

IoMT デバイスの使用は医療分野で急速に拡大しています。Azure IoT Connector for FHIR は、複数のデバイス データを Azure API for FHIR に取り込むときのギャップを埋め、セキュリティとコンプライアンスを実現できるように設計されています。 IoMT データを FHIR サーバーに取り込むことで、データの総合的な分析情報が得られ、革新的な臨床ワークフローを実現できます。 Azure IoT Connector for FHIR の一般的なシナリオは次のとおりです。
- **リモート患者モニタリング/遠隔医療:** リモート患者モニタリングでは、従来の医療環境の外部で患者の健康データを収集する機能が提供されます。 医療機関は Azure IoT Connector for FHIR を使用して、リモート デバイスで生成された健康データを Azure API for FHIR に取り込むことができます。 このデータを使用して、患者の健康状態を細かく注意して追跡し、患者が治療計画を遵守しているかを監視し、個人的なケアを提供できます。
- **研究およびライフ サイエンス:** 臨床試験では試験データを取得するために、バイオ センサー、ウェアラブル、モバイル アプリなどの IoMT デバイスが迅速に導入されています。 これらの試験では Azure IoT Connector for FHIR を利用して、セキュリティで保護された効率的かつ効果的な方法で、デバイス データを Azure API for FHIR に送信できます。 Azure API for FHIR に試験データが届くと、試験データのリアルタイム分析を実行できます。
- **高度な分析:** IoMT デバイスからは、多種多量のデータを高速で提供できるため、機械学習モデルのトレーニング データとテスト データの提供元として最適です。 Azure IoT Connector for FHIR は本来、さまざまなデータ頻度、柔軟なデータ スキーマ、低待機時間でのクラウド スケーリングなどに対処するように構築されています。 このような Azure IoT Connector for FHIR の特性は、高度な分析のニーズに合わせてデバイス データをキャプチャするための選択肢として最適です。
- **スマート ホスピタル/クリニック:** 現在、スマート ホスピタルおよびスマート クリニックでは、相互に接続されたデジタル資産のインフラストラクチャが構築されつつあります。 Azure IoT Connector for FHIR を使用して、これらの接続されたコンポーネントからのデータをキャプチャして統合できます。 このようなデータ セットから得られる実用的な分析情報により、患者へのより良い治療が可能になり、運用効率を向上できます。

## <a name="next-steps"></a>次の手順

Azure API for FHIR の使用を開始するには、5 分間のクイックスタートに従って、Azure API for FHIR をデプロイします。

>[!div class="nextstepaction"]
>[Azure API for FHIR をデプロイする](fhir-paas-portal-quickstart.md)

Azure IoT Connector for FHIR の機能を試すには、Azure portal を使用した Azure IoT Connector for FHIR のデプロイに関するクイックスタートをご覧ください。

>[!div class="nextstepaction"]
>[Azure IoT Connector for FHIR をデプロイする](iot-fhir-portal-quickstart.md)

*Azure portal では、Azure IoT Connector for FHIR は IoT Connector (プレビュー) と呼ばれています。 FHIR は HL7 の登録商標であり、HL7 の許可を得て使用しています。 
