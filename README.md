### Humus Doctrine Hydrator

A Hydrator implementing Zend\Stdlib\Hydrator\HydratorInterface, completely based on Doctrine\Common

- can return cloned object
- let's you control what field should be extracted (recursively)
- can flat single keys

## Requirements

 -  [ZendFramework 2.x](https://github.com/zendframework/zf2)
 -  [Doctrine\Common >=2.2](https://github.com/doctrine/common)
 -  [Doctrine\ORM >2.2](https://github.com/doctrine/doctrine2) or [Doctrine\ODM](https://github.com/doctrine/mongodb-odm)

## Installation

 1.  Add `"prolic/humus-doctrine-hydrator": "dev-master"` to your `composer.json`
 2.  Run `php composer.phar install`

## Example

Let's assume you want to hydrate an array to an object, and then you want to extract all it's internals

Example Class:
"User", has relationships to his "Locale", "Language" and "Address".
The address entity has also a relation to "Country".

```php
$hydrator = new \Humus\Doctrine\Hydrator\Hydrator(
    $context->get('EntityManager'), // the entity manager
    true, // yes, we want a cloned object, not the original one
    array(
        'id',
        'name',
        'firstname',
        'fax',
        'email',
        'gender',
        'locale' => array(
            'name'
        ),
        'preferred_contact_type' => array(
            'id'
        ),
        'address' => array(
            'street',
            'zip',
            'city',
            'country' => array(
                'id',
                'name'
            ),
        ),
        'spoken_languages' => array(
            'id',
        ),
    ), // we describe, what should be returned by extract, otherwise it could be that
       // a) We dump the Unit of Work
       // b) We dump half of the database
    false //do not flat single keys
);

// we got some data
$data = array(
    'id' => 5,
);

// we get the cloned address object,
// just switch the clone argument in constructor to false, in order to get the real object back
$address = $hydrator->hydrate($data, new \Application\Entity\Contact());

$result = $hydrator->extract($address);
var_dump($result);

// output:
array(6) {
  ["id"]=>
  int(5)
  ["gender"]=>
  string(4) "male"
  ["name"]=>
  string(20) "Sascha-Oliver Prolic"
  ["locale"]=>
  array(1) {
    ["name"]=>
    string(10) "Deutsch DE"
  }
  ["address"]=>
  array(4) {
    ["street"]=>
    string(13) "Sample Street"
    ["zip"]=>
    string(5) "00000"
    ["city"]=>
    string(6) "Berlin"
    ["country"]=>
    array(2) {
      ["id"]=>
      int(1)
      ["name"]=>
      string(7) "Germany"
    }
  }
  ["spoken_languages"]=>
  array(1) {
    [0]=>
    array(1) {
      ["id"]=>
      int(1)
    }
  }
}

// Please note how the output changes (watch for locale) when you switch the "flatSingleKeys" flag to true
// in the constructor of the hydrator

```
