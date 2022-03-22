---
author:
- Jiehong Kong
date: 2017-05-02
title: "Paper Title (use style: paper title)"
---

Operational use of marginal cost curves for hydropower plants as
decision support in real-time balancing markets

::: multicols
2

Hans Ivar Skjelbred

Jiehong Kong

Department of Energy Systems

SINTEF Energy Research

Trondheim, Norway

Email:
[[HansIvar.Skjelbred@sintef.no]{style="color: 0.019607844,0.3882353,0.75686276"}](mailto:HansIvar.Skjelbred@sintef.no)

Tellef Juell Larsen

Fredd Kristiansen

Department of Energy Management

Statkraft Energi AS

Oslo, Norway
:::

::: multicols
2
:::

::: multicols
2 ***Abstract*---When participating in the real-time balancing markets,
hydropower producers keep making decisions about how much power they are
willing to sell or purchase at what prices from the markets. Due to the
specific market characteristics, the decisions should be made quickly,
placing high demand on the computation time of rescheduling. In this
paper, we present a practical method to determine an hourly bidding
curve for a plant. It is applicable to both balancing and intraday
markets. We heuristically calculate the marginal cost for all the
operating points, covering the entire working area for the plant and
including all the physical limitations and reserve obligations in other
markets. Heuristics are proposed to reduce the computation time without
neglecting significant practical features. Numerical results based on a
real-world case study demonstrate the advantage of the proposed method
in terms of computation time and solution quality.**

***Index Terms*---Heuristic algorithms, hydroelectric power generation,
power generation economics**

# Introduction

The growth of intermittent renewable energy, such as wind and solar, in
the power system and stronger market connections increase the need for
the efficient and reliable balancing services, both in terms of capacity
and energy. Being a flexible electric power source, hydropower can
regulate its generation level and reduce the power fluctuations in the
system. Therefore, although the hydropower producers in the Nordic
market primarily benefit from selling energy in the day-ahead
electricity market, they are also actively participating in the
balancing markets to gain more profit by offering the unused
flexibility.

In real-time balancing markets, the trading of energy takes place every
day around the clock and is closed shortly before the period of
delivery. Thus, the response time for hydropower producers to submit
bids or accept offers is very crucial. For example, in the continuous
intraday market ELBAS covering the Nordic, Baltic, UK and German
markets, trades follow a first-come, first-served principle and can be
settled whenever a market participant accepts an offer of another market
participant \[1\]. Given the current production plan for a plant, a
producer must make a quick but reasonable decision about how much power
they are willing to sell or purchase at what prices from the markets.
This decision involves a new production plan, which must be rescheduled
in a both technically feasible and economically efficient manner. That
is, which units will be dispatched and at what generation level they
will run should be determined. In addition, the new operating cost,
including the start/stop cost due to the shift in working units, should
be quantified, which is one of the key indicators to assist the producer
to decide which bids or offers should be carried out in the markets.

In this paper, we present a novel version of a method used as decision
support to the process described above. It provides a viable bidding
curve per hour for each hydropower plant. It is applicable to both
balancing and intraday markets. We heuristically calculate the marginal
cost for all the operating points, covering the entire working area for
a plant and including all the physical limitations and reserve
obligations in other markets. The sum of start cost and stop cost,
referred to as the deviation cost, is also taken into consideration. The
dispatch decision is specified for each selected bidding point. In order
to provide the result within reasonable time but without neglecting
significant practical features, we present two new specific heuristics
to tackle the problem.

Since the bidding and scheduling problems are intimately connected
\[2\], the majority of published bidding strategies for hydropower,
either in one electricity spot market \[3\]-\[5\], or in multiple
markets \[6\]-\[8\], focus on optimizing the production scheduling given
a deterministic or stochastic pricing. That is, the optimal set of bids
is subject to *a priori* price-based criteria. By contrast, our method
provides a realistic bidding strategy extending from the minimum
generation to the maximum output of a plant. In other words, it is the
generating capacity in a plant that determines the bidding range. This
approach conforms to the trading characteristics in the real-time
markets, e.g. ELBAS, where prices are set based on a pay-as-bid basis
for all transactions rather than a common market clearing price in the
day-ahead market.  

