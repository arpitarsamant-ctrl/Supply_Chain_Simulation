# Galileo Optical Instrument: Supply Chain Decision Under Uncertainty

**Team 1** | Arpita Ram Samant, Victor Ostolaza, Muhammad Sawaiz Fatar, Vedaant Rath, Sam Sheng  
Duke University, Fuqua School of Business | DECISION 521

---

## The Business Problem

Galileo Optical Instrument Ltd. is a Chinese manufacturer that secured a contract to deliver up to 16,000 binoculars to a major U.S. client by November 15th, 2019. Each binocular requires two eyepieces, meaning Galileo needed 32,000 eyepieces total. Their existing supplier in Taizhou was responsible for producing all of them until Typhoon Lekima hit the region in August 2019, flooding the plant and making production uncertain.

Galileo identified a backup supplier in Kunshan that could deliver eyepieces reliably, but at a higher price. The key question: **how many eyepieces should Galileo order from the Kunshan supplier?**

Order too few and Galileo risks falling short of eyepieces, losing binocular sales and being stuck with unused parts. Order too many and Galileo overpays for eyepieces that may not be needed if Taizhou recovers quickly. Orders must be in multiples of 1,000 and are capped at a premium price above 15,000 units.

---

## Part A: Tornado Analysis

Before building a simulation, the team used a deterministic Excel model to understand which uncertain variables had the biggest impact on profit. By varying each input between its low and high estimates (10th and 90th percentiles) while holding everything else at its base case, we constructed a tornado chart to rank the most influential uncertainties.

**Key findings from Part A:**

When the Kunshan order quantity is set to zero, profit is almost entirely driven by whether Taizhou recovers in time. The two biggest variables were how many days until Taizhou resumed production (a swing of roughly 410k RMB) and Taizhou's daily production capacity once it resumed (a swing of roughly 365k RMB). The RMB to USD exchange rate was a distant third. Salvage value of the eyepiece had zero impact because with no Kunshan backup, Galileo never ends up with surplus eyepieces.

When the Kunshan order is set to 15,000, the risk profile changes significantly. With supply more protected, the exchange rate became the largest driver of profit variance. Labor cost per binocular moved up to second, and Taizhou's capacity was third. Taizhou's resume time dropped to near-zero impact, because even if Taizhou is slow, Kunshan has already covered most of the need.

---

## Part B: Monte Carlo Simulation

Using the additional information gathered from Galileo's conversation with the Taizhou supplier owner, the team built a full simulation model in Python to evaluate different Kunshan order quantities and their tradeoffs between expected profit and downside risk.

### Modeling the Uncertainties

**Taizhou disruption scenario** is modeled as a discrete random draw with three possible outcomes: optimistic (20% chance, all tools functional), most likely (60% chance, machines damaged but fixable), and pessimistic (20% chance, machines need replacement). This scenario then drives both the resume time and the production capacity.

**Taizhou resume time** is drawn from a piecewise uniform distribution fitted to 10-50-90 percentile assessments that vary by scenario. In the optimistic case the median is 28 days; in the most likely case it is 35 days; in the pessimistic case it is 42 days.

**Taizhou daily capacity** is fixed at 1,000 eyepieces per day in the optimistic and most likely scenarios. In the pessimistic scenario it is uncertain, fitted to a 10-50-90 of 400, 500, and 800 eyepieces per day using a piecewise uniform distribution.

**RMB to USD exchange rate** is modeled with a normal distribution, with a mean of 7 and standard deviation derived from the 10th percentile of 6.7.

**Labor cost per binocular** uses a piecewise uniform distribution fitted to 10-50-90 percentiles of 18, 22.5, and 25.5 RMB.

**Eyepiece salvage value** is modeled as a mixed distribution: with 20% probability Galileo receives a future order and the salvage value is 5 RMB; otherwise the salvage value is drawn from a uniform distribution between 2 and 4 RMB.

### How the Simulation Works

For each trial, the model draws random values for all uncertain inputs, computes Galileo's total revenue (binocular sales plus salvage) and total costs (parts, eyepieces from both suppliers, and labor), and returns a profit figure. This is repeated 10,000 times per order quantity tested. Order quantities are swept in multiples of 1,000 from 0 to 32,000.

The output is a distribution of profits for each order quantity, which lets us directly compare the expected profit, the risk of a loss, and the worst-case downside across different ordering strategies.

---

## Tools and Tech Stack

- **Python** (NumPy, Pandas, SciPy, Matplotlib)
- **Custom utility modules**: `model_uncertainty.py` for fitting distributions from 10-50-90 percentile assessments, `simresults_charts.py` for visualizing simulation outputs
- **Excel** for the deterministic tornado model in Part A
- **PowerPoint** for the client-facing presentation to Pinpin

---

## Files in This Repo

| File | Description |
|---|---|
| `Galileo_B_2026_Final.ipynb` | Full simulation model and analysis in Python |
| `Galileo_Solution_Team_1.xlsx` | Deterministic Excel model and tornado charts from Part A |
| `Galileo_Optical_Instrument_Ltd.pptx` | Presentation deck summarizing findings and recommendation |
| `Galileo_Part_A.pdf` | Part A case document |
| `Galileo_Optical_Instrument_Ltd_B.pdf` | Part B case document with supplier conversation and updated uncertainty estimates |
| `Galileo_Optical_Instrument_Ltd_Cover_A_2026.pdf` | Assignment cover sheet |
