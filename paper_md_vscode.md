# Operational use of marginal cost curves for hydropower plants as decision support in real-time balancing markets 

 Abstract—When participating in the real-time balancing markets, hydropower producers keep making decisions about how much power they are willing to sell or purchase at what prices from the markets. Due to the specific market character-istics, the decisions should be made quickly, placing high de-mand on the computation time of rescheduling. In this paper, we present a practical method to determine an hourly bidding curve for a plant. It is applicable to both balancing and intra-day markets. We heuristically calculate the marginal cost for all the operating points, covering the entire working area for the plant and including all the physical limitations and re-serve obligations in other markets. Heuristics are proposed to reduce the computation time without neglecting significant practical features. Numerical results based on a real-world case study demonstrate the advantage of the proposed method in terms of computation time and solution quality.

Cite:


Here is my nifty citation {cite}`perez2011python`.

- A link to an equation directive: {eq}`eq_1`

Index Terms—Heuristic algorithms, hydroelectric power gen-eration, power generation economics

## INTRODUCTION

The growth of intermittent renewable energy, such as wind and solar, in the power system and stronger market connections increase the need for the efficient and reliable balancing services, both in terms of capacity and energy. Being a flexible electric power source, hydropower can regu-late its generation level and reduce the power fluctuations in the system. Therefore, although the hydropower producers in the Nordic market primarily benefit from selling energy in the day-ahead electricity market, they are also actively participat-ing in the balancing markets to gain more profit by offering the unused flexibility.

In real-time balancing markets, the trading of energy takes place every day around the clock and is closed shortly before the period of delivery. Thus, the response time for hydropow-er producers to submit bids or accept offers is very crucial. For example, in the continuous intraday market ELBAS covering the Nordic, Baltic, UK and German markets, trades follow a "first-come, first-served" principle and can be settled whenever a market participant accepts an offer of another market participant [1]. Given the current production plan for a plant, a producer must make a quick but reasonable decision about how much power they are willing to sell or purchase at what prices from the markets. This decision involves a new production plan, which must be rescheduled in a both techni-cally feasible and economically efficient manner. That is, which units will be dispatched and at what generation level they will run should be determined. In addition, the new operating cost, including the start/stop cost due to the shift in working units, should be quantified, which is one of the key indicators to assist the producer to decide which bids or offers should be carried out in the markets. 

In this paper, we present a novel version of a method used as decision support to the process described above. It pro-vides a viable bidding curve per hour for each hydropower plant. It is applicable to both balancing and intraday markets. We heuristically calculate the marginal cost for all the operat-ing points, covering the entire working area for a plant and including all the physical limitations and reserve obligations in other markets. The sum of start cost and stop cost, referred to as the deviation cost, is also taken into consideration. The dispatch decision is specified for each selected bidding point. In order to provide the result within reasonable time but without neglecting significant practical features, we present two new specific heuristics to tackle the problem.

Since the bidding and scheduling problems are intimately connected [2], the majority of published bidding strategies for hydropower, either in one electricity spot market [3]-[5], or in multiple markets [6]-[8], focus on optimizing the production scheduling given a deterministic or stochastic pricing. That is, the optimal set of bids is subject to a priori price-based criteria. By contrast, our method provides a realistic bidding strategy extending from the minimum generation to the max-imum output of a plant. In other words, it is the generating capacity in a plant that determines the bidding range. This approach conforms to the trading characteristics in the real-time markets, e.g. ELBAS, where prices are set based on a pay-as-bid basis for all transactions rather than a common market clearing price in the day-ahead market.  
The proposed approach is implemented to an operational hydropower scheduling model, Short-term Hydro Optimiza-tion Optimization Program (SHOP), used by most large hydropower producers in Scandinavia [9]. It considers complex cascaded watercourses with technical constraints and various market constraints. It also handles the modelling challenge of nonlinear and nonconvex elements together with state-dependencies. 
The paper is outlined as follows. We begin, in Section II, by providing a brief description of the hydrological balance in the reservoirs and power generation in the plants. Section III presents the heuristics we employ to generate the bidding curve. The advantage of the proposed method in terms of computation time and solution quality is demonstrated in Section IV. Finally, conclusions are drawn in Section V.   