The proposed approach is implemented to an operational hydropower
scheduling model, Short-term Hydro Optimization Program (SHOP), used by
most large hydropower producers in Scandinavia \[9\]. It considers
complex cascaded watercourses with technical constraints and various
market constraints. It also handles the modelling challenge of nonlinear
and nonconvex elements together with state-dependencies.

The paper is outlined as follows. We begin, in Section
[2](#bkm:Ref474313466){reference-type="ref"
reference="bkm:Ref474313466"}, by providing a brief description of the
hydrological balance in the reservoirs and power generation in the
plants. Section [3](#bkm:Ref474836084){reference-type="ref"
reference="bkm:Ref474836084"} presents the heuristics we employ to
generate the bidding curve. The advantage of the proposed method in
terms of computation time and solution quality is demonstrated in
Section [4](#bkm:Ref474518919){reference-type="ref"
reference="bkm:Ref474518919"}. Finally, conclusions are drawn in Section
[5](#bkm:Ref474313902){reference-type="ref"
reference="bkm:Ref474313902"}.   

# Problem Description {#bkm:Ref474313466}

In this section, we first derive the hourly water cost for a plant from
the dual value of upstream and downstream reservoir balance constraints,
and then, discuss how the production is calculated when the discharge of
the generating units is given.

## Water Cost for a Plant  

The hydrological balance in the reservoirs features time coupling and
state dependency, which means water release in one time step will affect
the decision in future intervals and discharge in one reservoir will
impact on operation of other objects in the watercourse \[9\]. The dual
value (marginal value or shadow price) of the reservoir balance equation
implies the incremental water value () added by increasing one
additional unit (m^3^) of water in the reservoir. This value is actually
the opportunity cost of using water now versus storing it for later
generation \[8\].

For reservoir $k$  at time period $t$, the hydrological balance can be
formulated as

