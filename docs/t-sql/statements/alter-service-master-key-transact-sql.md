---
title: ALTER SERVICE MASTER KEY (Transact-SQL) | Microsoft Docs
ms.custom: 
ms.date: 03/14/2017
ms.prod: sql-non-specified
ms.prod_service: sql-database
ms.service: 
ms.component: t-sql|statements
ms.reviewer: 
ms.suite: sql
ms.technology:
- database-engine
ms.tgt_pltfrm: 
ms.topic: language-reference
f1_keywords:
- ALTER_SERVICE_MASTER_KEY_TSQL
- ALTER SERVICE MASTER KEY
dev_langs:
- TSQL
helpviewer_keywords:
- REGENERATE phrase
- FORCE option
- ALTER SERVICE MASTER KEY statement
- cryptography [SQL Server], Service Master Key
- modifying Service Master Key
- decryption [SQL Server], Service Master Key
- encryption [SQL Server], Service Master Key
- service master key [SQL Server], modifying
ms.assetid: a1e9be0e-4115-47d8-9d3a-3316d876a35e
caps.latest.revision: 41
author: edmacauley
ms.author: edmaca
manager: craigg
ms.workload: On Demand
ms.translationtype: MT
ms.sourcegitcommit: 876522142756bca05416a1afff3cf10467f4c7f1
ms.openlocfilehash: 4ee9fd8a888ed796b4d01b007aec5f8217cce5d9
ms.contentlocale: pt-br
ms.lasthandoff: 09/01/2017