## Problem Description
In this section, we first derive the hourly water cost for a plant from the dual value of upstream and downstream reservoir balance constraints, and then, discuss how the production is calculated when the discharge of the generating units is given. 
### Water Cost for a Plant  
The hydrological balance in the reservoirs features time coupling and state dependency, which means water release in one time step will affect the decision in future intervals and discharge in one reservoir will impact on operation of other objects in the watercourse [9]. The dual value (marginal value or shadow price) of the reservoir balance equation implies the incremental water value (€) added by increasing one additional unit (m3) of water in the reservoir. This value is actually the opportunity cost of using water now versus storing it for later generation [8].
For reservoir k at time period t, the hydrological balance can be formulated as 

```{math}
:label: eq_1
\begin{array}{ll}
V_{k t}=V_{k, t-1}+ & \\
& 3600 \cdot\left(\sum_{\substack{j \in J_{k}^{U P}}} F_{j, t-\tau_{i k}}^{U P}-\sum_{j \in J_{k}^{D o W N}} F_{j, t}^{D o W N}\right)
\end{array}
```


where:
||||
|-|-|-
| T	| Set of time periods (hourly resolution).
| K	| Set of reservoirs. 
| J_k^UP |	Set of upstream objects of reservoir k.¬
| J_k^DOWN |	Set of downstream objects of reservoir k.
| V_kt | 	Water volume of reservoir k at the end of period t (m3).
| V_(k,t-1) |  Water volume of reservoir k at the beginning of period t, i.e. volume at the end of period t-1  (m3).
| F_(j,t-τ_ik)^UP |	Flow from upstream source j in period t-τ_jk and τ_jk is the time delay of water from j to reservoir k. The flow can be nature inflow, discharge through plants or gates, or spilled flow from other reservoirs (m3/s).
| F_(j,t)^DOWN	| Flow to downstream source j in period t. The flow can be discharge to plants, bypass or spill gates (m3/s). 

We assume that before the producers participate in the real-time balancing markets, they already have a committed production scheduling to meet the obligation from the spot market. Therefore, the marginal water value λ_kt for reservoir k at time period t, i.e. the dual value of {eq}`my_label`, is available.  
The operating cost for a plant s mainly derives from the water it uses in that period. It is the difference of the marginal water value for the immediately upstream reservoir k and downstream reservoir k+1, which is expressed as 
〖WC〗_st=λ_kt-λ_(k+1,t), s∈S,t∈T.
2

where:
||||
|-|-|-
|S |	Set of plants.
|〖WC〗_st |	Water cost for plant s at period t (€/m3).
|λ_kt |	Marginal water value of reservoir k at period t, which is the immediately upstream reservoir to plant s (€/m3).
|λ_(k+1,t) |	Marginal water value of reservoir k+1 at period t, which is the immediately downstream reservoir to plant s (€/m3). 

Note that in a cascaded watercourse, the marginal water value normally becomes larger when moving upward because more electricity can be produced with the same amount of water, i.e. λ_kt>λ_(k+1,t). If there is no downstream reservoir below a plant, i.e. water is released to the sea, λ_(k+1,t) is set to 0. 
Production for One Operating Point
The water level of reservoir k is interpolated according to the piecewise-linear curve of the volume and the water level. The gross head of plant s is hence updated by 3. We use the initial water volume of period t as a constant level for the whole hour. If the water level of downstream reservoir k+1 is higher than the outlet line of the plant, it will be used in the head calculation instead of the outlet line.
〖GH〗_st=L_(k,t-1) (V_(k,t-1) )-MAX[L_(k+1,t-1) (V_(k+1,t-1) ),L_s ]
s∈S,t∈T
3

