# Underwritten Assets Withdrawal Mechanism

## Available Assets can be Withdrawn by Underwriters

Underwriters can withdraw a portion of their unfrozen capital at any time before it is completely frozen. However, the frozen part needs to wait for the corresponding policy expiration to exit.

The assets that can be withdrawn by the underwriter is calculated by the formula:

$$
withdraw = η*sTokenAmount - shadow
$$

​_**shadow**_ is the amount of assets of the underwriters that has been frozen. The algorithm underlying it is described in the previous section[#policy-generation-and-frozen-capital](benefits-and-risks-of-underwriting.md#policy-generation-and-frozen-capital "mention")。However, the situation becomes more complicated when considering a policy that has expired which results in releasing part of frozen capital.

## Cancellation of Policies

Along with the cancellation of policies which expire, the corresponding underwriters’ frozen capital should be released.

When a policy is generated, we record its _lastProviderIndex_ and _ΔaccSPS_. When it is cancelled, the protocol synchronizes the latestUnfrozenIndex in [#global-variables](benefits-and-risks-of-underwriting.md#global-variables "mention") to the _lastProviderIndex_ of this policy, and accumulates _accSPSDown_:

$$
accSPSDown += ΔaccSPS
$$

​When calculating an underwriter's _shadow_, it is necessary to first determine the relationship between his _index_ and _latestUnfrozenIndex_. When index<=latestUnfrozenIndex, it means that the capital being released already has the part of the underwriter’s frozen capital. His _shadow_ will be calculated by:

$$
shadow_{down} = sTokenAmount*accSPSDown - SDebt
$$

$$
shadow_{new}=shadow-shadow_{down} = sTokenAmount*(accSPS-accSPSDown)
$$

$$
withdraw = η*sTokenAmount - shadow_{new}
$$

## **After Underwriter Withdrawal of Assets**

In order to make the protocol easier to maintain and to reduce the cost of gas fee when users invoke the contract, Meta Defender currently requires the underwriter who would like to quit to withdraw all remaining liquidity at once (i.e., _withdraw_ mentioned above).

Of course, you may only want to "withdraw partially". It is very simple to implement this - you could simply withdraw in full and then re-join with your desired coverage amount. This saves the protocol from having to maintain a huge ledger, where the cost of gas would otherwise become unimaginable.

When an underwriter withdraws assets, he or she needs to destroy all of his or her sToken.

{% hint style="info" %}
You may be concerned that some of my assets is still frozen by policies and will have to wait until each of these policies expires before I can withdraw it. Will destroying all sToken now have an impact? If an underwriter stays in the protocol long enough, he may even find that he currently has no idle capital to withdraw.

The answer is there is no need to worry. After you destroy your sToken and declare yourself as an underwriter, the agreement creates a specific ledger to manage your "historical status", and monitor your frozen capital and distribute it to you when it is unfrozen. This is explained in more detail below.
{% endhint %}

Suppose an underwriter P destroys all of his sToken, declaring that he has ended his status as an underwriter. Regardless of how much capital he has actually withdrawn (usually less than his original provided capital, i.e., less than $$η*sTokenAmount$$), he can no longer continue to capture premium and no longer underwrite new policies, i.e., no longer "capture" new _shadow_.

The protocol creates a "historical identity" for him, P', and records the following parameters:

* fsToken(frozen\_sToken): the number of Tokens that this underwriter has not withdrawn, which will be then converted to sToken according to current η;
* accSPS\_P: the accSPS of P when it quits the protocol;
* index: the index inherited from P;
* sTokenAmount\_P : the sTokenAmount inherited from P;
* SDebt\_P: the sDebt inherited from P.

It can be seen that the maximum amount that can eventually be released by P' is: $$η*fsToken$$​ ------ ①;

The shadow inherited by P' from P is:

sTokenAmount\_P_\*_accSPS\_P _-_ SDebt\_P&#x20;

Or

&#x20;sTokenAmount\_P_\*_（accSPS\_P - accSPSDown）------- ②;

Obviously, at this point ②>=①.

Thereafter, the Meta Defender protocol monitors for changes in ②. As the earlier policies gradually expire and are cancelled as well as the capital is unfrozen, the value of ② gradually decreases (of course, the relationship between index and latestUnfrozenIndex is taken into account, and ② is really reduced when _index_ <= _latestUnfrozenIndex_).

When ② < ①, the difference between ① and ② is the capital of _P_ being unfrozen. _P_ extracts it, updates the value of ① to ②, and waits for the next unfrozen part.

As _accSPSDown_ increases, the value of ② eventually goes to zero. Zero is the minimum value of ② and cannot be a negative number.

After ① goes to zero, all residual capital of _P_ finishes withdrawing.

