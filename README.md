# Inventory Stock vs. Nonstock Decision

This document describes the mathematical logic behind deciding whether an item should be **Stocked** or **Nonstocked**. Essentially, we compare:

- **Non-Stock Cost** (the cost to replenish on-demand each time there’s a need),  
  against  
- **Stock Cost** (the cost to carry inventory plus the cost to replenish less frequently).

If the **Stock Cost** is lower, we Stock the item; otherwise, we keep it Nonstocked.

---

## 1. Non-Stock Cost

When an item is **Nonstocked**, each “hit” or usage event triggers a separate replenishment. Mathematically:

$$
\text{Non-Stock Cost}
= \text{(Annual Number of Replenishments)} \times \text{(Replenishment Cost per Order)}
$$

- **Annual Number of Replenishments**: This is essentially the number of usage “hits” per year (Hits per Year).  
- **Replenishment Cost per Order ($R\text{-Cost}$)**: The administrative, shipping, and/or handling cost of creating and receiving one order.

So, in short:

$$
\text{Non-Stock Cost}
= \text{Hits per Year} \times R\text{-Cost}.
$$

---

## 2. Stock Cost

When an item is **Stocked**, we place larger, less-frequent orders, so we reduce the number of replenishments—but we pay an additional carrying cost because we hold inventory.

$$
\text{Stock Cost} = \text{Cost to Replenish (Stock)} + \text{Cost to Carry (Stock)}
$$

### 2(A) Cost to Replenish (Stock)

$$
\text{Cost to Replenish (Stock)}
= (\text{Number of Orders per Year}) \times R\text{-Cost}
$$

Where:

- **Number of Orders per Year** is computed by dividing the total quantity sold per year by the order quantity $\text{OQ}$:
  
$$
\text{Number of Orders per Year} = \frac{\text{Quantity Sold per Year}}{\text{OQ}}
$$

### 2(B) Cost to Carry (Stock)

$$
\text{Cost to Carry (Stock)}
= (\text{Average On-Hand Quantity}) \times (\text{Carrying-Cost Rate}) \times (\text{Unit Cost})
$$

- **Average On-Hand Quantity** follows the “sawtooth” pattern assumption:  
  1. When we order $\text{OQ}$ units, the inventory level goes from **Safety Stock** up to **(Safety Stock + OQ)**.  
  2. It then gradually depletes at a steady rate until we reorder.  
  3. The average of that cycle is often taken to be $\frac{\text{OQ}}{2}$ above the safety stock.
  
$$
\text{Average On-Hand Quantity}
= \frac{\text{OQ}}{2} + \text{Safety Stock}
$$

- **Carrying-Cost Rate** ($K\text{-Cost}$) is the annual fraction of the inventory’s value that you pay in holding costs (e.g. cost of capital, storage, shrinkage, insurance, etc.).

- **Unit Cost** might be an average or current purchase cost for the item.

**Note**: Average On-Hand Quantity here assumes we replenish when $\text{PNA}$ (Purchase Net Available) is at $\text{OP}$ (Order Point). This is a **simplification**. If you have a more accurate formula or logic, please comment on this repo. We err on the side of favoring **stocking** an item, since this slightly lowers the calculated average on-hand quantity.

---

## 3. Determining the Order Quantity (OQ)

The **Order Quantity (OQ)** is chosen to balance ordering cost against carrying cost—often via an **Economic Order Quantity (EOQ)** calculation, with practical minimum/maximum limits.

### 3(A) EOQ Formula

A common form of the EOQ formula is:

$$
\text{EOQ}
= \sqrt{\frac{2 \times (\text{Annual Demand}) \times (\text{Order Cost})}{(\text{Carrying Cost per Unit})}}
$$

In practice, you might see a variation depending on how you measure annual demand and carrying cost. For example, if you define:

- $\text{Annual Demand} = 12 \times \text{Usage (per month)}$,  
- $\text{Order Cost} = R\text{-Cost}$,  
- $\text{Carrying Cost per Unit} = K\text{-Cost} \times \text{Average Unit Cost}$,

then you might see something like:

$$
\text{EOQ}
= \sqrt{\frac{24 \times R\text{-Cost} \times \text{Usage}}{K\text{-Cost} \times \text{Average Unit Cost}}}
$$

### 3(B) Applying Min/Max Constraints

Real-world scenarios often impose minimum and maximum bounds on the EOQ (for example, if we can’t order more than 12 months’ usage or we don’t want fewer than half a month’s usage). Symbolically:

$$
\text{OQ} = \max\left(
  \min\left(\text{EOQ},\ \text{MaxThreshold}\right),\ 
  \text{MinThreshold}
\right)
$$

Where:

- **MaxThreshold** could be something like $12 \times \text{Monthly Usage}$.  
- **MinThreshold** might be something like $0.5 \times \text{Monthly Usage}$.  

Thus, the final $\text{OQ}$ respects both the theoretical EOQ calculation and your operational constraints.

---

## 4. Decision: Stock or Nonstock?

Finally, you compare:

$$
\text{Non-Stock Cost}
\quad\text{vs.}\quad
\text{Stock Cost}
$$

- If $\text{Stock Cost} < \text{Non-Stock Cost}$, you **Stock** the item.  
- Otherwise, you **Nonstock** it.

This comparison ensures you choose the approach with the lowest overall cost.