where:
〖GH〗_st	Gross head of plant s at period t (m).
L_(k,t-1)	Water level of reservoir k at the beginning of period t, which is interpolated on the basis of V_(k,t-1) (m).
L_(k+1,t-1)	Water level of reservoir k+1 at the beginning of period t, which is interpolated on the basis of V_(k+1,t-1) (m).
L_s	Outlet line of plant s (m). 
In real-world operation, the head loss in the main tunnel and the penstock that unit i connects to should not be neglected. It can be represented as a quadratic equation of the total flow going through the main tunnel/penstock. The net head is calculated as 
〖NH〗_ist=GH_st
-α_main∙(∑_(i∈I_(s,main))▒q_ist )^2-α_pen∙(∑_(i∈I_(s,pen))▒q_ist )^2
i∈I_s,s∈S,t∈T
4

where:
I_s	Set of units in plant s.
I_(s,main)	Set of units that connect to main tunnel in plant s.
I_(s,pen)	Set of units that connect to penstock pen in plant s.
NH_ist	Net head of unit i in plant s at period t (m).
α_main	Loss factor for main tunnel.
α_pen	Loss factor for penstock pen.
q_ist	Flow going through unit i in plant s in period t (m3/s). 
For a generating unit i in plant s, the power production, in 5, depends on the net head and the flow going through that unit. It also relies on the generator efficiency and head-dependent turbine efficiency.
mw_ist=0.001
∙η_i^GEN (mw_ist )∙η_i^TURB (q_ist,NH_ist )∙G∙NH_ist∙q_ist
i∈I_s,s∈S,t∈T.
	5

where:
mw_ist	Power produced by unit i in plant s in period t (MW).
η_i^GEN	Generator efficiency of unit i, which is interpolated on the basis of production mw_ist.
η_i^TURB	Turbine efficiency of unit i, which is interpolated on the basis of flow q_ist and net head NH_ist.
G 	Gravity value, default setting is 9.81 (m/s2). 
Based on 4 and 5, if the discharge for each unit is given (i.e. one possible operating point for the plant), we can precisely calculate the corresponding production, taking the head loss into consideration. This transformation from the flow discharge to the power generation is implicitly done by the functionality in SHOP. 
Heuristics
In this section, four heuristics are presented in a logical sequence that one can follow to define the hourly bidding curve for a plant. The first two heuristics are introduced to significantly reduce the computation time. The chosen operating points in the selected unit combinations cover the entire working area for the plant. The third heuristic is presented to define the average cost and marginal cost for each operating point. It is originated from the economic theory, but adapted to the practice of hydropower operation. The last heuristic is proposed to select the best bidding points, taking into consideration the cost of deviation from the original production plan.
Heuristic I: Define the Unit Combinations
To find a bidding curve covering the entire viable working area for a plant, we first need to determine the production limits for the plant, i.e. horizontal axis of the curve. We introduce the idea of unit combination as the basis for grouping. For a plant having N generating units, the number of possible unit combinations is 2^N (the left of Fig. 1). For each unit combination, its production limits are restricted by the generating capacity of the units in that combination. Then these combinations constitute the whole operating range for the plant.  
However, if there are identical units in a plant, which is common in real-world application, the unit combinations that separately contain these units overlap the working area. Take a plant having 3 identical units as example (Fig. 1). Combinations 2, 3, and 4 give the same operating limits. So do Combinations 5, 6, and 7. Therefore, Heuristic I is proposed:
Heuristic I: We establish the unit combinations based on the type of units instead of the number of units. If the units are defined as same type, it means these units are identical and connected to the same penstock as well as main tunnel.
If the units belong to the same type, only one combination will be chosen from those combinations with the same number of units. To avoid unnecessary operation shifting, we also assume that the working units have the priority to be included in the chosen combinations. Therefore, according to Heuristic I, from the combinations with one unit, only the one including the working unit G2 is selected; from the combinations with two units, the one containing the working units G2 and G3 is chosen (the right of Fig. 1). 
To sum up, if the number of identical units for type r is N_r and there are R types of units in one plant, then the number of unit combinations becomes ∏_(r=1,…,R)▒〖〖(N〗_r+1)〗. For a plant where all the N units belong to one type, the number of unit combinations is reduced from 2^N to N+1. 