---
# <a name="alter-service-master-key-transact-sql"></a>ALTER SERVICE MASTER KEY (Transact-SQL)
[!INCLUDE[tsql-appliesto-ss2008-xxxx-xxxx-xxx-md](../../includes/tsql-appliesto-ss2008-xxxx-xxxx-xxx-md.md)]

  Altera a chave mestra de serviço de uma instância do [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)].  
  
 ![Ícone de link do tópico](../../database-engine/configure-windows/media/topic-link.gif "Topic link icon") [Convenções da sintaxe Transact-SQL](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>Sintaxe  
  
```  
  
ALTER SERVICE MASTER KEY   
    [ { <regenerate_option> | <recover_option> } ] [;]  
  
<regenerate_option> ::=  
    [ FORCE ] REGENERATE  
  
<recover_option> ::=  
    { WITH OLD_ACCOUNT = 'account_name' , OLD_PASSWORD = 'password' }  
    |      
    { WITH NEW_ACCOUNT = 'account_name' , NEW_PASSWORD = 'password' }  
```  
  
## <a name="arguments"></a>Argumentos  
 FORCE  
 Indica que a chave mestra de serviço deve ser regenerada, mesmo com o risco de perda de dados. Para obter mais informações, consulte [alterar a conta de serviço do SQL Server](#_changing) mais adiante neste tópico.  
  
 REGENERE  
 Indica que a chave mestra de serviço deve ser gerada novamente.  
  
 OLD_ACCOUNT **='***account_name***'**  
 Especifica o nome da antiga conta de serviço do Windows.  
  
> [!WARNING]  
>  Essa opção está obsoleta. Não use. Use o [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Configuration Manager.  
  
 OLD_PASSWORD **='***senha***'**  
 Especifica o nome da antiga senha de serviço do Windows.  
  
> [!WARNING]  
>  Essa opção está obsoleta. Não use. Use o [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Configuration Manager.  
  
 NEW_ACCOUNT **='***account_name***'**  
 Especifica o nome da nova conta de serviço do Windows.  
  
> [!WARNING]  
>  Essa opção está obsoleta. Não use. Use o [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Configuration Manager.  
  
 Nova_senha **='***senha***'**  
 Especifica o nome da nova senha de serviço do Windows.  
  
> [!WARNING]  
>  Essa opção está obsoleta. Não use. Use o [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Configuration Manager.  
  
## <a name="remarks"></a>Comentários  
 A chave mestra de serviço é gerada automaticamente na primeira vez que é necessária para criptografar a senha, as credenciais ou a chave mestra de banco de dados de um servidor vinculado. A chave mestra de serviço que usa a chave da máquina local ou a API de Proteção de Dados do Windows é criptografada. Essa API usa uma chave que é derivada das credenciais do Windows da conta de serviço do [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)].  
  
 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] usa o algoritmo de criptografia AES para proteger a SMK (chave mestra de serviço) e a DMK (chave mestra de banco de dados). O AES é um algoritmo de criptografia mais novo que o 3DES usado em versões anteriores. Depois de atualizar uma instância do [!INCLUDE[ssDE](../../includes/ssde-md.md)] para [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] , o SMK e o DMK devem ser regenerados para atualizar as chave mestras para AES. Para obter mais informações sobre como regenerar a DMK, veja [ALTER MASTER KEY &#40;Transact-SQL&#41;](../../t-sql/statements/alter-master-key-transact-sql.md).  
  
##  <a name="_changing"></a>Alterar a conta de serviço do SQL Server  
 Para alterar a conta de serviço do [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Configuration Manager, use o [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Configuration Manager. Para gerenciar uma alteração da conta de serviço, [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] armazena uma cópia redundante da chave mestra de serviço protegida pela conta da máquina que tem as permissões necessárias para o [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] grupo do serviço. Se o computador for recriado, o mesmo usuário de domínio que foi usado anteriormente pela conta de serviço poderá recuperar a chave mestra de serviço. Isso não funciona com contas locais ou o sistema Local, serviço Local ou serviço de rede. Quando você está movendo [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] para outro computador, migre a chave mestra de serviço usando backup e restauração.  
  
 A frase REGENERATE gera a chave mestra de serviço novamente. Quando a chave mestra de serviço é gerada novamente, o [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] descriptografa todas as chaves que foram criptografadas com ela e, em seguida, as criptografa com a nova chave mestra de serviço. Essa operação utiliza muitos recursos. É recomendável agendar essa operação em um período de baixa demanda, a menos que a chave esteja comprometida. Se qualquer uma das descriptografias falhar, a instrução inteira falhará.  
  
 A opção FORCE faz com que a o processo de gerar novamente a chave continue mesmo que não possa recuperar a chave mestra atual ou não possa descriptografar todas as chaves particulares criptografadas com ela. Use FORCE somente se a nova geração falhar e você não pode restaurar a chave mestra de serviço usando o [RESTORE SERVICE MASTER KEY](../../t-sql/statements/restore-service-master-key-transact-sql.md) instrução.  
  
> [!CAUTION]  
>  A chave mestra de serviço é a raiz da hierarquia de criptografia do [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] . Ela protege todas as outras chaves e segredos diretamente ou indiretamente na árvore. Se uma chave dependente não puder ser descriptografada durante uma nova geração forçada, os dados que ela protege serão perdidos.  
  
 Se você mover a SQL para outro computador, deverá usar a mesma conta de serviço para descriptografar o SMK; o SQL Server corrigirá a criptografia da conta de computador automaticamente.  
  
## <a name="permissions"></a>Permissões  
 Exige a permissão CONTROL SERVER no servidor.  
  
## <a name="examples"></a>Exemplos  
 O exemplo a seguir gera a chave mestra de serviço novamente.  
  
```  
ALTER SERVICE MASTER KEY REGENERATE;  
GO  
```  
  
## <a name="see-also"></a>Consulte também  
 [Restauração da chave master de serviço &#40; Transact-SQL &#41;](../../t-sql/statements/restore-service-master-key-transact-sql.md)   
 [Backup da chave master de serviço &#40; Transact-SQL &#41;](../../t-sql/statements/backup-service-master-key-transact-sql.md)   
 [Hierarquia de encriptação](../../relational-databases/security/encryption/encryption-hierarchy.md)  
  
  

