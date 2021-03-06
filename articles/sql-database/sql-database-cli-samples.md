---
title: Azure CLI 스크립트 예제
description: Azure SQL Database 서버, 탄력적 풀, 데이터베이스 및 방화벽을 만들고 관리하는 Azure CLI 스크립트 예제입니다.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples, mvc
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/03/2019
ms.openlocfilehash: 459a5ea69e11a8614c572f68fce039b6cabbb1ed
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80061710"
---
# <a name="azure-cli-samples-for-azure-sql-database"></a>Azure SQL Database에 대한 Azure CLI 샘플

<a href="/cli/azure">Azure CLI</a>를 사용하여 Azure SQL Database를 구성할 수 있습니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 항목에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

# <a name="single-database--elastic-pools"></a>[단일 데이터베이스 및 탄력적 풀](#tab/single-database)

| | |
|---|---|
|**단일 데이터베이스 및 탄력적 풀 만들기**||
| [단일 데이터베이스 만들기 및 방화벽 규칙 구성](scripts/sql-database-create-and-configure-database-cli.md) | Azure SQL 데이터베이스를 만들고 서버 수준 방화벽 규칙을 구성합니다. |
| [탄력적 풀 만들기 및 풀링된 데이터베이스 이동](scripts/sql-database-move-database-between-pools-cli.md) | SQL 탄력적 풀을 만들고, 풀링된 Azure SQL 데이터베이스를 이동하고, 컴퓨팅 크기를 변경합니다. |
|**단일 데이터베이스 및 탄력적 풀 크기 조정**||
| [단일 데이터베이스 크기 조정](scripts/sql-database-monitor-and-scale-database-cli.md) | 데이터베이스의 크기 정보를 쿼리한 후 Azure SQL 데이터베이스를 다른 컴퓨팅 크기로 조정합니다. |
| [탄력적 풀 크기 조정](scripts/sql-database-scale-pool-cli.md) | SQL 탄력적 풀을 다른 컴퓨팅 크기로 조정합니다. |
|**지역에서 복제 및 장애 조치(failover) 구성**||
| [장애 조치(failover) 그룹에 단일 데이터베이스 추가](scripts/sql-database-add-single-db-to-failover-group-cli.md)| 데이터베이스 및 장애 조치(failover) 그룹을 만들고, 장애 조치(failover) 그룹에 데이터베이스를 추가한 다음, 보조 서버에 장애 조치(failover)를 테스트합니다. |
| [탄력적 풀에 대한 장애 조치(failover) 그룹 구성](scripts/sql-database-add-elastic-pool-to-failover-group-cli.md) | 데이터베이스를 만들어서 탄력적 풀에 추가하고, 탄력적 풀을 장애 조치(failover) 그룹에 추가한 다음, 보조 서버에 장애 조치(failover)를 테스트합니다. |
| [활성 지역 복제를 사용하여 단일 데이터베이스 구성 및 장애 조치(Failover)](scripts/sql-database-setup-geodr-and-failover-database-cli.md)| Azure SQL 데이터베이스에 대해 활성 지역 복제를 구성하고 보조 복제본에 장애 조치합니다. |
| [활성 지역 복제를 사용하여 풀링된 데이터베이스 구성 및 장애 조치(Failover)](scripts/sql-database-setup-geodr-and-failover-pool-cli.md)| SQL 탄력적 풀에서 Azure SQL 데이터베이스에 대해 활성 지역 복제를 구성한 다음, 보조 복제본에 장애 조치합니다. |
| **감사 및 위협 감지** |
| [감사 및 위협 감지 구성](scripts/sql-database-auditing-and-threat-detection-cli.md)| Azure SQL 데이터베이스에 대한 감사 및 위협 감지 정책을 구성합니다. |
| **데이터베이스 백업, 복원, 복사 및 가져오기**||
| [데이터베이스 백업](scripts/sql-database-backup-database-cli.md)| Azure SQL 데이터베이스를 Azure 스토리지 백업에 백업합니다. |
| [데이터베이스 복원](scripts/sql-database-restore-database-cli.md)| 지역 중복 백업에서 Azure SQL 데이터베이스를 복원하고 삭제된 Azure SQL 데이터베이스를 최신 백업으로 복원합니다. |
| [새 서버에 데이터베이스 복사](scripts/sql-database-copy-database-to-new-server-cli.md) | 새 Azure SQL Server에 기존 Azure SQL 데이터베이스의 복사본을 만듭니다. |
| [bacpac 파일에서 데이터베이스 가져오기](scripts/sql-database-import-from-bacpac-cli.md)| *.bacpac* 파일에서 Azure SQL Server로 데이터베이스를 가져옵니다. |
|||

[단일 데이터베이스 Azure CLI API](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)에 대해 자세히 알아봅니다.

# <a name="managed-instance"></a>[Managed Instance](#tab/managed-instance)

다음 표에서는 Azure SQL Database - Managed Instance의 Azure CLI 스크립트 예제에 대한 링크를 포함합니다.

| | |
|---|---|
| **Managed Instance 만들기**||
| [관리형 인스턴스 만들기](scripts/sql-database-create-configure-managed-instance-cli.md)| 관리형 인스턴스를 만듭니다. |
| **TDE(투명한 데이터 암호화) 구성**||
| [Azure Key Vault를 사용하여 Managed Instance에서 투명한 데이터 암호화 관리](scripts/transparent-data-encryption-byok-sql-managed-instance-cli.md)| 다양한 주요 시나리오에서 Azure Key Vault를 사용하여 Azure SQL Managed Instance에서 TDE(투명한 데이터 암호화)를 구성합니다. |
|**장애 조치(failover) 그룹 구성**||
| [관리형 인스턴스에 대한 장애 조치(failover) 그룹 구성](scripts/sql-database-add-managed-instance-to-failover-group-cli.md) | 관리형 인스턴스 두 개를 만들어서 장애 조치(failover) 그룹에 추가한 다음, 기본 관리형 인스턴스에서 보조 관리형 인스턴스로 장애 조치(failover)를 테스트합니다. |
|||

Managed Instance 예제를 더 보려면 [만들기](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/), [업데이트](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/), [데이터베이스 이동](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/), [작업](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) 스크립트를 참조하세요.

[관리형 인스턴스 Azure CLI API](sql-database-managed-instance-create-manage.md#azure-cli-create-and-manage-managed-instances)에 대해 자세히 알아봅니다.

---
