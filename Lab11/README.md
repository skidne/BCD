# Laboratory Work No.11


### Objectives

#### Restoring a Database

### Tasks

1. Create a full backup file of the _universitatea_ database:

  __SQL Querry:__
  
  ```sql
EXEC sp_addumpdevice disk, dumpDev1, 'C:\Users\skdn\Desktop\UTM\Backup_lab11\exercitiul1.bak'
GO

BACKUP DATABASE universitatea
	TO dumpDev1 WITH FORMAT, NAME = N'universtitatea-Full Database Backup'
GO
  ```

2. Create a differential backup file of the _universitatea_ database:

    __SQL Querry:__
  
  ```sql
EXEC sp_addumpdevice disk, dumpDev2, 'C:\Users\skdn\Desktop\UTM\Backup_lab11\exercitiul2.bak'
GO

BACKUP DATABASE universitatea
	TO dumpDev2 WITH FORMAT, NAME = N'universtitatea-Differential Database Backup'
GO
  ```

3. Create a log backup file for the _universitatea_ database:

  __SQL Querry:__

  ```sql
EXEC sp_addumpdevice disk, dumpDev3, 'C:\Users\skdn\Desktop\UTM\Backup_lab11\exercitiul3.bak'
GO

BACKUP LOG universitatea TO dumpDev3
GO
  ```
 
4. Restore all the backup files created. The restore should be completed in a new database called _universitatea_lab11_:

  __SQL Querry:__
  
  ```sql
DROP DATABASE IF EXISTS universitatea_lab11;

RESTORE DATABASE universitatea_lab11
FROM DISK = 'C:\Users\skdn\Desktop\UTM\Backup_lab11\exercitiul1.bak'
WITH MOVE 'universitatea' TO 'C:\Users\skdn\Desktop\UTM\Backup_lab11\data.mdf',
MOVE 'universitatea_log' TO 'C:\Users\skdn\Desktop\UTM\Backup_lab11\log.ldf',
NORECOVERY
GO

RESTORE DATABASE universitatea_lab11
FROM DISK = 'C:\Users\skdn\Desktop\UTM\Backup_lab11\exercitiul2.bak'
WITH 
NORECOVERY
GO

RESTORE LOG universitatea_lab11
FROM DISK = 'C:\Users\skdn\Desktop\UTM\Backup_lab11\exercitiul3.bak'
WITH NORECOVERY
GO
  ```
