---
title: Service Fabric プロジェクトの作成の次の手順
description: Visual Studio で作成したアプリケーション プロジェクトについて説明します。  チュートリアルを使用してサービスを構築する方法を説明し、Service Fabric のサービスの開発について詳しく説明します。
ms.topic: conceptual
ms.date: 12/21/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 59c8eb0737d2cef1c4b1df34d673b74944fef4e1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97760437"
---
# <a name="your-service-fabric-application-and-next-steps"></a>Service Fabric アプリケーションと次の手順
Azure Service Fabric アプリケーションが作成されました。 この記事では、いくつかのリソース、関連するその他の情報、および[次のステップ](#next-steps)を紹介します。

新しいユーザーにとっては、 [チュートリアルやサンプル](#get-started-with-tutorials-walk-throughs-and-samples)が役立つでしょう。 また、[作成されたアプリケーション プロジェクトの構造](#the-application-project)を調べるのにも役立ちます。 さらにこの記事では、Service Fabric の[プログラミング モデル](#learn-more-about-the-programming-models)、[サービス通信](#learn-about-service-communication)、[アプリケーション セキュリティ](#learn-about-configuring-application-security)、[アプリケーション ライフサイクル](#learn-about-the-application-lifecycle)についても説明しています。

経験豊富なユーザーに向けては、Service Fabric の[ベスト プラクティス](#learn-about-best-practices)に関するセクションを設けています。これは、プラットフォームを活用し、アプリケーションを最も効果的に構築するための方法を理解するうえで役立ちます。

質問やフィードバックがある場合や、問題を報告しようとしている場合は、[対応するセクション](#have-questions-or-feedback--need-to-report-an-issue)を参照してください。

## <a name="get-started-with-tutorials-walk-throughs-and-samples"></a>チュートリアルとサンプルの使用
使い始める準備はできていますか。  

.NET アプリケーションのチュートリアルに取り組みます。 ASP.NET Core フロントエンドとステートフルなバックエンドを含む[アプリの作成](service-fabric-tutorial-create-dotnet-app.md)、クラスターへの[アプリケーションのデプロイ](service-fabric-tutorial-deploy-app-to-party-cluster.md)、[CI/CD の構成](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)、[監視と診断の設定](service-fabric-tutorial-monitoring-aspnet.md)の方法を学びます。

または、次のチュートリアルのいずれかを試して、最初のサービスまたはアプリケーションを作成します。
- [Windows 上の C# Reliable Services サービス](service-fabric-reliable-services-quick-start.md) 
- [Windows 上の C# Reliable Actors サービス](service-fabric-reliable-actors-get-started.md) 
- [Windows 上のゲスト実行可能サービス](quickstart-guest-app.md) 
- [Windows コンテナー アプリケーション](service-fabric-get-started-containers.md) 

[サンプル アプリケーション](/samples/browse/?products=azure)を試してみることもできます。

## <a name="the-application-project"></a>アプリケーション プロジェクト
すべての新しいアプリケーションには、アプリケーション プロジェクトが含まれています。 選択したサービスのタイプに応じて、さらに 1 つか 2 つのプロジェクトがある場合があります。

アプリケーション プロジェクトは次で構成されます。

* アプリケーションを構成するサービスへの一連の参照。
* クラスター エンドポイントや既定でデプロイメントのアップグレードを実行するかどうかなど、異なる環境で作業するための設定を管理する 3 つの発行プロファイル (1 ノード ローカル、5 ノード ローカル、およびクラウド)。
* サービス用に作成するパーティション数などの環境に固有のアプリケーション構成を維持するために使用する 3 つのアプリケーション パラメーター ファイル (上記と同じ)。 複数環境向けのアプリケーションを構成する方法については、[こちら](service-fabric-manage-multiple-environment-app-configuration.md)をご覧ください。
* コマンドラインまたは自動化された継続的インテグレーションおよびデプロイ パイプラインの一環としての、アプリケーションをデプロイするために使用するデプロイ スクリプト。 PowerShell を使用したアプリケーションのデプロイの詳細については、[こちら](service-fabric-deploy-remove-applications.md)をご覧ください。
* アプリケーションを説明するアプリケーション マニフェスト。 マニフェストは、ApplicationPackageRoot フォルダーにあります。 アプリケーション マニフェストとサービス マニフェストの詳細については、[こちら](service-fabric-application-model.md)をご覧ください。

## <a name="learn-more-about-the-programming-models"></a>プログラミング モデルの詳細
Service Fabric には、サービスの記述と管理に使用できる複数の方法が用意されています。  [ステートレスおよびステートフル Reliable Services](service-fabric-reliable-services-introduction.md)、[Reliable Actors](service-fabric-reliable-actors-introduction.md)、[コンテナー](service-fabric-containers-overview.md)、[ゲスト実行可能ファイル](service-fabric-guest-executables-introduction.md)、[ステートレスおよびステートフル ASP.NET Core サービス](service-fabric-reliable-services-communication-aspnetcore.md)の概要と概念情報をご覧ください。

## <a name="learn-about-service-communication"></a>サービスの通信について
Service Fabric アプリケーションはさまざまなサービスで構成されており、各サービスがそれぞれに特化したタスクを実行します。 これらのサービスは相互に通信できます。また、サービスに接続して通信するクライアント アプリケーションがクラスターの外部に存在する場合もあります。 Service Fabric のサービスとの通信やサービス間での通信を設定する方法については、[こちら](service-fabric-connect-and-communicate-with-services.md)をご覧ください。 

## <a name="learn-about-configuring-application-security"></a>アプリケーションのセキュリティの構成について
さまざまなユーザー アカウントを使用してクラスターで実行されているアプリケーションをセキュリティで保護することができます。 また、Service Fabric は、そのユーザー アカウントでデプロイするときにアプリケーションによって使用されるリソース (ファイル、ディレクトリ、証明書など) を保護するためにも役立ちます。 これにより、実行中のアプリケーションは、共有のホスト環境にある場合でも、互いからより保護されます。  アプリケーションのセキュリティ ポリシーを構成する方法については、[こちら](service-fabric-application-runas-security.md)をご覧ください。

アプリケーションには、機密情報 (ストレージ接続文字列、パスワード、プレーン テキストで処理できないその他の値など) が含まれている場合があります。 アプリケーションでシークレットを管理する方法については、[こちら](service-fabric-application-secret-management.md)をご覧ください。

## <a name="learn-about-the-application-lifecycle"></a>アプリケーション ライフサイクルについて
他のプラットフォームと同様に、通常、Service Fabric アプリケーションは、設計、開発、テスト、デプロイ、アップグレード、保守、削除の各フェーズを通過します。 [この記事](service-fabric-application-lifecycle.md)では、API の概要と、Service Fabric アプリケーション ライフサイクルの各フェーズでさまざまなロールが API をどのように使用するかについて説明しています。

## <a name="learn-about-best-practices"></a>ベスト プラクティスを確認する
Service Fabric では、[ベスト プラクティス](./service-fabric-best-practices-overview.md)について説明した記事が多数用意されています。 この情報を活用して、クラスターとアプリケーションを可能な限り有効に実行できるようにしましょう。
取り上げるトピックは次のとおりです。
* [Security](./service-fabric-best-practices-security.md)
* [ネットワーク](./service-fabric-best-practices-networking.md)
* [コンピューティングの計画とスケーリング](./service-fabric-best-practices-capacity-scaling.md)
* [コードとしてのインフラストラクチャ](./service-fabric-best-practices-infrastructure-as-code.md)
* [監視と診断](./service-fabric-best-practices-monitoring.md)
* [アプリケーションの設計](./service-fabric-best-practices-applications.md)

また、すべてのベスト プラクティス情報を使いやすい形式でまとめた、[運用環境の準備状況チェックリスト](./service-fabric-production-readiness-checklist.md)も提供されています。

## <a name="have-questions-or-feedback--need-to-report-an-issue"></a>質問やフィードバックがある場合や  問題を報告する必要がある場合
[よく寄せらせる質問](service-fabric-common-questions.md)に目を通し、Service Fabric で実行できる内容と使用方法に関する回答を確認します。

[トラブルシューティング ガイド](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)は、Service Fabric クラスターでの一般的な問題を診断して解決するのに役立ちます。

[サポート オプション](service-fabric-support.md)に関する記事には、質問に適した StackOverflow および MSDN のフォーラムと、問題の報告、サポートの利用、製品フィードバックの送信に関するオプションが記載されています。


## <a name="next-steps"></a>次のステップ
- [Azure での Windows クラスターの作成](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
- [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) を使用したクラスターの視覚化 (デプロイ済みのアプリケーションや物理的なレイアウトなど)
- [サービスのアップグレードとバージョン管理](service-fabric-application-upgrade-tutorial.md)