Quantifying the Airline "Monopoly Tax": Market Concentration and Pricing Dynamics in U.S. Domestic Aviation

Ben An, Dan Mosu, Kai Clancy, Kate Bloom, Priyanka Govani
Application Description and Motivation
Commercial air travel is a crucial part of the U.S. economy, generating billions of dollars each year, gathering revenue from millions of people for work, family, and vacation. On paper, American travelers have a variety of airline options. In reality, airline competition does not happen nationwide, it happens route-by-route. While travelers might have five different airlines to choose from when flying across the country, they often are limited to one or two options when flying between specific cities.
This situation raises the question for everyday consumers: How much extra money do travelers pay when a single airline controls a specific route?
When one airline dominates a flight path, passengers lose the power to shop alternatives. Without legitimate competition, dominant airlines can price gouge given that travelers have no realistic alternative. This occurrence is known as the "monopoly tax", an extra charge driven by market dominance rather than supply/demand and cost of operating the flight.
This project creates a complete data analytics pipeline using the U.S. Department of Transportation (DOT) Domestic Airline Consumer Airfare Report. This public dataset tracks quarterly airfare prices, passenger numbers, and airline market shares across major U.S. city routes. By bringing this data together and analyzing it step-by-step, this project aims to:
Calculate the Price Markup: Measure the exact dollar premium passengers pay when one airline controls most of a route.
Compare Trip Distances: See if airlines get away with higher markups on short flights (where people could potentially drive) or long flights (where flying is the only option).
Build a Fare Forecast: Create an intuitive model that estimates fair ticket prices and flags routes where travelers are likely overpaying due to a lack of competition.
Project Plan
Stage 1: Capture 
The project ingests two complementary data sources to ensure airline pricing behavior can be evaluated alongside macro operating costs:
Primary Dataset (DOT Airfare API): Data will be extracted directly from the U.S. Department of Transportation / Bureau of Transportation Statistics via the Data.gov Socrata Open Data API (data.transportation.gov). The query targets quarterly domestic route records, capturing origin and destination cities (city1, city2), non-stop flight distance (nsmiles), passenger volume (passengers), average ticket fare (fare), the largest carrier (carrier_lg), the largest carrier’s market share (large_ms), and the largest carrier's average fare (fare_lg).
Enrichment Dataset (EIA Jet Fuel API): Jet fuel represents one of the largest operating expenses for airlines. To confirm that high fares reflect market power rather than spiking fuel prices, we will query the U.S. Energy Information Administration (EIA) API to retrieve average monthly jet fuel spot prices matching the same quarterly periods.
Mechanism: Python scripts utilizing the requests library will automate the extraction, pulling down raw records in JSON format and staging them for transformation.
Stage 2: Process 
Raw government data requires standardization and calculated metrics before running any statistical tests:
Data Cleaning & Normalization:
Clean text and currency formatting by stripping symbols (e.g., converting "$238.05" into numeric 238.05).
Convert market share figures (large_ms) into standard decimals.
Standardize city names by separating city and state text from metropolitan tags.
Remove incomplete records and low-volume routes (e.g., routes carrying fewer than 10 passengers per day) to eliminate statistical noise.
Feature Engineering:
Airline Fare Premium (The Markup): Calculate the direct dollar and percentage markup charged by the lead airline:
Market Competition Tiers: Classify each route into one of three competition categories based on large_ms:
Competitive: Lead carrier has less than 40% market share.
Concentrated: Lead carrier holds between 40% and 65% market share.
Monopolized: Lead carrier controls more than 65% market share.
Cost-per-Mile Metric: Compute fare / nsmiles to standardize ticket prices across short hops and coast-to-coast flights.
Stage 3: Store 
As permitted by the project guidelines, a full relational database is not required. Instead, the project uses a clean, reproducible file-based repository:
Storage Format: Cleaned and enriched data will be serialized into Apache Parquet format. Parquet provides columnar compression, preserves explicit data types (numeric, dates, categories), and loads substantially faster in Python than raw CSVs.
Partitioning Scheme: Files will be organized logically on local disk by year and quarter (/data/processed/year=YYYY/quarter=Q/), making it straightforward to query specific quarters or expand the dataset over time without re-running past ETL scripts.
Stage 4: Analyze 
The analysis applies descriptive statistics, hypothesis testing, and regression modeling to answer three core questions:
Insight 1: Measuring the "Monopoly Surcharge" Across Market Tiers
Method: Group routes by their competition tier (Competitive, Concentrated, Monopolized) and run comparative difference-of-means tests (ANOVA / t-tests) while controlling for distance.
Target Finding: Determine the exact average dollar penalty consumers pay when a route shifts from competitive to monopolized, proving whether single-carrier dominance directly inflates ticket prices.
Insight 2: Distance vs. Monopoly Pricing Power
Method: Correlation and scatter analysis comparing flight distance (nsmiles) to the fare markup (fare_lg - fare).
Target Finding: Test whether airlines exercise higher pricing power on long routes (where travelers have no substitute for flying) versus short routes (under 400 miles, where driving or taking a train acts as a natural price cap).
Insight 3: Predictive Fair-Price Model & Route Gouging Detection
Method: Train a Multiple Linear Regression or Random Forest model where ticket price is predicted using flight distance, market share, seasonal quarter, passenger volume, and fuel price.
Target Finding: Establish an expected "fair market fare" for any domestic city pair. By comparing actual fares against predicted fares, we can flag the specific routes where lack of competition causes consumers to overpay the most.
Stage 5: Communicate 
The final stage translates the numerical models into clear, intuitive takeaways:
Visual Charts:
A box plot showing ticket prices across the three competition tiers to visually demonstrate the price gap.
A scatter plot mapping flight distance against ticket prices, color-coded by the dominant carrier to reveal pricing outliers.
Summary Table of Overpriced Routes: A top-10 list of the most monopolized city pairs in the country, showing the route, the controlling airline, and the estimated dollar markup travelers pay.
Executive Summary: A plain-language summary outlining how travelers, corporate travel planners, and transportation policy analysts can use these findings to identify competitive alternative routes or push for new airline entrants.
