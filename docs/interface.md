---
layout: default
title: Interfacing with other codes
---

# Interfacing with other codes

flavio is specialized on computing flavour observables given new physics
contributions to the Wilson coefficients. In addition to model-independent
analyses, this functionality can also be used for studying specific new physics
models when interfaced with tools that compute these Wilson coefficients.

## WCxf interface

The universal interface to use codes that produce numerical values for the Wilson coefficients is the [Wilson coefficient exchange format](https://wcxf.github.io), which is supported by [various public codes](https://wcxf.github.io/codes.html).

The easiest way to use WCxf together with flavio is to load a WCxf file into an instance of `wilson.Wilson` with the wilson package, as described in the [discussion of new physics contributions](np.html). Assuming the WCxf file is named `my_wc.json`, all it takes is

{% highlight python %}
from wilson import Wilson
with open('my_wc.json', 'r') as f:
    my_w = Wilson.load_wc(f)
{% endhighlight %}

Now you can use this Wilson instance to compute anything you like,
e.g.

{% highlight python %}
flavio.np_prediction('BR(Bs->mumu)', my_w)
{% endhighlight %}

## Legacy FlavorKit interface

*Note: this interface is superseded by WCxf, but will not be removed until numerical agreement on the FlavorKit side is assured.*

Currently, flavio supports
[FlavorKit](http://arxiv.org/abs/1405.1434), which is a plugin to
[SARAH](https://sarah.hepforge.org/),
a Mathematica framework to analyze new physics models. SARAH/FlavorKit can
produce custom versions of the
[SPheno](https://spheno.hepforge.org/)
Fortran package, which can in turn be used
to compute the spectrum and also the new physics contributions to the Wilson
coefficients.

*Caution*: this interface only works with SARAH versions 4.9.0 and later.
Do not use it with earlier versions or with FLHA files generated by other codes.
The FLHA format unfortunately does not fix the normalization of the Wilson
coefficients, so is not a universal exchange format.

### Step 1: run FlavorKit

Having installed the most recent SARAH and SPheno versions, you can use the
custom SPheno module built by SARAH to produce an output file in SLHA format,
which contains the Wilson coefficients in
[FLHA](http://arxiv.org/abs/1008.0762) format.
Instructions for this are found in the
[FlavorKit manual](http://arxiv.org/pdf/1405.1434.pdf).

Let's assume the output file produced is
`SPheno.spc.MSSM` (a file with this name is actually
[included in flavio's source](https://github.com/flav-io/flavio/blob/master/flavio/data/test/SPheno.spc.MSSM)
for testing purposes).

### Step 2: read the output file with flavio

In a python session or script, you can now simply call

{% highlight python %}
import flavio
from flavio.io import flha

my_wc = flha.read_wilson('SPheno.spc.MSSM')
{% endhighlight %}

Now you can use this Wilson coefficient object to compute anything you like,
e.g.

{% highlight python %}
flavio.np_prediction('BR(Bs->mumu)', my_wc)
{% endhighlight %}
