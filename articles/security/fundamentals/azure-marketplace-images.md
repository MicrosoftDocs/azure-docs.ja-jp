---
title: Azure Marketplace イメージのセキュリティに関する推奨事項 | Microsoft Docs
description: この記事では、Marketplace に含まれるイメージについての推奨事項を示します
services: security
documentationcenter: na
author: terrylanfear
manager: barbkess
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: terrylan
ms.openlocfilehash: 7c317a0b4fea0c981b227bace00c1b8924fd582c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89536384"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Azure Marketplace イメージのセキュリティに関する推奨事項

イメージは、これらのセキュリティ構成の推奨事項を満たしている必要があります。 そうすることで、Azure Marketplace のパートナー ソリューション イメージのセキュリティを高いレベルに維持することができます。

送信する前に、必ずイメージに対してセキュリティの脆弱性の検出を実行してください。 自分が発行したイメージでセキュリティの脆弱性を検出した場合は、脆弱性とその修正方法の両方について、適切なタイミングで顧客に通知する必要があります。

## <a name="open-source-based-images"></a>オープン ソース ベースのイメージ

| カテゴリ | ○ |
| -------- | ----- |
| Security                                                     | Linux ディストリビューションの最新のセキュリティ パッチをすべてインストールします。                                                                                                                                                                                                              |
| Security                                                     | 特定の Linux ディストリビューション用の VM イメージのセキュリティ保護に関する業界のガイドラインに従います。                                                                                                                                                                                     |
| Security                                                     | Windows Server の役割、機能、サービス、ネットワーク ポートを必要なものだけにしてフット プリントを最小限にすることで、攻撃対象領域を制限します。                                                                                                                                               |
| Security                                                     | ソース コードおよびそれから作成される VM イメージでマルウェアをスキャンします。                                                                                                                                                                                                                                   |
| Security                                                     | VHD イメージには、既定のパスワードを持たない、ロックされた必要なアカウントだけが含まれています。これにより、対話型ログインが可能になり、バック ドアが存在しないようにします。                                                                                                                                           |
| Security                                                     | ファイアウォール アプライアンスなど、アプリケーションが機能的に依存している場合を除き、ファイアウォール規則を無効にします。                                                                                                                                                                             |
| Security                                                     | テスト SSH キー、既知の hosts ファイル、ログ ファイル、不要な証明書など、すべての機密情報を VHD イメージから削除します。                                                                                                                                       |
| Security                                                     | LVM の使用を避けます。                                                                                                                                                                                                                                            |
| Security                                                     | 必要なライブラリの最新バージョンを含めます。 </br> - OpenSSL v1.0 以降 </br> - Python 2.5 以降 (Python 2.6 以降の使用を強くお勧めします) </br> - Python pyasn1 パッケージ (まだインストールされていない場合) </br> - d.OpenSSL v 1.0 以降                                                                |
| Security                                                     | Bash/シェルの履歴エントリをクリアします。                                                                                                                                                                                                                                             |
| ネットワーク                                                   | 既定で SSH サーバーを含めます。 次のオプションを使って、SSH キープアライブを sshd 構成に設定します。ClientAliveInterval 180。                                                                                                                                                        |
| ネットワーク                                                   | イメージからカスタム ネットワーク構成をすべて削除します。 resolv.conf を削除します (`rm /etc/resolv.conf`)。                                                                                                                                                                                |
| デプロイ                                                   | 最新の Azure Linux エージェントをインストールします。</br> - RPM または Deb パッケージを使用してインストールします。  </br> - 手動インストール プロセスを使うこともできますが、インストーラー パッケージを優先することをお勧めします。 </br> - GitHub リポジトリから手動でエージェントをインストールする場合は、最初に `waagent` ファイルを `/usr/sbin` にコピーし、root として実行します。 </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>エージェントの構成ファイルは `/etc/waagent.conf` に配置されます。 |
| デプロイ                                                   | Azure サポートが必要に応じてシリアル コンソール出力を提供し、クラウド ストレージからの OS ディスクのマウントに十分なタイムアウトを提供できることを確認します。 パラメーター `console=ttyS0 earlyprintk=ttyS0 rootdelay=300`をイメージのカーネル ブート ラインに追加します。 |
| デプロイ                                                   | OS ディスクにスワップ パーティションがないこと。 Linux エージェントは、ローカル リソース ディスクへのスワップの作成を要求できます。         |
| デプロイ                                                   | OS ディスクの単一のルート パーティションを作成します。      |
| デプロイ                                                   | 64 ビット オペレーティング システムだけであること。                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Windows Server ベースのイメージ

| カテゴリ | ○ |
|--------- | ----- |
| Security                                                         | セキュリティで保護された OS ベース イメージを使います。 Windows Server に基づくイメージのソースに使われる VHD は、Microsoft Azure によって提供される Windows Server OS イメージが基になっている必要があります。 |
| Security                                                         | 最新のセキュリティ更新プログラムをすべてインストールします。                                                                                                                                     |
| Security                                                         | アプリケーションは、administrator、root、admin などの制限されたユーザー名に依存してはなりません。                                                                |
| Security                                                         | OS ハード ドライブとデータ ハード ドライブの両方に対して BitLocker ドライブ暗号化を有効にします。                                                             |
| Security                                                         | Windows Server の役割、機能、サービス、ネットワーク ポートは必要なものだけを有効にしてフット プリントを最小限にすることで、攻撃対象領域を制限します。                         |
| Security                                                         | ソース コードおよびそれから作成される VM イメージでマルウェアをスキャンします。                                                                                                                     |
| Security                                                         | Windows Server イメージのセキュリティ更新プログラムを自動更新に設定します。                                                                                                                |
| Security                                                         | VHD イメージには、既定のパスワードを持たない、ロックされた必要なアカウントだけが含まれています。これにより、対話型ログインが可能になり、バック ドアが存在しないようにします。                             |
| Security                                                         | ファイアウォール アプライアンスなど、アプリケーションが機能的に依存している場合を除き、ファイアウォール規則を無効にします。                                                               |
| Security                                                         | HOSTS ファイル、ログ ファイル、不要な証明書など、すべての機密情報を VHD イメージから削除します。                                              |
| デプロイ                                                       | 64 ビット オペレーティング システムだけであること。                            |

組織が Azure Marketplace にイメージを持っていない場合でも、これらの推奨事項に照らして Windows と Linux のイメージ構成をチェックすることを検討してください。

