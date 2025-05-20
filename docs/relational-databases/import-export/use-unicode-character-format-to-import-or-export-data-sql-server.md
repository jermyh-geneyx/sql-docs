---
title: "Use Unicode Character Format to Import & Export Data"
description: The Unicode character data format allows data to be exported from a SQL Server instance by using a code page that differs from the code page used by the client.
author: rwestMSFT
ms.author: randolphwest
ms.reviewer: wiassaf
ms.date: 05/19/2025
ms.service: sql
ms.subservice: data-movement
ms.topic: concept-article
helpviewer_keywords:
  - "data formats [SQL Server], Unicode character"
  - "Unicode [SQL Server], bulk importing and exporting"
monikerRange: ">=aps-pdw-2016 || =azuresqldb-current || =azure-sqldw-latest || >=sql-server-2016 || >=sql-server-linux-2017 || =azuresqldb-mi-current"
---
# Use unicode character format to import or export data (SQL Server)

[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

Unicode character format is recommended for bulk transfer of data between multiple instances of [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] by using a data file that contains extended/DBCS characters. The Unicode character data format allows data to be exported from a server by using a code page that differs from the code page used by the client that is performing the operation. In such cases, use of Unicode character format has the following advantages:  

- If the source and destination data are Unicode data types, use of Unicode character format preserves all of the character data.  

- If the source and destination data are not Unicode data types, use of Unicode character format minimizes the loss of extended characters in the source data that cannot be represented at the destination.

<a id="considerations"></a>

## Considerations for using unicode character format

When using Unicode character format, consider the following:  

- By default, the [bcp utility](../../tools/bcp-utility.md) separates the character-data fields with the tab character and terminates the records with the newline character. For information about how to specify alternative terminators, see [Specify field and row terminators (SQL Server)](specify-field-and-row-terminators-sql-server.md).

- The [sql_variant](../../t-sql/data-types/sql-variant-transact-sql.md) data that is stored in a Unicode character-format data file operates in the same way it operates in a character-format data file, except that the data is stored as [nchar](../../t-sql/data-types/nchar-and-nvarchar-transact-sql.md) instead of [char](../../t-sql/data-types/char-and-varchar-transact-sql.md) data. For more information about character format, see [Collation and Unicode support](../collations/collation-and-unicode-support.md).  

<a id="special_considerations"></a>

## Special Considerations for Using Unicode Character Format, bcp, and a Format File

Unicode character format data files follow the conventions for Unicode files. The first two bytes of the file are hexadecimal numbers, `0xFFFE`. These bytes serve as byte-order marks (BOM), specifying whether the high-order byte is stored first or last in the file. The [bcp Utility](../../tools/bcp-utility.md) might misinterpret the BOM and cause part of your import process to fail. You might receive an error message similar as follows:

```output
Starting copy...
SQLState = 22005, NativeError = 0
Error = [Microsoft][ODBC Driver 13 for SQL Server]Invalid character value for cast specification
```

The BOM might be misinterpreted under the following conditions:

- The [bcp Utility](../../tools/bcp-utility.md) is used and the `-w` switch is used to indicate Unicode character

- A format file is used

- The first field in the data file is non-character

Consider whether any of the following workarounds are available for your *specific* situation:

- Don't use a format file. An example of this workaround is provided below, see [Using bcp and Unicode Character Format to Import Data without a Format File](#bcp_widechar_import).

- Use the `-c` switch instead of `-w`.

- Re-export the data using a native format.

- Use [BULK INSERT](../../t-sql/statements/bulk-insert-transact-sql.md) or [OPENROWSET BULK](../../t-sql/functions/openrowset-bulk-transact-sql.md). For more information, see [Using BULK INSERT and Unicode Character Format with a Non-XML Format File](#bulk_widechar_fmt) and [Using OPENROWSET and Unicode Character Format with a Non-XML Format File](#openrowset_widechar_fmt).

- Manually insert first record in destination table and then use `-F 2` switch to have import start on second record.

- Manually insert dummy first record in data file and then use `-F 2` switch to have import start on second record. An example of this workaround is provided below, see [Using bcp and Unicode Character Format to Import Data with a Non-XML Format File](#bcp_widechar_import_fmt).

- Use a staging table where the first column is a character data type.

- Re-export the data and change the data field order so that the first data field will be character. Then use a format file to remap the data field to the actual order in the table. For an example, see [Use a format file to map table columns to data-file fields (SQL Server)](use-a-format-file-to-map-table-columns-to-data-file-fields-sql-server.md).

<a id="command_options"></a>

## Command options for unicode character format

You can import Unicode character format data into a table using [bcp](../../tools/bcp-utility.md), [BULK INSERT](../../t-sql/statements/bulk-insert-transact-sql.md), or [OPENROWSET BULK](../../t-sql/functions/openrowset-bulk-transact-sql.md). For a [bcp](../../tools/bcp-utility.md) command or [BULK INSERT](../../t-sql/statements/bulk-insert-transact-sql.md) statement, you can specify the data format in the statement. For an [OPENROWSET BULK](../../t-sql/functions/openrowset-bulk-transact-sql.md) statement, you must specify the data format in a format file.  

Unicode character format is supported by the following command options:  

|Command|Option|Description|  
|-------------|------------|-----------------|  
| `bcp` |`-w`|Uses the Unicode character format.|  
| `BULK INSERT` |`DATAFILETYPE ='widechar'`|Uses Unicode character format when bulk importing data.|  
| `OPENROWSET` |N/A|Must use a format file.|

> [!NOTE]
>  Alternatively, you can specify formatting on a per-field basis in a format file. For more information, see [Format files to import or export data (SQL Server)](format-files-for-importing-or-exporting-data-sql-server.md).

<a id="etc"></a>

## Example test conditions

<a id="sample_table"></a>

The examples are based on the sample `myWidechar` table and format file. The script creates a test database, a table named `myWidechar` and populates the table with some initial values. Execute the following Transact-SQL in Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS):

```sql
CREATE DATABASE TestDatabase;
GO

USE TestDatabase;
CREATE TABLE dbo.myWidechar ( 
    PersonID smallint NOT NULL,
    FirstName nvarchar(25) NOT NULL,
    LastName nvarchar(30) NOT NULL,
    BirthDate date,
    AnnualSalary money
);

-- Populate table
INSERT TestDatabase.dbo.myWidechar
VALUES 
(1, N'ϴAnthony', N'Grosse', '02-23-1980', 65000.00),
(2, N'❤Alica', N'Fatnowna', '11-14-1963', 45000.00),
(3, N'☎Stella', N'Rossenhain', '03-02-1992', 120000.00);

-- Review Data
SELECT * FROM TestDatabase.dbo.myWidechar;
```

<a id="nonxml_format_file"></a>

### Sample Non-XML Format File

SQL Server support two types of format file: non-XML format and XML format. The non-XML format is the original format that is supported by earlier versions of SQL Server. For more information, see [Use Non-XML format files (SQL Server)](non-xml-format-files-sql-server.md). 

- The command uses the [bcp utility](../../tools/bcp-utility.md) to generate a non-xml format file, `myWidechar.fmt`, based on the schema of `myWidechar`. 
- To use a [bcp](../../tools/bcp-utility.md) command to create a format file, specify the `format` argument and use `nul` instead of a data-file path. 
- The format option also requires the `-f` option. In addition, for this example, the qualifier `c` is used to specify character data, and `T` is used to specify a trusted connection using integrated security. 

At a command prompt, enter the following commands:

```cmd
bcp TestDatabase.dbo.myWidechar format nul -f D:\BCP\myWidechar.fmt -T -w

REM Review file
Notepad D:\BCP\myWidechar.fmt
```

> [!IMPORTANT]
> Ensure your non-XML format file ends with a carriage return\line feed. Otherwise you will likely receive the following error message:
> 
> `SQLState = S1000, NativeError = 0`  
> `Error = [Microsoft][ODBC Driver 13 for SQL Server]I/O error while reading BCP format file`

<a id="examples"></a>

## Examples

The examples use [the database and format files created in this article](#example-test-conditions).

<a id="bcp_widechar_export"></a>

<a id="using-bcp-and-unicode-character-format-to-export-data"></a>

### Use bcp and Unicode Character Format to Export Data

Use the `-w` switch and `OUT` command. 

The data file `myWidechar.bcp` created in this example will be used in subsequent examples. 

Change the location of the code sample from `D:\BCP\myWidechar.bcp` to a file location on your machine. 

At a command prompt, enter the following commands:

```cmd
bcp TestDatabase.dbo.myWidechar OUT D:\BCP\myWidechar.bcp -T -w

REM Review results
NOTEPAD D:\BCP\myWidechar.bcp
```

<a id="bcp_widechar_import"></a>

<a id="using-bcp-and-unicode-character-format-to-import-data-without-a-format-file"></a>

### Use bcp and Unicode Character Format to Import Data without a Format File

The `-w` switch and `IN` command. 

Change the location of the code sample from `D:\BCP\myWidechar.bcp` to a file location on your machine. 

At a command prompt, enter the following commands:

```cmd
REM Truncate table (for testing)
SQLCMD -Q "TRUNCATE TABLE TestDatabase.dbo.myWidechar;"

REM Import data
bcp TestDatabase.dbo.myWidechar IN D:\BCP\myWidechar.bcp -T -w

REM Review results is SSMS
```

<a id="bcp_widechar_import_fmt"></a>

<a id="using-bcp-and-unicode-character-format-to-import-data-with-a-non-xml-format-file"></a>

### Use bcp and unicode character format to import data with a non-XML format file

The `-w` and `-f` switches, and the `IN` command.

[A workaround will need to be used](#special_considerations) since this example involves bcp, a format file, Unicode character, and the first data field in the data file is non-character. The data file `myWidechar.bcp` will be altered by adding an additional record as a "dummy" record which will then be skipped with the `-F 2` switch.

At a command prompt, enter the following commands and follow the modification steps:

```cmd
REM Truncate table (for testing)
SQLCMD -Q "TRUNCATE TABLE TestDatabase.dbo.myWidechar;"

REM Open data file
Notepad D:\BCP\myWidechar.bcp
REM Copy first record and then paste as new first record.  This additional record is the "dummy" record.
REM Close file.

REM Import data instructing bcp to skip dummy record with the -F 2 switch.
bcp TestDatabase.dbo.myWidechar IN D:\BCP\myWidechar.bcp -f D:\BCP\myWidechar.fmt -T -F 2

REM Review results is SSMS

REM Return data file to original state for usage in other examples
bcp TestDatabase.dbo.myWidechar OUT D:\BCP\myWidechar.bcp -T -w
```

<a id="bulk_widechar"></a>

<a id="using-bulk-insert-and-unicode-character-format-without-a-format-file"></a>

### Use BULK INSERT and unicode character format without a format file
The `DATAFILETYPE` argument. 

Execute the following Transact-SQL in Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS):

```sql
TRUNCATE TABLE TestDatabase.dbo.myWidechar; -- for testing
BULK INSERT TestDatabase.dbo.myWidechar
    FROM 'D:\BCP\myWidechar.bcp'
    WITH (
        DATAFILETYPE = 'widechar'
        );

-- review results
SELECT * FROM TestDatabase.dbo.myWidechar;
```

<a id="bulk_widechar_fmt"></a>

<a id="using-bulk-insert-and-unicode-character-format-with-a-non-xml-format-file"></a>

### Use BULK INSERT and unicode character format with a non-XML format file
The `FORMATFILE` argument. 

Execute the following Transact-SQL in Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS):

```sql
TRUNCATE TABLE TestDatabase.dbo.myWidechar; -- for testing
BULK INSERT TestDatabase.dbo.myWidechar
   FROM 'D:\BCP\myWidechar.bcp'
   WITH (
        FORMATFILE = 'D:\BCP\myWidechar.fmt'
        );

-- review results
SELECT * FROM TestDatabase.dbo.myWidechar;
```

<a id="openrowset_widechar_fmt"></a>

<a id="using-openrowset-and-unicode-character-format-with-a-non-xml-format-file"></a>

### Use OPENROWSET and unicode character format with a non-XML format file
The `FORMATFILE` argument. 

Execute the following Transact-SQL in Microsoft [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (SSMS):

```sql
TRUNCATE TABLE TestDatabase.dbo.myWidechar;  -- for testing
INSERT INTO TestDatabase.dbo.myWidechar
    SELECT *
    FROM OPENROWSET (
        BULK 'D:\BCP\myWidechar.bcp', 
        FORMATFILE = 'D:\BCP\myWidechar.fmt'  
        ) AS t1;

-- review results
SELECT * FROM TestDatabase.dbo.myWidechar;
```

<a id="RelatedTasks"></a>

## Related Tasks

To use data formats for bulk import or bulk export:

-   [Import native and character format data from earlier versions of SQL Server](import-native-and-character-format-data-from-earlier-versions-of-sql-server.md)  

-   [Use character format to import or export data (SQL Server)](use-character-format-to-import-or-export-data-sql-server.md)  

-   [Use native format to import or export data (SQL Server)](use-native-format-to-import-or-export-data-sql-server.md)  

-   [Use Unicode Native Format to Import or Export Data (SQL Server)](use-unicode-native-format-to-import-or-export-data-sql-server.md)  

## Related content

- [bcp Utility](../../tools/bcp-utility.md)
- [BULK INSERT (Transact-SQL)](../../t-sql/statements/bulk-insert-transact-sql.md)
- [OPENROWSET (Transact-SQL)](../../t-sql/functions/openrowset-transact-sql.md)
- [Data types (Transact-SQL)](../../t-sql/data-types/data-types-transact-sql.md)
- [Collation and Unicode support](../collations/collation-and-unicode-support.md)
