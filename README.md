**FOB USG-NWE Voyage P&L Model**

**Historical Monte-Carlo Simulation of WTI-Brent crude arbitrage under
market and operational risk.**


About: Independently designed and implemented in Python by Adam Palmer,
second year undergrad at the University of Nottingham
(19/12/25-present).


The model is used as a decision tool for evaluating the commercial
viability of a single Aframax shipment of WTI crude from the US Gulf
Coast to Northwest Europe, given the current WTI-Brent spread. This is
achieved by simulating voyage profitability under the various
combinations of market and operational risk factors. Live WTI Houston
FOB and WTI-Brent spread user inputs generate the following outputs: P&L
distributions, expected profit, expected shortfall and decision
surfaces. A trade is recommended if its expected profit sufficiently
compensates for the risk of loss.

The USG-NWE export flow is monitored for several reasons. As the markets
are geographically separated by transport time and costs, regional
prices do not converge. The spread persists because transportation
constraints are binding and operational execution risk is large. WTI
exports to Northwest Europe are specifically common due to compatible
refinery infrastructure. As WTI and Brent crude have similar chemical
compositions, NWE refineries search for opportunities to increase
margins by substituting local Brent with cheaper WTI barrels. In the US
shale boom of 2011-2014, the oversupply of crude oil caused heavy WTI
discounting, exacerbated by the US export ban. When the ban was lifted
in 2015, exports surged and the spread reduced, often resulting in thin
arbitrage margins. As arbitrage profitability is no longer obvious, the
model provides decision-making insight.


Project Charter:

Commodity: Crude Oil

Origin proxy: WTI

Destination proxy: Brent

Route: USG--NWE

Vessel class: Aframax

Cargo size: 730kbbl

Incoterms: FOB Buy USG, CIF Sell NWE

Decision rule: EV/CVaR~0.05~ ≥ 0.75


Methodology

1.  Trade structure:

The model estimates voyage P&L from arbitrage decision to destination
port discharge for a fixed trade structure, with total exposure spanning
arbitrage decision (t=0) to settlement.

Timeline

At t=0, freight is fixed and WTI is bought, with the laycan window
aligned with cargo readiness to load. The vessel spends an elapsed time
at the port of origin in the US Gulf before transit, arriving at a port
in the ARA region and discharging, ending market exposure. Operational
exposure ends after discharge at settlement, where financing is repaid.

Conventions

Revenue is from WTI resale at a Brent-linked price. Costs are: WTI
purchase, cost of transport (Freight), interest (Financing), cost of
port delay (Demurrage), insurance, evaporation, handling and measurement
error ($Q_{BL} \neq Q_{Discharge}$) and port fees.

Sales and Purchase Agreement (SPA):

- Cargo quantity of 730kbbl bought at arbitrage decision

- Buy leg priced as the 5-day average of daily WTI Houston FOB
  assessments around the Bill of Lading (BL) date

- If BL falls on a non-trading day, the nearest previous assessment
  represents price at BL

- Sell leg is left floating, priced as the 5-day average of Dated Brent
  assessments around discharge

- If discharge falls on a non-trading day, the nearest previous
  assessment represents price at discharge

- NWE counterparty pays for cargo quantity at discharge

- Settlement under Cash Against Documents (CAD)

- Institute Cargo Clauses A coverage

- Insured value is 110% of the underlying cargo value

- Insurance premium of 0.50%

- Settlement delay for NWE counterparty limited to 30 days


Charterparty:

- Aframax and freight rate are fixed at arbitrage decision

- Full freight payment at BL for voyage charter, priced off WS quote

- Notice of Readiness (NOR) issued at port limits (WIBON)

- 72-hour total running laytime, Sundays and holidays included,
  non-reversible. Maximum of 36 hours at origin and destination

- Laytime clock begins 6 hours after NOR at origin and destination port

- Demurrage rate of \$75,000/day

- Laytime clock ends at hose disconnection

- Destination port fees accrued after discharge are borne by the
  shipowner


Financing:

- Letter of credit issued from bank to USG counterparty after arbitrage
  decision

- Priced at cargo value convention in SPA

- Interest accrues from BL until settlement, under ACT 365

- Interest rate fixed at BL, charged at SOFR 30-day average plus a
  positive basis range


2.  P&L Function

The P&L function relies on the principle that voyage P&L is equal to the
gross netback from sale minus the operational execution costs. It is
defined as:

$$\pi\  = \ Spread - \ Freight\  - \ Financing\  - \ Demurrage\  - \ Insurance\  - \ Port\ fees$$