```{image} images/img1.png
:alt: alt_text
:class: bg-primary mb-1
:width: 600px
:align: center
```
 
Example of defination of unit combinations
Heuristic II: Define the Operating Points in Each Selected Unit Combination
For each unit, the generating capacity spans from the minimum to the maximum limit, deducting the scheduled obligations in other reserve markets. If we equally make X segments within this range, i.e. there are X+1 generating levels for this unit, then the number of operating points in a unit combination that includes N units is (X+1)^N (the left of Fig. 2). The computation time grows exponentially with the increase of the number of units.
In practice, if two or more Francis or Kaplan turbines of the same type are in operation, they are prone to run symmetrically. That is, the optimal dispatch is more likely to be achieved when the units run at the same levels (the right of Fig. 2) instead of operating at different levels, e.g. one runs at best point whereas the other runs at maximum. Given this premise, we suggest Heuristic II: 
Heuristic II: For the Francis or Kaplan turbines of the same type in one unit combination, only the symmetrical operating points will be selected.
Therefore, if there are R types of units in one unit combination, then the number of the operating points is (X+1)^R. For a combination consisting of N units of one type, the number of operating points (with X+1 generating levels) is reduced from exponential (X+1)^N to constant X+1. 
It is worth mentioning that, different from Francis or Kaplan turbines, the symmetrical operation is not applicable for Pelton turbines. The power-discharge curve for Pelton turbines is normally nonconvex. However, the convexity of the power-discharge curve is a necessary condition for Heuristic II to be valid. Therefore, Pelton turbines are always treated as unique even though they are the same type. 
 
Example of selection of operating points in one unit combination
	Heuristic III: Calculate the Average Cost and Marginal Cost for One Operating Point
For a given operating point p in one specific unit combination c, the generation cost for this point is the opportunity cost of the water used. In the previous section, we have presented how the hourly water cost is defined, in 2, and how the production can be accurately obtained when the discharge of the units is decided, in 5. Therefore, we denote the average cost for this operating point by
〖ac〗_cst^p=(3600∙WC_st∙∑_(i∈I_c)▒q_ist^p )/(∑_(i∈I_c)▒〖mw〗_ist^p )
p∈P_c,c∈C,s∈S,t∈T.
	6

where:
C	Set of unit combinations.
I_c	Set of units in unit combination c.
P_c	Set of operating points in unit combination c.
ac_cst^p	Average cost for the operating point p in unit combination c in plant s in period t (€/MWh). 
In economics, marginal cost is the change in the opportunity cost that arises when the quantity produced has an increment by one unit. In contrast to the transformation from discharge to production, it is much more complicated to find the discharge by a given production. In addition, the power produced is infinitely divisible. Therefore, we put forward Heuristic III: 
Heuristic III: The marginal cost for one operating point is the change in the opportunity cost of water involved as a result of an infinitesimally small increase in the discharge of the units, which is expressed as

〖mc〗_cst^p
=(3600∙WC_st∙∑_(i∈I_c)▒(q_ist^p+∆q_ist^p ) -3600∙WC_st∙∑_(i∈I_c)▒q_ist^p )/(∑_(i∈I_c)▒((mw) ̃_ist^p ) -∑_(i∈I_c)▒〖mw〗_ist^p )
p∈P_c,c∈C,s∈S,t∈T.
	7

