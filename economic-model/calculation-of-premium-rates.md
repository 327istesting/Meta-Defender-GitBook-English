# Calculation of Premium Rates

Meta Defender extended and upgraded the well-known AMM model.

First, two concepts are defined,

* Premium Rate (_α_) : the amount spent to purchase the policy, as a ratio to the policy sum insured;
* Remaining Available Capital (_P_): the amount of assets left in the Capital Reserve in the extreme case, assuming all surviving policies have claims; also understood as the total amount of money currently in the Capital Reserve, minus the total amount of coverage for all unexpired policies.

In the absence of new underwriters joining or no underwriters withdrawing liquidity, both of the above always satisfy as the followings:

$$
α*P = k
$$

_k_ is a fixed value. As the number of insureds increases, _P_ gradually decreases and _α_ increases, representing an increase in insurance demand, and vice versa.&#x20;

When new underwriters join, _P_ increases, α remains constant, and k increases accordingly.

$$
α * （P+P_1） = k_1
$$

Similarly, when the underwriter withdraws liquidity, _P_ and _k_ decrease at the same time.&#x20;

If the policy expires and frozen capital is released, this also leads to an increase in _P_. In this case, _k_ is constant and _α_ decreases.

There is a minimum value of _α_ as $$α_0$$​, and the decrease of _α_ is limited to this value, which then causes the value of _k_ to increase. When the value of _P_ "expands" to the point where α touches $$α_0$$, then _α_ stops decreasing and _k_ increases:

$$
α_0 * P_{new} = k_{new}
$$

In the traditional insurance industry, premiums are actuarially priced based on the probability of historical risk events. The existing blockchain insurance products also build some actuarial models, but it is difficult to ensure the actuarial results are truly objective and credible when the historical data samples are not sufficient. Meta Defender tends to determine the risk pricing of each agreement based on the most basic economic rule: the relations between supply and demand.



{% hint style="info" %}
One possible question is whether the mechanism will cause underwriters to purchase their own insurance in bad faith and drive up premium rates. In order to avoid such attack, In order to calculate k, Meta Defender will add another arbitrary number to the real P value, which will make the curve of premium rate more smooth and significantly increase the cost of such attack.&#x20;
{% endhint %}