$$Spread = \ {(P}_{Dated\ Brent\ discharge}.Q_{Discharge})\  - \ ({P_{WTI\ Houston\ FOB}.Q}_{BL})$$

BL approximated to origin port departure and discharge approximated to
destination berth departure.

$$Q_{BL} = \ 730,000,\ bbl$$

$$P_{Dated\ Brent\ discharge} = \ \frac{\sum_{}^{}{Dated\ Brent}_{discharge - 2,discharge - 1,discharge,discharge + 1,discharge + 2}}{5},\$/bbl$$

$$P_{WTI\ Houston\ FOB} = \ \frac{\sum_{}^{}{WTI\ Houston\ FOB}_{BL - 2,BL - 1,BL,BL + 1,BL + 2}}{5},\$/bbl$$

$$Freight = \ \frac{WS\ Quote}{100}\  \times TD25\ Flat\ rate\  \times Cargo\ quantity,$$

$$Where\ Cargo\ quantity = 95,000,\ mt$$

$$Financing = {(SOFR}_{BL} + \ \varepsilon_{1})\  \times \left( P_{WTI\ Houston\ FOB}.Q_{BL} \right)\  \times \ \frac{Financing\ exposure}{365},\$,$$

$$Where\ \varepsilon_{1}\sim Tri(75,200,125)\ in\ bps\ and\ Financing\ exposure =$$

$$t_{sea\ passage} + t_{destination\ port\ arrival \rightarrow berth\ departure} + t_{settlement\ lag},$$

$$And\ t_{settlement\ lag}\sim Tri(5,30,10),\ all\ t\ in\ days$$

$$Demurrage = Demurrage\ origin + Demurrage\ destination,\$$$

$$Demurrage\ origin = max\left( 0,\max\left( 0,t_{origin\ port\ arrival \rightarrow berth\ departure} - 6 \right) - 36 \right) \times r_{demurrage}$$

$$Demurrage\ destination = \ max\left( 0,\max\left( 0,t_{destination\ port\ arrival \rightarrow berth\ departure} - 6 \right) - 36 \right) \times r_{demurrage}$$

$$Where\ origin\ laytime\ allowance = 36\ ,\ destination\ laytime\ allowance = 24,\ $$

$$All\ t\ in\ hours\ and\ r_{demurrage} = 3,125,\$/hr$$

$$Insurance = (P_{WTI\ Houston\ FOB}.Q_{BL})\  \times 110\%\  \times r_{premium},\$,$$

$${Where\ r}_{premium} = 0.50\%$$

$$Q_{Discharge} = Q_{BL}\left( 1 - \ \varepsilon_{2} \right),bbl,$$

$${Where\ \varepsilon}_{2}\sim Tri( - 0.002,0.00467,0.001)$$

$$Port\ fees = Origin\ port\ fees + Destination\ port\ fees,\$,$$

$$Where\ Origin\ port\ fees = {FX}_{€ \rightarrow \$,BL}\left( 84,000 + \ \varepsilon_{3}.84000 \right),$$

$$And\ Destination\ port\ fees = {FX}_{€ \rightarrow \$,Discharge}\left( 84,000 + \ \varepsilon_{4}.84000 \right),$$

$$Where\ \varepsilon_{3}\sim Tri(0.2,1.2,0.8)\ and\ \varepsilon_{4}\sim Tri(0.2,1.2,0.8)$$

$$And\ 84,000 = Seaport\ dues$$

$$Scheduling\ lag = \ t_{scheduling\ lag}\sim Tri(3,15,5),days$$


Fixed variables:

- 730kbbl

- 95,000mt cargo quantity

- 6-hour laytime clock delay

- 36-hour laytime allowance origin

- 36-hour laytime allowance destination

- \$75,000/day demurrage rate

- 110% multiplied by cargo value to calculate insured value

- 0.50% insurance premium on the insured value

- €84,000 seaport due proxy


Derived Random Variables:

- $Financing\ exposure,\ days$

- $Demurrage\ origin,\ \$$

- $Demurrage\ destination,\ \$$

- $Origin\ port\ fees,\ \$$

- $Destination\ port\ fees,\ \$$


Base Random Variables:

- ${Dated\ Brent}_{discharge - 2,discharge - 1,discharge,discharge + 1,discharge + 2},\$/bbl$

- ${WTI\ Houston\ FOB}_{BL - 2,BL - 1,BL,BL + 1,BL + 2},\$/bbl$

