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





.



