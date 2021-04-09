---
title: Azure API for FHIR の関連 GitHub プロジェクト
description: Azure API for FHIR のすべてのオープンソース (GitHub) リポジトリを一覧表示します。
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/01/2021
ms.author: ginle
ms.openlocfilehash: 9977765183bd567377592631d65f3e548bef4b34
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "105097640"
---
# <a name="related-github-projects"></a>関連する GitHub プロジェクト

GitHub には、さまざまな用途のためにサービスをデプロイするためのソースコードと手順を提供する多数のオープンソースプロジェクトが用意されています。 Microsoft の機能や製品について学習し、実験を行うには、常に GitHub リポジトリにアクセスしてください。 

## <a name="fhir-server"></a>FHIR サーバー
* [microsoft/fhir-server](https://github.com/microsoft/fhir-server/): Fhir 用 Azure API の基礎となるオープンソースの FHIR サーバー
* 最新のリリースを確認するには、[リリースノート](https://github.com/microsoft/fhir-server/releases)を参照してください。
* [microsoft/fhir-サーバー-](https://github.com/microsoft/fhir-server-samples)サンプル: サンプル環境

## <a name="data-conversion--anonymization"></a>データ変換 & 匿名化

#### <a name="fhir-converter"></a>FHIR コンバーター
* [microsoft/FHIR-Converter](https://github.com/microsoft/FHIR-Converter): 従来のデータ形式を FHIR に変換する変換ユーティリティ
* Azure API for FHIR、および $convert データ操作の形式での Azure 用の FHIR server と統合されています。
* OSS の継続的な改善、および FHIR サーバーとの継続的な統合
 
#### <a name="fhir-converter---vs-code-extension"></a>FHIR コンバーター-VS Code 拡張機能
* [microsoft/FHIR-匿名化](https://github.com/microsoft/FHIR-Tools-for-Anonymization): データを支援するためのツールのセット (fhir 形式) 匿名化
* Azure API for FHIR、および FHIR server for Azure と統合された "エクスポートの無効化" の形式で統合

#### <a name="fhir-tools-for-anonymization"></a>匿名化用の FHIR ツール
* [microsoft/vscode-azurehealthcareapis](https://github.com/microsoft/vscode-azurehealthcareapis-tools): Azure の医療 api を操作するためのツールのコレクションを含む VS Code の拡張機能
* Visual Studio Marketplace にリリース
* FHIR コンバーターで使用する液体テンプレートを作成するために使用されます。

## <a name="iot-connector"></a>IoT コネクタ

#### <a name="integration-with-iot-hub-and-iot-central"></a>IoT Hub と IoT Central との統合
* [microsoft/iomt-fhir](https://github.com/microsoft/iomt-fhir): 正規化されたデータのデータ正規化および fhir 変換による、IoT Hub または IoT Central との統合
* 正規化: デバイスデータ情報は、さらに処理するために共通の形式に抽出されます。
* FHIR 変換: 正規化されたデータとグループ化されたデータは FHIR にマップされます。 監視は、構成されたテンプレートに従って作成または更新され、デバイスと患者にリンクされます。
* [メッセージ交換マップの作成に役立つツール](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper): デバイス入力データを正規化するためのマッピング構成を視覚化し、FHIR リソースに変換します。 開発者は、このツールを使用して、マッピング、デバイスマッピング、および FHIR マッピングを編集してテストし、Azure portal の IoT コネクタにアップロードするためにエクスポートすることができます。

#### <a name="healthkit-and-fhir-integration"></a>HealthKit と FHIR の統合
* [microsoft/healthkit](https://github.com/microsoft/healthkit-on-fhir): Fhir サーバーへの Apple healthkit データのエクスポートを自動化する Swift ライブラリです。

 