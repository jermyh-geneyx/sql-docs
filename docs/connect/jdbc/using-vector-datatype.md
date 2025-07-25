---
title: Using Vector data type
description: Learn about the vector data type in the JDBC driver and how it can be used to support various operations.
author: David-Engel
ms.author: davidengel
ms.date: 08/07/2025
ms.service: sql
ms.subservice: connectivity
ms.topic: conceptual
---
# Using Vector data type

[!INCLUDE[Driver_JDBC_Download](../../includes/driver_jdbc_download.md)]

Starting with version 13.2.0, the JDBC driver supports the vector data type. Vector is also supported when using features such as Table-Valued Parameters and BulkCopy, with some limitations. This page shows various use cases of the Vector data type with the JDBC Driver. For an overview of vector data types, see [Vector Data Type](../../t-sql/data-types/vector-data-type.md).

## Creating a vector object

The microsoft.sql.Vector class is a custom implementation designed to represent vector data in the JDBC driver. It provides a structured way to handle high-dimensional data, including serialization and deserialization, while ensuring compatibility with SQL Server's vector datatype.
The vector object should include the number of elements, type indicator, actual data.
```java
public enum VectorDimensionType {
        FLOAT32 // 32-bit (single precision) float
}

private VectorDimensionType vectorType;
private int dimensionCount; //No. of dimensions in the vector
private Object[] data; //A float[] array containing the vector's numerical values.
```
The Microsoft SQL Server JDBC driver offers two methods to initialize a vector object, providing flexibility for different use cases and input data.

### Creating a vector object using dimension count and vector type

public Vector(int dimensionCount, VectorDimensionType vectorType, Object[] data);

```java
Vector vector = new Vector(3, Vector.VectorDimensionType.FLOAT32, new Float[]{1.0f, 2.0f, 3.0f});
```

### Creating a vector object using precision and scale

public Vector(int precision, int scale, Object[] data);
where parameters are defined as below:
- precision : The number of dimensions in the vector.
- scale :  The scale value, it represents no. of bytes per dimension where 4 represents FLOAT32.
- data : A float[] array containing the vector's numerical values.

```java
Vector vector = new Vector(3, 4, new Float[]{1.0f, 2.0f, 3.0f});
```

## Populating and retrieving a table

Assuming one has a table with a vector column as:

```sql
CREATE TABLE sampleTable (vector_col vector(3))
```

A sample script to insert values using statement:

```java
try (Statement stmt = connection.createStatement()){
    stmt.execute("insert into sampleTable values ('[0.1, 2.0, 3.0]')");
}
```

Inserting value using prepared statement:

```java
try (PreparedStatement preparedStatement = con.prepareStatement("insert into sampleTable values (?)")) {
    preparedStatement..setObject(1, vector, microsoft.sql.Types.VECTOR);
    preparedStatement.execute();
}
```

Inserting null value using prepared statement:

```java
Vector vector = new Vector(1, Vector.VectorDimensionType.FLOAT32, null);
try (PreparedStatement preparedStatement = con.prepareStatement("insert into sampleTable values (?)")) {
    preparedStatement.setObject(1, vector, microsoft.sql.Types.VECTOR);
    preparedStatement.execute();
}
```

```java
try (PreparedStatement preparedStatement = con.prepareStatement("insert into sampleTable values (?)")) {
    preparedStatement.setObject(1, null)
    preparedStatement.execute();
}
```

Reading values from the table:

```java
try (SQLServerResultSet resultSet = (SQLServerResultSet) stmt.executeQuery("select * from sampleTable")) {
    assertTrue(resultSet.next(), "No result found for inserted vector.");
    ResultSetMetaData metaData = resultSet.getMetaData();
    int columnCount = metaData.getColumnCount();

    while (resultSet.next()) {
        for (int i = 1; i <= columnCount; i++) {
            String columnName = metaData.getColumnName(i);
            int columnType = metaData.getColumnType(i); // from java.sql.Types

            Object value = null;
            switch (columnType) {
                case Types.VARCHAR:
                case Types.NVARCHAR:
                    value = resultSet.getString(i);
                    break;
                case microsoft.sql.Types.VECTOR:
                    value = resultSet.getObject(i, microsoft.sql.Vector.class);
            }

            System.out.println(columnName + " = " + value + " (type: " + columnType + ")");
        }
    }
}
```

## Using stored procedures with vector

Having a stored procedure such as:

```java
String sql = "CREATE PROCEDURE " + inputProc + 
             " @p0 VECTOR(3) OUTPUT AS " + 
             " SELECT TOP 1 @p0 = col_vector FROM sampleTable ";
```

Output parameters must be registered:

```java
try (CallableStatement callableStatement = con.prepareCall("{call " + inputProc + " (?) }")) {
    callableStatement.registerOutParameter(1, microsoft.sql.Types.VECTOR, 3, 4);
    callableStatement.execute();
}
```

## Using TVP with vector

```java
SQLServerDataTable tvp = new SQLServerDataTable();
tvp.addColumnMetadata("vector_col", microsoft.sql.Types.VECTOR);
tvp.addRow(vector);

try (SQLServerPreparedStatement preparedStatement = (SQLServerPreparedStatement) con.prepareStatement( "insert into sampleTable select * from (?)")) {
    pstmt.setStructured(1, TVP_NAME, tvp);
    pstmt.execute();
}
```

## Using SQLServerBulkCopy from source table to destination table with vector

```java
try (Statement stmt = con.createStatement();
        SQLServerBulkCopy bulkCopy = new SQLServerBulkCopy(con)) {

        stmt.executeUpdate("create table destinationTable (vector_col vector(3))");

        bulkCopy.setDestinationTableName("destinationTable");
        bulkCopy.writeToServer(vector);  
    }
```

## Using bulkCopy from csv file to destination table with vector

Having a csv file such as:
```java
vector_col
"[1.0,2.0,3.0]"
"[4.0,5.0,6.0]"
```

```java
try (Statement stmt = con.createStatement();
    SQLServerBulkCopy bulkCopy = new SQLServerBulkCopy(con);
    SQLServerBulkCSVFileRecord fileRecord = new SQLServerBulkCSVFileRecord(fileName, null, ",", true)) {

        stmt.executeUpdate("create table destinationTable (vector_col vector(3))");

        // Add column metadata for the CSV file
        fileRecord.addColumnMetadata(1, "vector_col", microsoft.sql.Types.VECTOR, 3, 4);
        fileRecord.setEscapeColumnDelimitersCSV(true);

        bulkCopy.setDestinationTableName("destinationTable");
        bulkCopy.writeToServer(fileRecord);

}
```

## Backward Compatibility

If an application hasn't been updated to handle the VECTOR data type, the driver provides backward compatibility by allowing vector data types to be read using backward compatible types. This is controlled using the `vectorTypeSupport` connection string property.
Currently, the supported values are "off" (server sends vector types as JSON string data) and "v1" (server sends vector types of FLOAT32 as vector data). Default is "v1". Future values may be added as needed to support additional vector types. ("v2" may indicate additional support for FLOAT16 and/or INT32 vectors, for example).

## Limitations of vector

For detailed limitations, see [Vector Data Type - Limitations](../../t-sql/data-types/vector-data-type.md#limitations).

## See also

[Understanding the JDBC driver data types](understanding-the-jdbc-driver-data-types.md)