- $WS\ Quote,\ WS\ points$

- $TD25\ Flat\ rate,\ \$/mt$

- ${SOFR}_{BL}$

- $\varepsilon_{1},bps$

- $t_{scheduling\ lag}$

- $t_{origin\ port\ arrival \rightarrow port\ departure}$

- $t_{origin\ port\ arrival \rightarrow berth\ departure}$

- $t_{sea\ passage}$

- $t_{destination\ port\ arrival \rightarrow berth\ departure}$

- $t_{settlement\ lag}$

- $\varepsilon_{2}$

- ${FX}_{€ \rightarrow \$}$

- $\varepsilon_{3}$

- $\varepsilon_{4}$


3.  Simulation Algorithm:

Non-deterministic variables are bootstrapped, dependent on commercial
availability (Bloomberg Terminal or other sources where possible). Where
data is incomplete, variables are parametrically fitted. The simulation
operates on two layers, an inner algorithm that computes P&L~i~, and an
outer algorithm that iterates the inner repeatedly, stores each
individual P&L scenario and generates the P&L distribution.

The inner algorithm is designed to preserve time-dependence of the P&L
function as well as variable correlations, by conditionally block
sampling from a time-indexed market data matrix, constructed with
historical time-series.


The market data matrix rows are time-indexed daily in ascending order,
with the columns:

- $Dated\ Brent$

- $WTI\ Houston\ FOB$

- $t_{sea\ passage}$

- $t_{origin\ port\ arrival \rightarrow berth\ departure}\$

- $t_{origin\ port\ arrival \rightarrow port\ departure}$

- $t_{destination\ port\ arrival \rightarrow berth\ departure}$

- $WS\ Quote$

- $TD25\ Flat\ Rate$

- $SOFR$

- ${FX}_{€ \rightarrow \$}$

The inner algorithm computes P&L~i~ by reading specific values from the
matrix that correspond to the trade timeline structure. The process that
determines the path traced through the matrix, determining the times at
which values are read is:

1.  Draw parametrized random variables (non-matrix):
    $t_{scheduling\ lag}$, $t_{settlement\ lag}$, $\varepsilon_{1}$,
    $\varepsilon_{2}$, $\varepsilon_{3}$ and $\varepsilon_{4}$ and
    store. Scheduling lag value represents time elapsed from arbitrage
    decision to vessel arrival within port limits, aligned with cargo
    readiness to load. Settlement lag represents time elapsed between
    discharge cargo pricing and NWE counterparty settlement.

2.  Locate a historical date with similar spread and WTI level, let it
    be $**t_{1}$. 

3.  Add ${(t}_{scheduling\ lag} - 1)$ to $t_{1}$. Represents time at
    which Aframax arrives within the origin port limits.

4.  Add $t_{origin\ port\ arrival \rightarrow port\ departure}$ to
    ${(t}_{scheduling\ lag} - 1\  + \ t_{1})$. Represents time at which
    Aframax leaves origin port and BL date.

5.  Add $t_{sea\ passage}$ to
    ${(t}_{scheduling\ lag} - 1\  + \ t_{1}\  + \ t_{origin\ port\ arrival \rightarrow port\ departure})$.
    Represents time at which Aframax reaches destination port limits.

6.  Add $t_{destination\ port\ arrival \rightarrow berth\ departure}$ to
    ${(t}_{scheduling\ lag} - 1\  + \ t_{1}\  + \ t_{origin\ port\ arrival \rightarrow port\ departure}\  + \ t_{sea\ passage})$.
    Represents time at which Aframax charter terminates and sell leg is
    priced.

7.  Add $t_{settlement\ lag}\$to
    ${(t}_{scheduling\ lag} - 1\  + \ t_{1}\  + \ t_{origin\ port\ arrival \rightarrow port\ departure}\  + \ t_{sea\ passage} + \ t_{destination\ port\ arrival \rightarrow berth\ departure})$.
    Represents time at which total exposure ends.


Simplified to Matrix Timestamps:

$$Node\ 1:\ t_{1}$$

$${Node\ 2:\ (t}_{scheduling\ lag} - 1\  + \ t_{1})$$

$${Node\ 3:\ (t}_{scheduling\ lag} - 1\  + \ t_{1}\  + \ t_{origin\ port\ arrival \rightarrow port\ departure})$$

$$Node\ 4:\ {(t}_{scheduling\ lag} - 1\  + \ t_{1}\  + \ t_{origin\ port\ arrival \rightarrow port\ departure} + \ t_{sea\ passage})$$

