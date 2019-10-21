---
title: Azure Security Center でのクラウド ネイティブ コンピューティングの脅威検出 | Microsoft Docs
description: このトピックでは、Azure Security Center で使用可能なクラウド ネイティブ コンピューティングのアラートについて説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5aa5efcf-9f6f-4aa1-9f72-d651c6a7c9cd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: memildin
ms.openlocfilehash: c3fcbadf93ff72f7d2a1dca3b25ace81c9d4f1ae
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202631"
---
# <a name="threat-detection-for-cloud-native-compute-in-azure-security-center"></a>Azure Security Center でのクラウド ネイティブ コンピューティングの脅威検出

クラウド プロバイダーとして、Azure Security Center では、独自の可視性を使用して、内部ログが分析され、複数のターゲットに対する攻撃手法が識別されます。 このトピックでは、次の Azure サービスで使用可能なアラートについて説明します。

* [Azure App Service](#app-services)
* [Azure Container Service](#azure-containers) 

## Azure App Service<a name="app-services"></a>

Security Center では、クラウドのスケールを使用して、App Service で実行されるアプリケーションをターゲットとした攻撃が識別されます。 Web アプリケーションは現代のネットワークに普通に存在しているため、攻撃者はこれらを見つけ出し、脆弱性を悪用します。 Azure で実行されているアプリケーションに対する要求は、特定の環境にルーティングされる前に、複数のゲートウェイを通過し、そこで検査され、ログに記録されます。 その後、このデータは、悪用や攻撃者を特定し、後で使用される新しいパターンを学習するために使用されます。

Security Center では、Azure がクラウド プロバイダーとして備える可視性を使用して、App Service の内部ログが分析され、複数のターゲットに対する攻撃手法が識別されます。 たとえば、広範囲にわたるスキャンや分散型の攻撃などの手法です。 この種の攻撃は通常、IP の小さなサブセットから発生し、複数のホスト上の類似のエンドポイントをクロールするパターンを示します。 攻撃では脆弱なページやプラグインが検索され、単一のホストの観点からは攻撃を特定できません。

Security Center では、基になるサンド ボックスおよび VM にアクセスすることもできます。 メモリ フォレンジクスと組み合わせることで、インフラストラクチャは、流行している新しい攻撃から、お客様のマシンの侵害まで、状況を通知できます。 そのため、Security Center では、悪用されてからかなり後に Web アプリケーションに対する攻撃を検出する可能性があります。

> [!div class="mx-tableFixed"]

|アラート:|説明|
|---|---|
|**Suspicious WordPress theme invocation detected (不審な WordPress テーマの呼び出しが検出されました)**|App Service のアクティビティ ログは、App Service リソースに対するコード インジェクション アクティビティの可能性を示しています。<br/> この不審なアクティビティは、WordPress テーマを操作してサーバー側でのコード実行をサポートし、操作されたテーマ ファイルを呼び出すための直接の Web 要求を行うアクティビティに似ています。 この種のアクティビティは、WordPress での攻撃活動の一部である可能性があります。|
|**Connection to web page from anomalous IP address detected (異常な IP アドレスからの Web ページへの接続が検出されました)**|App Service のアクティビティ ログは、これまで接続されたことのないソース アドレスから機密性の高い Web ページへの接続を示しています。 これは、何者かが Web アプリの管理ページにブルート フォース攻撃を試みていることを示している可能性があります。 また、正当なユーザーが新しい IP アドレスを使用した結果である可能性もあります。|
|**An IP that connected to your Azure App Service FTP Interface was found in Threat Intelligence (脅威インテリジェンスで、Azure App Service の FTP インターフェイスに接続した IP が見つかりました)**|App Service の FTP ログ分析で、脅威インテリジェンス フィードで見つかったソース アドレスからの接続が検出されました。 この接続中に、ユーザーが、一覧表示されているページにアクセスしました。|
|**Web fingerprinting detected (Web フィンガー プリントが検出されました)**|App Service のアクティビティ ログは、App Service リソースに対する Web フィンガー プリント アクティビティの可能性を示しています。 <br/>この不審なアクティビティは、Blind Elephant と呼ばれるツールに関連しています。 このツールは、Web サーバーのフィンガープリントを取得し、インストールされているアプリケーションとそのバージョンを検出しようとします。 攻撃者はこのツールを使用して Web アプリケーションを調べ、脆弱性を見つけることがよくあります。|
|**Suspicious access to possibly vulnerable web page detected (脆弱な可能性がある Web ページへの不審なアクセスが検出されました)**|App Service のアクティビティ ログは、機密性が高いと思われる Web ページがアクセスされたことを示しています。 <br/>この不審なアクティビティは、Web スキャナーのアクセス パターンに似たアクセス パターンを持つソース アドレスから発生しています。 この種のアクティビティは、多くの場合、攻撃者がネットワークをスキャンして、機密性が高いか、脆弱な Web ページへのアクセス権を取得しようとする試みと関連しています。|
|**PHP file in upload folder (アップロード フォルダー内の PHP ファイル)**|App Service のアクティビティ ログは、アップロード フォルダー内にある不審な PHP ページに何かがアクセスしたことを示しています。 <br/>この種のフォルダーには通常、PHP ファイルは含まれません。 この種のファイルが存在する場合は、任意のファイル アップロードの脆弱性を利用した悪用を示している可能性があります。|
|**An attempt to run Linux commands on a Windows App Service (Windows App Service での Linux コマンドの実行試行)**|App Service のプロセスの分析で、Windows App Service での Linux コマンドの実行試行が検出されました。 このアクションは、Web アプリケーションにより実行されていました。 この動作は、一般的な Web アプリケーションの脆弱性を悪用する活動でよく見られます。|
|**Suspicious PHP execution detected (疑わしい PHP の実行が検出されました)**|マシン ログは、不審な PHP プロセスが実行されていることを示しています。 このアクションには、PHP プロセスを使用してコマンドラインからオペレーティング システム コマンドまたは PHP コードを実行する試みが含まれていました。 この動作は、正当である可能性もありますが、Web アプリケーションでは、この動作は Web シェルで Web サイトを感染させようとするなどの悪意のあるアクティビティを示している可能性があります。|
|**Process execution from temporary folder (一時フォルダーからのプロセスの実行)**|App Service のプロセスの分析で、アプリの一時フォルダーからのプロセスの実行が検出されました。 この動作は、正当である可能性もありますが、Web アプリケーションでは、この動作は悪意のあるアクティビティを示している可能性があります。|
|**Attempt to run high privilege command detected (高い特権のコマンドの実行試行が検出されました)**|App Service のプロセスの分析で、高い特権が必要なコマンドの実行試行が検出されました。 このコマンドは、Web アプリケーションのコンテキストで実行されました。 この動作は、正当である可能性もありますが、Web アプリケーションでは、この動作は悪意のあるアクティビティを示している可能性があります。|

> [!NOTE]
> App Service の Security Center の脅威検出は、現在のところ Azure Government およびソブリン クラウド リージョンでは使用できません。

## Azure Container Service <a name="azure-containers"></a>

Security Center では、auditd フレームワークに基づいて、Linux マシン上のコンテナーに対してリアルタイムの脅威検出が提供されます。 アラートでは、ホスト上での特権を持つコンテナーの作成、Docker コンテナー内での Secure Shell (SSH) サーバーの実行の兆候、暗号マイナーの使用など、いくつかの疑わしい Docker アクティビティが識別されます。 

ユーザーは、この情報を使用して、迅速にセキュリティの問題を修復し、コンテナーのセキュリティを強化することができます。 Security Center は、Linux の検出に加えて、コンテナーのデプロイに特化した分析も提供します。
