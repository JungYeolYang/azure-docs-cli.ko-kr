---
title: Azure CLI 시작
description: 명령 기본 사항을 학습하여 Azure CLI 사용을 시작합니다.
keywords: Azure CLI, CLI 도움말, Azure 도움말, 쿼리, 자동화,
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 09/07/2018
ms.topic: conceptual
ms.prod: azure
ms.technology: azure-cli
ms.devlang: azurecli
ms.openlocfilehash: 003576ba22cdc4fc64977b653d0fb6859cd38446
ms.sourcegitcommit: 334a1da92a73e42e715e33470057f4194f10b2ea
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59429033"
---
# <a name="get-started-with-azure-cli"></a>Azure CLI 시작

Azure CLI 시작! CLI는 특히 자동화 기능을 통해 Azure 서비스를 빠르고 효율적으로 사용할 수 있도록 디자인된 도구입니다. 이 문서에서는 CLI의 여러 기능을 소개하고 생산성 향상에 도움이 되는 리소스에 대한 링크를 제공합니다.

## <a name="install-or-run-in-azure-cloud-shell"></a>Azure Cloud Shell에서 설치 또는 실행

Azure CLI를 시작하는 가장 쉬운 방법은 브라우저를 통해 Azure Cloud Shell 환경에서 실행하는 것입니다. Cloud Shell에 대해 자세히 알아보려면 [Azure Cloud Shell의 Bash에 대한 빠른 시작](/azure/cloud-shell/quickstart)을 참조하세요.

CLI를 설치할 준비가 되었으면 [설치 지침](install-azure-cli.md)을 참조하세요.

CLI가 처음으로 설치되면 `az --version`을 실행하여 CLI가 설치되어 있고 올바른 버전인지 확인합니다.

## <a name="sign-in"></a>로그인

