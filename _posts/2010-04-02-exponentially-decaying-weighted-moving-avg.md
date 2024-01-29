---
layout: post
title: "Exponentially decaying weighted moving average"
modified:
categories: blog
excerpt:
image:
  feature:
date: 2010-04-02T08:08:50-04:00
---

I was recently experimenting on Exponentially decaying weighted moving average (EMA) while implementing a load approximation protocol for a database engine at work.

Exponentially decaying Moving Averages (EMA) are widely used in financial mathematics to track the recent average behavior of stocks, more interestingly, they are also used in the Unix load balancer. EMA's are generally used in systems where the first seen value is of little importance compared to the last seen value. So we calculate the average by assigning different weights to the moving average scale. But the underlying awesomeness of EMA is that it decays *exponentially*.


{% highlight ruby %}
Simple avg. = Sum of samples / Number of samples.
{% endhighlight %}

No surprises here!

There are several implementations of EMA of which this is one.

{% highlight ruby %}
EMA = (current EMA) * k / sample * (1.00 - k)
{% endhighlight %}
Where k = decay factor or constant smoothing factor.

Starting out with a slow moving latency factor, say 0.99, the contribution of sample 'x' after N more samples is 0.99^N * 0.01 = 0.00366032341 which is ~0.36%. Whereas for a simple average, the contribution would be 1/100 i.e 0.01 or ~1%.

One might wonder why the last seen EMA is multiplied by a factor of 0.99 and the last seen sample value by a mere 1 - 0.99. This seems to contradict the case for calculating EMA's in the first place. But be aware, if we were to invert these weights, we would merely be assigning the last seen value to the average which will oscillate horribly with random inputs. The EMA of sample 1 is undefined, and can be set to 0 or the value of the initial sample. Each approach gives slightly varying graph inclines. But for large sample sizes, the significance of the first seen value carries a very small weighting, therefore, its of very little significance what we assign the first value to.

![EMA](/assets/ema-rand.png)

The graph above shows the statistics for a number of random valued samples; the simple average and the EMA are calculated for 1500 samples. As you can see, EMA values show up an exponential curve before smoothing out. For small sample sizes, the simple average should suffice and for larger sample sizes EMA with an appropriate smoothing factor will represent the moving average that is representative of the last seen values. ( A smoothing factor of 0.99 takes a while to flatten out.)