$${Node\ 5:\ (t}_{scheduling\ lag} - 1\  + \ t_{1}\  + \ t_{origin\ port\ arrival \rightarrow port\ departure}\  + \ t_{sea\ passage} + t_{destination\ port\ arrival \rightarrow berth\ departure})$$

$$Node\ 6:\ {(t}_{scheduling\ lag} - 1\  + \ t_{1}\  + \ t_{origin\ port\ arrival \rightarrow port\ departure}\  + \ t_{sea\ passage} + t_{destination\ port\ arrival \rightarrow berth\ departure}\  + \ t_{settlement\ lag})$$

This results in a contiguous block drawn from Node 1 to Node 6. The
following are the values read at each node and stored. Brackets
represent to which sub-function/s the base random variable belongs. Note
that for every node, the corresponding node date is also read and stored
so that durations can be calculated:

$$Node\ 1:\ {*t}_{scheduling\ lag}\ (Matrix\ path),**WS\ quote\ (Freight)\ from\ t = 0,$$

$$**TD25\ Flat\ rate\ (Freight)\ from\ t = 0$$

$$Node\ 2:\ t_{origin\ port\ arrival \rightarrow port\ departure}\ (Matrix\ path),$$

$$\ t_{origin\ port\ arrival \rightarrow berth\ departure}\ (Demurrage\ origin)$$

$$Node\ 3:\ t_{sea\ passage}\ (Matrix\ path,Financing),!P_{WTI\ Houston\ FOB}\ (Buy\ leg,Insurance,Financing),$$

$$\ {FX}_{€ \rightarrow \$\ BL}\ (Origin\ port\ fees),{SOFR}_{BL}\ (Financing)$$

$$Node\ 4:\ t_{destination\ port\ arrival \rightarrow berth\ departure}\ (Matrix\ path,Financing,Demurrage\ destination)$$

$${Node\ 5:\ *t}_{settlement\ lag}\ (Matrix\ path,Financing),!P_{Dated\ Brent\ discharge}\ (Sell\ leg),$$

$${FX}_{€ \rightarrow \$,Discharge}\ (Destination\ port\ fees)$$

$$Node\ 6:\  -$$

Required Operations:

$$Node\ 6 - Node\ 3 = Financing\ exposure$$

$$Node\ 6 - Node\ 1 = Total\ trade\ exposure$$


Handling of market data matrix missing values:

The time-series for financial data (Dated Brent, WTI Houston FOB, SOFR
and FX columns) exclude assessments on weekends and public holidays as
the markets are closed. The sub-functions requiring SOFR and FX rely on
single inputs, so if BL or discharge falls on a non-trading day, the
nearest previous assessment is taken by the algorithm. The sub-functions
requiring Dated Brent and WTI Houston FOB need multiple inputs. When the
algorithm lands on a date where they are read, it anchors at the nearest
assessment value at or before that date. Then it takes the two nearest
values before and after the anchor.

Vessel tracking data (t columns in the matrix) does not have a
consistent time-series. When the algorithm lands on a date where a t
value is read, it takes the absolute closest value.


Combining the tracing and reading processes gives the complete P&L
sampling procedure of:

1.  $Locate\ Node\ 1 \rightarrow \ Read\ and\ store\ specified\ random\ base\ variables \rightarrow Jump\ to\ Node\ 2\  \rightarrow Read\ and\ store\ specified\ random\ base\ variables\  \rightarrow \ Repeat\ until\ Node\ 6$

2.  Node subtractions are performed and stored. The first operation
    transforms the raw block timestamp into the correct financing
    exposure window. The second operation is not a random variable
    sampled for P&L function input, but a separate output to P&L~i~.

3.  All stored random base variables are inputted into their relevant
    sub-function/s of the P&L function.

4.  P&L function is computed to output P&L~i~.

5.  Total trade exposure for P&L~i~ outputted.

6.  Outer function iterates inner function.

\*Scheduling and settlement lag are variables that affect total trade
exposure time, but do not have a direct cost. They are required for the
matrix tracing but are not values contained within the matrix itself.

\*\*The starting point $t_{1}$ acts as a next-day forecast of the
current WTI level and spread. If $t_{0}$ is the starting point, it must
exactly match the user inputs which assumes the current WTI level and
spread exactly coincide with the daily physical assessment of WTI
Houston FOB and Dated Brent, excluding intraday movement. The block
begins one day ahead of arbitrage decision, hence freight fixture.
$t_{scheduling\ lag} - 1$ ensures that the date the vessel arrives is
correct relative to the timeline and WS Quote and TD25 Flat Rate are
read and stored at arbitrage decision, rather than $t_{1}$.