로컬 설치에 CLI 명령을 사용하려면 먼저 [az login](/cli/azure/reference-index#az-login)으로 로그인해야 합니다.

[!INCLUDE [interactive-login](includes/interactive-login.md)]

로그인하면 Azure 계정에 연결된 구독 목록이 나타납니다. `isDefault: true`가 있는 구독 정보는 로그인한 후 현재 활성화된 구독입니다. 다른 구독을 선택하려면 전환할 구독 ID와 함께 [az account set](/cli/azure/account#az-account-set) 명령을 사용합니다. 구독 선택에 대한 자세한 내용은 [여러 Azure 구독 사용](manage-azure-subscriptions-azure-cli.md)을 참조하세요.

비 대화형으로 로그인할 수 있는 방법도 여러 가지가 있으며, 이는 [Azure CLI에 로그인](authenticate-azure-cli.md)에서 자세히 설명됩니다.

## <a name="common-commands"></a>일반적인 명령

이 표에는 CLI 및 해당 참조 설명서에 대한 링크에 사용되는 몇 가지 일반적인 명령이 나열되어 있습니다.

| 리소스 종류 | Azure CLI 명령 그룹 |
|---------------|-------------------------|
| [리소스 그룹](/azure/azure-resource-manager/resource-group-overview) | [az group](/cli/azure/group) |
| [가상 머신](/azure/virtual-machines) | [az vm](/cli/azure/vm) |
| [Storage 계정](/azure/storage/common/storage-introduction) | [az 저장소 계정](/cli/azure/storage/account) |
| [Key Vault](/azure/key-vault/key-vault-whatis) | [az keyvault](/cli/azure/keyvault) |
| [웹 애플리케이션](/azure/app-service) | [az webapp](/cli/azure/webapp) |
| [SQL 데이터베이스](/azure/sql-database) | [az sql server](/cli/azure/sql/server) |
| [CosmosDB](/azure/cosmos-db) | [az cosmosdb](/cli/azure/cosmosdb) |

## <a name="finding-commands"></a>명령 찾기

CLI에서 명령은 _그룹_ 의 _명령_ 으로 조직됩니다. 각 그룹은 Azure 서비스를 나타내며, 명령은 해당 서비스에서 작동합니다.

명령을 검색하려면 [az find](/cli/azure/reference-index#az-find)를 사용합니다. 예를 들어 이름에 `secret`이(가) 포함된 명령을 검색하려면 다음 명령을 사용합니다.

```azurecli-interactive
az find secret
```

`--help` 인수를 사용하여 명령 및 그룹의 하위 그룹의 전체 목록을 가져옵니다. 예를 들어, 네트워크 보안 그룹(NSG)을 사용하는 작업에 대한 CLI 명령을 찾으려면 다음을 수행합니다.

```azurecli-interactive
az network nsg --help
```

CLI에는 Bash 셸에서 명령에 대한 전체 탭 완성 기능이 포함됩니다.

## <a name="globally-available-arguments"></a>전역으로 사용 가능한 인수

일부 인수는 모든 명령에 사용할 수 있습니다.

* `--help`은(는) 명령 및 해당 인수에 대한 CLI 참조 정보를 출력하고 사용 가능한 하위 그룹 및 명령을 나열합니다.
* `--output`은(는) 출력 형식을 변경합니다. 사용 가능한 출력 형식은 `json`, `jsonc`(색이 지정된 JSON), `tsv`(탭으로 구분된 값), `table`(사람이 읽을 수 있는 ASCII 테이블) 및 `yaml`입니다. 기본적인 CLI 출력은 `json`입니다. 사용 가능한 출력 형식에 대해 자세히 알아보려면 [Azure CLI의 출력 형식](format-output-azure-cli.md)을 참조하십시오.
* `--query`은(는) [JMESPath 쿼리 언어](http://jmespath.org/)를 사용하여 Azure 서비스에서 반환되는 출력을 필터링합니다. 쿼리에 대한 자세한 내용은 [Azure CLI를 사용한 쿼리 명령 결과](query-azure-cli.md) 및 [JMESPath 자습서](http://jmespath.org/tutorial.html)를 참조하세요.
* `--verbose`은(는) 작업 중 Azure에서 생성되는 리소스에 대한 정보 및 기타 유용한 정보를 출력합니다.
* `--debug`은(는) 디버깅 목적으로 사용되는 CLI 작업에 대한 자세한 정보를 출력합니다. 버그가 발견된 경우, 버그 보고서를 제출할 때 `--debug` 플래그를 사용해서 생성된 출력을 제공하십시오.

## <a name="interactive-mode"></a>대화형 모드

CLI는 도움말 정보를 자동으로 표시하고 하위 명령을 더 쉽게 선택할 수 있게 해주는 대화형 모드를 제공합니다. 대화형 모드로 전환하려면 [az interactive](/cli/azure/reference-index#az-interactive) 명령을 사용합니다.

```azurecli-interactive
az interactive
```

대화형 모드에 대한 자세한 내용은 [Azure CLI 대화형 모드](interactive-azure-cli.md)를 참조하십시오.

또한 자동 완성 및 마우스를 위로 가져갈 때 표시되는 설명을 포함하여 대화형 환경을 제공하는 [Visual Studio Code 플러그인](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)도 있습니다.

## <a name="learn-cli-basics-with-quickstarts-and-tutorials"></a>퀵 스타트 및 자습서로 CLI 기본 내용 학습

Azure CLI를 시작하려면 가상 머신 설정 및 Azure 리소스 쿼리를 위한 CLI 기능 사용에 대한 세부 자습서를 참조하십시오.

> [!div class="nextstepaction"]
> [Azure CLI를 사용하여 가상 머신 만들기 자습서](azure-cli-vm-tutorial.yml)

다른 인기 있는 서비스에 대한 빠른 시작도 있습니다.

* [Azure CLI를 사용하여 저장소 계정 만들기](/azure/storage/common/storage-quickstart-create-storage-account-cli)
* [CLI를 사용하여 Azure Blob Storage에 대한 개체 전송](/azure/storage/blobs/storage-quickstart-blobs-cli)
* [Azure CLI를 사용하여 단일 Azure SQL 데이터베이스 만들기](/azure/sql-database/sql-database-get-started-cli)
* [Azure CLI를 사용하여 Azure Database for MySQL 서버 만들기](/azure/mysql/quickstart-create-mysql-server-database-using-azure-cli)
* [Azure CLI를 사용하여 PostgreSQL용 Azure Database 만들기](/azure/postgresql/quickstart-create-server-database-azure-cli)
* [Azure에서 Python 웹앱 만들기](/azure/app-service/app-service-web-get-started-python)
* [Azure Web Apps for Containers에서 사용자 지정 Docker Hub 이미지 실행](/azure/app-service/containers/quickstart-custom-docker-image)

## <a name="give-feedback"></a>피드백 제공

CLI 기능 개선 및 버그 해결을 위해 피드백을 보내주시기 바랍니다. [GitHub에서 문제를 제출](https://github.com/azure/azure-cli/issues)하거나 CLI의 기본 제공 기능을 사용하여 [az feedback](/cli/azure/reference-index#az-feedback) 명령을 통해 일반적인 피드백을 남길 수 있습니다.

```azurecli-interactive
az feedback
```
