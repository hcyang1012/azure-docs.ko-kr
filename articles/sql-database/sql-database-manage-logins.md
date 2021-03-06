---
title: 로그인 및 사용자 계정을 사용하여 서버 및 데이터베이스 액세스 권한 부여
description: Azure SQL Database 및 Azure Synapse Analytics가 로그인 및 사용자 계정을 사용하여 사용자가 액세스할 수 있는지 에 대해 알아봅니다. 또한 로그인 및 사용자가 작업을 수행하고 데이터를 쿼리할 수 있도록 권한을 부여하기 위해 역할 및 명시적 권한을 데이터베이스화하는 방법에 대해서도 알아봅니다.
keywords: sql 데이터베이스 보안,데이터베이스 보안 관리,로그인 보안,데이터베이스 보안,데이터베이스 액세스
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/23/2020
ms.openlocfilehash: 98c15fe11b64e8c177e60a2ea1eb7c50eaf69353
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124792"
---
# <a name="authorizing-database-access-to-authenticated-users-to-sql-database-and-azure-synapse-analytics-using-logins-and-user-accounts"></a>로그인 및 사용자 계정을 사용하여 인증된 사용자에 대한 데이터베이스 액세스 권한 부여 SQL Database 및 Azure Synapse Analytics

이 문서에서는 다음을 알아봅니다.

- 사용자가 관리 작업을 수행하고 이러한 데이터베이스에 저장된 데이터에 액세스할 수 있도록 Azure SQL Database 및 Azure Synapse 분석(이전의 Azure SQL Data Warehouse)을 구성하는 옵션입니다.
- 새 Azure SQL 데이터베이스를 처음 만든 후 액세스 및 권한 부여 구성
- 마스터 데이터베이스 및 사용자 계정에 로그인 및 사용자 계정을 추가한 다음 이러한 계정에 관리 권한을 부여하는 방법
- 로그인 또는 포함된 사용자 계정으로 연결된 사용자 데이터베이스에 사용자 계정을 추가하는 방법
- 데이터베이스 역할 및 명시적 사용 권한을 사용하여 사용자 데이터베이스의 사용 권한으로 사용자 계정 구성

> [!IMPORTANT]
> Azure SQL 데이터베이스 및 Azure Synapse 분석(이전의 Azure SQL Data Warehouse)의 데이터베이스는 이 문서의 나머지 부분에서 집합적으로 데이터베이스 또는 Azure SQL(단순성)으로 지칭됩니다.

## <a name="authentication-and-authorization"></a>인증 및 권한 부여

