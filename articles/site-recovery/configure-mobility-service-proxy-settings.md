---
title: Azure から Azure へのディザスター リカバリーのためにモビリティ サービスのプロキシ設定を構成する | Microsoft Docs
description: お客様がソース環境でプロキシを使用している場合のモビリティ サービスの構成方法の詳細について説明します。
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2020
ms.author: sideeksh
ms.openlocfilehash: 429ffcab147142ae2e96de13b7c9e1e5ee1ac7ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86133227"
---
# <a name="configure-mobility-service-proxy-settings-for-azure-to-azure-disaster-recovery"></a>Azure から Azure へのディザスター リカバリーのためにモビリティ サービスのプロキシ設定を構成する

この記事では、[Azure Site Recovery](site-recovery-overview.md) を使用してリージョン間で Azure 仮想マシン (VM) をレプリケートおよび復旧するときの、ターゲット Azure VM におけるネットワーク構成のカスタマイズに関するガイダンスを提供します。

このドキュメントの目的は、Azure から Azure へのディザスター リカバリー シナリオにおける Azure Site Recovery モビリティ サービスのプロキシ設定を構成する手順を説明することです。 

プロキシは、エンドポイントへのネットワーク接続を許可または禁止するネットワーク ゲートウェイです。 通常、プロキシは、ネットワーク エンドポイントへのアクセスを試みるクライアント マシンの外部にあるマシンです。 バイパス リストを使用すると、クライアントはプロキシを経由せずにエンドポイントに直接接続できます。 認証されたクライアントのみがプロキシを使用できるように、必要に応じて、ネットワーク管理者がプロキシに対してユーザー名とパスワードを設定することもできます。 

## <a name="before-you-start"></a>開始する前に

[このシナリオ](azure-to-azure-architecture.md)に対して、Site Recovery でどのようにディザスター リカバリーを提供するかについて説明します。
[Azure Site Recovery](site-recovery-overview.md) を使用してリージョン間で Azure VM をレプリケートして復旧するときの[ネットワーク ガイダンス](azure-to-azure-about-networking.md)を確認してください。
組織のニーズに基づいてプロキシが適切に設定されていることを確認してください。

## <a name="configure-the-mobility-service"></a>モビリティ サービスを構成する

モビリティ サービスは、認証されていないプロキシのみをサポートします。 Site Recovery エンドポイントとの通信に関してプロキシの詳細を入力する 2 つの方法が用意されています。 

### <a name="method-1-auto-detection"></a>方法 1:自動検出

モビリティ サービスは、レプリケーションの有効化中に、環境設定または IE 設定 (Windows のみ) からプロキシ設定を自動検出します。 

- Windows OS:レプリケーションの有効化中に、モビリティ サービスは、ローカル システム ユーザー用に Internet Explorer で構成されたプロキシ設定を検出します。 ローカル システム アカウントのプロキシを設定するために、管理者は psexec を使用してコマンド プロンプトを起動してから、Internet Explorer を起動できます。 
- Windows OS:プロキシ設定は、環境変数 http_proxy および no_proxy として構成されます。 
- Linux OS:プロキシ設定は、/etc/profile または /etc/environment で環境変数 http_proxy、no_proxy として構成されます。 
- 自動検出されたプロキシ設定は、モビリティ サービスのプロキシ構成ファイル ProxyInfo.conf に保存されます。 
- ProxyInfo.conf の既定の場所 
    - Windows: C:\ProgramData\Microsoft Azure Site Recovery\Config\ProxyInfo.conf 
    - Linux: /usr/local/InMage/config/ProxyInfo.conf


### <a name="method-2-provide-custom-application-proxy-settings"></a>方法 2:カスタム アプリケーション プロキシ設定を指定する

この場合は、お客様がモビリティ サービスの構成ファイル ProxyInfo.conf にカスタム アプリケーション プロキシ設定を指定します。 この方法を使用すると、お客様はモビリティ サービス専用のプロキシを指定することも、Azure Site Recovery モビリティ サービス用に、マシン上の他のアプリケーション用のプロキシ (またはプロキシなし) とは異なるプロキシを指定することもできます。

## <a name="proxy-template"></a>プロキシ テンプレート
ProxyInfo.conf には次のテンプレートが含まれています。[proxy] Address=http://1.2.3.4 Port=5678 BypassList=hypervrecoverymanager.windowsazure.com,login.microsoftonline.com,blob.core.windows.net。 BypassList では、"*.windows.net" のようなワイルドカードはサポートされませんが、windows.net と指定することで十分回避できます。 

## <a name="next-steps"></a>次のステップ:
- Azure VM のレプリケートに関する[ネットワークのガイダンス](./azure-to-azure-about-networking.md)を確認します。
- [Azure VM をレプリケートして](./azure-to-azure-quickstart.md)ディザスター リカバリーを展開します。
