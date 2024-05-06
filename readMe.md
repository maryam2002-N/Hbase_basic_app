# BASIC APP USING HBASE 
créer une application Java qui interagit avec une table HBase pour effectuer des opérations CRUD (Create, Read, Update, Delete). Vous êtes chargé de gérer une table Students pour une application de gestion scolaire. Cette table est destinée à stocker des informations sur les étudiants.

1-  Créer la table students avec les familles de colonnes suivantes :
- info : pour stocker les informations personnelles des étudiants.
- grades : pour stocker les notes des étudiants.
2- Ajoutez les deux étudiants suivants :
3- Récupérez et affichez  toutes les informations disponibles pour "student1".
4- Changez l'âge de "Jane Smith" à "23" et mettez à jour sa note de math à "A+".
5- Supprimez l'étudiant avec la Row Key "student1" de la table Students.
6- Affichez toutes les informations pour tous les étudiants.
## Creating the table :

````java
Connection connection = ConnectionFactory.createConnection(configuration);

Admin admin = connection.getAdmin();

TableName tableName = TableName.valueOf(TABLE_NAME);
TableDescriptorBuilder tableDescriptorBuilder = TableDescriptorBuilder.newBuilder(tableName);
tableDescriptorBuilder.setColumnFamily(ColumnFamilyDescriptorBuilder.of(CF_PERSONAL_DATA));
tableDescriptorBuilder.setColumnFamily(ColumnFamilyDescriptorBuilder.of(CF_PRO_DATA));

TableDescriptor tableDescriptor = tableDescriptorBuilder.build();

if (!admin.tableExists(tableName)) {
    admin.createTable(tableDescriptor);
    System.out.println("Table created !");
} else  {
    System.err.println("Already exist !");
}
````

## Inserting data :
````java
Table table = connection.getTable(tableName);
Put put = new Put(Bytes.toBytes("1"));
put.addColumn(Bytes.toBytes(CF_PERSONAL_DATA), Bytes.toBytes("name"), Bytes.toBytes("Maryam"));
put.addColumn(Bytes.toBytes(CF_PERSONAL_DATA), Bytes.toBytes("age"), Bytes.toBytes("21"));
put.addColumn(Bytes.toBytes(CF_PRO_DATA), Bytes.toBytes("dip"), Bytes.toBytes("BDCC ing"));
table.put(put);
System.out.println("Added !");
````

## Reading data :
````java
public static void display(Result result) {
    for(Cell cell: result.rawCells()) {
        byte[] cf = CellUtil.cloneFamily(cell);
        byte[] qualifier = CellUtil.cloneQualifier(cell);
        byte[] value = CellUtil.cloneValue(cell);

        String cfString = Bytes.toString(cf);
        String qualifierString = Bytes.toString(qualifier);
        String valueString = Bytes.toString(value);

        System.out.println("Column Family: " + cfString +
        ", Column: " + qualifierString +
        ", Value: " + valueString);
    }
}

Get get = new Get(Bytes.toBytes("1"));
Result result = table.get(get);

display(result);
````

## Updating data :
````java
put = new Put(Bytes.toBytes("1"));
put.addColumn(Bytes.toBytes(CF_PERSONAL_DATA), Bytes.toBytes("name"), Bytes.toBytes("Maryam"));
put.addColumn(Bytes.toBytes(CF_PERSONAL_DATA), Bytes.toBytes("age"), Bytes.toBytes("22"));
put.addColumn(Bytes.toBytes(CF_PRO_DATA), Bytes.toBytes("dip"), Bytes.toBytes("BDCC ing"));
table.put(put);

System.out.println("The record has been updated !");

get = new Get(Bytes.toBytes("1"));
result = table.get(get);

display(result);
````

## Deleting data :
````java
Delete delete = new Delete(Bytes.toBytes("1"));
table.delete(delete);

System.out.println("The record has been deleted !");
````

## Deleting the table :
````java
admin.disableTable(tableName);
admin.deleteTable(tableName);

System.out.println("The table has been deleted !");
````
