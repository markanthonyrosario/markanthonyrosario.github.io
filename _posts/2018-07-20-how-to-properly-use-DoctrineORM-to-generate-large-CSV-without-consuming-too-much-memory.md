---
layout: post
title: How to properly use Doctrine ORM to generate a large CSV download without consuming too much memory
categories: [Programming]
tags: [php, doctrineORM, symfony]
description: lowering memory usage from 1.8GB to 2MB
---

I will share how I fixed a report that generates 400k rows and consumes 646MB-1.8GB memory 
from doctrine and csv download operations.

The original code — this uses 646 MB for loading the data from the database alone, 
and can further increase to up to 1.8GB memory usage when downloaded via CSV.
Memory Usage is very high because we load the entire results from the database into the memory!

```
<?php
require_once "bootstrap.php";
$productRepository = $entityManager->getRepository('Product');
$products = $productRepository->findAll();
foreach ($products as $product) {
    echo '#' . $product->getId();
    echo ' ';
    echo $product->getName();
    echo ' (' . $product->getOwnerName() . ')';
    echo " - ". getMemoryUsage();
    echo "\n";
}
// with 400k rows this consumes a constant 646.01 MB
// this is because doctrine loads all the results into the memory before we iterate on it.

```

The final modified code now only uses a constant 6 MB to load data from the database 
because by using Doctrine Query::iterate and unbuffered Mysql queries, 
we are now able to traverse the results and load them into memory one by one.

We have also converted the results from Object to Array, 
because when using unbuffered queries we lose Lazy loading, 
because Doctrine won’t be able to execute queries while an unbuffered query is active, 
so instead of lazy loading, we manually create the needed left joins and select statements.

Please also note that the more lazy loaded associations your doctrine entities has, 
the more memory it will use during lazy loading.

```
<?php
require_once "bootstrap.php";
$pdo = $entityManager->getConnection()->getWrappedConnection();
$pdo->setAttribute(PDO::MYSQL_ATTR_USE_BUFFERED_QUERY, false);
$queryBuilder = $entityManager->createQueryBuilder();
$queryBuilder->select('product.id,product.name as productName,owner.name as ownerName')
    ->from(Product::class, 'product')
    ->leftJoin(ProductOwner::class,'owner','WITH','owner.id = product.productOwner');
    ;
$query = $queryBuilder->getQuery();
$query->setMaxResults(405000);
$iterableResult = $query->iterate(null,\Doctrine\ORM\Query::HYDRATE_ARRAY);
foreach ($iterableResult as $row) {
    $product = array_pop($row);
    echo '#' . $product['id'];
    echo $product['productName'];    echo ' ';
    echo ' (' . $product['ownerName'] . ')';
    echo " - ". getMemoryUsage();
    echo "\n";
}
// with 400k rows this consumes a constant 6 MB
// this is because doctrine loads the results into the memory one by one and we do not use objects
```

Using the code above, along with a normal Response, we could finally generate a CSV, 
however memory usage grows from 2MB up to 25MB (for 400k rows) 
because we are building the CSV file in the memory

```
<?php
require_once "bootstrap.php";
$pdo = $entityManager->getConnection()->getWrappedConnection();
$pdo->setAttribute(PDO::MYSQL_ATTR_USE_BUFFERED_QUERY, false);
$queryBuilder = $entityManager->createQueryBuilder();
$queryBuilder->select('product.id,product.name as productName,owner.name as ownerName')
    ->from(Product::class, 'product')
    ->leftJoin(ProductOwner::class,'owner','WITH','owner.id = product.productOwner');
;
$query = $queryBuilder->getQuery();
$query->setMaxResults(405000);
$iterableResult = $query->iterate(null,\Doctrine\ORM\Query::HYDRATE_ARRAY);
$csv = '';
$csv .= "\n\n initial memory usage: ". getMemoryUsage();
foreach ($iterableResult as $row) {
    $product = array_pop($row);
    $csv .= '#' . $product['id'];
    $csv .= ' ';
    $csv .= $product['productName'];
    $csv .= ' (' . $product['ownerName'] . ')';
    $csv .= " - ". getMemoryUsage();
    $csv .= "\n";
}
$csv .= "\n\n final memory usage: ". getMemoryUsage();
$response = new \Symfony\Component\HttpFoundation\Response($csv);
$response->headers->set('Content-Type', 'text/csv');
$response->headers->set('Content-Disposition', 'attachment; filename="export_from_doctrine_demo.csv"');
$response->send();
// with 400k rows this consumes a constant 2mb - 25mb
// this is because we are building the $csv content in the memory
// this is not actually bad, but could be dangerous if our report has more rows and columns
```

Using the same previous code above, along with a streamed response, 
we could finally generate the CSV download using only 2 MB of memory
```
<?php
require_once "bootstrap.php";
$pdo = $entityManager->getConnection()->getWrappedConnection();
$pdo->setAttribute(PDO::MYSQL_ATTR_USE_BUFFERED_QUERY, false);
$queryBuilder = $entityManager->createQueryBuilder();
$queryBuilder->select('product.id,product.name as productName,owner.name as ownerName')
    ->from(Product::class, 'product')
    ->leftJoin(ProductOwner::class,'owner','WITH','owner.id = product.productOwner');
;
$query = $queryBuilder->getQuery();
$query->setMaxResults(405000);
$iterableResult = $query->iterate(null,\Doctrine\ORM\Query::HYDRATE_ARRAY);
$response = new \Symfony\Component\HttpFoundation\StreamedResponse(function() use ($iterableResult) {
    $csv = '';
    $csv .= "\n\n initial memory usage: ". getMemoryUsage();
    echo $csv; $csv = '';
    foreach ($iterableResult as $row) {
        $product = array_pop($row);
        $csv .= '#' . $product['id'];
        $csv .= ' ';
        $csv .= $product['productName'];
        $csv .= ' (' . $product['ownerName'] . ')';
        $csv .= " - ". getMemoryUsage();
        $csv .= "\n";
        echo $csv; $csv = '';
    }
    $csv .= "\n\n final memory usage: ". getMemoryUsage();
    echo $csv; $csv = '';
});
$response->headers->set('Content-Type', 'text/csv');
$response->headers->set('Content-Disposition', 'attachment; filename="export_from_doctrine_demo.csv"');
$response->send();
// with 400k rows this consumes a constant 2mb
// this is because we are streaming the data directly into the user's browsers instead of our php server's memory
```

The complete code samples with more steps that show the step by step changes to lower memory usage ,which you can execute, so you could test for yourself, 
has been uploaded to https://github.com/markanthonyrosario/doctrine_csv_export_demo

this has also been posted at https://medium.com/@mark.anthony.r.rosario/how-to-properly-use-doctrine-orm-to-generate-a-large-csv-download-without-consuming-too-much-memory-1edeeab10407



