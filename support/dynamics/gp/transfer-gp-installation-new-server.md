---
title: Transfer GP installation to a new server
description: Discusses how to transfer your existing financial application to a new server that is running Microsoft SQL Server. Also discusses how to maintain the user logins and databases.
ms.reviewer: kyouells, 
ms.topic: how-to
ms.date: 03/31/2021
---
# How to transfer an existing Microsoft Dynamics GP, Small Business Financials, or Small Business Manager installation to a new server that is running SQL Server

This article describes how to transfer an existing Microsoft Dynamics GP installation to a new server that is running Microsoft SQL Server. The article also describes how to maintain the user logins and databases.

_Applies to:_ &nbsp; Microsoft Dynamics GP  
_Original KB number:_ &nbsp; 878449

## Introduction

> [!NOTE]
>
> - If you would like to move Business Portal for Microsoft Dynamics GP 10.0 to a new server, see [How to move Business Portal for Microsoft Dynamics GP 10.0 and for Microsoft Dynamics SL 7.0 from one server to another server](https://support.microsoft.com/help/967083).
> - If you currently use Web Services, there's no process to move Web Services to a new server. If Web Services isn't going to reside on the original IIS server, it must be removed from the original server with the **remove SQL Objects and Data** option marked. Once removed, web services can be re-installed on the new server before the Business Portal installation and all security must be re-configured.
> - If you use Workflow, there's currently no process to move Workflow to a new server. Workflow must be removed and re-installed.
> - We strongly recommend that you do the steps that are listed in the [Transfer instructions](#transfer-instructions) section in a test environment before you do the steps in a production environment.
> - The Microsoft SQL Server installation on the old server and on the new server can be Microsoft SQL Server 2000, Microsoft SQL Server Engine (also known as MSDE 2000), Microsoft SQL Server 2005, Microsoft SQL Server 2005 Express, Microsoft SQL Server 2008, Microsoft SQL Server 2008 R2, Microsoft SQL Server 2012, or Microsoft SQL Server 2014.
>
>     Microsoft SQL Server 2008 is only supported with Microsoft Dynamics GP 10.0 SP2 or a later version. Microsoft SQL Server 2008 R2 is only supported on Microsoft GP 10.0 SP5 or a later version.

## Transfer instructions

On the old server, copy the following Capture_Logins.sql script to the local hard disk. To obtain the Capture_Logins.sql script, see:

- **For SQL Server 2014 and later versions**: [Transfer logins and passwords between instances of SQL Server](/troubleshoot/sql/security/transfer-logins-passwords-between-instances)

> [!NOTE]
> If link doesn't work, copy and paste it to a new browser and try it again.

1. On the old server, run the Capture_Logins.sql script to capture all SQL Server logins and password information. All SQL Server logins that are used by the financial application, by Microsoft Business Solutions - FRx, by Personal Data Keeper, or by any other application that is using the SQL Server installation on the old server will be captured. Follow these steps, based on the SQL Server that tools you use:

    - If you use SQL Server Management Studio, follow these steps:
        1. Select **Start**, point to **All Programs**, point to **Microsoft SQL Server,** and then select **SQL Server Management Studio**.
        2. In the Connect to Server window, follow these steps:
            1. In the **Server name** box, type the name of the server that is running SQL Server.
            2. In the **Authentication** box, select **SQL Authentication**.
            3. In the **Login** box, type **sa**.
            4. In the **Password** box, type the password for the sa user, and then select **Connect**.
        3. Select **File**, point to **Open**, and then select **File**.
        4. In the **Look In** list, select the **Capture_Logins.sql** script that you copied to the local hard disk in step 1, and then select **Open**.
        5. In the Connect to Database Engine window, follow these steps:
            1. In the **Server Name** box, type the name of the old server that is running SQL Server.
            2. In the **Authentication box**, select **SQL Authentication**.
            3. In the **Login** box, type **sa**.
            4. In the **Password** box, type the password for the sa user, and then select **Connect**.
        6. Select **Query**, point to **Results to**, and then select **Results to File**.
        7. Select **Query**, and then select **Execute**.
        8. In the Save Results window, follow these steps:
            1. In the **Save in** list, select the location where you want to save the results of the script.
            2. In the **File name** box, type SQLLOGINS.sql, and then select **Save**.

    - If you use Query Analyzer, follow these steps:
        1. Select **Start**, point to **All Programs**, point to **Microsoft SQL Server**, and then select **Query Analyzer**.
        2. In the Connect to SQL Server window, follow these steps:
            1. In the **SQL Server** box, type the name of the old server that is running SQL Server.
            2. In the **Connect using** area, select **SQL Server Authentication**.
            3. In the **Login name** box, type **sa**.
            4. In the **Password** box, type the password for the sa user, and then select **OK**.
        3. Select **File**, and then select **Open**.
        4. In the Open Query File window, in the **Look In** list, select the Capture_Logins.sql script that you copied to the local hard disk of the old server in step 1, and then select **Open**.
        5. Select **Query**, and then select **Results to File**.
        6. Select **Query**, and then select **Execute**.
        7. In the Save Results window, follow these steps:
            1. In the **Save in** list, select the location where you want to save the results of the script.
            2. In the **File name** box, type **SQLLOGINS.sql**, and then select **Save**.

    - If you use MSDE 2000 and if no SQL Server tools are available, follow these steps:
        1. On the server that is running MSDE, select **Start**, select **Run**, type **cmd**, and then select **OK**.
        2. At the command prompt, type the following script, and then press **ENTER** to run the script. Replace **SERVERNAME** with the name of the server that is running MSDE 2000. Replace **SAPASSWORD** with the password for the sa user.

            ```sql
            OSQL –S SERVERNAME –U sa –P SAPASSWORD –i
            “C:\Capture_Logins.sql” –o “c:\SQLLOGINS.sql” -n -w 500-w 500
            ```

        3. Type **exit**, and then press **ENTER**.
        4. Right-click **Start**, and then select **Explore**.
        5. On drive C, open the *SQLLOGINS.sql* file to make sure that the script was created successfully.

2. Make a full backup of the DYNAMICS database and all company databases on the old server.

    > [!NOTE]
    > On the server that is running SQL Server, MSDE 2000, or SQL Server 2005 Express, start the financial application, select **File**, and then select **Backup** to make a backup of each database.

    You can also create a backup by using SQL Server Management Studio, Enterprise Manager, Query Analyzer, or the Support Administrator Console.

3. On the old server, generate a SQL script for each SQL Server Agent Job that is currently scheduled and for each SQL Server Agent Operator that is currently set up. Follow these steps, based on the SQL Server tools that you use.

    > [!NOTE]
    > These steps apply to SQL Server Standard, to SQL Server Enterprise, or to SQL Server Workgroup Edition.

    - If you use SQL Server Management Studio, follow these steps:

      1. Select **Start**, point to **All Programs**, point to **Microsoft SQL Server,** and then select **SQL Server Management Studio**.
      2. In the Connect to Server window, follow these steps:
          1. In the **Server name** box, type the name of the server that is running SQL Server.
          2. In the **Authentication** box, select **SQL Authentication**.
          3. In the **Login** box, type **sa**.
          4. In the **Password** box, type the password for the sa user, and then select **Connect**.
      3. In the **Object Explorer** pane, expand **SQL Server Agent**, and then expand **Jobs** to view all available jobs.

          > [!NOTE]
          > If the SQL Server Agent is not started, right-click **SQL Server Agent**, and then select **Start**.

      4. Right-click a job, point to **Script Job as**, point to **Create To**, and then select **File**.
      5. In the Select a File window, select the folder where you want to save the script, and then type a file name. Select **OK**.
      6. Repeat steps c through e for all jobs.
      7. In the **Object Explorer** pane, expand **SQL Server Agent**, and then expand **Operators** to view all Operators that are currently set up.
      8. Right-click an operator, point to **Script Operator as**, point to **Create To**, and then select **File**.
      9. In the Select a File Window, select the folder where you want to save the script, and then type a file name. Select **OK**.
      10. Repeat steps g through i for all operators.

    - If you use Enterprise Manager, follow these steps:

      1. Select **Start**, point to **All Programs**, point to **Microsoft SQL Server**, and then select **Enterprise Manager**.
      2. Expand **Microsoft SQL Servers**, expand **SQL Server Group**, and then expand the name of the server that is running SQL Server.
      3. Expand **Databases**, expand **Management**, and then expand **SQL Server Agent**.
      4. Right-click **Jobs**, point to **All Tasks**, and then select **Generate SQL Script**.
      5. In the Generate SQL Script window, select the folder where you want to save the script in the **Save In** list.
      6. In the **File name** box, type a name for the script, and then select **Save**.
      7. Select **OK** to generate the script.
      8. Right-click **Operators**, point to **All Tasks**, and then select **Generate SQL Script**.
      9. In the Generate SQL Script window, select the folder where you want to save the script in the **Save In** list.
      10. In the **File name** box, enter a name for the script, and then select **Save**.
      11. Select **OK** to generate the script.

4. In Windows Explorer, copy the SQLLOGINS.sql script that you created in step 2, the backup files that you created in step 3, and the SQL Server Agent Job and SQL Server Agent Operator scripts that you created in step 4 from the old server to the hard disk on the new server.

    > [!NOTE]
    > If you're using the same server, you don't have to complete this step.

5. Install SQL Server on the new server if it isn't already installed.

    Notes
    - Make sure that you use the same sort order that was used on the old server. To obtain the sort order that was used on the old server, run the following script against the master database in the SQL Server Management Studio, in Query Analyzer, or in the Support Administrator Console:

        ```sql
        sp_helpsort
        ```

        The following list shows the SQL Server sort orders that the financial applications support:

       - Column to verify: Server Collation Default  
      Column contents: Latin1-General, binary sort  
        Column meaning: Binary Sort Order 50
       - Column to verify: Server Collation Default  
      Column contents: Latin1-General, case-insensitive, accent-sensitive, kanatype-insensitive, width-insensitive for Unicode Data, SQL Server Sort Order 52 on Code Page 1252 for non-Unicode Data  
      Column meaning: Dictionary Order Case Insensitive (DOCI) Sort Order 52

    - If you're using the same server, install a new instance of SQL Server on the same computer. In the rest of this article, the term "new server" is used to refer to the new server that is running SQL Server or to the new instance of SQL Server on the old computer.
    - If you restore a database that was installed on the computer that is running SQL Server 7.0 or SQL Server 2000 and if you are moving the database to a computer that is running SQL Server 2005, you will have to update the database compatibility level for each database after the restore. To do it, follow these steps on the new server in SQL Server Management Studio:
      1. In the **Object Explorer** area, expand **Databases**, right-click the database, and then select **Options**.
      2. In the **Compatibility** box, select the **SQL Server 2005 (90)** check box.

6. On the new server, restore the DYNAMICS database from the backup file that you created in step 3. Follow these steps, based on the SQL Server tools that you use.

    > [!NOTE]
    > If you're using the same server, restore the databases on the new instance of SQL Server on the same computer.

    - If you use SQL Server Management Studio, follow these steps:
        1. Select **Start**, point to **All Programs**, point to **Microsoft SQL Server,** and then select **SQL Server Management Studio**.
        2. In the Connect to Server window, follow these steps:
          1. In the **Server Name** box, type the name of the new server that is running SQL Server.
          2. In the **Authentication** box, select **SQL Authentication**.
          3. In the **Login** box, type **sa**.
          4. In the **Password** box, type the password for the sa user, and then select **Connect**.
        3. In the **Object Explorer** area, right-click **Databases**, and then select **Restore Database**.
        4. In the **Destination for restore** area, type DYNAMICS in the **To database** box.
        5. In the **Source for restore** area, select **From Device**, and then select the ellipsis button to open the Specify Backup window.
        6. In the **Backup Media** list, select **File**, and then select **Add** to open the Locate Backup Files window.
        7. In the **Select the file** area, select the backup file for the DYNAMICS database that you backed up in step 3, select **OK**, and then select **OK**.
        8. In the **Select the backup sets to restore** area, select the **Restore** check box next to the backup that you want to restore.
        9. In the **Select a Page** area, select **Options**, and then select the **Overwrite the existing database** check box.
        10. In the **Restore the database files as** area, change the **Restore As** column so that the data file and the log file use the correct paths on the new server.

            > [!NOTE]
            > The default paths for SQL Server 2005 or later are the following.  
            > `%systemroot%\Program Files\Microsoft SQL Server\MSSQL.1\MSSQL\Data\_Data.mdf`  
            `%systemroot%\Program Files\Microsoft SQL Server\MSSQL.1\MSSQL\Data\_Log.ldf`
            You can find these files by using Windows Explorer.
        11. Select **OK**.

    - If you use Enterprise Manager, follow these steps:
      1. Select **Start**, point to **All Programs**, point to **Microsoft SQL Server**, and then select **Enterprise Manager**.
      2. Expand **Microsoft SQL Servers**, expand **SQL Server Group**, expand the name of the new server.
      3. Right-click **Databases**, point to **All Tasks**, and then select **Restore Database**.
      4. In the **Restore as database** box, type **DYNAMICS**.
      5. In the **Restore** area, select to check **From device**, and then select **Select Devices**.
      6. In the Choose Restore Devices window, select **Add**.
      7. In the Choose Restore Destination Window, select the **ellipsis** button, locate, and then select the backup file of the DYNAMICS database that you backed up in step 3, select **OK**, select **OK**, and then select **OK** again.
      8. Select the **Options** tab, and then select the **Force restore over existing database** check box.
      9. In the **Restore the database files as** area, change the **Restore As** column so that the data file and the log file use the correct paths on the new server.

          > [!NOTE]
          > The default paths for SQL Server 2000 are as follows:
          > `%systemroot%\Program Files\Microsoft SQL Server\MSSQL\Data\_Data.mdf`  
          `%systemroot%\Program Files\Microsoft SQL Server\MSSQL\Data\_Log.ldf`
          You can find these files by using Windows Explorer.
      10. Select **OK**.

    - If you use the Support Administrator Console, follow these steps:
      1. Select **Start**, point to **All Programs**, point to **Microsoft Support Administrator Console**, and then select **Support Administrator Console**.
      2. In the Connect to SQL Server window, follow these steps:
          1. In the **SQL Server** box, type the name of the new server.
          2. In the **Login Name** box, type sa.
          3. In the **Password** box, type the password for the sa user, and then select **OK**.
      3. Copy the following script to the New Query 1 window:

          ```console
          RESTORE DATABASE [TEST] 
            FROM  DISK = N'C:\Program Files\Dynamics\Backup\TEST.bak'
                      WITH  FILE = 1, NOUNLOAD, STATS = 10, RECOVERY, REPLACE,
                      MOVE N'GPSTESTDat.mdf' TO N'C:\Program Files\Microsoft SQL Server\MSSQL\Data\GPSTESTDat.mdf', 
                      MOVE N'GPSTESTLog.ldf' TO N'C:\Program Files\Microsoft SQL Server\MSSQL\Data\GPSTESTLog.ldf'
          ```

            > [!NOTE]
            > Make the following changes to the script to apply to your environment:
            >
            > - Replace **TEST** with the name of your company database on the new server.
            > - Replace `C:\Program Files\Dynamics\Backup\TEST.bak` with the correct path of the backup file.
            > - Replace **GPSTESTDat.mdf** with the correct name of the file.
            > - Replace **C:\Program Files\Microsoft SQL Server\MSSQL\Data\GPSTESTDat.mdf** with the correct path of the .mdf file for the database on the new server.
            > - Replace **GPSTESTLog.ldf** with the correct name of the file.
            > - Replace **C:\Program Files\Microsoft SQL Server\MSSQL\Data\GPSTESTLog.mdf** with the correct path of the .ldf file for the database on the new server.

      4. Select the green arrow to run the query.

7. Repeat step 7 for each company database.

    > [!NOTE]
    >  If you're restoring a Microsoft SQL Server 2000 database to Microsoft SQL Server 2005 or Microsoft SQL Server 2008, you must perform the following tasks:

      - Change the database compatibility level for each database. To do it in SQL Management Studio, right-click the database, select **Properties**, select **Options**, and then change the database compatibility to **SQL Server(90)**  for Microsoft SQL Server 2005, **SQL Server 2008 (100)** for Microsoft SQL Server 2008, SQL Server 2012 (110) for Microsoft SQL Server 2012, or SQL Server 2014 (120) for Microsoft SQL Server 2014.
      - Remove the schemas that have the Microsoft Dynamics GP user names that were created for each financial application user. The default schema is dbo, and the user schemas aren't needed.

8. Create an Open Database Connectivity (ODBC) connection at the new server and at all client workstations that use the financial application. For more information about how to set up an Open Database Connectivity connection on Microsoft SQL Server, see [How to set up an ODBC Data Source on SQL Server for Microsoft Dynamics GP](https://support.microsoft.com/help/870416).

9. On the new computer, install a Server and Client installation of the financial application. Then, install any third-party products or extra products that you use on the new server. Verify that the third-party and extra products are functional.

10. Run the SQLLOGINS.sql script that you created in step 2 to create all the SQL Server logins. You can use SQL Server Management Studio or Query Analyzer to run the script. If you use MSDE 2000, the SQLLOGINS.sql script must be run by using OSQL. See the instructions in step 2 for the process.

    > [!NOTE]
    > If the old server was running Microsoft Dynamics GP and doesn't have the same name as the new server, the passwords for the users will no longer be valid. To reset the password, follow these steps:
    >
    > 1. Sign  into Microsoft Dynamics GP as the sa user.
    > 2. On the **Tools** menu, point to **Setup**, point to **System**, and then select **User**.
    > 3. Select the **Lookup** button next to **User ID** and select the appropriate user.
    > 4. In the password field, enter a new password, and then select **Save**.

    > [!NOTE]
    > You must follow these steps if the old server was running Microsoft Business Solutions-Great Plains 7.5 or Microsoft Business Solutions - Great Plains 8.0 and the new server will be running Microsoft Dynamics GP 10.0 or Microsoft Dynamics GP 2010.0.

11. Run the scripts that you created in step 4 to create the SQL Server Agent jobs and the SQL Server Agent Operators on the new server. You can use SQL Server Management Studio or Query Analyzer to run the script.

    > [!NOTE]
    > If you use MSDE 2000, there is one additional script that you must run to create the PJOURNAL jobs and to truncate the PJOURNAL table every half hour. The script must be run by using OSQL. See the instructions in step 2 for the process.

12. Obtain the Dex_Req.sql script and the Grant.sql script, and then run the scripts.

    > [!NOTE]
    >
    > - Run the Dex_Req.sql script against the Master database. Run the Grant.sql script against the Dynamics database and against all company databases.
    > - You can use SQL Server Management Studio or Query Analyzer to run the scripts.
    > - If you use MSDE 2000, the Dex_Req.sql script must be run by using OSQL. See the instructions in step 2 for the process. You can run the Grant.sql script by using the Support Administrator Console.
    > - The Dex_Req.sql script creates the DEX_LOCK and DEX_SESSION tables in the tempdb database. The script also creates the smDEX_Build_Locks stored procedure in the master database. The Grant.sql script grants select, update, insert, and delete permissions to all tables, views, and stored procedures for all users in the DYNGRP database role. These are the permissions that you must have to use the financial application.

13. Run the following script against each financial application database to set the database owner to DYNSA.

    ```console
    sp_changedbowner 'DYNSA'
    ```

14. If the Reports and Forms dictionary files are shared on the old server, copy the files to the new server.

    > [!NOTE]
    > To verify whether the Reports and Forms dictionary files are shared, view the Dynamics.set file on a client workstation where the financial application is installed. To view the Dynamics.set file, right-click the Dynamics.set file, and then select **Edit** to open the file.

15. If the OLE Notes files are shared on the old server, copy the files to the new server.

    > [!NOTE]
    > To verify whether the OLE Notes files are shared, view the OLENotes path in the Dex.ini file on a client workstation where the financial application is installed. To view the Dex.ini file, double-click the Dex.ini file to open the file in Notepad.

16. If the Automatic Updates feature has been used and has entries that point to a share on the old server, the files must be copied to a share on the new server. The entries in the SYUPDATE table in the System DYNAMICS database needed to be adjusted. For more information, see Microsoft Knowledge Base article 916679.

17. If you're moving your Microsoft Dynamics GP 2010 databases and you use the drilldown functionality in the SQL Server Reporting Services or Excel-integrated reports you need to do the following to update your server links so the drilldowns work after the server move.

18.
    - Ensure that everyone has logged out of Microsoft Dynamics GP 2010 and close all instances of SQL Server Management Studio
    - On a machine where Dynamics GP 2010 is installed select **Start**, then point to **All Programs**. Select Microsoft Dynamics, then GP 2010 and select **Database Maintenance**
    - When the utility opens, select or enter the SQL Server instance where the Dynamics GP 2010 databases are stored. If you are logged in as a domain account with rights to this SQL Server instance, you can select that option. Otherwise select SQL Authentication and enter an appropriate user name and password. Then select **Next>>**
    - Select Mark All to choose each of the Dynamics GP 2010 databases and select **Next >>**
    - Select the Microsoft Dynamics GP product, then select **Next >>**
    - Select **Functions** and **Stored Procedures**, then select **Next >>**
    - Review the confirmation window, then select **Next >>** to begin the process. It can take some time, depending on the number of products installed and the number of databases that need to be addressed. Once it has completed your external report drilldowns will work in the new SQL Server instance, you've moved to.

## References

- For more information, see [Error message when you start Microsoft Dynamics GP: "Your login has been removed from the user activity file and you cannot be in the accounting system"](https://support.microsoft.com/help/916679).

- If you have any questions about the steps in this article, contact Microsoft Business Solutions Technical Support by using either of the following methods:
  - Sign into the following Microsoft Business Solutions Support site, and then enter a new support request:

    > [!NOTE]
    > For the subcategory, select **INSTALL-SQL**.

- Contact Microsoft Business Solutions Technical Support by telephone by calling 888-477-7877. Use Quick Access Code 6762 for MSDE or 6731 for SQL Server to reach Microsoft Business Solutions Technical Support.