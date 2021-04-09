---
title: Azure のセキュリティ コントロール - セキュリティで保護された構成
description: Azure セキュリティ コントロールのセキュリティで保護された構成
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: b13cc6f8bb50a3c9ec3cae519db387f9f32c950d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102615280"
---
# <a name="security-control-secure-configuration"></a>セキュリティ コントロールセキュリティで保護された構成

攻撃者から脆弱なサービスや設定が悪用されないように、Azure リソースのセキュリティ構成を確立、実装、および積極的に管理 (追跡、レポート、修正) します。

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 7.1 | 5.1 | Customer |

ご利用の Azure リソースの構成を監査または適用するには、Azure Policy エイリアスを使用してカスタム ポリシーを作成します。 組み込みの Azure Policy 定義を使用することもできます。

また、Azure Resource Manager には、テンプレートを JavaScript Object Notation (JSON) でエクスポートする機能があり、構成が確実に組織のセキュリティ要件を満たすかそれを超えるように確認する必要があります。

また、ご利用の Azure リソース用の安全な構成基準として Azure Security Center からの推奨事項を使用することもできます。

- [使用可能な Azure Policy エイリアスを表示する方法](/powershell/module/az.resources/get-azpolicyalias)

- [チュートリアル:コンプライアンスを強制するポリシーの作成と管理](../../governance/policy/tutorials/create-and-manage.md)

- [Azure portal のテンプレートへの単一および複数リソースのエクスポート](../../azure-resource-manager/templates/export-template-portal.md)

- [セキュリティの推奨事項 - リファレンス ガイド](../../security-center/recommendations-reference.md)

## <a name="72-establish-secure-operating-system-configurations"></a>7.2:セキュリティで保護されたオペレーティング システムの構成を確立する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 7.2 | 5.1 | Customer |

すべてのコンピューティング リソースのセキュリティ構成を維持するには、Azure Security Center の推奨事項を使用します。  さらに、カスタム オペレーティング システム イメージまたは Azure Automation State Configuration を使用して、組織に必要なオペレーティング システムのセキュリティ構成を確立できます。

- [Azure Security Center の推奨事項を監視する方法](../../security-center/security-center-recommendations.md)

- [セキュリティの推奨事項 - リファレンス ガイド](../../security-center/recommendations-reference.md)

- [Azure Automation State Configuration の概要](../../automation/automation-dsc-overview.md)

- [VHD をアップロードし、それを使用して Azure で新しい Windows VM を作成する](../../virtual-machines/windows/upload-generalized-managed.md)

- [Azure CLI を使用してカスタム ディスクから Linux VM を作成する](../../virtual-machines/linux/upload-vhd.md)

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 7.3 | 5.2 | Customer |

お使いの Azure リソース全体にセキュリティで保護された設定を適用するには、Azure Policy の [deny] と [deploy if not exist] を使用します。  さらに、Azure Resource Manager テンプレートを使用して、組織に必要な Azure リソースのセキュリティ構成を維持できます。 

- [Azure Policy の効果について](../../governance/policy/concepts/effects.md)

- [コンプライアンスを強制するポリシーの作成と管理](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager テンプレートの概要](../../azure-resource-manager/templates/overview.md)

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4:セキュリティで保護されたオペレーティング システムの構成を維持する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 7.4 | 5.2 | 共有 |

Azure コンピューティング リソースに対する脆弱性評価の実行に関する Azure Security Center の推奨事項に従います。  さらに、Azure Resource Manager テンプレート、カスタム オペレーティング システム イメージ、または Azure Automation State Configuration を使用して、組織に必要なオペレーティング システムのセキュリティ構成を維持できます。   Microsoft 仮想マシン テンプレートと Azure Automation Desired State Configuration を組み合わせると、セキュリティ要件を満たし、それを維持するために役立ちます。 

また、Microsoft によって公開された Azure Marketplace の仮想マシン イメージが Microsoft によって管理および維持されることにも注意してください。 

- [Azure Security Center の脆弱性評価の推奨事項を実装する方法](../../security-center/deploy-vulnerability-assessment-vm.md)

- [Azure Resource Manager テンプレートから Azure 仮想マシンを作成する方法](../../virtual-machines/windows/ps-template.md)

- [Azure Automation State Configuration の概要](../../automation/automation-dsc-overview.md)

- [Azure portal で Windows 仮想マシンを作成する](../../virtual-machines/windows/quick-create-portal.md)

- [VM テンプレートをダウンロードする方法に関する情報](/previous-versions/azure/virtual-machines/windows/download-template)

