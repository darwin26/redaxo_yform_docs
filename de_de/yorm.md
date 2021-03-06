# Yorm ORM

- [Arbeiten ohne eigene Modal class](#ohne-modal-class)
- [Eigene Modal class verwenden](#eigene-modal-class)
- [Methoden](#methoden)
- [Query debuggen](#query-debuggen)
    
<a name="ohne-modal-class"></a>
## Arbeiten ohne eigene Modal class

Hole alle Daten der Tabelle `rex_my_table`

```
$items = rex_yform_manager_table::get('rex_my_table')->query()->find();
```



<a name="eigene-modal-class"></a>
## Eigene Modal class verwenden

### Klasse erstellen

Zunächst wird eine Klasse erstellt und in das `project` AddOn im Ordner `lib` abgelegt
```
<?php
class MyTable extends \rex_yform_manager_dataset
{
}
```

### Klasse registrieren
Jetzt muss die erstellte Klasse noch registiert werden. Dazu öffnet man die Datei `boot.php` des `project` AddOns und fügt nachfolgenden Code ein

```
rex_yform_manager_dataset::setModelClass('rex_my_table', MyTable::class);
```

Nun kann man alle Daten wie folgt holen

```
$items = MyTable::query()->find();
```

#### weitere Beispiele

```
$items = MyTable::query()
            ->alias('t')
            ->joinRelation('relation_id', 'r')
            ->select('r.name', 'relation_name')
            ->where('t.status', '1')
            ->orderBy('t.name')
            ->find();
```

<a name="methoden"></a>
## Methoden

### query Methoden

- Alias
    - alias
    - getTableAlias
- count
- exists
- Find
    - find
    - findId
    - findIds
    - findOne
- Get
    - get
    - getAll
- Group By
    - groupBy
    - groupByRaw
    - resetGroupBy
- Join
    - joinRaw
    - joinRelation
    - joinType
    - joinTypeRelation
    - leftJoin
    - leftJoinRelation
    - resetJoins
- Limit
    - limit
    - resetLimit
- Order By
    - orderBy
    - orderByRaw
    - resetOrderBy
- paginate
- Query
    - query
    - queryOne
- save
- Select
    - resetSelect
    - select
    - selectRaw
- Table
    - getTable
    - getTableName
- Where
    - resetWhere
    - setWhereOperator
    - where
    - whereNested
    - whereRaw

### dataset Methoden

- get
- getData
- getForm
- getId
- getMessages
- getRaw
- getRelatedCollection
- getRelatedDataset
- getTable
- getTableName
- getValue
- hasValue
- isValid
- loadData


<a name="query-debuggen"></a>
## Query debuggen

Temporär, wird gelöscht wenn besseres Handling vorhanden ist

### Variante A

Wichtig ist nur der Part mit `rex_sql`

```
$query = MyTable::query();
$query
    ->alias('t')
    ->joinRelation('relation_id', 'r')
    ->select('r.name', 'relation_name')
    ->where('t.status', '1')
    ->orderBy('t.name')
$items = rex_sql::factory()->setDebug()->getArray($query->getQuery(), $query->getParams());
$items = $query->find();
``` 

### Variante B

Datei `/redaxo/src/addons/yform/plugins/manager/lib/yform/manager/dataset.php` und die Variable `private static $debug = false;` auf `true` setzen

## Beispiele

```
<?php

$table = rex_yform_manager_table::get('rex_data_product');

$products = $table->query()
    ->joinRelation('category_id', 'c') // Join auf rex_data_product_category gemäß Relationsfeld category_id, mit Alias "c" für weitere Verwendung
    ->select('c.name', 'category_name') // Aus der gejointen Tabelle den Kategorienamen mit auslesen mit Alias "category_name"
    ->where('status', 1)
    ->find();

foreach ($products as $product) {
    echo $product->name;
    echo $product->category_name; // Value aus der gejointen Tabelle, siehe oben

    // Alternativ das komplette Objekt für die Kategorie auslesen
    $category = $product->getRelatedDataset('category_id');
    echo $category->name;
}
```
