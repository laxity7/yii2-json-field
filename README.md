# Yii2 JsonFieldBehavior

[![License](https://img.shields.io/github/license/laxity7/yii2-json-field.svg)](https://github.com/laxity7/yii2-json-field/blob/master/LICENSE)
[![Latest Stable Version](https://img.shields.io/packagist/v/laxity7/yii2-json-field.svg)](https://packagist.org/packages/laxity7/yii2-json-field)
[![Total Downloads](https://img.shields.io/packagist/dt/laxity7/yii2-json-field.svg)](https://packagist.org/packages/laxity7/yii2-json-field)

This behavior adds advanced support for working with JSON data in Yii2 active record models.
Behavior convert array to JSON before save data in model, and also convert JSON to an array after saving and retrieving data.
Use JSON fields like normal fields with an array or object.

## Install

Install via composer 

```shell
composer require laxity7/yii2-json-field
```

## How to use

To use JsonFieldBehavior, insert the following code to your ActiveRecord class:

```php
/** @inheritdoc */
public function behaviors(): array
{
   return [
       [
           'class'  => \laxity7\yii2\behaviors\JsonFieldBehavior::class,
           'fields' => ['foo_data', 'bar_data'],
       ],
   ];
}
```

You can also pass the following parameters:
- **jsonOptions** `int` (by default `JSON_UNESCAPED_UNICODE`) [JSON constants](http://php.net/manual/en/json.constants.php) can be 
combined to form options for json_encode().
- **defaultValue** `array|string` (by default `'[]'`) The default value for attribute. 
This value by default will be stored in the database if the field value is empty. Ignored if [[skipEmpty]] is enabled.
- **skipEmpty** `bool` (by default `true`) Whether to skip a field if it's empty.
When TRUE in the database, the field can be null, when FALSE will save an empty object ('[]' or see defaultValue)
- **asArray** `bool` (by default `true`) Decode JSON into an array or object.

So, the complete list of settings will look like this:

```php
use laxity7\yii2\behaviors\JsonFieldBehavior;
use yii\db\ActiveRecord;

/**
* @property int $id
* @property array $foo_data
* @property array $bar_data
 */
class Foo extends ActiveRecord {
    /** @inheritdoc */
    public function behaviors(): array
    {
        return [
            [
                'class'        => JsonFieldBehavior::class,
                'fields'       => ['foo_data', 'bar_data'],
                'jsonOptions'  => JSON_UNESCAPED_SLASHES | JSON_UNESCAPED_UNICODE,
                'skipEmpty'    => false,
                'defaultValue' => ['foo' => 'bar'],
                'asArray'      => true,
            ],
        ];
    }
    
    // Based on these parameters may be approximately the code
    public function updateBar(int $id, array $barData): array
    {
        $model = self::findOne(['id' => $id]);
        $model->foo_data['foo'] = 'bar1';
        $model->bar_data['bar'] = array_merge($model->bar_data['bar'], $barData);
        $model->save();
    
        return $model->bar_data['bar'];
    }
}
```