[**인증은**](sql-database-security-overview.md#authentication) 사용자가 주장하는 사람이라는 것을 증명하는 프로세스입니다. 사용자는 사용자 계정을 사용하여 데이터베이스에 연결합니다.
사용자가 데이터베이스에 연결하려고 하면 사용자 계정 및 인증 정보를 제공합니다. 사용자는 다음 두 가지 인증 방법 중 하나를 사용하여 인증됩니다.

- [SQL 인증](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication).

  이 인증 방법을 사용하면 사용자는 사용자 계정 이름과 연결된 암호를 제출하여 연결을 설정합니다. 이 암호는 로그인에 연결된 사용자 계정의 마스터 데이터베이스에 저장되거나 로그인에 연결되지 않은 사용자 계정에 대한 사용자 계정을 포함하는 데이터베이스에 저장됩니다.
- [Azure Active Directory 인증](sql-database-aad-authentication.md)

  이 인증 방법을 사용하면 사용자는 사용자 계정 이름을 제출하고 서비스가 Azure Active Directory에 저장된 자격 증명 정보를 사용하도록 요청합니다.

**로그인 및 사용자**: Azure SQL에서 데이터베이스의 사용자 계정은 마스터 데이터베이스에 저장되거나 개별 데이터베이스에 저장된 사용자 이름일 수 있는 로그인과 연결할 수 있습니다.

- **로그인은** 하나 이상의 데이터베이스에 있는 사용자 계정을 연결할 수 있는 마스터 데이터베이스의 개별 계정입니다. 로그인하면 사용자 계정의 자격 증명 정보가 로그인과 함께 저장됩니다.
- **사용자 계정은** 로그인에 연결할 필요는 없지만 모든 데이터베이스의 개별 계정입니다. 로그인에 연결되지 않은 사용자 계정의 경우 자격 증명 정보가 사용자 계정과 함께 저장됩니다.

데이터에 액세스하고 다양한 작업을 수행할 수 있는 [**권한은**](sql-database-security-overview.md#authorization) 데이터베이스 역할 및 명시적 권한을 사용하여 관리됩니다. 권한 부여는 사용자에게 할당된 권한을 나타내며 해당 사용자가 수행할 수 있는 작업을 결정합니다. 권한 부여는 사용자 계정의 데이터베이스 [역할 멤버 자격](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles) 및 개체 수준 권한에 의해 [제어됩니다.](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) 사용자에게 필요한 최소한의 권한을 부여하는 것이 가장 좋습니다.

## <a name="existing-logins-and-user-accounts-after-creating-a-new-database"></a>새 데이터베이스를 만든 후 기존 로그인 및 사용자 계정

첫 번째 Azure SQL 배포를 만들 때 해당 로그인에 대한 관리자 로그인 및 관련 암호를 지정합니다. 이 관리 계정을 **서버 관리자라고**합니다. 마스터 및 사용자 데이터베이스의 로그인 및 사용자의 다음 구성은 배포 중에 발생합니다.

- 관리 권한이 있는 SQL 로그인은 지정한 로그인 이름을 사용하여 만들어집니다. [로그인은](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login) SQL Database에 로그온하기 위한 개별 사용자 계정입니다.
- 이 로그인에는 [서버 수준 주체로서](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine)모든 데이터베이스에 대한 전체 관리 권한이 부여됩니다. 이 로그인에는 SQL Database 내에서 사용 가능한 모든 권한이 있으며 제한될 수 없습니다. 관리되는 인스턴스에서 이 로그인은 [sysadmin 고정 서버 역할에](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) 추가됩니다(이 역할은 단일 또는 풀린 데이터베이스와 함께 존재하지 않음).
- 각 [사용자](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions#database-users) `dbo` 데이터베이스에서 이 로그인에 대해 호출된 사용자 계정이 만들어집니다. [dbo](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine) 사용자는 데이터베이스에 있는 모든 데이터베이스 권한을 가지며 `db_owner` 고정 된 데이터베이스 역할에 매핑됩니다. 이 문서의 후반부에서는 추가 고정 데이터베이스 역할에 대해 설명합니다.

데이터베이스에 대한 관리자 계정을 식별하려면 Azure 포털을 열고 서버 또는 관리되는 인스턴스의 **속성** 탭으로 이동합니다.

![SQL Server 관리자](media/sql-database-manage-logins/sql-admins.png)

![SQL Server 관리자](media/sql-database-manage-logins/sql-admins2.png)

> [!IMPORTANT]
> 관리자 로그인 이름을 만든 후에는 변경할 수 없습니다. 논리 서버 관리자의 암호를 재설정하려면 Azure [포털로](https://portal.azure.com)이동하여 **SQL Server를 클릭하고**목록에서 서버를 선택한 다음 **암호 재설정**을 클릭합니다. 관리되는 인스턴스 서버의 암호를 재설정하려면 Azure 포털로 이동하여 인스턴스를 클릭한 다음 **암호 재설정**을 클릭합니다. PowerShell 또는 Azure CLI를 사용할 수도 있습니다.

## <a name="create-additional-logins-and-users-having-administrative-permissions"></a>관리 권한이 있는 추가 로그인 및 사용자 만들기

이 시점에서 Azure SQL 인스턴스는 단일 SQL 로그인 및 사용자 계정을 사용 하 여 액세스를 위해 구성 됩니다. 전체 또는 부분 관리 권한이 있는 추가 로그인을 만들려면 배포 모드에 따라 다음 옵션이 있습니다.

- **전체 관리 권한이 있는 Azure Active Directory 관리자 계정 만들기**

  Azure Active Directory 인증을 활성화하고 Azure AD 관리자 로그인을 만듭니다. 하나의 Azure Active Directory 계정은 전체 관리 권한을 가진 SQL Database 배포의 관리자로 구성할 수 있습니다. 이 계정은 개인 또는 보안 그룹 계정일 수 있습니다. Azure AD 계정을 사용하여 SQL 데이터베이스에 연결하려면 Azure AD 관리자를 **구성해야 합니다.** 모든 SQL Database 배포 유형에 대해 Azure AD 인증을 사용하도록 설정하는 방법에 대한 자세한 내용은 다음 문서를 참조하십시오.

  - [SQL에서 인증을 위해 Azure Active Directory 인증 사용](sql-database-aad-authentication.md)
  - [SQL을 사용하여 Azure Active Directory 인증 구성 및 관리](sql-database-aad-authentication-configure.md)

- **관리되는 인스턴스 배포에서 전체 관리 권한이 있는 SQL 로그인을 만듭니다.**

  - 관리되는 인스턴스에서 추가 SQL Server 로그인 만들기
  - ALTER SERVER ROLE 문을 사용하여 [시스템 관리자 고정 서버 역할에](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) 로그인을 [추가합니다.](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql) 이 로그인에는 전체 관리 권한이 있습니다.
  - 또는 <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a> 구문을 사용하여 [Azure AD 로그인을](sql-database-aad-authentication-configure.md?tabs=azure-powershell#new-azure-ad-admin-functionality-for-mi) 만듭니다.

- **단일 또는 풀풀 배포에서 관리 권한이 제한된 SQL 로그인을 만듭니다.**

  - 단일 또는 풀링된 데이터베이스 배포 또는 관리되는 인스턴스 배포에 대해 마스터 데이터베이스에 추가 SQL 로그인 만들기
  - 이 새 로그인과 연결된 마스터 데이터베이스에서 사용자 계정 만들기
  - [ALTER SERVER ROLE](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql) 문을 `loginmanager` 사용하여 `master` 데이터베이스의 역할 또는 둘 다에 `dbmanager`사용자 계정을 추가합니다(Azure Synapse Analytics의 경우 [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) 문을 사용하십시오).

  > [!NOTE]
  > `dbmanager`역할은 `loginmanager` 관리되는 인스턴스 배포와 관련이 **없습니다.**

  단일 또는 풀링된 데이터베이스에 대한 이러한 [특수 마스터 데이터베이스 역할의](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles#special-roles-for--and-) 구성원은 사용자가 데이터베이스를 만들고 관리하거나 로그인을 만들고 관리할 수 있는 권한을 갖습니다. `dbmanager` 역할의 구성원인 사용자가 만든 데이터베이스에서 구성원은 `db_owner` 고정 된 데이터베이스 역할에 매핑되며 `dbo` 사용자 계정을 사용하여 해당 데이터베이스에 로그인하고 관리할 수 있습니다. 이러한 역할에는 마스터 데이터베이스 외부에 명시적 권한이 없습니다.

  > [!IMPORTANT]
  > 단일 또는 풀풀된 데이터베이스에서 전체 관리 권한이 있는 추가 SQL 로그인을 만들 수 없습니다.

## <a name="create-accounts-for-non-administrator-users"></a>관리자가 아닌 사용자를 위한 계정 만들기

다음 두 가지 방법 중 하나를 사용하여 관리되지 않는 사용자에 대한 계정을 만들 수 있습니다.

- **로그인을 만듭니다.**

  마스터 데이터베이스에서 SQL 로그인을 만듭니다. 그런 다음 각 데이터베이스에 사용자 계정을 만들고 해당 사용자가 액세스해야 하는 경우 해당 로그인과 사용자 계정을 연결합니다. 이 방법은 사용자가 여러 데이터베이스에 액세스해야 하고 암호를 동기화된 상태로 유지하려는 경우에 선호됩니다. 그러나 이 방법은 기본 서버와 보조 서버 모두에서 로그인을 만들어야 하므로 지역 복제와 함께 사용할 때 복잡성이 있습니다. 자세한 내용은 [지리적 복원 또는 장애 조치(failover)에 대한 Azure SQL Database 보안 구성 및 관리를](sql-database-geo-replication-security-config.md)참조하십시오.
- **사용자 계정 만들기**

  데이터베이스에 사용자가 액세스해야 하는 사용자 계정을 만듭니다(포함된 [사용자라고도](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)함).

  - 단일 또는 풀이형 데이터베이스를 사용하면 언제든지 이러한 유형의 사용자 계정을 만들 수 있습니다.
  - [Azure AD 서버 주체를](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)지원하지 않는 관리되는 인스턴스 데이터베이스에서는 [포함된 데이터베이스에서만](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases)이러한 유형의 사용자 계정을 만들 수 있습니다. [Azure AD 서버 주체를](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)지원하는 관리 인스턴스를 사용하면 데이터베이스 사용자를 포함된 데이터베이스 사용자로 만들 필요 없이 관리되는 인스턴스를 인증하는 사용자 계정을 만들 수 있습니다.

  이 방법을 사용하면 사용자 인증 정보가 각 데이터베이스에 저장되고 지리적 복제 데이터베이스에 자동으로 복제됩니다. 그러나 동일한 계정이 여러 데이터베이스에 있고 SQL 인증을 사용하는 경우 암호를 수동으로 동기화된 상태로 유지해야 합니다. 또한 사용자가 다른 암호가 있는 다른 데이터베이스에 계정이 있는 경우 해당 암호를 기억하는 것이 문제가 될 수 있습니다.

> [!IMPORTANT]
> Azure AD ID에 매핑된 포함된 사용자를 만들려면 SQL 데이터베이스의 관리자인 Azure AD 계정을 사용하여 로그인해야 합니다. 관리되는 인스턴스에서 사용 권한이 `sysadmin` 있는 SQL 로그인은 Azure AD 로그인 또는 사용자를 만들 수도 있습니다.

로그인 및 사용자를 만드는 방법을 보여 주면 다음을 참조하십시오.

- [단일 또는 풀린 데이터베이스에 대한 로그인 만들기](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current#examples-1)
- [관리되는 인스턴스 데이터베이스에 대한 로그인 만들기](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current#examples-2)
- [Azure 시냅스 분석 데이터베이스에 대한 로그인 만들기](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest#examples-3)
- [사용자 만들기](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql#examples)
- [Azure AD 만들기 포함 된 사용자](sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)

> [!TIP]
> SQL Server를 단일 또는 풀풀된 데이터베이스에 포함된 사용자를 만드는 보안 자습서의 경우 [자습서: 단일 또는 풀풀된 데이터베이스 보안](sql-database-security-tutorial.md)을 참조하십시오.

## <a name="using-fixed-and-custom-database-roles"></a>고정 및 사용자 지정 데이터베이스 역할 사용

로그인또는 포함된 사용자로 데이터베이스에서 사용자 계정을 만든 후 해당 사용자에게 다양한 작업을 수행하고 특정 데이터베이스의 데이터에 액세스할 수 있는 권한을 부여할 수 있습니다. 다음 방법을 사용하여 액세스 권한을 부여할 수 있습니다.

- **고정된 데이터베이스 역할**

  [고정 데이터베이스 역할에](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles)사용자 계정을 추가합니다. 9개의 고정된 데이터베이스 역할이 있으며 각 역할은 정의된 사용 권한 집합을 가지고 있습니다. 가장 일반적인 고정 데이터베이스 역할은 **db_owner,** **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter**및 **db_denydatareader**. **db_owner**는 일반적으로 일부 사용자에게만 전체 권한을 부여하는 데 사용됩니다. 기타 고정된 데이터베이스 역할은 개발에서 단순한 데이터베이스를 신속하게 가져오는 데 유용하지만 대부분의 프로덕션 데이터베이스에는 권장되지 않습니다. 예를 들어 **db_datareader** 고정 된 데이터베이스 역할은 데이터베이스의 모든 테이블에 대한 읽기 액세스 권한을 부여합니다.

  - 고정 데이터베이스 역할에 사용자를 추가하려면 다음을 수행하십시오.

    - Azure SQL 데이터베이스에서 [ROLE 을 변경](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql) 합니다. 예: 역할 [변경 예제](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql#examples) 참조
    - Azure 시냅스 분석에서는 [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) 문을 사용합니다. 예제는 sp_addrolemember [예제를](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql)참조하십시오.

- **사용자 지정 데이터베이스 역할**

  [CREATE ROLE](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql) 문을 사용하여 사용자 지정 데이터베이스 역할을 만듭니다. 사용자 지정 역할을 사용하면 사용자 정의 데이터베이스 역할을 직접 만들고 각 역할에 비즈니스 필요에 필요한 최소한의 권한을 신중하게 부여할 수 있습니다. 그런 다음 사용자 지정 역할에 사용자를 추가할 수 있습니다. 사용자가 여러 역할의 멤버인 경우 모두에 대한 권한을 집계합니다.
- **직접 권한 부여**

  사용자 계정 권한을 직접 [부여합니다.](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) SQL Database에는 개별적으로 부여하거나 거부할 수 있는 100개가 넘는 사용 권한이 있습니다. 이러한 사용 권한은 대부분 중첩됩니다. 예를 들어 스키마에 대한 `UPDATE` 권한에는 해당 스키마 내에 있는 각 테이블에 대한 `UPDATE` 권한이 포함됩니다. 대부분의 사용 권한 시스템에서와 같이 사용 권한 거부는 권한 부여를 재정의합니다. 중첩된 특성과 사용 권한 수로 인해 데이터베이스를 제대로 보호할 적절한 사용 권한 시스템을 설계하는 데 신중을 기할 수 있습니다. [사용 권한(데이터베이스 엔진)](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine)에서 사용 권한 목록부터 시작하여 사용 권한의 [포스터 크기 그래픽](https://docs.microsoft.com/sql/relational-databases/security/media/database-engine-permissions.png)을 검토하세요.

## <a name="using-groups"></a>그룹 사용

효율적인 액세스 관리는 Active Directory 보안 그룹에 할당된 사용 권한을 사용하며 개별 사용자에게 할당된 역할 대신 고정 또는 사용자 지정 역할을 사용합니다.

- Azure Active Directory 인증을 사용하는 경우 Azure Active Directory 사용자를 Azure Active Directory 보안 그룹에 넣습니다. 해당 그룹에 대해 포함된 데이터베이스 사용자를 만듭니다. 하나 이상의 데이터베이스 사용자를 해당 사용자 그룹에 적합한 특정 사용 권한을 사용하여 사용자 지정 데이터베이스 역할에 배치합니다.

- SQL 인증을 사용하는 경우 데이터베이스에 포함된 데이터베이스 사용자를 만듭니다. 하나 이상의 데이터베이스 사용자를 해당 사용자 그룹에 적합한 특정 사용 권한을 사용하여 사용자 지정 데이터베이스 역할에 배치합니다.

  > [!NOTE]
  > 포함되지 않은 데이터베이스 사용자에 대해 그룹을 사용할 수도 있습니다.

사용 권한을 제한 또는 확장시키는 데 사용할 수 있는 다음 기능을 잘 알고 있어야 합니다.

- [가장](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) 및 [모듈 서명](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)은 일시적으로 권한을 안전하게 상승시키는 데 사용할 수 있습니다.
- [행 수준 보안](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) 은 사용자가 액세스할 수 있는 행을 제한하는 데 사용할 수 있습니다.
- [데이터 마스킹](sql-database-dynamic-data-masking-get-started.md) 은 중요한 데이터의 노출을 제한하는 데 사용할 수 있습니다.
- [저장 프로시저](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine)는 데이터베이스에서 수행할 수 있는 작업을 제한하는 데 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

모든 SQL Database 보안 기능에 대한 개요는 [SQL 보안 개요](sql-database-security-overview.md)를 참조하세요.
