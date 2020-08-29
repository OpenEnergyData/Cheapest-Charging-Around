# Cheapest-Charging-Around-Open-Energy-Data-Hackdays

# Challenge goal
Further development of the GIS platform of the Swiss Federal Office of Energy (SFOE): Add price information to the charging stations and find the cheapest option around for electric car drivers.

# Compile the price information: not so easy at it may seem
## Finding the prices
What we needed was of course the list of the prices for every provider coherently compiled and available for comparison.
At the very beginning we toyed a little with the idea of scraping the provider's websites to harvest the price information. This approach was quickly proved as unfeasible since for many providers the price structure is not even clearly published and, even when it is, each provider present it in its own way or only through their proprietary mobile app.
Public APIs are not available and we had to scrap that approach too.

At the end, we resorted to manually look for and extract the pricing information from the various websites. For time reasons we limited ourselves to three of the biggest providers.
## Price structure consolidation
KWh, per minute, per hour, monthly abo, yearly abo, one-time fee per plug-in, flatrates, roaming... and many permutations of all that. The pricing landscape is obscure and confusing. We thus spent quite a lot of time looking for a way to model this variety and compile it in a single table.

We settled for the concept of "tariff plan" as our object. Discriminating parameters are the tariff provider, the roaming partner, time of the day, power type and KW at the plug. With such a structure we are able to filter for the chosen plug paramenters and present the user the tariff plans available for that particular plug.
As far as pricing information goes, we could not really consolidate it to a single measurement unit (ex: chf per KWh) because of the diversity in the pricing structures. Therefore, for each tariff plan we show the price in the same measurement units given by the provider. Because of this, it was also not possible to give the final charge price to the user to fill the battery. In addition, this would require a lot of information about the vehicle that are here out of scope.

# Main problems
As already stated, we faced several problems during our data collection and consolidation process.
* The pricing data is not publicly available, or very hard to find.
* Some pricing information is available only to registered customers.
* There is no common denominator across the tariff plans across different providers. And even within the same provider we found often huge differences in price calculations. Comparing the various tariff plans is therefore almost impossible.
* We did not manage to harvest the price structures programmatically and had to resort to manual work.
* The price also depends on the car type (max. kW input, battery volume etc.)

# Our solution
To allow a user to compare the available tariff plans for a given charging station we combined the following elements:
* A leaflet webmap. The map uses the publicly available geoJSON from the Federal Spatial Data Infrastructure (FSDI) to visualize the charging stations.
* The freely accessible FSDI API at api.geo.admin.ch to retrieve the full station and plug informations
* Our static table with the tariff plans information.

When the user picks a charging station, the application retrieves the ID from the geoJSON and then uses it to make an API call to api.geo.admin.ch. This retrieves all the needed information about the available plugs at the station. The app then filters the tariff table to present the valid plans and the pricing information.

# Data model of the tariff table
You can find the table [here](https://docs.google.com/spreadsheets/d/1dw7tkYa0nSNKVkIfXEMOxA0CAAqR0nYDYwAuvXVgSB0/edit#gid=0)
<table>
  <tr>
    <th>Variable name</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>tariff_provier</td>
    <td>String<br>Name of the provider offering this tariff</td>
  </tr>
  <tr>
    <td>roaming_partner</td>
    <td>String<br>Name of the roaming partner for this tariff. Is the same as tariff_provider if not a roaming tariff</td>
  </tr>
  <tr>
    <td>tariff_name</td>
    <td>String<br>Name of the tariff</td>
  </tr>
  <tr>
    <td>valid_kw</td>
    <td>String<br>Comma-separated list of the plug powers using this tariff</td>
  </tr>
  <tr>
    <td>start</td>
    <td>hh:mm:ss<br>Validity start for this tariff plan</td>
  </tr>
  <tr>
    <td>end</td>
    <td>hh:mm:ss<br>Validity end for this tariff plan</td>
  </tr>
  <tr>
    <td>powertype</td>
    <td>String<br>"AC", "DC" or "AC, DC" where the info is not available</td>
  </tr><tr>
    <td>flatrate</td>
    <td>Boolaen<br>Informs if the tariff is a flatrate one (pay once, charge unlimited without additional costs)</td>
  </tr>
  <tr>
    <td>chf_plug-in</td>
    <td>Float<br>Base fee just to plug-in the car, in Swiss Francs for this tariff</td>
  </tr>
  <tr>
    <td>chf_minute</td>
    <td>Float<br>Fee in Swiss Francs per minute plugged in</td>
  </tr>
  <tr>
    <td>chf_kwh</td>
    <td>Float<br>Fee in Swiss Francs per KWh delivered by the plug</td>
  </tr>
  <tr>
    <td>chf_month</td>
    <td>Integer<br>Cost in Swiss Francs of the monthly subscription</td>
  </tr>
  <tr>
    <td>chf_year</td>
    <td>Integer<br>Cost in Swiss Francs of the yearly subscription</td>
  </tr>
</table>

# Known problems and possible ameliorations
Ours is a rudimentary solution that must be considered as an early-stage POC.