::: {#bkm:Ref473212972}
+:-------------------------------------+:-----------------------------+
| $V_{\mathit{kt}}=V_{k,t-1}+?$        |                              |
|                                      | *[(]{.upright}[)]{.upright}* |
| $3600{\bullet}\left(\sum             |                              |
| _{j{\in}J_k^{\mathit{UP}}}F_{j,t-\ta |                              |
| u _{\mathit{ik}}}^{\mathit{UP}}-\sum |                              |
| _{j{\in}J_k^{\mathit{DO              |                              |
| WN}}}F_{j,t}^{\mathit{DOWN}}\right)$ |                              |
+--------------------------------------+------------------------------+
:::

where:

$T$   Set of time periods (hourly resolution).

$K$   Set of reservoirs.

$J_k^{\mathit{UP}}$   Set of upstream objects of reservoir $k$.

$J_k^{\mathit{DOWN}}$   Set of downstream objects of reservoir $k$.

$V_{\mathit{kt}}$   Water volume of reservoir $k$  at the end of period
$t$ (m^3^).

$V_{k,t-1}$   Water volume of reservoir $k$  at the beginning of period
$t$, i.e. volume at the end of period $t-1$  (m^3^).

$F_{j,t-\tau _{\mathit{ik}}}^{\mathit{UP}}$   Flow from upstream source
$j$  in period $t-\tau _{\mathit{jk}}$  and $\tau _{\mathit{jk}}$  is
the time delay of water from $j$  to reservoir $k$. The flow can be
nature inflow, discharge through plants or gates, or spilled flow from
other reservoirs (m^3^/s).

$F_{j,t}^{\mathit{DOWN}}$   Flow to downstream source $j$  in period
$t$. The flow can be discharge to plants, bypass or spill gates
(m^3^/s).

We assume that before the producers participate in the real-time
balancing markets, they already have a committed production scheduling
to meet the obligation from the spot market. Therefore, the marginal
water value $\lambda
_{\mathit{kt}}$  for reservoir $k$  at time period $t$, i.e. the dual
value of \[F028?\]1\[F029?\], is available.  

The operating cost for a plant $s$  mainly derives from the water it
uses in that period. It is the difference of the marginal water value
for the immediately upstream reservoir $k$  and downstream reservoir
$k+1$, which is expressed as

::: {#bkm:Ref474838674}
  -- ------------------
     *[()]{.upright}*
  -- ------------------
:::

where:

$S$   Set of plants.

$\mathit{WC}_{\mathit{st}}$   Water cost for plant $s$  at period
$t$ (/m^3^).

$\lambda _{\mathit{kt}}$   Marginal water value of reservoir $k$  at
period $t$, which is the immediately upstream reservoir to plant
$s$ (/m^3^).

$\lambda _{k+1,t}$   Marginal water value of reservoir $k$+1 at period
$t$, which is the immediately downstream reservoir to plant $s$ (/m^3^).

Note that in a cascaded watercourse, the marginal water value normally
becomes larger when moving upward because more electricity can be
produced with the same amount of water, i.e.
$\lambda _{\mathit{kt}}>\lambda _{k+1,t}$. If there is no downstream
reservoir below a plant, i.e. water is released to the sea,
$\lambda _{k+1,t}$  is set to 0.

## Production for One Operating Point

[The water level of reservoir ]{lang="en"} $k$ [ is interpolated
according to the piecewise-linear curve of the volume and the water
level. The gross head of plant ]{lang="en"} $s$ [ is hence updated by
\[F028?\]3\[F029?\]. We use the initial water volume of period
]{lang="en"} $t$ [ as a constant level for the whole hour. If the water
level of downstream reservoir ]{lang="en"} $k+1$ [ is higher than the
outlet line of the plant, it will be used in the head calculation
instead of the outlet line.]{lang="en"}

::: {#bkm:Ref474537052}
+:-------------------------------------------------+:-----------------+
| $\mathit{GH}_{\mathit{st}}=L                     |                  |
| _{k,t-1}\left(V_{k,t-1}\right)-\mathit{MAX}\left | *[()]{.upright}* |
| [L_{k+1,t-1}\left(V_{k+1,t-1}\right),L_s\right]$ |                  |
|                                                  |                  |
| $s{\in}S,t{\in}T$                                |                  |
+--------------------------------------------------+------------------+
:::

where:

$\mathit{GH}_{\mathit{st}}$   Gross head of plant $s$  at period
$t$ (m).

$L_{k,t-1}$   Water level of reservoir $k$  at the beginning of period
$t$, which is interpolated on the basis of $V_{k,t-1}$ (m).

$L_{k+1,t-1}$   Water level of reservoir $k+1$  at the beginning of
period $t$, which is interpolated on the basis of $V_{k+1,t-1}$ (m).

$L_s$   Outlet line of plant $s$ (m).

[In real-world operation, the head loss ]{lang="en"}in the main tunnel
and the penstock that unit $i$  connects to[ should not be neglected. It
can be represented as ]{lang="en"}a quadratic equation of the total flow
going through the main tunnel/penstock. [The net head ]{lang="en"}is
calculated as

::: {#bkm:Ref474682853}
+:-------------------------------------------------+:-----------------+
| $\mathit{NH}_{\mathit{ist}}=GH_{\mathit{st}}$    |                  |
|                                                  | *[()]{.upright}* |
| $-\alpha                                         |                  |
| _{\mathit{main}}{\bullet}\left(\sum _{i{\in}I_{s |                  |
| ,\mathit{main}}}q_{\mathit{ist}}\right)^2-\alpha |                  |
| _{\mathit{pen}}{\bullet}\left(\sum _{i{\         |                  |
| in}I_{s,\mathit{pen}}}q_{\mathit{ist}}\right)^2$ |                  |
|                                                  |                  |
| $i{\in}I_s,s{\in}S,t{\in}T$                      |                  |
+--------------------------------------------------+------------------+
:::

where:

$I_s$   Set of units in plant $s$.

$I_{s,\mathit{main}}$   Set of units that connect to main tunnel in
plant $s$.

$I_{s,\mathit{pen}}$   Set of units that connect to penstock
$\mathit{pen}$  in plant $s$.

$NH_{\mathit{ist}}$   Net head of unit $i$ in plant $s$  at period
$t$ (m).

$\alpha _{\mathit{main}}$   Loss factor for main tunnel.

$\alpha _{\mathit{pen}}$   Loss factor for penstock $\mathit{pen}$.

$q_{\mathit{ist}}$   Flow going through unit $i$  in plant $s$ * *in
period $t$ (m^3^/s).

[For a generating unit ]{lang="en"} $i$ [ in plant ]{lang="en"} $s$[,
the power ]{lang="en"}production, in \[F028?\]5\[F029?\], [depends on
the net head and the flow going through that unit. It also relies on the
generator efficiency and head-dependent turbine efficiency]{lang="en"}.

::: {#bkm:Ref473293512}
+:--------------------------------------------+:--+:-----------------+
| $mw_{\mathit{ist}}=0.001$                   |   |                  |
|                                             |   | *[()]{.upright}* |
| ${\bullet}\eta _i^{\mathit{GEN}}            |   |                  |
| \left(mw_{\mathit{ist}}\right){\bullet}\eta |   |                  |
| _i^{\mathit{TURB}}\left(q_{\mathit{ist}},   |   |                  |
| NH_{\mathit{ist}}\right){\bullet}G{\bullet} |   |                  |
| NH_{\mathit{ist}}{\bullet}q_{\mathit{ist}}$ |   |                  |
|                                             |   |                  |
| $i{\in}I_s,s{\in}S,t{\in}\mathit{T.}$       |   |                  |
+---------------------------------------------+---+------------------+
:::

where:

$mw_{\mathit{ist}}$   Power produced by unit $i$  in plant $s$ * *in
period $t$ (MW).

$\eta _i^{\mathit{GEN}}$   Generator efficiency of unit $i$, which is
interpolated on the basis of production $mw_{\mathit{ist}}$.

$\eta _i^{\mathit{TURB}}$   Turbine efficiency of unit $i$, which is
interpolated on the basis of flow $q_{\mathit{ist}}$  and net head
$NH_{\mathit{ist}}$.

$G$    Gravity value, default setting is 9.81 (m/s^2^).

Based on \[F028?\]4\[F029?\] and \[F028?\]5\[F029?\], if the discharge
for each unit is given (i.e. one possible operating point for the
plant), we can precisely calculate the corresponding production, taking
the head loss into consideration. This transformation from the flow
discharge to the power generation is implicitly done by the
functionality in SHOP.

# Heuristics {#bkm:Ref474836084}

In this section, four heuristics are presented in a logical sequence
that one can follow to define the hourly bidding curve for a plant. The
first two heuristics are introduced to significantly reduce the
computation time. The chosen operating points in the selected unit
combinations cover the entire working area for the plant. The third
heuristic is presented to define the average cost and marginal cost for
each operating point. It is originated from the economic theory, but
adapted to the practice of hydropower operation. The last heuristic is
proposed to select the best bidding points, taking into consideration
the cost of deviation from the original production plan.

## Heuristic I: Define the Unit Combinations

To find a bidding curve covering the entire viable working area for a
plant, we first need to determine the production limits for the plant,
i.e. horizontal axis of the curve. We introduce the idea of *unit
combination* as the basis for grouping. For a plant having $N$
 generating units, the number of possible unit combinations is
$2^N$ (the left of Fig. 1). For each unit combination, its production
limits are restricted by the generating capacity of the units in that
combination. Then these combinations constitute the whole operating
range for the plant.  

However, if there are identical units in a plant, which is common in
real-world application, the unit combinations that separately contain
these units overlap the working area. Take a plant having 3 identical
units as example (Fig. 1). Combinations 2, 3, and 4 give the same
operating limits. So do Combinations 5, 6, and 7. Therefore, Heuristic I
is proposed:

-    Heuristic I: We establish the unit combinations based on the *type*
    of units instead of the *number* of units. If the units are defined
    as same type, it means these units are identical and connected to
    the same penstock as well as main tunnel.

If the units belong to the same type, only one combination will be
chosen from those combinations with the same number of units. To avoid
unnecessary operation shifting, we also assume that the working units
have the priority to be included in the chosen combinations. Therefore,
according to Heuristic I, from the combinations with one unit, only the
one including the working unit G2 is selected; from the combinations
with two units, the one containing the working units G2 and G3 is chosen
(the right of Fig. 1).

To sum up, if the number of identical units for type $r$  is $N_r$  and
there are $R$  types of units in one plant, then the number of unit
combinations becomes $\prod _{r=1,{\dots},R}(N?_r+1)$. For a plant where
all the $N$  units belong to one type, the number of unit combinations
is reduced from $2^N$  to $N+1$.

\[Warning: Draw object ignored\]

1.  [Example of ]{lang="en"}[defination of unit combinations]{lang="en"}

## Heuristic II: Define the Operating Points in Each Selected Unit Combination

For each unit, the generating capacity spans from the minimum to the
maximum limit, deducting the scheduled obligations in other reserve
markets. If we equally make $X$  segments within this range, i.e. there
are $X+1$  generating levels for this unit, then the number of operating
points in a unit combination that includes $N$  units is
$\left(X+1\right)^N$ (the left of Fig. 2). The computation time grows
exponentially with the increase of the number of units.

In practice, if two or more Francis or Kaplan turbines of the same type
are in operation, they are prone to run symmetrically. That is, the
optimal dispatch is more likely to be achieved when the units run at the
same levels (the right of Fig. 2) instead of operating at different
levels, e.g. one runs at best point whereas the other runs at maximum.
Given this premise, we suggest Heuristic II:

-    Heuristic II: For the Francis or Kaplan turbines of the same type
    in one unit combination, only the symmetrical operating points will
    be selected.

Therefore, if there are $R$  types of units in one unit combination,
then the number of the operating points is $\left(X+1\right)^R$. For a
combination consisting of $N$  units of one type, the number of
operating points (with $X+1$  generating levels) is reduced from
exponential $\left(X+1\right)^N$  to constant $X+1$.

It is worth mentioning that, different from Francis or Kaplan turbines,
the symmetrical operation is not applicable for Pelton turbines. The
power-discharge curve for Pelton turbines is normally nonconvex.
However, the convexity of the power-discharge curve is a necessary
condition for Heuristic II to be valid. Therefore, Pelton turbines are
always treated as unique even though they are the same type.

\[Warning: Draw object ignored\]

1.  Example of selection of operating points in one unit combination

## Heuristic III: Calculate the Average Cost and Marginal Cost for One Operating Point

For a given operating point $p$  in one specific unit combination $c$,
the generation cost for this point is the opportunity cost of the water
used. In the previous section, we have presented how the hourly water
cost is defined, in \[F028?\]2\[F029?\], and how the production can be
accurately obtained when the discharge of the units is decided, in
\[F028?\]5\[F029?\]. Therefore, we denote the average cost for this
operating point by

+:--------------------------------------------+:--+:-----------------+
| $\mathit{ac}_{\mathit{cst}}^p=\frac         |   |                  |
| {3600{\bullet}WC_{\mathit{st}}{\bullet}\sum |   | *[()]{.upright}* |
| _{i{\in}I_c}q_{\mathit{ist}}^p}{\sum        |   |                  |
|  _{i{\in}I_c}\mathit{mw}_{\mathit{ist}}^p}$ |   |                  |
|                                             |   |                  |
| $p                                          |   |                  |
| {\in}P_c,c{\in}C,s{\in}S,t{\in}\mathit{T.}$ |   |                  |
+---------------------------------------------+---+------------------+

where:

$C$   Set of unit combinations.

$I_c$   Set of units in unit combination $c$.

$P_c$   Set of operating points in unit combination $c$.

$ac_{\mathit{cst}}^p$   Average cost for the operating point $p$  in
unit combination $c$  in plant $s$ * *in period $t$ (/MWh).

In economics, marginal cost is the change in the opportunity cost that
arises when the quantity produced has an increment by one unit. In
contrast to the transformation from discharge to production, it is much
more complicated to find the discharge by a given production. In
addition, the power produced is infinitely divisible. Therefore, we put
forward Heuristic III:

-    Heuristic III: The marginal cost for one operating point is the
    change in the opportunity cost of water involved as a result of an
    infinitesimally small increase in the discharge of the units, which
    is expressed as

+:--------------------------------------------+:--+:-----------------+
| $\mathit{mc}_{\mathit{cst}}^p$              |   |                  |
|                                             |   | *[()]{.upright}* |
| $=\frac                                     |   |                  |
| {3600{\bullet}WC_{\mathit{st}}{\bullet}\sum |   |                  |
| _{i{\in}I_c}\left(q_{\mathi                 |   |                  |
| t{ist}}^p+{\Delta}q_{\mathit{ist}}^p\right) |   |                  |
| -3600{\bullet}WC_{\mathit{st}}{\bullet}\sum |   |                  |
| _{i{\in}I_c}q_{\mathi                       |   |                  |
| t{ist}}^p}{\sum _{i{\in}I_c}\left(\widetild |   |                  |
| e{\mathit{mw}}_{\mathit{ist}}^p\right)-\sum |   |                  |
| _{i{\in}I_c}\mathit{mw}_{\mathit{ist}}^p}$  |   |                  |
|                                             |   |                  |
| $p                                          |   |                  |
| {\in}P_c,c{\in}C,s{\in}S,t{\in}\mathit{T.}$ |   |                  |
+---------------------------------------------+---+------------------+

where:

$mc_{\mathit{cst}}^p$   Marginal cost for the operating point $p$  in
unit combination $c$  in plant $s$ * *in period $t$ (/MWh).

${\Delta}q_{\mathit{ist}}^p$   A small increment in the discharge of
unit $i$  in unit combination $c$  in plant $s$ * *in period $t$,
${\Delta}q_{\mathit{ist}}^p=0.001{\bullet}\frac{q_{\mathit{ist}}^p}{\sum
_{i{\in}I_c}q_{\mathit{ist}}^p}$, (m^3^/s).

$\widetilde{\mathit{mw}}_{\mathit{ist}}^p$   Power produced by unit $i$
 in plant $s$ * *in period $t$  when there is a small increment in the
discharge of the units (MW).

## Heuristic IV: Determine the Bidding Points

After the average cost and marginal cost for all the chosen operating
points in all the selected unit combinations are calculated, we face the
question of how to determine the bidding points that span the entire
working area for the plant.

According to the economic theory, in a competitive market, the supply
curve for a producer should be the portion of the marginal cost curve
above its intersection with the average cost curve \[10\]. However, when
we calculate the generation cost for each operating point, we do not
take into account the start/stop cost incurred due to the shift from
working unit combination (i.e. the unit combination including all but
only the working units in the original plan) to other unit combinations.
Take the right of Fig. 1 as example. NEW Combination 3, as the working
unit combination, consists of the working units G2 and G3. NEW
Combination 2 only contains G2, which means the cost of deviation from
the original production plan is the stop cost for G3. Similarly, NEW
Combination 4 comprises all the three units. Then the deviation cost
should be the start cost for G1.

The deviation cost cannot be neglected. Otherwise, the corresponding
bids to the market would distort the profit margins. For instance, it
might be more cost effective for the producer to adjust the generation
within the working unit combination than to turn the working units off
and switch other units on, even if operating points in another unit
combination give higher profit.

When jumping from one unit combination to another, i.e. moving from
operating point $p$  in unit combination $c$  to operating point $p'$
 in unit combination $c'$, equation \[F028?\]8\[F029?\] should be
followed to ensure the jump is more profitable.

::: {#bkm:Ref476066429}
+:--------------------------------------------+:--+:-----------------+
| $\left(mc_{\mathit{cst}                     |   |                  |
| }^p-ac_{\mathit{cst}}^p\right){\bullet}\sum |   | *[()]{.upright}* |
| _{i{\in}I_c}\mat                            |   |                  |
| hit{mw}_{\mathit{ist}}^p-DC_{\mathit{cst}}$ |   |                  |
|                                             |   |                  |
| $<\left(mc_{c'\mathit{st}}^{p^'}-a          |   |                  |
| c_{c'\mathit{st}}^{p^'}\right){\bullet}\sum |   |                  |
| _{i{\in}I_{c'}}\mathit{m                    |   |                  |
| w}_{\mathit{ist}}^{p^'}-DC_{c'\mathit{st}}$ |   |                  |
|                                             |   |                  |
| $p{\in}P_c,c{\in}C,p^'{\in                  |   |                  |
| }P_{c'},c'{\in}C,s{\in}S,t{\in}\mathit{T.}$ |   |                  |
|                                             |   |                  |
|                                             |   |                  |
+---------------------------------------------+---+------------------+
:::

where:

$\mathit{DC}_{\mathit{cst}}$   Cost of deviation from the original
production plan for unit combination $c$  in plant $s$ * *in period
$t$ ().

Finally, Heuristic IV is applied to pick out the best points to
constitute a bidding curve spanning the entire production interval. The
procedure starts from the current working point. The left of Fig. 3 is
to determine the bidding points above the working point, while the right
is to decide the bidding points below. Given this bidding curve, the
part above the current working point is the quantity of energy that the
producer is willing to sell and able to produce when the market price is
at the bidding value or higher, whereas the part below the working point
is the amount of power that the producer would rather avoid to generate
but buy from the market if the price is at the bidding value or lower.
  

Note that each point on the bidding curve already contains all the
detailed information about which units are running and what the unit
discharge and production are. So it can be directly used as a new
production plan if this point is accepted in the bidding. Otherwise,
linear interpolation between two points can be employed to get a
feasible solution.

\[Warning: Draw object ignored\]

1.  Flowchart for Heuristic IV

# Case Study {#bkm:Ref474518919}

The numerical results are based on a real-world hydrological case, but
the data have been modified for confidentiality reasons. There are four
power plants in the cascaded watercourse. The production scheduling
after spot clearing (i.e. original production plan) is already obtained
from SHOP. The opportunity cost of the water for every particular hour,
derived from the dual value of the reservoir balance constraint, is
hence available.

We now focus on one plant that has 4 units (Francis turbines). Units G1
and G2 are identical and connected to Penstock 1, whereas the more
efficient units G3 and G4 are also identical and connected to Penstock
2. No capacity is reserved for other ancillary services markets. Based
on the current production plan and the configurations of hydroelectric
facilities (TABLE
[\[bkm:Ref475010424\]](#bkm:Ref475010424){reference-type="ref"
reference="bkm:Ref475010424"}), we sequentially use the Heuristics
presented in Section [3](#bkm:Ref474836084){reference-type="ref"
reference="bkm:Ref474836084"} to generate the bidding curve for the
first hour when the water cost is 0.035789 /m^3^. All the tests were
made on an i7-6600U 2.80 GHz processor with 16 GB of RAM.

1.  [[]{#bkm:Ref475010424 label="bkm:Ref475010424"}Data for Power
    Plant]{.smallcaps}

::: tabular
\|m1.262cm\|m2.405cm\|m2.409cm\|m2.405cm\|   & & & **Start/Stop Cost
()** & & & 285 / 200 & & & 285 / 200 & & & 285 / 200 & & & 285 / 200 & &
& -
:::

Firstly, in light of the definition in Heuristic I, there are 2 types of
units in this plant. Therefore, the number of unit combinations is
reduced from 16 to 9, but the entire production capacity of the plant is
still covered (TABLE
[\[bkm:Ref475012064\]](#bkm:Ref475012064){reference-type="ref"
reference="bkm:Ref475012064"}). Combination 6[ ]{style="color: red"}is
the working unit combination. Shifting from this combination to any
other unit combinations will lead to deviation cost because of the start
and/or stop of the operation in units.

1.  [[]{#bkm:Ref475012064 label="bkm:Ref475012064"}List of the Chosen
    Unit Combinations]{.smallcaps}

::: tabular
\|m1.262cm\|m2.405cm\|m2.409cm\|m2.405cm\|   &

**Units in**

& &

**Deviation Cost**

**()** & & & 400 & & & 685 & & & 200 & & & 970 & & & 485 & & & 0 & & &
770 & & & 285 & & & 570
:::

The number of operating points in one unit combination depends on how
many segments are defined between the unit production limits. The finer
the segments are, the more precise the bidding curve will be, and in
consequence, the more computation time is needed. TABLE
[\[bkm:Ref475020340\]](#bkm:Ref475020340){reference-type="ref"
reference="bkm:Ref475020340"} shows the computation time used to
calculate the production, average cost and marginal cost for each
operating point in Combination 9 where all the four units are included,
without and with Heuristic II, respectively. Without Heuristic II, the
number of operating points [is exponential to the number of units and
the computation time becomes unacceptable, for daily operation, after
segments are increased to 49. By contrast, after Heuristic II is
employed, the number of operating points becomes ]{lang="en"}exponential
to the number of types. The computation time, for all the choices of
segments, is reduced to the operationally acceptable level.

Following the steps in Heuristics III and IV, the production, average
cost and marginal cost for each operating point in the unit combinations
can be calculated and finally the bidding strategy can be determined.
The first two charts in Fig. 4 display the result without and with
Heuristic II applied (the number of segments is both set as 99). As we
can see, there is no obvious difference between them. With the
implementation of Heuristic II, the computation time is significantly
reduced while the result quality is not notably scaled down.

Note that some points for which the marginal cost is below the average
cost are selected in Combination 6 (the working unit combination). It
happens because there exists stop cost of G4. If we take way the start
cost and stop cost for all the units, the shift between the unit
combinations occurs as long as the marginal cost is lower than the
average cost (the third chart in Fig. 4).

1.  [[]{#bkm:Ref475020340 label="bkm:Ref475020340"}Comparison of
    Computation Time]{.smallcaps}

::: tabular
\|m1.262cm\|m1.754cm\|m1.754cm\|m1.754cm\|m1.754cm\| & &\
& &

***Computation time***

& &

***Computation time***

***(millisecond)*** & & & & 1 & & & & 1 & & & & 2 & & & & 17 & & & & 65
:::

\[Warning: Draw object ignored\]

1.  Comparison of the selection of bidding points

# Conclusion {#bkm:Ref474313902}

When participating in the real-time balancing markets, hydropower
producers always need to make decisions not only meeting their physical
positions but also benefiting from trades in the markets. Due to the
specific market characteristics, the decisions should be made quickly,
placing high demand on the computation time of rescheduling. For the
same product, the prices in the real-time markets vary, and therefore,
the bidding strategy based on the possible operating capacity of a
plant, instead of pricing determined *a priori,* can fully exploit the
flexibility and provide a more comprehensive choice for the hydropower
producers.

The contribution of this paper is to propose a practical method that can
assist the producers to trade in the real-time balancing markets. The
hourly bidding curve for one plant is put forward on the basis of the
marginal cost for the operating points, covering the entire feasible
working area for the plant.  It is also demonstrated that the proposed
heuristics are computationally efficient.

In this paper, when we calculate the average cost and marginal cost for
each operating point, we use the constant marginal water value. The
gross plant head is obtained from the original production plan. For a
hydrological system with significant storage, the discharge variation in
a plant within one hour would not lead to considerable difference in
marginal water value, and hence, this assumption is acceptable. However,
it might cause the problem for small-size reservoirs, in which there is
the risk of running out of water within one hour. We also assume that
the bids are decoupled in time and do not consider the impact of the
change in production plan for one hour on the consecutive hours. These
are the issues for further improvement.

##### References 

1.   R. Scharff, and M. Amelin, "Trading behaviour on the continuous
    intraday market ELBAS," *Energy Policy,* vol. 88, pp. 544-557,
    Jan, 2016.

2.   G. Steeger, L. A. Barroso, and S. Rebennack, "Optimal bidding
    strategies for hydro-electric producers: A literature survey," *IEEE
    Transactions on Power Systems,* vol. 29, no. 4, pp. 1758-1766,
    Jul, 2014.

3.   S. E. Fleten, and T. K. Kristoffersen, "Stochastic programming for
    optimizing bidding strategies of a Nordic hydropower producer,"
    *European Journal of Operational Research,* vol. 181, no. 2, pp.
    916-928, Sep, 2007.

4.   D. De Ladurantaye, M. Gendreau, and J. Y. Potvin, "Strategic
    bidding for price-taker hydroelectricity producers," *IEEE
    Transactions on Power Systems,* vol. 22, no. 4, pp. 2187-2203,
    Nov, 2007.

5.   E. K. Aasgard, G. S. Andersen, S. E. Fleten *et al.*, "Evaluating a
    stochastic-programming-based bidding model for a multireservoir
    system," *IEEE Transactions on Power Systems,* vol. 29, no. 4, pp.
    1748-1757, Jul, 2014.

6.   M. A. Plazas, A. J. Conejo, and F. J. Prieto, "Multimarket optimal
    bidding for a power producer," *IEEE Transactions on Power Systems,*
    vol. 20, no. 4, pp. 2041-2050, Nov, 2005.

7.   Y. Vardanyan, L. Soder, and M. Amelin, "Hydropower bidding
    strategies to day-ahead and real-time markets: Different
    approaches,", in *Proc*. *2013 24th International Workshop on
    Database and Expert Systems Applications (Dexa 2013)*, pp. 209-213.

8.   P. Ilak, S. Krajcar, I. Rajsl *et al.*, "Pricing energy and
    ancillary services in a day-ahead market for a price-taker hydro
    generating company ssing a risk-constrained approach," *Energies,*
    vol. 7, no. 4, pp. 2317-2342, Apr, 2014.

9.   O. B. Fosso, M. M. Belsnes, and ieee, "Short-term hydro scheduling
    in a liberalized power system,", in *Proc*. *2004 International
    Conference on Power System Technology - POWERCON, Vols 1 and 2*, pp.
    1321-1326.

10.  A. Mas-Colell, M. D. Whinston, and J. R. Green, *Microeconomic
    Theory*: Oxford University Press, Inc. , 1995.
:::
