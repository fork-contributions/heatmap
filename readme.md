![Packagist PHP Version Support](https://img.shields.io/packagist/php-v/blumilksoftware/heatmap?style=for-the-badge) ![Packagist Version](https://img.shields.io/packagist/v/blumilksoftware/heatmap?style=for-the-badge) ![Packagist Downloads](https://img.shields.io/packagist/dt/blumilksoftware/heatmap?style=for-the-badge)

## About
Making a heatmap from custom datasets should be extremely easy. We are here to help you with that.

What's a heatmap? It is a data visualization technique that shows magnitude of a phenomenon as color in two dimensions<sup>[[1](https://en.wikipedia.org/wiki/Heat_map)]</sup>, and you can know it from GitHub user profile pages:

![./docs/github.png](./docs/github.png)

### Installation
The Heatmap is distributed as Composer library via [Packagist](https://packagist.org/packages/blumilksoftware/heatmap). To add it to your project, execute the following command:

```bash
composer require blumilksoftware/heatmap
```

### Usage
You can use any set of data you want. By default, dates will be taken from `"created_at"` key of arrays or objects implementing `\ArrayAccess` interface. Builder accepts also objects implementing `\Blumilk\HeatmapBuilder\Contracts\TimeGroupable` contract with mandatory `getTimeGroupableIndicator()` method returning a string with proper date:

```php
$data = [
    ["created_at" => "2025-11-01 00:00:00", /** (...) */],
    ["created_at" => "2025-11-03 00:00:00", /** (...) */],
    ["created_at" => "2025-11-16 00:00:00", /** (...) */],
    ["created_at" => "2025-11-16 00:00:00", /** (...) */],
    ["created_at" => "2025-11-18 00:00:00", /** (...) */],
    ["created_at" => "2025-11-19 00:00:00", /** (...) */],
];
```

Then create an instance of `\Blumilk\HeatmapBuilder\HeatmapBuilder`. By default, it will be working on day-based periods for last week from the moment you are calling it:
```php
$builder = new HeatmapBuilder();
$result = $builder->build($data);
```

Method `build()` returns array of tiles that can be serialized into JSON with simple `json_encode()` function or any other serializer: 
```json
[
  {
    "label": "2025-11-16",
    "count": 2,
    "description": ""
  },
  {
    "label": "2025-11-17",
    "count": 0,
    "description": ""
  },
  {
    "label": "2025-11-18",
    "count": 1,
    "description": ""
  },
  {
    "label": "2025-11-19",
    "count": 1,
    "description": ""
  },
  {
    "label": "2025-11-20",
    "count": 0,
    "description": ""
  },
  {
    "label": "2025-11-21",
    "count": 0,
    "description": ""
  },
  {
    "label": "2025-11-22",
    "count": 0,
    "description": ""
  },
  {
    "label": "2025-11-23",
    "count": 0,
    "description": ""
  }
]
```
### Customizing the Heatmap Builder watching period

By default, the `HeatmapBuilder` automatically sets its observation period to the last seven days if no custom period (`$period`) is provided during instantiation. This default behavior ensures a rolling seven-day view from the current day.

If you need to specify a custom period (e.g., the entire month of January), you can achieve this by explicitly passing a `CarbonPeriod` instance to the builder, as shown in the example below:

```php
// Define a custom period for the entire month of January 2025
$period = CarbonPeriod::create(
    Carbon::parse('2025-01-01'),
    '1 day',
    Carbon::parse('2025-01-31')
);

// Instantiate the HeatmapBuilder with the custom period
$builder = new HeatmapBuilder(
    now: Carbon::now(),
    periodInterval: PeriodInterval::Daily,
    period: $period // Include the custom period
);

// Generate the heatmap data
$result = $builder->build($data);
```
