---
title: Azure API for FHIR の追加設定
description: Azure API for FHIR に設定できる追加設定の概要
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: matjazl
ms.author: cavoeg
author: CaitlinV39
ms.date: 11/22/2019
ms.openlocfilehash: 23c4fb6be4e30e78faa1ce411037f828d6518f50
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019111"
---
# <a name="additional-settings-for-azure-api-for-fhir"></a>Azure API for FHIR の追加設定

この攻略ガイドでは、Azure API for FHIR で設定できる追加設定を確認します。 さらに詳しい情報について説明する追加のページがあります。

## <a name="configure-database-settings"></a>データベース設定を構成する

Azure API for FHIR では、データベースを使用してデータを格納します。 基になるデータベースのパフォーマンスは、サービスのプロビジョニング中に選択された要求ユニット (RU) の数、またはサービスがプロビジョニングされた後のデータベース設定によって異なります。

データベースで常に十分なシステム リソースを確実に使用できるようにするには、スループットをプロビジョニングする必要があります。 アプリケーションに必要な RU の数は、実行する操作によって異なります。 操作の範囲は、単純な読み取りと書き込みから、より複雑なクエリにまで及びます。

既定の設定を変更する方法の詳細については、[データベース設定の構成](configure-database.md)に関する記事を参照してください。

## <a name="access-control"></a>アクセス制御

Azure API for FHIR では、承認されたユーザーのみが FHIR API にアクセスできます。 承認されたユーザーは、2 つの異なるメカニズムを使用して構成できます。 アクセス制御を構成するための主な方法として推奨される方法は、[Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/index.yml) を使用する方法です。これには、 **[アクセス制御 (IAM)]** ブレードからアクセスできます。 Azure RBAC は、サブスクリプションに関連付けられている Azure Active Directory テナントを使用してデータ プレーンのアクセスをセキュリティで保護する場合にのみ機能します。 別のテナントを使用する場合、Azure API for FHIR には、ローカルの FHIR データ プレーンのアクセス制御メカニズムが用意されています。 ローカルの RBAC メカニズムを使用する場合、構成オプションはそれほど豊富ではありません。 詳細については、次のいずれかのオプションを選択します。

* [Azure RBAC for FHIR データ プレーン](configure-azure-rbac.md)。 これは、お使いのサブスクリプションに関連付けられている Azure Active Directory テナントを使用する場合に推奨されるオプションです。
* [ローカルの FHIR データ プレーンのアクセス制御](configure-local-rbac.md)。 このオプションは、データ プレーンのアクセス制御に外部の Azure Active Directory テナントを使用する必要がある場合にのみ使用してください。 

## <a name="enable-diagnostic-logging"></a>診断ログの有効化
セットアップの一環として診断ログを有効にして、サービスを監視し、コンプライアンスのための正確なレポートを作成できるようにすることができます。 診断ログを設定する方法の詳細については、診断ログの設定方法に関する[攻略ガイド](enable-diagnostic-logging.md)と、いくつかのサンプル クエリを参照してください。 

## <a name="use-custom-headers-to-add-data-to-audit-logs"></a>カスタム ヘッダーを使用してデータを監査ログに追加する
Azure API for FHIR では、呼び出し元のシステムから取得した追加情報をログに含めることができます。 この情報を含めるには、カスタム ヘッダーを使用できます。

カスタム ヘッダーを使用して、いくつかの種類の情報をキャプチャできます。 次に例を示します。

* ID または認証情報
* 呼び出し元
* 元の組織
* クライアント システムの詳細 (電子医療記録、患者ポータル)

このデータを監査ログに追加する場合は、[カスタム HTTP ヘッダーを使用した監査ログへのデータの追加](use-custom-headers.md)に関する攻略ガイドを参照してください。

## <a name="next-steps"></a>次のステップ

この攻略ガイドでは、Azure API for FHIR の追加設定を設定しました。

次は、一連のチュートリアルを確認して、FHIR データを読み取る Web アプリケーションを作成します。

>[!div class="nextstepaction"]
>[JavaScript アプリケーションをデプロイする](tutorial-web-app-fhir-server.md)