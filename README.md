# FOB USG-NWE Voyage P&L Model
Historical Monte-Carlo Simulation of WTI-Brent crude arbitrage under market and operational risk.

About: Independently designed and implemented in Python by Adam Palmer, second year undergrad at the University of Nottingham (19/12/25-present). 

The model is used as a decision tool for evaluating the commercial viability of a single Aframax shipment of WTI crude from the US Gulf Coast to Northwest Europe, given the current WTI-Brent spread. This is achieved by simulating voyage profitability under the various combinations of market and operational risk factors. Live WTI Houston FOB and WTI-Brent spread user inputs generate the following outputs: P&L distributions, expected profit, expected shortfall and decision surfaces. A trade is recommended if its expected profit sufficiently compensates for the risk of loss.

The USG-NWE export flow is monitored for several reasons. As the markets are geographically separated by transport time and costs, regional prices do not converge. The spread persists because transportation constraints are binding and operational execution risk is large. WTI exports to Northwest Europe are specifically common due to compatible refinery infrastructure. As WTI and Brent crude have similar chemical compositions, NWE refineries search for opportunities to increase margins by substituting local Brent with cheaper WTI barrels.  In the US shale boom of 2011-2014, the oversupply of crude oil caused heavy WTI discounting, exacerbated by the US export ban. When the ban was lifted in 2015, exports surged and the spread reduced, often resulting in thin arbitrage margins. As arbitrage profitability is no longer obvious, the model provides decision-making insight.