- [VHD を Azure にアップロードし、新しい VM を作成するサンプル スクリプト](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 7.5 | 5.3 | Customer |

カスタム Azure ポリシー、Azure Resource Manager テンプレート、Desired State Configuration スクリプトなどのコードを安全に格納して管理するには、Azure DevOps を使用します。 Azure DevOps で管理するリソースにアクセスするには、Azure Active Directory (Azure AD) で定義された (Azure DevOps に統合されている場合)、または Active Directory で定義された (TFS に統合されている場合) 特定のユーザー、組み込みのセキュリティ グループ、またはグループにアクセス許可を付与したり、そのアクセス許可を拒否したりできます。

- [Azure DevOps でコードを格納する方法](/azure/devops/repos/git/gitworkflow)

- [Azure DevOps でのアクセス許可とグループについて](/azure/devops/organizations/security/about-permissions)

## <a name="76-securely-store-custom-operating-system-images"></a>7.6:カスタム オペレーティング システム イメージを安全に格納する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 7.6 | 5.3 | Customer |

カスタム イメージを使用している場合は、Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、承認されたユーザーのみがイメージにアクセスできるようにします。 共有イメージ ギャラリーを使用すると、組織内のさまざまなユーザー、サービス プリンシパル、AD グループに対してイメージを共有できます。  コンテナー イメージの場合は、Azure Container Registry に保存し、Azure RBAC を利用して、承認されたユーザーのみがイメージにアクセスできるようにします。  

- [Azure RBAC について](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- [コンテナー レジストリの Azure RBAC を理解する](../../container-registry/container-registry-roles.md)

- [Azure RBAC を構成する方法](../../role-based-access-control/quickstart-assign-role-user-portal.md)

- [共有イメージ ギャラリーの概要](../../virtual-machines/shared-image-galleries.md)

## <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7:Azure リソース用の構成管理ツールをデプロイする

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 7.7 | 5.4 | Customer |

Azure Policy を使用して、Azure リソースの標準的なセキュリティ構成を定義して実装します。 ご利用の Azure リソースのネットワーク構成を監査または適用するには、Azure Policy エイリアスを使用してカスタム ポリシーを作成します。 また、特定のリソースに関連する組み込みのポリシー定義を使用することもできます。  さらに、Azure Automation を使用して、構成の変更をデプロイすることもできます。

- [Azure Policy を構成して管理する方法](../../governance/policy/tutorials/create-and-manage.md)

- [エイリアスを使用する方法](../../governance/policy/concepts/definition-structure.md#aliases)

## <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8:オペレーティング システム用の構成管理ツールをデプロイする

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 7.8 | 5.4 | Customer |

Azure Automation State Configuration は、あらゆるクラウドまたはオンプレミスのデータセンターにある Desired State Configuration (DSC) ノードの管理構成サービスです。 マシンのオンボード、宣言型構成の割り当て、指定した必要な状態に準拠した各マシンを示すレポートの表示を簡単に行うことができます。 

- [Azure Automation State Configuration による管理のためのマシンのオンボード](../../automation/automation-dsc-onboarding.md)

## <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9:Azure リソースの自動構成監視を実装する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 7.9 | 5.5 | Customer |

Azure Security Center を使用して、ご利用の Azure リソースのベースライン スキャンを実行します。  さらに、Azure Policy を使用して Azure リソース構成をアラート送信および監査します。

- [Azure Security Center の推奨事項を修復する方法](../../security-center/security-center-remediate-recommendations.md)

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10:オペレーティング システムの自動構成監視を実装する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 7.10 | 5.5 | Customer |

Azure Security Center を使用して、コンテナーの OS と Docker の設定のベースライン スキャンを実行します。

- [Azure Security Center のコンテナーの推奨事項を理解する](../../security-center/container-security.md)

## <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 7.11 | 13.1 | Customer |

マネージド サービス ID を Azure Key Vault と組み合わせて使用し、クラウド アプリケーションのシークレット管理を簡素化し、セキュリティで保護します。

- [Azure マネージド ID と統合する方法](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [キー コンテナーを作成する方法](../../key-vault/secrets/quick-create-portal.md)

- [Key Vault に対して認証を行う方法](../../key-vault/general/authentication.md)

- [Key Vault のアクセス ポリシーを割り当てる方法](../../key-vault/general/assign-access-policy-portal.md)

## <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 7.12 | 4.1 | Customer |

マネージド ID を使用して、Azure AD で自動的に管理される ID を Azure サービスに提供します。 マネージド ID を使用すると、コードに資格情報を追加しなくても、Azure AD の認証をサポートするさまざまなサービス (Key Vault を含む) に対して認証を行うことができます。

- [マネージド ID を構成する方法](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

| Azure ID | CIS IDs | 担当 |
|--|--|--|
| 7.13 | 18.1、18.7 | Customer |

コード内で資格情報を特定する資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。 

- [資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html)


## <a name="next-steps"></a>次のステップ

- 次のセキュリティ コントロールを参照してください。[マルウェアからの防御](security-control-malware-defense.md)