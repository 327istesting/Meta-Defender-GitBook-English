# Benefits and Risks of Underwriting

To facilitate valuation, Meta Defender suggests to use a stablecoin for insurance and underwriting purposes. Assuming the stablecoin is called Token, the underwriter provides _A_ tokens(_A_ as the number of the token) to an Capital Reserve and receives a number of sTokens (staked token) based on the capital exchange rate _η_ of the pool. The number of sToken, which is _A'_ is:

$$
A' = \frac{A}{η} （η\leq1）
$$

The sToken is not a transferable ERC20 token; it is simply a credit to the underwriter by the pool as a credential to capture premium rewards and withdraw assets in the future.

At the time each pool is created, the starting value of _η_ is 1. Given that pay-outs of claims are prioritized through the Mining Pool balances for claims(Risk Reserve) (for details please check[insurance-claims-and-governance-mining.md](../project-architecture/insurance-claims-and-governance-mining.md "mention")), the value of _η_ is maintained at 1 as long as there is no large-scale claims event resulting in an encroachment on the Capital Reserve's principal.

## **Global Variables**

* η: the exchange rate of Token to sToken;
* accumlatedRewardPerShare (accRPS): 1 sToken's accumulated captured premium reward;
* accumulatedShadowPerShare (accSPS): 1 sToken's accumulated "captured" risk;
* accumulatedShadowPerShareDown (accSPSDown): the release (reduction) of accSPS as a result of policy expiration;

{% hint style="info" %}
No one wants to "capture" risk. But if you get a share of the premium reward, you have to take the corresponding share of the risk. We can use a Physics analogy here for explanation: the risk (shadow) is an antimatter token representing the potential future loss of assets (although in most cases the loss is absorbed by the Risk Reserve in time), and its presence limits the amount of capital that can be withdrawn by the underwriter.
{% endhint %}

* latestUnfrozenIndex: For the most recent policy written off, the latest underwriters who joined the pool that covered this policy. Or in other words: "youngest one" (age = length of time in the pool)

## Data Structure of the Underwriter and the Policy

When an underwriter stakes some Tokens to the protocol, the protocol marks him like this:

* index: the order in which the underwriter joined the protocol;
* sTokenAmount: the number of sTokens acquired by the underwriter (i.e., A as the number of Token in the previous section);
* rewardDebt（RDebt）: $$sTokenAmount * accRPS$$​ the reduction factor for calculating the underwriter's capture of the reward;
* shadowDebt（SDebt）：$$sTokenAmount * accSPS$$​ the reduction factor for calculating the underwriter's capture of the risk;

When a policy is created, the protocol will mark it as follows：

* coverage: the policy’s coverage, also the shadow that will be shared by all underwriters;
* ΔaccSPS:  $$\frac{coverage}{sToken.totalSupply}$$  the incremental accSPS resulting from the policy - it will accumulate in accSPSDown when the policy expires;
* latestProviderIndex: the index of the last underwriters of the pool, which can be understood in conjunction with the latestUnfrozenIndex in the previous section

## Policy Generation and Reward Capture

After a policy is generated, we set the amount paid by policyholder as “fee”, and we have

$$
accRPS += \frac{fee*0.95}{sToken.totalSupply}
$$

For each underwriter in the underwriting Capital Reserve, the withdrawable proceeds at this point are calculated as:

$$
reward = sTokenAmount*accRPS - RDebt
$$

​The proceeds can be immediately withdrawn to the underwriter's own wallet.

## **Policy Generation and Frozen Capital**

A policy is generated and the accSPS is increased:

$$
accSPS += \frac{coverage}{sToken.totalSupply}
$$

For each underwriter in the pool, the cumulative frozen capital amount is:

$$
shadow = sTokenAmount*accSPS - SDebt
$$

## ​When the Risk Reserve is Insufficient to Pay for Claims

When the Risk Reserve does not have enough funds to pay for a policy that actually suffers a loss or damage, the protocol will share the excess of the claim amount with the underwriter according to the weight of the underwriter's sToken.&#x20;

This is an extremely rare and exceptional circumstance and will result in a reduction of the exchange factor _η_.&#x20;

Assume when that this situation arises, and the excess of claims over the Risk Reserve is _δ_, the new _η_ becomes

$$
Δη = \frac{η_{0}*sToken.totalSupply - δ}{η_{0}*sToken.totalSupply}
$$

$$
η_{new} = η_{0}*Δη
$$

​It can be seen that the value of _η_ decreases further, and the number of sTokens - representing the weight of capturing premium rewards, while bearing liability - that can be obtained for an equal amount of Tokens for underwriters joining the pool thereafter is increased . This is clearly due to the reduction in the actual underwriting capacity of the original underwriters following the erosion of the principal in the pool.