where:
mc_cst^p	Marginal cost for the operating point p in unit combination c in plant s in period t (€/MWh).
〖∆q〗_ist^p	A small increment in the discharge of unit i in unit combination c in plant s in period t, 〖∆q〗_ist^p=0.001∙(q_ist^p)/(∑_(i∈I_c)▒q_ist^p ), (m3/s).
(mw) ̃_ist^p	Power produced by unit i in plant s in period t when there is a small increment in the discharge of the units (MW). 
Heuristic IV: Determine the Bidding Points
After the average cost and marginal cost for all the chosen operating points in all the selected unit combinations are calculated, we face the question of how to determine the bidding points that span the entire working area for the plant.
According to the economic theory, in a competitive market, the supply curve for a producer should be the portion of the marginal cost curve above its intersection with the average cost curve [10]. However, when we calculate the generation cost for each operating point, we do not take into account the start/stop cost incurred due to the shift from working unit combination (i.e. the unit combination including all but only the working units in the original plan) to other unit combinations. Take the right of Fig. 1 as example. NEW Combination 3, as the working unit combination, consists of the working units G2 and G3. NEW Combination 2 only contains G2, which means the cost of deviation from the original production plan is the stop cost for G3. Similarly, NEW Combination 4 comprises all the three units. Then the deviation cost should be the start cost for G1. 
The deviation cost cannot be neglected. Otherwise, the corresponding bids to the market would distort the profit margins. For instance, it might be more cost effective for the producer to adjust the generation within the working unit combination than to turn the working units off and switch other units on, even if operating points in another unit combination give higher profit. 
When jumping from one unit combination to another, i.e. moving from operating point p in unit combination c to operating point p' in unit combination c', equation 8 should be followed to ensure the jump is more profitable. 
(mc_cst^p-ac_cst^p )∙∑_(i∈I_c)▒〖mw〗_ist^p -DC_cst
<(mc_c'st^(p^' )-ac_c'st^(p^' ) )∙∑_(i∈I_c')▒〖mw〗_ist^(p^' ) -DC_c'st
p∈P_c,c∈C,p^'∈P_c',c'∈C,s∈S,t∈T.
		8

where:
〖DC〗_cst	Cost of deviation from the original production plan for unit combination c in plant s in period t (€).
Finally, Heuristic IV is applied to pick out the best points to constitute a bidding curve spanning the entire production interval. The procedure starts from the current working point. The left of Fig. 3 is to determine the bidding points above the working point, while the right is to decide the bidding points below. Given this bidding curve, the part above the current working point is the quantity of energy that the producer is willing to sell and able to produce when the market price is at the bidding value or higher, whereas the part below the working point is the amount of power that the producer would rather avoid to generate but buy from the market if the price is at the bidding value or lower.   
Note that each point on the bidding curve already contains all the detailed information about which units are running and what the unit discharge and production are. So it can be directly used as a new production plan if this point is accepted in the bidding. Otherwise, linear interpolation between two points can be employed to get a feasible solution. 
 
Flowchart for Heuristic IV
Case Study
The numerical results are based on a real-world hydrological case, but the data have been modified for confidentiality reasons. There are four power plants in the cascaded watercourse. The production scheduling after spot clearing (i.e. original production plan) is already obtained from SHOP. The opportunity cost of the water for every particular hour, derived from the dual value of the reservoir balance constraint, is hence available. 
We now focus on one plant that has 4 units (Francis turbines). Units G1 and G2 are identical and connected to Penstock 1, whereas the more efficient units G3 and G4 are also identical and connected to Penstock 2. No capacity is reserved for other ancillary services markets. Based on the current production plan and the configurations of hydroelectric facilities (TABLE I), we sequentially use the Heuristics presented in Section III to generate the bidding curve for the first hour when the water cost is 0.035789 €/m3. All the tests were made on an i7-6600U 2.80 GHz processor with 16 GB of RAM.
![example_watercourse](/Users/bjornarfjelldal/Pictures/example_watercourse.png)

Data for Power Plant
	Original Production Plan (MW)	Min-/Maximum Production (MW)	Start/Stop Cost (€)