!The two assessments around those at BL and discharge are also taken, in
line with the SPA 5-day average pricing convention.


Exclusions (Within Scope of the Trade Structure):

1.  Basis risk: The model treats WTI as economically equal to Brent at
    discharge whilst in reality:

$$WTI = Brent\  \pm Differential$$

The differential is a stochastic variable reflecting quality, logistics,
supply and demand conditions. As basis data (the differential) is
proprietary information, it cannot be bootstrapped. Parametrisation
would allow for impossible market combinations without regime modelling,
both requiring the data for justification.

2.  Hedging: The model's trade structure is a form of extreme risk
    warehousing. An unhedged physical trade is very rare and would only
    occur with significant confidence in the upside.

3.  Counterparty failure and force majeure: Any event that cancels the
    trade is excluded and counterparties are treated as intending to
    fulfil contracts, despite potential delays. This assumption is made
    throughout the timeline:

- Aframax always arrives in laycan window

- USG counterparty delivers cargo such that loading takes place upon
  ship readiness

- Events that trigger force majeure clauses are excluded at any point in
  the timeline

- Settlement paid by NWE counterparty is always received within 30 days

4.  Cost of Capital: In practice, if 2 trades have the same
    EV/CVaR~0.05~ ratio but one trade has a shorter total exposure, it
    is preferred due to higher capital deployment efficiency. The
    decision rule only accounts for risk-adjusted return.


Assumptions:

1.  Aframax: Aframax ships are common for the USG-NWE route. As they are
    smaller than VLCCs, Aframax ships have increased port optionality
    because they are less constrained by draft restrictions. As they are
    larger than Panamax, a bigger payload can be delivered. They have
    the most flexible specifications.

2.  95,000mt and 730,000bbl: Aframax ships have deadweight tonnage
    limits between 80,000mt-120,000mt. A cargo quantity of 95,000mt
    gives a typical deadweight tonnage once crew, fuel, water and
    provisions are accounted for, avoiding under and overloading. Fuel
    may be especially heavy for this long-haul route.

    ICE Futures Contract requires WTI API Gravity 40.0≤°API≤44.0. Using
    the following formula from the US Energy Information Administration,
    barrels can be derived:

$$Density = \frac{141.5}{131.5 + {^\circ}API}\  \times 999.016,{kgm}^{- 3},$$

> $$Where\ 999.016\ is\ the\ density\ of\ water\ at\ 60℉\ according\ to\ ASTM\ D1250\ 2008\ $$

This results in a 725,000≤bbl≤742,000 range. 730,000bbl is used as the
midpoint rounded to 2sf.

3.  6-Hour laytime clock delay: WIBON clause for NOR is commonly
    combined with a 6 hour laytime buffer. This transfers delay risk to
    the charterer, making P&L more conservative.

4.  72-Hour total laytime, SHINC, non-reversible: As charterparties are
    confidential, laytime is estimated from a dissertation by a
    shipbroker. Non-reversible increases delay risk because fast loading
    cannot compensate for a slow discharge.

5.  \$75,000/Day demurrage rate: Demurrage data is unavailable because
    it is agreed in charterparties. Due to correlations with the other
    variables (especially freight) parametrisation is avoided for the
    same reason as basis exclusion. Weekly 2026 Aframax tanker reports
    from the Baltic Exchange have shown TD25 TCE values upwards of
    \$60,000/day.

6.  110%: Under Incoterms 2020 CIF, the seller's insurance must cover at
    least 110% of the cargo value.

7.  0.5% Insurance premium: Insurance is confidential between the
    contracting parties. Due to correlations with the other variables,
    insurance is not parametrized. Marine cargo insurance is typically
    between 0.1%-0.5%. 0.5% is a conservative estimate, reflecting
    persistently increasing global geopolitical tensions.

