---
title: "ワークフローでのアプリの接続とデータの統合 - Azure Logic Apps | Microsoft Docs"
description: "ワークフローを作成し、Azure Logic Apps でアプリの接続とデータの統合を行うことによって、プロセスを自動化します。"
author: kevinlam1
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 07765c05-72a6-4169-a8ab-f6420bfbaf07
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/23/2017
ms.author: klam
translationtype: Human Translation
ms.sourcegitcommit: 11aa3f74d112244cd96278c2b2e3d701e031aee8
ms.openlocfilehash: 6415b0e1c7feb744c6c13a0ae19ed434b6e9befc


---
# <a name="what-are-logic-apps"></a>Logic Apps とは
Logic Apps では、クラウド上でのスケーラブルな統合やワークフローを簡略化し、実装するための手段を提供します。 また、ワークフローと呼ばれる一連のステップとしてプロセスをモデル化し、自動化するためのビジュアル デザイナーが用意されています。  サービスとプロトコルをまたいだ迅速な統合のために、クラウドとオンプレミスの両方で [数多くのコネクタ](../connectors/apis-list.md) が提供されています。  ロジック アプリはトリガー ("Dynamics CRM にアカウントが追加されたとき" など) によって起動することができ、その後も数多くのアクション、変換、条件ロジックを組み合わせて起動できます。

Logic Apps を使用する利点は次のとおりです。  

* わかりやすい設計ツールを使って複雑なプロセスを設計できるため、時間を節約できる
* コードでは実装が難しいパターンやワークフローをシームレスに実装できる
* テンプレートを基に簡単に設計を開始できる
* 独自のカスタム API、コード、アクションでロジック アプリをカスタマイズできる
* オンプレミスとクラウドにまたがる異なるシステム間で接続や同期ができる
* BizTalk Server、API Management、Azure Functions、Azure Service Bus を基に作成でき、最上級の統合サポートが得られる

Logic Apps は完全に管理された iPaaS (サービスとしての統合プラットフォーム) であり、開発者はホスティング、スケーラビリティ、可用性、管理能力の構築について頭を悩ます必要がなくなります。  Logic Apps は需要に合わせて自動的にスケールアップします。

![フロー アプリ デザイナー](media/logic-apps-what-are-logic-apps/LogicAppCapture2.png)

既に説明したように、Logic Apps を使うと、ビジネス プロセスを自動化できます。 いくつかの例を次に示します。  

* FTP サーバーにアップロードされたファイルを Azure Storage に移動する
* オンプレミス システムとクラウド システムの間で命令を処理し、ルーティングする
* 特定のトピックに関するすべてのツイートを監視し、センチメントを分析して、フォローアップが必要なアイテム用にアラートやタスクを作成する

こうしたシナリオはすべてビジュアル デザイナーで、コードを&1; 行も記述することなく構成できます。 [ロジック アプリの構築を今すぐ開始][create]しましょう。  作成後のロジック アプリは、複数の環境やリージョンにわたり、 [すばやくデプロイして再構築](../logic-apps/logic-apps-create-deploy-template.md) できます。

## <a name="why-logic-apps"></a>なぜ Logic Apps か
Logic Apps を利用することで、エンタープライズ統合の領域に速度とスケーラビリティが加わります。  使いやすいデザイン ツール、利用できるさまざまなトリガーとアクション、強力な管理ツールを駆使することで、API の一元的な管理がこれまでになく簡単になります。  ビジネスのデジタル化が進むなか、Logic Apps を利用すれば、従来型のシステムと最先端システムを結び付けることが可能です。

さらに、[エンタープライズ統合アカウント][biztalk]を使えば、[XML メッセージング][xml]や[取引先管理][tpm]などを活用して、成熟した統合シナリオへと拡張することができます。

