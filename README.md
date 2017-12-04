# DarkSkyApi [![NuGet](https://img.shields.io/nuget/v/DarkSkyApi.svg?maxAge=2592000)]()

An unofficial C# library for the [Dark Sky](https://darksky.net/dev) weather service. Targets .NET Standard 1.1 - compatible with .NET 4.5, Mono 4.6, Windows 8/8.1, Windows Phone 8/8.1, .NET Core 1.0, Xamarin Android/iOS, and Universal Windows Apps.

## Installation

[NuGet](https://www.nuget.org/packages/DarkSkyApi/): `Install-Package DarkSkyApi`

## New in Version 3.3 (04 December 2017)

[Full Changelog](CHANGELOG.md)

* **Breaking Change**: `UVIndexTime` is now a `DateTimeOffset` like other time fields; previously it was a `double`.
* Added support for new fields (thanks, [@MattLugar!](https://github.com/MattLugar)).
    - `Alert`
      - `Severity` indicates the severity of the alert.
      - `Time` indicates the time at which the alert was issued.
      - `Regions` indicates the regions covered by the alert.
    - `DayDataPoint`
      - `HighTemperature`/`HighTemperatureTime` indicates the daytime high temperature, and when it occurs.
      - `LowTemperature`/`LowTemperatureTime` indicates the overnight low temperature, and when it occurs.
      - `WindGust`/`WindGustTime` indicates the wind gust speed, and when it occurs.
      - `PrecipitationAccumulation` indicates the amount of snowfall accumulation expected.
    - `HourDataPoint`
      - `WindGust` indicates the wind gust speed.
      - `PrecipitationAccumulation` indicates the amount of snowfall accumulation expected.
* Mark deprecated fields as deprecated.
  - `DayDataPoint`
    - `ApparentMaxTemperature`/`ApparentMaxTemperatureTime`
    - `ApparentMinTemperature`/`ApparentMinTemperatureTime`
    - `MaxTemperature`/`MaxTemperatureTime`
    - `MinTemperature`/`MinTemperatureTime`

## Quick Start

### Current Conditions

```c#
using DarkSkyApi;
using DarkSkyApi.Models;

...

var client = new DarkSkyService("YOUR API KEY HERE");
Forecast result = await client.GetWeatherDataAsync(37.8267, -122.423);

...
```

![](./screenshot.png)

Note that the Dark Sky service doesn't always return all fields for each region. In these cases, some properties may be null or zero.

### Conditions for a specific date

```c#
using DarkSkyApi;
using DarkSkyApi.Models;

...

var client = new DarkSkyService("YOUR API KEY HERE");

Forecast result = await client.GetTimeMachineWeatherAsync(37.8267, -122.423, DateTimeOffset.Now);

...
```

The `Helpers` class contains extension methods to convert a DateTimeOffset to Unix time, and back:

```c#

int unixTime = DateTimeOffset.Now.ToUnixTime();

DateTimeOffset date = unixTime.ToDateTimeOffset();
```

### API Usage Information

After making a request for data (be it current or historical), the DarkSkyService instance's `ApiCallsMade` property will contain the number of calls made today, using the given API key. The property will be null if no requests have been made through the particular instance.

## Design

This library uses a client-oriented approach, instead of a request-response model: the `DarkSkyService` object is intended to be an abstraction from which weather data (`Forecast`s) can be obtained.

`Forecast`s do contain all the fields that can appear in the raw JSON obtained through making a direct request to the web service, but exposes them through more .NET convention-friendly properties: for example, `precipIntensityMax` is exposed as `MaxPrecipitationIntensity`. These properties are (as shown here) sometimes more verbose, but were intended to match the style commonly used in .NET projects.

## Tests

NUnit is used for some simple integration tests with the actual web service. To run the tests, a valid API key must be added to the `app.config` file in the `DarkSkyService.Test` folder.
