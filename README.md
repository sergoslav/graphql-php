# Build a Mutation/Query from php-array

This library can build a ready to use query/mutation string from php array

## Installation
```
composer require adeey/graphql-php
```
## Classes
```php
<?php

require 'vendor/autoload.php';

use 'MaxGraphQL\Query'; // For Query
use 'MaxGraphQL\Mutation'; // For Mutation
```

## Steps to use
Steps to use:
1. Create new class object ```$mutation = new Mutation('name');``` with mutation/query name
2. Add what you want to select ```$mutation->addSelect(['test', 'name']);```
3. Add arguments(filters) to your query ```$mutation->addArguments(['test' => 123]);```
4. Get the builded query ```$mutation->getPreparedQuery();```
5. Use the string in your request

## Where is arguments/select?
```graphql
query {
    HEREYOURNAME( argument: "ITS MY ARGUMENT" ) {
        HERE SELECT
    }
}
```
php code of example:
```php
$query = new Query('HEREYOURNAME');
$arguments = [
    'argument' => 'ITS MY ARGUMENT'
];
$select = [
    'HERE SELECT'
];
$query->addSelect($select);
$query->addArguments($arguments);
$query->getPreparedQuery(); // and here ours query
```

## How to build a query like this? Its pretty easy
```graphql
query {
    users(
        format: ALL,
        filter: {
            activeUsers: true,
            userIds: [1,2]
        }
    ) {
        id
        name
        code
        password
        channels {
            id
            titles {
                id
            }
        }
        ... on UserAdmin {
            userAdminLevel
        }
    }
}
```
## PHP
```php

$whatIWantToSelect = [
    'id',
    'name',
    'code',
    'password',
    'channels' => [
        'id',
        'titles' => [
            'id'
        ]
    ],
    '... on UserAdmin' => [
        'userAdminLevel'
    ]       
];

$filteringArguments = [
    'format' => 'ALL', // if you want write enum values you need to write it uppercase
    'filter' => [
        'activeUsers' => true,
        'userIds' => [1,2]
    ]
];

$query = new Query('users');
$query->addSelect($whatIWantToSelect);
$query->addArguments($filteringArguments);
echo $query->getPreparedQuery(); // returns query string
```
## The result of PHP code is string of query that equals my GraphQL query and its generated from PHP arrays:
```graphql
query{users(format:ALL,filter:{activeUsers:true,userIds:[1,2]}){id,name,code,password,channels{id,titles{id}},... on UserAdmin{userAdminLevel}}}
```

## How to build a mutation like this?
```graphql
mutation {
    updateUser(
        id: "321",
        data: {
            name: "Test",
            age: 32,
            admin: false
        }
    ) {
        id
        name
        code
        password
        channels {
            id
            titles {
                id
            }
        }
        ... on UserAdmin {
            userAdminLevel
        }
    }
}
```
## PHP
```php

$whatIWantToSelect = [
    'id',
    'name',
    'code',
    'password',
    'channels' => [
        'id',
        'titles' => [
            'id'
        ]
    ],
    '... on UserAdmin' => [
        'userAdminLevel'
    ]       
];

$mutationArguments = [
    'id' => '321', // look that id is in string format
    'data' => [
        'name' => "Test",
        'age' => 32, // and the age is int
        'admin' => false
    ]
];

$mutation = new Mutation('updateUser'); // users - name of mutation
$mutation->addSelect($whatIWantToSelect);
$mutation->addArguments($mutationArguments);

echo $mutation->getPreparedQuery(); // returns mutation string
```
## Result mutation in string
```graphql
mutation{updateUser(id:"321",data:{name:"Test",age:32,admin:false}){id,name,code,password,channels{id,titles{id}},... on UserAdmin{userAdminLevel}}}
```

## Additional cases:
Sometimes we want to add extra filter to query like this:
```graphql
query {
    users {
        all(pageSize: 25) {
            name
            ...
        }
    }
}
```
We need to write like that:
```php
$whatWeNeedToSelect = [
    'all(pageSize: 25)' => [
        'name',
        ...    
    ]
];
```
