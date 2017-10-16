---
title: "Microsoft Azure Stack Development Kit のリリース ノート | Microsoft Docs"
description: 
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: a7e61ea4-be2f-4e55-9beb-7a079f348e05
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 2bd93faf01c5d1790989a0231020ce8340eff57d
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---

# <a name="azure-stack-development-kit-release-notes"></a>Azure Stack Development Kit のリリース ノート

*適用先: Azure Stack Development Kit*

これらのリリース ノートでは、新機能と既知の問題に関する情報を提供します。

## <a name="release-build-201706271"></a>リリース ビルド 20170627.1
[20170627.1](azure-stack-updates.md#determine-the-current-version) リリース以降、Azure Stack Proof of Concept は、Azure Stack Development Kit という名前に変更されています。  Azure Stack POC と同様、Azure Stack Development Kit は、Azure Stack の機能を検討し、Azure Stack 用の開発プラットフォームを提供するために使用される開発および評価環境として提供されます。

### <a name="whats-new"></a>新機能
- CLI 2.0 を使用して、一般的なオペレーティング システムのコマンドラインから Azure Stack リソースを管理できるようになりました。
- DSV2 仮想マシン サイズを使用することで、Azure と Azure Stack 間でのテンプレート移植が可能になります。
- クラウド オペレーターは、容量管理ブレード内で容量管理エクスペリエンスをプレビューすることができます。
- Microsoft Azure 診断の拡張機能を使用して、仮想マシンから診断データを収集できるようになりました。  このデータのキャプチャは、ワークロードのパフォーマンスを分析する場合や、問題を調査する場合に役立ちます。
- デプロイについてスクリプト化された以前の手順は、新しい[デプロイ エクスペリエンス](azure-stack-run-powershell-script.md)に置き換えられます。  新しいデプロイ エクスペリエンスでは、デプロイ ライフサイクル全体で共通のグラフィカル インターフェイスが提供されます。
- デプロイ時に Microsoft アカウント (MSA) がサポートされるようになりました。
- デプロイ時に Multi-Factor Authentication (MFA) がサポートされるようになりました。  以前は、デプロイ時に MFA を無効にする必要がありました。

### <a name="known-issues"></a>既知の問題
#### <a name="deployment"></a>デプロイ
* 以前のリリースよりも、デプロイに時間がかかる場合があります。 
* Get-AzureStackLogs では診断ログが生成されますが、進行状況はコンソールに記録されません。
* Azure Stack をデプロイするには、新しい[デプロイ エクスペリエンス](azure-stack-run-powershell-script.md)を使用する必要があります。そうしないと、デプロイに失敗する可能性があります。
* *PublicVLANID* パラメーターを使用すると、デプロイは失敗します。

#### <a name="portal"></a>ポータル
* ポータルに空のダッシュボードが表示されることがあります。  ポータルの右上にある歯車を選択し、[既定の設定に戻す] を選択することで、ダッシュボードを復元できます。
* テナントはサブスクリプションなしですべてのマーケットプレースを参照することができ、プランやオファーなどの管理アイテムを示します。  これらのアイテムはテナントでは機能しません。
* インフラストラクチャ ロール インスタンスを選択すると、参照エラーを示すエラーが表示されます。 管理ポータルを更新するには、ブラウザーの更新機能を使用します。
* [リソース グループ] ブレードの "移動" ボタンは無効になっています。  サブスクリプション間のリソース グループの移動は現在サポートされていないため、これは予期される動作です。
* ダウンロードが完了したシンジケート化されたマーケットプレース アイテムについて、繰り返し通知を受け取ります。
* Azure Stack ポータルを使用して、サブスクリプションへのアクセス許可を表示することはできません。  回避策として、Powershell を使用してアクセス許可を確認することができます。
* ポータルから自動化スクリプトとして完了したデプロイをエクスポートする場合は、フラグとして `-TenantID` を追加する必要があります。

#### <a name="services"></a>サービス
* Key Vault サービスは、テナント ポータルまたはテナント API から作成する必要があります。  管理者としてログインしている場合は、必ず、テナント ポータルを使用して新しい Key Vault コンテナー、シークレット、およびキーを作成してください。
* 仮想マシン スケール セットを作成するためのマーケットプレース エクスペリエンスはありませんが、テンプレートを使用して作成することはできます。
* ポータルを介して、バックエンド ネットワークにロード バランサーを関連付けることはできません。  このタスクは、PowerShell またはテンプレートを使用して完了することができます。
* VM 可用性セットは、1 つの障害ドメインと 1 つの更新ドメインでのみ構成可能です。  
* 新しい Azure 関数を作成するには、テナントに既存のストレージ アカウントが必要です。
* VM で障害が発生し、"引数が null であるため、パラメーター 'VM ネットワーク アダプター' にバインドできません" と報告される場合があります。  仮想マシンの再デプロイは正常に行われます。  
* テナント サブスクリプションを削除すると、リソースが孤立します。  回避策として、まず、テナント リソースまたはリソース グループ全体を削除してから、テナント サブスクリプションを削除します。 
* ネットワーク ロード バランサーを作成する場合は、NAT 規則を作成する必要があります。そうしないと、ロード バランサーが作成された後、NAT 規則を追加しようとしたときにエラーが発生します。
* テナントは、クォータで許可される数より多くの仮想マシンを作成できます。  コンピューティング クォータが適用されていないと、このような動作になります。
* テナントでは、geo 冗長ストレージを持つ仮想マシンを作成するオプションを利用できます。  この構成の場合、仮想マシンの作成が失敗します。
* テナントでデータベースを新しい SQL または MySQL SKU で作成する際に、最大で 1 時間かかる場合があります。 
* リソース プロバイダーで実行されない SQL および MySQL ホスティング サーバーで直接アイテムを作成することはサポートされていないため、状態が不一致になる場合があります。
* AzureRM PowerShell 1.2.10 には、以下の追加の構成手順が必要です。
    * Azure AD デプロイでは Add-AzureRMEnvironment を実行してから次のコマンドを実行します。  `Add-AzureRMEnvironment` の出力を使用して、Name と GraphAudience の値を指定します。
      
      ```PowerShell
      Set-AzureRmEnvironment -Name <Environment Name> -GraphAudience <Graph Endpoint URL>
      ```
    * Active Directory フェデレーション サービス (AD FS) デプロイでは Add-AzureRMEnvironment を実行してから次のコマンドを実行します。  `Add-AzureRMEnvironment` の出力を使用して、Name と GraphAudience の値を指定します。
      
      ```PowerShell
      Set-AzureRmEnvironment <Environment Name> -GraphAudience <Graph Endpoint URL> -EnableAdfsAuthentication:$true
      ```
    
    たとえば、Azure AD 環境では以下のコマンドが使用されます。

    ```PowerShell
      Set-AzureRmEnvironment AzureStack -GraphAudience https://graph.local.azurestack.external/
    ```

#### <a name="fabric"></a>Fabric
* コンピューティング リソース プロバイダーの状態が不明と表示されます。
* BMC IP アドレスとモデルは、スケール ユニット ノードの重要な情報には表示されません。  Azure Stack Development Kit では、この動作は予期されるものです。
* コンピューティング コントローラー インフラストラクチャ ロール (AzS XRP01 インスタンス) では再起動アクションは使用しないでください。
* インフラストラクチャ バックアップ ブレードは使用しないでください。