8.  €85,000 seaport dues, $\varepsilon_{3}$ and $\varepsilon_{4}$:
    Proforma Disbursement Agreements containing port expense quotes are
    confidential. The port fee formula estimates seaport dues from the
    Port of Rotterdam's tariff book, then treats the rest of the
    expenses as a proportion of the seaport dues.

    Estimating Aframax gross tonnage at 60,000mt, with no ESI score or
    Green Award gives seaport dues equal to €83,884.74 for a 95,000mt
    cargo, using the steps outlined in the tariff book. It is rounded to
    €84,000 (2sf). This value is taken as a proxy of other ports' seaport
    dues, representing those in the US Gulf and Northwestern Europe.

    Triangular distributions are used to parametrise when a variable is
    assumed to be less correlated with the others in the P&L function,
    such that its draw does not imply impossible market combinations. The
    parameters for $\varepsilon_{3}$ and $\varepsilon_{4}$ are chosen on
    the assumption that the other port expenses do not exceed 1.2x seaport
    dues, and outcomes are slightly skewed towards the upper bound.

9.  $\varepsilon_{1}\sim Tri(75,200,125)\ in\ bps$: Financing agreements
    are confidential. Basis points are added to SOFR 30-day average
    because credit risk is elevated for physical commodity financing.
    The parameters are picked such that the bank has reasonable
    confidence in the ability of the borrower.

10. $\varepsilon_{2}\sim Tri( - 0.002,0.00467,0.001)$: The parameters
    are picked such that the distribution captures losses through
    handling, evaporation or quality degradation, and also gains through
    measurement error. The mode and max value represent a skew towards
    losses.

11. $t_{scheduling\ lag}\sim Tri(3,15,5)$: The model assumes the USG
    counterparty agrees to delivery for a near laycan window, as the
    arbitrage opportunity is fleeting. The parameters reflect the close
    proximity of Houston to the Gulf's export terminals.

12. $t_{settlement\ lag}\sim Tri(5,30,10)$: 30 Days is the SPA's limit,
    whilst a settlement delay of 5 represents smooth administration. The
    left skew from 10 as the modal outcome assumes the USG counterparty
    is reliable.

13. EV/CVaR~0.05~ ≥ 1: Risk governance at desk level is confidential. A
    ratio of 1 implies for every unit increase of severe left-tail
    outcomes, another unit of return must be generated. This aims for a
    conservative risk strategy given the exclusion of hedging. The rule
    only recommends trades with a significant upside probability to
    match the risk warehousing of an unhedged, floating sell leg.

14. BL, Discharge and Laytime Timings: Statement of Facts documents are
    unavailable, which would contain the full operational timestamps of
    the Aframax. In practice, hose disconnection ends the laytime clock
    at origin and destination ports, triggers the issue of the Bill of
    Lading at the origin port and marks discharge completion at the
    destination port. The vessel tracking data obtained has the
    following timestamps:

    Origin: Port limit arrival, berth arrival, berth departure and port
    limit departure.

    Destination: port limit arrival, berth arrival, berth departure and
    port limit departure.

    BL is approximated to the date of origin port limit departure whilst
    discharge approximated to the date of berth departure for destination
    port. BL is not issued at berth departure for ease of implementation,
    as the mapping distorts exposure windows in the timeline. Usually, it
    is very common for berth departure and port limit departure to fall on
    the same day. Laytime clock ends at berth departure for origin and
    destination ports as it is the closest available timestamp.


Limitations:

1.  The ability of the simulation algorithm to include correlations
    between the variables of the P&L function is heavily dependent on
    data availability for bootstrapping. Confidentiality in SPAs,
    Charterparties and Financing agreements reduces the accuracy of the
    simulation by forcing estimations.

2.  Whilst triangular distributions are practical, they understate tail
    risk because of the fixed boundaries. This is partially offset by
    conservative estimates.


Sources

https://www.ice.com/publicdocs/contractregs/179_SECTION_7A1.pdf

<https://www.eia.gov/totalenergy/data/monthly/pdf/sec12_8.pdf>

<https://www.portofrotterdam.com/sites/default/files/2025-12/port-tariffs-and-conditions-port-of%20-rotterdam-2026.pdf>

<https://www.steamshipmutual.com/publications/articles/notice-readiness-faqs#:~:text=It%20is%20also%20common%20for,reasons%20such%20as%20weather7>.

<https://icsireland.ie/pdfs/ICS-LAYTIME.pdf>

<https://www.balticexchange.com/en/data-services/WeeklyRoundup/tanker/news/2025/tanker-report-week-49.html#:~:text=In%20the%20Mediterranean%2C%20the%20rate,by%2011%20points%20to%20WS240>.

<https://www.balticexchange.com/en/data-services/WeeklyRoundup/tanker/news/2026/tanker-report-week-10.html>

<https://cargoinsurepro.com/how-much-does-marine-cargo-insurance-cost/>

https://rosewoodfinance.co.uk/trade-finance/



.


