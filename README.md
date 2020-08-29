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
As far as pricing information goes, we could not really consolidate it to a single measurement unit (ex: chf per KWh) because of the diversity in the pricing structures. Therefore, for each tariff plan we show the price in the same measurement units given by the provider. It was also not possible to give the price to the user to fill the battary due to this and missing information to the car the user drives. 

# Main problems
As already stated, we faced several problems during our data collection and consolidation process.
* The pricing data is not publicly available, or very hard to find.
* Some pricing information is available only to registered customers.
* There is no common denominator across the tariff plans across different providers. And even within the same provider we found often huge differences in price calculations. Comparing the various tariff plans is therefore almost impossible.
* We did not manage to harvest the price structures programmatically and had to resort to manual work.
* The price also depends on the car type (max. kW input, battery volume etc.)
