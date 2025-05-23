[![Latest Stable Version](http://poser.pugx.org/sujanshresthanet/php-xml-sitemap-generator/v)](https://packagist.org/packages/sujanshresthanet/php-xml-sitemap-generator) [![Total Downloads](http://poser.pugx.org/sujanshresthanet/php-xml-sitemap-generator/downloads)](https://packagist.org/packages/sujanshresthanet/php-xml-sitemap-generator) [![Latest Unstable Version](http://poser.pugx.org/sujanshresthanet/php-xml-sitemap-generator/v/unstable)](https://packagist.org/packages/sujanshresthanet/php-xml-sitemap-generator) [![License](http://poser.pugx.org/sujanshresthanet/php-xml-sitemap-generator/license)](https://packagist.org/packages/sujanshresthanet/php-xml-sitemap-generator) [![PHP Version Require](http://poser.pugx.org/sujanshresthanet/php-xml-sitemap-generator/require/php)](https://packagist.org/packages/sujanshresthanet/php-xml-sitemap-generator) [![Dependents](http://poser.pugx.org/sujanshresthanet/php-xml-sitemap-generator/dependents)](https://packagist.org/packages/sujanshresthanet/php-xml-sitemap-generator)

# PHP Sitemap

PHP XML Sitemap Generator.

## Installation via Composer

Add this to composer.json require block.

``` json
{
    "require": {
        "sujanshresthanet/php-xml-sitemap-generator": "2.*"
    }
}
```

## Getting Started

Create a sitemap object:

``` php
use SujanShrestha\Sitemap\Sitemap;

$sitemap = new Sitemap;
```

Add items to sitemap:

``` php
$sitemap->addUrlItem($url);
$sitemap->addUrlItem($url);
$sitemap->addUrlItem($url);
```

You can add some optional params.

``` php
use SujanShrestha\Sitemap\ChangeFreq;

$sitemap->addUrlItem($url, '1.0', ChangeFreq::DAILY, '2015-06-07 10:51:20');
$sitemap->addUrlItem($url, '0.7', ChangeFreq::WEEKLY, new \DateTime('2015-06-03 11:24:20'));
```

The arguments are `loc`, `priority`, `changefreq` and `lastmod`. See this table:

| Params | Required | Description |
| ------ | -------- | ----------- |
| `loc`   | required | URL of the page. This URL must begin with the protocol (such as http) and end with a trailing slash, if your web server requires it. This value must be less than 2,048 characters. |
| `priority` | optional | The date of last modification of the file. This date should be in [W3C Datetime format](http://www.w3.org/TR/NOTE-datetime). This format allows you to omit the time portion, if desired, and use YYYY-MM-DD. |
| `changefreq` | optional | How frequently the page is likely to change. This value provides general information to search engines and may not correlate exactly to how often they crawl the page. |
| `lastmod` | optional | The priority of this URL relative to other URLs on your site. Valid values range from 0.0 to 1.0. This value does not affect how your pages are compared to pages on other sites—it only lets the search engines know which pages you deem most important for the crawlers. |

See: http://www.sitemaps.org/protocol.html#xmlTagDefinitions

### Render it to XML:

``` php
echo $sitemap->toString();

// OR

(string) $sitemap;
```

This is an example to send it as real sitemap for Google or other search engine:

``` php
header('Content-Type: application/xml');

echo $sitemap;

exit();
```

Output:

``` xml
<?xml version="1.0" encoding="utf-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
	<url>
		<loc>http://sitemap.io</loc>
	</url>
	<url>
		<loc>http://sitemap.io/foo/bar/?flower=sakura&amp;fly=bird</loc>
		<changefreq>daily</changefreq>
		<priority>1.0</priority>
		<lastmod>2015-06-07T10:51:20+02:00</lastmod>
	</url>
</urlset>
```

## Arguments

### loc

The URL will be auto escaped. For example, the `&`, `>` will convert to `&amp;`, `&gt;`.

If you want to escape it yourself, set auto escape off:

``` php
$sitemap->setAutoEscape(false);
```

See: http://www.sitemaps.org/protocol.html#escaping

### changefreq

Valid values are:

``` php
ChangeFreq::ALWAYS;
ChangeFreq::HOURLY;
ChangeFreq::DAILY;
ChangeFreq::WEEKLY;
ChangeFreq::MONTHLY;
ChangeFreq::YEARLY;
ChangeFreq::NEVER;
```

The value `always` should be used to describe documents that change each time they are accessed.

The value `never` should be used to describe archived URLs.

Please note that the value of this tag is considered a hint and not a command. Even though search engine crawlers may consider this information when making decisions,
they may crawl pages marked `hourly` less frequently than that, and they may crawl pages marked `yearly` more frequently than that.

Crawlers may periodically crawl pages marked `never` so that they can handle unexpected changes to those pages.

### priority

The default priority of a page is `0.5`.
Please note that the priority you assign to a page is not likely to influence the position of your URLs in a search engine's result pages. Search engines may use this information when selecting between URLs on the same site, so you can use this tag to increase the likelihood that your most important pages are present in a search index.
Also, please note that assigning a high priority to all of the URLs on your site is not likely to help you. Since the priority is relative, it is only used to select between URLs on your site.

### lastmod

Your date format will auto convert to [W3c Datetime format](http://www.w3.org/TR/NOTE-datetime). for example, if you send
a string look like: `2015-06-07 10:51:20`, Sitemap object will auto convert it to `2015-06-07T10:51:20+02:00`.

This is an example to send it as real sitemap for Google or other search engine:

``` php
header ("Last-Modified: " . gmdate("D,d M YH:i:s") . " GMT");
header ("Cache-Control: no-cache, must-revalidate");
header ("Pragma: no-cache");
header ("Content-type: text/xml");
header ("Content-Description:XML Sitemap" );

echo $sitemap;

exit();

```

You can set the format you want:


``` php
$sitemap->setDateFormat(\DateTime::ISO8601);

// OR

$sitemap->setDateFormat('Y-m-d');
```

## Using Sitemap index files (to group multiple sitemap files)

``` php
use SujanShrestha\Sitemap\SitemapIndex;

$index = new SitemapIndex;

$index->addIndexItem('http://domain.com/sitemap1.xml', $lastmod1);
$index->addIndexItem('http://domain.com/sitemap2.xml', $lastmod2);

echo $index->toString();
```

Output:

``` xml
<?xml version="1.0" encoding="utf-8"?>
<sitemapindex xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
	<sitemap>
        <loc>http://domain.com/sitemap1.xml</loc>
        <lastmod>2015-06-07T10:51:20+02:00</lastmod>
    </sitemap>
	<sitemap>
		<loc>http://domain.com/sitemap2.xml</loc>
		<lastmod>2015-06-07T10:51:20+02:00</lastmod>
	</sitemap>
</sitemapindex>
```

See: http://www.sitemaps.org/protocol.html#index

## More

- [Extending the Sitemaps protocol](http://www.sitemaps.org/protocol.html#extending)
