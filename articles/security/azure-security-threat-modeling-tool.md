---
title: Microsoft Threat Modeling Tool - Azure | Microsoft Docs
description: "発生した最も危険な脅威の軽減策を含む、Microsoft Threat Modeling Tool のメイン ページ"
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: rodsan
translationtype: Human Translation
ms.sourcegitcommit: 2ad59f9b463671e2b74708871a9a9faae9bd24eb
ms.openlocfilehash: b75de472508bbf44c6e02e0d751054f37b0e1b8c
ms.lasthandoff: 02/21/2017


---

# <a name="microsoft-threat-modeling-tool"></a>Microsoft Threat Modeling Tool 
Threat Modeling Tool は、Microsoft セキュリティ開発ライフサイクル (SDL) の主要な要素です。 これを使用すると、ソフトウェア アーキテクトは早い段階で潜在的なセキュリティの問題を特定し、危険を軽減することができます。早い段階であれば、問題の解決は比較的容易で、コスト効率も良くなります。 そのため、開発総コストを大幅に軽減できます。 また、このツールはセキュリティの専門家ではないユーザーを想定して設計され、脅威モデルの作成と分析に関するわかりやすいガイダンスが用意されているため、すべての開発者が簡単に脅威をモデリングできます。 

このツールを使用すると、だれでも次の作業を行うことができます。
* システムのセキュリティ設計に関して連絡する
* 実績ある手法を使用して、潜在的なセキュリティの問題に関して、これらの設計を分析する
* セキュリティの問題の軽減策を提案および管理する

ツールの代表的な機能と技術は、次のとおりです。
* **自動化:** モデルを作成するためのガイダンスとフィードバック
* **要素ごとの STRIDE:** 脅威と軽減策についてのガイド付き分析
* **レポート:** 検証フェーズでのセキュリティ アクティビティとテスト
* **独自の方法論:** 脅威を視覚化し、より的確に理解
* **開発者向けの設計とソフトウェア重視:** 多くのアプローチでは、資産または攻撃者が中心になっています。 このツールでは、ソフトウェアが中心です。 すべてのソフトウェア開発者やアーキテクトが慣れ親しんでいる、ソフトウェア アーキテクチャの図の作成などのアクティビティをベースにしています
* **設計分析を重視:** "脅威モデリング" という用語は、要件を意味することも、設計分析手法を意味することもあります。 場合によっては、両方が複雑に入り交じっています。 脅威モデリングに対する Microsoft SDL のアプローチでは、設計分析手法に重点を置いています

## <a name="threats"></a>脅威

Threat Modeling Tool を使用すると、以下のような特定の質問に答えることができます。

* 攻撃者はどのような方法で認証データを変更できるか。
* 攻撃者がユーザー プロファイル データを読み取ることができる場合、どのような影響があるか。
* ユーザー プロファイル データベースへのアクセスが拒否された場合、何が起きるか。

これらの種類の特定の質問をより適切に定式化できるように、Microsoft は STRIDE モデルを使用しています。このモデルではさまざまな種類の脅威を分類し、セキュリティに関する会話全体を簡単にします。

| カテゴリ | Description |
| -------- | ----------- |
| なりすまし | 他のユーザーの認証情報 (ユーザー名、パスワードなど) に不正にアクセスし、それを使用する行為などです |
| 改ざん | 悪意のあるデータの変更などです。 例としては、データベースに保持されているような永続的なデータに対する許可されていない変更や、インターネットなどのオープン ネットワーク経由で&2; 台のコンピューター間を流れるデータの変更などがあります |
| 否認 | 反証できる関係者がいない状況でアクションの実行を否定するユーザーに関連するものです。たとえば、禁止されている操作を追跡できる機能がないシステムでユーザーが不正な操作を行うような場合です。 否認不可とは、否認の脅威に対抗するシステムの機能のことです。 たとえば、商品を購入するユーザーは、受け取り時に署名をする必要があります。 販売者は、署名された受領書をユーザーが荷物を受け取ったことの証拠として使用することができます |
| 情報漏えい | 情報へのアクセスが想定されていない個人への情報の暴露などです。たとえば、アクセスが許可されていないファイルをユーザーが読み取ることができたり、侵入者が2 台のコンピューター間で送信されるデータを読み取ることができたりする場合です |
| サービス拒否 | サービス拒否 (DoS) 攻撃では、有効なユーザーへのサービスが拒否されます。たとえば、Web サーバーを一時的に使用できなくする行為です。 システムの可用性と信頼性を向上させるために、特定の種類の DoS 脅威からシステムを保護する必要があります |
| 特権の昇格 | 特権のないユーザーが特権的なアクセスを取得すると、システム全体を侵害したり破壊したりできるようになります。 特権の昇格の脅威には、攻撃者が効果的にすべてのシステム防御を破り、信頼されているシステム自体の一部となる、本当に危険な状況が含まれます |

