---
title: "Azure Stack とは | Microsoft Docs"
description: "Azure Stack を使用すると、お使いのデータセンターで Azure を実行できます。"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 09/25/2017
ms.author: helaw
ms.custom: mvc
ms.openlocfilehash: 950ba44c0b7eb80c9b0a3c69a9fca03cd244576d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-stack"></a>Azure Stack とは

Microsoft Azure Stack はハイブリッド クラウド プラットフォームで、所属している組織のデータセンターから Azure サービスを提供できます。  Azure Stack は、主要なシナリオで最新アプリケーション向けの新しいシナリオ (エッジ環境や非接続環境など) を実現し、セキュリティとコンプライアンスの要件に対応できるように設計されています。  Azure Stack には、お客様のニーズに合わせて 2 つのデプロイ オプションが用意されています。

## <a name="azure-stack-integrated-systems"></a>Azure Stack 統合システム
Azure Stack 統合システムは Microsoft と[ハードウェア パートナー](https://azure.microsoft.com/overview/azure-stack/integrated-systems/)のパートナーシップによって提供され、クラウドと歩調を合わせた革新と、管理のしやすさをバランスよく両立させたソリューションを実現します。  Azure Stack はハードウェアとソフトウェアの統合システムとして提供されるため、クラウドからの革新を採用しつつ適度な柔軟性と制御性が得られます。  Azure Stack 統合システムには 4 ノードから 12 ノードまでのサイズがあり、ハードウェア パートナーと Microsoft によって共同でサポートされます。  Azure Stack 統合システムの使用により、実稼働ワークロード向けの新しいシナリオが可能になります。    

## <a name="azure-stack-development-kit"></a>Azure Stack Development Kit
Microsoft Azure Stack 開発キットは、Azure Stack についての評価と学習に使用できる Azure Stack の単一ノード デプロイです。  Azure Stack 開発キットは開発環境としても使用でき、Azure と一貫性のある API やツールを使用して開発を行うことができます。  Azure Stack 開発キットは、運用環境での使用を意図していません。

Azure Stack 開発キットには次の制限事項があります。
* Azure Stack 開発キットは、単一の Azure Active Directory または Active Directory フェデレーション サービス (AD FS) ID プロバイダーに関連付けられます。 このディレクトリに複数のユーザーを作成し、各ユーザーにサブスクリプションを割り当てることができます。
* すべてのコンポーネントが単一のマシンにデプロイされているため、テナントのリソースが使用できる物理リソースは限られています。 この構成は、拡張性やパフォーマンスの評価のためのものではありません。
* 単一ホスト/NIC 要件のため、ネットワークのシナリオは制限されています。  

## <a name="next-steps"></a>次のステップ
[主要機能および概念](azure-stack-key-features.md)

[Azure Stack: Azure の拡張機能 (pdf)](https://azure.microsoft.com/en-us/resources/azure-stack-an-extension-of-azure/)

