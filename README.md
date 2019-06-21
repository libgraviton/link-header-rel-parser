# graviton/link-header-rel-parser

[![Build Status](https://travis-ci.org/libgraviton/link-header-rel-parser.svg?branch=master)](https://travis-ci.org/libgraviton/link-header-rel-parser) [![Latest Stable Version](https://poser.pugx.org/graviton/link-header-rel-parser/v/stable.svg)](https://packagist.org/packages/graviton/link-header-rel-parser) [![Total Downloads](https://poser.pugx.org/graviton/link-header-rel-parser/downloads.svg)](https://packagist.org/packages/graviton/link-header-rel-parser) [![License](https://poser.pugx.org/graviton/link-header-rel-parser/license.svg)](https://packagist.org/packages/graviton/link-header-rel-parser)

This is a small standalone PHP library that helps with parsing _and creating_ `Link` header contents with `rel=""` attributes
as it is used in the context of RESTful web services.

It is a tolerant parser, silently accepting invalid input. You can use the API functions to check if a given `rel` exists.

## Installation

The preferred way to install library is through [composer](http://getcomposer.org/download/).

Either run

```
composer require graviton/link-header-rel-parser
```

or add

```json
"graviton/link-header-rel-parser": "*",
```

to the require section of your `composer.json`.

## Examples

### Parsing headers

Parsing an existing header is easy, just pass the plain header _contents_ to the static function:

```php
$header = '<http://localhost/service/self>; rel="self", '<http://localhost/service/next>; rel="next"';
$linkHeader = LinkHeader::fromString($header);

$selfUrl = $linkHeader->getRel('self')->getUri(); // will output 'http://localhost/service/self'
```

If you are using Symfony, just fetch the Link header from the `Request` or `Response` object and pass it to the library. 


### Creating headers

You can create header contents programmatically:

```php
$header = new LinkHeader();
$header->add(new LinkHeaderItem('http://localhost?limit(10,10)', 'self'));
$header->add(new LinkHeaderItem('http://localhost?limit(10,30)', 'next'));
$header->add(new LinkHeaderItem('http://localhost?limit(10,0)', 'prev'));

echo (string) $header;
```

Will produce

```
<http://localhost?limit(10,10)>; rel="self", <http://localhost?limit(10,30)>; rel="next", <http://localhost?limit(10,0)>; rel="prev"
```

### Manipulating existing

Combining both methods above, you can also parse existing headers, manipulate them and render them.

```php
$header = '<http://localhost/service/self>; rel="self", '<http://localhost/service/next>; rel="next"';
$linkHeader = LinkHeader::fromString($header);

$linkHeader->add(new LinkHeaderItem('http://localhost?limit(10,10)', 'prev'));
$linkHeader->removeRel('next');
$header->getRel('self')->setUri('http://newhost');

echo (string) $header;
```

Will produce

```
<http://newhost>; rel="self", <http://localhost?limit(10,10)>; rel="prev"
```