* **使いやすいデザイン ツール** - Logic Apps は、ブラウザーや Visual Studio ツールで全体を設計することができます。 トリガーによる起動 - 単純なスケジュールから特定のイベント (GitHub Issue の作成時など) まで、さまざまなタイミングでトリガーを設定できます。 次に、豊富なコネクタのギャラリーを使用して必要な数のアクションを調整します。
* **API と簡単に接続** - 簡単に説明できるような構成タスクでさえ、コードとして実装するには困難が伴います。 Logic Apps を使用すれば異なるシステムへの接続も簡単です。 クラウド マーケティング ソリューションをオンプレミスの請求書作成システムに接続しようとしていますか? エンタープライズ サービス バスを使用して、API やシステム間でのメッセージングを一元的に管理する必要がありますか? Logic Apps は、これらの問題にソリューションを提供する、最も迅速で信頼できる手段です。
* **テンプレートからすばやく開始** - 作業開始を手助けするため、一般的なソリューションを速やかに作成できる[テンプレート ギャラリー][templates]が用意されています。 高度な B2B ソリューションから単純な SaaS 接続にまで対応し、単なる "お遊び用" のテンプレートも用意しています。Logic Apps の機能を理解するには、このギャラリーが一番の近道です。
* **組み込みの拡張機能** - 必要なコネクタが見つかりませんか? Logic Apps はユーザー独自の API やコードと連携するように設計されているため、独自の API アプリを簡単に作成してカスタム コネクタとして使用することや、 [Azure Function](https://functions.azure.com) を呼び出し、要求に応じてコード スニペットを実行することができます。 
* **真の統合能力** - 手軽に始めて必要に応じて拡張できます。 Logic Apps は、Microsoft の業界屈指の統合ソリューション、BizTalk の能力を統合担当者が簡単に利用して必要なソリューションを構築できるようにしています。 Enterprise Integration Pack の詳細については、[こちら](../logic-apps/logic-apps-enterprise-integration-overview.md)をご覧ください。

## <a name="logic-app-concepts"></a>Logic Apps の概念
Logic Apps の機能を構成する主な要素とは次のとおりです。 

* **ワークフロー** - Logic Apps では、ビジネス プロセスを一連の手順やワークフローとしてグラフィカルにモデル化できます。
* **マネージ コネクタ** - ロジック アプリにはデータとサービスへのアクセスが必要です。 マネージ コネクタは、データへの接続とデータの操作を支援する目的で作成されています。 [マネージ コネクタ][managedapis]に関するページで、現在利用できるコネクタの一覧を確認してください。
* **トリガー** - 一部のマネージ コネクタは、トリガーとしても動作します。 トリガーは、電子メールや Azure のストレージ アカウントの変更の到着などの特定のイベントに基づいて、ワークフローの新しいインスタンスを開始します。
* **アクション** - ワークフローにおけるトリガーの後の各ステップは、アクションと呼ばれます。 通常、各アクションはマネージ コネクタまたはカスタム API アプリでの操作にマップされます。
* **Enterprise Integration Pack** - Logic Apps には、高度な統合シナリオ向けに BizTalk の機能が含まれています。 BizTalk は、Microsoft による業界屈指の統合プラットフォームです。 Enterprise Integration Pack コネクタにより、Logic Apps ワークフローに検証や変換などを簡単に含めることができます。

## <a name="getting-started"></a>Getting Started (概要)
* Logic Apps の使用を開始するには、[ロジック アプリの作成][create]に関するチュートリアルに従ってください。  
* [一般的な例とシナリオを確認する](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Logic Apps を使用してビジネス プロセスを自動化する](http://channel9.msdn.com/Events/Build/2016/T694) 
* [Logic Apps を使用してシステムを統合する方法を説明する](http://channel9.msdn.com/Events/Build/2016/P462)

[biztalk]: logic-apps-enterprise-integration-accounts.md
[appservice]: ../app-service/app-service-value-prop-what-is.md
[create]: logic-apps-create-a-logic-app.md
[managedapis]: ../connectors/apis-list.md
[tpm]: logic-apps-enterprise-integration-accounts.md
[xml]: logic-apps-enterprise-integration-b2b.md
[templates]: logic-apps-use-logic-app-templates.md



<!--HONumber=Jan17_HO4-->