## <a name="mitigations"></a>軽減策

Threat Modeling Tool の軽減策は、Web アプリケーションのセキュリティ フレームに基づいて分類されます。このフレームの構成要素は、次のとおりです。

| カテゴリ | 説明 |
| -------- | ----------- |
| [監査とログ記録](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-auditing-and-logging) | だれがいつ何をしたか。 監査とログ記録とは、アプリケーションがセキュリティ関連のイベントを記録する方法のことです |
| [認証](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-authentication) | 自分がだれであるか。 認証は、エンティティが他のエンティティの ID を証明するプロセスであり、通常はユーザー名、パスワードなどの資格情報を使用します |
| [承認](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-authorization) | 何ができますか? 承認は、アプリケーションがリソースや操作に対するアクセス制御を提供する方法です | 
| [通信のセキュリティ](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-communication-security) | 通信相手はだれか。 通信のセキュリティでは、行われるすべての通信が可能な限り保護されることが保証されます |
| [構成管理](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-configuration-management) | アプリケーションがだれとして実行されるか。 どのデータベースに接続するか。 アプリケーションはどのように管理されるか。 これらの設定はどのように保護されるか。 構成管理とは、アプリケーションがこれらの操作上の問題を扱う方法のことです | 
| [暗号化](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-cryptography) | どのように秘密を保持するか (機密性)。 データまたはライブラリを改ざんからどのように保護するか (整合性)。 暗号的に強固である必要があるランダムな値のシードをどのように指定するか。 暗号化とは、アプリケーションが機密性と整合性を強制的に確保する方法のことです | 
| [例外管理](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-exception-management) | アプリケーションで呼び出したメソッドが失敗した場合、アプリケーションはどうするか。 どの程度公開するか。 エンド ユーザーにわかりやすいエラー情報を返すか。 呼び出し元に有益な例外情報を戻すか。 アプリケーションの失敗時に正常に終了させるか。 |
| [入力の検証](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-input-validation) | アプリケーションが受け取る入力が有効で安全であることをどうやって知ることができるか。 入力の検証とは、処理を進める前にアプリケーションが入力をフィルター処理、スクラブ、または拒否する方法のことです。 エントリ ポイントで入力を制限し、エグジット ポイントで出力をエンコードすることを検討してください。 データベースやファイル共有などのソースからのデータを信頼するか。 |
| [機微なデータ](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-sensitive-data) | アプリケーションが機微なデータをどのように処理するか。 機微なデータとは、メモリ内、ネットワーク上、または固定ストア内で保護する必要があるデータをアプリケーションが処理する方法のことです | 
| [セッションの管理](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-session-management) | アプリケーションがユーザー セッションをどのように処理および保護するか。 セッションとは、ユーザーと Web アプリケーション間の一連の関連するやり取りのことです | 

これにより、以下のことを特定できます。 

* 最も一般的な間違いがどこで起きるか
* 最も実践的な機能強化をどこで行うことができるか

そのため、これらのカテゴリを使用して、セキュリティに関連する作業の対象と優先順位を定めることができます。最も一般的なセキュリティの問題が入力の検証、認証、および承認のカテゴリで発生することが判明した場合は、そこから作業を開始できます。 詳細については、[こちら](https://www.google.com/patents/US7818788)をクリックしてください

## <a name="next-steps"></a>次のステップ
今すぐ作業を始めるには、以下の役に立つリンクを参照してください。
* [ダウンロード リンク](https://www.microsoft.com/download/details.aspx?id=49168)
* [作業の開始](https://msdn.microsoft.com/magazine/dd347831.aspx)
* [コア トレーニング](https://www.microsoft.com/download/details.aspx?id=16420)

同時に、Threat Modeling Tool の専門家による成果も参照してください。
* [Threats Manager](https://simoneonsecurity.com/threatsmanagersetup-v1-5-10/)
* [Simone Curzi のセキュリティ ブログ](https://simoneonsecurity.com/)