G1	0.0	61.0 / 97.6	285 / 200
G2	0.0	61.0 / 97.6	285 / 200
G3	85.4	61.0 / 97.6	285 / 200
G4	85.4	61.0 / 97.6	285 / 200
Plant	170.8	61.0 / 390.4	-
Firstly, in light of the definition in Heuristic I, there are 2 types of units in this plant. Therefore, the number of unit combinations is reduced from 16 to 9, but the entire production capacity of the plant is still covered (TABLE II). Combination 6 is the working unit combination. Shifting from this combination to any other unit combinations will lead to deviation cost because of the start and/or stop of the operation in units. 
List of the Chosen Unit Combinations 
	Units in 
Combination	Min-/Maximum Production (MW)	Deviation Cost 
(€)
Comb 1	-	0 / 0	400
Comb 2	G1	61.0 / 97.6	685
Comb 3	G3	61.0 / 97.6	200
Comb 4	G1, G2	122 / 195.2	970
Comb 5	G1, G3	122 / 195.2	485
Comb 6	G3, G4	122 / 195.2	0
Comb 7	G1, G2, G3	183 / 292.8	770 
Comb 8	G1, G3, G4	183 / 292.8	285
Comb 9	G1, G2, G3, G4	244 / 390.4	570
The number of operating points in one unit combination depends on how many segments are defined between the unit production limits. The finer the segments are, the more precise the bidding curve will be, and in consequence, the more computation time is needed. TABLE III shows the computation time used to calculate the production, average cost and marginal cost for each operating point in Combination 9 where all the four units are included, without and with Heuristic II, respectively. Without Heuristic II, the number of operating points is exponential to the number of units and the computation time becomes unacceptable, for daily operation, after segments are increased to 49. By contrast, after Heuristic II is employed, the number of operating points becomes exponential to the number of types. The computation time, for all the choices of segments, is reduced to the operationally acceptable level.
Following the steps in Heuristics III and IV, the production, average cost and marginal cost for each operating point in the unit combinations can be calculated and finally the bidding strategy can be determined. The first two charts in Fig. 4 display the result without and with Heuristic II applied (the number of segments is both set as 99). As we can see, there is no obvious difference between them. With the implementation of Heuristic II, the computation time is significantly reduced while the result quality is not notably scaled down.
Note that some points for which the marginal cost is below the average cost are selected in Combination 6 (the working unit combination). It happens because there exists stop cost of G4. If we take way the start cost and stop cost for all the units, the shift between the unit combinations occurs as long as the marginal cost is lower than the average cost (the third chart in Fig. 4).
Comparison of Computation Time 
Segment	Without Heuristic II	With Heuristic II
	Operating points	Computation time 
(millisecond)	Operating points	Computation time
(millisecond)
4	54	3	52	1
9	104	66	102	1
19	204	1101	202	2
49	504	43725	502	17
99	1004	625143	1002	65

 
Comparison of the selection of bidding points
Conclusion
When participating in the real-time balancing markets, hydropower producers always need to make decisions not only meeting their physical positions but also benefiting from trades in the markets. Due to the specific market characteristics, the decisions should be made quickly, placing high demand on the computation time of rescheduling. For the same product, the prices in the real-time markets vary, and therefore, the bidding strategy based on the possible operating capacity of a plant, instead of pricing determined a priori, can fully exploit the flexibility and provide a more comprehensive choice for the hydropower producers. 
The contribution of this paper is to propose a practical method that can assist the producers to trade in the real-time balancing markets. The hourly bidding curve for one plant is put forward on the basis of the marginal cost for the operating points, covering the entire feasible working area for the plant.  It is also demonstrated that the proposed heuristics are computationally efficient. 
In this paper, when we calculate the average cost and marginal cost for each operating point, we use the constant marginal water value. The gross plant head is obtained from the original production plan. For a hydrological system with significant storage, the discharge variation in a plant within one hour would not lead to considerable difference in marginal water value, and hence, this assumption is acceptable. However, it might cause the problem for small-size reservoirs, in which there is the risk of running out of water within one hour. We also assume that the bids are decoupled in time and do not consider the impact of the change in production plan for one hour on the consecutive hours. These are the issues for further improvement. 


perez2011python
Here is my nifty citation {cite}`scharff2016a`.

References 

```{bibliography}
:style: unsrt
```