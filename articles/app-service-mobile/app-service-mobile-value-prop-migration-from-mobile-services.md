---
title: Mobile Services の使用時に App Service はどのように役立つか
description: 既存の Mobile Services プロジェクトに App Service がもたらす利点について説明します。
services: app-service\mobile
documentationcenter: ios
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 26b68a11-8352-4f78-acd2-e4e0ec177781
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 8a0bf1c39977d1659d398fce7c4bb3ae0024c88a
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57771142"
---
# <a name="getting-started"> </a>Mobile Services の使用時に App Service はどのように役立つか
## <a name="overview"></a>概要
既存の Mobile Service は安全で、引き続きサポートされます。 ただし、*Azure App Service* プラットフォームでモバイル アプリ向けに提供される機能の一部は、現在の Mobile Services では利用できません。

* Web クライアントとモバイル クライアントの両方を含むアプリを、よりシンプルで簡単なコスト効率の良いものに
* Web ジョブ、カスタム CName、進化した監視を含む新しいホスト機能
* Traffic Manager との統合
* ハイブリッド接続に加えて、VNet を使用したオンプレミス リソースと VPN への接続
* AppInsights を使用したアプリの監視、警告、およびトラブルシューティング
* 基盤となるコンピューティング リソースと価格の幅が広がった
* 組み込みの自動スケール、負荷分散、パフォーマンス監視
* 組み込みのステージング、バックアップ、ロールバック、運用環境でのテスト機能

## <a name="new-hosting-features"></a>新しいホスト機能
*Azure App Service* では、"*モバイル アプリ*" バックエンド コードは Web アプリや API アプリと同じコンテナーで実行されます。 このコンテナーの機能は、現在 Mobile Services にはないものも含めて、すべて利用することができます。

* Web ジョブを使用して、実行中のバックエンド ロジックを継続的に追加する
* バックエンド コードが常に実行されていることを確認する
* カスタムの CNames を使用して、使いやすくて安定した名前をモバイル バックエンド エンドポイントに提供する
* Traffic Manager を使用してアプリを地球スケールにする
* 任意のライブラリとパッケージが含まれます。
* (.NET の場合) MVC などの ASP.NET の任意の機能を使用します。
* (Node.js の場合) 共通 MVC ライブラリなど、ノード エコシステムの任意の純粋な JavaScript ライブラリを使用します。

## <a name="access-on-premises-data-using-vnet"></a>VNet を使用したオンプレミス データへのアクセス
Mobile Services により、現在既にハイブリッド接続を使用してオンプレミス リソースにアクセスすることができます。 ただし、VPN ソリューションの方が望ましい場合もあります。 *Azure App Service* により、モバイル アプリのバックエンド コードに Azure VNet を使用できます。

## <a name="use-your-favorite-backend-language"></a>好みのバックエンド言語の使用
*Azure App Service* により、最新のランタイムへのアクセスなど、ASP.NET と Node.js プラットフォームの広範で豊富なサポートが提供されます。

## <a name="set-up-automatic-scale"></a>自動スケールの設定
Mobile services では、バックエンド コードのすべてのインスタンスが S VM で実行されていました。 *Azure App Service* では、より豊富なオプション セットから VM のサイズを選択することができます。 また、さまざまなパフォーマンス メトリックに基づいて、すばやくスケールアップまたはスケールアウトして顧客の受信負荷を処理することもできます。

## <a name="be-in-the-know"></a>状況の把握
監視と警告を使用して問題にリアルタイムで対応し、自動的に担当者やチームに通知します。 高度なアプリ分析と AppInsights の監視機能を統合して、モバイル アプリの実行状況を分析します。 *Azure App Service* を使用することで、さまざまなパフォーマンス メトリックに基づき、プログラムまたは Azure Portal を使用してアラートを設定できるようになりました。

## <a name="keep-your-assets-safe"></a>資産の安全性の保持
バックエンドとデータベースを自動的にバックアップします。 コードとデータは災害からセキュリティ保護されていて簡単に復元できるため、安心して業務を遂行することができます。

## <a name="ready-stage-go"></a>準備、ステージング、運用開始
*Azure App Service* を使用することにより、モバイル アプリ用に複数のプライベートなテスト環境とステージング環境を作成できるようになりました。 これらを使用して、デプロイ前にテストを実行します。 ダウンタイムなしで運用環境と交換します。 Web アプリは事前に読み込まれ、優れたカスタマー エクスペリエンスを実現します。

こちらの "*チュートリアル*" に従って、既存のモバイル サービスに [App Service](app-service-mobile-migrating-from-mobile-services.md) を活用することができます。
